## Additional Quicksetup Configuration

In [Quicksetup](../guide/quicksetup.md) we describe how it works and how you begin using `configuration.yaml` to easily bootstrap your OwnTracks platform.

## Friends list syntax

Friends are basically configured like this in the `configuration.yaml` file:

              friends:
                - { tid: JJ, username: jane,   devicename: nokia }

To be clear this is YAML, so you could also write it this way, if you prefer. The field names are the same, the order doesn't matter but note the leading hyphen (`-`) which denotes a new entry in the array.

              friends:
                - tid: JJ
                  username: jane
                  devicename: nokia

The second friend's entry would again begin with a hyphen which must be placed under the first:

              friends:
                - tid: JJ
                  username: jane
                  devicename: nokia
                - tid: m1
                  username: another
                  ...

## Friend-specific settings

There are a few settings a friend can have we've not mentioned yet, so here they are.


- our _quicksetup_ configuration defaults to configuring the apps (via [inline or .otrc](../features/remoteconfig.md) in MQTT mode. Setting `httpmode` to `True` changes that to HTTP for this particular friend (e.g. `httpmode: True`).

-different passwords are auto-generated during bootstrapping and result in distinct (per user) and random 20-character passwords. Should you wish to explicit configure a specific password, do so by setting `password` to a string of your choice (e.g. `password: "supersecr1t"`). Note that this password will be visible in the configuration and in the `*.pass` files. Further note, that all entries for a specific user will re-use the first password (specified or randomly generated) set for said user.

- set up [payload encryption](../features/encrypt.md) for a particular friend by specifying a `secret` which is either the secret itself (e.g. `secret: "bla009"`) or, if the value begins with a slash, the path to a filename which contains the secret (e.g. `secret: "/home/jane/.secret"`).

## Global

- with `lua_script` you configure the path to a [Recorder Lua script](../tech/lua.md)  which is written into `/etc/defaults/ot-recorder` for the next Recorder start. Use this only after testing your Lua script as the Recorder will fail to start on error.
