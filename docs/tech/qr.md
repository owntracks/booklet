## QR codes

To setup the OwnTracks app (iOS only currently), you can use QR codes or URLs to configure the app for

* Hosted mode and
* iBeacons.

The QR codes can be scanned from the settings tab within the app, using an external QR reader app (such as _Qrafter_) or by providing the URL in the device's browser.

![QRafter](images/b-qrafter.png)

QR codes can be prepared with a number of tools: there are online offerings, and there is a standalone C program by the name of [qrencode](https://fukuchi.org/works/qrencode/) which works very well:

```
qrencode -l H -v 10 -d 300 -o mybeacon.png 'owntracks:///beacon?name=MyBeacon&uuid=12345678-1234-1234-1234-123456789A&major=2&minor=1'
```

### iBeacon QR URL

* scheme = `owntracks`
* host = `<empty>`
* path = `/beacon`
* query = `name=<name>&uuid=<uuid>&major=<major>&minor=<minor>`

major and minor are optional

Example:

```
owntracks:///beacon?name=MyBeacon&uuid=12345678-1234-1234-1234-123456789A&major=2&minor=1
```

![beacon QR](images/beacon.png)

### Hosted mode QR URL

* scheme = `owntracks`
* host = `<empty>`
* path = `/hosted`
* query = `user=<user>&device=<device>&token=<token>`

Example:

```
owntracks:///hosted?user=user&device=device&token=token
```

![hosted QR](images/hosted.png)

