## Quicksetup Extras

There are some additional features of [Quicksetup](../guide/quicksetup.md) we describe here.

## MQTT

During bootstrapping _quicksetup_ creates a user for the MQTT broker with additional permissions. Specifically, the user `_lr` is permitted to publish in `owntracks/#`: the idea is that this user can, for instance, be used to clear other users' OwnTracks topics, etc.

The password for this user is randomly generated and stored in a file in the _userdata_ directory. The following invocation can be used from the command line:

```console
$ mosquitto_pub -u _lr -P "$(cat /usr/local/owntracks/userdata/.lr.pw)" ...
```

