## Clients

Using our OwnTracks app is cool, but what do you do with the location data the apps send (i.e. _publish_) to the MQTT broker? We have some suggestions.

You know that OwnTracks _publishes_ location information to an MQTT or HTTP endpoint. The data it publishes is transferred in a particular format called [JSON](http://json.org), and [this is what it looks like](../tech/json.md).

When you've got your endpoint set up, and OwnTracks configured to use it, you'll want to do something useful with the data, and the following sections introduce you to some of the existing clients you can utilize for doing that.

## mosquitto_sub

_mosquitto_sub_ is a very basic command-line client provided by the [Mosquitto project](http://mosquitto.org). However, it is brilliantly useful, and you can also use it for long-time collection of data by having it run in the background with _stdout_ pointing to a file.

In it's most basic invocation, you have the program connect to your broker and subscribe to the topics you're interested in.

```bash
mosquitto_sub -h localhost -p 1883 -v -t 'owntracks/#'
```

_mosquitto_sub_ and it's counterpart, _mosquitto_pub_ for publishing to a broker, support a slew of options. It'll be worth your while to study their [manual page](http://mosquitto.org/man/mosquitto_sub-1.html).

## Recorder

We recommend our very own [OwnTracks Recorder](https://github.com/owntracks/recorder) because it's lightweight, relatively simple to set up, and because it offers a lot of features. It stores location data published by the apps by subscribing to an MQTT broker, and it enables you to access that data in a variety of formats. The best part is: it doesn't require a heavy external database of any kind.

![Recorder](images/demo-geojson-linestring.png)

And the best is, you can configure the Recorder to also accept the HTTP POST requests from the apps, so it's the best companion for the OwnTracks apps in HTTP mode as well.

Read [more about the Recorder](../clients/recorder.md).

## mqttwarn

You'll recall (we hope) the discussion on [MQTT](mqtt.md) and the analogy with the cauldron. _mqttwarn_, while not specific to OwnTracks, is another client you connect to your [MQTT broker](broker.md). We think it's worth mentioning (and both Ben and JP -- the creators -- are quite pleased with what it does).

_mqttwarn_ lets you do things like tweet everytime OwnTracks reports a location (but do consider whether you *really* want that), send an e-mail when a loved one enters a particular region, etc.


## openHAB

Presence detection in home automation is one of the most important pieces of the puzzle. Without it your smart home is effectively blind, and as a result can hardly be called 'smart' at all. There are very few automation rules that do not require some form of context in terms of human occupancy. Rules around security, lighting, music, even coffee machines -- all need to know if 'someone' is home before deciding whether to take action. 

![openHAB](images/openhab.png)

This is where OwnTracks steps in. In its simplest form it can be configured with waypoints (for home/work etc) that generate events whenever your phone enters/leaves these regions. These are sent to your own personal [MQTT broker](broker.md) where your home automation software can react accordingly. The beauty of OwnTracks is two-fold - it is simple, you can disable the standard location reporting so all you get is the waypoint event/leave events, which is all your smart home cares about. The second is that, in [_private mode_](scenarios.md), all your data is 'yours' and yours alone - there is no cloud service collecting, storing and potentially mining your data. 

Read more about [openHAB and OwnTracks](https://github.com/openhab/openhab/wiki/Mqttitude-Binding).


## Home Assistant

Another home automation solution that can be used with OwnTracks is Home Assistant. Home Assistant allows you to track the location of people connected to your personal [MQTT broker](broker.md). It has a map to show their current location and can trigger automations based on entering and leaving zones. In order to use the internal broker of Home Assistant, the MQTT protocol level has to be set to 4. 

See the [demo][ha-demo] for an example of the map with OwnTracks devices or read more about how Home Assistant [connects to your private MQTT broker][ha-mqtt], how to [enable OwnTracks support in Home Assistant][ha-owntracks] and how to [configure automations based on zones][ha-zone].

[ha-demo]: https://home-assistant.io/demo/
[ha-mqtt]: https://home-assistant.io/components/mqtt/
[ha-owntracks]: https://home-assistant.io/components/device_tracker.owntracks/
[ha-zone]: https://home-assistant.io/components/automation/#zone-trigger


## OwnTracks-Cards
This is a webapp to create and edit [OwnTracks cards](.../features/card.md).
It can be either used to just create the JSON representation of the card, which has to be published to the MQTT broker manually. Or it can directly publish the card to an MQTT broker connected via websockets.
Just head over to the [demo][oc-demo] and create a card. Configure your MQTT broker by clicking the connection state.
The source code can be found on [Github][oc-code].

[oc-demo]: https://avanc.github.io/owntracks-cards/
[oc-code]: https://github.com/avanc/owntracks-cards


## Roll your own

If you feel really brave, you can [write your own program](../tech/program.md) which subscribes to OwnTracks data, but don't forget to tell us about it!

## Related

Here are some projects we know of which use and/or integrate OwnTracks and/or
which are useful when using MQTT in general or OwnTracks in particular.

* [MQTT Inspector for iOS](http://jpmens.net/2013/11/19/mqtt-inspector-for-ios/), written by Christoph Krey, who also did our OwnTracks for iOS app. This is an indispensable utility for all things MQTT.
* [the thing system](http://thethingsystem.com) ( [code](https://github.com/TheThingSystem/steward) )
* A Clojure webapp that aims to manage information gathered from OwnTracks; [clojure-mqttitude-backend](https://github.com/razorinc/clojure-mqttitude-backend)
* An [OwnTracks flow](http://flows.nodered.org/flow/ab31cd939f2e73503fb0) by [Giovanni @juzam Angoli](https://twitter.com/juzam) for Node-RED which publishes messages to [pushover.net](https://pushover.net) upon entering or leaving a geo-fence.
* [MyMQTT](https://play.google.com/store/apps/details?id=at.tripwire.mqtt.client) is an app for Android which enables you to subscribe and publish to an MQTT broker.
* Matthew Bordignon made a small [Web page which uses Websockets to display the barometer reading](https://github.com/matbor/Owntracks-Barometer) published by OwnTracks for iOS. ([Screenshot](https://twitter.com/OwnTracks/status/623823420053172224).)
* [PHP front & backend for OwnTracks payloads](https://github.com/tomyvi/php-owntracks-recorder)
* Orion, an alternative to OwnTracks Recorder with powerful visualization features ([server](https://github.com/LINKIWI/orion-server), [web client](https://github.com/LINKIWI/orion-web), [demo](https://linkiwi.github.io/orion-web))
* [OwntracksOSM](https://github.com/wansti/OwntracksOSM) is an Owntracks and OpenStreetMap based mapping and location sharing app for Samsung smartwatches ([screenshots](https://github.com/owntracks/talk/issues/80)).
