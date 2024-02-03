## Programming

We've [shown you some clients which consume OwnTracks data](../guide/clients.md), but you may well wish to dip your fingers into programming something of your own. Creating a program to consume OwnTracks data isn't particularly difficult, and we're going to show you what you can do with a bit of [Python](https://www.python.org) together with the [Paho Python client](https://www.eclipse.org/paho/clients/python/).

## Getting started: Paho Python


We're assuming you have Python installed, which you can verify by attempting to invoke `python3 -V`; if installed, it tells you the version number. We're also assuming you have `pip` installed (a Python package manager) which may be called `pip` or something on your machine.

Install the [Paho Python](https://www.eclipse.org/paho/clients/python/) module using

```bash
$ python3 -mvenv venv
$ source venv/bin/activate
(venv) $ pip install paho-mqtt
```

## Progress reports

Assume we want to create a program which should report the location of our friends, as a simple list:

```
TID = n4 is currently at 51.0343863, 9.4763712
TID = jane is currently at 48.856826, 2.292713
```

We need a small utility program which will subscribe to location publishes received by your broker by connecting to it on _localhost_ and subscribing to `owntracks/+/+` (a wild-card expression in which each `+` means _anything at this level_).

## The Code

For each received message, the utility attempts to decode the JSON payload and then prints the tracker-ID (_TID_) as well as latitude, longitude coordinates. Keep a copy of our [OwnTracks-JSON](json.md) documentation handy and study the topic names of each possible publish by the apps.


```python
#!/usr/bin/env python

import paho.mqtt.client as mqtt
import json

# The callback for when the client successfully connects to the broker
def on_connect(client, userdata, flags, rc):
    ''' We subscribe on_connect() so that if we lose the connection
        and reconnect, subscriptions will be renewed. '''

    client.subscribe("owntracks/+/+")

# The callback for when a PUBLISH message is received from the broker
def on_message(client, userdata, msg):

    topic = msg.topic

    payload = msg.payload.decode("utf-8")

    try:
        data = json.loads(payload)

        print("TID = {0} is currently at {1}, {2}".format(data['tid'], data['lat'], data['lon']))
    except:
        print("Cannot decode data on topic {0}".format(topic))

client = mqtt.Client()
client.on_connect = on_connect
client.on_message = on_message

client.connect("localhost", 1883, 60)

# Blocking call which processes all network traffic and dispatches
# callbacks (see on_*() above). It also handles reconnecting.

client.loop_forever()
```

## Testing

Testing location-based apps is a bit of a, well, pain, but remember there are a few simple tricks you can apply:

* Use the _publish now_ button in OwnTracks to fire a location update. The smart phone won't really _move_ much of course, but it'll allow you to test your program a bit.
* Publish your own [OwnTracks-JSON](json.md) payload to the broker with _mosquitto_pub_ or similar.

