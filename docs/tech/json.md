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
- `owntracks/user/device/cmd` with `_type=cmd` for remote commands
- `owntracks/user/device/event` with `_type=transition` for enter/leave events
- `owntracks/user/device/step` to report step counter
- `owntracks/user/device/beacon` for beacon ranging
- `owntracks/user/device/dump` for config dumps

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

* `acc` Accuracy of the reported location in meters without unit _(iOS/integer/meters/optional)_
* `alt` Altitude measured above sea level _(iOS/integer/meters/optional)_
* `batt` Device battery level _(iOS,Android/integer/percent/optional)_
* `cog` Course over ground _(iOS/integer/degree/optional)_
* `lat` latitude _(iOS,Android/float/meters/required)_
* `lon` longitude _(iOS,Android/float/meters/required)_
* `rad` radius around the region when entering/leaving _(iOS/integer/meters/optional)_
* `t` trigger for the location report _(iOS,Android/string/optional)_
    * `p` ping issued randomly by background task _(iOS)_
    * `c` circular region enter/leave event _(iOS/Android)_
    * `b` beacon region enter/leave event _(iOS)_
    * `r` response to a reportLocation cmd message _(iOS/Android)_
    * `u` manual publish requested by the user _(iOS/Android)_
    * `t` timer based publish in move move _(iOS)_
    * `v` updated by `Settings/Privacy/Locations Services/System Services/Frequent Locations` monitoring _(iOS)_
* `tid` Tracker ID used to display the initials of a user _(iOS,Android/string/optional)_
* `tst` UNIX [epoch timestamp](http://en.wikipedia.org/wiki/Unix_time) in seconds of the location fix _(iOS,Android/integer/epoch/required)_
* `vac` vertical accuracy of the `alt` element _(iOS/integer/meters/optional)_
* `vel` velocity _(iOS/integer/kmh/optional)_
* `p` barometric pressure _(iOS/integer/kPa/optional/extended data)_
* `conn` Internet connectivity status (route to host) when the message is created _(iOS,Android/string/optional/extended data)_
    * `w` phone is connected to a WiFi connection _(iOS, Android)_
    * `o` phone is offline _(iOS, Android)_
    * `m` mobile data _(iOS, Android)_
* `cp` [copy mode](../features/copy.md) enabled; only if `true`, missing otherwise _(iOS)_
* `topic` (only in HTTP payloads) contains the original publish topic (e.g. `owntracks/jane/phone`). _(iOS)_

#### Notes
* The `tst` in a ping is a [current timestamp](https://github.com/owntracks/ios/issues/197), so that it doesn't look like a duplicate.
* The `tid` defaults to the last two characters of the topic
* A missing `t` element also indicates an automatic location update
* A publish of `"_type": "location"` with a `"b"` trigger is sent when an iOS device enters or leaves a beacon in addition to a `"_type": "transition"`: if somebody leaves and enters his home without having left the radius of detection for significant changes, a subscriber to his main topic would otherwise not get notified of any location change although beacon or circular region enter and leave transitions were generated.
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
* `tst` Timestamp of waypoint _creation to identify the waypoint. Copied into the `wtst` element of the transition message _(iOS,Android/integer/epoch/required)_
* `tid` Tracker ID that is included in the sent transition message _(iOS/string/optional)_
* `uuid` UUID of the BLE Beacon _(iOS/string/optional)_
* `major` Major number of the BLE Beacon _(iOS/integer/optional)_
* `minor`  Minor number of the BLE Beacon_(iOS/integer/optional)_

#### Notes
* In iOS version >= 9.1.0 the last three elements (uuid, major, and minor) are used to configure Beacon waypoints instead of encoding these values into the `desc` element.
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
	  - `enter` The device entered the defined geographical region or BLE Beacon range _(iOS)_
	  - `leave` The device left the defined geographical region or BLE Beacon range _(iOS)_
* `desc` Name of the waypoint _(iOS,Android/string/optional)_
* `t` Trigger of the event _(iOS,Android/string/optional)_
    - `c` Circular geographical region  _(iOS, Android)_
    - `b` BLE Beacon _(iOS)_


## `_type=configuration`
The device configuration can be imported and exported as JSON. The exported configuration can contain an array of waypoints that are defined on the device. If enabled, apps also accept remote configuration messages.

```json
{
    "_type": "configuration",
    elements
}
```
* `allowRemoteLocation` Respond to reportLocation cmd message _(iOS,Android/boolean)_
* `allowinvalidcerts` Allow self signed certificates in user defined security policy _(iOS/boolean)_
* `auth` Use `username` and `password` for endpoint authentication _(iOS,Android/boolean)_
* `autostartOnBoot` Autostart the app on device boot _(Android/boolean)_

* `beaconMode` Backend mode for BLE beacon scanning (Android/Integer/obsolete)
    - `0` Normal bluetooth scanning for regions with UUID
    - `1` Legacy beacon scanning instead of Android 6 scanning. Has no effect on devices running Android < 6.x
    - `2` Hard disable any Bluetooth functions even if regions with UUID are defined
* `cleanSession` MQTT endpoint clean session _(iOS,Android/boolean)_
* `clientId` client id to use for MQTT connect. Defaults to "*user* *deviceId*" _(iOS,Android/string)_
* `clientpkcs` Name of the client pkcs12 file _(iOS/string)_
* `cp` Copy mode _(iOS,Android/boolean)_
* `cmd` Respond to cmd messages _(iOS,Android/boolean)_
* `deviceId` id of the device used for `pubTopicBase` and `clientId` construction. Defaults to the os name of the device  _(iOS,Android/string)_
* `extendedData` Add extended data attributes to location messages _(iOS,Android/boolean)_
* `host` MQTT endpoint host _(iOS,Android/string)_
* `httpSchedulerConsiderStrategyDirect` Directly send messages in HTTP mode and bypass the default scheduler if a network connection exists _(Android/boolean)_
* `ignoreInaccurateLocations` Location accuracy below which reports are supressed _(iOS,Android/integer/days)_
* `ignoreStaleLocations` Number of days after which location updates are assumed stale _(iOS,Android/integer/days)_
* `keepalive` MQTT endpoint keepalive _(iOS,Android/integer/seconds)_
* `locatorDisplacement` maximum distance between location source updates _(iOS,Android/integer/meters)_
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
* `locatorInterval` maximum interval between location source updates _(iOS,Android/integer/seconds)_
* `locked` Locks settings screen on device for editing _(iOS/boolean)_
* `mode` Endpoint protocol mode _(iOS,Android/integer)_
    - `0` Private  MQTT _(iOS, Android)_
    - `2` Public  MQTT _(iOS, Android)_
    - `3` Private  HTTP _(iOS, Android)_
    - `4` Watson IOT Quickstart _(iOS)_
    - `4` Watson IOT Registered _(iOS)_
* `monitoring` Location reporting mode _(iOS,integer)_
    - `-1` Quiet
    - `0` Manual
    - `1` Significant
    - `2` Move
* `mqttProtocolLevel` MQTT broker protocol level _(iOS,Android/integer)_
    - `3` MQTT 3 (default)
    - `4` MQTT 3.1.1

* `notification` Show ongoing notification. Required to keep running in the background _(Android/boolean)_
* `notificationGeocoder` Resolve last reported location in ongoing notification to an address  _(Android/boolean)_
* `notificationLocation` Show last reported location in ongoing notification _(Android/boolean)_
* `passphrase` Passphrase of the client pkcs12 file _(iOS/string)_
* `password` Endpoint password _(iOS,Android/string)_
* `policymode` User defined securiy policy mode _(iOS/integer)_
    - `0` Do not used pinned certificates to validate servers
    - `1` Validate host certificates against public keys of pinned certificates
    - `2` Validate host certificates against pinned certificates
* `port` MQTT endpoint port _(iOS,Android/integer)_
* `positions` Number of locatoins to keep and display _(iOS/integer)_
* `pub` Automatic reporting _(Android/boolean)_
* `pubTopicBase` MQTT topic base to which the app publishes; `%u` is replaced by the user name, `%d` by device   _(iOS,Android/string)_
* `pubRetain` MQTT retain flag for reported messages _(iOS,Android/boolean)_
* `pubQos` MQTT QoS level for reported messages _(iOS,Android/boolean)_
* `ranging` Beacon ranging _(iOS/boolean)_
* `remoteConfiguration` Allow remote configuration by sending a `setConfiguration` cmd message _(Android/boolean)_
* `servercer` Blank separated list of certificate file names in DER format _(iOS/string)_
* `sub` subscribe to `subTopic` via MQTT _(iOS,Android/boolean)_
* `subTopic` A whitespace separated list of MQTT topics to which the app subscribes if `sub` is true (defaults see [topics](#topics))   _(iOS,Android/string)_
* `subQos` _(iOS,Android/boolean)_
* `tid` Two digit Tracker ID used to display short name and default face of a user _(iOS,Android/string)_
* `tls` MQTT endpoint TLS connection _(iOS,Android/boolean)_
* `tlsClientCrtPassword` Passphrase of the client pkcs12 file _(Android/string)_
* `url` HTTP endpoint URL to which messages are POSTed _(iOS,Android/string)_
* `updateAddressBook` _(iOS/boolean)_
* `usepolicy` Use user defined security policy _(iOS/boolean)_
* `username` Endpoint username _(iOS,Android/string)_
* `validatedomainname` Validate domain name during TLS handshake _(iOS/boolean)_
* `validatecertificatechain` Validate the whole certificate chain or just the server certificate _(iOS/boolean)_
* `watsonAuthToken` Auth token Watson IOT Registered mode _(iOS/string)_
* `watsonDeviceType` Device type in Watson IOT Registered mode _(iOS/string)_
* `watsonDeviceId` Device ID in Watson IOT Registered mode _(iOS/string)_
* `watsonOrganization` Organization in Watson IOT Registered mode _(iOS/string)_
* `willRetain`
* `willTopic`
* `willQos`
* `waypoints` Array of waypoint messages _(iOS,Android/array)_
* `quickstartId` Device ID in Watson IOT Quickstart mode _(iOS/string)_

#### Notes
* When importing a configuration message, all contained values are imported for the currently active mode. If the message also contains a `mode` element, the mode is changed first and all remaining elements are imported for the new mode.
* In MQTT mode the server will consider the client as dead if it the keepalive interval plus 50% passed without receiving any MQTT packet from the client (e.g. after 90 sec if `keepalive` was 60). Afterwards, an `lwt` message will be send.


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
{"_type":"cmd","action":"reportLocation"}
{"_type":"cmd","action":"reportSteps"}
{"_type":"cmd","action":"dump"}
{"_type":"cmd","action":"waypoints"}
{"_type":"cmd","action":"setConfiguration","configuration":{"_type":"configuration",...}
{"_type":"cmd","action":"setWaypoints","waypoints":{"_type":"waypoints","waypoints":[...]}
{"_type":"cmd","action":"action","content":"Backend maintenance scheduled for tonight\n\nhttp://support.owntracks.org"}
{"_type":"cmd","action":"action","content":"<a href='http://support.owntracks.org'>Backend Maintenance tonight</a>"}
{"_type":"cmd","action":"action","url":"http://support.owntracks.org"}
```
* `action` action to be performed by the device _(iOS,Android/string)_
  * `action` Inserts an additional _Featured Content_ tab in the UI _(iOS)_
  * `dump` Triggers the publish of a `configuration` message _(iOS)_
  * `reportSteps` Triggers the report of a `steps` messages_(iOS)_
    * `from` Timestamp _(iOS/epoch/optional)_
    * `to` Timestamp _(iOS/epoch/optional)_
  * `reportLocation` Triggers the publish of a `location` messages _(iOS,Android)_
  * `setWaypoints` Imports (merge) and activates new waypoints _(iOS,Android)_
    * `waypoints` Array of `waypoint` messages to import _(iOS,Android/array/required)_
  * `setConfiguration` Imports and activates new configuration values _(iOS,Android)_
    * `configuration` Configuration message to import _(iOS,Android/required)_
  * `waypoints` Triggers publish of a `waypoints` message _(iOS,Android)_

#### Notes
* If `url` for the `action` cmd message is specified, the URL is opened in a full screen web view within the app
* If the optional `extern` boolean is `true` a click on the notification will launch an external browser instead
* If `url` is not specified the text of the `content` element is displayed. Links embedded in the text are operational.
* If the `content` consists of HTML, it is rendered
* The _Featured Content_ tab can be removed with an `action` cmd message without `content` and without `url` element
* On iOS, the array of waypoints to the `setWaypoints` command allows updates / removal; the key of the waypoint is its name (`desc`). If you specify an invalid `lat` or `lon` (invalid means out of range number value e.g. `-1000000`, a string like `"foo"` will result in a `0` in app) the waypoint is deleted.

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

* `name` Name to identify a user _(iOS,Android/string/optional)_
* `face` Base64 encoded PNG image that is displayed instead of the Tracker ID _(iOS,Android/string/optional)_


## `_type=waypoints`
The app can export a list of configured waypoints to the endpoint.

```json
{
    "_type": "waypoints",
    elements
}
```
* `_creator` Identification of what created the array. Ignored by the apps _(iOS,Android/string/optional)_
* `waypoints` Array of `waypoint` messages _(iOS,Android/array/required)_


## `_type=encrypted`
Apps can optionally [encrypt](../features/encrypt.md) outgoing messages with a shared symmetric key.
The encrypted message is contained in the `data`element.
For security reasons, the encryption key is not exported with configuration messages and cannot be imported.

```json
{
    "_type": "encrypted",
    elements
}
```
* `data` Encrypted and Base64 encoded original JSON message _(iOS,Android/string/required)_

