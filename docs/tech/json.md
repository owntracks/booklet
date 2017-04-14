## Types 
OwnTracks publishes its message payloads in [JSON](http://www.json.org) format. The different payload types are identified by a `_type` element. Depending on the app platform, different payload types are supported. 

| `_type`                                   |  iOS   | Android |
| ----------------------------------------- | :----: | :-----: |
| `beacon`                                  |   Y    |   N     |
| `card`                                    |   Y    |   Y     |
| `cmd`                                     |   Y    |   Y     |
| `configuration`                           |   Y    |   Y     |
| `encrypted`                               |   Y    |   Y     |
| `location`                                |   Y    |   Y     |
| `lwt`                                     |   Y    |   Y     |
| `steps`                                   |   Y    |   N     |
| `transition`                              |   Y    |   Y     |
| `waypoint`                                |   Y    |   Y     |
| `waypoints`                               |   Y    |   Y     |


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

In HTTP mode the apps POST their data to a single endpoint you configure. 

## `_type=location`

This location object describes the location of the device that reported it. 

```json
{
    "_type" : "location",        
    elements....
}
```

* `acc` Accuracy of the reported location in meters without unit _(iOS/integer/meters/optional)_
* `alt` Altitude measured above sea level _(iOS/integer/meters/optional)_
* `batt` Device battery level _(iOS,Android/integer/percent/optional)_
* `cog` Course over ground _(iOS/integer/degree/optional)_
* `lat` latitude _(iOS/float/meters/required)_
* `lon` longitude _(iOS/float/meters/required)_
* `rad` radius around the region when entering/leaving _(iOS/integer/meters/optional)_
* `t` trigger for the location report _(iOS/string/optional)_ 
    * `p` ping issued randombly by background task _(iOS)_
    * `c` circular region enter/leave event _(iOS/Android)_
    * `b` beacon region enter/leave event _(iOS/Android)_
    * `r` response to a reportLocation cmd message _(iOS/Android)_
    * `u` manual publish requested by the user _(iOS/Android)_
    * `t` timer based publish in move move _(iOS)_
    * `a` automatic location update _(iOS/Android)_
    
* `tid` Tracker ID used to display the initials of a user _(iOS,Android/string/optional)_
* `tst` UNIX [epoch timestamp](http://en.wikipedia.org/wiki/Unix_time) of the location fix _(iOS,Android/integer/epoch/required)_
* `vac` vertical accuracy of the `alt` element _(iOS/integer/meters/optional)_
* `vel` velocity _(iOS/integer/kmh/optional)_ 
* `p` barometric pressure _(iOS/integer/kPa/optional/extended data)_
* `conn` Internet connectivity status (route to host) when the message is created _(iOS,Android/string/optional/extended data)_ 
    * `w` phone is connected to a WiFi connection _(iOS, Android)_
    * `o` phone is offline _(iOS, Android)_
    * `m` mobile data _(iOS, Android)_

#### Notes
* The `tst` in a ping is a [current timestamp](https://github.com/owntracks/ios/issues/197), so that it doesn't look like a duplicate.
* The `tid` dfaults to the last two characters of the topic 
* A missing `t` element also indicates an automatic location update
* A publish with of `"_type": "location"` with a `"b"` trigger is sent when an iOS device enters or leaves a beacon in addition to a `"_type": "transition"`: if somebody leaves and enters his home without having left the radius of detection for significant changes, a subscriber to his main topic would otherwise not get notified of any location change although beacon or circular region enter and leave transitions were generated.
* The `acc`, `alt`, `cog`, `vac`, `vel` elements are only added if they are not zero
* Elements marked with _extended data_ are only added if `extendedData=true` is configured




### Greenwich

The OwnTracks edition of the [Choral Greenwich](http://www.choral.it/greenwich&menu2=795) device reports the following additional elements in a `_type=location` message:

```json
{
    "_type": "location",
    elements
}
```

* `alt` Altitude measured above sea level _(integer/meters/optional)_
* `batt` Device battery level _(integer/percent/optional)_
* `cog` Course over ground _(integer/degree/optional)_
* `dist` Distance travelled since the last location report _(integer/meters/optional)_
* `trip` Distance travelled since the last reboot _(integer/meters/optional)_
* `vel` velocity _(integer/kmh/optional)_ 
* `t` trigger for the location report _(string/optional)_ 
  - `f` First publish after reboot
  - `m` Manually requested locations (e.g. by publishing to `/cmd`)
  - `t` Time for location published because device is moving.
  - `T` Time for location published because of time passed while device is stationary (`maxInterval`)
  - `k` Transitioning from _move_ to _stationary_ (park)
  - `v` Transitioning from _stationary_ to _move_ (mo-v-e)
  - `l` Last known position when device lost GPS fix
  - `L` Last known position before gracefull shutdown

#### Notes
* The device can be configured to produce or not produce fields marked as _optional_


## `_type=lwt`

A _last will and testament_ is published automatically by the MQTT broker when it loses contact with the app. This typically looks like this:

```json
{
    "_type":"lwt",
    elements
}
```
* `tst` UNIX [epoch timestamp](http://en.wikipedia.org/wiki/Unix_time) at which the app first connected _(iOS,Android/integer/epoch/required)_


## `_type=waypoint`

Waypoints denote specific geographical regions that you want to keep track of. You define a waypoint in the OwnTracks app, and OwnTracks publishes this waypoint if the waypoint is marked as `shared`. OwnTracks also monitors these waypoints and will publish `{_type: "transition", ...}` message when entering or leaving the region. A waypoint may also define a BLE [Beacon](../features/beacons.md) instead of a geographical region. 

```json
{
    "_type"  : "waypoint",
    elements
}
```
* `desc` Name of the waypoint that is included in the sent transition message _(iOS,Android,string/required)_
* `lat` Latitude  _(iOS,Android/float/meters/optional)_
* `lon` Longitude _(iOS,Android/float/meters/optional)_
* `rad` Radius around the latitude and longitude coordinates _(iOS,Android/integer/meters/optional)_  
* `tst` Timestamp of waypoint _creation to identify the waypoint. Copied into the `wtst` elemnt of the transition message _(iOS,Android/integer/epoch/required)_  
* `shared` Share status of the waypoint  _(iOS,Android/boolen/required)_  
* `tid` Tracker ID that is included in the sent transition message _(iOS/string/optional)_
* `uuid` UUID of the BLE Beacon _(iOS,Android/string/optional)_
* `major` Major number of the BLE Beacon _(iOS,Android/integer/optional)_
* `minor`  Minor number of the BLE Beacon_(iOS,Android/integer/optional)_

#### Notes
* In iOS version >= 9.1.0 and in Android version >= 0.6.0 the last three elements (uuid, major, and minor) are used to configure Beacon waypoints instead of encoding these values into the `desc` element.
* If `lat`, `lon` and `rad` elements are present, transition messages are sent when entering and leaving the geographical region 
* If `uuid`, `major`, `minor` elements are present, BLE becons with that specifications are monitored
* Beacons and Geographical regions can be defined together 
* Waypoint messages are published non-retained because the second waypoint would overwrite the first: a client would only get the last one which makes no sense. Your application will typically store waypoints to some kind of persistent storage.
* Shared Waypoint are sent to the broker and transition messages contain a `desc` and `event` element. Not shared ones are not sent to the broker and transition messages contain an `event` attribute only

## `_type=transition`

A transition message is sent, when entering or leaving a previously configured geographical region or BLE Beacon. In addition to the coordinates where the event fired, the message contains the timestamp of the waypoint creation as well as the event that triggered the transition message. If the corresponding waypoint is shared, it's description in `desc` is included too. MQTT transition messages are published non retained. 

```json
{
  "_type": "transition",
  elements
}
```
* `wtst` Timestamp of waypoint creation _(iOS,Android/integer/epoch/required)_  
* `lat` Latitude at which the event occured _(iOS,Android/float/meters/optional)_
* `lon` Longitue at which the event occured _(iOS,Android/float/meters/optional)_
* `tst` Timestamp at which the event occured _(iOS,Android/integer/epoch/required)_  
* `acc` Accuracy of the geographical coordinates _(iOS,Android/float/meters/required)_  
* `tid` Tracker ID of the waypoint _(iOS/string/none/optional)_  
* `event` Event that triggered the transition (iOS,Android/string/required)
  - `enter` The device entered the defined geographical region or BLE Beacon range _(iOS, Android)_
  - `leave` The device left the defined geographical region or BLE Beacon range _(iOS, Android)_
* `desc` Name of the waypoint _(iOS,Android/string/optional)_
* `t` Trigger of the event _(iOS,Android/string/optional)_
  - `c` Circular geographical region  _(iOS, Android)_
  - `b` BLE Beacon _(iOS, Android)_


## `_type=configuration`
The device configuration can be imported and exported as JSON. The exported configuration can contain an array of waypoints that are defined on the device. If enabled, apps also accept remote configuration messages.  

```json
{
    "_type": "configuration",
    elements

}
```
* `allowRemoteLocation` Respond to reportLocation cmd message	_(iOS,Android/boolean)_
* `allowinvalidcerts` Allow self signed certificates in user defined security policy _(iOS/boolean)_ 
* `auth` Use `username` and `password` for endpoint authentication _(iOS,Android/boolean)_ 
* `autostartOnBoot` Autostart the app on device boot	_(Android/boolean)_
* `beaconLayout` AltBeacon [parser layout](https://altbeacon.github.io/android-beacon-library/javadoc/org/altbeacon/beacon/BeaconParser.html) to understand different beacon advertisements other than the default iBeacons  _(Android/string)_

* `beaconMode` Backend mode for BLE beacon scanning (Android/Integer)
    - `0` Normal bluetooth scanning for regions with UUID 
    - `1` Legacy beacon scanning instead of Android 6 scanning. Has no effect on devices running Android < 6.x
    - `2` Hard disable any Bluetooth functions even if regions with UUID are defined
* `cleanSession` MQTT endpoint clean session _(iOS,Android/boolean)_
* `clientpkcs` Name of the client pkcs12 file _(iOS/string)_
* `cmd` Respond to cmd messages	_(iOS,Android/boolean)_
* `deviceId` MQTT client ID  _(iOS,Android/string)_
* `extendedData` Add extended data attributes to location messages _(iOS,Android/boolean)_
* `host` MQTT endpoint host _(iOS,Android/string)_
* `httpSchedulerConsiderStrategyDirect` Directly send messages in HTTP mode and bypass the default scheduler if a network connection exists _(Android/boolean)_
* `ignoreInaccurateLocations` Location accuracy below which reports are supressed	_(iOS,Android/integer/days)_
* `ignoreStaleLocations` Number of days after which location updates are assumed stale _(iOS,Android/integer/days)_	
* `keepalive` MQTT endpoint keepalive _(iOS,Android/integer/seconds)_
* `locatorDisplacement` maximum distance between location source updates _(Android/integer/meters)_
* `locatorAccuracyBackground` Location source power mode when the app is in the background _(Android/integer)_
    - `0` High power likely based on GPS location
    - `1` Balanced power based on multiple location sources 
    - `2` Low power based on cell tower location
    - `3` No power  based on locations requested by other apps 
* `locatorAccuracyForeground` Location source power mode when the app is in the foreground _(Android/integer)_
    - `0` High power likely based on GPS location
    - `1` Balanced power based on multiple location sources 
    - `2` Low power based on cell tower location
    - `3` No power  based on locations requested by other apps 
* `locatorInterval`	maximum interval between location source updates _(Android/integer/seconds)_
* `locked` Locks settings screen on device for editing _(iOS/boolean)_
* `mode`	Endpoint protocol mode _(iOS,Android/integer)_
    - `0` Private  MQTT _(iOS, Android)_
    - `2` Public  MQTT _(iOS, Android)_
    - `0` Private  HTPP _(iOS, Android)_
    - `4` Watson IOT Quickstart _(iOS)_
    - `4` Watson IOT Registered _(iOS)_
* `monitoring` Location reporting mode _(iOS,integer)_		
    - `-1` Quiet
    - `0` Manual
    - `1` Significant 
    - `2` Move 
* `mqttProtocolLevel` MQTT broker protocol level _(iOS,Android/integer)_
    - `3` MQTT 3
    - `4` MQTT 3.1.1 

* `notification` Show ongoing notification. Required to keep running in the background	_(Android/boolean)_	
* `notificationGeocoder` Resolve last reported location in ongoing notification to an address  _(Android/boolean)_	
* `notificationLocation`	Show last reported location in ongoing notification _(Android/boolean)_
* `passphrase` Passphrase of the client pkcs12 file _(iOS/string)_
* `password` Endpoint password _(iOS,Android/string)_
* `policymode`	User defined securiy policy mode _(iOS/integer)_
    - `0` Do not used pinned certificates to validate servers
    - `1` Validate host certificates against public keys of pinned certificates
    - `2` Validate host certificates against pinned certificates
* `port` MQTT endpoint port _(iOS,Android/integer)_
* `positions`	Number of locatoins to keep and display _(iOS/integer)_	
* `pub` Automatic reporting	_(Android/boolean)_				
* `pubTopicBase`  MQTT topic base to which the app publishes   _(iOS,Android/string)_	
* `pubRetain`
* `pubQos`
* `ranging`	_(iOS/boolean)_
* `remoteConfiguration` Allow remote configuration by sending a setConfiguration cmd message _(Android/boolean)_
* `servercer` Blank separated list of certificate file names in DER format _(iOS/string)_
* `sub` MQTT subscription _(Android/boolean)_
* `subTopic` MQTT topic to which the app subscribes   _(iOS,Android/string)_
* `subQos`
* `tid` Two digit Tracker ID used to display short name and default face of a user _(iOS,Android/string)_
* `tls` MQTT endpoint TLS connection _(Android/boolean)_
* `tlsClientCrtPassword` Passphrase of the client pkcs12 file _(Android/string)_
* `url`	HTTP endpoint URL	 to which messages are POSTed _(iOS,Android/string)_
* `updateAddressBook` _(iOS/boolean)_
* `usepolicy` Use user defined security policy _(iOS/boolean)_
* `username` Endpoint username _(iOS,Android/string)_
* `validatedomainname` Validate domain name during TLS handshake _(iOS/boolean)_
* `validatecertificatechain` Validate the whole certificate chain or just the server certificate _(iOS/boolean)_
* `watsonAuthToken` Auth token Watson IOT Registered mode _(iOS/string)_
* `watsonDeviceType` Device type in Watson IOT Registered mode _(iOS/string)_
* `watsonDeviceId`	Device ID in Watson IOT Registered mode _(iOS/string)_
* `watsonOrganization` Organization in Watson IOT Registered mode _(iOS/string)_
* `willRetain`
* `willTopic` 
* `willQos`
* `waypoints` Array of waypoint messages _(iOS,Android/array)_
* `quickstartId` Device ID in Watson IOT Quickstart mode _(iOS/string)_

#### Notes
* When importing a configuration message, all contained values are imported for the currently active mode. If the message also contains a `mode` element, the mode is changed first and all remaining elements are imported for the new mode. 

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
{"_type":"cmd","action":"setConfiguration","configuration":{"_type":"configuration",...}
{"_type":"cmd","action":"setConfiguration","payload":{"_type":"configuration",...}
{"_type":"cmd","action":"setWaypoints","waypoints":{"_type":"waypoints","waypoints":[...]}
{"_type":"cmd","action":"setWaypoints","payload":{"_type":"waypoints","waypoints":[...]}
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
    * `setWaypoints` configures new waypoints. ([details](../features/remoteconfig.md) ). The variant with `"payload"` only exists for backward compatibility 
    * `setConfiguration` configures the app. [details](../features/remoteconfig.md). The variant with `"payload"` only exists for backward compatibility 
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
