## Location data

Location data is obtained by your smartphone and published to the [MQTT broker](../guide/broker.md) as follows:

The Android and iOS apps offer 4 modes of location publication as well as region monitoring:

* _Quiet_ mode: Only manual location reports. Icon `[]`
* _Manual_ mode: Manual location reports and automated reports with region monitoring. Icon `||`
* _Significant location change_ mode: Standard tracking mode with automated location reports. Icon `|>`
* _Move_ mode: Frequent location  `||>`

All four modes work the same but behave slightly different on iOS or Android. In addition to region monitoring, iOS also supports location reporting based on iBeacons. 

### iOS 
#### _Move_ mode 

In _move_ mode, the app monitors location permanently and publishes a new
location as soon as the device moves `locatorDisplacement` meters or after `locatorInterval` seconds, whatever
happens first. `locatorDisplacement` and `locatorInterval` can be adjusted by the user in the systems settings for
OwnTracks. The defaults are 100m and 300 seconds (5 minutes). 

The payoff is higher battery usage as high as in navigation or tracker app.
So it is recommend to use _move_ mode while charging or during moves only - hence the name.

Please note, _move_ mode is active when the app is active (a.k.a in foreground).

#### _Significant location change_ mode

iOS defines a _Significant location change_ as traveling a distance of at least
500 meters in 5 minutes.  This mode allows the app to run in background and
minimize the power consumption.

This standard tracking mode reports significant location changes only (>500m
and at most once every 5 minutes).  This is defined by Apple and is optimal
with respect to battery usage.

Examples:

* if you don't move, no new location is published - even if you don't move for hours. (Note, however, that the app will publish a _ping_-type message once in a while.)
* if you move at least 500 meters, a new location will be published after 5 minutes
* if you move 10 kilometers in 5 minutes, only one location will be published


#### _Manual_ mode

The app doesn't monitor location changes in _manual_ mode while in background.
The user has to publish the current location explicitly via the UI. You use this if
you want to (temporarily) avoid friends seeing where you are. Note that Region events
triggered by entering or leaving Geo Fence or Beacon regions
are still published automatically whilst in _Manual_ mode.

#### _Quiet_ mode

Same as _Manual_ mode except that no region events are published.

#### _Region_ monitoring

The app user may mark a previously manually published or manually created 
location as a monitored circular region by specifying a monitoring radius in meters. (See [Regions](waypoints.md).)
The app will publish the location
additionally every time the device leaves or enters one of the regions, and the
published data contains an indication of whether the device is entering or
leaving the region.

Region monitoring is not related to one of the location publication modes and
works independently thereof. It is switched on when a region is setup with description
and radius. To switch region monitoring off, all regions have to be 
unmarked (by setting their radius to 0).

Regions are shown on the map display in transparent blue or red circles. Red
indicates the device is is within the region.

#### iBeacon monitoring

The app user may mark a previously manually published or manually created location
as a monitored beacon region by appending a beacon UUID to the region's name.
The app will publish the location
additionally every time the device leaves or enters one of the beacon regions, and the
published data contains an indication of whether the device is entering or
leaving the region.

Region monitoring is not related to one of the location publication modes and
works independently. It is switched on when a region's name has a valid UUID
appended to it.

If the device is within a monitored beacon region, the the beacon indicator
is shown in red, otherwise blue meaning device is not in any iBeacon region.

There are 2 kinds of locations:

* an _automatic location_ created when iOS detects a change of location
* a _manual location_ created by the user

A _manual location_ with a non-zero length remark (description) is a _waypoint_.

A _waypoint_'s attributes are published when the waypoint is created or changed.

If a _waypoint_ specifies a radius, a _circular region_ is monitored for _enter/leave events_.

If a _waypoint_ is not a _circular region_ and the waypoint's description contains a valid iBeacon specification, a _beacon region_ is monitored for enter/leave events.

If an enter/leave event occurs an event message is published with the `type` attribute set to `c`or `b`for (_circular region_or _beacon region_). The message contains an `event` attribute specifying either `enter`or `leave`.

The description of the _waypoint_ is added to the published `event` message.

| Automatic | Description | iBeacon | Radius | Event Message | /w Description | Waypoint Message |
|-----------|-------------|---------|--------|---------------|----------------|------------------|
| Y         | n/a         | n/a     | n/a    | N             | N              | N                |
| N         | N           | n/a     | n/a    | N             | N              | N                |
| N         | Y           | N       | N      | N             | N              | Y                |
| N         | Y           | N       | Y      | `c`           | N              | N                |
| N         | Y           | N       | Y      | `c`           | Y              | Y                |
| N         | Y           | Y       | N      | `b`           | N              | N                |
| N         | Y           | Y       | N      | `b`           | Y              | Y                |
| N         | Y           | Y       | Y      | `c`           | N              | N                |
| N         | Y           | Y       | Y      | `c`           | Y              | Y                |

### Android

#### _Move_ mode 

In _move_ mode, the app monitors device location permanently. It requests a location fix every 10s (by default, configurable in the settings through `moveModeLocatorInterval`) in high power mode and publishes a new location as soon as it arrives. 

This mode mostly relies on GPS location data and is hence the most accurate. The payoff is a higher battery usage.
It is recommend to use _move_ mode while charging or during periods that require highly accurate tracking while moving quickly. 

The `locatorDisplacement` option is ignored in this mode.

#### _Significant location change_ mode

This standard tracking mode is aimed at everyday usage for location tracking in the background. It uses a balanced power location request that gathers a new location fix every 15 minutes. Location data from other apps is reused and published as soon as it arrives. 

This mode relies mostly on cell tower and WiFi location to conserve power to provide location data that is sufficiently accurate for most users. 

In addition to the default settings, all location request parameters in this mode can also be changed. These parameters directly influence the raw [location request](https://developers.google.com/android/reference/com/google/android/gms/location/LocationRequest) that is send to the Android location API.  

* `locatorInterval`: The [desired interval](https://developers.google.com/android/reference/com/google/android/gms/location/LocationRequest#public-locationrequest-setinterval-long-millis) for active location updates.
>    "The location client will actively try to obtain location updates for your application at this interval, so it has a direct influence on the amount of power used by your application. Choose your interval wisely."

* `moveModeLocatorInterval`: The requested location interval in _move_ mode.

* `locatorDisplacement`: The smallest displacement in meters the user must move between location updates. Defaults to 0 and is an `and` relationship with interval. Can be used to only receive updates when the device has moved.
    * This means if the user has not moved by more than the displacement value, the location will not be reported at `locatorInterval`.

* `locatorPriority`: The priority of the request is a strong hint to the LocationClient for which location sources to use (e.g. wifi, cellular or gps). The accepted values are:
    * `0`: [No power usage](https://developers.google.com/android/reference/com/google/android/gms/location/LocationRequest#public-static-final-int-priority_no_power) "No locations will be returned unless a different client has requested location updates"
    * `1`: [Low power usage](https://developers.google.com/android/reference/com/google/android/gms/location/LocationRequest#public-static-final-int-priority_low_power) "City level accuracy is considered to be about 10km accuracy."
    * `2`: [Balanced power usage](https://developers.google.com/android/reference/com/google/android/gms/location/LocationRequest#public-static-final-int-priority_balanced_power_accuracy) "Block level accuracy is considered to be about 100 meter accuracy." (default)
    * `3`: [High power usage](https://developers.google.com/android/reference/com/google/android/gms/location/LocationRequest#public-static-final-int-priority_high_accuracy) "This will return the finest location available."

#### _Manual_ mode

In _manual_ mode, the app monitors device location with a low power location request. It uses the same interval configured for _significant mode_ to receive low accuracy updates to use minimal battery power. 

The user has to publish the current location explicitly via the UI. You use this if you want to (temporarily) avoid friends seeing where you are. Note that Region events triggered by entering or leaving Geo Fence are still published automatically whilst in _Manual_ mode.

Remote `reportLocation` commands are ignored. 

#### _Quiet_ mode

Same as _Manual_ mode except that no region events are published.



