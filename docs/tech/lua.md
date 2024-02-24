## Lua hooks

If Recorder is compiled with Lua support (which we do by default), a Lua script you provide is launched at startup. Lua is _a powerful, fast, lightweight, embeddable scripting language_. You can use this to process location publishes in any way you desire: your imagination (and Lua-scripting knowhow) set the limits. Some examples:

* insert received publishes into a database of your choice
* switch on the coffee machine when your OwnTracks device reports you're entering home (but see also [mqttwarn](http://jpmens.net/2014/02/17/introducing-mqttwarn-a-pluggable-mqtt-notifier/))
* write a file with data in a format of your choice (see [etc/example.lua](https://github.com/owntracks/recorder/blob/master/etc/example.lua))

Run the Recorder with the path to your Lua script specified in its `--lua-script` option (there is no default). If the script cannot be loaded (e.g. because it cannot be read or contains syntax errors), the Recorder aborts with a diagnostic.

Configure `lua_script:` in our [quicksetup](../guide/quicksetup.md) `configuration.yaml` to have this enabled for you.

If the Lua script can be loaded, it is automatically provided with a table variable called `otr` which contains the following members:

* `otr.version` is a read-only string with the Recorder version (example: `"0.3.2"`)
* `otr.log(s)` is a function which takes a string `s` which is logged to syslog at the Recorder's facility and log level INFO.
* `otr.strftime(fmt, t)` is a function which takes a format string `fmt` (see `strftime(3)`) and an integer number of seconds `t` and returns a string with the formatted UTC time. If `t` is 0 or negative, the current system time is used.

Your Lua script *must* provide the following functions:

## `otr_init`

This is invoked at start of Recorder. If the function returns a non-zero value, Recorder unloads Lua and disables its processing; i.e. the `hook()` will *not* be invoked on location publishes.

## `otr_exit`

This is invoked when the Recorder stops, which it doesn't really do unless you CTRL-C it or send it a SIGTERM signal.

## `otr_hook`

This function is invoked at every location publish processed by the Recorder. Your function is passed three arguments:

1. _topic_ is the topic published to (e.g. `owntracks/jane/phone`)
2. _type_ is the type of MQTT message. This is the `_type` in our JSON messages (e.g. `location`, `cmd`, `transition`, ...) or `"unknown"`.
3. _location_ is a [Lua table](http://www.lua.org/pil/2.5.html) (associative array) with all the elements obtained in the JSON message. In the case of _type_ being `location`, we also add country code (`cc`) and the location's address (`addr`) unless reverse-geo lookups have been disabled in Recorder.

Assume the following small example Lua script in `example.lua`:

        local file

        function otr_init()
            otr.log("example.lua starting; writing to /tmp/lua.out")
            file = io.open("/tmp/lua.out", "a")
            file:write("written by OwnTracks Recorder version " .. otr.version .. "\n")
        end

        function otr_hook(topic, _type, data)
            local timestr = otr.strftime("It is %T in the year %Y", 0)
            print("L: " .. topic .. " -> " .. _type)
            file:write(timestr .. " " .. topic .. " lat=" .. data['lat'] .. data['addr'] .. "\n")
        end

        function otr_exit()
        end

When Recorder is launched with `--lua-script example.lua` it invokes `otr_init()` which opens a file. Then, for each location received, it calls `otr_hook()` which updates the file.

Assuming an OwnTracks device publishes this (shortened for clarity) payload

        {"batt":-1,"lon":2.29513,"lat":48.85833,"t":"u","tst":1441984413,"_type":"location","tid":"JJ"}

the file `/tmp/lua.out` would contain

        written by OwnTracks Recorder version 0.3.0
        It is 17:13:33 in the year 2015 owntracks/jane/phone lat=48.858339 Avenue Anatole France, 75007 Paris, France

## Republishing HTTP posts

OwnTracks location (and other type) publishes submitted via HTTP are not automatically republished to MQTT. Should you wish to do this, Lua hooks can help accomplish this. The following example illustrates how to do so.

      -- requires: 
      --   a) Compile recorder with -DLUA and configure
      --   b) OTR_LUASCRIPT = ".../repub.lua"
      -- also requires Lua JSON from http://regex.info/blog/lua/json
      
      JSON = (loadfile "/etc/ot-recorder/JSON.lua")()
      
      function otr_init()
      end
      
      function otr_exit()
      end
      
      function otr_hook(topic, _type, data)
          otr.log("DEBUG_PUB:" .. topic .. " " .. JSON:encode(data))
          if(data['_http'] == true) then
              if(data['_repub'] == true) then
                 return
              end
              data['_repub'] = true
              local payload = JSON:encode(data)
              otr.publish(topic, payload, 1, 1)
          end
      end
      

There's [more information on this](https://github.com/owntracks/recorder/blob/master/doc/HOOKS.md) with examples, including creating a per-JSON-element hook, if you're interested.
