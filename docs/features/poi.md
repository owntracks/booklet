## POI and tags

Currently iOS only.

When manually publishing a location from the app, users have the possibility to set (or later clear) a tag. This tag is automatically used to subsequently tag messages with an appropriate JSON element until it is manually cleared in the UI. The value of a tag is added to the JSON as

```json
{
  "_type": "location",
  "tag" : "<your tag>",
  ...
 
}
```

Tags can be used to group locations pertaining to a particular event (e.g. a specific trip or vacation).

![POI on iOS](images/poi.png)

Also when publishing a location a user can set a Point of Interest (POI) which is associated with the location being published. This POI is added once-only to the location message (contrary to a tag which is used until cleared):


```json
{
  "_type": "location",
  "lat": 17.065921,
  "lon": -96.717023,
  "poi": "Oaxaca, hotel Los Pilares",
  ...
}
```

iOS displays the Points of Interest in a red bubble as shown above.
