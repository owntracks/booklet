## QR codes

To setup the OwnTracks app, you may use QR codes or URLs to configure the app for

* Hosted mode and
* iBeacons.

The QR codes can be scanned from the settings tab within the app, using an external QR reader app (like `Qrafter`) or 
by providing the URL in the device's browser.

QR codes can be prepared with a number of tools (like `qrencode`).

### iBeacon QR URL

* scheme = `owntracks`
* host = ``
* path = `/beacon`
* query = `name=<name>&uuid=<uuid>&major=<major>&minor=<minor>`

major and minor are optional

Example:
> owntracks:///beacon?name=MyBeacon&uuid=12345678-1234-1234-1234-123456789A&major=2&minor=1

![beacon QR](images/beacon.png)

### Hosted mode QR URL

* scheme = `owntracks`
* host = ``
* path = `/hosted`
* query = `user=<user>&device=<device>&token=<token>`

Example:
> owntracks:///hosted?user=user&device=device&token=token

![hosted QR](images/hosted.png)

