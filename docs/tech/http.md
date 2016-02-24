## HTTP

An optional HTTP mode is being implemented. This mode, which is like Private, uses HTTP POST requests to a configurable HTTP endpoint which can bei either HTTP or HTTPS. In this mode:

* The device publishes location data. "friends" and their [card](../features/card.md)s are a hack which is supported by the Recorder.

For use with the [OwnTracks Recorder](https://github.com/owntracks/recorder) the URL you specify must include parameters for _username_ and _devicename_ (`?u=user&d=device`), alternatively using the `X-Limit-U` and `X-Limit-D` headers.

The syntax for the URL is:

```
http[s]://[user[:password]@]host[:port]/path
```

Authentication to the endpoint is performed with HTTP Basic authentication and, as such, we very strongly recommend the use of TLS (`https://` scheme).

All publishes which are currently done with MQTT will then be POSTed to the endpoint with exactly the same [JSON][json.md] payload formats.

If the HTTP endpoint is reachable (i.e. it responds with _any_ status code -- even 5xx), the payload is considered POSTed. In the event that the endpoint is unreachable (e.g. no connectivity), the payload will be queued and posted at a later time.

[Encryption](../features/encrypt.md) are supported and can be used with HTTP endpoints.

If the endpoint returns a status code 200 it will typically return an empty JSON payload array `[]`. The HTTP endpoint may, however, return an array of JSON objects to the OwnTracks device, each of which must be a valid `_type` as described in [JSON](../tech/json.md).

The OwnTracks Recorder supports [HTTP mode](https://github.com/owntracks/recorder#http-mode) out of the box.


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
    print json_encode($response);
?>
```

Assuming the Web server hosting this example is called `example.com`, and assuming the above script is in Jane's home directory's `public_html` saved as `loc.php`, the URL you configure in the OwnTracks app would be `http://example.com/~jane/loc.php`. We _urge_ you to consider transmitting your data to your Web server securely using TLS and authentication, in which case the URL you use will be along the lines of `https://user:password@example.com/~jane/loc.php`.

There's lots of other data in the JSON payload from the OwnTracks apps you may be interested in; we urge you to [study the API documentation](json.md).
