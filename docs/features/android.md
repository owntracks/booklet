### Setup
The most important step to configure the OwnTracks Android app is to configure the broker connection. To do so, specify the hostname and port of your broker in the connection preferences. Additionally, specify a name of your device and the username and password that is used to authenticate yourself on the broker.

In the unlikely case that your broker does not require a username and password, you can enable the advanced connection preferences `Preferences/Advanced`. Afterwards an additional menu to select anonymous authentication appears in the connection preferences. Please note that even then, you have to set a device name and username before you can connect as these are also used to construct various other internal things. 

Per default a [[TLS]] secured communication channel with the broker is assumed. Thus you have to ensure that the broker certificate is properly imported on your device. If you do not want to use TLS, you can enable the advanced connection preferences after which you can disable it in the connection preferences. 
In recent Android, a passcode is required once custom TLS certificates are imported. If you want to circumvent this, you can enable TLS with custom certificates and specify the absolute filesystem path to the broker certificate on your device. This will circumvent the Android TLS certificate management by using the certificate directly. 

![](https://raw.github.com/wiki/owntracks/owntracks/assets/android/b-prefs-conn.png)

#### Reporting
![](https://raw.github.com/wiki/owntracks/owntracks/assets/android/b-prefs02.png)

#### Contacts

A single checkbox here which determines whether or not you wish to have
location details shown [[for friends|Friends]].

#### Notifications

![](https://raw.github.com/wiki/owntracks/owntracks/assets/android/b-prefs04.png)

OwnTracks can optionally show the last reported location in the ticker area,
from which you can also forcefully publish your current location.

![](https://raw.github.com/wiki/owntracks/owntracks/assets/android/b-ticker.png)

#### Advanced

![](https://raw.github.com/wiki/owntracks/owntracks/assets/android/b-prefs05.png)

![](https://raw.github.com/wiki/owntracks/owntracks/assets/android/b-prefs-pubtopic.png)


### The map

The app's main screen is the map, which displays your location on it as well as
a reverse geo-coded location if possible.

![](https://raw.github.com/wiki/owntracks/owntracks/assets/android/b-mainmap.png)

You can zoom in and out of the map, and see [[your friends|Friends]] with their photos.

![](https://raw.github.com/wiki/owntracks/owntracks/assets/android/b-map-all.png)

### Beta
To receive beta versions and help us with testing new features of the app, you have to join our [Google+ Community](https://plus.google.com/u/0/communities/102489112826520993343). Afterwards you can register as a  [beta tester](https://play.google.com/apps/testing/st.alr.mqttitude). After a short time, you will receive the current beta version as a regular update via the Google Play Store. If, for some reason you cannot/do not want to our Google+ community, you can grab the APK by selecting the latest Android tag of our repository from [https://github.com/owntracks/android/tags](https://github.com/owntracks/android/tags) and installing the APK from the apps/android/apk/mqttitude.apk. 

### Keepalive 
Due to limitations of the Java MQTT library the Android app cannot send native ping messages. As a workaround a one byte long normal message is published to ```$SYS/keepalive``` periodically to keep the connection alive. It is important to ensure that this topic is writable by all Android devices.

### Development
To contribute to the development, fork the project and clone it to your workstation. Continue to import the project into Eclipse/ADT and opt to open all sub-projects. If this does not automatically import the Google Play Services project into your workspace, import it manually as an extra project. Afterwards, check if the play services are references as project in ```Project Properties > Android```. Once imported, create a feature branch, write your changes and commit them to your feature branch. These changes can be pushed to your fork from which you can submit a pull-request to get your changes included in the official code. 



