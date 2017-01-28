## Topics

If you use HTTP mode skip this chapter.

You now know that [MQTT](mqtt.md) is a messaging protocol, and that it uses
topic names to label messages.  topic names can be structured into multiple
topic levels by separating them with a forward slash (`/`). topic levels are
UTF-8 text strings, and almost any character is valid except `/`, `*`, and `+`.
The last two are used to address sets of topic names.

The principles during the design of the OwnTracks topic-naming scheme were

* human readability
* traffic minimization
* granular access control

## topic name Root

Typically an MQTT broker hosts multiple applications. To create a separate name space
for OwnTracks we use a topic name root as the beginning of a topic name. Per default
this is `owntracks/`, but you may choose any other root (e.g. `peters/locations/`) or,
if you prefer, the empty string. In order to change the default behaviour of `owntracks/`
create a [personalized configuration](features/remoteconfig.md) from scratch or, export
your current configuration, edit that and re-import to your device. On Android you trigger
the import function by opening the `.otrc` file in a file manager; on iOS by, say, opening
the file in Mail, Dropbox or similar app. The settings which have to be changed are

```
pubTopicBase
subTopic
```

## Device name

Locations are captured in OwnTracks by devices (e.g. your Smartphone). The
device name forms the second component of the topic name. In order to avoid
clashes in naming and to simplify subscription and authorization we decided to
use two topic levels

1. The user name or identifier (e.g. `peter`)
2. The device name (e.g. `iPhone`)

A typical topic name for a device would therefore be `peter/iPhone`. Other
device naming schemes are possible and transparent to OwnTracks (e.g. using
just one level `peters-iPhone` or multiple levels `aFamily/father/iPhone`), but
under any one OwnTracks name root the number of levels used for device names
have to be the same.

## Base topic name

Combining the topic name root and the device name result in what we call _base
topic name_ for each device (e.g. `owntracks/peter/iPhone`).  The devices label
their location messages with the _base topic name_ when publishing to the MQTT
broker.

## Subscribing to location Messages

Given the structure explained above, the topic filter you can use to subscribe to the
location messages of all devices is `owntracks/+/+` (the topic name root plus a so-called
wild card (`+`) for each level of the device name.

## Additional topic names

For additional communication from and to devices, OwnTracks uses topic names relative
to the base topic name.

Devices listen to the relative topic name `cmd` for commands you can publish to
OwnTracks devices (i.e. `owntracks/peter/iPhone/cmd`). For example, if
authorized, you could request a location update from a friend's device by
publishing an [appropriate payload](../tech/json.md) to said topic.

The output of specific OwnTracks commands is published by the device to the
relative topic names `step`, `dump`, etc.

In addition, devices subscribe to the relative topic name `info` for information about 
other devices. Messages published with the relative topic name `info`
(e.g. `owntracks/peter/iPhone/info`) contain a _card_ payload -- the name and
avatar of a user with which authorized users can identify, say, Peter within their OwnTracks app.

Devices publish their [waypoints](waypoints.md) to the relative topic name
`waypoint` and the corresponding events the transition into or out of a
waypoint triggers, to the relative topic name `event`.

## Summary

A device (named `peter/iPhone` in this example) is connected to an [MQTT
Broker](broker.md) with the OwnTracks name Root `owntracks`. It publishes
location messages labeled with the topic name `owntracks/peter/iPhone`. The
same device publishes [waypoints](waypoints.md) labeled as
`owntracks/peter/iPhone/waypoint`and publishes to
`owntracks/peter/iPhone/event` when entering or leaving a waypoint (i.e. a monitored region).

The same device subscribes to `owntracks/+/+` to receive location messages of all 
other devices it is authorized for which are connected to the same MQTT broker.
In addition, the device may listen to `owntracks/+/+/event`if the user of the devices
wants to be informed about other users entering or leaving monitored regions. Subscribing
to `owntracks/+/+/info`gives the device access to additional information about the other
devices.

If a device's owner allows other users to ad-hoc request messages from his device,
the device will subscribe to `owntracks/peter/iPhone/cmd` as well, and it will listen for OwnTracks
commands; responses to these commands will be published and labeled `owntracks/peter/iPhone/dump`,
`owntracks/peter/iPhone/step`, etc.

A full set of supported topic names and expected payloads is documented in [our JSON API](../tech/json.md).

## Terms used

* topic name
* OwnTracks topic name root
* OwnTracks device name
* OwnTracks base topic name
* relative topic names
* OwnTracks device
* OwnTracks user
* OwnTracks command
