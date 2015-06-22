## LBS/Messages

The OwnTracks apps have support for receiving messages; this feature is also known as Location-Based Services. The way it works is that the apps subscribe to a geo-dependent topic as soon as they detect movement. When the app is about to publish a `"_type" : "location"` message because it has detected the user has moved, the apps subscribe to four (4) [GeoHash](https://en.wikipedia.org/wiki/Geohash) specific topics (levels 2 through 6 inclusive).

A point in Madrid, Spain is, for example `40.426979, -3.703913` (lat, lon), which resolves to the Geohash `ezjmgxwbhx47` (precision 12 == 12 characters). OwnTracks subscribes to four topics:

```
2: ez
3: ezj
4: ezjm
5: ezjmg
6: ezjmgx
```

Thanks to the wonderful [page on Geohashes by Chris Veness](http://www.movable-type.co.uk/scripts/geohash.html) we can visualize these on a map (note the map's scale):

![lbs geohash](images/lbs-geohash.png)


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
