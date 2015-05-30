## Remote config

Getting bored and frustrated by telling other people how to edit settings on their iPhone to connect to your server?

Here's what you can do with OwnTracks:

* Create a configuration file in JSON with an `.otrc` suffix (for OwnTracks Configuration). (The legacy `.mqtc` suffix is also supported.)
* Send the file to the device via email, http, dropbox, ... you name it.
* Open the file on the device with OwnTracks

Here is a sample configuration file which follows the settings as explained
above. Boolean values are represented as `0` (false) or `1` (true).  The `_type`
entry is mandatory, all other entries are optional; they are replaced by built-in
defaults if not specified.

```json
{
    "_type": "configuration",
    "deviceid": "phone",
    "clientid": "jane-phone",
    "subscription": "owntracks/#",
    "topic": "owntracks/jane/phone",
    "host": "broker.my.net",
    "user": "jane",
    "pass": "secr3t",
    "will": "lwt",
    "willtopic": "",
    "subscriptionqos": "1",
    "qos": "2",
    "port": "8883",
    "keepalive": "60",
    "willqos": "1",
    "retain": "1",
    "tls": "1",
    "auth": "1",
    "clean": "0",
    "willretain": "0",
    "mindist": "200",
    "mintime": "180",
    "monitoring": "1",
    "ab": "0",
    "ranging": "0",
    "cmd": "0"
}
```
