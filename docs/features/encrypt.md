## Payload encryption

In addition to the [transport encryption via TLS](tls.md), the OwnTracks apps support payload encryption using [libsodium](https://github.com/jedisct1/libsodium/), in particular [secret-key authenticated encryption](https://download.libsodium.org/doc/secret-key_cryptography/authenticated_encryption.html). We have also implemented support for payload encryption (i.e. decryption) in the [OwnTracks Recorder](https://github.com/owntracks/recorder).

You configure a _secret_ (which you keep secret) in the app's settings. From this moment onwards, the apps encrypt the [JSON](../tech/json.md) with this secret and transport the payload in a new JSON object which looks like this:

```json
{
  "_type": "encrypted",
  "data": "1Vu7Owp ... W4lMnh28FB+el22GsCrlnggvEcp4H8GR9iKJdi1qfwkejYpzrQ+491Mwunjg="
}
```

If you want to share location with a friend, both you and your friend need to share the same _secret_. A _secret_ should be kept secret; it is an up to 32 octet long passphrase (which the apps pad with binary zeroes) which is required both for encryption as well as for decryption of the payloads.

### Recorder

You can set up multiple decryption keys on the Recorder (but not in the apps). This is to enable, say, different people to use different secrets on the same Recorder.

Add a key by determining the _username_ and the _device name_ of the client app. Lowercase these, and create a string with the two values separated with a dash. Suppose the _username_ is `jjolie` and the _device name_ is `phone`, the result will be `jjolie-phone`.

Load the key into the (running) Recorder:

```bash
echo "jjolie-phone mysecreTpass01" | ocat --load=keys
```

From this moment onwards, the Recorder will use this secret key for this username/device combination when it receives JSON payloads of `_type: encrypted`. The Recorder will decrypt the payload and will store the result in plain text in its storage.
