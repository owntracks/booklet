# Clients

Using our OwnTracks app is cool, but what do you do with the location data the apps send (i.e. _publish_) to the MQTT broker? We have some suggestions. (Do remember however, that you cannot do this in [_public mode_](scenarios.md).)

You know that OwnTracks _publishes_ location information to an MQTT _broker_. The data it publishes is transferred in a particular format called [JSON](http://json.org), and [this is what it looks like](../tech/json.md).

When you've got your broker set up, and OwnTracks configured to use it, you'll want to do something useful with the data, and the following sections introduce you to some of the existing clients you can utilitze for doing that.

## mosquitto_sub

_mosquitto_sub_ is a very basic command-line client provided by the [Mosquitto project](http://mosquitto.org). However, it is brilliantly useful, and you can also use it for long-time collection of data by having it run in the background with _stdout_ pointing to a file.

In it's most basic invocation, you have the program connect to your broker and subscribe to the topics you're interested in.

```bash
mosquitto_sub -h localhost -p 1883 -v -t 'owntracks/#'
```

_mosquitto_sub_ and it's counterpart, _mosquitto_pub_ for publishing to a broker, support a slew of options. It'll be worth your while to study their [manual page](http://mosquitto.org/man/mosquitto_sub-1.html).


## o2s

One of the clients we provide as part of the Open Source OwnTracks project is called _o2s_; the strange name stands for _OwnTracks to Storage_, and we think you're going to want to have this connected to your [MQTT broker](broker.md), particularly in [Private mode](scenarios.md).

Correctly configured (and it's not trivial), _o2s_ connects to your broker and subscribes to location messages and other [OwnTracks payloads](../tech/json.md). Upon receiving a location update, _o2s_ will do a number of things:

* Perform a reverse-geo lookup (using your choice of either Google or XXX to do so) for the geographical coordinates received from the OwnTracks app
* Store the message and its details into a database (MySQL and PostgreSQL are supported)
* Alert via (future) plugins that a geographical region ([waypoint](waypoints.md)) has been entered or left

The result of using _o2s_ is that you get a database table (several actually) in which your OwnTracks locations are stored.

```
+---------------------+-----+------------+------------+------+--------------------------------------+
| tst                 | tid | lat        | lon        | cc   | addr                                 |
+---------------------+-----+------------+------------+------+--------------------------------------+
| 2015-05-27 06:23:36 | jJ  | 46.7835540 | 17.2073088 | HU   | Keszthely, Hévízi út, 8360 Ungarn    |
| 2015-05-26 20:10:52 | jJ  | 46.7710135 | 17.1829915 | HU   | Alsópáhok, 760, 8394 Ungarn          |
| 2015-05-26 14:32:50 | jJ  | 46.9557636 | 19.4132377 | HU   | Kerekegyháza, 5211, 6041 Ungarn      |
| 2015-05-23 22:42:27 | jJ  | 47.4986630 | 19.0438124 | HU   | Budapest, Eötvös tér, 1051 Ungarn    |
+---------------------+-----+------------+------------+------+--------------------------------------+
```

So, now that you have the OwnTracks locations being neatly stored for you, how do you visualize them on, say, a map, at a later point in time? Enter _Pista_.



## Pista

_Pista_ is a word which signifies _track_ in Spanish and in Italian. It is, unfortunately, a very difficult bit of software to get installed, but we'll try to help you. _Pista_ contains a whole kitchen-sink of features, most of which you won't need to use with our OwnTracks apps. (These features were built for the OwnTracks Greenwich devices.)

We'll concentrate on setting up _Pista_'s _Tracks_ utility which will enable you to view past locations directly from the database tables which are updated by _o2s_.

## mqttwarn

You'll recall (we hope) the discussion on [MQTT](mqtt.md) and the analogy with the cauldron. _mqttwarn_, while not specific to OwnTracks, is another client you connect to your [MQTT broker](broker.md). We think it's worth mentioning (and both Ben and JP -- the creators -- are quite pleased with what it does).

_mqttwarn_ lets you do things like tweet everytime OwnTracks reports a location (but do consider whether you *really* want that), send an e-mail when a loved one enters a particular region, etc.


## openHAB

Presence detection in home automation is one of the most important pieces of the puzzle. Without it your smart home is effectively blind, and as a result can hardly be called 'smart' at all. There are very few automation rules that do not require some form of context in terms of human occupancy. Rules around security, lighting, music, even coffee machines -- all need to know if 'someone' is home before deciding whether to take action. 

This is where OwnTracks steps in. In its simplest form it can be configured with waypoints (for home/work etc) that generate events whenever your phone enters/leaves these regions. These are sent to your own personal [MQTT broker](broker.md) where your home automation software can react accordingly. The beauty of OwnTracks is two-fold - it is simple, you can disable the standard location reporting so all you get is the waypoint event/leave events, which is all your smart home cares about. The second is that, in [_private mode_](scenarios.md), all your data is 'yours' and yours alone - there is no cloud service collecting, storing and potentially mining your data. 

Read more about [openHAB and OwnTracks](https://github.com/openhab/openhab/wiki/Mqttitude-Binding).

## Roll your own

If you feel really brave, you can [write your own program](../tech/program.md) which subscribes to OwnTracks data, but don't forget to tell us about it!

## Related

Here are some projects we know of which use and/or integrate OwnTracks and/or
which are useful when using MQTT in general or OwnTracks in particular.

* [MQTT Inspector for iOS](http://jpmens.net/2013/11/19/mqtt-inspector-for-ios/), written by Christoph Krey, who also did our OwnTracks for iOS app. This is an indispensible utility for all things MQTT.
* [the thing system](http://thethingsystem.com) ( [code](https://github.com/TheThingSystem/steward) )
* A Clojure webapp that aims to manage information gathered from OwnTracks; [clojure-mqttitude-backend](https://github.com/razorinc/clojure-mqttitude-backend)
* An [OwnTracks flow](http://flows.nodered.org/flow/ab31cd939f2e73503fb0) by [Giovanni @juzam Angoli](https://twitter.com/juzam) for Node-RED which publishes messages to [pushover.net](https://pushover.net) upon entering or leaving a geo-fence.
* [MyMQTT](https://play.google.com/store/apps/details?id=at.tripwire.mqtt.client) is an app for Android which enables you to subscribe and publish to an MQTT broker.
* Stefano Costa cooks Italian pasta [using OwnTracks](https://github.com/bluewindthings/butta-la-pasta).


