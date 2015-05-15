# MQTT

So what's this MQTT thing? Quoting directly [from the horse's mouth](http://mqtt.org):

> MQTT is a machine-to-machine (M2M)/"Internet of Things" connectivity protocol. It was designed as an extremely lightweight publish/subscribe messaging transport. It is useful for connections with remote locations where a small code footprint is required and/or network bandwidth is at a premium. For example, it has been used in sensors communicating to a broker via satellite link, over occasional dial-up connections with healthcare providers, and in a range of home automation and small device scenarios. It is also ideal for mobile applications because of its small size, low power usage, minimised data packets, and efficient distribution of information to one or many receivers

We've tried, but we couldn't have phrased this better.

It's lightweight. It's easy on a battery. It has a low overhead (important to save on your phone's data plan). And it distributes information to one or more receivers.

## Pub/Sub

In MQTT-speak, clients publish messages, which means they send or transmit them, and other clients subscribe to messages, meaning they receive them. Messages are published on [topics](topics.md), and the MQTT server, which is called a [broker](broker.md) is configured to permit or deny clients doing so.

### Cauldron

We like to imagine that MQTT makes something reminiscent of a large _cauldron_ available to publishing and subscribing clients. Publishers drop messages which are tagged [with topic names](topics.md) into the pot, and subscribers can, access-control permitting, slurp these out, store them, create new messages and ladle them back into the pot or simply throw them away after peeking at them. As a small example, assume a client periodically publishes a Celsius temperature measurement to a particular topic, but you want Fahrenheit (incomprehensible that you would, but anyway). You could [create a small client](../tech/program.md) which subscribed to the Celsius reading (from the topic, say, `temp/celsius` in the cauldron), converted to Fahrenheit or Kelvin or whatever, and republished the new value to a different topic, say, `temp/fahrenheit` back into the cauldron. It's important to understand that subscribing and receiving an MQTT message does _not remove_ it from the broker; the same message is available to all clients which are subscribed at the time the message was published. (So you could have one client doing Fahrenheit conversion and another doing Kelvin.)

And what does OwnTracks have to do with all of this? Well, the OwnTracks apps are MQTT clients which publish messages (your location) and subscribe to and consume messages (e.g. the locations of [your friends](friends.md)). OwnTracks' location updates land in the cauldron when the app publishes a location message from which it can be consumed by any number of subscribers to the same [MQTT broker](broker.md).

There are [a number of clients](clients.md) which do that already, and we're even going to show you how you can [create your own client](../tech/program.md) for specific purposes.

