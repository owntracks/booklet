## HTTP

An optional HTTP mode will be implemented. This mode which is like Private will use HTTP POST requests to a configurable HTTP endpoint which can bei either HTTP or HTTPS.

In this mode:

* There are no "friends". The device can publish only which means you cannot track other people.
* There is no support for [card](../features/card.md)s.

The URL will be specified including parameters for _username_ and _devicename_ (`?u=user&d=device`), alternatively using the `X-Limit-U` and `X-Limit-D` headers. Other than that, the syntax for the URL is

```
http[s]://[user[:password]@]host[:port]/path
```

Authentication to the endpoint is performed with HTTP Basic authentication and, as such, we very strongly recommend the use of TLS (`https://` scheme). Authentication uses the username / password combination configured up in the apps' preferences. 

All publishes which are currently done with MQTT will then be POSTed to the endpoint with exactly the same [JSON][json.md] payloads.

If the HTTP endpoint is reachable (i.e. it responds with _any_ status code), the payload is considered POSTed. In the event that the endpoint is unreachable (e.g. no connectivity), the payload will be queued and posted at a later stage.

[Encryption](../features/encrypt.md) will be supported and can be used with HTTP endpoints.

If the endpoint returns a status code 200 it will typically return an empty JSON payload object `{}`. The HTTP endpoint may, however, return a command to the OwnTracks device, in which case it must return a valid `_type: cmd` as described in [JSON](../tech/json.md).

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

    print json_encode(new stdClass);    # empty object
?>
```

Assuming the Web server hosting this example is called `example.com`, and assuming the above script is in Jane's home directory's `public_html` saved as `loc.php`, the URL you configure in the OwnTracks app would be `http://example.com/~jane/loc.php`.
