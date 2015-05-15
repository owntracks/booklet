The _base topic_ for publishes from the devices (OwnTracks apps) is what you configure it
to be in the app's preferences/settings. For argument's sake we'll use
`owntracks/<user>/<device>` as our example.

This base topic is used for publishes of type `location` (see [[JSON|JSON]]).
All messages published to this base topic are retained if you have configured
the app to retain them.

Additionally, if you've configured a geo-fence, a `location` message will
contain the elements:

* `rad`ius (if its value is greater than 0)
* `desc`ription with the name you set for the waypoint
* `event` with a value of `"enter"` or `"leave"`, depending on
   whether the device is entering or leaving a configured region, respectively.

If you set up a shared waypoint, the app publishes that waypoint (without the retain flag,
irrespective of your general preference) to the base topic with `/waypoints`
tacked onto the topic (e.g. `owntracks/<user>/<device>/waypoints`) with the
payload for `_type=waypoint` as specified in the [[JSON page|JSON]]. For waypoints with disabled sharing, no waypoint message is published. These can be used to note down locations privately. But note that entering or leaving a waypoint will be published as a `location` message as described above.

For example, If Jane configures a waypoint on her iPhone

![](https://raw.github.com/wiki/owntracks/owntracks/assets/waypoints/b-waypoint-config-ios.png)

![](https://raw.github.com/wiki/owntracks/owntracks/assets/waypoints/b-waypoint-map-ios.png)

the app will publish the following payload

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

![](https://raw.github.com/wiki/owntracks/owntracks/assets/waypoints/b-waypoint-config-android.png)

Subscribers to the broker (our apps and any other program) can avoid getting
waypoints by subscribing to, say, `owntracks/+/+`; also broker ACLs can
prohibit access to `owntracks/+/+/waypoints` for particular users if so desired.
Conversely, all messages published by the apps (`location` and `waypoint`) are
available with a subscription to `owntracks/#`.

The OwnTracks apps may keep track of waypoints, e.g. for displaying to users. 

For convenience, waypoints can be loaded remotely: [[Remote-Loading-of-Waypoints]].

## iOS

On iOS you can navigate to the place where you want to put a waypoint, Tap long and edit the Waypoint. The waypoint is always placed at the center of the map (hence it's predefined name `Center`, which you should change to something meaningful). With a bit of practice, you can then drag the waypoint to it's final destination on the map or, and this may be easier, edit the waypoint and specify its exact coordinates.

# Setting up iBeacons

If you want to monitor an iBeacon region rather than a circular region set the radius to zero (0) and add the beacon identifier to the description separated by a colon (:).

![](https://raw.github.com/wiki/owntracks/owntracks/assets/waypoints/b-waypoint-config-ibeacon.jpg)

You add the UUID of the beacon to the description and may specify the major and minor identifier numbers of the beacons in hex.

When the UUID is valid, iBeacon monitoring will start.

Examples:
* 'myBeacon:CA271EAE-5FA8-4E80-8F08-2A302A95A959:0001:CAFE'
* 'myBeacons:CA271EAE-5FA8-4E80-8F08-2A302A95A959'
