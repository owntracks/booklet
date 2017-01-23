## Setup

The most important step to configure the OwnTracks Android app is to configure the broker connection. To do so, specify the hostname and port of your broker in the connection preferences. Additionally, specify a name of your device and the username and password that is used to authenticate yourself on the broker.

In the unlikely case that your broker does not require a username and password, you can disable authentication in the connection preferences. Please note that even then, you have to set a device name and username before you can connect as these are also used to construct various other internal things. 

Per default a [TLS](tls.md) secured communication channel with the broker is assumed. Thus you have to ensure that the broker certificate is properly imported on your device. 

In recent Android, a passcode is required once custom TLS certificates are imported. If you want to circumvent this, you can enable TLS with custom certificates and import that into the private storage location of the app. This will circumvent the Android TLS certificate management by using the certificate directly and skipping any other certificates known to the system. 



### The map

The app's main screen is the map, which displays your location on it as well as
a reverse geo-coded location if possible.

You can zoom in and out of the map, and see your friends with their photos.




