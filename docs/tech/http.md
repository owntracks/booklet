## HTTP

An optional HTTP mode is being implemented with which the OwnTracks apps use a privately configured HTTP endpoint (a.k.a. a Web server) to which they POST requests over HTTP instead of publishing to MQTT. In this mode all [JSON](json.md) payloads reported by the apps are transmitted via HTTP to the endpoint. In particular and most importantly, the apps publish their location data.

The URL you enter in the setting for HTTP mode has the following syntax:

```
http[s]://[user[:password]@]host[:port]/path
```

Authentication to the endpoint is performed with HTTP Basic authentication and, as such, we very strongly recommend the use of TLS (`https://` scheme).

All publishes which are currently done with MQTT will then be POSTed to the endpoint with exactly the same [JSON](json.md) payload formats. Support for Friends is available if your HTTP endpoint can produce appropriate data which is consumed by the app whenever it POSTs a location. This differs greatly from MQTT mode wherein the app subscribes to topics and is informed of data on those topics whenever it's available; in HTTP mode the apps do not periodically poll your HTTP endpoint; rather it is contacted only when the app is ready to publish its location or when you manually trigger a publish. (Support for friends and optionally their cards is implemented in the Recorder.)

If the HTTP endpoint is reachable (no exception, no timeout, DNS name exists, etc.) and a successfull return code (`2xx`) is returned  the payload is considered POSTed. In the event that the endpoint is unreachable, the payload will be queued and posted at a later time.

The [encryption](../features/encrypt.md) feature is supported, and you can use it with HTTP endpoints; the Recorder supports decryption, but if you implement your own endpoint you have to perform decryption in the endpoint yourself.

If the HTTP endpoint returns a status code 200 it will typically return an empty JSON payload array `[]`. It may, however, return an array of JSON objects to the OwnTracks device, each of which must be a valid `_type` as described in [JSON](../tech/json.md). Support for the following `_type` is implemented:

| `_type`      |  iOS  | Android    | Usage
| :----------- | :---  | :--- | :--------------
| `location`   | Y     | Y    | Can return friend location objects.
| `cmd`        | Y     | Y    | with `action` set to `dump`, `reportLocation`, `reportSteps`, `action`, and `setWaypoints`
| `card`       | Y     | Y    | Can return [card](../features/card.md) objects for self and friends
| `transition` | Y     | Y    | Obtain friends' transition events.

The OwnTracks Recorder supports [HTTP mode](https://github.com/owntracks/recorder#http-mode) out of the box, as long as it is built with HTTP support and a `--http-port` is configured.
When using the Recorder, the URL you specify in the app's configuration *must* include parameters for _username_ and _devicename_ (`?u=user&d=device`), alternatively using the `X-Limit-U` and `X-Limit-D` headers respectively. You can also force _username_ using a proxy as described in the Recorder's documentation.


### PHP example

Using a simple PHP script which you host, say, on an Apache or nginx server, you can quite easily record locations POSTed from the OwnTracks apps. The following very simple example will fill a database table:

```
mysql> select * from locations;
+---------------------+------+-----------+----------+
| dt                  | tid  | lat       | lon      |
+---------------------+------+-----------+----------+
| 2016-02-20 09:16:05 | JJ   | 48.858330 | 2.295130 |
| 2016-02-20 09:19:49 | JJ   | 48.860430 | 2.294010 |
+---------------------+------+-----------+----------+
```

For the sake of clarity this example uses a database table with a MySQL timestamp column which is automatically set upon INSERT; keep in mind that the real location event posted by the OwnTracks apps has a `tst` timestamp when the event actually occurred.

```php
<?php
    # Obtain the JSON payload from an OwnTracks app POSTed via HTTP
    # and insert into database table.

    header("Content-type: application/json");

    $payload = file_get_contents("php://input");
    $data =  @json_decode($payload, true);

    if ($data['_type'] == 'location') {

        # CREATE TABLE locations (dt TIMESTAMP, tid CHAR(2), lat DECIMAL(9,6), lon DECIMAL(9,6));
        $mysqli = new mysqli("127.0.0.1", "user", "password", "database");

        $lat = $data['lat'];
        $lon = $data['lon'];
        $tid = $data['tid'];

        $sql = "INSERT INTO locations (tid, lat, lon) VALUES (?, ?, ?)";
        $stmt = $mysqli->prepare($sql);
        # bind parameters (s = string, i = integer, d = double,  b = blob)
        $stmt->bind_param('sdd', $tid, $lat, $lon);
        $stmt->execute();
        $stmt->close();
    }

    $response = array();
    # optionally add objects to return to the app (e.g.
    # friends or cards)
    print json_encode($response);
?>
```

Assuming the Web server hosting this example is called `example.com`, and assuming the above script is in Jane's home directory's `public_html` saved as `loc.php`, the URL you configure in the OwnTracks app would be `http://example.com/~jane/loc.php`. We _urge_ you to consider transmitting your data to your Web server securely using TLS and authentication, in which case the URL you use will be along the lines of `https://user:password@example.com/~jane/loc.php`.

There's lots of other data in the JSON payload from the OwnTracks apps you may be interested in; we reccomend you [study the API documentation](json.md).

* see also: [Traccar](../features/traccar.md)
