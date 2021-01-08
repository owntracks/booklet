## Regions (Waypoints)

The _base topic_ for publishes from the devices (OwnTracks apps) is what you configure it
to be in the app's preferences/settings. For argument's sake we'll use the default `owntracks/<user>/<device>` as our example.

This base topic is used for publishes of type `location` (see [JSON](../tech/json.md)).
All messages published to this base topic are retained if you have configured the app to retain them.

Additionally, if you've configured a waypoint, regions, or geo-fence (we intermix these terms a lot to mean basically the same thing), a transition event will be published upon
entering or leaving a waypoint:

* `rad`ius (if its value is greater than 0)
* `desc`ription with the name you set for the waypoint
* `event` with a value of `"enter"` or `"leave"`, depending on
   whether the device is entering or leaving a configured region, respectively.
* `rid` (> January 2021) with the _region ID_ of the region.

If you set up a region (or waypoint or geo-fence, you get the drift), the app publishes that region (with `retain=0` irrespective of your general preference) to the base topic with `/waypoints` tacked onto the topic (e.g. `owntracks/<user>/<device>/waypoints`) with the payload for `_type=waypoint` as specified in the [JSON page](../tech/json.md). Entering or leaving a waypoint will be published as a `transition` message and will contain a `wtst` (for historical purposes) with the timestamp of when the region was originally defined.

For example, If Jane configures a region on her iPhone, the app could publish the following payload

```json
{
    "_type": "waypoint",
    "desc": "Paris is lovely",
    "lat": "48.858330",
    "lon": "2.295130",
    "rad": "50",
    "tst": "1385997757",
    "rid": "f7676c"
}
```

When you set up a region (with a `desc`ription and a `rad`ius), this
region is published to the broker with the current time stamp. If you update
the region definition on the device at a later stage, the region is
re-published, with the _original_ timestamp, but with possibly new
`desc`ription and/or `rad`ius and `lat`itude / `lon`gitude, but with the same
`rid`. To be precise, the `rid` (region ID) is used as an
identifier of the region even if it is later modified on the device.

Subscribers to the broker (our apps and any other program) can avoid getting
regions by subscribing to, say, `owntracks/+/+`; also broker ACLs can
prohibit access to `owntracks/+/+/waypoints` for particular users if so desired.
Conversely, all messages published by the apps (`location`, `transition`, and `waypoint`) are
available with a subscription to `owntracks/#`.

The OwnTracks apps may keep track of regions, e.g. for displaying to users. 

### In regions

When the apps publish a `_type = location` message, they add to that an array
called `inregions` (and as from January 2021 an array called `inrids`) with
the names of the regions and their `rid`s respectively. The `inregions` array
might be consumed by humans whereas the `inrids` array would be consumed by
developers who will wish to associate the region IDs (`rid`) with the values in
`inrids`.

### Adding on iOS

On iOS you can navigate to the place where you want to place a region, Tap long and edit the Region. The region is always placed at the center of the map (hence it's predefined name `Center`, which you should change to something meaningful). With a bit of practice, you can then drag the region to it's final destination on the map or, and this may be easier, edit the region and specify its exact coordinates.

### Adding on Android

On Android, regions can be configured on a separate activity. Coordinates for the geographical region can be entered by hand or by using the place picker.  

### Remote loading of regions

Instead of configuring multiple regions on the device, they can be loaded [remotely](remoteconfig.md) with the `setWaypoints` cmd message if remote configuration is enabled on the device. 

You can use this to import individual regions or groups of pre-configured regions which are merged into the existing regions definition on the device. Recall, however, that the `rid` element is like a _key_ which uniquely identifies each region / waypoint.

### Deleting regions

On iOS a region can be deleted by swiping it away. On Android, a long press is required. 

### Beacons Ranging

In addition to circular regions, the iOS app can monitor its proximity to [BLE Beacons](beacons.md). 
