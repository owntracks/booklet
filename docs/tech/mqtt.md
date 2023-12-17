## MQTT

### Keepalives

MQTT was designed with mobile clients in mind so it doesn't require a persistent TCP connection. The broker detects a dead client if it hasn't heard from a client within the _keepalive_ interval which is set by the client.

The server should send the LWT if it the interval plus 50% passed without receiving any MQTT packet from the client (e.g. after 90 sec if keepalive was 60 sec.

From the spec:

> The actual value of the Keep Alive is application specific; typically this is
> a few minutes. The maximum value is 18 hours 12 minutes and 15 seconds.

For example, `mosquitto_sub` uses a default of 60 sec.

In addition, note that the mosquitto broker sends an LWT message if the TCP connection is closed/aborts without sending an MQTT-Disconnect beforehand.

## Broker 
Below is a list of known configuration values that are required to connect to certain MQTT endpoints 

### AWS IOT 
The OwnTracks apps are partially compatible to the [AWS IOT](https://aws.amazon.com/iot/) broker in MQTT private mode. 
As AWS IOT does not support some MQTT features (notable retained messages), some OwnTracks features might not work as expected. 

Before connecting, a so called _thing_ has to be configured which represents the connecting client. 
The thing needs to have a client certificate generated for authentication and a policy attached that allows some actions. 
The most basic policy allows all actions for the _thing_. 
```
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Allow",
      "Action": "iot:*",
      "Resource": "*"
    }
  ]
}
```

The following settings are required to connect 
```
{
    "_type":"configuration", 
    "mode":0, 
    "host": "ENDPOINT.amazonaws.com",
    "port": 8883,
    "subQos":0,
    "pubQos":0, 
    "auth":false,
    "pubRetain":false, 
    "cleanSession":true, 
    "tls":true
    "clientId":"CLIENTID",
}
```
The `ENDPOINT` can be obtained in the _thing_ details under _Interact_.  
Additionaly, you need to configure the app to use a client TLS certificate for authentication and the AWS TLS ca.
Before importing it into the app, you need to set a password to the client TLS certificate. 

### Losant IoT Developer Platform 
The OwnTracks apps are partially compatible to the [Losant IoT Developer Platform](https://www.losant.com/) in MQTT private mode. 
As Losant does not support some MQTT features (notable retained messages), some OwnTracks features might not work as expected. 

The following settings are required to connect 
```
{
    "_type": "configuration",
    "mode":0,
    "mqttProtocolLevel": 4,
    "host":"broker.losant.com",
    "port":8883,
    "tls":true,
    "pubRetain": false,
    "pubQos": 0,
    "subQos":0,
    "cleanSession": true,
    "clientId":"CLIENT_ID",
    "auth":true,
    "username":"ACCESS_KEY",
    "password":"ACCESS_SECRET"
}
```

See the [official documentation](https://docs.losant.com/applications/access-keys/) how to obtain the `ACCESS_KEY` and `ACCESS_SECRET`. 
