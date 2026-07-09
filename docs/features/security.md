## Security

OwnTracks and its associated utilities have been designed foremost with [data privacy](../privacy.md) in mind. We think your location or that of your family members and friends is something only you respectively they should know about. While we cannot help you (or ourselves for that matter) protect against NSA snooping, we do our best to protect your data from other people's eyes.

* If your broker supports it, and if you configure OwnTracks to do so, OwnTracks uses MQTT over [TLS](http://en.wikipedia.org/wiki/Transport_Layer_Security) when connecting to your broker. (We configure this and passwords automatically for you in [quicksetup](../guide/quicksetup.md).)

* The apps support authenticating to the MQTT broker with _username_ and _password_ which can be as strong (or as convoluted) as your broker supports.

* In HTTP mode, the apps provide support for HTTP basic authentication with TLS.

* Credentials you configure (i.e. _username_ and _password_) are thus protected from snooping, at least until they get to your broker. The same applies to location data our app transmits.

* The apps support using [TLS client certificates](tlscert.md) for authentication to the MQTT broker.

* The apps have optional support for [payload encryption](encrypt.md) for privacy.

* The apps do not send location data to any server until that server has been configured by the user (i.e., there is no preconfigured demo server).

* The apps do, however, perform reverse geo location lookups.
    * For iOS these lookups are submitted to the Apple Maps infrastructure, and as reverse geo-coding is rate-limited on the iPhone we do not use it for all sent locations, but only when a location is displayed on user request, e.g. on the Friends page.
    * On Android the requests are performed via Google Play services for the non OSS release. This configuration can be optionally changed by configuring Android to use [OpenCage](../other/opencage.md) as reverse geo location provider. This functionality cannot be disabled.

* The OwnTracks Recorder should not be directly connected to the Internet. In MQTT mode it will typically be fronted by a TLS-capable MQTT broker, and in HTTP mode, we very strongly recommend placing it behind a TLS-protected HTTP reverse proxy which will typically also provide authentication.

### Security-related changes beginning in July 2026

Beginning with our iOS app version 26.2.2, and our Android app version 2.6.0 sundry security-related changes have been implemented. (The Changelogs of the respective repositories have all the details.)

The following changes have been implemented:

- a security advisory covering several intent-handling and remote configuration vulnerabilities has been addressed.
- external configuration loading (via `owntracks://` URLs and via `*.otrc` config files) is now disabled by default and must be explicitly enabled in Settings. Once enabled, the apps can be remotely configured.
- a confirmation dialog is shown when enabling external configuration, warning that any config URL can fully reconfigure the app.
- all intents/shortcuts now require a shared secret (`Intent Auth Key`) in every intent, preventing un-authorized apps from triggering location publishes or changing monitoring mode. The `Intent Auth Key` is created once when the app is installed.
- security-related preferences (on iOS these are `allowConfigurationByURIAndConfigFile`, `allowIntentControl`, `intentAuthKey`, and on Android these are `allowConfigurationByURIAndConfigFile`, `allowIntentControl,` `intentAuthKey`) cannot be changed via imported config files or URLs

Many users will not need to concern themselves with these settings, but they might be important for OwnTracks users who wish to remotely control the OwnTracks apps.
