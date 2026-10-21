[Contents](../README.md) | [Previous: Console](14-console.md) | [Next: Crypto, part 2 of 6](15-crypto-part2.md)

Source: [https://nodejs.org/docs/latest/api/crypto.html](https://nodejs.org/docs/latest/api/crypto.html)

Part 1 of 6 of this section.

# Crypto

[Stability: 2](01-documentation.md#stability-index) - Stable

The `node:crypto` module provides cryptographic functionality that includes a
set of wrappers for OpenSSL's hash, message authentication code (MAC), cipher,
decipher, sign, verify, and key encapsulation mechanism (KEM) functions.

*ES module version:*

```js
const { createHmac } = await import('node:crypto');

const secret = 'abcdefg';
const hash = createHmac('sha256', secret)
               .update('I love cupcakes')
               .digest('hex');
console.log(hash);
// Prints:
//   c0fa1bc00531bd78ef38c628449c5102aeabd49b5dc3a2a516ea6ea959d6658e
```

*CommonJS version:*

```js
const { createHmac } = require('node:crypto');

const secret = 'abcdefg';
const hash = createHmac('sha256', secret)
               .update('I love cupcakes')
               .digest('hex');
console.log(hash);
// Prints:
//   c0fa1bc00531bd78ef38c628449c5102aeabd49b5dc3a2a516ea6ea959d6658e
```

## Determining if crypto support is unavailable

It is possible for Node.js to be built without including support for the
`node:crypto` module. In such cases, attempting to `import` from `crypto` or
calling `require('node:crypto')` will result in an error being thrown.

When using CommonJS, the error thrown can be caught using try/catch:

```js
let crypto;
try {
  crypto = require('node:crypto');
} catch (err) {
  console.error('crypto support is disabled!');
}
```

When using the lexical ESM `import` keyword, the error can only be
caught if a handler for `process.on('uncaughtException')` is registered
*before* any attempt to load the module is made (using, for instance,
a preload module).

When using ESM, if there is a chance that the code may be run on a build
of Node.js where crypto support is not enabled, consider using the
[`import()`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/import) function instead of the lexical `import` keyword:

```js
let crypto;
try {
  crypto = await import('node:crypto');
} catch (err) {
  console.error('crypto support is disabled!');
}
```

## Asymmetric key types

The following lists group the asymmetric key types recognized by the
[`KeyObject`](15-crypto-part2.md#class-keyobject) API by the complete set of formats supported for importing and
exporting each type.

**Formats:** `'pem'`, `'der'`

- **`'dh'` (Diffie-Hellman)** — OID `1.2.840.113549.1.3.1`
- **`'dsa'`** — OID `1.2.840.10040.4.1`
- **`'rsa-pss'`** — OID `1.2.840.113549.1.1.10`

**Formats:** `'pem'`, `'der'`, `'jwk'`

- **`'rsa'`** — OID `1.2.840.113549.1.1.1`

**Formats:** `'pem'`, `'der'`, `'jwk'`, `'raw-public'`, `'raw-private'`

- **`'ec'` (Elliptic curve)** — OID `1.2.840.10045.2.1`
- **`'ed25519'`** — OID `1.3.101.112`
- **`'ed448'`** — OID `1.3.101.113`
- **`'slh-dsa-sha2-128f'`[1](#user-content-fn-openssl35)** — OID `2.16.840.1.101.3.4.3.21`
- **`'slh-dsa-sha2-128s'`[1](#user-content-fn-openssl35)** — OID `2.16.840.1.101.3.4.3.20`
- **`'slh-dsa-sha2-192f'`[1](#user-content-fn-openssl35)** — OID `2.16.840.1.101.3.4.3.23`
- **`'slh-dsa-sha2-192s'`[1](#user-content-fn-openssl35)** — OID `2.16.840.1.101.3.4.3.22`
- **`'slh-dsa-sha2-256f'`[1](#user-content-fn-openssl35)** — OID `2.16.840.1.101.3.4.3.25`
- **`'slh-dsa-sha2-256s'`[1](#user-content-fn-openssl35)** — OID `2.16.840.1.101.3.4.3.24`
- **`'slh-dsa-shake-128f'`[1](#user-content-fn-openssl35)** — OID `2.16.840.1.101.3.4.3.27`
- **`'slh-dsa-shake-128s'`[1](#user-content-fn-openssl35)** — OID `2.16.840.1.101.3.4.3.26`
- **`'slh-dsa-shake-192f'`[1](#user-content-fn-openssl35)** — OID `2.16.840.1.101.3.4.3.29`
- **`'slh-dsa-shake-192s'`[1](#user-content-fn-openssl35)** — OID `2.16.840.1.101.3.4.3.28`
- **`'slh-dsa-shake-256f'`[1](#user-content-fn-openssl35)** — OID `2.16.840.1.101.3.4.3.31`
- **`'slh-dsa-shake-256s'`[1](#user-content-fn-openssl35)** — OID `2.16.840.1.101.3.4.3.30`
- **`'x25519'`** — OID `1.3.101.110`
- **`'x448'`** — OID `1.3.101.111`

**Formats:** `'pem'`, `'der'`, `'jwk'`, `'raw-public'`, `'raw-seed'`

- **`'ml-dsa-44'`[1](#user-content-fn-openssl35)** — OID `2.16.840.1.101.3.4.3.17`
- **`'ml-dsa-65'`[1](#user-content-fn-openssl35)** — OID `2.16.840.1.101.3.4.3.18`
- **`'ml-dsa-87'`[1](#user-content-fn-openssl35)** — OID `2.16.840.1.101.3.4.3.19`
- **`'ml-kem-512'`[1](#user-content-fn-openssl35)** — OID `2.16.840.1.101.3.4.4.1`
- **`'ml-kem-768'`[1](#user-content-fn-openssl35)** — OID `2.16.840.1.101.3.4.4.2`
- **`'ml-kem-1024'`[1](#user-content-fn-openssl35)** — OID `2.16.840.1.101.3.4.4.3`

### Key formats

Asymmetric keys can be represented in several formats. **The recommended
approach is to import key material into a [`KeyObject`](15-crypto-part2.md#class-keyobject) once and reuse it**
for all subsequent operations, as this avoids repeated parsing and delivers
the best performance.

When a [`KeyObject`](15-crypto-part2.md#class-keyobject) is not practical - for example, when key material
arrives in a protocol message and is used only once - most cryptographic
functions also accept a PEM string or an object specifying the format
and key material directly. See [`crypto.createPublicKey()`](15-crypto-part4.md#cryptocreatepublickeykey),
[`crypto.createPrivateKey()`](15-crypto-part3.md#cryptocreateprivatekeykey), and [`keyObject.export()`](15-crypto-part2.md#keyobjectexportoptions) for the full
options accepted by each format.

#### KeyObject

A [`KeyObject`](15-crypto-part2.md#class-keyobject) is the in-memory representation of a parsed key. It is
created by [`crypto.createPublicKey()`](15-crypto-part4.md#cryptocreatepublickeykey), [`crypto.createPrivateKey()`](15-crypto-part3.md#cryptocreateprivatekeykey),
[`crypto.createSecretKey()`](15-crypto-part4.md#cryptocreatesecretkeykey-encoding), or key generation functions such as
[`crypto.generateKeyPair()`](15-crypto-part4.md#cryptogeneratekeypairtype-options-callback). The first cryptographic operation with a given
[`KeyObject`](15-crypto-part2.md#class-keyobject) may be slower than subsequent ones because OpenSSL lazily
initializes internal caches on first use.

#### PEM and DER

PEM and DER are the traditional encoding formats for asymmetric keys based on
ASN.1 structures.

- **PEM** is a text encoding that wraps Base64-encoded DER data between
  header and footer lines (e.g. `-----BEGIN PUBLIC KEY-----`). PEM strings can
  be passed directly to most cryptographic operations.
- **DER** is the binary encoding of the same ASN.1 structures. When providing
  DER input, the `type` (typically `'spki'` or `'pkcs8'`) must be specified
  explicitly.

#### JSON Web Key (JWK)

JSON Web Key (JWK) is a JSON-based key representation defined in
[RFC 7517](https://www.rfc-editor.org/rfc/rfc7517.txt). JWK encodes each key component as an individual Base64url-encoded
value inside a JSON object. For RSA keys, JWK avoids ASN.1 parsing overhead
and is the fastest serialized import format.

#### Raw key formats

Stability: 1.1 - Active development

The `'raw-public'`, `'raw-private'`, and `'raw-seed'` key formats allow
importing and exporting raw key material without any encoding wrapper.
See [`keyObject.export()`](15-crypto-part2.md#keyobjectexportoptions), [`crypto.createPublicKey()`](15-crypto-part4.md#cryptocreatepublickeykey), and
[`crypto.createPrivateKey()`](15-crypto-part3.md#cryptocreateprivatekeykey) for usage details.

`'raw-public'` is generally the fastest way to import a public key.
`'raw-private'` and `'raw-seed'` are not always faster than other formats
because they only contain the private scalar or seed - importing them requires
deriving the public key component (e.g. elliptic curve point multiplication or
seed expansion), which can be expensive. Other formats include both private
and public components, avoiding that computation.

### Choosing a key format

**Always prefer a [`KeyObject`](15-crypto-part2.md#class-keyobject)** - create one from whatever format you
have and reuse it. The guidance below applies only when choosing between
serialization formats, either for importing into a [`KeyObject`](15-crypto-part2.md#class-keyobject) or for
passing key material inline when a [`KeyObject`](15-crypto-part2.md#class-keyobject) is not practical.

#### Importing keys

When creating a [`KeyObject`](15-crypto-part2.md#class-keyobject) for repeated use, the import cost is paid once,
so choosing a faster format reduces startup latency.

The import cost breaks down into two parts: **parsing overhead** (decoding the
serialization wrapper) and **key computation** (any mathematical work needed to
reconstruct the full key, such as deriving a public key from a private scalar
or expanding a seed). Which part dominates depends on the key type. For
example:

- Public keys - `'raw-public'` is the fastest serialized format because the
  raw format skips all ASN.1 and Base64 decoding.
- EC private keys - `'raw-private'` is faster than PEM or DER because it
  avoids ASN.1 parsing. However, for larger curves (e.g. P-384, P-521) the
  required derivation of the public point from the private scalar becomes
  expensive, reducing the advantage.
- RSA keys - `'jwk'` is the fastest serialized format. JWK represents RSA
  key components as individual Base64url-encoded integers, avoiding the
  overhead of ASN.1 parsing entirely.

#### Inline key material in operations

When a [`KeyObject`](15-crypto-part2.md#class-keyobject) cannot be reused (e.g. the key arrives as raw bytes in
a protocol message and is used only once), most cryptographic functions also
accept a PEM string or an object specifying the format and key
material directly. In this case the total cost is the sum of key import and
the cryptographic computation itself.

For operations where the cryptographic computation dominates - such as
signing with RSA or ECDH key agreement with P-384 or P-521 - the
serialization format has negligible impact on overall throughput, so choose
whichever format is most convenient. For lightweight operations like Ed25519
signing or verification, the import cost is a larger fraction of the total,
so a faster format like `'raw-public'` or `'raw-private'` can meaningfully
improve throughput.

Even if the same key material is used only a few times, it is worth importing it
into a [`KeyObject`](15-crypto-part2.md#class-keyobject) rather than passing the raw or PEM representation
repeatedly.

### Examples

Example: Reusing a [`KeyObject`](15-crypto-part2.md#class-keyobject) across sign and verify operations:

```js
import { promisify } from 'node:util';
const { generateKeyPair, sign, verify } = await import('node:crypto');

const { publicKey, privateKey } = await promisify(generateKeyPair)('ed25519');

// A KeyObject holds the parsed key in memory and can be reused
// across multiple operations without re-parsing.
const data = new TextEncoder().encode('message to sign');
const signature = sign(null, data, privateKey);
verify(null, data, publicKey, signature);
```

Example: Importing keys of various formats into [`KeyObject`](15-crypto-part2.md#class-keyobject)s:

```js
import { promisify } from 'node:util';
const {
  createPrivateKey, createPublicKey, generateKeyPair,
} = await import('node:crypto');

const generated = await promisify(generateKeyPair)('ed25519');

// PEM
const privatePem = generated.privateKey.export({ format: 'pem', type: 'pkcs8' });
const publicPem = generated.publicKey.export({ format: 'pem', type: 'spki' });
createPrivateKey(privatePem);
createPublicKey(publicPem);

// DER - requires explicit type
const privateDer = generated.privateKey.export({ format: 'der', type: 'pkcs8' });
const publicDer = generated.publicKey.export({ format: 'der', type: 'spki' });
createPrivateKey({ key: privateDer, format: 'der', type: 'pkcs8' });
createPublicKey({ key: publicDer, format: 'der', type: 'spki' });

// JWK
const privateJwk = generated.privateKey.export({ format: 'jwk' });
const publicJwk = generated.publicKey.export({ format: 'jwk' });
createPrivateKey({ key: privateJwk, format: 'jwk' });
createPublicKey({ key: publicJwk, format: 'jwk' });

// Raw
const rawPriv = generated.privateKey.export({ format: 'raw-private' });
const rawPub = generated.publicKey.export({ format: 'raw-public' });
createPrivateKey({ key: rawPriv, format: 'raw-private', asymmetricKeyType: 'ed25519' });
createPublicKey({ key: rawPub, format: 'raw-public', asymmetricKeyType: 'ed25519' });
```

Example: Passing key material directly to [`crypto.sign()`](15-crypto-part5.md#cryptosignalgorithm-data-key-callback) and
[`crypto.verify()`](15-crypto-part6.md#cryptoverifyalgorithm-data-key-signature-callback) without creating a [`KeyObject`](15-crypto-part2.md#class-keyobject) first:

```js
import { promisify } from 'node:util';
const { generateKeyPair, sign, verify } = await import('node:crypto');

const generated = await promisify(generateKeyPair)('ed25519');

const data = new TextEncoder().encode('message to sign');

// PEM strings
const privatePem = generated.privateKey.export({ format: 'pem', type: 'pkcs8' });
const publicPem = generated.publicKey.export({ format: 'pem', type: 'spki' });
const sig1 = sign(null, data, privatePem);
verify(null, data, publicPem, sig1);

// JWK objects
const privateJwk = generated.privateKey.export({ format: 'jwk' });
const publicJwk = generated.publicKey.export({ format: 'jwk' });
const sig2 = sign(null, data, { key: privateJwk, format: 'jwk' });
verify(null, data, { key: publicJwk, format: 'jwk' }, sig2);

// Raw key bytes
const rawPriv = generated.privateKey.export({ format: 'raw-private' });
const rawPub = generated.publicKey.export({ format: 'raw-public' });
const sig3 = sign(null, data, {
  key: rawPriv, format: 'raw-private', asymmetricKeyType: 'ed25519',
});
verify(null, data, {
  key: rawPub, format: 'raw-public', asymmetricKeyType: 'ed25519',
}, sig3);
```

Example: For EC keys, the `namedCurve` option is required when importing
raw keys:

```js
import { promisify } from 'node:util';
const {
  createPrivateKey, createPublicKey, generateKeyPair, sign, verify,
} = await import('node:crypto');

const generated = await promisify(generateKeyPair)('ec', {
  namedCurve: 'P-256',
});

// Export the raw EC public key (uncompressed by default).
const rawPublicKey = generated.publicKey.export({ format: 'raw-public' });

// The following is equivalent.
const rawPublicKeyUncompressed = generated.publicKey.export({
  format: 'raw-public',
  type: 'uncompressed',
});

// Export compressed point format.
const rawPublicKeyCompressed = generated.publicKey.export({
  format: 'raw-public',
  type: 'compressed',
});

// Export the raw EC private key.
const rawPrivateKey = generated.privateKey.export({ format: 'raw-private' });

// Import the raw EC keys.
// Both compressed and uncompressed point formats are accepted.
const publicKey = createPublicKey({
  key: rawPublicKey,
  format: 'raw-public',
  asymmetricKeyType: 'ec',
  namedCurve: 'P-256',
});
const privateKey = createPrivateKey({
  key: rawPrivateKey,
  format: 'raw-private',
  asymmetricKeyType: 'ec',
  namedCurve: 'P-256',
});

const data = new TextEncoder().encode('message to sign');
const signature = sign('sha256', data, privateKey);
verify('sha256', data, publicKey, signature);
```

Example: Exporting raw seeds and importing them:

```js
import { promisify } from 'node:util';
const {
  createPrivateKey, decapsulate, encapsulate, generateKeyPair,
} = await import('node:crypto');

const generated = await promisify(generateKeyPair)('ml-kem-768');

// Export the raw seed (64 bytes for ML-KEM).
const seed = generated.privateKey.export({ format: 'raw-seed' });

// Import the raw seed.
const privateKey = createPrivateKey({
  key: seed,
  format: 'raw-seed',
  asymmetricKeyType: 'ml-kem-768',
});

const { ciphertext } = encapsulate(generated.publicKey);
decapsulate(privateKey, ciphertext);
```

## Class: `Certificate`

Added in: v0.11.8

SPKAC is a Certificate Signing Request mechanism originally implemented by
Netscape and was specified formally as part of HTML5's `keygen` element.

`<keygen>` is deprecated since [HTML 5.2](https://www.w3.org/TR/html52/changes.html#features-removed) and new projects
should not use this element anymore.

The `node:crypto` module provides the `Certificate` class for working with SPKAC
data. The most common usage is handling output generated by the HTML5
`<keygen>` element. Node.js uses [OpenSSL's SPKAC implementation](https://www.openssl.org/docs/man3.0/man1/openssl-spkac.html) internally.

### Static method: `Certificate.exportChallenge(spkac[, encoding])`

Added in: v9.0.0

**History**

| Version | Changes |
| --- | --- |
| v15.0.0 | The spkac argument can be an ArrayBuffer. Limited the size of the spkac argument to a maximum of 2\*\*31 - 1 bytes. |

- `spkac` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) | [`<ArrayBuffer>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/ArrayBuffer) | [`<Buffer>`](07-buffer-part1.md#class-buffer) | [`<TypedArray>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/TypedArray) | [`<DataView>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/DataView)
- `encoding` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) The [encoding](07-buffer-part1.md#buffers-and-character-encodings) of the `spkac` string.
- Returns: [`<Buffer>`](07-buffer-part1.md#class-buffer) The challenge component of the `spkac` data structure, which
  includes a public key and a challenge.

*ES module version:*

```js
const { Certificate } = await import('node:crypto');
const spkac = getSpkacSomehow();
const challenge = Certificate.exportChallenge(spkac);
console.log(challenge.toString('utf8'));
// Prints: the challenge as a UTF8 string
```

*CommonJS version:*

```js
const { Certificate } = require('node:crypto');
const spkac = getSpkacSomehow();
const challenge = Certificate.exportChallenge(spkac);
console.log(challenge.toString('utf8'));
// Prints: the challenge as a UTF8 string
```

### Static method: `Certificate.exportPublicKey(spkac[, encoding])`

Added in: v9.0.0

**History**

| Version | Changes |
| --- | --- |
| v15.0.0 | The spkac argument can be an ArrayBuffer. Limited the size of the spkac argument to a maximum of 2\*\*31 - 1 bytes. |

- `spkac` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) | [`<ArrayBuffer>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/ArrayBuffer) | [`<Buffer>`](07-buffer-part1.md#class-buffer) | [`<TypedArray>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/TypedArray) | [`<DataView>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/DataView)
- `encoding` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) The [encoding](07-buffer-part1.md#buffers-and-character-encodings) of the `spkac` string.
- Returns: [`<Buffer>`](07-buffer-part1.md#class-buffer) The public key component of the `spkac` data structure,
  which includes a public key and a challenge.

*ES module version:*

```js
const { Certificate } = await import('node:crypto');
const spkac = getSpkacSomehow();
const publicKey = Certificate.exportPublicKey(spkac);
console.log(publicKey);
// Prints: the public key as <Buffer ...>
```

*CommonJS version:*

```js
const { Certificate } = require('node:crypto');
const spkac = getSpkacSomehow();
const publicKey = Certificate.exportPublicKey(spkac);
console.log(publicKey);
// Prints: the public key as <Buffer ...>
```

### Static method: `Certificate.verifySpkac(spkac[, encoding])`

Added in: v9.0.0

**History**

| Version | Changes |
| --- | --- |
| v15.0.0 | The spkac argument can be an ArrayBuffer. Added encoding. Limited the size of the spkac argument to a maximum of 2\*\*31 - 1 bytes. |

- `spkac` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) | [`<ArrayBuffer>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/ArrayBuffer) | [`<Buffer>`](07-buffer-part1.md#class-buffer) | [`<TypedArray>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/TypedArray) | [`<DataView>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/DataView)
- `encoding` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) The [encoding](07-buffer-part1.md#buffers-and-character-encodings) of the `spkac` string.
- Returns: [`<boolean>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#boolean_type) `true` if the given `spkac` data structure is valid,
  `false` otherwise.

*ES module version:*

```js
import { Buffer } from 'node:buffer';
const { Certificate } = await import('node:crypto');

const spkac = getSpkacSomehow();
console.log(Certificate.verifySpkac(Buffer.from(spkac)));
// Prints: true or false
```

*CommonJS version:*

```js
const { Buffer } = require('node:buffer');
const { Certificate } = require('node:crypto');

const spkac = getSpkacSomehow();
console.log(Certificate.verifySpkac(Buffer.from(spkac)));
// Prints: true or false
```

### Legacy API

Stability: 0 - Deprecated

As a legacy interface, it is possible to create new instances of
the `crypto.Certificate` class as illustrated in the examples below.

#### `new crypto.Certificate()`

Instances of the `Certificate` class can be created using the `new` keyword
or by calling `crypto.Certificate()` as a function:

*ES module version:*

```js
const { Certificate } = await import('node:crypto');

const cert1 = new Certificate();
const cert2 = Certificate();
```

*CommonJS version:*

```js
const { Certificate } = require('node:crypto');

const cert1 = new Certificate();
const cert2 = Certificate();
```

#### `certificate.exportChallenge(spkac[, encoding])`

Added in: v0.11.8

- `spkac` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) | [`<ArrayBuffer>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/ArrayBuffer) | [`<Buffer>`](07-buffer-part1.md#class-buffer) | [`<TypedArray>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/TypedArray) | [`<DataView>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/DataView)
- `encoding` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) The [encoding](07-buffer-part1.md#buffers-and-character-encodings) of the `spkac` string.
- Returns: [`<Buffer>`](07-buffer-part1.md#class-buffer) The challenge component of the `spkac` data structure, which
  includes a public key and a challenge.

*ES module version:*

```js
const { Certificate } = await import('node:crypto');
const cert = Certificate();
const spkac = getSpkacSomehow();
const challenge = cert.exportChallenge(spkac);
console.log(challenge.toString('utf8'));
// Prints: the challenge as a UTF8 string
```

*CommonJS version:*

```js
const { Certificate } = require('node:crypto');
const cert = Certificate();
const spkac = getSpkacSomehow();
const challenge = cert.exportChallenge(spkac);
console.log(challenge.toString('utf8'));
// Prints: the challenge as a UTF8 string
```

#### `certificate.exportPublicKey(spkac[, encoding])`

Added in: v0.11.8

- `spkac` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) | [`<ArrayBuffer>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/ArrayBuffer) | [`<Buffer>`](07-buffer-part1.md#class-buffer) | [`<TypedArray>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/TypedArray) | [`<DataView>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/DataView)
- `encoding` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) The [encoding](07-buffer-part1.md#buffers-and-character-encodings) of the `spkac` string.
- Returns: [`<Buffer>`](07-buffer-part1.md#class-buffer) The public key component of the `spkac` data structure,
  which includes a public key and a challenge.

*ES module version:*

```js
const { Certificate } = await import('node:crypto');
const cert = Certificate();
const spkac = getSpkacSomehow();
const publicKey = cert.exportPublicKey(spkac);
console.log(publicKey);
// Prints: the public key as <Buffer ...>
```

*CommonJS version:*

```js
const { Certificate } = require('node:crypto');
const cert = Certificate();
const spkac = getSpkacSomehow();
const publicKey = cert.exportPublicKey(spkac);
console.log(publicKey);
// Prints: the public key as <Buffer ...>
```

#### `certificate.verifySpkac(spkac[, encoding])`

Added in: v0.11.8

- `spkac` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) | [`<ArrayBuffer>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/ArrayBuffer) | [`<Buffer>`](07-buffer-part1.md#class-buffer) | [`<TypedArray>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/TypedArray) | [`<DataView>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/DataView)
- `encoding` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) The [encoding](07-buffer-part1.md#buffers-and-character-encodings) of the `spkac` string.
- Returns: [`<boolean>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#boolean_type) `true` if the given `spkac` data structure is valid,
  `false` otherwise.

*ES module version:*

```js
import { Buffer } from 'node:buffer';
const { Certificate } = await import('node:crypto');

const cert = Certificate();
const spkac = getSpkacSomehow();
console.log(cert.verifySpkac(Buffer.from(spkac)));
// Prints: true or false
```

*CommonJS version:*

```js
const { Buffer } = require('node:buffer');
const { Certificate } = require('node:crypto');

const cert = Certificate();
const spkac = getSpkacSomehow();
console.log(cert.verifySpkac(Buffer.from(spkac)));
// Prints: true or false
```

## Class: `Cipheriv`

Added in: v0.1.94

- Extends: [`<stream.Transform>`](51-stream-part2.md#class-streamtransform)

Instances of the `Cipheriv` class are used to encrypt data. The class can be
used in one of two ways:

- As a [stream](51-stream-part1.md) that is both readable and writable, where plain unencrypted
  data is written to produce encrypted data on the readable side, or
- Using the [`cipher.update()`](#cipherupdatedata-inputencoding-outputencoding) and [`cipher.final()`](#cipherfinaloutputencoding) methods to produce
  the encrypted data.

The [`crypto.createCipheriv()`](15-crypto-part3.md#cryptocreatecipherivalgorithm-key-iv-options) method is
used to create `Cipheriv` instances. `Cipheriv` objects are not to be created
directly using the `new` keyword.

The selected algorithm may impose additional restrictions on streaming and
calls to [`cipher.update()`](#cipherupdatedata-inputencoding-outputencoding). See [CCM mode](15-crypto-part6.md#ccm-mode), [CBC-CTS mode](15-crypto-part6.md#cbc-cts-mode), [XTS mode](15-crypto-part6.md#xts-mode),
[AES key wrap modes](15-crypto-part6.md#aes-key-wrap-modes), and [SIV and GCM-SIV modes](15-crypto-part6.md#siv-and-gcm-siv-modes).

Example: Using `Cipheriv` objects as streams:

*ES module version:*

```js
const {
  scrypt,
  randomFill,
  createCipheriv,
} = await import('node:crypto');

const algorithm = 'aes-192-cbc';
const password = 'Password used to generate key';

// First, we'll generate the key. The key length is dependent on the algorithm.
// In this case for aes192, it is 24 bytes (192 bits).
scrypt(password, 'salt', 24, (err, key) => {
  if (err) throw err;
  // Then, we'll generate a random initialization vector
  randomFill(new Uint8Array(16), (err, iv) => {
    if (err) throw err;

    // Once we have the key and iv, we can create and use the cipher...
    const cipher = createCipheriv(algorithm, key, iv);

    let encrypted = '';
    cipher.setEncoding('hex');

    cipher.on('data', (chunk) => encrypted += chunk);
    cipher.on('end', () => console.log(encrypted));

    cipher.write('some clear text data');
    cipher.end();
  });
});
```

*CommonJS version:*

```js
const {
  scrypt,
  randomFill,
  createCipheriv,
} = require('node:crypto');

const algorithm = 'aes-192-cbc';
const password = 'Password used to generate key';

// First, we'll generate the key. The key length is dependent on the algorithm.
// In this case for aes192, it is 24 bytes (192 bits).
scrypt(password, 'salt', 24, (err, key) => {
  if (err) throw err;
  // Then, we'll generate a random initialization vector
  randomFill(new Uint8Array(16), (err, iv) => {
    if (err) throw err;

    // Once we have the key and iv, we can create and use the cipher...
    const cipher = createCipheriv(algorithm, key, iv);

    let encrypted = '';
    cipher.setEncoding('hex');

    cipher.on('data', (chunk) => encrypted += chunk);
    cipher.on('end', () => console.log(encrypted));

    cipher.write('some clear text data');
    cipher.end();
  });
});
```

Example: Using `Cipheriv` and piped streams:

*ES module version:*

```js
import {
  createReadStream,
  createWriteStream,
} from 'node:fs';

import {
  pipeline,
} from 'node:stream';

const {
  scrypt,
  randomFill,
  createCipheriv,
} = await import('node:crypto');

const algorithm = 'aes-192-cbc';
const password = 'Password used to generate key';

// First, we'll generate the key. The key length is dependent on the algorithm.
// In this case for aes192, it is 24 bytes (192 bits).
scrypt(password, 'salt', 24, (err, key) => {
  if (err) throw err;
  // Then, we'll generate a random initialization vector
  randomFill(new Uint8Array(16), (err, iv) => {
    if (err) throw err;

    const cipher = createCipheriv(algorithm, key, iv);

    const input = createReadStream('test.js');
    const output = createWriteStream('test.enc');

    pipeline(input, cipher, output, (err) => {
      if (err) throw err;
    });
  });
});
```

*CommonJS version:*

```js
const {
  createReadStream,
  createWriteStream,
} = require('node:fs');

const {
  pipeline,
} = require('node:stream');

const {
  scrypt,
  randomFill,
  createCipheriv,
} = require('node:crypto');

const algorithm = 'aes-192-cbc';
const password = 'Password used to generate key';

// First, we'll generate the key. The key length is dependent on the algorithm.
// In this case for aes192, it is 24 bytes (192 bits).
scrypt(password, 'salt', 24, (err, key) => {
  if (err) throw err;
  // Then, we'll generate a random initialization vector
  randomFill(new Uint8Array(16), (err, iv) => {
    if (err) throw err;

    const cipher = createCipheriv(algorithm, key, iv);

    const input = createReadStream('test.js');
    const output = createWriteStream('test.enc');

    pipeline(input, cipher, output, (err) => {
      if (err) throw err;
    });
  });
});
```

Example: Using the [`cipher.update()`](#cipherupdatedata-inputencoding-outputencoding) and [`cipher.final()`](#cipherfinaloutputencoding) methods:

*ES module version:*

```js
const {
  scrypt,
  randomFill,
  createCipheriv,
} = await import('node:crypto');

const algorithm = 'aes-192-cbc';
const password = 'Password used to generate key';

// First, we'll generate the key. The key length is dependent on the algorithm.
// In this case for aes192, it is 24 bytes (192 bits).
scrypt(password, 'salt', 24, (err, key) => {
  if (err) throw err;
  // Then, we'll generate a random initialization vector
  randomFill(new Uint8Array(16), (err, iv) => {
    if (err) throw err;

    const cipher = createCipheriv(algorithm, key, iv);

    let encrypted = cipher.update('some clear text data', 'utf8', 'hex');
    encrypted += cipher.final('hex');
    console.log(encrypted);
  });
});
```

*CommonJS version:*

```js
const {
  scrypt,
  randomFill,
  createCipheriv,
} = require('node:crypto');

const algorithm = 'aes-192-cbc';
const password = 'Password used to generate key';

// First, we'll generate the key. The key length is dependent on the algorithm.
// In this case for aes192, it is 24 bytes (192 bits).
scrypt(password, 'salt', 24, (err, key) => {
  if (err) throw err;
  // Then, we'll generate a random initialization vector
  randomFill(new Uint8Array(16), (err, iv) => {
    if (err) throw err;

    const cipher = createCipheriv(algorithm, key, iv);

    let encrypted = cipher.update('some clear text data', 'utf8', 'hex');
    encrypted += cipher.final('hex');
    console.log(encrypted);
  });
});
```

### `cipher.final([outputEncoding])`

Added in: v0.1.94

- `outputEncoding` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) The [encoding](07-buffer-part1.md#buffers-and-character-encodings) of the return value.
- Returns: [`<Buffer>`](07-buffer-part1.md#class-buffer) | [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) Any remaining enciphered contents.
  If `outputEncoding` is specified, a string is
  returned. If an `outputEncoding` is not provided, a [`Buffer`](07-buffer-part1.md) is returned.

If an output encoding was specified in a previous call to
[`cipher.update()`](#cipherupdatedata-inputencoding-outputencoding), `outputEncoding` must use the same encoding.

Once the `cipher.final()` method has been called, the `Cipheriv` object can no
longer be used to encrypt data. Attempts to call `cipher.final()` more than
once will result in an error being thrown.

### `cipher.getAuthTag()`

Added in: v1.0.0

- Returns: [`<Buffer>`](07-buffer-part1.md#class-buffer) When using an authenticated encryption mode (`GCM`, `CCM`,
  `OCB`, `SIV`, `GCM-SIV`, and `chacha20-poly1305` are currently
  supported), the `cipher.getAuthTag()` method returns a
  [`Buffer`](07-buffer-part1.md) containing the *authentication tag* that has been computed from
  the given data.

The `cipher.getAuthTag()` method should only be called after encryption has
been completed using the [`cipher.final()`](#cipherfinaloutputencoding) method.

If the `authTagLength` option was set during the `cipher` instance's creation,
this function will return exactly `authTagLength` bytes.

### `cipher.setAAD(buffer[, options])`

Added in: v1.0.0

- `buffer` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) | [`<ArrayBuffer>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/ArrayBuffer) | [`<Buffer>`](07-buffer-part1.md#class-buffer) | [`<TypedArray>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/TypedArray) | [`<DataView>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/DataView)
- `options` [`<Object>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Object) [`stream.transform` options](51-stream-part3.md#new-streamtransformoptions)
  - `plaintextLength` [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type)
  - `encoding` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) The string encoding to use when `buffer` is a string.
- Returns: [`<Cipheriv>`](15-crypto-part1.md#class-cipheriv) The same `Cipheriv` instance for method chaining.

When using an authenticated encryption mode (`GCM`, `CCM`, `OCB`, `SIV`,
`GCM-SIV`, and `chacha20-poly1305` are currently supported), the
`cipher.setAAD()` method sets the value used for the *additional authenticated
data* (AAD) input parameter.

The `plaintextLength` option is optional for `GCM`, `OCB`, `SIV`, and
`GCM-SIV`. When using `CCM`, the `plaintextLength` option must be specified and
its value must match the length of the plaintext in bytes. See [CCM mode](15-crypto-part6.md#ccm-mode).

The `cipher.setAAD()` method must be called before [`cipher.update()`](#cipherupdatedata-inputencoding-outputencoding).

### `cipher.setAutoPadding([autoPadding])`

Added in: v0.7.1

- `autoPadding` [`<boolean>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#boolean_type) **Default:** `true`
- Returns: [`<Cipheriv>`](15-crypto-part1.md#class-cipheriv) The same `Cipheriv` instance for method chaining.

When using block ciphers that use standard block padding, the `Cipheriv` class
will automatically add padding to the input data to the appropriate block size.
To disable the default padding call `cipher.setAutoPadding(false)`.

For block ciphers that use standard block padding, when `autoPadding` is
`false`, the length of the entire input data must be a multiple of the cipher's
block size or [`cipher.final()`](#cipherfinaloutputencoding) will throw an error. Disabling automatic
padding is useful for non-standard padding, for instance using `0x0` instead of
PKCS padding.

The `cipher.setAutoPadding()` method must be called before
[`cipher.final()`](#cipherfinaloutputencoding).

### `cipher.update(data[, inputEncoding][, outputEncoding])`

Added in: v0.1.94

**History**

| Version | Changes |
| --- | --- |
| v6.0.0 | The default `inputEncoding` changed from `binary` to `utf8`. |

- `data` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) | [`<Buffer>`](07-buffer-part1.md#class-buffer) | [`<TypedArray>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/TypedArray) | [`<DataView>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/DataView)
- `inputEncoding` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) The [encoding](07-buffer-part1.md#buffers-and-character-encodings) of the data.
- `outputEncoding` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) The [encoding](07-buffer-part1.md#buffers-and-character-encodings) of the return value.
- Returns: [`<Buffer>`](07-buffer-part1.md#class-buffer) | [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type)

Updates the cipher with `data`. If the `inputEncoding` argument is given,
the `data`
argument is a string using the specified encoding. If the `inputEncoding`
argument is not given, `data` must be a [`Buffer`](07-buffer-part1.md), `TypedArray`, or
`DataView`. If `data` is a [`Buffer`](07-buffer-part1.md), `TypedArray`, or `DataView`, then
`inputEncoding` is ignored.

The `outputEncoding` specifies the output format of the enciphered
data. If the `outputEncoding`
is specified, a string using the specified encoding is returned. If no
`outputEncoding` is provided, a [`Buffer`](07-buffer-part1.md) is returned.
When `outputEncoding` is specified, it must use the same encoding as previous
calls to `cipher.update()`.

For most algorithms, `cipher.update()` can be called multiple times with new
data until [`cipher.final()`](#cipherfinaloutputencoding) is called. Some algorithms restrict calls to
`cipher.update()`. For example, [CCM mode](15-crypto-part6.md#ccm-mode), [CBC-CTS mode](15-crypto-part6.md#cbc-cts-mode), [XTS mode](15-crypto-part6.md#xts-mode),
[AES key wrap modes](15-crypto-part6.md#aes-key-wrap-modes), and [SIV and GCM-SIV modes](15-crypto-part6.md#siv-and-gcm-siv-modes) require the whole message
in a single call. Calling `cipher.update()` after [`cipher.final()`](#cipherfinaloutputencoding) will
result in an error being thrown.

## Class: `Decipheriv`

Added in: v0.1.94

- Extends: [`<stream.Transform>`](51-stream-part2.md#class-streamtransform)

Instances of the `Decipheriv` class are used to decrypt data. The class can be
used in one of two ways:

- As a [stream](51-stream-part1.md) that is both readable and writable, where plain encrypted
  data is written to produce unencrypted data on the readable side, or
- Using the [`decipher.update()`](#decipherupdatedata-inputencoding-outputencoding) and [`decipher.final()`](#decipherfinaloutputencoding) methods to
  produce the unencrypted data.

The [`crypto.createDecipheriv()`](15-crypto-part3.md#cryptocreatedecipherivalgorithm-key-iv-options) method is
used to create `Decipheriv` instances. `Decipheriv` objects are not to be created
directly using the `new` keyword.

The selected algorithm may impose additional restrictions on streaming and
calls to [`decipher.update()`](#decipherupdatedata-inputencoding-outputencoding). See [CCM mode](15-crypto-part6.md#ccm-mode), [CBC-CTS mode](15-crypto-part6.md#cbc-cts-mode),
[XTS mode](15-crypto-part6.md#xts-mode), [AES key wrap modes](15-crypto-part6.md#aes-key-wrap-modes), and [SIV and GCM-SIV modes](15-crypto-part6.md#siv-and-gcm-siv-modes).

Example: Using `Decipheriv` objects as streams:

*ES module version:*

```js
import { Buffer } from 'node:buffer';
const {
  scryptSync,
  createDecipheriv,
} = await import('node:crypto');

const algorithm = 'aes-192-cbc';
const password = 'Password used to generate key';
// Key length is dependent on the algorithm. In this case for aes192, it is
// 24 bytes (192 bits).
// Use the async `crypto.scrypt()` instead.
const key = scryptSync(password, 'salt', 24);
// The IV is usually passed along with the ciphertext.
const iv = Buffer.alloc(16, 0); // Initialization vector.

const decipher = createDecipheriv(algorithm, key, iv);

let decrypted = '';
decipher.on('readable', () => {
  let chunk;
  while (null !== (chunk = decipher.read())) {
    decrypted += chunk.toString('utf8');
  }
});
decipher.on('end', () => {
  console.log(decrypted);
  // Prints: some clear text data
});

// Encrypted with same algorithm, key and iv.
const encrypted =
  'e5f79c5915c02171eec6b212d5520d44480993d7d622a7c4c2da32f6efda0ffa';
decipher.write(encrypted, 'hex');
decipher.end();
```

*CommonJS version:*

```js
const {
  scryptSync,
  createDecipheriv,
} = require('node:crypto');
const { Buffer } = require('node:buffer');

const algorithm = 'aes-192-cbc';
const password = 'Password used to generate key';
// Key length is dependent on the algorithm. In this case for aes192, it is
// 24 bytes (192 bits).
// Use the async `crypto.scrypt()` instead.
const key = scryptSync(password, 'salt', 24);
// The IV is usually passed along with the ciphertext.
const iv = Buffer.alloc(16, 0); // Initialization vector.

const decipher = createDecipheriv(algorithm, key, iv);

let decrypted = '';
decipher.on('readable', () => {
  let chunk;
  while (null !== (chunk = decipher.read())) {
    decrypted += chunk.toString('utf8');
  }
});
decipher.on('end', () => {
  console.log(decrypted);
  // Prints: some clear text data
});

// Encrypted with same algorithm, key and iv.
const encrypted =
  'e5f79c5915c02171eec6b212d5520d44480993d7d622a7c4c2da32f6efda0ffa';
decipher.write(encrypted, 'hex');
decipher.end();
```

Example: Using `Decipheriv` and piped streams:

*ES module version:*

```js
import {
  createReadStream,
  createWriteStream,
} from 'node:fs';
import { Buffer } from 'node:buffer';
const {
  scryptSync,
  createDecipheriv,
} = await import('node:crypto');

const algorithm = 'aes-192-cbc';
const password = 'Password used to generate key';
// Use the async `crypto.scrypt()` instead.
const key = scryptSync(password, 'salt', 24);
// The IV is usually passed along with the ciphertext.
const iv = Buffer.alloc(16, 0); // Initialization vector.

const decipher = createDecipheriv(algorithm, key, iv);

const input = createReadStream('test.enc');
const output = createWriteStream('test.js');

input.pipe(decipher).pipe(output);
```

*CommonJS version:*

```js
const {
  createReadStream,
  createWriteStream,
} = require('node:fs');
const {
  scryptSync,
  createDecipheriv,
} = require('node:crypto');
const { Buffer } = require('node:buffer');

const algorithm = 'aes-192-cbc';
const password = 'Password used to generate key';
// Use the async `crypto.scrypt()` instead.
const key = scryptSync(password, 'salt', 24);
// The IV is usually passed along with the ciphertext.
const iv = Buffer.alloc(16, 0); // Initialization vector.

const decipher = createDecipheriv(algorithm, key, iv);

const input = createReadStream('test.enc');
const output = createWriteStream('test.js');

input.pipe(decipher).pipe(output);
```

Example: Using the [`decipher.update()`](#decipherupdatedata-inputencoding-outputencoding) and [`decipher.final()`](#decipherfinaloutputencoding) methods:

*ES module version:*

```js
import { Buffer } from 'node:buffer';
const {
  scryptSync,
  createDecipheriv,
} = await import('node:crypto');

const algorithm = 'aes-192-cbc';
const password = 'Password used to generate key';
// Use the async `crypto.scrypt()` instead.
const key = scryptSync(password, 'salt', 24);
// The IV is usually passed along with the ciphertext.
const iv = Buffer.alloc(16, 0); // Initialization vector.

const decipher = createDecipheriv(algorithm, key, iv);

// Encrypted using same algorithm, key and iv.
const encrypted =
  'e5f79c5915c02171eec6b212d5520d44480993d7d622a7c4c2da32f6efda0ffa';
let decrypted = decipher.update(encrypted, 'hex', 'utf8');
decrypted += decipher.final('utf8');
console.log(decrypted);
// Prints: some clear text data
```

*CommonJS version:*

```js
const {
  scryptSync,
  createDecipheriv,
} = require('node:crypto');
const { Buffer } = require('node:buffer');

const algorithm = 'aes-192-cbc';
const password = 'Password used to generate key';
// Use the async `crypto.scrypt()` instead.
const key = scryptSync(password, 'salt', 24);
// The IV is usually passed along with the ciphertext.
const iv = Buffer.alloc(16, 0); // Initialization vector.

const decipher = createDecipheriv(algorithm, key, iv);

// Encrypted using same algorithm, key and iv.
const encrypted =
  'e5f79c5915c02171eec6b212d5520d44480993d7d622a7c4c2da32f6efda0ffa';
let decrypted = decipher.update(encrypted, 'hex', 'utf8');
decrypted += decipher.final('utf8');
console.log(decrypted);
// Prints: some clear text data
```

### `decipher.final([outputEncoding])`

Added in: v0.1.94

- `outputEncoding` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) The [encoding](07-buffer-part1.md#buffers-and-character-encodings) of the return value.
- Returns: [`<Buffer>`](07-buffer-part1.md#class-buffer) | [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) Any remaining deciphered contents.
  If `outputEncoding` is specified, a string is
  returned. If an `outputEncoding` is not provided, a [`Buffer`](07-buffer-part1.md) is returned.

If an output encoding was specified in a previous call to
[`decipher.update()`](#decipherupdatedata-inputencoding-outputencoding), `outputEncoding` must use the same encoding.

Once the `decipher.final()` method has been called, the `Decipheriv` object can
no longer be used to decrypt data. Attempts to call `decipher.final()` more
than once will result in an error being thrown.

### `decipher.setAAD(buffer[, options])`

Added in: v1.0.0

**History**

| Version | Changes |
| --- | --- |
| v15.0.0 | The buffer argument can be a string or ArrayBuffer and is limited to no more than 2 \*\* 31 - 1 bytes. |
| v7.2.0 | This method now returns a reference to `decipher`. |

- `buffer` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) | [`<ArrayBuffer>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/ArrayBuffer) | [`<Buffer>`](07-buffer-part1.md#class-buffer) | [`<TypedArray>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/TypedArray) | [`<DataView>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/DataView)
- `options` [`<Object>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Object) [`stream.transform` options](51-stream-part3.md#new-streamtransformoptions)
  - `plaintextLength` [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type)
  - `encoding` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) String encoding to use when `buffer` is a string.
- Returns: [`<Decipheriv>`](15-crypto-part1.md#class-decipheriv) The same `Decipheriv` instance for method chaining.

When using an authenticated encryption mode (`GCM`, `CCM`, `OCB`, `SIV`,
`GCM-SIV`, and `chacha20-poly1305` are currently supported), the
`decipher.setAAD()` method sets the value used for the *additional
authenticated data* (AAD) input parameter.

The `options` argument is optional for `GCM`, `OCB`, `SIV`, and `GCM-SIV`.
When using `CCM`, the `plaintextLength` option must be specified and its value
must match the length of the ciphertext in bytes. See [CCM mode](15-crypto-part6.md#ccm-mode).

The `decipher.setAAD()` method must be called before [`decipher.update()`](#decipherupdatedata-inputencoding-outputencoding).

When passing a string as the `buffer`, please consider
[caveats when using strings as inputs to cryptographic APIs](15-crypto-part6.md#using-strings-as-inputs-to-cryptographic-apis).

### `decipher.setAuthTag(buffer[, encoding])`

Added in: v1.0.0

**History**

| Version | Changes |
| --- | --- |
| v26.0.0 | Using GCM tag lengths other than 128 bits without specifying the `authTagLength` option when creating `decipher` is not allowed anymore. |
| v22.0.0, v20.13.0 | Using GCM tag lengths other than 128 bits without specifying the `authTagLength` option when creating `decipher` is deprecated. |
| v15.0.0 | The buffer argument can be a string or ArrayBuffer and is limited to no more than 2 \*\* 31 - 1 bytes. |
| v11.0.0 | This method now throws if the GCM tag length is invalid. |
| v7.2.0 | This method now returns a reference to `decipher`. |

- `buffer` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) | [`<Buffer>`](07-buffer-part1.md#class-buffer) | [`<ArrayBuffer>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/ArrayBuffer) | [`<TypedArray>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/TypedArray) | [`<DataView>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/DataView)
- `encoding` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) String encoding to use when `buffer` is a string.
- Returns: [`<Decipheriv>`](15-crypto-part1.md#class-decipheriv) The same `Decipheriv` instance for method chaining.

When using an authenticated encryption mode (`GCM`, `CCM`, `OCB`, `SIV`,
`GCM-SIV`, and `chacha20-poly1305` are currently supported), the
`decipher.setAuthTag()` method is used to pass in the received
*authentication tag*. If no tag is provided, or if the cipher text has been
tampered with, [`decipher.final()`](#decipherfinaloutputencoding) will throw, indicating that the cipher
text should be discarded due to failed authentication. If the tag length is
invalid according to [NIST SP 800-38D](https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-38d.pdf) or does not match the value of the
`authTagLength` option, `decipher.setAuthTag()` will throw an error.

The `decipher.setAuthTag()` method must be called before [`decipher.update()`](#decipherupdatedata-inputencoding-outputencoding)
for `CCM`, `SIV`, and `GCM-SIV` modes or before [`decipher.final()`](#decipherfinaloutputencoding) for
`GCM` and `OCB` modes and `chacha20-poly1305`.
`decipher.setAuthTag()` can only be called once.

When passing a string as the authentication tag, please consider
[caveats when using strings as inputs to cryptographic APIs](15-crypto-part6.md#using-strings-as-inputs-to-cryptographic-apis).

### `decipher.setAutoPadding([autoPadding])`

Added in: v0.7.1

- `autoPadding` [`<boolean>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#boolean_type) **Default:** `true`
- Returns: [`<Decipheriv>`](15-crypto-part1.md#class-decipheriv) The same `Decipheriv` instance for method chaining.

When data has been encrypted without standard block padding, calling
`decipher.setAutoPadding(false)` will disable automatic padding to prevent
[`decipher.final()`](#decipherfinaloutputencoding) from checking for and removing padding.

For block ciphers that use standard block padding, disabling it requires the
input data's length to be a multiple of the cipher's block size.

The `decipher.setAutoPadding()` method must be called before
[`decipher.final()`](#decipherfinaloutputencoding).

### `decipher.update(data[, inputEncoding][, outputEncoding])`

Added in: v0.1.94

**History**

| Version | Changes |
| --- | --- |
| v6.0.0 | The default `inputEncoding` changed from `binary` to `utf8`. |

- `data` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) | [`<Buffer>`](07-buffer-part1.md#class-buffer) | [`<TypedArray>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/TypedArray) | [`<DataView>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/DataView)
- `inputEncoding` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) The [encoding](07-buffer-part1.md#buffers-and-character-encodings) of the `data` string.
- `outputEncoding` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) The [encoding](07-buffer-part1.md#buffers-and-character-encodings) of the return value.
- Returns: [`<Buffer>`](07-buffer-part1.md#class-buffer) | [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type)

Updates the decipher with `data`. If the `inputEncoding` argument is given,
the `data`
argument is a string using the specified encoding. If the `inputEncoding`
argument is not given, `data` must be a [`Buffer`](07-buffer-part1.md), `TypedArray`, or
`DataView`. If `data` is a [`Buffer`](07-buffer-part1.md), `TypedArray`, or `DataView`, then
`inputEncoding` is ignored.

The `outputEncoding` specifies the output format of the deciphered
data. If the `outputEncoding`
is specified, a string using the specified encoding is returned. If no
`outputEncoding` is provided, a [`Buffer`](07-buffer-part1.md) is returned.
When `outputEncoding` is specified, it must use the same encoding as previous
calls to `decipher.update()`.

For most algorithms, `decipher.update()` can be called multiple times with new
data until [`decipher.final()`](#decipherfinaloutputencoding) is called. Some algorithms restrict calls to
`decipher.update()`. For example, [CCM mode](15-crypto-part6.md#ccm-mode), [CBC-CTS mode](15-crypto-part6.md#cbc-cts-mode), [XTS mode](15-crypto-part6.md#xts-mode),
[AES key wrap modes](15-crypto-part6.md#aes-key-wrap-modes), and [SIV and GCM-SIV modes](15-crypto-part6.md#siv-and-gcm-siv-modes) require the whole message
in a single call. Calling `decipher.update()` after [`decipher.final()`](#decipherfinaloutputencoding) will
result in an error being thrown.

Even if the underlying cipher implements authentication, the authenticity and
integrity of the plaintext returned from this function may be uncertain at this
time. For authenticated encryption algorithms, authenticity is generally only
established when the application calls [`decipher.final()`](#decipherfinaloutputencoding).

## Class: `DiffieHellman`

Added in: v0.5.0

The `DiffieHellman` class is a utility for creating Diffie-Hellman key
exchanges.

Instances of the `DiffieHellman` class can be created using the
[`crypto.createDiffieHellman()`](15-crypto-part3.md#cryptocreatediffiehellmanprime-primeencoding-generator-generatorencoding) function.

*ES module version:*

```js
import assert from 'node:assert';

const {
  createDiffieHellman,
} = await import('node:crypto');

// Generate Alice's keys...
const alice = createDiffieHellman(2048);
const aliceKey = alice.generateKeys();

// Generate Bob's keys...
const bob = createDiffieHellman(alice.getPrime(), alice.getGenerator());
const bobKey = bob.generateKeys();

// Exchange and generate the secret...
const aliceSecret = alice.computeSecret(bobKey);
const bobSecret = bob.computeSecret(aliceKey);

// OK
assert.strictEqual(aliceSecret.toString('hex'), bobSecret.toString('hex'));
```

*CommonJS version:*

```js
const assert = require('node:assert');

const {
  createDiffieHellman,
} = require('node:crypto');

// Generate Alice's keys...
const alice = createDiffieHellman(2048);
const aliceKey = alice.generateKeys();

// Generate Bob's keys...
const bob = createDiffieHellman(alice.getPrime(), alice.getGenerator());
const bobKey = bob.generateKeys();

// Exchange and generate the secret...
const aliceSecret = alice.computeSecret(bobKey);
const bobSecret = bob.computeSecret(aliceKey);

// OK
assert.strictEqual(aliceSecret.toString('hex'), bobSecret.toString('hex'));
```

### `diffieHellman.computeSecret(otherPublicKey[, inputEncoding][, outputEncoding])`

Added in: v0.5.0

- `otherPublicKey` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) | [`<ArrayBuffer>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/ArrayBuffer) | [`<Buffer>`](07-buffer-part1.md#class-buffer) | [`<TypedArray>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/TypedArray) | [`<DataView>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/DataView)
- `inputEncoding` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) The [encoding](07-buffer-part1.md#buffers-and-character-encodings) of an `otherPublicKey` string.
- `outputEncoding` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) The [encoding](07-buffer-part1.md#buffers-and-character-encodings) of the return value.
- Returns: [`<Buffer>`](07-buffer-part1.md#class-buffer) | [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type)

Computes the shared secret using `otherPublicKey` as the other
party's public key and returns the computed shared secret. The supplied
key is interpreted using the specified `inputEncoding`, and secret is
encoded using specified `outputEncoding`.
If the `inputEncoding` is not
provided, `otherPublicKey` is expected to be a [`Buffer`](07-buffer-part1.md),
`TypedArray`, or `DataView`.

If `outputEncoding` is given a string is returned; otherwise, a
[`Buffer`](07-buffer-part1.md) is returned.

### `diffieHellman.generateKeys([encoding])`

Added in: v0.5.0

- `encoding` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) The [encoding](07-buffer-part1.md#buffers-and-character-encodings) of the return value.
- Returns: [`<Buffer>`](07-buffer-part1.md#class-buffer) | [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type)

Generates private and public Diffie-Hellman key values unless they have been
generated or computed already, and returns
the public key in the specified `encoding`. This key should be
transferred to the other party.
If `encoding` is provided a string is returned; otherwise a
[`Buffer`](07-buffer-part1.md) is returned.

This function is a thin wrapper around [`DH_generate_key()`](https://www.openssl.org/docs/man3.0/man3/DH_generate_key.html). In particular,
once a private key has been generated or set, calling this function only
recomputes the public key from the existing private key. Since the public key is
determined by the private key, the result will be the same unless the private key
has been changed via [`diffieHellman.setPrivateKey()`](15-crypto-part2.md#diffiehellmansetprivatekeyprivatekey-encoding).

### `diffieHellman.getGenerator([encoding])`

Added in: v0.5.0

- `encoding` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) The [encoding](07-buffer-part1.md#buffers-and-character-encodings) of the return value.
- Returns: [`<Buffer>`](07-buffer-part1.md#class-buffer) | [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type)

Returns the Diffie-Hellman generator in the specified `encoding`.
If `encoding` is provided a string is
returned; otherwise a [`Buffer`](07-buffer-part1.md) is returned.

### `diffieHellman.getPrime([encoding])`

Added in: v0.5.0

- `encoding` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) The [encoding](07-buffer-part1.md#buffers-and-character-encodings) of the return value.
- Returns: [`<Buffer>`](07-buffer-part1.md#class-buffer) | [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type)

Returns the Diffie-Hellman prime in the specified `encoding`.
If `encoding` is provided a string is
returned; otherwise a [`Buffer`](07-buffer-part1.md) is returned.

### `diffieHellman.getPrivateKey([encoding])`

Added in: v0.5.0

- `encoding` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) The [encoding](07-buffer-part1.md#buffers-and-character-encodings) of the return value.
- Returns: [`<Buffer>`](07-buffer-part1.md#class-buffer) | [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type)

Returns the Diffie-Hellman private key in the specified `encoding`.
If `encoding` is provided a
string is returned; otherwise a [`Buffer`](07-buffer-part1.md) is returned.

[Contents](../README.md) | [Previous: Console](14-console.md) | [Next: Crypto, part 2 of 6](15-crypto-part2.md)
