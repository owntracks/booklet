# Ideas

Over the course of time, we've had people propose a number of ideas for OwnTracks. Some of these we thought excellent, others we thought would maybe sometime be nice to have, and others, well, see for yourself. Be that as it may, this page lists a few of these in the hope that you'll see we know of them and if you dream up something new, that you'll first check wether we've heard of it already.

* Allow KML, GPX export. This we do enable in the back-end.
* Publish _charging_ alongside battery level. No.
* Add temperature to the JSON payload. No.
* Have the device track a route; we do this in the back-end.
* Feature to start a "sequence" which sets two custom attributes for transport mode (e.g. bicycle, walk, etc.) and/or a route name (e.g. drivehome2work_01032021) until manually turned off. If no sequence is started the attribute is just left empty. Makes querying the data much easier.

## Short term

## Mid-term

## Long-term

* <del>Add presence. Are my friends in the area?</del>
  * <del>Needs friends/family on same broker</del>
  * <del>Needs 'standardized' topic names (maybe with Twitter id in topic?)</del>
* Add transportation info (e.g. trains) as friends
* Queue updates on device (with `tst` etc) to be PUBlished upon available connection
* NFC support, [suggested by mrizvic](https://community.owntracks.org/topic/38/nfc-support)

## Very-long term, a.k.a. "Neat ideas"

* Publish incoming phone call (caller-id), [submitted by @bordingnon](http://twitter.com/bordignon/status/372627079059079168). JPM: Also SMS? Have to force TLS then, at least.
* Requested in #86: "app should register a subscriprion (configurable topic) a) if someone sends a text message it should be displayed as popup window b) if someone sends an HTML message it should be opened in a embedded browser"
* Generalized messaging app 
* Indoors edition with iBeacons as requested in [#296](https://github.com/binarybucks/mqttitude/issues/296)

#### Waypoints

<del>JPM added 2013-11-22: We currently have "Annotations" on iOS (see above). I'd like to be able to have an annotation PUBlished to the broker so that m2s (or other processors) can pick it up. I propose the following payload: `{ "_type": "_waypoint", "tst":"xx", "lat":"<current>", "lon":<current>", "text":"utf-8 text" }`</del>

#### Remote control

* Maybe add remote-control for enabling "move-mode" on iOS (https://github.com/binarybucks/mqttitude/issues/139)
* Interesting idea in [#227](https://github.com/binarybucks/mqttitude/issues/227): device can change mode (e.g. _silent_, _airplane mode_, etc.) depending on location and by listening to a remote-control topic with which it is instructed to switch modes.
* Add remote-control for setting waypoints via MQTT

