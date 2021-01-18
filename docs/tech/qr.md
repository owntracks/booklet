To configure OwnTracks (and especially iBeacons for the iOS app), you can use URIs or QR codes.
The QR codes can opened via the camera app, the URIs via the Web browser, the email app, etc.

QR codes can be prepared with a number of tools: there are online offerings, and there is a standalone C program by the name of [qrencode](https://fukuchi.org/works/qrencode/) which works very well:

```
qrencode -l H -v 10 -d 300 -o mybeacon.png 'owntracks:///beacon?rid=ac3def&name=MyBeacon&uuid=12345678-1234-1234-1234-123456789A&major=2&minor=1'
```

### iBeacon QR URI

* scheme = `owntracks`
* host = `<empty>`
* path = `/beacon`
* query = `rid=<rid>&name=<name>&uuid=<uuid>&major=<major>&minor=<minor>`

rid, major, and minor are optional

Example:

```
owntracks:///beacon?rid=ac3def&name=MyBeacon&uuid=12345678-1234-1234-1234-123456789A&major=2&minor=1
```

![beacon QR](images/beacon.png)

### config QR URI

* scheme = `owntracks`
* host = `<empty>`
* path = `/config`
* query = <TODO>


Example:

```
owntracks:///config?<TODO>
```

