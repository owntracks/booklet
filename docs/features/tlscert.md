## Client certificates

The OwnTracks iOS and Android clients can be configured to use TLS client
certificates to authenticate against their MQTT broker. This is the next best
thing to two-factor authentication, where the apps have a TLS key and a
certificate which has to be presented to the broker for successful
authentication.

### Mosquitto

When using the Mosquitto [broker](../guide/broker.md) support for TLS
certificates can be enabled as follows:

```
require_certificate true
use_identity_as_username true
```

### Client certs

To create a client certificate and key, you can use, say, `generate-CA.sh` from
our [tools repository](https://github.com/owntracks/tools/tree/master/TLS).

```bash
./generate-CA.sh client jjolie
```

This produces at least two files which are required on the OwnTracks devices: a `.key` file and a `.crt` file.


### PKCS#12

In order to safely transport a user's key and certificate to the OwnTracks app,
we make use of the [PKCS#12](https://en.wikipedia.org/wiki/PKCS_12) container
format. OpenSSL's `pkcs12` subcommand creates this container and protects it
with a passphrase you specify. This passphrase we will later require in the app.

```bash
openssl pkcs12 \
  -export \
  -in jjolie.crt \
  -inkey jjolie.key \
  -name "Jane's certificate/key" \
  -out jjolie.p12
```

You now send the PKCS#12 file as attachment to the device, e.g. by e-mail. Note that for iOS, the file must have a `.otrp` extension so that it can be opened in OwnTracks.
You also send the CA certificate to the device and install it there, as discussed in [TLS](../features/tld.md)

### Android

Save the file
configure settings


### iOS

open the file in OwnTracks.

