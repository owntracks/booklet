## iOS / Android compared

The OwnTracks [Android](android.md) and [iOS](ios.md) apps try to be on par as far as the list
of supported features is concernened. This table summarizes the current status.

| Feature                                   |  iOS   | Android |
| ----------------------------------------- | :----: | :-----: |
| MQTT mode                                 |    Y   |    Y      |
| HTTP mode                                 |   Y    |    Y      |
| [Location reporting](location.md)         |   Y    |   Y     |
| [TLS](tls.md) with system certificate        |   Y    |   Y     |
| Plain connections (no TLS)                |   Y    |   Y     |
| Username/password authentication          |   Y    |   Y     |
| Reconnection to broker                    |   Y    |   Y     |
| Automatic PUB after move                  |   Y    |   Y     |
| Automatic PUB after time                  |   Y    |   Y     |
| Map with marker                           |   Y    |   Y     |
| Reverse geocoding on map                  |   Y    |   Y     |
| Accuracy of last-known loc                |   Y    |   Y     |
| Button for manual publish                 |   Y    |   Y     |
| Configurable settings                     |   Y    |   Y     |
| Move mode                                 |   Y    |   Y      |
| Friends list                              |   Y    |   Y     |
| Geofencing                                |   Y    |   Y     |
| [Waypoints](waypoints.md)                 |   Y    |   Y     |
| Location data (payload) [published as JSON](../tech/json.md) |   Y    |   Y     |
| [Remote configuration](remoteconfig.md)   |   Y    |   Y      |
| [iBeacons](beacons.md)                    |   Y    |          |
| [Step-counting, a.k.a pedometer](pedometer.md)   |   Y    |         |
| [Payload encryption](encrypt.md)          |   Y    |    Y    |


### Android

* The broker connection works well with:

  * No TLS (i.e. plain)
  * TLS with Android system certificate authorities
  * TLS with side-loaded user-provided self signed certificate authorities 
  * TLS with client certificates
  * Username/password authentication to broker.

* Automatic publishes at configurable maximum intervals under the condition that the devices moved the specified number of meters. Publishes may occur earlier if a new location is avaiable. 

* Display of a marker at which the app believes the user to be at
  (lastKnownLocation)

* Reverse geo coding that displays the address of lastKnownLocation

* Accuracy of lastKnownLocation 

* Button to manually publish lastKnownLocation



### iOS

* Runs on iPhones and iPads as an iPhone app. Not tested on iPods.

* Monitors "significant location changes" as defined by Apple Inc. (about 5 minutes AND 
  	"significant location changes" (>500m)) or as described in Move Mode below.
	In addition version >= 5.3 supports circular region monitoring (aka geo fences).
	In addition version >= 7.7 supports iBeacon region monitoring and ranging.

* publishes this locations via MQTT to the configured server while in foreground and background.

* The current location can be sent (a.k.a. published) on request.

