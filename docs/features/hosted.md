## Hosted mode

As shortly described in [scenarios](../guide/scenarios.md), we offer a _Hosted_ mode on a best-effort basis. We provide an MQTT broker to which you can connect a device or two. Or three. You can also invite OwnTracks friends to share their location with you or, let them see where you are.

1. Register for an account
2. Configure your devices
3. Enjoy

You sign up for a free account at [https://hosted.owntracks.org](https://hosted.owntracks.org) where you specify the following:

![Register](../guide/hosted/01-register.png)

* `username` is the the name with which your device will connect to our hosted broker, and it's the username by which your friends (if you invite anybody) will see you.
* `Name` is your name. This will be visible to your friends
* `Device name` is a name you give your device
* `Email` is your valid e-mail address. We need this in order to send you notifications when a friend invites you to track them
* `Password` is a password for this Web site only. This is **not** the password you will use to actually connect to the service via MQTT.


![Filled out registration form](../guide/hosted/02-register-filled2.png)

As shown above, during registration you specify the name of a device you'll be using. This can be any name, but it must contain ASCII characters from the set `[a-zA-Z0-9]` only. We generate a special _token_ for this device which you will use upon configuring OwnTracks on it. The credentials you use on your device are shown on the screen you then see, and these are shown exactly once and never again. As the page clearly says, keep a copy safe somewhere. (But you can regenerate this _token_ at any time and re-configure your OwnTracks app to use that new token.) The token is a mumble-jumble of letters and digits.

![Initial token](../guide/hosted/03-initial-token.png)

This completes the initial setup, and you can now configure the OwnTracks apps to use _Hosted_ mode with your _username_, your _device name_, and the _token_ that was issued to you.

## Trackers

The main page of our Web site is then shown with three columns:

1. The list of devices you have created for yourself.
2. The list of friends who are tracking one of your devices.
3. The list of people you track.

![Add a tracker](../guide/hosted/04-main-view.png)

Let's assume a friend of yours has also registered for _Hosted_ mode, and you want to allow him to track your device. You add that friend as a _Tracker_, whereupon your friend will get an email asking her/him to confirm the tracking request. Simultaneously, you offer to let her/him track your `s3` device. (You cannot track somebody without them seeing you as well -- it wouldn't be fair!)

![Add a tracker](../guide/hosted/05-add-tracker.png)

This friend of yours (shown here as user `jp`) is added to your Friends with a status of _Pending_ until your friend confirms the tracking request.

![Pending tracking request](../guide/hosted/06-tracker-pending.png)

When your friend confirms your request, this is shown on the main page. The right column shows that `jp` is seeing your `s3` device, and the center column shows that you have not yet confirmed you wish to track `jp`'s `pad` device.

![Confirm tracking](../guide/hosted/07-tracker-accepted-pending.png)

You can then accept to track this friend's device, but you can also decline the request.

![Accept tracking](../guide/hosted/08-accept-tracker.png)

![Accepted tracker](../guide/hosted/09-trackers-accepted.png)

## Devices

If you want to add more devices to your account, go ahead: you enter a device name, and are then shown the access _token_ which you use, together with your _username_ and new _device name_ to configure OwnTracks.

![Add a device](../guide/hosted/20-add-device.png)

This new device is shown in the list of Devices, and you'll note that Jane hasn't shared this device with anybody because she needn't to.

![Friends](../guide/hosted/11-trackers-list-new-device.png)
