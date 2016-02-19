## HTTP

An optional HTTP mode will be implemented. This mode which is like Private will use HTTP POST requests to a configurable HTTP endpoint which can bei either HTTP or HTTPS. The URL will be specified including parameters for _username_ and _devicename_ (`?u=user&d=device`), alternatively using the `X-Limit-U` and `X-Limit-D` headers.

All publishes which are currently done with MQTT will then be POSTed to the endpoint with exactly the same [JSON][json.md] payloads.

If the HTTP endpoint is reachable (i.e. it responds with _any_ status code), the payload is considered POSTed. In the event that the endpoint is unreachable (e.g. no connectivity), the payload will be queued and posted at a later stage.

[Encryption](../features/encrypt.md) will be supported and can be used with HTTP endpoints.

If the endpoint returns a status code 200, and its response contains JSON with a `_type: cmd`, that will be interpreted accordingly. Otherwise an empty JSON object `{}` shall be returned.
