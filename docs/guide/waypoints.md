## Waypoints


f you've configured a geo-fence, a `location` message will
contain the elements:

* `rad`ius (if its value is greater than 0)
* `desc`ription with the name you set for the waypoint
* `event` with a value of `"enter"` or `"leave"`, depending on
   whether the device is entering or leaving a configured region, respectively.

If you set up a shared waypoint, the app publishes that waypoint (without the retain flag,
irrespective of your general preference) to the base topic with `/waypoints`
tacked onto the topic (e.g. `owntracks/<user>/<device>/waypoints`) with the
payload for `_type=waypoint` as specified in the [[JSON page|JSON]]. For waypoints with disabled sharing, no waypoint message is published. These can be used to note down locations privately. But note that entering or leaving a waypoint will be published as a `location` message as described above.

For example, If Jane configures a waypoint on her iPhone

the app will publish the following payload

When you set up a shared waypoint (with a `desc`ription and a `rad`ius), this
waypoint is published to the broker with the current time stamp. If you update
the waypoint definition on the device at a later stage, the waypoint is
re-published, with the _original_ timestamp, but with possibly new
`desc`ription and/or `rad`ius. To be precise, the `tst` timestamp is used as an
identifier of the waypoint even if it is later modified on the device. For waypoints that are not shared, the above obviously does not apply. 

Subscribers to the broker (our apps and any other program) can avoid getting
waypoints by subscribing to, say, `owntracks/+/+`; also broker ACLs can
prohibit access to `owntracks/+/+/waypoints` for particular users if so desired.
Conversely, all messages published by the apps (`location` and `waypoint`) are
available with a subscription to `owntracks/#`.

The OwnTracks apps may keep track of waypoints, e.g. for displaying to users. 



