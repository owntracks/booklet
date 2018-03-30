## Location data

Location data is obtained by your smartphone and published to the [MQTT broker](../guide/broker.md) as follows:

### Android

The way it currently works is by specifying an interval in which you want to receive updates (the one you configure in the settings). You only receive updates if this amount of time has passed and you moved more than 500m (when the app is in the background. The movement restriction is set to 50m or so when in the foreground).

Say I set an interval of 10min and I don't move; will I get a PUBlish? No. 
If you move more than 500 meters in 5 minutes you'll get a PUB after 10 minutes. You won't get a PUB after 5 minutes though.

Additional restrictions apply regarding the update intervall. When switching into the background or foreground, a new location request is setup with a different granularity and energy requirement. As a result, a new location is received immedeately by the app. 

Thus, when the app goes into the background, a new location is received. Most likely, this will not result in a publish as the last publish was not longer ago than 10 minutes. Say the last update was exactly 9 minutes ago when the background mode is enabled. In this case no publish will be sent. However, the next location will probably only be received after the specified 10 minutes. At this point, the time of the last publish is longer than 10 minutes ago. So in this worst case, around 20 will pass before a location will be sent. 

If you switch on the app after one minute in the background (11 minutes after the last publish), a new report will be sent immediately as soon as a new location is available. 


### iOS 

The iOS app offers 4 modes of location publication:

* _Quiet_ mode
* _Manual_ mode
* _Significant location change_ mode
* _Move_ mode

In addition to this, there's also _Region Monitoring_ (a.k.a. Geo Fence) and _iBeacon Monitoring_ and _iBeacon Ranging_.

#### _Move_ mode 

In _move_ mode, the app monitors location permanently and publishes a new
location as soon as the device moves `x` meters or after `t` seconds, whatever
happens first. `x` and `t` can be adjusted by the user in the systems settings for
OwnTracks. The defaults are 100m and 300 seconds (5 minutes). 

The payoff is higher battery usage as high as in navigation or tracker app.
So it is recommend to use _move_ mode while charging or during moves only - hence the name.

Please note, _move_ mode is active when the app is active (a.k.a in foreground).

#### _Significant location change_ mode

iOS defines a _Significant location change_ as travelling a distance of at least
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
location as a monitored circular region by specifying a monitoring radius in meters. (See [Waypoints](waypoints.md).)
The app will publish the location
additionally everytime the device leaves or enters one of the regions, and the
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

If a _waypoint_ is _shared_, its attributes are published when the waypoint is created or changed.

If a _waypoint_ specifies a radius, a _circular region_ is monitored for _enter/leave events_.

If a _waypoint_ is not a _circular region_ and the waypoint's description contains a valid iBeacon specification, a _beacon region_ is monitored for enter/leave events.

If an enter/leave event occurs an event message is published with the `type` attribute set to `c`or `b`for (_circular region_or _beacon region_). The message contains an `event` attribute specifying either `enter`or `leave`.

If the _waypoint_is _shared_, the description of the _waypoint_ is added to the published `event` message.

| Automatic | Description | iBeacon | Radius | Shared | Event Message | /w Description | Waypoint Message |
|-----------|-------------|---------|--------|--------|---------------|----------------|------------------|
| Y         | n/a         | n/a     | n/a    | n/a    | N             | N              | N                |
| N         | N           | n/a     | n/a    | n/a    | N             | N              | N                |
| N         | Y           | N       | N      | N      | N             | N              | N                |
| N         | Y           | N       | N      | Y      | N             | N              | Y                |
| N         | Y           | N       | Y      | N      | `c`           | N              | N                |
| N         | Y           | N       | Y      | Y      | `c`           | Y              | Y                |
| N         | Y           | Y       | N      | N      | `b`           | N              | N                |
| N         | Y           | Y       | N      | Y      | `b`           | Y              | Y                |
| N         | Y           | Y       | Y      | N      | `c`           | N              | N                |
| N         | Y           | Y       | Y      | Y      | `c`           | Y              | Y                |
