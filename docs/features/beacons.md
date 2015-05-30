## iBeacons

If you want to monitor an iBeacon region rather than a circular region set the
radius to zero (`0`), and add the beacon identifier to the description
separated by a colon (:).

![Beacon configuration](images/b-waypoint-config-ibeacon.jpg)

You add the UUID of the beacon to the description, and you can optionally specify the major and/or
minor identifier numbers of the beacons in hex if you desire finer control over which beacons OwnTracks will monitor.

If the UUID is valid, iBeacon monitoring will start.

Examples:

```
myBeacons:CA271EAE-5FA8-4E80-8F08-2A302A95A959
mySpecificBeacon:CA271EAE-5FA8-4E80-8F08-2A302A95A959:0001:CAFE
```

In the first example above, OwnTracks will monitor all beacons with the specified UUID, whereas
in the second example, OwnTracks would monitor just that one specific beacon with the major number `0001` and the minor number `CAFE`.
