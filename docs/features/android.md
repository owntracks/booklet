## General Restrictions on Android 6 and higher. 
Since Android 6 and higher, the operating systems enforces stricter restrictions for apps running in the background. Most importantly, network access and background jobs are restricted to certain time slots. As a result, certain features might be restricted when the app is not actively used. This also applies if OwnTracks is exluded from battery optimizations.

* It is highly recommended to use HTTP mode on Android 6 and higher.
* The ongoing notification is required to run in the background.
* App standby or battery optimization may interfere with OwnTracks due to broken implementation on some devices. It is recommended to disable the Android battery optimization feature for OwnTracks in Settings > Battery > Battery Optimization.  
* Outgoing messages will only be sent in batched intervals. Location updates or event messages may be delayed.
* Incoming messages such as events might be delayed.
* When using MQTT, the broker connection is not maintained permanently. A reconnect will be attempted every 15 minutes or when a message is sent. Changing the keepalive interval does not change this behavior. If the connection is established without the clean session flag, missed messages will be received once connected.
* The app cannot attempt to connect if the network connection changes because it is not possible to receive the network change event.
* Beacons are no long supported in OwnTracks for Android

## Restrictions on Huawei Phones 
Huawei Phones will stop apps in the background. OwnTracks needs to be whitelisted as a protected app in order to run in the background. 

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
In MQTT mode the `host` and `username` is required to connect. If `authentication` is enabled, a `password` is required too.

## Debug Log

Since Owntracks 2.1.2 application logs can be exported easily to assist in debugging problems with the app. 

To generate the logs, follow the next steps 
* Open the configuration editor at Preferences > Configuration Management > Menu on the right top > Editor
* Under key enter `debugLog`
* As value enter `true`
* Restart the app at Menu on the right top > Restart

Application logs are written to the local Download folder at Downloads/owntracks_debug DD-MM-YYY.html
The file is appended after application restarts and degrades application performance. It should be disabled after gathering enough information. To do so, follow the steps above but enter `false` as value in the configuration editor. 

