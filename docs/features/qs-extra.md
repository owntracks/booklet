## Quicksetup Extras

There are some additional features of [Quicksetup](../guide/quicksetup.md) we describe here.

## MQTT

During bootstrapping _quicksetup_ creates a user for the MQTT broker with additional permissions:

```
user _lr
topic read #
topic read $SYS/#
topic write owntracks/#
```

The password for this user is randomly generated and stored in a file in the _userdata_ directory. The following invocation can be used from the command line:

```console
$ mosquitto_pub -u _lr -P "$(cat /usr/local/owntracks/userdata/.lr.pw)" ...
```

The idea is that this user can, for instance, be used to clear other users' OwnTracks topics, etc.
