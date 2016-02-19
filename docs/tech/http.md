## HTTP

An optional HTTP mode will be implemented. This mode which is like Private will use HTTP POST requests to a configurable HTTP endpoint which can bei either HTTP or HTTPS.

In this mode:

* There are no "friends". The device can publish only which means you cannot track other people.
* There is no support for [card](../features/card.md)s.

The URL will be specified including parameters for _username_ and _devicename_ (`?u=user&d=device`), alternatively using the `X-Limit-U` and `X-Limit-D` headers. Other than that, the syntax for the URL is

```
http[s]://[user[:password]@]host[:port]/path`
```

All publishes which are currently done with MQTT will then be POSTed to the endpoint with exactly the same [JSON][json.md] payloads.

If the HTTP endpoint is reachable (i.e. it responds with _any_ status code), the payload is considered POSTed. In the event that the endpoint is unreachable (e.g. no connectivity), the payload will be queued and posted at a later stage.

[Encryption](../features/encrypt.md) will be supported and can be used with HTTP endpoints.

If the endpoint returns a status code 200 it will typically return an empty JSON payload object `{}`. The HTTP endpoint may, however, return a command to the OwnTracks device, in which case it must return a valid `_type: cmd` as described in [JSON](../tech/json.md).
