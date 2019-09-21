## General Restrictions on Android 6 and higher.
Since Android 6 and higher, the operating systems enforces stricter restrictions for apps running in the background. Most importantly, network access and background jobs are restricted to certain time slots. As a result, certain features might be restricted when the app is not actively used. This also applies if OwnTracks is exluded from battery optimizations.

* It is highly recommended to use HTTP mode on Android 6 and higher.
* The ongoing notification is required to run in the background.
* App standby or battery optimization may interfere with OwnTracks due to broken implementation on some devices. It is recommended to disable the Android battery optimization feature for OwnTracks in Settings > Battery > Battery Optimization.
* Outgoing messages will only be sent in batched intervals. Location updates or event messages may be delayed.
* Incoming messages such as events might be delayed.
* When using MQTT, the broker connection is not maintained permanently. A reconnect will be attempted regularly or when a message is sent. Changing the keepalive interval does not change this behavior. If the connection is established without the clean session flag, missed messages will be received once connected.
* The app cannot attempt to connect if the network connection changes because it is not possible to receive the network change event.
* Beacons are no long supported in OwnTracks for Android

## Vendor background restrictions
Certain vendors have their own restrictions for apps running in the background. On these devices, Owntracks might be killed even though it behaves according to the official Android background execution limits.

A list of vendors known to interfer with background apps and a number of workarounds can be found at [Don’t kill my app!](https://dontkillmyapp.com/).

## Google Play Services
Google Play Services are required to use OwnTracks. There are no plans to remove the dependency.

They are used for
* Battery efficient location access
* Geofencing
* Scheduling of background jobs
* Resolving coordinates to addresses (an alterative Geocoding service can be configured)
* Displaying the map

## Setup

In the unlikely case that your broker does not require a username and password, you can disable authentication in the configuration editor. Please note that even then, you have to set a device name and username before you can connect as these are also used to construct various other internal things.

By default a [TLS](tls.md) secured communication channel with the broker is assumed. Thus you have to ensure that the broker certificate is properly imported on your device.

In recent Android versions, a passcode is required once custom TLS certificates are imported. If you want to circumvent this, you can enable TLS with custom certificates and import that into the private storage location of the app. This will circumvent the Android TLS certificate management by using the certificate directly and skipping any other certificates known to the system.

In HTTP mode the `url` is required to connect.
In MQTT mode the `host` and `username`  and `password` is required to connect.

## Debug Log

Since Owntracks 2.1.2 application logs can be exported easily to assist in debugging problems with the app.

To generate the logs, follow the next steps:

* Temporarily grant write access to local storage. Go to Android App Info > Permissions and enable the storage permission.
* Open the configuration editor at Preferences > Configuration Management > Menu on the right top > Editor
* Under key enter `debugLog`
* As value enter `true`
* Restart the app at Menu on the right top > Restart

Application logs are written to the local Download folder at Downloads/owntracks_debug DD-MM-YYY.html
The file is appended after application restarts and degrades application performance. It should be disabled after gathering enough information. To do so, follow the steps above but enter `false` as value in the configuration editor.

## Automation via Tasker, etc.

Since Owntracks 2.1 it is possible to automate changes to the location monitoring mode by sending Owntracks an intent. You can change the mode to one of the following pre-defined [modes](https://owntracks.org/booklet/features/location/): `Move`, `Significant location change`,`Manual`,`Quiet`.

### Example: 
To trigger `Move` mode in tasker, create a `Send Intent` action and enter the following information:
* Action: `org.owntracks.android.CHANGE_MONITORING`
* Cat: `None`
* Mime Type: [LEAVE FIELD BLANK]
* Data: [LEAVE FIELD BLANK]
* Extra(1): `monitoring:2`
* Extra(2): [LEAVE FIELD BLANK]
* Extra(3): [LEAVE FIELD BLANK]
* Package: [LEAVE FIELD BLANK]
* Class: [LEAVE FIELD BLANK]
* Target: `Service`
  
| Mode  | Send This Extra Value|
|---|---|
|Quiet|`monitoring:-1`|
|Manual|`monitoring:0`|
|Significant Changes|`monitoring:1`|
|Move|`monitoring:2`|

To set other monitoring modes, use the same example and simply substitute the Extra in the table above that coresponding to the desired state of Owntracks into the first Extra field of the Intent Action.  Do not put any text into the fields listed as [LEAVE FIELD BLANK] above.
