## Messages

The OwnTracks apps have support for receiving messages in newer versions, if it is configured in preferences/settings. Messaging is an experimental feature which relies on MQTT transport: you publish messages with a specific payload to your MQTT broker, and the apps subscribe to those messages on the broker. Just as with OwnTracks location data, the apps rely on connectivity between the mobile device and the MQTT broker to exchange messages. In particular with iOS, in which the operating system puts the app to sleep for a large number of seconds, this means that message reception is not necessarily instantaneous. Contrary to popular messaging or chat apps, OwnTracks currently doesn't utilize APN or other forms of notification of the device; a user may need a bit of patience until a message is received, unless the OwnTracks app is running in the foreground.

If messaging in OwnTracks is enabled, the apps subscribe to `owntracks/<user>/<device>/cmd` to receive messages which you publish to that topic from an application. Messages should be published qith `qos=2` to ensure delivery exactly once.

The full description of the supported JSON payload in messages [described here](../tech/json.md), but a short example will clarify:

```json
{
   "_type" : "msg",
   "prio" : 0,
   "title" : "Library",
   "desc" : "Don't forget to return War and Peace",
   "icon" : "fa-book",
   "tst"  : 1435017442
}
```

### GeoHashes

If messaging is enabled, the apps additionally subscribe to a geo-dependent topic as soon as they detect movement. When the app is about to publish a `"_type" : "location"` message because it has detected the user has moved, the apps subscribe to four (4) [GeoHash](https://en.wikipedia.org/wiki/Geohash) specific topics (levels 3 through 6 inclusive).

A point in Madrid, Spain is, for example `40.426979, -3.703913` (lat, lon), which resolves to the Geohash `ezjmgxwbhx47` (precision 12 == 12 characters).

```python
#!/usr/bin/env python

import geohash   # https://github.com/hkwi/python-geohash

lat, lon = 40.426979, -3.703913

for prec in range (3, 7):
    print "%s: %s" % (prec, geohash.encode(lat, lon, prec))
```

OwnTracks subscribes to the four topics:

```
3: msg/+/ezj
4: msg/+/ezjm
5: msg/+/ezjmg
6: msg/+/ezjmgx
```

Thanks to the wonderful [page on Geohashes by Chris Veness](http://www.movable-type.co.uk/scripts/geohash.html) we can visualize these on a map (note the map's scale):

![lbs geohash](images/lbs-geohash.png)

Received messages are destroyed when you switch modes in the app from, say, _Private_ to _Hosted_, etc.
