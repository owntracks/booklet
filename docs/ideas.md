# Ideas

Over the course of time, we've had people propose a number of ideas for OwnTracks. Some of these we thought excellent, others we thought would maybe sometime be nice to have, and others, well, see for yourself. Be that as it may, this page lists a few of these in the hope that you'll see we know of them and if you dream up something new, that you'll first check wether we've heard of it already.

* Allow KML, GPX export. This we do enable in the back-end.
* Publish _charging_ alongside battery level. No.
* Add temperature to the JSON payload. No.
* Have the device track a route; we do this in the back-end.


## Short term

* Stability
* <del>Reliability [#38](https://github.com/binarybucks/mqttitude/issues/38)</del>
* <del>Battery consumption [#68](https://github.com/binarybucks/mqttitude/issues/68)</del>
* <del>Ensure UI has `Credits` with URL to Web site [#41](https://github.com/binarybucks/mqttitude/issues/41)</del>
* Disable all location services (unload app) [#74](https://github.com/binarybucks/mqttitude/issues/74)
* <del>LWT [#55](https://github.com/binarybucks/mqttitude/issues/55)</del>

## Mid-term

* <del>Add "traffic light" [#73](https://github.com/binarybucks/mqttitude/issues/73)</del>
* <del>Add live "info" pane to Apps [#47](https://github.com/binarybucks/mqttitude/issues/47)</del>
* Remote-control [#71](https://github.com/binarybucks/mqttitude/issues/71)
* <del>Annotations.</del>
  * <del>Click on pin</del>
  * <del>Enter text string `"Restaurante La Comida; wonderful gambas al ajillo"`</del>
  * <del>PUBlish with full `_location` and additional `"note" : "...."` (see _waypoints_ below)</del>
* 2013-11-22: @mrose has some interesting things to say about Messages on iOS [#199](https://github.com/binarybucks/mqttitude/issues/199). In particular, screen real-estate should be better organized.


## Long-term

* <del>Add presence. Are my friends in the area?</del>
  * <del>Needs friends/family on same broker</del>
  * <del>Needs 'standardized' topic names (maybe with Twitter id in topic?)</del>
* Queue updates on device (with `tst` etc) to be PUBlished upon available connection
* Corner-pegging [#94](https://github.com/binarybucks/mqttitude/issues/94)

## Very-long term, a.k.a. "Neat ideas"

* Publish incoming phone call (caller-id), [submitted by @bordingnon](http://twitter.com/bordignon/status/372627079059079168). JPM: Also SMS? Have to force TLS then, at least.
* Requested in #86: "app should register a subscriprion (configurable topic) a) if someone sends a text message it should be displayed as popup window b) if someone sends an HTML message it should be opened in a embedded browser"
* Generalized messaging app (see [#205](https://github.com/binarybucks/mqttitude/issues/205) )
* Find my friends [#290](https://github.com/binarybucks/mqttitude/blob/master/docs/FUTURE.md)
* Indoors edition with iBeacons as requested in [#296](https://github.com/binarybucks/mqttitude/issues/296)

#### Waypoints

<del>JPM added 2013-11-22: We currently have "Annotations" on iOS (see above). I'd like to be able to have an annotation PUBlished to the broker so that m2s (or other processors) can pick it up. I propose the following payload: `{ "_type": "_waypoint", "tst":"xx", "lat":"<current>", "lon":<current>", "text":"utf-8 text" }`</del>

#### Remote control

* Maybe add remote-control for enabling "move-mode" on iOS (https://github.com/binarybucks/mqttitude/issues/139)
* Interesting idea in [#227](https://github.com/binarybucks/mqttitude/issues/227): device can change mode (e.g. _silent_, _airplane mode_, etc.) depending on location and by listening to a remote-control topic with which it is instructed to switch modes.

