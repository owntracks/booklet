## Traccar

[Traccar](https://www.traccar.org) is a free and open source GPS tracking system for which there exists an `OwnTracks` plugin which, by default, runs on TCP port 5144. You configure our apps [in HTTP mode](../tech/http.md) to your Traccar server on that port, using a URL such as

```
http://traccar.example.net:5144
```

In Traccar proper, you configure your tracking deviceId as `tid` respectively in post-June 2017 versions of our apps, as your configured `topic`:

![Traccar device configuration](images/traccar-device.jpg)

Encryption is not supported nor are friends.
