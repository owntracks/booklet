## Answers

This is a list of questions we really get asked a lot and some answers to these questions.

Q: Can I have OwnTracks not publish location for a specific period of time?<br/>
A: Set mode to manual (iOS) or disable automatic location reporting (Android)

Q: How can I restart OwnTracks?<br/>
A: On iOS: Swipe away and start; On Android: Force close (e.g. from ≣ → ⏻Exit app) and start.

Q: Can you remove Google Play Services from the Android app? <br/>
A: No

Q: Can you remove Google Maps from the Android app? <br/>
A: No.  Uninstalling [the app from Google Play Store](https://play.google.com/store/apps/details?id=org.owntracks.android) and [installing it from F-Droid](https://f-droid.org/en/packages/org.owntracks.android/) does not offer anymore the Google Default, Google Hybrid and Google Terrain map layers.  The OpenStreetMap and Wikimedia layers are available in both Google Play and F-Droid download locations.

Q: Since updating to Android 6 (or higher), background location reporting does not work as before<br/>
A: Since Android 6 Google has started to restrict background apps. We're doing our best to work against this were we can. 
Your best bet is to enable the ongoing notifications, which enable some background features. HTTP mode works best in the background because it can rely on the OS background scheduling, in contrast to MQTT mode, which requires a persistent TCP connection. 

Q: I've moved, but OwnTracks is not reporting my location<br/>
A: Depending on the mode, the apps report location changes only after significant changes. 

Q: How do I enable region monitoring? (Waypoints)<br/>

Q: I'm sure I've moved into (or out of) a region, but OwnTracks isn't reporting that.<br/>

Q: Why are transition events delayed?<br/>

Q: How can I increase reporting frequency and does that have any negative impact?<br/>

Q: I have a question<br/>
A: We love questions. Well, sometimes. If you want to ask us a question or desire feedback from other OwnTracks users, visit us at the [OwnTracks meta tracker](https://github.com/owntracks/talk).

Q: How can I report an issue?<br/>
A: If you think you've found a bug, please report it on our respective [Android](https://github.com/owntracks/android) or [iOS](https://github.com/owntracks/ios) issue tracker on Github. 

