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

