## Security

OwnTracks and its associated utilities have been designed foremost with [data privacy](../privacy.md) in mind. We think your location or that of your family members and friends is something only you respectively they should know about. While we cannot help you (or ourselves for that matter) protect against NSA snooping, we do our best to protect your data from other people's eyes.

* If your broker supports it, and if you configure OwnTracks to do so, OwnTracks uses MQTT over [TLS](http://en.wikipedia.org/wiki/Transport_Layer_Security) when connecting to your broker. (We configure this and passwords automatically for you in [quicksetup](../guide/quicksetup.md).)
* The apps support authenticating to the MQTT broker with _username_ and _password_ which can be as strong (or as convoluted) as your broker supports.
* In HTTP mode, the apps provide support for HTTP basic authentication with TLS.
* Credentials you configure (i.e. _username_ and _password_) are thus protected from snooping, at least until they get to your broker. The same applies to location data our app transmits.
* The apps support using [TLS client certificates](tlscert.md) for authentication to the MQTT broker.
* The apps support [payload encryption](encrypt.md) for privacy.
* The apps do not send location data to any server until that server has been configured by the user (i.e., there is no preconfigured demo server).
