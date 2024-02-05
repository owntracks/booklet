## iOS / Android compared

Our OwnTracks apps try to be on par in terms of features, but there are differences. This table lists the main features, and specifics for [Android](android.md) and [iOS](ios.md) are documented.

| Feature                                   |  iOS   | Android |
| ----------------------------------------- | :----: | :-----: |
| [MQTT](../tech/mqtt.md) mode              |   Y    |    Y    |
| [HTTP](../tech/http.md)  mode             |   Y    |    Y    |
| [Location reporting](location.md)         |   Y    |   Y     |
| [TLS](tls.md) with system certificate     |   Y    |   Y     |
| Plain connections (no TLS)                |   Y    |   Y     |
| Username/password authentication          |   Y    |   Y     |
| Reconnection to broker                    |   Y    |   Y     |
| Automatic PUB after move                  |   Y    |   Y     |
| Automatic PUB after time                  |   Y    |   Y     |
| Button for [manual publish](ui.md)        |   Y    |   Y     |
| Move mode                                 |   Y    |   Y      |
| Map with [TID](tid.md) markers            |   Y    |   Y     |
| Support for [CARD](card.md) on map        |   Y    |   Y     |
| Reverse geocoding on map                  |   Y    |   Y     |
| Use of external geocoders ([OpenCageData](../other/opencage.md))  |        |   Y     |
| Accuracy of last-known loc                |   Y    |   Y     |
| Friends list                              |   Y    |   Y     |
| Geofencing (a.k.a. [Regions](waypoints.md))|  Y    |   Y     |
| [Transition events](location.md) (enter / leave)         |   Y    |   Y     |
| Location data (payload) [published as JSON](../tech/json.md) |   Y    |   Y     |
| [iBeacons](beacons.md)                    |   Y    |          |
| [Step-counting, a.k.a pedometer](pedometer.md)   |   Y    |         |
| Barometric pressure in location payloads  |   Y    |         |
| [Payload encryption](encrypt.md)          |   Y    |    Y    |
| Submit [Points of Interest](poi.md) (POI) |   Y    |         |
| On-device [Tour](tours.md) configuration  |   Y    |         |
| Popup an [info tab](featured.md) on command  |   Y    |         |
| [Debugging](debug.md) with on-device logs |   Y    |    Y    |
| Supports POSTing [to Traccar](traccar.md) |   Y    |    Y    |
| Configurable settings                     |   Y    |   Y     |
| Configuration editor                      |        |   Y     |
| [Remote and URL configuration](remoteconfig.md)   |   Y    |   Y      |
| Configuration via [QR code](../tech/qr.md)        |   Y    |          |

