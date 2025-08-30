## Types

OwnTracks uses [JSON](http://www.json.org) format for its message payloads. The different payload types are identified by a mandatory `_type` element. Depending on the app platform, different payload types are supported.

| `_type`                                   |  iOS   | Android |
| ----------------------------------------- | :----: | :-----: |
| `beacon`                                  |   Y    |   N     |
| `card`                                    |   Y    |   Y     |
| `cmd`                                     |   Y    |   Y     |
| `configuration`                           |   Y    |   Y     |
| `encrypted`                               |   Y    |   Y     |
| `location`                                |   Y    |   Y     |
| `lwt`                                     |   Y    |   Y     |
| `request`                                 |   Y    |   N     |
| `status`                                  |   Y    |   Y     |
| `steps`                                   |   Y    |   N     |
| `transition`                              |   Y    |   Y     |
| `waypoint`                                |   Y    |   Y     |
| `waypoints`                               |   Y    |   Y     |


## Topics

In MQTT mode the apps publish to:

- `owntracks/user/device` with `_type=location` for location updates, and with `_type=lwt`
- `owntracks/user/device/cmd` with `_type=cmd` for remote commands
- `owntracks/user/device/event` with `_type=transition` for enter/leave events
- `owntracks/user/device/step` to report step counter
- `owntracks/user/device/beacon` for beacon ranging
- `owntracks/user/device/dump` for config dumps
- `owntracks/user/device/status` for status messages
- `owntracks/user/device/waypoint` when a geofence is created on the device
- `owntracks/user/device/waypoints` (plural) when exporting a list of configured waypoints from device to backend

In MQTT mode apps subscribe to:

- `owntracks/user/device/cmd` if remote commands are enabled
- `owntracks/+/+` for seeing other user's locations, depending on broker ACL
- `owntracks/+/+/event` for transition messages (`enter`/`leave`)
- `owntracks/+/+/info` for obtaining [cards](../features/card.md).

In HTTP mode the apps POST their data to a single endpoint you configure.

## `_type=location`

This location object describes the location of the device that reported it.

```json
{
    "_type" : "location",
    elements
}
```

* `acc` Accuracy of the reported location in meters without unit _(iOS,Android/integer/meters/optional)_
* `alt` Altitude measured above sea level _(iOS,Android/integer/meters/optional)_
* `batt` Device battery level _(iOS,Android/integer/percent/optional)_
* `bs` Battery Status 0=unknown, 1=unplugged, 2=charging, 3=full  _(iOS, Android)_
* `cog` Course over ground _(iOS/integer/degree/optional)_
* `lat` latitude _(iOS,Android/float/degree/required)_
* `lon` longitude _(iOS,Android/float/degree/required)_
* `rad` radius around the region when entering/leaving _(iOS/integer/meters/optional)_
* `t` trigger for the location report _(iOS,Android/string/optional)_
    * `p` ping issued randomly by background task _(iOS,Android)_
    * `c` circular region enter/leave event _(iOS,Android)_
    * `C` circular region enter/leave event for [+follow regions](https://owntracks.org/booklet/features/ios/#region-monitoring-with-follow) _(iOS)_
    * `b` beacon region enter/leave event _(iOS)_
    * `r` response to a reportLocation cmd message _(iOS,Android)_
    * `u` manual publish requested by the user _(iOS,Android)_
    * `t` timer based publish in move move _(iOS)_
    * `v` updated by `Settings/Privacy/Locations Services/System Services/Frequent Locations` monitoring _(iOS)_
* `tid` Tracker ID used to display the initials of a user _(iOS,Android/string/optional)_ required for `http` mode
* `tst` UNIX [epoch timestamp](http://en.wikipedia.org/wiki/Unix_time) in seconds of the location fix _(iOS,Android/integer/epoch/required)_
* `vac` vertical accuracy of the `alt` element _(iOS/integer/meters/optional)_
* `vel` velocity _(iOS,Android/integer/kmh/optional)_
* `p` barometric pressure _(iOS/float/kPa/optional/extended data)_
* `poi` point of interest name _(iOS/string/optional)_
* `image` Base64 encoded image associated with the poi _(iOS/string/optional)_
* `imagename` Name of the image associated with the poi _(iOS/string/optional)_
* `conn` Internet connectivity status (route to host) when the message is created _(iOS,Android/string/optional/extended data)_
    * `w` phone is connected to a WiFi connection _(iOS,Android)_
    * `o` phone is offline _(iOS,Android)_
    * `m` mobile data _(iOS,Android)_
* `tag` name of the tag _(iOS/string/optional)_
* `topic` (only in HTTP payloads) contains the original publish topic (e.g. `owntracks/jane/phone`). _(iOS,Android >= 2.4,string)_
* `inregions` contains a list of regions the device is currently in (e.g. `["Home","Garage"]`). Might be empty. _(iOS,Android/list of strings/optional)_
* `inrids` contains a list of region IDs the device is currently in (e.g. `["6da9cf","3defa7"]`). Might be empty. _(iOS,Android/list of strings/optional)_
* `SSID`, if available, is the unique name of the WLAN. _(iOS,string/optional)_
* `BSSID`, if available, identifies the access point. _(iOS,string/optional)_
* `created_at` identifies the time at which the message is constructed (if it differs from `tst`, which is the timestamp of the GPS fix) _(iOS,Android/integer/epoch/optional)_
* `m` identifies the monitoring mode at which the message is constructed (_significant_=`1`, _move_=`2`)  _(iOS/integer/optional)_
* `_id` random identifier to be used by consumers to correlate & distinguish send/return messages _(Android/string)_
* `motionactivities` contains a list of motion states detected by iOS' motion manager (a combination of `stationary`, `walking`, `running`, `automotive`, `cycling`, and/or `unknown`, e.g. `["cycling"]`). _(iOS/list of strings/optional)_

#### Notes
* The `tst` in a ping is a [current timestamp](https://github.com/owntracks/ios/issues/197), so that it doesn't look like a duplicate.
* The `tid` defaults to the last two characters of the topic
* A missing `t` element also indicates an automatic location update
* A publish of `"_type": "location"` with a `"b"` trigger is sent when an iOS device enters or leaves a beacon in addition to a `"_type": "transition"`: if somebody leaves and enters his home without having left the radius of detection for significant changes, a subscriber to his main topic would otherwise not get notified of any location change although beacon or circular region enter and leave transitions were generated.
* The `acc`, `alt`, `cog`, `vac`, `vel` elements are only added if they are not zero
* Some Android devices always return 0 for `alt` or `vel`
* Elements marked with _extended data_ are only added if `extendedData=true` is configured
* Android devices using a VPN might not report either of SSID/BSSID which might be expected behaviour. [#1888](https://github.com/owntracks/android/issues/1888)


### Queclink

OwnTracks works with a selection of trackers by [Queclink](http://www.queclink.com), for which the protocol conversion is done via [qtripp](https://github.com/jpmens/qtripp).
Queclink devices report the following additional elements in a `_type=location` message:

```json
{
    "_type": "location",
    elements
}
```

* `t` trigger for the location report _(string/optional)_
    - `o` Corner
    - `M` Mileage
    - `!` Tow or fake tow or sensor without ignition
    - `a` motionless (aka park)
    - `i` ignition on
    - `I` ignition off
    - `e` external power off
    - `E` external power ON
    - `1` Devices powered up
    - `2` Battery stop charging
    - `3` Battery start charging
    - `9` Battery power low
    - `h` harsh behavior
    - `s` speed alarm
* `odometer` total distance of the device _(float/kilometers/optional)_
* `hmc` total hours of operation _(float/seconds/optional)_
* `ubatt` voltage of the battery _(float/volts/optional)_
* `uext` voltage of the external power source _(float/volts/optional)_
* `vin` vehicle identification number _(string/optional)_
* `imei` identification number _(string/optional)_
* `name` vehicle name _(string/optional)_
* `don` is duration since ignition on _(float/seconds/optional)_
* `doff` is duration since ignition off _(float/seconds/optional)_
* `aiv` is analog input voltage _(float/volts/optional)_
* `rpm` is engine rounds per minute _(float/rounds per minute/optional)_
* `fcon` is fuel consumption _(float/L per 100km/optional)_
* `flvl` is fuel level _(float/percent/optional)_
* `anum` is number of analog inputs _(integer/optional)_
* `adid-xx` is id of analog input number xx _(string/optional)_
* `adty-xx` is type of analog input number xx _(string/optional)_
* `adda-xx` is data of analog input number xx _(string/optional)_
* `temp_c-xx` is temperature of analog input number xx _(float/celsius/optional)_
* `can` is can data _(string/optional)_
* `din1` is status of digital input 1 _(boolean/optional)_
* `din2` is status of digital input 2 _(boolean/optional)_
* `dout1` is status of digital output 1 _(boolean/optional)_
* `dout2` is status of digital output 2 _(boolean/optional)_
* `ign` is status of ignition _(boolean/optional)_
* `motion` is motion status _(boolean/optional)_
* `tow` is status of tow sensor _(boolean/optional)_
* `fake` is status of fake tow sensor _(boolean/optional)_
* `sens` is status of motion sensor _(boolean/optional)_
* `sent` is epoch when message was sent _(integer/epoch/optional)_
* `mcc` is mobile country code _(integer/optional)_
* `mnc` is mobile network code _(integer/optional)_
* `lac` is location area code _(string/optional)_
* `cid` is cell id _(string/optional)_
* `nmds` is non movement detection status _(boolean/optional)_
* `rit` queclink record id and type _(integer/optional)_
* `rty` queclink record type _(integer/optional)_
* `rid` queclink record id _(integer/optional)_
* `mst` queclink motion state _(integer/optional)_
* `count` is counter of message _(string/optional)_
* `raw_line` raw data _(string/optional)_
* `counter` number of ignored positions _(integer/optional)_
* `ignored` indicates counter positions have been ignored _(boolean/optional)_


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

Waypoints / regions denote specific geographical regions that you want to keep track of. You define a region in the OwnTracks app, and OwnTracks publishes this waypoint to the topic branch `../waypoint` (singular). OwnTracks also monitors these waypoints and will publish `{_type: "transition", ...}` message when entering or leaving the region. A waypoint may also define a BLE [Beacon](../features/beacons.md) instead of a geographical region.

```json
{
    "_type"  : "waypoint",
    elements
}
```
* `desc` Name of the waypoint that is included in the sent transition message, copied into the `location` message `inregions` array when a current position is within a region. _(iOS,Android,string/required)_
* `lat` Latitude  _(iOS,Android/float/degree/optional)_
* `lon` Longitude _(iOS,Android/float/degree/optional)_
* `rad` Radius around the latitude and longitude coordinates _(iOS,Android/integer/meters/optional)_
* `tst` Timestamp of creation of region, copied into the `wtst` element of the transition message _(iOS,Android/integer/epoch/required)_
* `uuid` UUID of the BLE Beacon _(iOS/string/optional)_
* `major` Major number of the BLE Beacon _(iOS/integer/optional)_
* `minor`  Minor number of the BLE Beacon_(iOS/integer/optional)_
* `rid`  region ID, created automatically, copied into the `location` payload `inrids` array (iOS/string)_

#### Notes
* In iOS version >= 9.1.0 the last three elements (uuid, major, and minor) are used to configure Beacon waypoints instead of encoding these values into the `desc` element.
* If `lat`, `lon` and `rad` elements are present, transition messages are sent when entering and leaving the geographical region
* If `uuid`, `major`, `minor` elements are present, BLE becons with that specifications are monitored
* Beacons and Geographical regions can be defined together
* Waypoint messages are published non-retained because the second waypoint would overwrite the first: a client would only get the last one which makes no sense. Your application will typically store waypoints to some kind of persistent storage.
* Waypoints are sent to the broker and transition messages contain a `desc` and `event` element.

## `_type=transition`

A transition message is sent, when entering or leaving a previously configured geographical region or BLE Beacon. In addition to the coordinates where the event fired, the message contains the timestamp of the waypoint creation as well as the event that triggered the transition message with its description. MQTT transition messages are published non retained.

```json
{
    "_type": "transition",
    elements
}
```
* `wtst` Timestamp of waypoint creation _(iOS,Android/integer/epoch/required)_
* `lat` Latitude at which the event occured _(iOS,Android/float/degree/optional)_
* `lon` Longitue at which the event occured _(iOS,Android/float/degree/optional)_
* `tst` Timestamp at which the event occured _(iOS,Android/integer/epoch/required)_
* `acc` Accuracy of the geographical coordinates _(iOS,Android/int/meters/required)_
* `tid` Tracker ID of the _(iOS/string/none/optional)_ required in http mode.
* `event` Event that triggered the transition (iOS,Android/string/required)
	  - `enter` The device entered the defined geographical region or BLE Beacon range _(iOS)_
	  - `leave` The device left the defined geographical region or BLE Beacon range _(iOS)_
* `desc` Name of the waypoint _(iOS,Android/string/optional)_
* `t` Trigger of the event _(iOS,Android/string/optional)_
    - `c` Circular geographical region  _(iOS, Android)_
    - `b` BLE Beacon _(iOS)_
    - `l` Location update _(Android)_
* `rid` Region ID _(iOS/Android, after January 2021)_


## `_type=configuration`
The device configuration can be imported and exported as JSON. The exported configuration can contain an array of waypoints that are defined on the device. If enabled, apps also accept remote configuration messages.

```json
{
    "_type": "configuration",
    elements
}
```
* `adapt` time in minutes of non-movement before switching from move to significant mode. 0 (zero) means disabled. Defaults to 0 (zero) _(iOS/integer/minutes/optional)_
* `allowRemoteLocation` Respond to reportLocation cmd message _(iOS/boolean)_
* `allowinvalidcerts` disable TLS certificate checks **insecure**  _(iOS/boolean)_
* `auth` Use `username` and `password` for endpoint authentication _(iOS,Android/boolean)_
* `autostartOnBoot` Autostart the app on device boot _(Android/boolean)_
* `cleanSession` MQTT endpoint clean session _(iOS,Android/boolean)_
* `clientId` client id to use for MQTT connect. Defaults to "*user* *deviceId*" _(iOS,Android/string)_
* `clientpkcs` Name of the client pkcs12 file _(iOS/string)_
* `cmd` Respond to cmd messages _(iOS,Android/boolean)_
* `connectionTimeoutSeconds` (default 30) TCP timeout for establishing a connection to the MQTT / HTTP broker, _(Android/int)_
* `days` Number of days to keep locations stored locally. 0 means no local keeping of locations. A negative number indicates to use the `positions` value. Defaults to -1 for backward compatibility. _(iOS/integer/days)_
* `debugLog` (default false) whether or not debug logs should be shown in the log viewer / exporter activity _(Android/bool)_
* `deviceId` id of the device used for `pubTopicBase` and `clientId` construction. Defaults to the os name of the device  _(iOS,Android/string)_
* `downgrade` battery level below which to downgrade monitoring from move mode _(iOS/integer/percent/optional)_
* `encryptionKey` the secret key used for [payload encryption](../features/encrypt.md) _(iOS_,_Android/string)_
* `extendedData` Add extended data attributes to location messages _(iOS,Android/boolean)_
* `host` MQTT endpoint host _(iOS,Android/string)_
* `httpHeaders` extra HTTP headers:field names and field content are separated by a colon (:), multiple fields by a backslash-n (\n) \<field-name\>:\<field-content\>\n\<field-name\>:\<field-content\>... _(iOS only/string)_
* `ignoreInaccurateLocations` Location accuracy below which reports are supressed. 0 means no locations are suppressed. _(iOS,Android/integer/meters)_
* `ignoreStaleLocations` Number of days after which location updates are assumed stale. Locations sent by friends older than the number of days specified here will not be shown on map or in friends list. Defaults to 0, which means stale locations are not filtered. _(iOS,Android/integer/days)_
* `keepalive` MQTT endpoint keepalive _(iOS,Android/integer/seconds)_
* `locatorDisplacement` maximum distance between location source updates _(iOS,Android/integer/meters)_
* `locatorInterval` maximum interval between location source updates _(iOS,Android/integer/seconds)_
* `locatorPriority` source/power setting for location updates _(Android/integer)_
    - `0` NO_POWER / best accuracy possible with zero additional power consumption _(Android)_
    - `1` LOW_POWER / city level accuracy _(Android)_
    - `2` BALANCED_POWER / block level accuracy based on Wifi/Cell _(Android)_
    - `3` HIGH_POWER / most accurate accuracy based on GPS _(Android)_
* `locked` Locks settings screen on device for editing _(iOS/boolean)_
* `maxHistory` Number of notifications to store historically. Zero (0) means no notifications are stored and history tab is hidden. Defaults to zero. _(iOS/integer)_
* `mode` Endpoint protocol mode _(iOS,Android/integer)_
    - `0` MQTT _(iOS, Android)_
    - `3` HTTP _(iOS, Android)_
* `monitoring` Location reporting mode _(iOS,Android/integer)_
    - `-1` Quiet
    - `0` Manual
    - `1` Significant
    - `2` Move
* `mqttProtocolLevel` MQTT broker protocol level _(iOS,Android/integer)_
    - `3` MQTT 3 (default)
    - `4` MQTT 3.1.1
    - `5` MQTT 5 (iOS only)
* `notificationLocation` Show last reported location in ongoing notification _(Android/boolean)_
* `opencageApiKey` API key for alternate Geocoding provider. See [OpenCage](../other/opencage.md) for details. _(Android/string)_
* `osmTemplate` URL template for alternate tile provider. Defaults to `https://tile.openstreetmap.org/{z}/{x}/{y}.png`. _(iOS/string)_
* `osmCopyright` Attribution text shown with OSM map. Defaults to `(c) OpenStreetMap contributors`. _(iOS/string)_
* `passphrase` Passphrase of the client pkcs12 file _(iOS/string)_
* `password` Endpoint password _(iOS,Android/string)_
* `pegLocatorFastestIntervalToInterval` (default false) - if true, requests that that the device provide locations no faster than the specified interval. Location providers often use the requested interval as a "at least every" setting, and may return locations more frequencly. Some people wanted the behaviour where it also meant "no more frequently than", so this setting lets them specify this _(Android/bool)_
* `ping` Interval in which location messages of with `t`:`p` are reported _(Android/integer)_
* `port` MQTT endpoint port _(iOS,Android/integer)_
* `positions` Number of locations to keep for friends and own device and display _(iOS/integer)_
* `pubTopicBase` MQTT topic base to which the app publishes; `%u` is replaced by the user name, `%d` by device   _(iOS,Android/string)_
* `pubRetain` MQTT retain flag for reported messages _(iOS,Android/boolean)_
* `pubQos` MQTT QoS level for reported messages _(iOS,Android/integer)_
* `ranging` Beacon ranging _(iOS/boolean)_
* `remoteConfiguration` Allow remote configuration by sending a `setConfiguration` cmd message _(Android/boolean)_
* `sub` subscribe to `subTopic` via MQTT _(iOS,Android/boolean)_
* `subTopic` A whitespace separated list of MQTT topics to which the app subscribes if `sub` is true (defaults see [topics](#topics))   _(iOS,Android/string)_
* `subQos` _(iOS,Android/boolean)_
* `tid` Two digit Tracker ID used to display short name and default face of a user _(iOS,Android/string)_
* `tls` MQTT endpoint TLS connection _(iOS,Android/boolean)_
* `tlsClientCrtPassword` Passphrase of the client pkcs12 file _(Android/string)_
* `url` HTTP endpoint URL to which messages are POSTed _(iOS,Android/string)_
* `username` Endpoint username _(iOS,Android/string)_
* `ws` use MQTT over Websocket, default `false` _(iOS,Android/boolean)_
* `waypoints` Array of waypoint messages _(iOS,Android/array)_

#### Notes
* When importing a configuration message, all contained values are imported for the currently active mode. If the message also contains a `mode` element, the mode is changed first and all remaining elements are imported for the new mode.
* In MQTT mode the server will consider the client as dead if it the keepalive interval plus 50% passed without receiving any MQTT packet from the client (e.g. after 90 sec if `keepalive` was 60). Afterwards, an `lwt` message will be send.

## `_type=status`
The device status contains information about the settings on the device which are not configured in the app.

```json
{
    "_type": "status",
    "iOS": <iOS specific elements>,
    "android": <android specific elements>
    <other elements>
}
```
* iOS specific elements
	- `altimeterAuthorizationStatus` used for steps _(iOS/string)_ as defined in [CMAuthorizationStatus](https://developer.apple.com/documentation/coremotion/cmauthorizationstatus?language=objc)
	- `altimeterIsRelativeAltitudeAvailable` used for steps _(iOS/boolean)_ as defined in [isRelativeAltitudeAvailable](https://developer.apple.com/documentation/coremotion/cmaltimeter/isrelativealtitudeavailable()?language=objc)
	- `backgroundRefreshStatus` _(iOS/boolean)_ as defined in [UIBackgroundRefreshStatusAvailable](https://developer.apple.com/documentation/uikit/uibackgroundrefreshstatus/uibackgroundrefreshstatusavailable/)
	- `deviceIdentifierForVendor` used for default DeviceID _(iOS/string)_ as defined in [identifierForVendor](https://developer.apple.com/documentation/uikit/uidevice/1620059-identifierforvendor?language=objc)
	- `deviceModel` _(iOS/string)_ as defined in [model](https://developer.apple.com/documentation/uikit/uidevice/1620044-model?language=objc)
	- `deviceSystemName` _(iOS/string)_ as defined in [systemName](https://developer.apple.com/documentation/uikit/uidevice/1620054-systemname?language=objc)
	- `deviceSystemVersion` _(iOS/string)_ as defined in [systemVersion](https://developer.apple.com/documentation/uikit/uidevice/1620043-systemversion?language=objc)
	- `deviceUserInterfaceIdiom` _(iOS/string)_ as defined in [userInterfaceIdiom](https://developer.apple.com/documentation/uikit/uidevice/1620037-userinterfaceidiom?language=objc)
	- `locale` the current locale on the device _(iOS/string)_ as defined in [localeIdentifier](https://developer.apple.com/documentation/foundation/nslocale/1416263-localeidentifier?language=objc)
	- `localeUsesMetricSystem` _(iOS/boolean)_ as defined in [usesMetricSystem](https://developer.apple.com/documentation/foundation/nslocale/1643225-usesmetricsystem?language=objc)
	- `locationManagerAuthorizationStatus` used for all locations _(iOS/string)_ as defined in ["CLAuthorizationStatus"](https://developer.apple.com/documentation/corelocation/clauthorizationstatus?language=objc)
	- `motionActivityManagerAuthorizationStatus` used for motionactivities _(iOS/string)_ as defined in [motionActivityManagerAuthorizationStatus](https://developer.apple.com/documentation/coremotion/cmmotionactivitymanager/authorizationstatus()?language=objc)
	- `motionActivityManagerIsActivityAvailable` used for motionactivities _(iOS/boolean)_ as defined in [motionActivityManagerIsActivityAvailable](https://developer.apple.com/documentation/coremotion/cmmotionactivitymanager/isactivityavailable()?language=objc)
	- `pedometerIsDistanceAvailable` _(iOS/boolean)_ as defined in [isDistanceAvailable](https://developer.apple.com/documentation/coremotion/cmpedometer/isdistanceavailable()?language=objc)
	- `pedometerIsFloorCountingAvailable` _(iOS/boolean)_ as defined in [isFloorCountingAvailable](https://developer.apple.com/documentation/coremotion/cmpedometer/isfloorcountingavailable()?language=objc)
	- `pedometerIsStepCountingAvailable` _(iOS/boolean)_ as defined in [isStepCountingAvailable](https://developer.apple.com/documentation/coremotion/cmpedometer/isstepcountingavailable()?language=objc)
	- `version` version of the OwnTracks app _(iOS/string)_
* android specific elements
	- `hib` app can hibernate if not used _(Android/integer)_
	- `bo` app is configured with battery optimizations _(Android/integer)_
	- `loc` app location permissions _(Android/integer)_
	- `ps` phone power save mode _(Android/integer)_
	- `wifi` wifi is on/off _(Android/integer)_
* other elements
	- `_id` random identifier to be used by consumers to correlate & distinguish send/return messages _(Android/string)_


## `_type=beacon`

These messages are published when beacon ranging (iOS only) is enabled. Be advised that beacon ranging publishes a lot of messages and has a strong impact on battery life.

```json
{
    "_type":"beacon",
    elements
}
```
* `desc` name of the seen beacon _(iOS/String)_
* `uuid` UUID of the seen beacon _(iOS/String)_
* `major` Major number of the seen beacon _(iOS/integer/epoch)_
* `minor` Minor number of the seen beacon _(iOS/integer/epoch)_
* `tst` Timestamp at which the beacon was seen _(iOS/integer/epoch)_
* `acc` Accuracy of the proximity value _(iOS/integer/meters)_
* `rssi` Received signal strength of the beacon _(iOS/integer/decibel)_
* `prox`Relative distance to the beacon _(iOS/integer)_
    - `0` Proximity of the beacon could not be determined
    - `1` Beacon is in the immediate vicinity
    - `2` Beacon is relatively close to the user
    - `3` Beacon is far away

#### Notes
* The theoretical relationship between RSSI and distance is `RSSI[dbm] = −(10n log10(d) − A)` where d is the distance and A is the offset which is the measured RSSI one meter point away from the beacon.

## `_type=cmd`

```json
{"_type":"cmd", "action":"reportLocation"}
{"_type":"cmd", "action":"reportSteps"}
{"_type":"cmd", "action":"dump"}
{"_type":"cmd", "action":"status"}
{"_type":"cmd", "action":"waypoints"}
{"_type":"cmd", "action":"clearWaypoints"}
{"_type":"cmd", "action":"setConfiguration", "configuration":{"_type":"configuration",...}
{"_type":"cmd", "action":"setWaypoints", "waypoints":{"_type":"waypoints","waypoints":[...]}
```
* `action` action to be performed by the device _(iOS,Android/string)_
    * `dump` Triggers the publish of a `configuration` message _(iOS)_
    * `status` Triggers the publish of a `status` message to `../status` _(iOS)_
    * `reportSteps` Triggers the report of a `steps` messages_(iOS)_
        - `from` Timestamp _(iOS/epoch/optional)_
        - `to` Timestamp _(iOS/epoch/optional)_
    * `reportLocation` Triggers the publish of a `location` messages _(iOS,Android)_
      Don‘t expect device to be online. Send with QoS>0. Device will receive and repond when activated next time.
    * `clearWaypoints` deletes all waypoints/regions _(iOS)_
    * `setWaypoints` Imports (merge) and activates new waypoints _(iOS,Android)_
        - `waypoints` Array of `waypoint` messages to import _(iOS,Android/array/required)_
    * `setConfiguration` Imports and activates new configuration values _(iOS,Android)_
        - `configuration` Configuration message to import _(iOS,Android/required)_
    * `waypoints` Triggers publish of a `waypoints` message _(iOS,Android)_

#### Notes
* On iOS, the array of waypoints to the `setWaypoints` command allows updates / removal; the key of the waypoint is its name (`desc`). If you specify an invalid `lat` or `lon` (invalid means out of range number value e.g. `-1000000`, a string like `"foo"` will result in a `0` in app) the waypoint is deleted.
* On Android there's a primary key which isn't surfaced to the API, and `tst` is a uniquely-constrained value which effectively acts like a key.

## `_type=steps`


```json
{
    "_type":"steps",
    elements
}
```
* `tst` Timestamp of the request _(iOS/integer/epoch)_
* `steps` Steps walked with the device in the specfied time period  _(iOS/integer/steps)_
* `from` Effective start of time period _(iOS/integer/epoch)_
* `to`  Effective end of time period _(iOS/integer/epoch)_

#### Notes
* `steps` is -1 if device does not support step counting or specified time period is invalid


## `_type=card`
Apps read [Card](../features/card.md) to display a name and icon for a user.

```json
{
    "_type": "card",
    elements
}
```

* `tid` Tracker ID to associate the card with _(iOS,Android/string/required)_
* `name` Name to identify a user _(iOS,Android/string/optional)_
* `face` Base64 encoded PNG image that is displayed instead of the Tracker ID _(iOS,Android/string/optional)_


## `_type=waypoints`
The app can export a list of configured waypoints to the endpoint `../waypoints` (plural).

```json
{
    "_type": "waypoints",
    elements
}
```
* `_creator` Identification of what created the array. Ignored by the apps _(iOS,Android/string/optional)_
* `waypoints` Array of `waypoint` messages _(iOS,Android/array/required)_


## `_type=encrypted`
Apps can optionally [encrypt](../features/encrypt.md) outgoing messages with a shared symmetric key.  The encrypted message is contained in the `data`element.
For security reasons, the encryption key is not exported with configuration messages and cannot be imported.

```json
{
    "_type": "encrypted",
    elements
}
```
* `data` Encrypted and Base64 encoded original JSON message _(iOS,Android/string/required)_

## `_type=request`

Apps can request the creation of [tours](../features/tours.md) which elicit a `cmd` response from the Recorder. See [tours](../features/tours.md) for the details.

```json
{
  "_type": "request",
  "request": "tour",
  "tour": {
    ...
  }
}
```

