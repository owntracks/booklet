## Friends

OwnTracks has a so-called _Friends and Family_ capability which is enabled by
default. The way this works is friends and/or family members who share a single
[MQTT broker](../guide/broker.md) can "see" eachother on the map, respectively on a
list of friends held by the app.  (This can be done either by configuring all
apps to use the same broker or by what is called _bridging_.)

Let's assume for a moment, that a user Jane (user name `jjolie`) has an iPhone 4s (device name `4s`), and that she publishes location data to a topic at

```
owntracks/jjolie/4s
```

Let us further assume, that her friend John (user name `john`) has an Android Nexus 4 (device name `nex4`) and he therefore publishes on the _same broker_ to a topic called

```
owntracks/john/nex4
```

Let us further assume that access control on the [MQTT broker](../guide/broker.md) allows both users to subscribe to eachother's topics.

Now, if *both* friends subscribe to a topic called `owntracks/+/+` (which the
apps do by default) their devices will effectively receive published location
messages to either topic. So when either Jane or John publish their location,
the other's device will receive it.

What OwnTracks then does is to list these topic names (e.g. `owntracks/jjolie/4s`) with the last received position and a time stamp. A click on a particular entry will take you to that entry's location on the map. Neat, eh?

### Faces

It gets better.

You probably don't want to remember the (sometimes cryptic) topic names of your friends or family members, but you would like to see their names and a matching face. That's no problem: OwnTracks can do that.

Depending on the device you're using, the procedure differs slightly.

#### Android

The app subscribes to a topic branch `owntracks/+/+` on the broker per default
(but this can be configured in advanced preferences) to receive locations of
other people connected to the the same [[MQTT broker|MQTTbroker]] that publish
to the default topic of `mqttittude/$username/$devicename`.

When the app receives a message published by a different user, it looks for a
contact with an instant messenger (`IM`) entry of type "`OwnTracks`" (case insensitive)
whose entry value is equal to the topic of the received location
publish. If a contact is found, the contact is shown with its profile picture
and name instead of the topic in the list and on the map.

Note that if you add an IM entry to a contact via, say, Google, you have to
sync the contact to your phone (phone preferences/accounts/google/sync untick
and tick contacts, wait a few seconds) and restart the app. In other words, the
contact's data must be on your device.

Add an instant-messaging (`IM`) entry to the contact you want to associate with
a particular MQTT topic name. The tag of the `IM` should be "`OwnTracks`" (case
independent), and the value is the topic name.

![](https://raw.github.com/wiki/owntracks/owntracks/assets/android/b-contact-address.png)

The _Locations_ list will then show you your contact's name and photo:

![](https://raw.github.com/wiki/owntracks/owntracks/assets/android/b-locations01.png)

#### iOS

The app "sees" a friend as soon as it receives a publish to the topic branch subscribed to, which by default is `owntracks/+/+`.

Select the _Friends_ button to see a list of friends:

![](https://raw.github.com/wiki/owntracks/owntracks/assets/friends/ios/ios-friends-01.png)

Tapping on one of the friends, performs a reverse geo-coding to show address. Furthermore, a click on the little right-arrow shows the last location and
all shared waypoints of that friend.

## Who is that?

It's difficult to remember which MQTT topic belongs to which friend, so we can associate a topic with an image of the friend as contained in the iOS address book:

Select an entry:

![](https://raw.github.com/wiki/owntracks/owntracks/assets/friends/ios/ios-friends-02.png)

Then click on the _bookmark_ icon on top right. The Address book opens.

![](https://raw.github.com/wiki/owntracks/owntracks/assets/friends/ios/ios-friends-09.png)

Select the entry you want to associate with the OwnTracks topic. In this
example, I choose Kate Bell.

![](https://raw.github.com/wiki/owntracks/owntracks/assets/friends/ios/ios-friends-04.png)

We're back in OwnTracks, and we see the picture from the addressbook.
If you want to release the association, select the wastepaper basket icon in the address book screen. The display
changes back from the picture and name of the friend to the mqtt topic (e.g. "owntracks/kate/nexus4").

![](https://raw.github.com/wiki/owntracks/owntracks/assets/friends/ios/ios-friends-05.png)

![](https://raw.github.com/wiki/owntracks/owntracks/assets/friends/ios/ios-friends-06.png)


When you zoom the map out (or near to the friend's location), you see a small rendition of the icon directly on the map.

![](https://raw.github.com/wiki/owntracks/owntracks/assets/friends/ios/ios-friends-07.png)

Voila!

Note: this works only with contacts stored locally -- not with corporate address books associated with your iPhone.

To support corporate address books (which usually cannot be updated) the ios
app Version >=5.1 can be set to store the associations locally rather than in
the address book. You find the respective switch in the expert mode settings.
Switch it `off` (i.e. disabled) to keep associations between MQTT topics and 
your contact within OwnTracks.

![](https://raw.github.com/wiki/owntracks/owntracks/assets/friends/ios/ios-friends-08.png)

