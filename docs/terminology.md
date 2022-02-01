# Terminology

Some of the terms we use can be a little confusing at times, so here's a list of terms you should be familiar with.

#### MQTT

MQTT is a machine-to-machine (M2M)/"Internet of Things" connectivity protocol. It was designed as an extremely lightweight publish/subscribe messaging transport. It is useful for connections with remote locations where a small code footprint is required and/or network bandwidth is at a premium. For example, it has been used in sensors communicating to a broker via satellite link, over occasional dial-up connections with healthcare providers, and in a range of home automation and small device scenarios. It is also ideal for mobile applications because of its small size, low power usage, minimised data packets, and efficient distribution of information to one or many receivers

And what does OwnTracks have to do with all of this? Well, the OwnTracks apps are MQTT clients which publish messages (your location) and subscribe to and consume messages (e.g. the locations of [your friends](features/friends.md)). OwnTracks' location updates land in the cauldron when the app publishes a location message from which it can be consumed by any number of subscribers to the same [MQTT broker](guide/broker.md).

There are [a number of clients](guide/clients.md) which do that already, and we're even going to show you how you can [create your own client](tech/program.md) for specific purposes.


#### Pub/Sub

In MQTT-speak, clients publish messages, which means they send or transmit them, and other clients subscribe to messages, meaning they receive them. Messages are published on topics, and the MQTT server, which is called a broker is configured to permit or deny clients doing so.

#### broker

A MQTT _broker_ is a server that enables the communication between clients. The broker is responsible for distributing messages to interested clients based on the topic of a message. 

#### publish

The act of sending a message to a broker. 

#### topic
Each message that is published by a client is send to a specific topic on the broker. Clients can opt to receive only certain or all messages depending on which topic they subscribe to. 

#### subscribe

Clients can specify to receive messages that are published to specific topics by subscribing to them. A client that is subscribed to the topic `owntracks/jane/iphone` will, for example, not receive a message that is published to `owntracks/john/nexus`.

#### QoS

_QoS_ or _Quality of Service_, specifies how the app should attempt to publish messages to an MQTT broker.

* QoS=0. The message is delivered at most once, or it is not delivered at all. Its delivery across the network is not acknowledged.
* QoS=1. The message is always delivered at least once. If the sender does not receive an acknowledgment, the message is sent again with the DUP flag set until an acknowledgment is received. As a result receiver can be sent the same message multiple times, and might process it multiple times.
* QoS=2. The message is always delivered exactly once. This is the safest but slowest method of transfer.

#### Retain

When a message is published to a _broker_ with the _durable_ or _retain_ flat
set, it means that the MQTT broker will attempt to store the last published
message on a particular topic.

A client which subscribes to that topic will receive that last retained message once it
connects to the broker.

#### Geocoding

_Reverse geocoding_ is the act of looking up the address for a pair of coordinates.

#### Geofence or Region

A _geofence_ or a _region_ is an area around a particular coordinate. Geofences will trigger certain actions once the device enters or leaves the setup area. 
