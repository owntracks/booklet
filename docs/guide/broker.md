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

Ideally, you set up a private broker under your control. This sounds more difficult
than it actually is, and there are some very nice brokers you can use free of charge
on your own infrastructure. As an example, we've written up how to install
Mosquitto on a Raspberry Pi.

## RasPi

![Mosquitto on a Raspbi](images/raspi-broker.png)

The hardest bit is installing an OS, say, [Raspbian Wheezy][2], onto an SD card, but there are many tutorials on how to do that. (Here's an example [using Mac OS X][1].) A basic install will suffice, and after logging in with Raspbian's default username and password, we'll get started from there.

Roger Light, [Mosquitto]'s creator has thankfully (!) set up a few [Mosquitto repositories][6] we can use to obtain the latest and greatest version, so we'll do just that. We first perform the required steps to add and activate the repository. The last step in particular can take a few moments.

```bash
curl -O http://repo.mosquitto.org/debian/mosquitto-repo.gpg.key
sudo apt-key add mosquitto-repo.gpg.key
rm mosquitto-repo.gpg.key
cd /etc/apt/sources.list.d/
sudo curl -O http://repo.mosquitto.org/debian/mosquitto-repo.list
sudo apt-get update
```

Now we can go ahead and install [Mosquitto] proper. There are three packages:

* `mosquitto` is the MQTT broker (i.e. server)
* `mosquitto-clients` are the command-line clients, which I recommend you install
* Don't install `python-mosquitto`; if you want to do programming with Python and MQTT, we [show you how to do so with the Paho Python module](../tech/program.md).

These packages together require about 665Kb of space, which we can easily afford even on the tiny Pi.

```bash
sudo apt-get install mosquitto mosquitto-clients
```

Regrettably, as with most Debian packages, the broker is immediately started; stop it.

```bash
sudo /etc/init.d/mosquitto stop
```

That concludes the installation of the [Mosquitto] MQTT broker, and we'll now proceed to
its configuration. This section is geared towards a configuration of [Mosquitto] which will
work well with OwnTracks. In particular we want the following features enabled
by default:

* Connections to the broker must be [TLS] protected. This requires a TLS certificate and key which will be configured automatically.
* ACLs will restrict who may access what.

Over at the [OwnTracks repository][7], we've got some utilities which are going to automate this. It's a work-in-progress (of course), but this is what `sudo ./mosquitto-setup.sh` looks like at the moment:

```bash
Saving previous configuration as mosquitto.conf-20130901-133525
Generating a 2048 bit RSA private key
.................................................................................................+++
...............................+++
writing new private key to '/etc/mosquitto/ca.key'
-----
Created CA certificate in /etc/mosquitto/ca.crt
subject=
    commonName                = An MQTT broker
    organizationName          = MQTTitude.org
    emailAddress              = nobody@example.net
--- Creating server key and signing request
Generating RSA private key, 2048 bit long modulus
............+++
..............+++
e is 65537 (0x10001)
--- Creating and signing server certificate
Signature ok
subject=/CN=raspberrypi/O=MQTTitude.org/emailAddress=nobody@example.net
Getting CA Private Key
```

A CA is created together with a server key-pair with a whole bunch of `subjAltName`
settings:

```
    X509v3 Subject Alternative Name:
        IP Address:192.168.1.189, IP Address:127.0.0.1, IP Address:0:0:0:0:0:0:0:1, DNS:broker.example.com, DNS:foo.example.de, DNS:localhost
```

Will it work? Let's start the broker manually to see what it says:

```bash
sudo /usr/sbin/mosquitto -c /etc/mosquitto/mosquitto.conf
1378042632: mosquitto version 1.2 (build date 2013-08-09 21:49:03+0100) starting
1378042632: Config loaded from /etc/mosquitto/mosquitto.conf.
1378042632: Opening ipv4 listen socket on port 1883.
1378042632: Opening ipv4 listen socket on port 8883.
1378042632: Opening ipv6 listen socket on port 8883.
1378042632: Warning: Address family not supported by protocol
...^C
1378042634: mosquitto version 1.2 terminating
1378042634: Saving in-memory database to /tmp/mosquitto.db.
```

The Mosquitto clients need to have access to a copy of the CA certificate (`ca.crt`) and you can transport that insecurely to your clients (it's a public certificate).

```bash
mosquitto_pub  --cafile ca.crt -h 127.0.0.1 -p 8883  ...
```

That's it for the moment. 

  [1]: http://aryo.lecture.ub.ac.id/install-raspbian-wheezy-on-raspberry-pi-sd-card-using-macos-x/
  [2]: http://www.raspberrypi.org/downloads
  [7]: https://github.com/owntracks/tools

## Testing

Once you've chosen an MQTT broker, make sure you feel comfortable with the
utilities it provides to subscribe and publish to topics. We recommend the
[Mosquitto] utilities for doing so.

For example, to subscribe to all topics prefixed by `owntracks` on your broker:

```
mosquitto_sub -h hostname -p 1883 -v -t 'owntracks/#'
```

(Note that the hash symbol has to be quoted in the shell which is why we've put
the whole topic branch in single quotes.)

In another screen you could publish a test message:

```
mosquitto_pub -h hostname -p 1883 -t 'owntracks/test' -m 'hello world'
```

and in the first screen you'd see the topic name followed by a space and the message
_payload_.

Once you feel comfortable with what is going on, you should consider adding [TLS].

## Bridging

If you want to connect two (or more) brokers (e.g. yours and that of your friend) you can, and we've written up [how you can bridge brokers](bridge.md).

## Logging

Before doing anything else, please consult the manual to determine where your Mosquitto logs are being written to. It's hard stabbing around in the dark when a glance at a log file can give you valuable tips on what is actually happening.

Mosquitto typically logs via _syslog_, and _syslog_'s configuration defines where the log messages are actually written to. Your _syslog_ may be called _syslog_, _rsyslog_, _syslog-ng_, or anything else for that matter. In case of doubt, check the files in `/var/log`; one of them _ought_ to have what you're looking for (e..g `messages`, `syslog`, `localmessages`, or even `debugmessages`).

Mosquitto typically logs each connection request, a publish, a subscribe request, etc. (Read the [manpage for `mosquitto.conf`](http://mosquitto.org/man/mosquitto-conf-5.html) to learn how to configure logging.

A successful publish of an OwnTracks location could look somewhat like this:

```
mosquitto[1366]: Received PUBLISH from jane-5s-m-o (d0, q2, r1, m7, 'owntracks/jane/5s', ... (159 bytes))
mosquitto[1366]: Sending PUBREC to jane-5s-m-o (Mid: 7)
mosquitto[1366]: Received PUBREL from jane-5s-m-o (Mid: 7)
mosquitto[1366]: Sending PUBCOMP to jane-5s-m-o (Mid: 7)
```

## ACLs

You will definitely want to set up Access Control Lists (ACLs) on your broker so that you can control who may see what. As an example, suppose Jane (username `jjolie`) should be able to publish to her [OwnTracks MQTT topics](topics.md) and Fred (username `fred`) should be allowed to see Jane's location, we could configure something like this:

```
user jjolie
topic owntracks/jjolie/#

user fred
topic read owntracks/jjolie/5s
topic owntracks/fred/nexus/#
```

## Firewall

If you want to run your _Private_ broker it's possibly going to be at home under your desk (or is it in your small office?). Be that as it may, how does an OwnTracks app reach (network-wise) that broker?  Chances are you have some form of router which connects the local network in your home to the Internet.

OwnTracks runs on the device which is in your pocket or your purse, or wherever you placed it, and it must be able to connect to your MQTT broker, but it cannot: your router hopefully has a firewall configured on it which will allow outgoing (from your home outwards) connections, but it is sure to not allow incoming connections. We must change that, at least for MQTT, and we're going to assume you've configured TLS, i.e. your broker is (also) listening on TCP port 8883

What you need to do to get this working is to reconfigure your router to allow incoming TCP traffic on port 8883 and to hand that off to the TCP/IP address of your MQTT broker. The exact details on how to do that vary from vendor to vendor, but a close look at the documentation of your router should show you how to do that.

What is also likely, or at least possible, is that your home doesn't have a fixed TCP/IP address, but one which changes periodically. The OwnTracks apps won't be able to find your home then, will they? If you keep "moving" (i.e changing addresses).

A service which is typically (if maybe incorrectly) called dynamic DNS comes to the rescue. These services allow you to configure a DNS name (e.g. `freds-router.example.org`) which points to the changing IP address of your router.

Once you've completed those steps, configure the OwnTracks apps to use the shiny new DNS name and TCP port number (8883) and you should be all set.

## OwnTracks

So, you've configured your broker and you are familiar with the _mosquitto_ command-line
clients, so now it's time to see if you can [get OwnTracks to speak to your broker](apps.md).


  [MQTT]: http://mqtt.org
  [1]: http://mqtt.org/wiki/doku.php/public_brokers
  [2]: http://jpmens.net/2013/09/01/installing-mosquitto-on-a-raspberry-pi/
  [3]: http://jpmens.net/2013/08/14/latitude-longitude-mqttitude/
  [mosquitto]: http://mosquitto.org
  [4]: https://github.com/binarybucks/mqttitude/blob/master/docs/Starting/iphone/Getting-Started.md
  [TLS]: http://en.wikipedia.org/wiki/Transport_Layer_Security
  [6]: http://mosquitto.org/download/

