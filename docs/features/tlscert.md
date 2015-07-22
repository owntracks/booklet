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


There are a number of settings available to fine-tune TLS connections between the app and the broker. The button _Use Custom Security Policy_ can be enabled to do so.

* Mode
    - None; do not use pinned certificates to validate servers
    - Public Key; validate host (broker) certificates against the public keys of the pinned certificate
    - Certificate; validate host certificates against pinned certificates
* Pinned Server Certificate; select the certificate to use for pinning. Adding pinned SSL certificates to your app helps prevent man-in-the-middle attacks and other vulnerabilities. Applications dealing with sensitive customer data or financial information are strongly encouraged to route all communication over an SSL/TLS connection with SSL pinning configured and enabled.

* Validate Certificate Chain; if enabled, the entire TLS certificate chain is validated and not just the leaf (host) certificate if disabled.
* Allow untrusted Certificates should be disabled if possible. If the app doesn't have the CA certificate in its keychain, you'll have to allow untrusted certificates.
* Validate Domain Name; whether or not to validate the domain name in the certificate's `CN` field.

If you wish to use certificate pinning, you must provide a DER-encoded file with an `.otre` extension to the device containing the pinned certificate.

