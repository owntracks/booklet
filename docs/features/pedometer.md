## Pedometer

The iPhone 5S has a built-in pedometer which we’ve leveraged into OwnTracks (iOS only).
The way this works is that the iPhone counts the steps you take, and OwnTracks
can report these in a JSON payload upon request.

### Command

In order for the app to actually report the steps counted by the device, you
send it a specially formatted command. (The device does not periodically publish steps on its own -- you must trigger it.)

```
mosquitto_pub -q 2 -t owntracks/jj/5s/cmd -m '{"_type" : "cmd", "action": "reportSteps"}'
```

Note how we’re using QoS=2 here: the message is published to the MQTT broker,
and when the phone next wakes up, which happens every few hundred seconds, it
will obtain the message, and publish a [JSON](../tech/json.md) payload with the counted steps back
to your MQTT broker.

```json
{
    "_type": "steps", 
    "from": 1400455130, 
    "steps": 1234, 
    "to": 1400458000, 
    "tst": 1400455130
}
```

### Daily reports

We use the following small program to issue the JSON needed to request the
steps for the 00:00 - 23:59 time-frame of this particular day (or the day before, etc.)

```python
#!/usr/bin/env python

import datetime
import time
import json
import sys

days = 0

def unix_epoch(t, delta):
    dt = t + delta

    # print dt
    return int(time.mktime(dt.timetuple()))

now = datetime.datetime.today()

f = now.replace(now.year, now.month, now.day, 0, 0, 1, 0)
t = now.replace(now.year, now.month, now.day, 23, 59, 59, 0)

delta = datetime.timedelta(days=days)


payload = {
        '_type' : 'cmd',
        'action' : 'reportSteps',
        'from'  : unix_epoch(f, delta),
        'to'    : unix_epoch(t, delta),
}
print json.dumps(payload)
```

In the evening, a _cron_ entry sends that off to our broker which will, eventually,
deliver the message to the the phone.

```
50 22 * * * /usr/local/bin/reportsteps | mosquitto_pub -q 2 -t owntracks/jpm/5s/cmd -l
```

#### Daily reports with Openhab

With openhab it's quite easy to user rules engine from openhab and to not rely on servers crontab.

At first you have to define mqtt retain broker (MQTT v. 1 is used) - simply add configuration to /etc/openhab2/services/mqtt.cfg

```
mqtt-retain.url=tcp://192.168.1.1:1884
mqtt-retain.qos=2
```

After that make a simple rule in /etc/openhab2/rules/ directory. Lets say the filename is /etc/openhab2/rules/owntracks.rules with contents:

```
rule "MQTT_OWNTRACKS_STEPS"
when
	// every day at 23:59
	Time cron "0 59 23 * * ? *"
then
	val long from1 = DateTime.now().withTimeAtStartOfDay().millis / 1000
	val long to1 = (now.millis / 1000)
	publish("mqtt-retain","owntracks/jj/5s/cmd",'{"action": "reportSteps", "to": '+ to1 +', "_type": "cmd", "from": '+ from1 +'}')
end
```

For usage the info in sitemaps or anywhere else 1 more item has to be made into file /etc/openhab2/items/owntracks.items

```
Number Steps_Yesterday "[%d]"  { mqtt="<[mqtt:owntracks/jj/5s/step:state:JSONPATH($.steps)]" }
```

Now You can make use of yesterday's steps count.
