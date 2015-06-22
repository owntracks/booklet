## LBS/Messages

The OwnTracks apps have support for receiving messages; this feature is also known as Location-Based Services. The way it works is that the apps subscribe to a geo-dependent topic as soon as they detect movement. When the app is about to publish a `"_type" : "location"` message because it has detected the user has moved, the apps subscribe to four (4) [GeoHash](https://en.wikipedia.org/wiki/Geohash) specific topics (levels 2 through 6 inclusive).

ezjmvj0jfdx5

OwnTracks subscribes to

```
lbs/+/+/ezjmvj0jfdx5
```

Messages are published to _channels_ and have an identifier _lbsid_.

The full description of the supported JSON payload is [described here](../tech/json.md), but a short example will clarify:

```json
{
   "_type" : "lbs",
   "prio" : 0,                        // optional priority (0 -- 2)
   "title" : "",
   "desc" : "",
   "icon" : 
}
```
