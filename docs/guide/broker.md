## MQTT broker

An MQTT broker is a service to which MQTT clients connect. These clients publish
data to specific _topics_ and they can subscribe to one or more _topics_ to receive
messages. A _topic_ is like an "address" for a particular message. For example,
a topic for a device that publishes a temperature reading of your living room
may be `temperature/indoors/living`, whereas a device which publishes weather
data could do so to `weather/germany/frankfurt`. In the particular case of OwnTracks, we
use a topic branch called `owntracks/username/device`, but you can override that
name if you prefer to. The reason we've chosen that structure is to accomodate
friends and family on a single broker, taking into consideration that a particular
user might have more than one device.

## Private broker

You set up a private broker under your control. This sounds more difficult
than it actually is, and there are some very nice brokers you can use free of charge
on your own infrastructure. As an example, we've written up how to install
Mosquitto on a Raspberry Pi.

## RasPi

![Mosquitto on a Raspbi](images/raspi-broker.png)

The hardest bit is installing an OS, say, [Raspbian Wheezy][2], onto an SD card, but there are many tutorials on how to do that. A basic install will suffice, and after logging in with Raspbian's default username and password, we're going to suggest you use our [quicksetup](quicksetup.md) to configure most of what you need automatically.


  [1]: http://aryo.lecture.ub.ac.id/install-raspbian-wheezy-on-raspberry-pi-sd-card-using-macos-x/
  [2]: http://www.raspberrypi.org/downloads
  [7]: https://github.com/owntracks/tools


## Bridging

If you want to connect two (or more) brokers (e.g. yours and that of your friend) you can, and we've written up [how you can bridge brokers](bridge.md).

## Logging

Before doing anything else, please consult the manual to determine where your Mosquitto logs are being written to. It's hard stabbing around in the dark when a glance at a log file can give you valuable tips on what is actually happening.

Mosquitto typically logs via _syslog_, and _syslog_'s configuration defines where the log messages are actually written to. Your _syslog_ may be called _syslog_, _rsyslog_, _syslog-ng_, or anything else for that matter. In case of doubt, check the files in `/var/log`; one of them _ought_ to have what you're looking for (e..g `messages`, `syslog`, `localmessages`, or even `debugmessages`), very likely `/var/log/mosquitto/mosquitto.log`.

Mosquitto typically logs each connection request, a publish, a subscribe request, etc.

A successful publish of an OwnTracks location could look somewhat like this:

```
mosquitto[1366]: Received PUBLISH from jane-5s-m-o (d0, q2, r1, m7, 'owntracks/jane/5s', ... (159 bytes))
mosquitto[1366]: Sending PUBREC to jane-5s-m-o (Mid: 7)
mosquitto[1366]: Received PUBREL from jane-5s-m-o (Mid: 7)
mosquitto[1366]: Sending PUBCOMP to jane-5s-m-o (Mid: 7)
```

## OwnTracks

So, you've configured your broker and you are familiar with the _mosquitto_ command-line
clients, so now it's time to see if you can [get OwnTracks to speak to your broker](apps.md).


  [MQTT]: http://mqtt.org
  [1]: http://mqtt.org/wiki/doku.php/public_brokers
  [2]: http://jpmens.net/2013/09/01/installing-mosquitto-on-a-raspberry-pi/
  [3]: http://jpmens.net/2013/08/14/latitude-longitude-mqttitude/
  [mosquitto]: http://mosquitto.org

