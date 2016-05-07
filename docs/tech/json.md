## JSON

OwnTracks publishes its message payloads in [JSON](http://www.json.org) format. The different payload types are identified by a `_type` element. Depending on the app platform, different payload types are supported. 

| `_type`                                   |  iOS   | Android |
| ----------------------------------------- | :----: | :-----: |
| `location`                                |   Y    |   Y     |
| `lwt`                                     |   Y    |   Y     |
| `waypoint`                                |   Y    |   Y     |
| `transition`                              |   Y    |   Y     |
| `beacon`                                  |   Y    |   N     |
| `cmd`                                     |   Y    |   Y     |
| `steps`                                   |   Y    |   N     |
| `configuration`                           |   Y    |   Y     |
| `card`                                    |   Y    |   Y     |
| `waypoints`                               |   Y    |   Y     |
| `encrypted`                               |   Y    |   Y     |


## Topics

In MQTT mode the apps publish to:

- `owntracks/user/device` with `_type=location` for location updates, and with `_type=lwt`
- `owntracks/user/device/cmd` with `_type=cmd`      for remote commands
- `owntracks/user/device/event` with `_type=transition` for enter/leave events



Apps subscribe to:

- `owntracks/user/device/cmd` if remote commands are enabled
- `owntracks/+/+` for seeing other user's locations, depending on broker ACL
- `owntracks/+/+/event` (singular) for transition messages (`enter`/`leave`)
- `owntracks/+/+/waypoint`  (singular) for Waypoint definitions/modifications
- `owntracks/+/+/info`  for obtaining [cards](../features/card.md).

In addition, the iOS app publishes to:

- `owntracks/user/device/step` to report step counter
- `owntracks/user/device/beacon` for beacon ranging
- `owntracks/user/device/dump` for config dumps

In HTTP mode the apps POST their data to a single endpoint you configure. That endpoint needs to distinguish which type of payload it is receiving.

## `_type=location`

This location object describes the location of the device that published it. **Heads up: as from Android version 0.5.11 and from iOS version 8.0 numeric types are real numeric (`float`, `int`, boolean `true`/`false`) and no longer strings.**

```json
{
    "_type" : "location",        
    "acc"   : 75,              
    "alt"   : 13,
    "batt"  : nnn,              
    "cog"   : 270,
    "desc"  : "sssss",
    "event" : "sssss",
    "lat"   : x.xxxxxx,       
    "lon"   : y.yyyyyy,        
    "rad"   : nnn, 
    "t"     : "x",
    "tid"   : "YY",
    "tst"   : 1376715317,      
    "vacc"  : 10,
    "vel"   : 54,
    "p"     : nnn
}
```

* `acc` is accuracy of the reported location in meters without unit (integer). iOS adds this element only if it >= 0.
* `alt` is the altitude measured in meters above sea level (_Optional_, integer). iOS adds this element only if it >= 0.
* `batt` is the device's battery level in percent (integer)
* `cog` is the heading (course over ground) in degrees, 0 = North (_Optional_, integer). iOS adds this element only if it is >= 0.
* `desc` is the description of a [waypoint](../features/waypoints.md)
* `event` is one of `"enter"` or `"leave"` and tells if the app is entering or leaving a geofence 
* `lat` is latitude as decimal, represented as a floating point number
* `lon` is longitude as decimal, represented as a floating point number
* `rad` is the radius in meters around around the geo-fence when entering/leaving a geofence (integer)
* `t` is the trigger for the publish x = 
    * "p" ping, issued randomly by background task. Note, that the `tst` in a ping is a [current timestamp](https://github.com/owntracks/ios/issues/197), so that it doesn't look like a duplicate.
    * "c" circular region enter/leave event 
    * "b" beacon region enter/leave event
    * "r" response to a "reportLocation" request
    * "u" manual publish requested by the user
    * "t" timer based publish in move move
    * "a" or missing `t` indicates automatic location update
* `tid` is a configurable tracker-ID which is used by the auto-faces feature to display, say, initials of a user. If it isn't explicitly configured, it defaults to the last two characters of the device's publish topic. 
* `tst` is a UNIX [epoch timestamp](http://en.wikipedia.org/wiki/Unix_time) of the event as it occurs which may be different from the time it is published (integer, seconds).
* `vacc` is the vertical accuracy of the reported altitude in meters (_Optional_, integer). iOS adds this element only if it >= 0.
* `vel` is the velocity (speed) in km/h (_Optional_, integer). iOS adds this element only if it >= 0.
* `p` is barometric pressure in kPa (kilo Pascal) (iOS > 8.1.1 only with _Extended data_ enabled)

(The iOS device can be configured to produce or not produce fields marked as _optional_ with the Extended Data setting.)

Notes:
* A publish with of `"_type": "location"` with a `"b"` trigger is sent when an iOS device enters or leaves a beacon in addition to a `"_type": "transition"`: if somebody leaves and enters his home without having left the radius of detection for significant changes, a subscriber to his main topic would otherwise not get notified of any location change although beacon or circular region enter and leave transitions were generated.



### Greenwich

The OwnTracks-edition of the [Choral Greenwich](http://www.choral.it/greenwich&menu2=795) device reports the following additional elements in a `_type=location` message:

```json
{
    "_type": "location",
    "alt": 160,
    "cog": 0,
    "dist": 189,
    "trip": 279122,
    "lat": xx.xxxxxx,
    "lon": y.yyyyyy,
    "vel": 12,
    "t": "t",
    "tst": 1406803894
}
```

* `alt` is altitude in meters. (_Optional_)
* `batt` is the external battery voltage. (_Optional_)
* `cog` is the course over ground (heading) in degrees. (_Optional_)
* `dist` is the distance travelled in meters since the last location publish. (_Optional_)
* `trip` is the distance travelled in meters since the last reboot. (_Optional_)
* `vel` is the speed (velocity) in km/h. (_Optional_)
* `t` is the trigger of the published message:
  - `f` first publish after reboot
  - `m` for manually requested locations (e.g. by publishing to `/cmd`)
  - `t` (time) for location published because device is moving.
  - `T` (time) for location published because of time passed (`maxInterval`); device is stationary
  - `k` When transitioning from _move_ to _stationary_ an additional publish is sent marked with trigger `k` (park)
  - `v` When transitioning from _stationary_ to _move_ additional publish is sent marked with trigger `v` (mo-v-e)
  - `l` When device loses GPS fix, an additional publish is sent to transmit the last known position
  - `L` last position before gracefull shutdown

(The device can be configured to produce or not produce fields marked as _optional_.)


## `_type=lwt`

A _last will and testament_ is published automatically by the MQTT broker when it loses contact with the app. This typically looks like this:

```json
{
    "_type":"lwt",
    "tst": 1380738247
}
```

The timestamp is the Unix epoch time at which the app first connected (and *not* the time at which the LWT was published).

## `_type=waypoint`

Waypoints denote specific geographical locations that you want to keep track of. You define a waypoint on the OwnTracks device, and OwnTracks publishes this waypoint (if the waypoint is marked `shared`). OwnTracks also monitors these waypoints and will publish a transition event (`_type: transition`) when entering or leaving the region. Note, that a waypoint may also define a [Beacon](../features/beacons.md).

```json
{
    "_type"  : "waypoint",
    "desc"   : "Free text entered on device",
    "lat"    : x.xxxxxx,       
    "lon"    : y.yyyyyy,        
    "rad"    : xxx,
    "tst"    : nnnnn,
    "shared" : true,
    "tid"    : "XX",
    "uuid"   : "YYYYYYYYYYYYYYYYY",
    "major"  : 17,
    "minor"  : 1
}
```
* `shared` location messages of shared waypoints contain a desc and event attribute. Not shared ones contain an event attribute only
* `tst` is the timestamp of waypoint _creation_ even if it was subsequently modified by the user. (See [Waypoints](../features/waypoints.md).) It is copied into the `wtst` of the transition event (`type: transition`) when an event pertaining to this waypoint fires.
* In iOS version >= 9.1.0 and in Android version >= 0.6.0 the last three elements (uuid, major, and minor) are used to configure Beacon waypoints instead of encoding these values into the `desc` element.

Waypoints are published non-retained because the second waypoint would overwrite the first: a client would only get the last one which makes no sense. Your application will typically store waypoints to some kind of persistent storage.

## `_type=transition`

A transition into or out of a previously configured waypoint is effected by publishing a _transition_ to the `../event` subtopic using MQTT. (In HTTP mode the payload is published to the configured endpoint.) In addition to the coordinates where the event fired (`lat`, `lon`, and `acc` of these), the message contains the timestamp of the waypoint creation (`wtst`) as well as the `event` (which can be either `enter` or `leave`) and, in the case of a shared waypoint, it's description in `desc`. Transition messages are published with `retain=0`.

```json
{
  "_type": "transition",
  "wtst": 1425042603,           // time of waypoint creation
  "lat": 12.34,                 // transition coordinates i.e. "here"
  "lon": 44.5,
  "tst": 1427634603,            // timestamp of transition (e.g. "now")
  "acc": 130,                   // accuracy of lat, lon
  "tid": "JP",
  "event": "enter",
  "desc": "my Indian restaurant",
  "t": "b"			// trigger ("c" -default- for circular regions, "b" for beacons, "w" for WiFi (Android only))
}
```

A transition event published as a result of a Beacon movement will have a `"t" : "b"` associated with it.


## `_type=configuration`
The device configuration can be imported and exported as JSON. The exported configuration can contain an array of waypoints that are defined on the device. 

```json
{
    "_type": "configuration",
    "mode": 0,
    "locked": false,
    "deviceId": "phone",
    "clientId": "jane-phone",
    "trackerId": "xx",
    "subTopic": "owntracks/#",
    "pubTopicBase": "owntracks/jane/phone",
    "host": "broker.my.net",
    "username": "jane",
    "password": "secr3t",
    "willTopic": "",
    "subQos": 1,
    "pubQos": 2,
    "pubInterval" : 30,
    "port": 8883,
    "keepalive": 60,
    "willQos": 1,
    "pubRetain": true,
    "tls": true,
    "tlsCrtPath" : "/foo/bar",
    "auth": true,
    "cleanSession": false,
    "willRetain": false,
    "locatorDisplacement": 200,
    "locatorInterval": 180,
    "locatorAccuracyBackground" : 2,
    "locatorAccuracyForeground" : 0,
    "messaging": true,
    "monitoring": 1,
    "ranging": "",
    "positions": 50,
    "connectionAdvancedMode" : false,
    "autostartOnBoot" : false,
    "pubIncludeBattery" : false,
    "sub" : false,
    "pub" : false,
    "updateAddressBook" : false,
    "notification" : false,
    "notificationLocation" : false,
    "notificationGeocoder" : false,
    "notificationTickerOnPublish" : false,
    "notificationTickerOnGeofenceTransition" : false,
    "remoteCommandReportLocation" : false,
    "remoteCommandDump" : false,
    "cmd" : false,
    "allowRemoteLocation" : false,
    "extendedData" : false,
    "remoteConfiguration" : false,
    "usepolicy" : false,
    "policymode" : 0,
    "beaconMode" : 0,
    "allowinvalidcerts" : false,
    "validatedomainname" : false,
    "validatecertificatechain" : false,
    "servercer" : "",
    "clientpkcs" : "",
    "passphrase" : "",
    "waypoints" : [
                    { "_type" : "waypoint",
                      "tst": nnnnnnnnnn,
                      "lat": nn.nnn,
                      "lon": nn.nnn,
                      "rad": nnn,
                      "shared": true,
                      "desc" : "blabla"
                    } 
                  ]
}
```

* `mode`					0 == _Private_ mode, 2 == _Public_ mode, 3 == _HTTP_ mode
* `locked`					iOS only, if set to true, locks settings screen on device for editing. View only. Default = false. Can only be set via `.otrc` import.
* `subTopic`                                    is what the apps subscribe to
* `pubTopicBase`                                is the prefix under which the apps will publish
* `username`
* `password`
* `deviceId`
* `tid`						 is a configurable tracker-ID which is used by the auto-faces feature to display, say, initials of a user. If it isn't explicitly configured, it defaults to the last two characters of the device's publish topic. 
* `willTopic`
* `subQos`
* `pubQos`
* `willQos`
* `pubRetain`
* `cleanSession`
* `willRetain`
* `locatorDisplacement`				in meters
* `locatorInterval`				in seconds
* `locatorAccuracyBackground`			0 == high power, 1 == balanced power, 2 == low power, 3 == no power
* `locatorAccuracyForeground`			0 == high power, 1 == balanced power, 2 == low power, 3 == no power
* `monitoring`					iOS only, set in UI. The four modes are: Quiet (-1), Manual (0), Significant (1), and Move (2)
* `ranging`					iOS only, set in UI
* `positions`					iOS only, number of positions to keep and display
* `autostartOnBoot`				Android only
* `sub`						Android only, subscription enabled for contacts, in iOS always subscribed
* `pub`						Android only, auto publish, in iOS controlled by `monitoring`
* `updateAddressBook`
* `notification`				Android only, show notifications
* `notificationLocation`			Android only, show last reported location in notification, off in iOS
* `notificationGeocoder`			Android only, resolve location in notification to address, in iOS only resolved when in show details
* `cmd`						respond to remote commands
* `allowRemoteLocation`				iOS only, respond to remote location request even if monitoring mode is set to manual
* `extendedData`				iOS, add altitude, vertical accuracy, velocity and course over ground to published data. Android, include battery in location publishes
* `remoteConfiguration`				Android only, respond to remote configuration messages
* `usepolicy`					iOS only, use user defined security policy
* `allowinvalidcerts`				iOS only, allow self signed certificates in user defined security policy
* `validatedomainname`				iOS only, validate domain main during TLS handshake
* `validatecertificatechain`			iOS only, validate the whole certificate chain or just the server certificate
* `policymode`					iOS only, 
	* 0 = Do not used pinned certificates to validate servers.
	* 1 = Validate host certificates against public keys of pinned certificates.
	* 2 = Validate host certificates against pinned certificates.
* `servercer`					iOS only, blank separated list of certificate file names in DER format, or empty
* `clientpkcs`					iOS only, name of the client pkcs12 file,or empty 
* `passphrase`					iOS only, passphrase for client pkcs12 file 
* `beaconMode`					Android only. `0` = normal Bluetooth mode that enables scanning when regions with UUID are defined, `1` = Same as 0 but uses legacy beacon scanning instead of Android 6 scanning. Has no effect on devices not running Android < 6.x, `2` = hard disable any Bluetooth functions even if regions with UUID are defined
* `ws`						Android only, connect using WebSocket protocol  


#### Notes

* Imagine you are operating an OwnTracks backend for e.g. your family, and you want to avoid your family members accidentally switching modes or modifying settings locally on the device. You can export their configuration, add a `locked: true` to it and re-import it onto the iOS device. The settings will then be visible, but cannot be changed.


## `_type=beacon`

These messages are published when beacon ranging (iOS only) is enabled. Be advised that beacon ranging publishes a lot of messages and has a strong impact on battery life.

```json
{
        "_type":"beacon",
        "uuid":"CA271EAE-5FA8-4E80-8F08-2A302A95A959",
        "major":1,
        "minor":1,
        "tst": 1399028969,
        "acc":n,
        "rssi":n,
        "prox":n,
}
```

* `acc`	The accuracy of the proximity value, measured in meters from the beacon.
* `rssi`	The received signal strength of the beacon, measured in decibels. The theoretical relationship between RSSI and distance is something like this: `RSSI[dbm] = −(10n log10(d) − A)` where d is the distance and A is the offset which is the measured RSSI 1 meter point away from the Bluetooth Low Energy device.
* `prox`	The relative distance to the beacon where n indicates
    * 0 = the proximity of the beacon could not be determined.
    * 1 = the beacon is in the user’s immediate vicinity.
    * 2 = the beacon is relatively close to the user.
    * 3 = the beacon is far away."

## `_type=cmd`

```json
{"_type":"cmd","action":"reportLocation"}
{"_type":"cmd","action":"reportSteps"}
{"_type":"cmd","action":"dump"}
{"_type":"cmd","action":"waypoints"}
{"_type":"cmd","action":"setWaypoints","waypoints":[...]}
{"_type":"cmd","action":"action","content":"Backend maintenance scheduled for tonight\n\nhttp://support.owntracks.org"}
{"_type":"cmd","action":"action","content":"<a href='http://support.owntracks.org'>Backend Maintenance tonight</a>"}
{"_type":"cmd","action":"action","url":"http://support.owntracks.org"}
```
* `action`      action to be performed by the device
    * `reportSteps` reports steps walked on iPhone 5s devices. <br>
      You may add "from":_timestamp_ and/or "to":_timestamp_" 
      which defaults to current time, from defaults to current date 00:00 am
      (see [Pedometer](../features/pedometer.md)).
    * `reportLocation` triggers the publish of the current location
    * `dump` triggers the publish of a configuration message
    * `setWaypoints` configures new waypoints. (iOS only; [details](../features/remoteconfig.md) )
    * `waypoints` triggers a publish of all waypoints configured on the device. These are published in `.otrw` format to the `../waypoints` topic.
    * `action` inserts an additional 'Featured Content' tab in the UI.
	- If `"url"` is specified, the URL is opened in a full screen web view within the app. 
	- If `"url"` is not specified the text in `"content"` is displayed. Links embedded in the text are operational.  If `"content"` is HTML, it is rendered.
	- Send `"action":"action"` without `"content"` and without `"url"` to remove the extra tab.
	- If `"notify"` is in the payload, it is a string which will be used as notification in the UI
	- If the optional `"extern"` boolean is `true` a click on the notification will launch an external Web browser; `false` (the default) opens the `url` in a Webview within the OwnTracks app.

## `_type=steps`

```json
{"_type":"steps","tst":1400455130,"steps":1234,"from":1400455130,"to":1400458000}
```
* `tst`         timestamp of the request
* `steps`	the number of steps walked with the device in the specfied time period or
                -1 if device does not support step counting or specified time period
                is invalid
* `from`        effective start of time period
* `to`          effective end of time period

## `_type=card`

As described in [Card](../features/card.md), apps read retained messages of `_type=card` on `owntracks/+/+/info` to find the _name_ and avatar (_face_) of a user.

```json
{
  "_type": "card",
  "name": "Jane Jolie"
  "face": "iV1CFEVkMhmCIKBUKh3 ... ghAAAAABJRU5ErkJggg==",
}
```

The `name` element contains a name which is displayed by the apps to identify a user, and `face` contains the base64-encoded PNG image (40x40 px) which is displayed instead of a [TID](../features/tid.md).

## `_type=waypoints`

The app can export a list of configured waypoints (separate from the configuration) in order to share these, for example. Similarly, the app can import a list of waypoints, merging them into the current list, from a JSON file with `.otrw` extension. The list of waypoints looks like this.

```json
{
    "_type": "waypoints",
    "_creator": "OTwpDraw",
    "waypoints": [
        {
            "_type": "waypoint",
            "tst": 1433598071,
            "lat": 47.580231298,
            "lon": 9.525146484,
            "rad": 25186,
            "desc": "I went swimming here"
        },
        {
            "_type": "waypoint",
            "tst": 1430398091,
            "lat": 46.517295754,
            "lon": 9.871215820,
            "rad": 500,
            "desc": "Cheese fondue"
        }
    ]
}
```

Note that `_type=waypoints` is *plural*.

## `_type=encrypted`

This payload type contains a single `data` element with the original JSON object `_type` (e.g. `location`, `beacon`, etc.) [encrypted payload](../features/encrypt.md) in it.

```json
{
  "_type": "encrypted",
  "data": "1Vu7Owp ... W4lMnh28FB+el22GsCrlnggvEcp4H8GR9iKJdi1qfwkejYpzrQ+491Mwunjg="
}
```
