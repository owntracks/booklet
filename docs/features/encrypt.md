## Payload encryption

In addition to the [transport encryption via TLS](tls.md) (over MQTT and HTTPS), the OwnTracks apps support payload encryption using [libsodium](https://github.com/jedisct1/libsodium/), in particular [secret-key authenticated encryption][1]. We have also implemented support for payload encryption (i.e. decryption) in the [OwnTracks Recorder](https://github.com/owntracks/recorder).

You configure a _secret_ (which you keep secret) in the app's settings. From this moment onwards, the apps encrypt outgoing [JSON](../tech/json.md) with this secret and transport the payload in a new JSON object which looks like this:

```json
{
  "_type": "encrypted",
  "data": "1Vu7Owp ... W4lMnh28FB+el22GsCrlnggvEcp4H8GR9iKJdi1qfwkejYpzrQ+491Mwunjg="
}
```

If you want to share location with a friend, both you and your friend need to share the same _secret_. A _secret_ should be kept secret; it is an up to 32 octet long passphrase (which the apps pad with binary zeroes) which is required both for encryption as well as for decryption of the payloads. It is not possible to share locations with a friend's device using different _secret_ keys.

### Encryption key

For the OwnTracks apps, the encryption key can be configured as follows:

- Android and iOS via the settings UI / configuration editor
- Android and iOS also via [configuration file](remoteconfig.md) from the `encryptionKey` setting

The key is a string of up to 32 characters in length which the apps pad with binary zeroes.

### Recorder

You can set up multiple decryption keys on the Recorder (but not in the apps). This is to enable, say, different people to use different secrets on the same Recorder.

Add a key by determining the _username_ and the _device name_ of the client app. Lowercase these, and create a string with the two values separated with a dash. Suppose the _username_ is `jjolie` and the _device name_ is `Phone`, the result will be `jjolie-phone`.

Load the key into the (running) Recorder:

```bash
printf "jjolie-phone mysecreTpass01" | ocat --load=keys
```

From this moment onwards, the Recorder will use this secret key for this username/device combination when it receives JSON payloads of `_type: encrypted`. The Recorder will decrypt the payload and will store the result in plain text in its storage.

You can check which decryption keys you have stored by running:

```bash
ocat --dump=keys
```

You can also delete entries for specific devices by replacing the encryption key with `DELETE`:

```bash
printf "jjolie-phone DELETE" | ocat --load=keys
```

Be aware that this is a very powerful command that can corrupt your database if you write it incorrectly!

### Configuring encryption in Quicksetup

Using [quicksetup](../guide/quicksetup.md) you can optionally configure payload encryption for a user:

* configure the `friends` array with an element `secret` for that user:

        friends:
           - { tid: j2, username: jjolie, devicename: Phone, secret: "mysecreTpass01" }

* if you prefer to not have the secret in that configuration, write it into a file on the file system and specify the path in lieu of the actual secret, i.e. `secret: "/path/to/.jjolie.key"`

        $ printf "mysecreTpass01" > .jjolie.key

* after rerunning the `bootstrap` program, the user's `.otrc` file will have the secret configured:

        $ grep encryption /usr/local/owntracks/userdata/jjolie.otrc
        "encryptionKey": "mysecreTpass01",

* the Recorder's key store will also have the key:

        $ ocat --dump=keys
        jjolie-phone mysecreTpass01

Note that the secret is in clear text in the configuration file, the key file, the Recorder's key store, and in the `.otrc` file!

## Notes

- Payload encryption makes little sense for just one user on a shared installation. Assume Alice and Bob share a broker. Alice has configured payload encryption and her device publishes a location message. The Recorder receives that payload, decrypts it (assuming it too has Alice's key), and stores the result. Note that the Recorder will not automatically republish Alice's location, so Bob won't receive that data. In order to accomplish this, one would have to configure the Recorder to [republish using Lua](../tech/lua.md) those messages with the `_decrypted` element in them.
- In an environment consisting of a group of people who wish to communicate securely, payload encryption might make sense.

  [1]: https://libsodium.gitbook.io/doc/secret-key_cryptography/secretbox
