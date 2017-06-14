## Traccar

[Traccar](https://www.traccar.org) is a free and open source GPS tracking system for which there exists an `OwnTracks` plugin which, by default, runs on TCP port 5144. You configure our apps [in HTTP mode](../tech/http.md) to your Traccar server on that port, using a URL such as

```
http://traccar.example.net:5144
```

In Traccar proper, you configure your tracking deviceId as `tid` respectively in post-June 2017 versions of our apps, as your configured `topic`:

![Traccar device configuration](images/traccar-device.jpg)

HTTP POST payloads shall contain at least the elements `lat`, `lon`, `_type:location`, `tst`, and either or both of `tid` and `topic`. If `topic` is given in the payload, that will be used as Traccar's _uniqueId_ (in which case `tid` will be added to attributes), else `tid`.

```json
{"lon":2.29513,"lat":48.85833,"_type":"location","tst":1497476456, "tid":"JJ"}
```

```json
{"lon":2.29513,"lat":48.85833,"_type":"location","topic":"owntracks/jane/phone", "tid": "JJ"}
```

Neither encryption nor friends are supported.
