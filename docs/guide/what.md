# What OwnTracks does

The OwnTracks app runs in the background on your Android or iOS device and waits for the smart phone to tell it that the device has moved, whereupon OwnTracks sends out a message with its current coordinates (and a few other things we'll discuss in a moment).

We'd like you to read this bit again:

> _waits for the smart phone to tell it that the device has moved_

It is very important to understand that OwnTracks literally waits for the device's operating system to inform it of a movement; only then can OwnTracks react and do something. To make matters even more complicated[^1], OwnTracks on Android runs in the background, all the time, whereas on iOS it is "killed off" by the operating system and woken up every several hundred seconds, in which it gets a teeny tiny time slot to do its thing.

That's basically all OwnTracks does.

### More

That last sentence isn't true. Strike it.

OwnTracks does quite a bit more. It shows your location on a map. Wow: big deal, huh? Well, it shows your location and [that of your friends](friends.md) on a map. Ah: better. It can also detect when you enter or leave a particular region for which [you set a so-called waypoint](waypoints.md). People use this, say, to control some aspect of their home-automation system. (Everybody left home? We can turn the lights off.)

OwnTracks also has a pedometer, a.k.a. step counter (on iPhone 5S, 6) which you can query remotely. OwnTracks has support for [iBeacons](beacons.md), and it gives you neat notifications when one of your friends gets home so you can phone them over a land-line to plan a get-together.

OwnTracks reports the device's battery level remotely so you can add that to your monitoring setup, if you have one. (No worries if you don't -- you probably don't need it then.)

And finally, though for us this is *very* important, OwnTracks does all this securely.

[^1]: and believe us, they *are* complicated
