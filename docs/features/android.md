## Android specifics

* The broker connection works well with:

      * No TLS (i.e. plain)
      * TLS with Android system certificate authorities
      * TLS with side-loaded user-provided self signed certificate authorities
      * TLS with client certificates
      * Username/password authentication to broker.

* Automatic publishes at configurable maximum intervals under the condition that the devices moved the specified number of meters. Publishes may occur earlier if a new location is available.

* Display of a marker at which the app believes the user to be at
  (last known location)

* Reverse geo coding that displays the address of last known location

* Accuracy of last known location

* Button to manually publish last known (i.e. probably current) location


## General Restrictions on Android 6 and higher.

Since Android 6 and higher, the operating systems enforces stricter restrictions for apps running in the background. Most importantly, network access and background jobs are restricted to certain time slots. As a result, certain features might be restricted when the app is not actively used. This also applies if OwnTracks is exluded from battery optimizations.

* You may find HTTP mode more reliable on Android 6 and higher.
* The ongoing notification is required to run in the background.
* App standby or battery optimization may interfere with OwnTracks due to broken implementation on some devices. It is recommended to disable the Android battery optimization feature for OwnTracks in Settings > Battery > Battery Optimization.
* Outgoing messages may only be sent in batched intervals. Location updates or event messages may be delayed.
* Incoming messages such as events might be delayed.
* When using MQTT, the broker connection is not guaranteed to be maintained permanently. A reconnect will be attempted regularly or when a message is sent. If the connection is established without the clean session flag, missed messages will be received once connected.
* Beacons are no long supported in OwnTracks for Android

## Vendor background restrictions

Certain vendors have their own restrictions for apps running in the background. On these devices, Owntracks might be killed even though it behaves according to the official Android background execution limits.

A list of vendors known to interfer with background apps and a number of workarounds can be found at [Don’t kill my app!](https://dontkillmyapp.com/).

## Google Play Services

Google Play Services are required to use OwnTracks that's distributed on the Google Play Store.

They are used for:

* Battery efficient location access
* Scheduling of background jobs
* Resolving coordinates to addresses (an alterative Geocoding service can be configured)
* Displaying the map

A separate "flavour" of OwnTracks called "OSS" aims to remove this dependency for people that can't or prefer not to use Google Play Services. This version will use other providers for maps, geocoding and location and is currently in development. The plan is to release this via the [F-Droid](https://www.f-droid.org/) app catalogue.

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
* Open the log viewer at _Status_ > _View Logs_
* The most recent logs are displayed. To view debug log entries as well, select the _Debug Logs_ option in the top right menu.
* The _Share_ button (bottom right) allows the logs to be exported and shared, either to disk (via a suitable file manager app such as _Total Commander_) or sent over email.

## Automation via Tasker, Automagic, etc.

Since Owntracks 2.1 it is possible to automate changes to the location monitoring mode by sending Owntracks an intent. You can change the mode to one of the following pre-defined [modes](https://owntracks.org/booklet/features/location/): `Move`, `Significant location change`,`Manual`,`Quiet`.

To set modes, use the same example as provided below and simply substitute the Extra in the table that corresponds to the desired state of Owntracks into the first Extra field of the Intent Action.  Do not put any text into the fields listed as [LEAVE FIELD BLANK] above.

### Tasker example: 

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

### Macrodroid example:

To trigger `Move` mode in tasker, create a `Send Intent` action and enter the following information:

* Target: `Service`
* Action: `org.owntracks.android.CHANGE_MONITORING`
* Package: `org.owntracks.android`
* Class: [LEAVE FIELD BLANK]
* Mime Type: [LEAVE FIELD BLANK]
* Data: [LEAVE FIELD BLANK]
* Extra 1 parameter name: `monitoring`
* Extra 1 value: `2`
* Extra(2): [LEAVE FIELD BLANK]
* Extra(3): [LEAVE FIELD BLANK]
* Extra(4): [LEAVE FIELD BLANK]

| Mode  | Send This Extra Value|
|---|---|
|Quiet|`-1`|
|Manual|`0`|
|Significant Changes|`1`|
|Move|`2`|

### Automagic example: 

To trigger `Move` mode in Automagic, create a `Start Service` action and enter the following information:

* Action: `org.owntracks.android.CHANGE_MONITORING`
* Category List: [LEAVE FIELD BLANK]
* Data URI: [LEAVE FIELD BLANK]
* Data MIME Type: [LEAVE FIELD BLANK]
* Explicit Component: ticked
* Package Name: `org.owntracks.android`
* Class Name: [LEAVE FIELD BLANK]
* Flag List: [LEAVE FIELD BLANK]
* Extra: `putInt("MONITORING": 2)`
  
| Mode  | Send This Extra Value|
|---|---|
|Quiet|`putInt("monitoring": -1)`|
|Manual|`putInt("monitoring": 0)`|
|Significant Changes|`putInt("monitoring": 1)`|
|Move|`putInt("monitoring": 2)`|

Other intents are, for instance:

- `org.owntracks.android.SEND_LOCATION_USER` sends a manual report (i.e. pushes a location) 

### ADB example

It's possible to send an intent to OwnTracks using the `am` command on the device shell. You just need to call `start-service` with the correct action, and optionally the extra that you need to set the specific mode.

E.g. to set move mode:

    am start-service -a org.owntracks.android.CHANGE_MONITORING --ei monitoring 2
