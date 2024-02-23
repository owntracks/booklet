## Regions (Waypoints)

A _region_, _geofence_, or _waypoint_ (sadly we've used these terms interchangeably) is a circular area you configure in the UI on your OwnTracks device. The device then begins monitoring whether the area is being entered or left and it will publish what we call a _transition event_ accordingly.

The _base topic_ for publishes from the OwnTracks apps is what you've configured it to be in the apps' preferences / settings. This base topic is used for publishes of type `location` (see [JSON](../tech/json.md)). All messages published to this base topic are retained if you have configured the app to retain them.

So upon entering or leaving a region, the device publishes a transition event which contains:

| element       | meaning |
| ------------- | ------  |
| `desc`        | description with the name you set for the waypoint
| `event`       | `"enter"` or `"leave"`, depending on whether the <br />device is entering or leaving a configured region, respectively.
| `rid`         | the _region ID_ of the region
| `lat`, `lon`  | where the region was detected (entering respectively leaving)
| `tid`         | trackerID 
| `tst`         | timestamp when the event was published
| `wtst`        | the time stamp of when the region was created 

For example, this JSON might be published when entering a region:

```json
{
      "_type": "transition",
      "desc": "My favorite coffee shop (Delaville)",
      "event": "enter",
      "lat": 48.87069,
      "lon": 2.34916,
      "rid": "f7676c",
      "tid": "j1",
      "tst": 1707057574,
      "wtst": 1610104395
}
```

If you set up a region (or waypoint or geo-fence, you get the drift), the app publishes that region (with `retain=0` irrespective of your general preference) to the base topic with `/waypoint` tacked onto the topic (e.g. `owntracks/<user>/<device>/waypoint`) with the payload for `_type=waypoint` as specified in the [JSON page](../tech/json.md). Entering or leaving a waypoint will be published as a `transition` message and will contain a `wtst` (for historical purposes) with the timestamp of when the region was originally defined. (Note that that branch is `/waypoint` -- singular.)

For example, If Jane configures a region on her iPhone, the app could publish the following payload to `owntracks/jane/phone/waypoint`:

```json
{
    "_type": "waypoint",
    "desc": "My favorite coffee shop (Delaville)",
    "lat": 48.87069,
    "lon": 2.34916,
    "rad": "50",
    "tst": "1385997757",
    "rid": "f7676c",
    "wtst": 1610104395
}
```

When you set up a region (with a `desc`ription and a `rad`ius), this new
region is published to the broker with the current time stamp. If you update
the region definition on the device at a later stage, the region is
re-published, with the _original_ timestamp, but with possibly new
`desc`ription and/or `rad`ius and `lat`itude / `lon`gitude, but with the same
`rid`. To be precise, the `rid` (region ID) is used as an
identifier of the region even if it is later modified on the device.

Subscribers to the broker (our apps and any other program) can avoid getting
regions by subscribing to, say, `owntracks/+/+`; also broker ACLs can
prohibit access to `owntracks/+/+/waypoint` for particular users if so desired.
Conversely, all messages published by the apps (`location`, `transition`, and `waypoint`) are available with a subscription to `owntracks/#`.

The OwnTracks apps may keep track of regions, e.g. for displaying to users. 

### In regions

When the apps publish a `_type = location` message, they add to that an array
called `inregions` (and as from January 2021 an array called `inrids`) with
the names of the regions and their respective `rid`s. The `inregions` array
might be consumed by humans whereas the `inrids` array would be consumed by
developers who will wish to associate the region IDs (`rid`) with the values in
`inrids`.

### Adding on iOS

On iOS you can navigate to the place where you want to place a region, Tap long and edit the Region. The region gets a default name (`Here ..`) which you will want to change to something meaningful. With a bit of practice, you can then drag the region to it's final destination on the map or, and this may be easier, edit the region and specify its exact coordinates.

### Adding on Android

On Android, regions can be configured on a separate activity. Coordinates for the geographical region can be entered by hand or by using the place picker.  

### Remote loading of regions

Instead of configuring multiple regions on the device, they can be loaded [remotely](remoteconfig.md) with the `setWaypoints` cmd message if remote configuration is enabled on the device. 

You can use this to import individual regions or groups of pre-configured regions which are merged into the existing regions definition on the device. Recall, however, that the `rid` element is like a _key_ which uniquely identifies each region / waypoint; leave it unchanged when modifying and resubmitting a waypoint.

```json
{
    "_type": "cmd",
    "action": "setWaypoints",
    "waypoints": {
        "_type": "waypoints",
        "waypoints": [
            {
                "_type": "waypoint",
                "tst": 1708625557,
                "rid": "my-region-id-1",
                "desc": "home",
                "rad": 100,
                "lat": 30.0,
                "lon": 40.0
            },
            {
                "_type": "waypoint",
                "tst": 1708625558,
                "rid": "my-region-id-2",
                "desc": "work",
                "rad": 100,
                "lat": 30.1,
                "lon": 40.1
            }
        ]
    }
}
```

Assuming the above is in a file named `wp.json`, a user can publish that to themselves (or an authorized user to any other) as follows (note the use of `jq -c` which ensures a single line of JSON and the benefit that `jq` verifies for valid JSON):

```console
$ jq -c . wp.json |
      mosquitto_pub -u username -P 'password' -t owntracks/jane/nokia/cmd -q 1 -l
```

### Deleting regions

On iOS a region can be deleted by swiping it away. On Android, a long press is required. Note that deleting a region on the device does not publish anything to the MQTT broker or HTTP server.

### Beacons Ranging

In addition to circular regions, the iOS app can monitor its proximity to [BLE Beacons](beacons.md). 
