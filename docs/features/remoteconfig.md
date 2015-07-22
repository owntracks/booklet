## Remote config

Instead of telling others how to edit settings on their device in order to use OwnTracks to connect to your server, you can help them yourself by sending them a ready-to-run configuration which they load onto their device. This is particularly useful in cases of "remote support" for parents, etc.

* Create a configuration file in JSON with an `.otrc` suffix (for OwnTracks Configuration). (The legacy `.mqtc` suffix is also supported.)
* Ensure the JSON is valid by using either of the [online JSON validator](http://jsonlint.com), a utility such as [jq](http://stedolan.github.io/jq/) or simply by running

```
python -mjson.tool < your.otrc
```
* Send the file to the device via e-mail, provide it for download on an HTTP server, send via Dropbox, ... you name it.
* Have the OwnTracks app user open the file on their device with OwnTracks.

Here is a sample configuration file which contains some of the possible settings.
 The `_type`
entry is mandatory, most other entries are optional; they are replaced by built-in
defaults if not specified.

```json
{
  "username": "jjolie",
  "password": "s1kr3t",
  "clientId": "janecli01",
  "pubRetain": true,
  "subTopic": "owntracks/+/+",
  "allowRemoteLocation": true,
  "cmd": true,
  "pubTopicBase": "",
  "_type": "configuration",
  "tid": "jJ",
  "host": "mybroker.example.org",
  "willRetain": false,
  "updateAddressBook": true,
  "port": 8883,
  "pubQos": 2,
  "locatorInterval": 180,
  "tls": true,
  "auth": true,
  "cleanSession": false
}
```

A list of all possible settings is described [on the JSON page](../tech/json.md).

## setWaypoints

(iOS only)

Waypoints can be configured remotely if _Remote Commands_ is enabled on the device, by publishing a JSON payload which is merged into existing waypoints. (Note: the "key" for a waypoint is the `tst` timestamp, so overwriting an existing `tst` actually overwrites an existing waypoint.)

```json
{
  "_type": "cmd",
  "action": "setWaypoints",
  "payload": {
    "waypoints": [
      {
        "desc": "Some place",
        "rad": 8867,
        "lon": 10.428771973,
        "lat": 46.935260881,
        "tst": 1437552714,
        "_type": "waypoint"
      }
    ],
    "_type": "waypoints"
  }
}
```

Publishing the above payload to an OwnTracks iOS device will create/modify said waypoint. This also works for Beacon configurations, which are basically waypoints.
