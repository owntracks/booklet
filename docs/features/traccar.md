## Traccar

[Traccar](https://www.traccar.org) is a free and open source GPS tracking system for which there exists an `OwnTracks` protocol decoder (called `owntracks`)  which is, by default, configured on TCP port 5144 on the Traccar server:

```xml
<entry key='owntracks.port'>5144</entry>
```

Thus you need to configure OwnTracks clients [in HTTP mode](../tech/http.md) to connect to your Traccar server at this port, using a URL such as
```
http://traccar.example.net:5144
```

To make Traccar accept a connection from an owntracks client, you need to set a matching _identifier_. This identifier is per default the `tid` of your device. If you have configured a `topic` for your device, owntracks will identify  using the `topic` instead of the `tid`. 

![Traccar device configuration](images/traccar-device.jpg)

An example of a configuration using a `topic` of `owntracks/jane/phone` to identify against the Traccar server.


### Notes

* Neither encryption nor friends are supported in Traccar.
* If you see a 400 error in the Traccar log, this means the _identifier_ you configured for the Traccar device doesn't match the one sent by the OwnTracks app; try the other one, `tid` or `topic`.

### HTTP Payloads

HTTP POST payloads shall contain at least the elements `lat`, `lon`, `_type:location`, `tst`, and either or both of `tid` and `topic`. If `topic` is contained in the payload, that will be used as Traccar's _identifier_ (in which case `tid` will be added to attributes), else `tid`.

```json
{"lon":2.29513,"lat":48.85833,"_type":"location","tst":1497476456, "tid":"JJ"}
```

```json
{"lon":2.29513,"lat":48.85833,"_type":"location","topic":"owntracks/jane/phone", "tid": "JJ"}
```

The following JSON elements, if they're contained in the HTTP payload, will be added to Traccar's position attributes: `vel`, `alt`, `cog`, `acc`, `t`, `batt`, so with an HTTP payload that an OwnTracks app produces like

```json
{
  "cog": 271,
  "batt": 41,
  "lon": 2.29513,
  "acc": 5,
  "vel": 61,
  "vac": 21,
  "lat": 48.85833,
  "t": "u",
  "tst": 1497508651,
  "alt": 167,
  "_type": "location",
  "topic": "owntracks/jane/iphone",
  "p": 71,
  "tid": "JJ"
}
```

a query on the Traccar API could produce something like this:

```json
[
  {
    "id": 475,
    "attributes": {
      "t": "u",
      "battery": 41,
      "tid": "JJ",
      "ip": "127.0.0.1",
      "distance": 0,
      "totalDistance": 0
    },
    "deviceId": 4,
    "type": null,
    "protocol": "owntracks",
    "serverTime": "2017-06-15T06:37:32.000+0000",
    "deviceTime": "2017-06-15T06:37:31.000+0000",
    "fixTime": "2017-06-15T06:37:31.000+0000",
    "outdated": false,
    "valid": true,
    "latitude": 48.85833,
    "longitude": 2.29513,
    "altitude": 167,
    "speed": 1.18575,
    "course": 271,
    "address": "9 Avenue Anatole France, Paris, Île-de-France, FR",
    "accuracy": 5,
    "network": null
  }
]
```
