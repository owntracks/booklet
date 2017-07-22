## General Restrictions on Android 6 and higher. 
Since Android 6 and higher the operating systems enforces stricter restrictions for apps running in the background. Most importabtly, network access and background jobs are restricted to certain time slots. As a result certain features might be restricted when the app is not actively used. This also applies if OwnTracks is exluded from energy optimizations.

* It is highly recommended to use HTTP mode on Android 6 and higher.
* The ongoing notification is required to run in the background.
* App standby or battery optimization may interfer with OwnTracks due to broken implementation on some devices. It is recommended to disable the Android battery optimizations feature for OwnTracks in Settings > Battery > Battery Optimization.  
* Outgoing messages will only be send in batched intervals. Location updates or event messages may be delayed.
* Incomming messages such as events might be delayed.
* When using MQTT, the broker connection is not maintained permanently. A reconnect will be attempted every 10 minutes or when a messages is sent. Changing the keepalive intervall does not change this behavior. If the connection is established without the clean session flag, missed messages will be received once connected.
* The app cannot attempt to connect if the network connection changes because it is not possible to receive the network change event.
* Beacon detection might not work reliable because background scanning intervals are severely restricted.

## Restrictions on Huawei Phones 
Huawei Phones will stop apps in the background. OwnTracks needs to be whitelisted as a protected app in order to run in the background. 

## Beacon Scanning
Beacon detection might cause all sorts of problems due to bad implementation by the device vendor.
This includes WiFi issues, intermittently working beacon detection or beacon detection not working at all.
In the default settings, iBeacons and AltBeacons can be detected. A custom `beaconLayout` can be provided to detect other types.

## Google Play Services
Google Play Services are required to use OwnTracks. There are no plans to remove the dependency.

They are used for
* Battery efficient location access
* Geofencing
* Scheduling of background jobs
* Resolving coordinates to adresses
* Displaying the map

## Setup

In the unlikely case that your broker does not require a username and password, you can disable authentication in the connection preferences. Please note that even then, you have to set a device name and username before you can connect as these are also used to construct various other internal things.

Per default a [TLS](tls.md) secured communication channel with the broker is assumed. Thus you have to ensure that the broker certificate is properly imported on your device.

In recent Android, a passcode is required once custom TLS certificates are imported. If you want to circumvent this, you can enable TLS with custom certificates and import that into the private storage location of the app. This will circumvent the Android TLS certificate management by using the certificate directly and skipping any other certificates known to the system.

In HTTP mode the `url` is required to connect.
In MQTT mode the `host` and `username` is required to connect. If `authentication` is enabled, a `password` is required too.



