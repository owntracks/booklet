## Scenarios

There are two basic scenarios we have for you depending on your needs. It'll help if you keep a copy of the [technical terminology](../terminology.md) handy.

There is a bit of stuff you have to learn about in order to use OwnTracks: [MQTT brokers](broker.md), [topics](topics.md), but the upside is that you get to use many of the neat features _MQTT_ mode or _HTTP_ mode come with. For example, you can set up [friends](../features/friends.md).

## HTTP mode

If you don't want to go to the trouble of hosting your own MQTT setup, you can use _HTTP_ mode to publish location data to a [HTTP server of your chosing](../tech/http.md), and you can also use our Recorder for doing so.

## MQTT mode

In MQTT mode you [set up and configure your MQTT broker](broker.md), you configure authentication, and then you configure your OwnTracks apps to connect to your broker. It's not particularly difficult to do, and you have full control over everything you set up. We've said it before: [using quicksetup](quicksetup.md) is likely the easiest way to accomplish this.

What you basically have to do is to

* [set up an MQTT broker](broker.md)
* Optionally [use existing clients](clients.md) to consume OwnTracks data
* Possibly dig in and [write your own client](../tech/program.md) and do really cool stuff!

We urge you to resist the temptation of using _MQTT mode_ with one of the many public brokers hosted by different organizations. While it _is_ an easy way out those brokers are publically accessible which means anybody can see where you are if they can identify you. Furthermore, public brokers often don't implement access control, so basically anybody can create a "location" update which looks as though it may be yours. Believe us: it'll drive you crazy.

You will now want to [configure a client](clients.md) with which you consume the data which is published by OwnTracks, or if you're feeling very brave, why not [write your own client](../tech/program.md)?

## MQTT vs. HTTP

Which should it be? We invented OwnTracks backed by MQTT so we have preference for that, and using MQTT with, say, our Recorder is easier than with HTTP.

- implementing support for Friends is easier with MQTT as the apps do this themselves. In HTTP mode, the backend (Recorder) needs to know about Friends and send the information back to the apps. The software has support for this, but configuration is tedious.

- MQTT connections are more lightweight than HTTP, or rather the overhead is lower.

- for both MQTT and HTTP, connections are re-established when required.


### on iOS

In the iOS client there is no functional difference between MQTT and HTTP.  The main difference is the backend. MQTT brokers queue messages from devices to other devices (_transition events_ especially). In iOS, the track of friends is recorded, which obviously only makes sense if locations sent by other devices are queued by the backend.

HTTP works with friends only if the backend takes responsibility to retrieve friend's positions when a new location arrives. (Our recorder does, _Home Assistant_ seems to do it partially, and what other systems do we likely don't know.)

Re-transmission of off-line locations from an MQTT client is much more efficient and is faster than with HTTP.

In iOS MQTT connections are deliberately shut down when the app goes into background.  On the other hand there is no permanent HTTP connection, so HTTP connections are established when a new location/event is POSTed.




Now it's time to [discuss topics](topics.md).
