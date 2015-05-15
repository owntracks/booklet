# Card

OwnTracks typically displays the _TID_ of a [friend](friends.md) on the map, but you can associate an address book entry to that friend in order to see a friendly face (if you have you friend's photo in your device's address book) and/or a friendly name.

![TID on iOS](images/b-ipad-TID-map.png)

For _Hosted mode_ we developed a new feature we call a _card_ which you can also make use of when in in _Private mode_. A card is a retained message which contains a [JSON payload](../tech/json.md) which, in absence of an address-book association, will be used to populate your friend on your map. The payload contains a full name (hopefully one you recognize), and an avatar -- a small image. If a card exists it will be used, but you can override its use in OwnTracks by associating your friend with an address book entry of your own device.

```json
{
  "_type": "card",
  "name": "Jane Jolie"
  "face": "iV1CFEVkMhmCIKBUKh3 ... ghAAAAABJRU5ErkJggg==",
}
```




