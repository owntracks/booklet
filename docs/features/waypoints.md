## Waypoints

The _base topic_ for publishes from the devices (OwnTracks apps) is what you configure it
to be in the app's preferences/settings. For argument's sake we'll use the default `owntracks/<user>/<device>` as our example.

This base topic is used for publishes of type `location` (see [JSON](../tech/json.md)).
All messages published to this base topic are retained if you have configured the app to retain them.

Additionally, if you've configured a waypoint or geo-fence, a transition event will be published upon
entering or leaving a waypoint:

* `rad`ius (if its value is greater than 0)
* `desc`ription with the name you set for the waypoint
* `event` with a value of `"enter"` or `"leave"`, depending on
   whether the device is entering or leaving a configured region, respectively.

If you set up a shared waypoint, the app publishes that waypoint (with `retain=0` 
irrespective of your general preference) to the base topic with `/waypoints`
tacked onto the topic (e.g. `owntracks/<user>/<device>/waypoints`) with the
payload for `_type=waypoint` as specified in the [JSON page](../tech/json.md). For waypoints with disabled sharing, no waypoint message is published. These can be used to note down locations privately. But note that entering or leaving a waypoint will be published as a `location` message as described above.

For example, If Jane configures a waypoint on her iPhone, the app could publish the following payload

```json
{
    "_type": "waypoint",
    "desc": "Paris is lovely",
    "lat": "48.858330",
    "lon": "2.295130",
    "rad": "50",
    "tst": "1385997757"
}
```

When you set up a shared waypoint (with a `desc`ription and a `rad`ius), this
waypoint is published to the broker with the current time stamp. If you update
the waypoint definition on the device at a later stage, the waypoint is
re-published, with the _original_ timestamp, but with possibly new
`desc`ription and/or `rad`ius. To be precise, the `tst` timestamp is used as an
identifier of the waypoint even if it is later modified on the device. For waypoints that are not shared, the above obviously does not apply. 

Subscribers to the broker (our apps and any other program) can avoid getting
waypoints by subscribing to, say, `owntracks/+/+`; also broker ACLs can
prohibit access to `owntracks/+/+/waypoints` for particular users if so desired.
Conversely, all messages published by the apps (`location` and `waypoint`) are
available with a subscription to `owntracks/#`.

The OwnTracks apps may keep track of waypoints, e.g. for displaying to users. 

### Adding on iOS
On iOS you can navigate to the place where you want to put a waypoint, Tap long and edit the Waypoint. The waypoint is always placed at the center of the map (hence it's predefined name `Center`, which you should change to something meaningful). With a bit of practice, you can then drag the waypoint to it's final destination on the map or, and this may be easier, edit the waypoint and specify its exact coordinates.

### Adding on Android
On Android, waypoints can be configured on a separate activity. Coordinates for the geographical region can be entered by hand or by using the place picker.  

### Remote loading of waypoints
Instead of configuring multiple waypoints on the device, they can be loaded [remotely](remoteconfig.md) with the `setWaypoints` cmd message if remote configuration is enabled on the device. 

You can use this to import individual waypoints or groups of pre-configured waypoints which are merged into the existing waypoint definitions on the device. Recall, however, that the `tst` element is like a _key_ which uniquely identifies each waypoint.

### Deleting waypoints
On both iOS waypoints can be deleted by swiping it away. On Android, a long press is required. 

### Beacons Ranging
In addition to circular regions, the apps can monitor their proximity for [BLE Beacons](beacons.md). 
