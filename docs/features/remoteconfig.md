## Configuration file 
The configuration can be send to the device as a file that can by imported into the apps. This can be used to initially configure the device or chane detailed settings later. 
 
* Create a configuration file in JSON with an `.otrc` suffix (for OwnTracks Configuration)
* Ensure the JSON is valid with [online JSON validator](http://jsonlint.com), [jq](http://stedolan.github.io/jq/) or simply by running `python -mjson.tool < your.otrc`
* Send the file to the devices via e-mail, provide it for download on an HTTP server, send via Dropbox, ... you name it.
* Have the OwnTracks app user open the file on their device with OwnTracks.

Here is a sample configuration file which contains some of the possible settings.
```json
{
  "_type": "configuration",
  "auth": true,
  "username": "jjolie",
  "password": "s1kr3t",
  "host": "mybroker.example.org",
  "port": 8883,
}
```
The `_type`entry is mandatory, most other entries are optional. They are replaced by built-in defaults or previously configured values if not specified. A list of all possible settings is described [on the JSON page](../tech/json.md).

## setConfiguration
The configuration of a connected app can also be changed remotely if _Remote Commands_ is enabled on the device, by publishing a JSON payload which is merged into existing configuration. A `_type: configuration` as described [on the JSON page](../tech/json.md) can be send to the device. 

## setWaypoints

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
