## MQTT

### Keepalives

MQTT was designed with mobile clients in mind so it doesn't require a persistent TCP connection. The broker detects a dead client if it hasn't heard from a client within the _keepalive_ interval which is set by the client.

The server should send the LWT if it the interval plus 50% passed without receiving any MQTT packet from the client (e.g. after 90 sec if keepalive was 60 sec.

From the spec:

> The actual value of the Keep Alive is application specific; typically this is
> a few minutes. The maximum value is 18 hours 12 minutes and 15 seconds.

For example, `mosquitto_sub` uses a default of 60 sec.

In addition, note that the mosquitto broker sends an LWT message if the TCP connection is closed/aborts without sending an MQTT-Disconnect beforehand.

