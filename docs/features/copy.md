## Copy

To allow some friends to see your location temporarily, even if they don't have OwnTracks installed on their devices (but do ask them why they don't :) you can toggle a `_cp` switch in the UI of both Android and iOS apps.

When this switch is active, the apps add a `{ "_cp" : true }` JSON element to outgoing `"_type" : "location"` publishes (via MQTT) or POSTs (via HTTP). Other than setting this value the apps don't do anything with the switch.

```json
{
  "_type": "location",
  "lat": 48.85833,
  "t": "u",
  "lon": 3.29513,
  "_cp": true,
  "tid": "JJ",
  "tst": 1496473278
}
```

What this `_cp` element basically does is to tell your backend software: "hey, (s)he says it's ok to share this location!", and your software can then, say, copy the location just published to a special Web site for your friends to see, or send them an e-mail, or do any amount of other neat stuff: let your imagination run free. Note this is something you implement depending on what you want to do with the data.

To show you how easy it could be, here's a bit of Python code:

```python
def on_message(client, userdata, msg):

    topic = msg.topic

    try:
        data = json.loads(str(msg.payload))

        if '_cp' in data:
            # tell friends where we are
            print "public: TID = {0} is currently at {1}, {2}".format(data['tid'], data['lat'], data['lon'])
        else:
            # handle data privately

    except:
        return
```

