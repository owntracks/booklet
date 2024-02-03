## TLS

You want data between your mobile device (a.k.a. smartphone) and the MQTT broker you use to be secured from eavesdropping. This is accomplished using _TLS_. When you configure your broker you will generate what is called a CA certificate which is basically a large amount of bits. (See below for the nitty gritty.)

Configuring TLS ([Transport Layer Security](http://en.wikipedia.org/wiki/Transport_Layer_Security)) for OwnTracks involves doing three things:

* Obtain a TLS certificate/key pair (see below on how to do this) if you don't yet have one
* Configure TLS on the MQTT broker
* Configuring the app to use TLS

You can share the certificate if you want to (though not many people will want it ...)
It's so public, that it's perfectly OK to send yourself the file by, say, e-mail.

## Configure TLS on the Mosquitto broker

We'll first show you how to set up TLS on your Mosquitto [broker](../guide/broker.md).
The basic configuration for TLS in [mosquitto.conf][mconf] is:

```
listener 8883
cafile .............
certfile .............
keyfile .............
```

(see also: [mosquitto-tls][mtls].)

* `8883` is the standard MQTT port for TLS connections. Change it if you need to, but make sure your OwnTracks app is configured to use the same port.
* `cafile` is the path to the certificate authority file
* `certfile` points to your server's certificate in PEM format
* `keyfile` points to your server's secret key file (which you *never* divulge!)


## Configure the OwnTracks app to use TLS

We show you here how to set up your iOS or Android device with that kind of certificate.

In both cases we assume you've got an e-mail in your inbox with the file you've sent yourself. This file has a `.crt` extension, and in both Android and iOS you can simply launch configuration by clicking on the attachment.

In our example, the CA certificate is called `MQTTitude-ca.crt`.

### Android

Note that by following these instructions on Android you'll be prompted to set a device PIN or pattern to protect the device. If you already have that, just carry on. If you absolutely don't want to do that, you should download the certificate into, say, the Downloads folder, and configure it manually in OwnTracks (see below).

So, here's your e-mail message with said attachment.

![Android](https://raw.github.com/wiki/owntracks/owntracks/assets/tls/android-cert-01-mail.png)

Now click on the attached file, and you'll get the following dialog where you have to specify a name for the certificate (sigh: the certificate contains a name, but Android wants you to name it anyway). Give it any old name. We've chosen `MQTTitude`, of course. Then click OK.

![Android](https://raw.github.com/wiki/owntracks/owntracks/assets/tls/android-cert-02-stor.png)

That's it. You're done, and if you no longer require the certificate file just delete the e-mail, though we recommend you keep it around for a bit.

Note that installing a certificate into Android will, since Kitkat (4.4) bring up a warning when you power up your device. This warning is harmless (even though we consider it quite annoying).


![Android](https://raw.github.com/wiki/owntracks/owntracks/assets/tls/cert-alert.png)


### iOS

So, here's your e-mail containing the certificate file. Click on it to launch the profile installer. No worries: we're not going to break anything. We're just adding yet another certificate to iOS' certificate store, and you're going to say that you trust that certificate. You ought to trust it: you've just created it yourself!

![iOS](https://raw.github.com/wiki/owntracks/owntracks/assets/tls/ios-cert-01-mail.png)

You've clicked, and here's the trust store. You see the certificate has a name, but it's not trusted yet. Click on Install.

![iOS](https://raw.github.com/wiki/owntracks/owntracks/assets/tls/ios-cert-02-stor.png)

Read the text if you must. Just kidding: honestly, it's ok. But do read the text, and then click "Install" on the top right.

![iOS](https://raw.github.com/wiki/owntracks/owntracks/assets/tls/ios-cert-03-stor.png)

There you go! That's it: the certificate was installed.

![iOS](https://raw.github.com/wiki/owntracks/owntracks/assets/tls/ios-cert-04-stor.png)

You're done, and if you no longer require the certificate file just delete the e-mail, though we recommend you keep it around for a bit.

### manual configuration on Android

From the, say, e-mail you got with the certificate, click "save" on the attached `.crt` file which is stored on your device's `Download` folder.

In OwnTracks's settings, select `TLS (Custom Certificate)`

![Android custom](https://raw.github.com/wiki/owntracks/owntracks/assets/tls/android-custom-01.png)

Now comes the hard part: you have to enter the path to the certificate you downloaded. On my device this is `/storage/emulated/0/Download/MQTTitude.crt`. Your mileage, etc; 

![Android custom](https://raw.github.com/wiki/owntracks/owntracks/assets/tls/android-custom-02.png)

When you click `Connect`, it will try and use your certificate. Good luck! ;-)



## The nitty gritty

That CA certificate file we mentioned? The attachment called `something.crt`?
It looks like this, in fact, in what is called [PEM] format; but you don't really want to know that, but if you do, this is what it looks like:

```
-----BEGIN CERTIFICATE-----
MIIDGTCCAoKgAwIBAgIJAODXne2yV51zMA0GCSqGSIb3DQEBBQUAMGcxCzAJBgNV
BAYTAkRFMQwwCgYDVQQIEwNOUlcxETAPBgNVBAcTCElyZ2VuZHdvMRYwFAYDVQQK
...
pjGM/XgBs62UhqXnoHrHh/AHIiHieuNFwOhUg0fD/vQ5O6UZkJTWY5LLmEyPN5sS
cPZ5pT/WCvGuIOgNdy1VyWJrrlAjeQlbK+GDcNc=
-----END CERTIFICATE-----
```

That's plain text, even though it might be hard to believe. Actually, it's all sorts of magic wrapped into ASCII bytes, but we can look inside that, and we'll give you a sneak peak:

```
Certificate:
    Data:
        Version: 3 (0x2)
        Serial Number:
            e0:d7:9d:ed:b2:57:9d:73
        Signature Algorithm: sha1WithRSAEncryption
        Issuer: C=DE, ST=NRW, L=Irgendwo, O=MQTTitude.org/emailAddress=jjolie@example.com
        Validity
            Not Before: Aug 18 10:26:08 2013 GMT
            Not After : Aug 16 10:26:08 2023 GMT
        Subject: C=DE, ST=NRW, L=Irgendwo, O=MQTTitude.org/emailAddress=jjolie@example.com
        Subject Public Key Info:
            Public Key Algorithm: rsaEncryption
            RSA Public Key: (1024 bit)
                Modulus (1024 bit):
                    00:bd:70:b0:49:d4:17:c9:17:33:4d:74:fc:61:25:
                    ...
                Exponent: 65537 (0x10001)
        X509v3 extensions:
            X509v3 Subject Key Identifier: 
                4C:89:83:F0:33:AD:2B:D7:FD:DE:1D:6D:8D:00:52:C1:1C:75:B1:85
            X509v3 Authority Key Identifier: 
                keyid:4C:89:83:F0:33:AD:2B:D7:FD:DE:1D:6D:8D:00:52:C1:1C:75:B1:85
                DirName:/C=DE/ST=NRW/L=Irgendwo/O=MQTTitude.org/emailAddress=jjolie@example.com
                serial:E0:D7:9D:ED:B2:57:9D:73

            X509v3 Basic Constraints: 
                CA:TRUE
    Signature Algorithm: sha1WithRSAEncryption
        74:12:f8:d0:51:3a:c6:f8:62:75:61:d1:56:2f:bf:e3:64:3c:
        ...
```

Better, huh? Well, at least parts of it are now readable. Hope you liked this.

### Obtain a certificate and key 

The following sections are not an endorsement of a particular Certification Authority, but rather are a few tips on getting you set up with [Mosquitto].

You can also create your own self-signed certificate. We provide the [generate-CA.sh](https://github.com/owntracks/tools/tree/master/TLS) utility for doing this easily.

#### startssl.com

If you have a key-pair issued by _startssl.com_, you'll be issued a key (in a `*.key` file), and a certificate (as `*.pem` or `*.crt` -- either contains a PEM-encoded certificate).

[Mosquitto] will need the certificate chain for _startssl.com_, which you can find as [ca-bundle.crt](http://www.startssl.com/certs/ca-bundle.crt) at [www.startssl.com/certs/](http://www.startssl.com/certs/).

As far as TLS is concerned, you'll therefore set up your `mosquitto.conf` as follows, specifying corrrect paths to the files.

```
listener 8883
cafile ca-bundle.crt
certfile server.crt
keyfile server.key
```

Then download the _startssl.com_ CA certificate ([ca.crt](http://www.startssl.com/certs/ca.crt)) and install that on your device as described above.

## TLS client certificates

- We also support using [TLS client certificates](tlscert.md)

  [TLS]: http://en.wikipedia.org/wiki/Transport_Layer_Security
  [PEM]: http://en.wikipedia.org/wiki/Privacy_Enhanced_Mail
  [mosquitto]: http://mosquitto.org
  [mconf]: http://mosquitto.org/man/mosquitto-conf-5.html
  [mtls]: http://mosquitto.org/man/mosquitto-tls-7.html

