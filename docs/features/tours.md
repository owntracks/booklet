## Tours: Location Sharing

You might occasionally wish to share your location with somebody who doesn't have OwnTracks. For these situations you can set up what we call a _Tour_ -- a time-limited view of your location which can be viewed from a Web browser.

The OwnTracks Recorder gets a request for a Tour as a JSON published to the `../request` subtopic or POSTed to the HTTP endpoint:

```json
{
  "_type": "request",
  "request": "tour",
  "tour": {
    "label": "Meeting with C. in Essen",
    "from": "2022-08-01T05:35:58",
    "to": "2022-08-02T15:00:58"
  }
}
```

The Recorder creates a View from this and returns a unique URL to this View which you can share with anybody you want to.

```json
{
  "_type": "cmd",
  "action": "response",
  "request": "tour",
  "status": 200,
  "tour": {
       "label": "Meeting with C. in Essen",
       "from": "2022-08-01T05:35:58",
       "to": "2022-08-02T15:00:58",
       "uuid": "7fc09e95-0082-4f2e-b3ee-bd68e39a39fe",
       "url": "http://localhost:8085/view/7fc09e95-0082-4f2e-b3ee-bd68e39a39fe"
}
```


Your last location is marked on this View's map with a red dot and the track of your movements between the `from` and `to` times can be loaded. The important bit: the visitor of the unique URL will not see your location prior to the specified `from` time nor after the `to` time. It is also possible to completely delete the Tour in which case a user attempting to access it would get a 404 Not Found. Tours can outlive the `to` field if you want to, say, provide a historic map of where you once were (i.e. _My 2019 vacation on Mallorca_).

An authorized client may also request a list of current Tours with this JSON:

```json
{
  "_type": "request",
  "request": "tours"
}
```

whereupon an array of existing Tours and their unique URLs is returned:

```json
{
 "_type": "cmd",
 "action": "response",
 "request": "tours",
 "tours": [
  {
   "label": "Meeting with C. in Essen",
   "from": "2022-08-01T05:35:58",
   "to": "2022-08-02T15:00:58",
   "uuid": "0a721022-c54a-49a4-9f5a-f1a76dd92e9e",
   "url": "http://localhost:8085/view/0a721022-c54a-49a4-9f5a-f1a76dd92e9e"
  }
 ],
 "ntours": 1
}
```

Only Tours which have been created by a user for their specific device will be enumerated in the Tours list.

Tours can be "un-toured" (i.e. deleted) with this payload:

```json
{
  "_type": "request",
  "request": "untour",
  "uuid": "0a721022-c54a-49a4-9f5a-f1a76dd92e9e"
}
```

For Tours to work, the Recorder needs to be compiled with support for HTTP and TOURS, and the following settings need defining:

- `OTR_HTTPPREFIX` must be configured to contain the URL to the Recorder as seen from "outside", i.e. from the Internet. This setting is used to construct the unique URL which will be returned for a Tour. So, if you have, say, a Recorder behind an NGINX proxy (as described in our README), then the setting would say
   ```console
   export OTR_HTTPPREFIX="https://example.net/owntracks"
   ```
   on the other hand, if you're testing internally and your Recorder is listening on port 8085, you might do this:
   ```console
   export OTR_HTTPPREFIX="http://localhost:8085"
   ```

Initially we thought the term "share" would be good but then decided it's a too ambiguous word, and we didn't want to run into the mess we created with mixing terms (remember region and waypoint? :-) ). After a bit of pondering we think the word tour as in ("I did a tour last year I want to show you") or as in ("I am now on tour") is adequate.


