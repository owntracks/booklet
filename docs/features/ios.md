## iOS

* Runs on iPhones and iPads as an iPhone app, and on Macs. Not tested on iPods.

* Monitors "significant location changes" as defined by Apple Inc. (about 5 minutes AND 
  	"significant location changes" (>500m)) or as described in Move Mode below.
	In addition version >= 5.3 supports circular region monitoring (aka geo fences).
	In addition version >= 7.7 supports iBeacon region monitoring and ranging.

* publishes this locations via MQTT to the configured server while in foreground and background.

* The current location can be sent (a.k.a. published) on request.

## Startup

After [downloading and installing the iPhone/iPad app](https://itunes.apple.com/en/app/mqttitude/id692424691?mt=8), start it up.

The first time you start the app, the system asks you to give OwnTracks access to your location which it requires in order to, well, find your location.


## Settings

Access settings and the individual modes by pressing on the `I` on the top left.

## Friends

The Friends feature shows self and all Friends published by the server,
their picture or OwnTracks default icon, and their last location.  Tapping on
the entry switches back to map centered on friend's last location.

The first time you launch the app you'll also be asked whether it may access your
contacts data, which you should allow. (If you change your mind later, _Privacy_ in
_General settings_ allows you to disable the feature.)

Tapping on an location entry centers the map on the selected location.

No geo-coding is done automatically in the background to limit mobile data usage.

### Location indicators and friendly faces

The locations of yourself, your friends and of the waypoints you set are displayed on the map. The inner area of the circle either shows a picture of yourself / your friend or the Tracker ID (tid) which is usually 2 characters and defaults to the last two characters of the publish topic.

A red, yellow rimmed tachometer indicating the velocity (speed) at the reported location starts at at the 6 o'clock position and works clockwise. It uses a logarithmic scale to show slow speeds
as good as high speeds. The 9 o'clock position indicates a speed of 30 km/h, the 12 o'clock posistion a speed of 100 km/h. 3 o'clock means 180 km/h.

A small blue, yellow rimmed semicircle at the perimeter indicates the course over ground (direction of movement)  with the 12 o'clock position meaning North.

If your iOS addressbook has an entry for, say, Jane Jolie, and Jane has an image associated with her addressbook entry, OwnTracks will show Jane's image on the map and on the [Friends](friends.md) list, as soon as a location update for Jane is seen by the app.

## Regions

### Region Monitoring and Waypoints

Regions can be created with a description and a radius. If the description is non-empty and a radius > 0 (meters) is set, the app starts monitoring the circular region around the coordinate. These regions are shown on the map as blue-ish circles. If the device is within a region, the corresponding circle turns reddish.

If the description is non-empty and includes a value UUID for an iBeacon, the app starts monitoring the iBeacon region. e.g. 'myBeacons:CA271EAE-5FA8-4E80-8F08-2A302A95A959' monitors any iBeacon identified with UUID = CA..., with major and minor numbers irrelevant.

Every time the devices enters or leaves a monitored region, an additional so-called _transition_ message with an enter or leave event is published to the MQTT broker.

Use cases:
* Define a `home` region to insure that the device publishes a new location when coming home or leaving home even if you do not move more than 500m.
* Share your favorite places with your friends ("Best Sushi in Town").
* Keep a private note ("Parked Car here").
* Use iBeacons to accurately report enter/leave events for your home
* Use iBeacons to publish enter/leave events for individual rooms/floors in your home

See also [Waypoints](waypoints.md).

### Region monitoring (a.k.a. Geo Fences)

If the waypoint description is non-empty and a radius > 0 (meters) is set, the
app starts monitoring the circular region around the specified coordinates.
Regions are shown on the map as blue-ish circles. If the
device is within a region, the corresponding circle turns red-ish. Every time
the devices enters or leaves a monitored region, an additional location message
is published to the MQTT broker, specifying whether the device is entering or leaving
said region.


For example, you can define a home region to ensure that the device publishes a
new location when coming home or leaving home even if you do not move more than
500m. Share your favorite places with your friends ("Best Sushi in Town"). Keep
a private note ("Parked Car here").

### Region Monitoring with iBeacons

Rather than setting the radius, you may add your iBeacon's UUID and optionally major and minor numbers
to the description to start region monitoring using iBeacons.

e.g.
* 'myBeacon:CA271EAE-5FA8-4E80-8F08-2A302A95A959:0001:CAFE'
reports region with description 'myBeacon' when the iBeacon identified with UUID = CA..., major = 0001 and minor = CAFE is in proximity

* 'myBeacons:CA271EAE-5FA8-4E80-8F08-2A302A95A959'
reports region with description 'myBeacons' when any iBeacon identified with UUID = CA..., with major and minor numbers irrelevant is in proximity

### Region Monitoring with `+follow`

Create a region with a name begining with a `+` character, e.g. `+follow` and set an initial radius to be greater than zero. From this moment onwards, _lat_ and _lon_ will be set dynamically at each location publish and _radius_ will be the distance you've covered in 30 seconds, at least 50 meters.

Available in version > 15.x.x: If the name of such a region starts with a _number_ (e.g. `+60follow`), the dynamically set new _radius_ will be the distance you've covered in _number_ seconds (minimum 50 meters).

This _follow_-type region will not trigger `enter/leave` events or notifications, and location publishes will have a type setting of `"t" : "C"`.

### Region Monitoring with automatic monitoring mode change
*Available in version > 16.x.x*

Create a region with a name ending with `'|<monitoring mode on enter>|<monitoring mode on exit>`. E.g. `Home|1|2` will
switch to monitoring mode 2 (Move Mode) when exiting the region `Home` and will switch back to monitoring mode 1 (Significant
Mode) when entering the region `Home`.

Please note automatic change to monitoring mode -1 (Quiet Mode) will stop all further region detection.

As the automatic change to monitoring mode 2 (Move Mode) does not work reliably under all circumstances, we recommend to
add a `+follow` region, which will trigger a wake up of the app when moving.

## Smart Monitoring Mode Changes

### Automatic battery level controlled monitoring mode change
*Available in version > 16.x.x*

A new Settings parameter "downgrade" indicates the percentage of battery level which is the threshold.

If the phone is in Move Mode and the battery level drops below "downgrade" percent, the app will automatically switch from Move Mode to Significant Mode.

If the phone did that automatic switch and detects the charger is connected, it will automatically switch back to Move Mode.
This will happen when then next location update in Significant mode is processed. 

The fact the app is in "downgraded" mode is indicated in the UI by a trailing `!` to the `Significant` mode (e.g. `Significant!`).
 
### Automatic movement controlled monitoring mode change
*Available in version > 18.4.x*

A new Settings parameter "adapt" indicates the minutes of non-movement after which the iPhone will switch from Move Mode to Significant Mode.
"adapt" inactive if set the 0 (zero).

If the phone did that automatic switch and gets the next location update, it will automatically switch back to Move Mode.
This will happen when then next location update in Significant mode is processed. It is best practice to setup a `+follow` region as described above
to ensure timely switching back to Move Mode.

The fact the app is in "adapt" mode is indicated in the UI by a trailing `#` to the `Significant` mode (e.g. `Significant#`).
 
## Miscellaneous

### Offline Mode and GPX Export

To avoid connection to a backend server, please select MQTT mode and use Port 0 (zero).

The iPhone will keep all locations for the last number of days specified in the `days` configuration setting.

On the status screen, there is a possibility to export the stored locations in GPX format.

### Remote configuration

You can help your friends customize their copy of OwnTracks with [remote configuration](remoteconfig.md).

### Why do location publishes sometimes seem to cease to work?

Here's what happens:

If the app goes into Background, the connection is disconnected because the app
cannot maintain the TCP connection in background.

If you bring the app back into foreground, the app will re-connect.

When a location change is recorded (no matter if manual, significant, move or
region mode), a message is prepared and an attempt to connect to the broker is
started. If there is an error, connect will be retried after 2, 4, 8, ..., 64,
64, .... seconds until it succeeds.

BUT...

If the application is in background or is sent to the background, retries are
restarted eventually - depending on iOS' background execution model about every 10 minutes.

With automatic modes switched off, even when bringing the app to foreground, no
new location updates are generated.

