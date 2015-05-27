## Startup

After [downloading and installing the iPhone/iPad app](https://itunes.apple.com/en/app/mqttitude/id692424691?mt=8), start it
up.

You will see the launch image
<img src="https://raw.github.com/wiki/owntracks/owntracks/assets/ios/iphone4-launch.png" width="200px" height="300px" />

followed by OwnTracks' main screen. When the main screen appeares it shows the title bar
<img src="https://raw.github.com/wiki/owntracks/owntracks/assets/ios/iphone4-title-on.png" width="200px" height="300px" />

but hides it after a few seconds to give more room for the map (examples show iPhone 3 1/2", but looks similar on iPhone 4" and iPad).

<img src="https://raw.github.com/wiki/owntracks/owntracks/assets/ios/iphone4-title-off.png" width="200px" height="300px" />

The first time you start the app, the system asks you to give OwnTracks access to your location. Please hit OK.
<img src="https://raw.github.com/wiki/owntracks/owntracks/assets/ios/iphone5-use-location.png" width="200px" height="300px" />

Now you'll notice the connection indicator in _red_ (iPhone top/right, iPad bottom/right). Let's head to settings by
using the _Settings_ tab on the iPhone or by activating the popover menu (hit 'Friends' button on bottom/left) and then _Settings_ tab on the iPad.

<img src="https://raw.github.com/wiki/owntracks/owntracks/assets/ios/ipad-friends.png" width="300px" height="200px" />

## Settings

You're now in the settings panel for OwnTracks, and there are some things
you have to change. (See [[general settings|Settings]] for additional information.)

Don't get overwhelmed by the number of settings, there are only 4 things you need to change (marked with an asterisk '*').
1. _DeviceID_ is a short name you'll give your device. Good examples could be `iphone`, `myphone`, or `xyzz90`.
2. _Host_ is the host name or IP address of your MQTT broker. (The port defaults to 8883 because we default to using TLS.)
3. _UserID_ is your username on the broker. Even if your broker doesn't enforce authentication, you _must set a UserID_.
4. _Password_ is the password for _UserID_.

<img src="https://raw.github.com/wiki/owntracks/owntracks/assets/ios/iphone5-settings1.png" width="200px" height="300px" />
<img src="https://raw.github.com/wiki/owntracks/owntracks/assets/ios/iphone5-settings2.png" width="200px" height="300px" />
<img src="https://raw.github.com/wiki/owntracks/owntracks/assets/ios/iphone5-settings3.png" width="200px" height="300px" />
<img src="https://raw.github.com/wiki/owntracks/owntracks/assets/ios/iphone5-settings4.png" width="200px" height="300px" />

After adjusting your settings, you can check the MQTT broker connection by hitting _Check_ and watch the status change or look at the detail status screen (information button right of connection status in the first row).

<img src="https://raw.github.com/wiki/owntracks/owntracks/assets/ios/iphone5-check.png" width="200px" height="300px" />
<img src="https://raw.github.com/wiki/owntracks/owntracks/assets/ios/iphone5-error-ok.png" width="200px" height="300px" />
<img src="https://raw.github.com/wiki/owntracks/owntracks/assets/ios/iphone5-error-nok.png" width="200px" height="300px" />

You do not need to fill out all fields, however to check the effective values have a look by hitting the information button to the right of UserID, ClientID, Topic or Will Topic.

Quality of Service (QoS) settings for Reporting, Subscription or Will are indicated next to the descriptive label and can be altered by selecting the corresponding value after opening the detail selection screen.

<img src="https://raw.github.com/wiki/owntracks/owntracks/assets/ios/iphone5-qos.png" width="200px" height="300px" />

Scroll down to the bottom of the screen to find the current application version, license information, a link to this documentation and the possibility to export all settings.

<img src="https://raw.github.com/wiki/owntracks/owntracks/assets/ios/iphone5-export.png" width="200px" height="300px" />

If you have successfully connected to your MQTT broker, switch the the main screen again (use the OwnTracks tab on iPhone or tap on the map on iPad).

### Follow your broker

At this time you should check that the MQTT broker is actually receiving location messages. (See [[MQTT broker|MQTTbroker]] on how to do this.)

### Main Screen

Let's look at the main screen for a moment:

<img src="https://raw.github.com/wiki/owntracks/owntracks/assets/ios/ipad-start.png" width="300px" height="200px" />

The icons on the bottom/top (iPad/iPhone) row are as follows:

1. First Button is User Tracking Mode
2. Second Button is Monitoring Mode and Indicator
	- a `PAUSE` symbol indicates no automatic location monitoring 
	- a `PLAY`symbol indicates significant location monitoring
	- a `FAST FORWARD` indicates move mode monitoring
	(See [[Location]])
3. Third Button is Beacon Mode and Indicator
	- RED = inside iBeacon region
	- BLUE = not within an iBeacon region
	- GRAY = iBeacon status not available
	- flashing = continously updating iBeacon distances (a.k.a. ranging)
4. Forth Button is Connection Switch and Indicator
	- BLUE=IDLE, no connection established
	- GREEN=CONNECTED, server connection established
	- AMBER=ERROR OCCURED, WAITING FOR RECONNECT, app will automatically try to reconnect to the server
	- RED=ERROR, no connection to the server possible or transient errror condition
	- animated size changes indicate outgoing messages

The system network activity indicator (in top status bar) is now used to indicate actual
transmission of messages.

To switch Map Mode, Monitoring Mode, Beacon Ranging and Switch Connection, tap on the corresponding button and select one of the displayed options:

<img src="https://raw.github.com/wiki/owntracks/owntracks/assets/ios/ipad-map-modes.png" width="300px" height="200px" />
<img src="https://raw.github.com/wiki/owntracks/owntracks/assets/ios/ipad-monitoring.png" width="300px" height="200px" />
<img src="https://raw.github.com/wiki/owntracks/owntracks/assets/ios/ipad-ranging.png" width="300px" height="200px" />
<img src="https://raw.github.com/wiki/owntracks/owntracks/assets/ios/ipad-connection.png" width="300px" height="200px" />

Tap on Map Mode Follow to see your position.

### Friends

The [[Friends]] feature shows self and all Friends published by the server,
their picture or OwnTracks default icon, and their last location.  Tapping on
the entry switches back to map centered on friend's last location.

The first time you launch the app you'll also be asked whether it may access your
contacts data, which you should allow. (If you change your mind later, _Privacy_ in
_General settings_ allows you to disable the feature.)

Tapping on the disclosure indicator (little right-arrow) lists all received locations of the friend.
App keeps track of a number (see Settings) of own locations and the last location and sharedpoint of others.s
 Tapping on an location entry centers the map on the selected location.

If the location was set manually, a disclosure indicator allows changing the remark of the location (e.g. parked my car here!, started my run here!). Manual locations can only be deleted manually.

Deleting locations or all locations of a friend is done by left-swipe on the entry.

Q. As regards the friends list: when is reverse geo-coding done?

Reverse geo-coding is done when displaying/editing location details by either
* tapping the info button of a location callout on the map or
* navigating via friends/locations/...

No geo-coding is done automatically in the background to limit mobile data usage.

### Location indicators and friendly faces

The locations of yourself, your friends and of the waypoints you set are displayed as little yellow circles. The color of the circumfence indicates
* Orange: yourself
* Blue: one of your waypoints
* Green: one of your friends

<img src="https://raw.github.com/wiki/owntracks/owntracks/assets/ios/iphone5-circles.png" width="200px" height="300px" />

The inner area of the circle either shows a picture of yourself / your friend or the Tracker ID (tid) which is usually 2 digits and defaults to the last two characters of the publish topic.

A red, yellow rimmed tachometer indicating the velocity (speed) at the reported location starts at at the 6 o'clock position and works clockwise. It uses a logarithmic scale to show slow speeds
as good as high speeds. The 9 o'clock position indicates a speed of 30 km/h, the 12 o'clock posistion a speed of 100 km/h. 3 o'clock means 180 km/h.

A small blue, yellow rimmed semicircle at the perimeter indicates the course over ground (direction of movement)  with the 12 o'clock position meaning North.

If your iOS addressbook has an entry for, say, Jane Jolie, and Jane has an image associated with her addressbook entry, OwnTracks will show Jane's image on the map and on the [[Friends]] list, as soon as a location update for Jane is seen by the app.

In order to associate an MQTT topic with our friend (Jane, in this case), edit your addressbook entry on iOS for Jane and do either of the following:

* Create a new relashionship (like 'Spouse') called `OwnTracks` (case insensitive), and add your friend's topic name to that (e.g. `owntracks/jane/loc`)

The addressbook API might need a while to be refreshed, but you may be able to speed that up by swiping the Friends list downward until the activity indicator appears, then let go).

or...

Tap the Bookmark Button in the Navigation Bar on the Location screen. Select an entry from your Address Book.
The entry will be marked with a relationship to the current friend's topic name.

Beginning with iPhone app version 5.1, there's now a switch in Expert Mode settings titled _Update Address Book". If this is _on_, the app uses related names from the address book to identify topics and updates address book as described above. If the switch is _off_, the app maintains an internal reference to the address book record and doesn't modify the address book.


### Region Monitoring and Waypoints

For all manually published locations, a description, a region radius and a share flag can be edited.

Setting the description of a location helps you to remember places.

If a description is entered and the share flag set, the location is published to the MQTT broker as a `waypoint` once.
Waypoints shared by other users are displayed on the map and are visible in the Friends/Locations... table.

If the description is non-empty and a radius > 0 (meters) is set, the app starts monitoring the circular region around the coordinate. A region may be 'waypoint'.
The regions are shown on the map as blue-ish circles. If the device is within a region, the corresponding circle turns red-ish.

If the description is non-empty and includes a value UUID for an iBeacon, the app starts monitoring the iBeacon region. e.g. 'myBeacons:CA271EAE-5FA8-4E80-8F08-2A302A95A959'
monitors any iBeacon identified with UUID = CA..., with major and minor numbers irrelevant.

Everytime the devices enters or leaves a monitored region, an additional location message is published to the MQTT broker.

Use cases:
* Define a `home` region to insure that the device publishes a new location when coming home or leaving home even if you do not move more than 500m.
* Share your favorite places with your friends ("Best Sushi in Town").
* Keep a private note ("Parked Car here").
* Use iBeacons to accurately report enter/leave events for your home
* Use iBeacons to publish enter/leave events for individual rooms/floors in your home

### Waypoints

All manually published locations can be edited (by tapping on the `(I)` to add
a description. If the _Share_ flag is on, this waypoint is published to the
[[MQTT broker|MQTTbroker]] along with its location information. Setting a
description helps you remember particular points of interest.

If a description is entered and the share flag set, the location is published
to the MQTT broker as a waypoint once. Waypoints shared by other users are
displayed on the map and are visible in the _Friends/Locations_ table.

### Region monitoring (a.k.a. Geo Fences)

If the waypoint description is non-empty and a radius > 0 (meters) is set, the
app starts monitoring the circular region around the specified coordinates. 
Regions are shown on the map as blue-ish circles. If the
device is within a region, the corresponding circle turns red-ish. Every time
the devices enters or leaves a monitored region, an additional location message
is published to the MQTT broker, specifying whether the device is entering or leaving
said region.

<img src="https://raw.github.com/wiki/owntracks/owntracks/assets/ios/iphone5-notification-center.jpg" width="300px" height="200px" />

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

### Remote configuration

You can help your friends customize their copy of OwnTracks with [[remote configuration|RemoteConfig]].

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
restarted eventually - depending on IOS' background execution model about every 10 minutes.

With automatic modes switched off, even when bringing the app to foreground, no
new location updates are generated.

