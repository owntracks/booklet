## Featured

The iOS app has a new tab (hidden by default) which is shown when it receives a particular MQTT message of type `cmd`:

```json
{"action": "action", "content": "hello there!", "_type": "cmd"}
```


You can use `url` instead of `content` in the payload, in which case the tab opens with the appropriate URL loaded.

```json
{"action": "action", "url": "http://example.com", "_type": "cmd"}
```

The tab pops up silently, i.e. without notification, and selecting the tab displays it.

The open tab silently disappears if content and url are omitted.

See also:

* [Switching lights with OwnTracks, iBeacons, and openHAB](http://jpmens.net/2016/02/15/switching-lights-with-owntracks-ibeacons-and-openhab/)
