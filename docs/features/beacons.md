# iBeacons

If you want to monitor an iBeacon region rather than a circular region set the radius to zero (0) and add the beacon identifier to the description separated by a colon (:).

![](https://raw.github.com/wiki/owntracks/owntracks/assets/waypoints/b-waypoint-config-ibeacon.jpg)

You add the UUID of the beacon to the description and may specify the major and minor identifier numbers of the beacons in hex.

When the UUID is valid, iBeacon monitoring will start.

Examples:
* 'myBeacon:CA271EAE-5FA8-4E80-8F08-2A302A95A959:0001:CAFE'
* 'myBeacons:CA271EAE-5FA8-4E80-8F08-2A302A95A959'

