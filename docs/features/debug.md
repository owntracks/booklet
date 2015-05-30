## Debugging

Designing mobile apps is hard. Designing mobile apps which are killed off every once in a while by the underlying OS is harder (iOS). Designing mobile apps which reliable do what they should at all times is hardest. [Christoph and Alexander](../people.md) know this and they've done a hell of a job to get stability and reliability of OwnTracks to where it stands today.

Be that as it may, there are things which can be difficult to test, and in this section we give you some things to watch out for.

First and foremost, and please don't laugh: does your device have either WiFi or 3G connectivity? No data means no location reported, which ought to be self-explanatory.

* Check whether OwnTracks is connected to your broker. On iOS this is the green mushroom symbol on the map; on Android you can check in preferences. If need be, disconnect and reconnect OwnTracks and check your broker's logfile as you do so.
* Is your broker running and can it be reached by your device? We've [given you tips on configuring your broker](../guide/broker.md).
* Make liberal use of the Mosquitto [clients](../guide/clients.md) to test whether your broker is receiving messages, and please ensure your broker ACLs match the settings in OwnTracks.

