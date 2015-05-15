# Friends

OwnTracks has a so-called _Friends_ feature which allows friends and/or family members who connect to the same [MQTT broker](broker.md) to see eachother on the map and receive location updates from these friends. This is a really nice feature which allows, say, all members of a family to see where another member is currently located. Even when on different brokers, users can configure their systems to enable location updates to pass from one broker to another by a [process called bridging](bridge.md).

Let's assume for a moment, that a user Jane (user name `jjolie`) has an iPhone 4s (device name `4s`), and that she publishes location data to a topic at


The nitty-gritty is documented in the [Friends feature](../features/friends.md).
