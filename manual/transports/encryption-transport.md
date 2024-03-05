# Encryption Transport

The Encryption Transport is not a transport itself, but it allows you to encrypt the data flowing through an existing transport.

### Technical details

The transport does an asymmetric key exchange via [ECDH](https://en.wikipedia.org/wiki/Elliptic-curve\_Diffie%E2%80%93Hellman) and [HKDF](https://en.wikipedia.org/wiki/HKDF)-SHA256 to safely derive a common 256-bit symmetric key, which is then used for [AES](https://en.wikipedia.org/wiki/Advanced\_Encryption\_Standard)-[GCM](https://en.wikipedia.org/wiki/Galois/Counter\_Mode) with a 96-bit nonce and 128-bit mac ([similarly used for TLS 1.3](https://en.wikipedia.org/wiki/Transport\_Layer\_Security#Cipher)). AES-GCM secures the data in-flight and checks the integrity on the receiving end with the help of the mac (a "checksum" of sorts)

The handshake process is entirely via unreliable and encryption is order/reliability agnostic, so will work with all kinds of transports. Once the handshake is complete, the connection is entirely secure. For complete [Man-In-The-Middle](https://en.wikipedia.org/wiki/Man-in-the-middle\_attack) security, at least one side needs to validate the public key (usually the client validates the server key).

### Usage

In order to use the transport, assuming you have a transport set up already, you simply add the transport to the "Inner" field and assign the EncryptionTransport to your NetworkManager

<figure><img src="../../.gitbook/assets/image (147).png" alt=""><figcaption><p>Basic inspector setup</p></figcaption></figure>



### Validating Server Public Key

Without validating the server public key, the initial handshake is vulnerable to MITM attacks.&#x20;

Although validating the server key might not be possible in all hosting modes (especially player hosted games may be difficult), it is still recommended to add to your game if possible.

Out of the box, we provide 3 modes of validation:

#### Off

Nothing is validated, this is the default. Good for development and where validation is simply not possible.

#### List

Public keys are validated from a list of trusted keys that are "baked in" to the build.

This mode is good for games that host servers for their players and the easiest to use, simply pre-generate a key file to load on the server which will automatically add it to the list.

<figure><img src="../../.gitbook/assets/image (149).png" alt=""><figcaption><p>Inspector showing Generate Key Pair Button</p></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (148).png" alt=""><figcaption><p>Dialogue to automatically add key to the trusted list</p></figcaption></figure>

#### Callback

In this mode, when a client connects to a server, it will call the `EncryptionTransport.onClientValidateServerPubKey` callback which is expected to return `true`for a valid key.

This allows for the most flexibility and allows you to validate public keys by exchanging them before connecting, for example from the lobby service, or a login api.

Once a client or server is running, the fingerprint can be retrieved from `EncryptionTransport.EncryptionPublicKeyFingerprint` (and the serialized public key itself from`EncryptionTransport.EncryptionPublicKey`).

### Performance

The transport currently uses BouncyCastle for all cryptography, which is very heavy on allocations. We are planning to address this in a future update through both, improving BouncyCastle itself and providing a native library for most platforms to do the bulk of the work, the AES en-/decryption.
