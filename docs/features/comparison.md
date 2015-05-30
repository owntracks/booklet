## iOS / Android compared

The OwnTracks [[Android]] and [[iOS]] apps try to be on par as far as the list
of supported features is concernened. This table summarizes the current status.

| Feature                                   |  iOS   | Android |
| ----------------------------------------- | :----: | :-----: |
| [[Location reporting|Location]]           |   Y    |   Y     |
| [[TLS]] with system cert                  |   Y    |   Y     |
| TLS with cert in filesystem               |   -    |   Y     |
| Plain connections (no TLS)                |   Y    |   Y     |
| Username/password authentication          |   Y    |   Y     |
| Reconnection to broker                    |   Y    |   Y     |
| Automatic PUB after move                  |   Y    |   Y     |
| Automatic PUB after time                  |   Y    |   Y     |
| Map with marker                           |   Y    |   Y     |
| Reverse geocoding on map                  |   Y    |   Y     |
| Accuracy of last-known loc                |   Y    |   Y     |
| Button for manual publish                 |   Y    |   Y     |
| Button for sharing location               |        |   Y     |
| Configurable settings                     |   Y    |   Y     |
| Move mode                                 |   Y    |         |
| Friends list                              |   Y    |   Y     |
| Geofencing                                |   Y    |   Y     |
| [[Waypoints]]                             |   Y    |   Y     |
| Location data (payload) [[published as JSON|JSON]] |   Y    |   Y     |
| [[Remote configuration|RemoteConfig]]     |   Y    |         |
| [[iBeacons]]                              |   Y    |         |
| [[Step-Counting]]                         |   Y    |         |


### Android

* The broker connection works well with:

  * No TLS (i.e. plain)
  * TLS using the Android built-in certificate authorities (either the shipped
    ones, or user provided ones that require a lock screen password to be set)
  * TLS with user-provided certificates via an absolute path (e.g. to Downloads).
    This doesn't require a password to be set on the device, but is a bit fiddly
    to set up.
  * Username/password auth works.

* Automatic publishes at configurable intervals (disabled or > 1 minute)

* Display of a marker at which the app believes the user to be at
  (lastKnownLocation)

* Reverse geo coding that displays the address of lastKnownLocation

* Accuracy of lastKnownLocation (if the accuracy is > 250m, the marker
  changes to a circle centered around lastKnownLocation with radius == accuracy)

* Button to manually publish lastKnownLocation

* Button to share a Google Maps link that places a marker at lastKnownLocation

* For location the Google Fused Location Provider at Balanced Battery
  Settings is used. This one can re use GPS or other Position fixes that are
  requested by other apps in order to save battery and select the most
  appropriate position source.


### iOS

* Runs on iPhones running iOS >=6.1 (3GS, 4, 4S, 5, ...) and iPads running iOS >=6.1 as an iPhone app. Not tested on iPods yet.
* UI is iOS7 compliant

* Monitors "significant location changes" as defined by Apple Inc. (about 5 minutes AND 
  	"significant location changes" (>500m)) or as described in Move Mode below.
	In addition version >= 5.3 supports circular region monitoring (aka geo fences).
	In addition version >= 7.7 supports iBeacon region monitoring and ranging.

* publishes this locations via MQTT to the configured server while in foreground and background.

* The current location can be sent (a.k.a. published) on request.

