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

* Save the certificate to your device in an easy accessible location 
* Import the certificate into the app (Preferences, Connection, Security, Client certificate, Select) by opening it with the file choser. 
* The file will be copied to the secure storage location of the app. If the import is successfull, the original file can be removed from the device. 
* Specify the certificate password (certificates without password are not supported). 


### iOS

We recommend you proceed as follows:

1. Install the [TLS](tls.md) CA certificate in your system keystore by sending it (e.g. via e-mail) to your device and installing it in the system profile. (Click on the certificate and follow iOS' instructions.)
2. Send the prepared PKCS#12 file (with an `.otrp` extension) to your device, and open it. It will be imported into OwnTracks
3. Launch OwnTracks, select Settings and TLS. Select the `otrp` file you just imported as Client Certificate Filename, and below that, enter its passphrase.
4. Leave _Use Custom Security Policy_ disabled.
5. Verify the connection to your broker.

If need be, you can adjust a great number of parameters regarding how TLS connections will be verified: there are a number of settings available to fine-tune TLS connections between the app and the broker. The button _Use Custom Security Policy_ can be enabled to do so. This switch allows you to control your TLS settings more granularly. If not switched on, it uses the default iOS settings and uses the iOS keychain for certificate validation only.

* Mode
    - None; do not use pinned certificates to validate servers
    - Public Key; validate host (broker) certificates against the public keys of the pinned certificate
    - Certificate; validate host certificates against pinned certificates
* Pinned Server Certificate; select the certificate to use for pinning. Adding pinned SSL certificates to your app helps prevent man-in-the-middle attacks and other vulnerabilities. Applications dealing with sensitive customer data or financial information are strongly encouraged to route all communication over an SSL/TLS connection with SSL pinning configured and enabled.

* Validate Certificate Chain; if enabled, the entire TLS certificate chain is validated and not just the leaf (host) certificate if disabled.
* Allow untrusted Certificates should be disabled if possible. If the app doesn't have the CA certificate in its keychain, you'll have to allow untrusted certificates.
* Validate Domain Name; whether or not to validate the domain name in the certificate's `CN` field.

Note: on iOS self-signed certificates **require** _Custom Security Policy_ to be set. (There seems to be a problem we have not tracked down yet. Using Custom Security Policy once does mark a self signed certificate as valid until the app is restarted. This isn't reset even when Custom Security Policy is switched off afterwards.)

Client certificates are independent of the custom security policy settings.

If you wish to use certificate pinning, you must provide a DER-encoded file with an `.otre` extension to the device containing the pinned certificate.

```bash
openssl x509 \
   -in ca.crt \
   -out ca.otre \
   -outform DER
```

