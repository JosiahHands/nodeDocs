[Contents](../README.md) | [Previous: Crypto, part 1 of 6](15-crypto-part1.md) | [Next: Crypto, part 3 of 6](15-crypto-part3.md)

Source: [https://nodejs.org/docs/latest/api/crypto.html](https://nodejs.org/docs/latest/api/crypto.html)

Part 2 of 6 of this section.

# Crypto (part 2 of 6)

### `diffieHellman.getPublicKey([encoding])`

Added in: v0.5.0

- `encoding` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) The [encoding](07-buffer-part1.md#buffers-and-character-encodings) of the return value.
- Returns: [`<Buffer>`](07-buffer-part1.md#class-buffer) | [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type)

Returns the Diffie-Hellman public key in the specified `encoding`.
If `encoding` is provided a
string is returned; otherwise a [`Buffer`](07-buffer-part1.md) is returned.

### `diffieHellman.setPrivateKey(privateKey[, encoding])`

Added in: v0.5.0

- `privateKey` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) | [`<ArrayBuffer>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/ArrayBuffer) | [`<Buffer>`](07-buffer-part1.md#class-buffer) | [`<TypedArray>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/TypedArray) | [`<DataView>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/DataView)
- `encoding` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) The [encoding](07-buffer-part1.md#buffers-and-character-encodings) of the `privateKey` string.

Sets the Diffie-Hellman private key. If the `encoding` argument is provided,
`privateKey` is expected
to be a string. If no `encoding` is provided, `privateKey` is expected
to be a [`Buffer`](07-buffer-part1.md), `TypedArray`, or `DataView`.

This function does not automatically compute the associated public key. Either
[`diffieHellman.setPublicKey()`](#diffiehellmansetpublickeypublickey-encoding) or [`diffieHellman.generateKeys()`](15-crypto-part1.md#diffiehellmangeneratekeysencoding) can be
used to manually provide the public key or to automatically derive it.

### `diffieHellman.setPublicKey(publicKey[, encoding])`

Added in: v0.5.0

- `publicKey` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) | [`<ArrayBuffer>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/ArrayBuffer) | [`<Buffer>`](07-buffer-part1.md#class-buffer) | [`<TypedArray>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/TypedArray) | [`<DataView>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/DataView)
- `encoding` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) The [encoding](07-buffer-part1.md#buffers-and-character-encodings) of the `publicKey` string.

Sets the Diffie-Hellman public key. If the `encoding` argument is provided,
`publicKey` is expected
to be a string. If no `encoding` is provided, `publicKey` is expected
to be a [`Buffer`](07-buffer-part1.md), `TypedArray`, or `DataView`.

### `diffieHellman.verifyError`

Added in: v0.11.12

A bit field containing any warnings and/or errors resulting from a check
performed during initialization of the `DiffieHellman` object.

The following values are valid for this property (as defined in `node:constants` module):

- `DH_CHECK_P_NOT_SAFE_PRIME`
- `DH_CHECK_P_NOT_PRIME`
- `DH_UNABLE_TO_CHECK_GENERATOR`
- `DH_NOT_SUITABLE_GENERATOR`

## Class: `DiffieHellmanGroup`

Added in: v0.7.5

The `DiffieHellmanGroup` class takes a well-known modp group as its argument.
It works the same as `DiffieHellman`, except that it does not allow changing
its keys after creation. In other words, it does not implement `setPublicKey()`
or `setPrivateKey()` methods.

*ES module version:*

```js
const { createDiffieHellmanGroup } = await import('node:crypto');
const dh = createDiffieHellmanGroup('modp16');
```

*CommonJS version:*

```js
const { createDiffieHellmanGroup } = require('node:crypto');
const dh = createDiffieHellmanGroup('modp16');
```

The following groups are supported:

- `'modp14'` (2048 bits, [RFC 3526](https://www.rfc-editor.org/rfc/rfc3526.txt) Section 3)
- `'modp15'` (3072 bits, [RFC 3526](https://www.rfc-editor.org/rfc/rfc3526.txt) Section 4)
- `'modp16'` (4096 bits, [RFC 3526](https://www.rfc-editor.org/rfc/rfc3526.txt) Section 5)
- `'modp17'` (6144 bits, [RFC 3526](https://www.rfc-editor.org/rfc/rfc3526.txt) Section 6)
- `'modp18'` (8192 bits, [RFC 3526](https://www.rfc-editor.org/rfc/rfc3526.txt) Section 7)

The following groups are still supported but deprecated (see [Caveats](15-crypto-part6.md#support-for-weak-or-compromised-algorithms)):

- `'modp1'` (768 bits, [RFC 2409](https://www.rfc-editor.org/rfc/rfc2409.txt) Section 6.1)
- `'modp2'` (1024 bits, [RFC 2409](https://www.rfc-editor.org/rfc/rfc2409.txt) Section 6.2)
- `'modp5'` (1536 bits, [RFC 3526](https://www.rfc-editor.org/rfc/rfc3526.txt) Section 2)

These deprecated groups might be removed in future versions of Node.js.

## Class: `ECDH`

Added in: v0.11.14

The `ECDH` class is a utility for creating Elliptic Curve Diffie-Hellman (ECDH)
key exchanges.

Instances of the `ECDH` class can be created using the
[`crypto.createECDH()`](15-crypto-part3.md#cryptocreateecdhcurvename) function.

*ES module version:*

```js
import assert from 'node:assert';

const {
  createECDH,
} = await import('node:crypto');

// Generate Alice's keys...
const alice = createECDH('secp521r1');
const aliceKey = alice.generateKeys();

// Generate Bob's keys...
const bob = createECDH('secp521r1');
const bobKey = bob.generateKeys();

// Exchange and generate the secret...
const aliceSecret = alice.computeSecret(bobKey);
const bobSecret = bob.computeSecret(aliceKey);

assert.strictEqual(aliceSecret.toString('hex'), bobSecret.toString('hex'));
// OK
```

*CommonJS version:*

```js
const assert = require('node:assert');

const {
  createECDH,
} = require('node:crypto');

// Generate Alice's keys...
const alice = createECDH('secp521r1');
const aliceKey = alice.generateKeys();

// Generate Bob's keys...
const bob = createECDH('secp521r1');
const bobKey = bob.generateKeys();

// Exchange and generate the secret...
const aliceSecret = alice.computeSecret(bobKey);
const bobSecret = bob.computeSecret(aliceKey);

assert.strictEqual(aliceSecret.toString('hex'), bobSecret.toString('hex'));
// OK
```

### Static method: `ECDH.convertKey(key, curve[, inputEncoding[, outputEncoding[, format]]])`

Added in: v10.0.0

- `key` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) | [`<ArrayBuffer>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/ArrayBuffer) | [`<Buffer>`](07-buffer-part1.md#class-buffer) | [`<TypedArray>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/TypedArray) | [`<DataView>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/DataView)
- `curve` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type)
- `inputEncoding` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) The [encoding](07-buffer-part1.md#buffers-and-character-encodings) of the `key` string.
- `outputEncoding` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) The [encoding](07-buffer-part1.md#buffers-and-character-encodings) of the return value.
- `format` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) **Default:** `'uncompressed'`
- Returns: [`<Buffer>`](07-buffer-part1.md#class-buffer) | [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type)

Converts the EC Diffie-Hellman public key specified by `key` and `curve` to the
format specified by `format`. The `format` argument specifies point encoding
and can be `'compressed'`, `'uncompressed'` or `'hybrid'`. The supplied key is
interpreted using the specified `inputEncoding`, and the returned key is encoded
using the specified `outputEncoding`.

Use [`crypto.getCurves()`](15-crypto-part4.md#cryptogetcurves) to obtain a list of available curve names.
On recent OpenSSL releases, `openssl ecparam -list_curves` will also display
the name and description of each available elliptic curve.

If `format` is not specified the point will be returned in `'uncompressed'`
format.

If the `inputEncoding` is not provided, `key` is expected to be a [`Buffer`](07-buffer-part1.md),
`TypedArray`, or `DataView`.

Example (uncompressing a key):

*ES module version:*

```js
const {
  createECDH,
  ECDH,
} = await import('node:crypto');

const ecdh = createECDH('secp256k1');
ecdh.generateKeys();

const compressedKey = ecdh.getPublicKey('hex', 'compressed');

const uncompressedKey = ECDH.convertKey(compressedKey,
                                        'secp256k1',
                                        'hex',
                                        'hex',
                                        'uncompressed');

// The converted key and the uncompressed public key should be the same
console.log(uncompressedKey === ecdh.getPublicKey('hex'));
```

*CommonJS version:*

```js
const {
  createECDH,
  ECDH,
} = require('node:crypto');

const ecdh = createECDH('secp256k1');
ecdh.generateKeys();

const compressedKey = ecdh.getPublicKey('hex', 'compressed');

const uncompressedKey = ECDH.convertKey(compressedKey,
                                        'secp256k1',
                                        'hex',
                                        'hex',
                                        'uncompressed');

// The converted key and the uncompressed public key should be the same
console.log(uncompressedKey === ecdh.getPublicKey('hex'));
```

### `ecdh.computeSecret(otherPublicKey[, inputEncoding][, outputEncoding])`

Added in: v0.11.14

**History**

| Version | Changes |
| --- | --- |
| v10.0.0 | Changed error format to better support invalid public key error. |
| v6.0.0 | The default `inputEncoding` changed from `binary` to `utf8`. |

- `otherPublicKey` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) | [`<ArrayBuffer>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/ArrayBuffer) | [`<Buffer>`](07-buffer-part1.md#class-buffer) | [`<TypedArray>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/TypedArray) | [`<DataView>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/DataView)
- `inputEncoding` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) The [encoding](07-buffer-part1.md#buffers-and-character-encodings) of the `otherPublicKey` string.
- `outputEncoding` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) The [encoding](07-buffer-part1.md#buffers-and-character-encodings) of the return value.
- Returns: [`<Buffer>`](07-buffer-part1.md#class-buffer) | [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type)

Computes the shared secret using `otherPublicKey` as the other
party's public key and returns the computed shared secret. The supplied
key is interpreted using specified `inputEncoding`, and the returned secret
is encoded using the specified `outputEncoding`.
If the `inputEncoding` is not
provided, `otherPublicKey` is expected to be a [`Buffer`](07-buffer-part1.md), `TypedArray`, or
`DataView`.

If `outputEncoding` is given a string will be returned; otherwise a
[`Buffer`](07-buffer-part1.md) is returned.

`ecdh.computeSecret` will throw an
`ERR_CRYPTO_ECDH_INVALID_PUBLIC_KEY` error when `otherPublicKey`
lies outside of the elliptic curve. Since `otherPublicKey` is
usually supplied from a remote user over an insecure network,
be sure to handle this exception accordingly.

### `ecdh.generateKeys([encoding[, format]])`

Added in: v0.11.14

- `encoding` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) The [encoding](07-buffer-part1.md#buffers-and-character-encodings) of the return value.
- `format` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) **Default:** `'uncompressed'`
- Returns: [`<Buffer>`](07-buffer-part1.md#class-buffer) | [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type)

Generates private and public EC Diffie-Hellman key values, and returns
the public key in the specified `format` and `encoding`. This key should be
transferred to the other party.

The `format` argument specifies point encoding and can be `'compressed'` or
`'uncompressed'`. If `format` is not specified, the point will be returned in
`'uncompressed'` format.

If `encoding` is provided a string is returned; otherwise a [`Buffer`](07-buffer-part1.md)
is returned.

### `ecdh.getPrivateKey([encoding])`

Added in: v0.11.14

- `encoding` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) The [encoding](07-buffer-part1.md#buffers-and-character-encodings) of the return value.
- Returns: [`<Buffer>`](07-buffer-part1.md#class-buffer) | [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) The EC Diffie-Hellman in the specified `encoding`.

If `encoding` is specified, a string is returned; otherwise a [`Buffer`](07-buffer-part1.md) is
returned.

### `ecdh.getPublicKey([encoding][, format])`

Added in: v0.11.14

- `encoding` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) The [encoding](07-buffer-part1.md#buffers-and-character-encodings) of the return value.
- `format` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) **Default:** `'uncompressed'`
- Returns: [`<Buffer>`](07-buffer-part1.md#class-buffer) | [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) The EC Diffie-Hellman public key in the specified `encoding` and `format`.

The `format` argument specifies point encoding and can be `'compressed'` or
`'uncompressed'`. If `format` is not specified the point will be returned in
`'uncompressed'` format.

If `encoding` is specified, a string is returned; otherwise a [`Buffer`](07-buffer-part1.md) is
returned.

### `ecdh.setPrivateKey(privateKey[, encoding])`

Added in: v0.11.14

- `privateKey` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) | [`<ArrayBuffer>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/ArrayBuffer) | [`<Buffer>`](07-buffer-part1.md#class-buffer) | [`<TypedArray>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/TypedArray) | [`<DataView>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/DataView)
- `encoding` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) The [encoding](07-buffer-part1.md#buffers-and-character-encodings) of the `privateKey` string.

Sets the EC Diffie-Hellman private key.
If `encoding` is provided, `privateKey` is expected
to be a string; otherwise `privateKey` is expected to be a [`Buffer`](07-buffer-part1.md),
`TypedArray`, or `DataView`.

If `privateKey` is not valid for the curve specified when the `ECDH` object was
created, an error is thrown. Upon setting the private key, the associated
public point (key) is also generated and set in the `ECDH` object.

### `ecdh.setPublicKey(publicKey[, encoding])`

Added in: v0.11.14Deprecated in: v5.2.0

Stability: 0 - Deprecated

- `publicKey` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) | [`<ArrayBuffer>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/ArrayBuffer) | [`<Buffer>`](07-buffer-part1.md#class-buffer) | [`<TypedArray>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/TypedArray) | [`<DataView>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/DataView)
- `encoding` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) The [encoding](07-buffer-part1.md#buffers-and-character-encodings) of the `publicKey` string.

Sets the EC Diffie-Hellman public key.
If `encoding` is provided `publicKey` is expected to
be a string; otherwise a [`Buffer`](07-buffer-part1.md), `TypedArray`, or `DataView` is expected.

There is not normally a reason to call this method because `ECDH`
only requires a private key and the other party's public key to compute the
shared secret. Typically either [`ecdh.generateKeys()`](#ecdhgeneratekeysencoding-format) or
[`ecdh.setPrivateKey()`](#ecdhsetprivatekeyprivatekey-encoding) will be called. The [`ecdh.setPrivateKey()`](#ecdhsetprivatekeyprivatekey-encoding) method
attempts to generate the public point/key associated with the private key being
set.

Example (obtaining a shared secret):

*ES module version:*

```js
const {
  createECDH,
  createHash,
} = await import('node:crypto');

const alice = createECDH('secp256k1');
const bob = createECDH('secp256k1');

// This is a shortcut way of specifying one of Alice's previous private
// keys. It would be unwise to use such a predictable private key in a real
// application.
alice.setPrivateKey(
  createHash('sha256').update('alice', 'utf8').digest(),
);

// Bob uses a newly generated cryptographically strong
// pseudorandom key pair
bob.generateKeys();

const aliceSecret = alice.computeSecret(bob.getPublicKey(), null, 'hex');
const bobSecret = bob.computeSecret(alice.getPublicKey(), null, 'hex');

// aliceSecret and bobSecret should be the same shared secret value
console.log(aliceSecret === bobSecret);
```

*CommonJS version:*

```js
const {
  createECDH,
  createHash,
} = require('node:crypto');

const alice = createECDH('secp256k1');
const bob = createECDH('secp256k1');

// This is a shortcut way of specifying one of Alice's previous private
// keys. It would be unwise to use such a predictable private key in a real
// application.
alice.setPrivateKey(
  createHash('sha256').update('alice', 'utf8').digest(),
);

// Bob uses a newly generated cryptographically strong
// pseudorandom key pair
bob.generateKeys();

const aliceSecret = alice.computeSecret(bob.getPublicKey(), null, 'hex');
const bobSecret = bob.computeSecret(alice.getPublicKey(), null, 'hex');

// aliceSecret and bobSecret should be the same shared secret value
console.log(aliceSecret === bobSecret);
```

## Class: `Hash`

Added in: v0.1.92

- Extends: [`<stream.Transform>`](51-stream-part2.md#class-streamtransform)

The `Hash` class is a utility for creating hash digests of data. It can be
used in one of two ways:

- As a [stream](51-stream-part1.md) that is both readable and writable, where data is written
  to produce a computed hash digest on the readable side, or
- Using the [`hash.update()`](#hashupdatedata-inputencoding) and [`hash.digest()`](#hashdigestencoding) methods to produce the
  computed hash.

The [`crypto.createHash()`](15-crypto-part3.md#cryptocreatehashalgorithm-options) method is used to create `Hash` instances. `Hash`
objects are not to be created directly using the `new` keyword.

Example: Using `Hash` objects as streams:

*ES module version:*

```js
const {
  createHash,
} = await import('node:crypto');

const hash = createHash('sha256');

hash.on('readable', () => {
  // Only one element is going to be produced by the
  // hash stream.
  const data = hash.read();
  if (data) {
    console.log(data.toString('hex'));
    // Prints:
    //   6a2da20943931e9834fc12cfe5bb47bbd9ae43489a30726962b576f4e3993e50
  }
});

hash.write('some data to hash');
hash.end();
```

*CommonJS version:*

```js
const {
  createHash,
} = require('node:crypto');

const hash = createHash('sha256');

hash.on('readable', () => {
  // Only one element is going to be produced by the
  // hash stream.
  const data = hash.read();
  if (data) {
    console.log(data.toString('hex'));
    // Prints:
    //   6a2da20943931e9834fc12cfe5bb47bbd9ae43489a30726962b576f4e3993e50
  }
});

hash.write('some data to hash');
hash.end();
```

Example: Using `Hash` and piped streams:

*ES module version:*

```js
import { createReadStream } from 'node:fs';
import { stdout } from 'node:process';
const { createHash } = await import('node:crypto');

const hash = createHash('sha256');

const input = createReadStream('test.js');
input.pipe(hash).setEncoding('hex').pipe(stdout);
```

*CommonJS version:*

```js
const { createReadStream } = require('node:fs');
const { createHash } = require('node:crypto');
const { stdout } = require('node:process');

const hash = createHash('sha256');

const input = createReadStream('test.js');
input.pipe(hash).setEncoding('hex').pipe(stdout);
```

Example: Using the [`hash.update()`](#hashupdatedata-inputencoding) and [`hash.digest()`](#hashdigestencoding) methods:

*ES module version:*

```js
const {
  createHash,
} = await import('node:crypto');

const hash = createHash('sha256');

hash.update('some data to hash');
console.log(hash.digest('hex'));
// Prints:
//   6a2da20943931e9834fc12cfe5bb47bbd9ae43489a30726962b576f4e3993e50
```

*CommonJS version:*

```js
const {
  createHash,
} = require('node:crypto');

const hash = createHash('sha256');

hash.update('some data to hash');
console.log(hash.digest('hex'));
// Prints:
//   6a2da20943931e9834fc12cfe5bb47bbd9ae43489a30726962b576f4e3993e50
```

### `hash.copy([options])`

Added in: v13.1.0

- `options` [`<Object>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Object) [`stream.transform` options](51-stream-part3.md#new-streamtransformoptions)
- Returns: [`<Hash>`](15-crypto-part2.md#class-hash)

Creates a new `Hash` object that contains a deep copy of the internal state
of the current `Hash` object.

The optional `options` argument controls stream behavior. For XOF hash
functions such as `'shake256'`, the `outputLength` option can be used to
specify the desired output length in bytes.

An error is thrown when an attempt is made to copy the `Hash` object after
its [`hash.digest()`](#hashdigestencoding) method has been called.

*ES module version:*

```js
// Calculate a rolling hash.
const {
  createHash,
} = await import('node:crypto');

const hash = createHash('sha256');

hash.update('one');
console.log(hash.copy().digest('hex'));

hash.update('two');
console.log(hash.copy().digest('hex'));

hash.update('three');
console.log(hash.copy().digest('hex'));

// Etc.
```

*CommonJS version:*

```js
// Calculate a rolling hash.
const {
  createHash,
} = require('node:crypto');

const hash = createHash('sha256');

hash.update('one');
console.log(hash.copy().digest('hex'));

hash.update('two');
console.log(hash.copy().digest('hex'));

hash.update('three');
console.log(hash.copy().digest('hex'));

// Etc.
```

### `hash.digest([encoding])`

Added in: v0.1.92

- `encoding` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) The [encoding](07-buffer-part1.md#buffers-and-character-encodings) of the return value.
- Returns: [`<Buffer>`](07-buffer-part1.md#class-buffer) | [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type)

Calculates the digest of all of the data passed to be hashed (using the
[`hash.update()`](#hashupdatedata-inputencoding) method).
If `encoding` is provided a string will be returned; otherwise
a [`Buffer`](07-buffer-part1.md) is returned.

The `Hash` object can not be used again after `hash.digest()` method has been
called. Multiple calls will cause an error to be thrown.

### `hash.update(data[, inputEncoding])`

Added in: v0.1.92

**History**

| Version | Changes |
| --- | --- |
| v6.0.0 | The default `inputEncoding` changed from `binary` to `utf8`. |

- `data` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) | [`<Buffer>`](07-buffer-part1.md#class-buffer) | [`<TypedArray>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/TypedArray) | [`<DataView>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/DataView)
- `inputEncoding` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) The [encoding](07-buffer-part1.md#buffers-and-character-encodings) of the `data` string.

Updates the hash content with the given `data`, the encoding of which
is given in `inputEncoding`.
If `encoding` is not provided, and the `data` is a string, an
encoding of `'utf8'` is enforced. If `data` is a [`Buffer`](07-buffer-part1.md), `TypedArray`, or
`DataView`, then `inputEncoding` is ignored.

This can be called many times with new data as it is streamed.

## Class: `Hmac`

Added in: v0.1.94

- Extends: [`<stream.Transform>`](51-stream-part2.md#class-streamtransform)

The `Hmac` class is a utility for creating cryptographic HMAC digests. It can
be used in one of two ways:

- As a [stream](51-stream-part1.md) that is both readable and writable, where data is written
  to produce a computed HMAC digest on the readable side, or
- Using the [`hmac.update()`](#hmacupdatedata-inputencoding) and [`hmac.digest()`](#hmacdigestencoding) methods to produce the
  computed HMAC digest.

The [`crypto.createHmac()`](15-crypto-part3.md#cryptocreatehmacalgorithm-key-options) method is used to create `Hmac` instances. `Hmac`
objects are not to be created directly using the `new` keyword.

Example: Using `Hmac` objects as streams:

*ES module version:*

```js
const {
  createHmac,
} = await import('node:crypto');

const hmac = createHmac('sha256', 'a secret');

hmac.on('readable', () => {
  // Only one element is going to be produced by the
  // hash stream.
  const data = hmac.read();
  if (data) {
    console.log(data.toString('hex'));
    // Prints:
    //   7fd04df92f636fd450bc841c9418e5825c17f33ad9c87c518115a45971f7f77e
  }
});

hmac.write('some data to hash');
hmac.end();
```

*CommonJS version:*

```js
const {
  createHmac,
} = require('node:crypto');

const hmac = createHmac('sha256', 'a secret');

hmac.on('readable', () => {
  // Only one element is going to be produced by the
  // hash stream.
  const data = hmac.read();
  if (data) {
    console.log(data.toString('hex'));
    // Prints:
    //   7fd04df92f636fd450bc841c9418e5825c17f33ad9c87c518115a45971f7f77e
  }
});

hmac.write('some data to hash');
hmac.end();
```

Example: Using `Hmac` and piped streams:

*ES module version:*

```js
import { createReadStream } from 'node:fs';
import { stdout } from 'node:process';
const {
  createHmac,
} = await import('node:crypto');

const hmac = createHmac('sha256', 'a secret');

const input = createReadStream('test.js');
input.pipe(hmac).pipe(stdout);
```

*CommonJS version:*

```js
const {
  createReadStream,
} = require('node:fs');
const {
  createHmac,
} = require('node:crypto');
const { stdout } = require('node:process');

const hmac = createHmac('sha256', 'a secret');

const input = createReadStream('test.js');
input.pipe(hmac).pipe(stdout);
```

Example: Using the [`hmac.update()`](#hmacupdatedata-inputencoding) and [`hmac.digest()`](#hmacdigestencoding) methods:

*ES module version:*

```js
const {
  createHmac,
} = await import('node:crypto');

const hmac = createHmac('sha256', 'a secret');

hmac.update('some data to hash');
console.log(hmac.digest('hex'));
// Prints:
//   7fd04df92f636fd450bc841c9418e5825c17f33ad9c87c518115a45971f7f77e
```

*CommonJS version:*

```js
const {
  createHmac,
} = require('node:crypto');

const hmac = createHmac('sha256', 'a secret');

hmac.update('some data to hash');
console.log(hmac.digest('hex'));
// Prints:
//   7fd04df92f636fd450bc841c9418e5825c17f33ad9c87c518115a45971f7f77e
```

### `hmac.digest([encoding])`

Added in: v0.1.94

- `encoding` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) The [encoding](07-buffer-part1.md#buffers-and-character-encodings) of the return value.
- Returns: [`<Buffer>`](07-buffer-part1.md#class-buffer) | [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type)

Calculates the HMAC digest of all of the data passed using [`hmac.update()`](#hmacupdatedata-inputencoding).
If `encoding` is
provided a string is returned; otherwise a [`Buffer`](07-buffer-part1.md) is returned;

The `Hmac` object can not be used again after `hmac.digest()` has been
called. Multiple calls to `hmac.digest()` will result in an error being thrown.

### `hmac.update(data[, inputEncoding])`

Added in: v0.1.94

**History**

| Version | Changes |
| --- | --- |
| v6.0.0 | The default `inputEncoding` changed from `binary` to `utf8`. |

- `data` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) | [`<Buffer>`](07-buffer-part1.md#class-buffer) | [`<TypedArray>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/TypedArray) | [`<DataView>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/DataView)
- `inputEncoding` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) The [encoding](07-buffer-part1.md#buffers-and-character-encodings) of the `data` string.

Updates the `Hmac` content with the given `data`, the encoding of which
is given in `inputEncoding`.
If `encoding` is not provided, and the `data` is a string, an
encoding of `'utf8'` is enforced. If `data` is a [`Buffer`](07-buffer-part1.md), `TypedArray`, or
`DataView`, then `inputEncoding` is ignored.

This can be called many times with new data as it is streamed.

## Class: `KeyObject`

Added in: v11.6.0

**History**

| Version | Changes |
| --- | --- |
| v24.6.0 | Add support for ML-DSA keys. |
| v14.5.0, v12.19.0 | Instances of this class can now be passed to worker threads using `postMessage`. |
| v11.13.0 | This class is now exported. |

Node.js uses a `KeyObject` class to represent a symmetric or asymmetric key,
and each kind of key exposes different functions. The
[`crypto.createSecretKey()`](15-crypto-part4.md#cryptocreatesecretkeykey-encoding), [`crypto.createPublicKey()`](15-crypto-part4.md#cryptocreatepublickeykey) and
[`crypto.createPrivateKey()`](15-crypto-part3.md#cryptocreateprivatekeykey) methods are used to create `KeyObject`
instances. `KeyObject` objects are not to be created directly using the `new`
keyword.

Most applications should consider using the new `KeyObject` API instead of
passing keys as strings or `Buffer`s due to improved security features.

`KeyObject` instances can be passed to other threads via [`postMessage()`](67-worker-threads.md#portpostmessagevalue-transferlist).
The receiver obtains a cloned `KeyObject`, and the `KeyObject` does not need to
be listed in the `transferList` argument.

### Static method: `KeyObject.from(key)`

Added in: v15.0.0

**History**

| Version | Changes |
| --- | --- |
| v26.0.0 | Passing a non-extractable CryptoKey as `key` is deprecated. |

- `key` [`<CryptoKey>`](65-webcrypto.md#class-cryptokey)
- Returns: [`<KeyObject>`](15-crypto-part2.md#class-keyobject)

Returns the underlying [`<KeyObject>`](15-crypto-part2.md#class-keyobject) of a [`<CryptoKey>`](65-webcrypto.md#class-cryptokey). The returned [`<KeyObject>`](15-crypto-part2.md#class-keyobject)
does not retain any of the restrictions imposed by the Web Crypto API on the
original [`<CryptoKey>`](65-webcrypto.md#class-cryptokey), such as the allowed key usages, the algorithm or hash
algorithm bindings, and the extractability flag. In particular, the underlying
key material of the returned [`<KeyObject>`](15-crypto-part2.md#class-keyobject) can always be exported.

*ES module version:*

```js
const { KeyObject } = await import('node:crypto');
const { subtle } = globalThis.crypto;

const key = await subtle.generateKey({
  name: 'HMAC',
  hash: 'SHA-256',
  length: 256,
}, true, ['sign', 'verify']);

const keyObject = KeyObject.from(key);
console.log(keyObject.symmetricKeySize);
// Prints: 32 (symmetric key size in bytes)
```

*CommonJS version:*

```js
const { KeyObject } = require('node:crypto');
const { subtle } = globalThis.crypto;

(async function() {
  const key = await subtle.generateKey({
    name: 'HMAC',
    hash: 'SHA-256',
    length: 256,
  }, true, ['sign', 'verify']);

  const keyObject = KeyObject.from(key);
  console.log(keyObject.symmetricKeySize);
  // Prints: 32 (symmetric key size in bytes)
})();
```

### `keyObject.asymmetricKeyDetails`

Added in: v15.7.0

**History**

| Version | Changes |
| --- | --- |
| v16.9.0 | Expose `RSASSA-PSS-params` sequence parameters for RSA-PSS keys. |

- Type: [`<Object>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Object)
  - `modulusLength` [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) Key size in bits (RSA, DSA).
  - `publicExponent` [`<bigint>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#bigint_type) Public exponent (RSA).
  - `hashAlgorithm` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) Name of the message digest (RSA-PSS).
  - `mgf1HashAlgorithm` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) Name of the message digest used by
    MGF1 (RSA-PSS).
  - `saltLength` [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) Minimal salt length in bytes (RSA-PSS).
  - `divisorLength` [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) Size of `q` in bits (DSA).
  - `namedCurve` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) Name of the curve (EC).

This property exists only on asymmetric keys. Depending on the type of the key,
this object contains information about the key. None of the information obtained
through this property can be used to uniquely identify a key or to compromise
the security of the key.

For RSA-PSS keys, if the key material contains a `RSASSA-PSS-params` sequence,
the `hashAlgorithm`, `mgf1HashAlgorithm`, and `saltLength` properties will be
set.

Other key details might be exposed via this API using additional attributes.

### `keyObject.asymmetricKeyType`

Added in: v11.6.0

**History**

| Version | Changes |
| --- | --- |
| v24.8.0 | Add support for SLH-DSA keys. |
| v24.7.0 | Add support for ML-KEM keys. |
| v24.6.0 | Add support for ML-DSA keys. |
| v13.9.0, v12.17.0 | Added support for `'dh'`. |
| v12.0.0 | Added support for `'rsa-pss'`. |
| v12.0.0 | This property now returns `undefined` for KeyObject instances of unrecognized type instead of aborting. |
| v12.0.0 | Added support for `'x25519'` and `'x448'`. |
| v12.0.0 | Added support for `'ed25519'` and `'ed448'`. |

- Type: [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type)

For asymmetric keys, this property represents the type of the key. See the
supported [asymmetric key types](15-crypto-part1.md#asymmetric-key-types).

This property is `undefined` for unrecognized `KeyObject` types and symmetric
keys.

### `keyObject.equals(otherKeyObject)`

Added in: v17.7.0, v16.15.0

- `otherKeyObject` [`<KeyObject>`](15-crypto-part2.md#class-keyobject) A `KeyObject` with which to
  compare `keyObject`.
- Returns: [`<boolean>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#boolean_type)

Returns `true` or `false` depending on whether the keys have exactly the same
type, value, and parameters. This method is not
[constant time](https://en.wikipedia.org/wiki/Timing_attack).

### `keyObject.export([options])`

Added in: v11.6.0

**History**

| Version | Changes |
| --- | --- |
| v26.1.0 | Added JWK format support for ML-KEM and SLH-DSA key types. |
| v26.0.0 | Added support for `'raw-public'`, `'raw-private'`, and `'raw-seed'` formats. |
| v26.0.0 | ML-KEM and ML-DSA private key `'pkcs8'` export now uses seed-only format by default when a seed is available. |
| v15.9.0 | Added support for `'jwk'` format. |

- `options` [`<Object>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Object)
- Returns: [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) | [`<Buffer>`](07-buffer-part1.md#class-buffer) | [`<Object>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Object)

For symmetric keys, the following encoding options can be used:

- `format` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) Must be `'buffer'` (default) or `'jwk'`.

For public keys, the following encoding options can be used:

- `format` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) Must be `'pem'`, `'der'`, `'jwk'`, or `'raw-public'`.
  See [asymmetric key types](15-crypto-part1.md#asymmetric-key-types) for format support.
- `type` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) When `format` is `'pem'` or `'der'`, must be `'pkcs1'`
  (RSA only) or `'spki'`. For EC keys with `'raw-public'` format, may be
  `'uncompressed'` (default) or `'compressed'`. Ignored when `format` is
  `'jwk'`.

For private keys, the following encoding options can be used:

- `format` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) Must be `'pem'`, `'der'`, `'jwk'`, `'raw-private'`,
  or `'raw-seed'`. See [asymmetric key types](15-crypto-part1.md#asymmetric-key-types) for format support.
- `type` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) When `format` is `'pem'` or `'der'`, must be `'pkcs1'`
  (RSA only), `'pkcs8'`, or `'sec1'` (EC only). Ignored when `format` is
  `'jwk'`, `'raw-private'`, or `'raw-seed'`.
- `cipher` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) If specified, the private key will be encrypted with
  the given `cipher` and `passphrase` using PKCS#5 v2.0 password based
  encryption. Ignored when `format` is `'jwk'`, `'raw-private'`, or
  `'raw-seed'`.
- `passphrase` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) | [`<Buffer>`](07-buffer-part1.md#class-buffer) The passphrase to use for encryption.
  Required when `cipher` is specified.

The result type depends on the selected encoding format, when PEM the
result is a string, when DER it will be a buffer containing the data
encoded as DER, when [JWK](https://tools.ietf.org/html/rfc7517) it will be an object. Raw formats return a
[`<Buffer>`](07-buffer-part1.md#class-buffer) containing the raw key material.

Private keys can be encrypted by specifying a `cipher` and `passphrase`.
The PKCS#8 `type` supports encryption with both PEM and DER `format` for any
key algorithm. PKCS#1 and SEC1 can only be encrypted when the PEM `format` is
used. For maximum compatibility, use PKCS#8 for encrypted private keys. Since
PKCS#8 defines its own encryption mechanism, PEM-level encryption is not
supported when encrypting a PKCS#8 key. See [RFC 5208](https://www.rfc-editor.org/rfc/rfc5208.txt) for PKCS#8 encryption
and [RFC 1421](https://www.rfc-editor.org/rfc/rfc1421.txt) for PKCS#1 and SEC1 encryption.

### `keyObject.symmetricKeySize`

Added in: v11.6.0

- Type: [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type)

For secret keys, this property represents the size of the key in bytes. This
property is `undefined` for asymmetric keys.

### `keyObject.toCryptoKey(algorithm, extractable, keyUsages)`

Added in: v23.0.0, v22.10.0

- `algorithm` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) | [`<Algorithm>`](65-webcrypto.md#class-algorithm) | [`<RsaHashedImportParams>`](65-webcrypto.md#class-rsahashedimportparams) | [`<EcKeyImportParams>`](65-webcrypto.md#class-eckeyimportparams) | [`<HmacImportParams>`](65-webcrypto.md#class-hmacimportparams)

- `extractable` [`<boolean>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#boolean_type)
- `keyUsages` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type)[] See [Key usages](65-webcrypto.md#cryptokeyusages).
- Returns: [`<CryptoKey>`](65-webcrypto.md#class-cryptokey)

Converts a `KeyObject` instance to a `CryptoKey`.

### `keyObject.type`

Added in: v11.6.0

- Type: [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type)

Depending on the type of this `KeyObject`, this property is either
`'secret'` for secret (symmetric) keys, `'public'` for public (asymmetric) keys
or `'private'` for private (asymmetric) keys.

## Class: `Mac`

Added in: v26.9.0

- Extends: [`<stream.Transform>`](51-stream-part2.md#class-streamtransform)

The `Mac` class computes message authentication codes using MAC
implementations supplied by OpenSSL providers. It can be used in one of two
ways:

- As a [stream](51-stream-part1.md) that is both readable and writable, where data is written and
  one authentication tag is produced on the readable side when the writable
  side ends; or
- By calling [`mac.update()`](#macupdatedata-inputencoding) one or more times followed by [`mac.final()`](#macfinaloutputencoding).

Instances of `Mac` are created using [`crypto.createMac()`](15-crypto-part3.md#cryptocreatemacalgorithm-key-options). The `Mac` class
is not exported directly by the `node:crypto` module.

Calling `mac.end()` without first writing data computes the authentication tag
for an empty message. If the selected MAC produces a zero-byte tag, such as
when a provider accepts `outputLength: 0`, the readable side ends without
emitting a data chunk because Node.js streams do not emit zero-length chunks.
When using `mac.final()` instead, it returns a zero-length [`Buffer`](07-buffer-part1.md) or an
empty encoded string.

`mac.end()` and `mac.final()` are alternative terminal operations and must not
both be called on the same object. A `Mac` object cannot be used again after
either operation attempts finalization or after an underlying MAC update fails.

Example: Using [`mac.update()`](#macupdatedata-inputencoding) and [`mac.final()`](#macfinaloutputencoding):

```js
const { createMac, randomBytes } = await import('node:crypto');

const key = randomBytes(16);
const mac = createMac('CMAC', key, {
  cipher: 'AES-128-CBC',
});

mac.update('some data to authenticate');
console.log(mac.final('hex'));
```

### `mac.final([outputEncoding])`

Added in: v26.9.0

- `outputEncoding` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) The [encoding](07-buffer-part1.md#buffers-and-character-encodings) of the return value.
- Returns: [`<Buffer>`](07-buffer-part1.md#class-buffer) | [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type)

Completes the MAC computation and returns the authentication tag. If
`outputEncoding` is omitted or is `'buffer'`, a [`Buffer`](07-buffer-part1.md) is returned.
Otherwise, a string is returned.

To verify an authentication tag, compare equal-length [`Buffer`](07-buffer-part1.md) values using
[`crypto.timingSafeEqual()`](15-crypto-part6.md#cryptotimingsafeequala-b).

The `Mac` object cannot be used again after finalization is attempted,
including when finalization fails. Later calls to `mac.update()` or
`mac.final()` throw `ERR_CRYPTO_MAC_FINALIZED`.

### `mac.update(data[, inputEncoding])`

Added in: v26.9.0

- `data` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) | [`<Buffer>`](07-buffer-part1.md#class-buffer) | [`<TypedArray>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/TypedArray) | [`<DataView>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/DataView)
- `inputEncoding` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) The [encoding](07-buffer-part1.md#buffers-and-character-encodings) of the `data` string.
- Returns: {Mac}

Updates the MAC with `data` and returns the `Mac` object so that calls can be
chained. When `data` is a string, `inputEncoding` defaults to `'utf8'`. When
`data` is a [`Buffer`](07-buffer-part1.md), `TypedArray`, or `DataView`, `inputEncoding` is
ignored.

This method can be called multiple times before finalization. If an underlying
MAC update fails, the `Mac` object cannot be used again. Calling this method
after a previous underlying MAC update failure or after finalization throws
`ERR_CRYPTO_MAC_FINALIZED`.

## Class: `Sign`

Added in: v0.1.92

- Extends: [`<stream.Writable>`](51-stream-part1.md#class-streamwritable)

The `Sign` class is a utility for generating signatures. It can be used in one
of two ways:

- As a writable [stream](51-stream-part1.md), where data to be signed is written and the
  [`sign.sign()`](#signsignprivatekey-outputencoding) method is used to generate and return the signature, or
- Using the [`sign.update()`](#signupdatedata-inputencoding) and [`sign.sign()`](#signsignprivatekey-outputencoding) methods to produce the
  signature.

The [`crypto.createSign()`](15-crypto-part4.md#cryptocreatesignalgorithm-options) method is used to create `Sign` instances. The
argument is the string name of the hash function to use. `Sign` objects are not
to be created directly using the `new` keyword.

Example: Using `Sign` and [`Verify`](#class-verify) objects as streams:

*ES module version:*

```js
const {
  generateKeyPairSync,
  createSign,
  createVerify,
} = await import('node:crypto');

const { privateKey, publicKey } = generateKeyPairSync('ec', {
  namedCurve: 'sect239k1',
});

const sign = createSign('SHA256');
sign.write('some data to sign');
sign.end();
const signature = sign.sign(privateKey, 'hex');

const verify = createVerify('SHA256');
verify.write('some data to sign');
verify.end();
console.log(verify.verify(publicKey, signature, 'hex'));
// Prints: true
```

*CommonJS version:*

```js
const {
  generateKeyPairSync,
  createSign,
  createVerify,
} = require('node:crypto');

const { privateKey, publicKey } = generateKeyPairSync('ec', {
  namedCurve: 'sect239k1',
});

const sign = createSign('SHA256');
sign.write('some data to sign');
sign.end();
const signature = sign.sign(privateKey, 'hex');

const verify = createVerify('SHA256');
verify.write('some data to sign');
verify.end();
console.log(verify.verify(publicKey, signature, 'hex'));
// Prints: true
```

Example: Using the [`sign.update()`](#signupdatedata-inputencoding) and [`verify.update()`](#verifyupdatedata-inputencoding) methods:

*ES module version:*

```js
const {
  generateKeyPairSync,
  createSign,
  createVerify,
} = await import('node:crypto');

const { privateKey, publicKey } = generateKeyPairSync('rsa', {
  modulusLength: 2048,
});

const sign = createSign('SHA256');
sign.update('some data to sign');
sign.end();
const signature = sign.sign(privateKey);

const verify = createVerify('SHA256');
verify.update('some data to sign');
verify.end();
console.log(verify.verify(publicKey, signature));
// Prints: true
```

*CommonJS version:*

```js
const {
  generateKeyPairSync,
  createSign,
  createVerify,
} = require('node:crypto');

const { privateKey, publicKey } = generateKeyPairSync('rsa', {
  modulusLength: 2048,
});

const sign = createSign('SHA256');
sign.update('some data to sign');
sign.end();
const signature = sign.sign(privateKey);

const verify = createVerify('SHA256');
verify.update('some data to sign');
verify.end();
console.log(verify.verify(publicKey, signature));
// Prints: true
```

### `sign.sign(privateKey[, outputEncoding])`

Added in: v0.1.92

**History**

| Version | Changes |
| --- | --- |
| v15.0.0 | The privateKey can also be an ArrayBuffer and CryptoKey. |
| v13.2.0, v12.16.0 | This function now supports IEEE-P1363 DSA and ECDSA signatures. |
| v12.0.0 | This function now supports RSA-PSS keys. |
| v11.6.0 | This function now supports key objects. |
| v8.0.0 | Support for RSASSA-PSS and additional options was added. |

- `privateKey` [`<Object>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Object) | [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) | [`<ArrayBuffer>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/ArrayBuffer) | [`<Buffer>`](07-buffer-part1.md#class-buffer) | [`<TypedArray>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/TypedArray) | [`<DataView>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/DataView) | [`<KeyObject>`](15-crypto-part2.md#class-keyobject) | [`<CryptoKey>`](65-webcrypto.md#class-cryptokey) | [`<URL>`](59-url.md#the-whatwg-url-api)
  - `dsaEncoding` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type)
  - `padding` [`<integer>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type)
  - `saltLength` [`<integer>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type)
- `outputEncoding` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) The [encoding](07-buffer-part1.md#buffers-and-character-encodings) of the return value.
- Returns: [`<Buffer>`](07-buffer-part1.md#class-buffer) | [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type)

Calculates the signature on all the data passed through using either
[`sign.update()`](#signupdatedata-inputencoding) or [`sign.write()`](51-stream-part1.md#writablewritechunk-encoding-callback).

If `privateKey` is not a [`KeyObject`](#class-keyobject), this function behaves as if
`privateKey` had been passed to [`crypto.createPrivateKey()`](15-crypto-part3.md#cryptocreateprivatekeykey). When
`privateKey` is a string, `ArrayBuffer`, [`Buffer`](07-buffer-part1.md), `TypedArray`, or
`DataView`, it must contain PEM-encoded key material. If it is an object, the
following additional properties can be passed:

- `dsaEncoding` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) For DSA and ECDSA, this option specifies the
  format of the generated signature. It can be one of the following:

  - `'der'` (default): DER-encoded ASN.1 signature structure encoding `(r, s)`.
  - `'ieee-p1363'`: Signature format `r || s` as proposed in IEEE-P1363.
- `padding` [`<integer>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) Optional padding value for RSA, one of the following:

  - `crypto.constants.RSA_PKCS1_PADDING` (default)
  - `crypto.constants.RSA_PKCS1_PSS_PADDING`

  `RSA_PKCS1_PSS_PADDING` will use MGF1 with the same hash function
  used to sign the message as specified in section 3.1 of [RFC 4055](https://www.rfc-editor.org/rfc/rfc4055.txt), unless
  an MGF1 hash function has been specified as part of the key in compliance with
  section 3.3 of [RFC 4055](https://www.rfc-editor.org/rfc/rfc4055.txt).
- `saltLength` [`<integer>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) Salt length for when padding is `RSA_PKCS1_PSS_PADDING`. The special value
  `crypto.constants.RSA_PSS_SALTLEN_DIGEST` sets the salt length to the digest
  size, `crypto.constants.RSA_PSS_SALTLEN_MAX_SIGN` (default) sets it to the
  maximum permissible value.

If `outputEncoding` is provided a string is returned; otherwise a [`Buffer`](07-buffer-part1.md)
is returned.

The `Sign` object can not be again used after `sign.sign()` method has been
called. Multiple calls to `sign.sign()` will result in an error being thrown.

### `sign.update(data[, inputEncoding])`

Added in: v0.1.92

**History**

| Version | Changes |
| --- | --- |
| v6.0.0 | The default `inputEncoding` changed from `binary` to `utf8`. |

- `data` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) | [`<Buffer>`](07-buffer-part1.md#class-buffer) | [`<TypedArray>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/TypedArray) | [`<DataView>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/DataView)
- `inputEncoding` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) The [encoding](07-buffer-part1.md#buffers-and-character-encodings) of the `data` string.

Updates the `Sign` content with the given `data`, the encoding of which
is given in `inputEncoding`.
If `encoding` is not provided, and the `data` is a string, an
encoding of `'utf8'` is enforced. If `data` is a [`Buffer`](07-buffer-part1.md), `TypedArray`, or
`DataView`, then `inputEncoding` is ignored.

This can be called many times with new data as it is streamed.

## Class: `Verify`

Added in: v0.1.92

- Extends: [`<stream.Writable>`](51-stream-part1.md#class-streamwritable)

The `Verify` class is a utility for verifying signatures. It can be used in one
of two ways:

- As a writable [stream](51-stream-part1.md) where written data is used to validate against the
  supplied signature, or
- Using the [`verify.update()`](#verifyupdatedata-inputencoding) and [`verify.verify()`](#verifyverifykey-signature-signatureencoding) methods to verify
  the signature.

The [`crypto.createVerify()`](15-crypto-part4.md#cryptocreateverifyalgorithm-options) method is used to create `Verify` instances.
`Verify` objects are not to be created directly using the `new` keyword.

See [`Sign`](#class-sign) for examples.

### `verify.update(data[, inputEncoding])`

Added in: v0.1.92

**History**

| Version | Changes |
| --- | --- |
| v6.0.0 | The default `inputEncoding` changed from `binary` to `utf8`. |

- `data` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) | [`<Buffer>`](07-buffer-part1.md#class-buffer) | [`<TypedArray>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/TypedArray) | [`<DataView>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/DataView)
- `inputEncoding` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) The [encoding](07-buffer-part1.md#buffers-and-character-encodings) of the `data` string.

Updates the `Verify` content with the given `data`, the encoding of which
is given in `inputEncoding`.
If `inputEncoding` is not provided, and the `data` is a string, an
encoding of `'utf8'` is enforced. If `data` is a [`Buffer`](07-buffer-part1.md), `TypedArray`, or
`DataView`, then `inputEncoding` is ignored.

This can be called many times with new data as it is streamed.

### `verify.verify(key, signature[, signatureEncoding])`

Added in: v0.1.92

**History**

| Version | Changes |
| --- | --- |
| v15.0.0 | The key can also be an ArrayBuffer and CryptoKey. |
| v13.2.0, v12.16.0 | This function now supports IEEE-P1363 DSA and ECDSA signatures. |
| v12.0.0 | This function now supports RSA-PSS keys. |
| v11.7.0 | The key can now be a private key. |
| v8.0.0 | Support for RSASSA-PSS and additional options was added. |

- `key` [`<Object>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Object) | [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) | [`<ArrayBuffer>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/ArrayBuffer) | [`<Buffer>`](07-buffer-part1.md#class-buffer) | [`<TypedArray>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/TypedArray) | [`<DataView>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/DataView) | [`<KeyObject>`](15-crypto-part2.md#class-keyobject) | [`<CryptoKey>`](65-webcrypto.md#class-cryptokey)
  - `dsaEncoding` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type)
  - `padding` [`<integer>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type)
  - `saltLength` [`<integer>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type)
- `signature` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) | [`<ArrayBuffer>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/ArrayBuffer) | [`<Buffer>`](07-buffer-part1.md#class-buffer) | [`<TypedArray>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/TypedArray) | [`<DataView>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/DataView)
- `signatureEncoding` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) The [encoding](07-buffer-part1.md#buffers-and-character-encodings) of the `signature` string.
- Returns: [`<boolean>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#boolean_type) `true` or `false` depending on the validity of the
  signature for the data and public key.

Verifies the provided data using the given `key` and `signature`.

If `key` is not a [`KeyObject`](#class-keyobject), this function behaves as if
`key` had been passed to [`crypto.createPublicKey()`](15-crypto-part4.md#cryptocreatepublickeykey). When `key` is a string,
`ArrayBuffer`, [`Buffer`](07-buffer-part1.md), `TypedArray`, or `DataView`, it must contain
PEM-encoded key material. If it is an object, the following additional
properties can be passed:

- `dsaEncoding` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) For DSA and ECDSA, this option specifies the
  format of the signature. It can be one of the following:

  - `'der'` (default): DER-encoded ASN.1 signature structure encoding `(r, s)`.
  - `'ieee-p1363'`: Signature format `r || s` as proposed in IEEE-P1363.
- `padding` [`<integer>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) Optional padding value for RSA, one of the following:

  - `crypto.constants.RSA_PKCS1_PADDING` (default)
  - `crypto.constants.RSA_PKCS1_PSS_PADDING`

  `RSA_PKCS1_PSS_PADDING` will use MGF1 with the same hash function
  used to verify the message as specified in section 3.1 of [RFC 4055](https://www.rfc-editor.org/rfc/rfc4055.txt), unless
  an MGF1 hash function has been specified as part of the key in compliance with
  section 3.3 of [RFC 4055](https://www.rfc-editor.org/rfc/rfc4055.txt).
- `saltLength` [`<integer>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) Salt length for when padding is `RSA_PKCS1_PSS_PADDING`. The special value
  `crypto.constants.RSA_PSS_SALTLEN_DIGEST` sets the salt length to the digest
  size, `crypto.constants.RSA_PSS_SALTLEN_AUTO` (default) causes it to be
  determined automatically.

The `signature` argument is the previously calculated signature for the data, in
the `signatureEncoding`.
If a `signatureEncoding` is specified, the `signature` is expected to be a
string; otherwise `signature` is expected to be a [`Buffer`](07-buffer-part1.md),
`TypedArray`, or `DataView`.

The `verify` object can not be used again after `verify.verify()` has been
called. Multiple calls to `verify.verify()` will result in an error being
thrown.

Because public keys can be derived from private keys, a private key may
be passed instead of a public key.

## Class: `X509Certificate`

Added in: v15.6.0

Encapsulates an X509 certificate and provides read-only access to
its information.

*ES module version:*

```js
const { X509Certificate } = await import('node:crypto');

const x509 = new X509Certificate('{... pem encoded cert ...}');

console.log(x509.subject);
```

*CommonJS version:*

```js
const { X509Certificate } = require('node:crypto');

const x509 = new X509Certificate('{... pem encoded cert ...}');

console.log(x509.subject);
```

### `new X509Certificate(buffer)`

Added in: v15.6.0

- `buffer` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) | [`<TypedArray>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/TypedArray) | [`<Buffer>`](07-buffer-part1.md#class-buffer) | [`<DataView>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/DataView) A PEM or DER encoded
  X509 Certificate.

### `x509.ca`

Added in: v15.6.0

- Type: [`<boolean>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#boolean_type) Will be `true` if this is a Certificate Authority (CA)
  certificate.

### `x509.checkEmail(email[, options])`

Added in: v15.6.0

**History**

| Version | Changes |
| --- | --- |
| v18.0.0 | The subject option now defaults to `'default'`. |
| v17.5.0, v16.14.1 | The `wildcards`, `partialWildcards`, `multiLabelWildcards`, and `singleLabelSubdomains` options have been removed since they had no effect. |
| v17.5.0, v16.15.0 | The subject option can now be set to `'default'`. |

- `email` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type)
- `options` [`<Object>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Object)
  - `subject` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) `'default'`, `'always'`, or `'never'`.
    **Default:** `'default'`.
- Returns: [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) | [`<undefined>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#undefined_type) Returns `email` if the certificate matches,
  `undefined` if it does not.

Checks whether the certificate matches the given email address.

If the `'subject'` option is undefined or set to `'default'`, the certificate
subject is considered according to OpenSSL's default behavior.

If the `'subject'` option is set to `'always'` and if the subject alternative
name extension either does not exist or does not contain a matching email
address, the certificate subject is considered.

If the `'subject'` option is set to `'never'`, the certificate subject is never
considered, even if the certificate contains no subject alternative names.

[Contents](../README.md) | [Previous: Crypto, part 1 of 6](15-crypto-part1.md) | [Next: Crypto, part 3 of 6](15-crypto-part3.md)
