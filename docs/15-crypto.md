[Contents](../README.md) | [Previous: Console](14-console.md) | [Next: Debugger](16-debugger.md)

Source: [https://nodejs.org/docs/latest/api/crypto.html](https://nodejs.org/docs/latest/api/crypto.html)

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
[`KeyObject`](#class-keyobject) API by the complete set of formats supported for importing and
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
approach is to import key material into a [`KeyObject`](#class-keyobject) once and reuse it**
for all subsequent operations, as this avoids repeated parsing and delivers
the best performance.

When a [`KeyObject`](#class-keyobject) is not practical - for example, when key material
arrives in a protocol message and is used only once - most cryptographic
functions also accept a PEM string or an object specifying the format
and key material directly. See [`crypto.createPublicKey()`](#cryptocreatepublickeykey),
[`crypto.createPrivateKey()`](#cryptocreateprivatekeykey), and [`keyObject.export()`](#keyobjectexportoptions) for the full
options accepted by each format.

#### KeyObject

A [`KeyObject`](#class-keyobject) is the in-memory representation of a parsed key. It is
created by [`crypto.createPublicKey()`](#cryptocreatepublickeykey), [`crypto.createPrivateKey()`](#cryptocreateprivatekeykey),
[`crypto.createSecretKey()`](#cryptocreatesecretkeykey-encoding), or key generation functions such as
[`crypto.generateKeyPair()`](#cryptogeneratekeypairtype-options-callback). The first cryptographic operation with a given
[`KeyObject`](#class-keyobject) may be slower than subsequent ones because OpenSSL lazily
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
See [`keyObject.export()`](#keyobjectexportoptions), [`crypto.createPublicKey()`](#cryptocreatepublickeykey), and
[`crypto.createPrivateKey()`](#cryptocreateprivatekeykey) for usage details.

`'raw-public'` is generally the fastest way to import a public key.
`'raw-private'` and `'raw-seed'` are not always faster than other formats
because they only contain the private scalar or seed - importing them requires
deriving the public key component (e.g. elliptic curve point multiplication or
seed expansion), which can be expensive. Other formats include both private
and public components, avoiding that computation.

### Choosing a key format

**Always prefer a [`KeyObject`](#class-keyobject)** - create one from whatever format you
have and reuse it. The guidance below applies only when choosing between
serialization formats, either for importing into a [`KeyObject`](#class-keyobject) or for
passing key material inline when a [`KeyObject`](#class-keyobject) is not practical.

#### Importing keys

When creating a [`KeyObject`](#class-keyobject) for repeated use, the import cost is paid once,
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

When a [`KeyObject`](#class-keyobject) cannot be reused (e.g. the key arrives as raw bytes in
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
into a [`KeyObject`](#class-keyobject) rather than passing the raw or PEM representation
repeatedly.

### Examples

Example: Reusing a [`KeyObject`](#class-keyobject) across sign and verify operations:

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

Example: Importing keys of various formats into [`KeyObject`](#class-keyobject)s:

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

Example: Passing key material directly to [`crypto.sign()`](#cryptosignalgorithm-data-key-callback) and
[`crypto.verify()`](#cryptoverifyalgorithm-data-key-signature-callback) without creating a [`KeyObject`](#class-keyobject) first:

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

- `spkac` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) | [`<ArrayBuffer>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/ArrayBuffer) | [`<Buffer>`](07-buffer.md#class-buffer) | [`<TypedArray>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/TypedArray) | [`<DataView>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/DataView)
- `encoding` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) The [encoding](07-buffer.md#buffers-and-character-encodings) of the `spkac` string.
- Returns: [`<Buffer>`](07-buffer.md#class-buffer) The challenge component of the `spkac` data structure, which
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

- `spkac` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) | [`<ArrayBuffer>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/ArrayBuffer) | [`<Buffer>`](07-buffer.md#class-buffer) | [`<TypedArray>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/TypedArray) | [`<DataView>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/DataView)
- `encoding` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) The [encoding](07-buffer.md#buffers-and-character-encodings) of the `spkac` string.
- Returns: [`<Buffer>`](07-buffer.md#class-buffer) The public key component of the `spkac` data structure,
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

- `spkac` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) | [`<ArrayBuffer>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/ArrayBuffer) | [`<Buffer>`](07-buffer.md#class-buffer) | [`<TypedArray>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/TypedArray) | [`<DataView>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/DataView)
- `encoding` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) The [encoding](07-buffer.md#buffers-and-character-encodings) of the `spkac` string.
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

- `spkac` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) | [`<ArrayBuffer>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/ArrayBuffer) | [`<Buffer>`](07-buffer.md#class-buffer) | [`<TypedArray>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/TypedArray) | [`<DataView>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/DataView)
- `encoding` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) The [encoding](07-buffer.md#buffers-and-character-encodings) of the `spkac` string.
- Returns: [`<Buffer>`](07-buffer.md#class-buffer) The challenge component of the `spkac` data structure, which
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

- `spkac` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) | [`<ArrayBuffer>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/ArrayBuffer) | [`<Buffer>`](07-buffer.md#class-buffer) | [`<TypedArray>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/TypedArray) | [`<DataView>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/DataView)
- `encoding` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) The [encoding](07-buffer.md#buffers-and-character-encodings) of the `spkac` string.
- Returns: [`<Buffer>`](07-buffer.md#class-buffer) The public key component of the `spkac` data structure,
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

- `spkac` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) | [`<ArrayBuffer>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/ArrayBuffer) | [`<Buffer>`](07-buffer.md#class-buffer) | [`<TypedArray>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/TypedArray) | [`<DataView>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/DataView)
- `encoding` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) The [encoding](07-buffer.md#buffers-and-character-encodings) of the `spkac` string.
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

- Extends: [`<stream.Transform>`](51-stream.md#class-streamtransform)

Instances of the `Cipheriv` class are used to encrypt data. The class can be
used in one of two ways:

- As a [stream](51-stream.md) that is both readable and writable, where plain unencrypted
  data is written to produce encrypted data on the readable side, or
- Using the [`cipher.update()`](#cipherupdatedata-inputencoding-outputencoding) and [`cipher.final()`](#cipherfinaloutputencoding) methods to produce
  the encrypted data.

The [`crypto.createCipheriv()`](#cryptocreatecipherivalgorithm-key-iv-options) method is
used to create `Cipheriv` instances. `Cipheriv` objects are not to be created
directly using the `new` keyword.

The selected algorithm may impose additional restrictions on streaming and
calls to [`cipher.update()`](#cipherupdatedata-inputencoding-outputencoding). See [CCM mode](#ccm-mode), [CBC-CTS mode](#cbc-cts-mode), [XTS mode](#xts-mode),
[AES key wrap modes](#aes-key-wrap-modes), and [SIV and GCM-SIV modes](#siv-and-gcm-siv-modes).

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

- `outputEncoding` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) The [encoding](07-buffer.md#buffers-and-character-encodings) of the return value.
- Returns: [`<Buffer>`](07-buffer.md#class-buffer) | [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) Any remaining enciphered contents.
  If `outputEncoding` is specified, a string is
  returned. If an `outputEncoding` is not provided, a [`Buffer`](07-buffer.md) is returned.

If an output encoding was specified in a previous call to
[`cipher.update()`](#cipherupdatedata-inputencoding-outputencoding), `outputEncoding` must use the same encoding.

Once the `cipher.final()` method has been called, the `Cipheriv` object can no
longer be used to encrypt data. Attempts to call `cipher.final()` more than
once will result in an error being thrown.

### `cipher.getAuthTag()`

Added in: v1.0.0

- Returns: [`<Buffer>`](07-buffer.md#class-buffer) When using an authenticated encryption mode (`GCM`, `CCM`,
  `OCB`, `SIV`, `GCM-SIV`, and `chacha20-poly1305` are currently
  supported), the `cipher.getAuthTag()` method returns a
  [`Buffer`](07-buffer.md) containing the *authentication tag* that has been computed from
  the given data.

The `cipher.getAuthTag()` method should only be called after encryption has
been completed using the [`cipher.final()`](#cipherfinaloutputencoding) method.

If the `authTagLength` option was set during the `cipher` instance's creation,
this function will return exactly `authTagLength` bytes.

### `cipher.setAAD(buffer[, options])`

Added in: v1.0.0

- `buffer` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) | [`<ArrayBuffer>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/ArrayBuffer) | [`<Buffer>`](07-buffer.md#class-buffer) | [`<TypedArray>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/TypedArray) | [`<DataView>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/DataView)
- `options` [`<Object>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Object) [`stream.transform` options](51-stream.md#new-streamtransformoptions)
  - `plaintextLength` [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type)
  - `encoding` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) The string encoding to use when `buffer` is a string.
- Returns: [`<Cipheriv>`](15-crypto.md#class-cipheriv) The same `Cipheriv` instance for method chaining.

When using an authenticated encryption mode (`GCM`, `CCM`, `OCB`, `SIV`,
`GCM-SIV`, and `chacha20-poly1305` are currently supported), the
`cipher.setAAD()` method sets the value used for the *additional authenticated
data* (AAD) input parameter.

The `plaintextLength` option is optional for `GCM`, `OCB`, `SIV`, and
`GCM-SIV`. When using `CCM`, the `plaintextLength` option must be specified and
its value must match the length of the plaintext in bytes. See [CCM mode](#ccm-mode).

The `cipher.setAAD()` method must be called before [`cipher.update()`](#cipherupdatedata-inputencoding-outputencoding).

### `cipher.setAutoPadding([autoPadding])`

Added in: v0.7.1

- `autoPadding` [`<boolean>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#boolean_type) **Default:** `true`
- Returns: [`<Cipheriv>`](15-crypto.md#class-cipheriv) The same `Cipheriv` instance for method chaining.

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

- `data` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) | [`<Buffer>`](07-buffer.md#class-buffer) | [`<TypedArray>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/TypedArray) | [`<DataView>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/DataView)
- `inputEncoding` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) The [encoding](07-buffer.md#buffers-and-character-encodings) of the data.
- `outputEncoding` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) The [encoding](07-buffer.md#buffers-and-character-encodings) of the return value.
- Returns: [`<Buffer>`](07-buffer.md#class-buffer) | [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type)

Updates the cipher with `data`. If the `inputEncoding` argument is given,
the `data`
argument is a string using the specified encoding. If the `inputEncoding`
argument is not given, `data` must be a [`Buffer`](07-buffer.md), `TypedArray`, or
`DataView`. If `data` is a [`Buffer`](07-buffer.md), `TypedArray`, or `DataView`, then
`inputEncoding` is ignored.

The `outputEncoding` specifies the output format of the enciphered
data. If the `outputEncoding`
is specified, a string using the specified encoding is returned. If no
`outputEncoding` is provided, a [`Buffer`](07-buffer.md) is returned.
When `outputEncoding` is specified, it must use the same encoding as previous
calls to `cipher.update()`.

For most algorithms, `cipher.update()` can be called multiple times with new
data until [`cipher.final()`](#cipherfinaloutputencoding) is called. Some algorithms restrict calls to
`cipher.update()`. For example, [CCM mode](#ccm-mode), [CBC-CTS mode](#cbc-cts-mode), [XTS mode](#xts-mode),
[AES key wrap modes](#aes-key-wrap-modes), and [SIV and GCM-SIV modes](#siv-and-gcm-siv-modes) require the whole message
in a single call. Calling `cipher.update()` after [`cipher.final()`](#cipherfinaloutputencoding) will
result in an error being thrown.

## Class: `Decipheriv`

Added in: v0.1.94

- Extends: [`<stream.Transform>`](51-stream.md#class-streamtransform)

Instances of the `Decipheriv` class are used to decrypt data. The class can be
used in one of two ways:

- As a [stream](51-stream.md) that is both readable and writable, where plain encrypted
  data is written to produce unencrypted data on the readable side, or
- Using the [`decipher.update()`](#decipherupdatedata-inputencoding-outputencoding) and [`decipher.final()`](#decipherfinaloutputencoding) methods to
  produce the unencrypted data.

The [`crypto.createDecipheriv()`](#cryptocreatedecipherivalgorithm-key-iv-options) method is
used to create `Decipheriv` instances. `Decipheriv` objects are not to be created
directly using the `new` keyword.

The selected algorithm may impose additional restrictions on streaming and
calls to [`decipher.update()`](#decipherupdatedata-inputencoding-outputencoding). See [CCM mode](#ccm-mode), [CBC-CTS mode](#cbc-cts-mode),
[XTS mode](#xts-mode), [AES key wrap modes](#aes-key-wrap-modes), and [SIV and GCM-SIV modes](#siv-and-gcm-siv-modes).

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

- `outputEncoding` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) The [encoding](07-buffer.md#buffers-and-character-encodings) of the return value.
- Returns: [`<Buffer>`](07-buffer.md#class-buffer) | [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) Any remaining deciphered contents.
  If `outputEncoding` is specified, a string is
  returned. If an `outputEncoding` is not provided, a [`Buffer`](07-buffer.md) is returned.

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

- `buffer` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) | [`<ArrayBuffer>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/ArrayBuffer) | [`<Buffer>`](07-buffer.md#class-buffer) | [`<TypedArray>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/TypedArray) | [`<DataView>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/DataView)
- `options` [`<Object>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Object) [`stream.transform` options](51-stream.md#new-streamtransformoptions)
  - `plaintextLength` [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type)
  - `encoding` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) String encoding to use when `buffer` is a string.
- Returns: [`<Decipheriv>`](15-crypto.md#class-decipheriv) The same `Decipheriv` instance for method chaining.

When using an authenticated encryption mode (`GCM`, `CCM`, `OCB`, `SIV`,
`GCM-SIV`, and `chacha20-poly1305` are currently supported), the
`decipher.setAAD()` method sets the value used for the *additional
authenticated data* (AAD) input parameter.

The `options` argument is optional for `GCM`, `OCB`, `SIV`, and `GCM-SIV`.
When using `CCM`, the `plaintextLength` option must be specified and its value
must match the length of the ciphertext in bytes. See [CCM mode](#ccm-mode).

The `decipher.setAAD()` method must be called before [`decipher.update()`](#decipherupdatedata-inputencoding-outputencoding).

When passing a string as the `buffer`, please consider
[caveats when using strings as inputs to cryptographic APIs](#using-strings-as-inputs-to-cryptographic-apis).

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

- `buffer` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) | [`<Buffer>`](07-buffer.md#class-buffer) | [`<ArrayBuffer>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/ArrayBuffer) | [`<TypedArray>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/TypedArray) | [`<DataView>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/DataView)
- `encoding` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) String encoding to use when `buffer` is a string.
- Returns: [`<Decipheriv>`](15-crypto.md#class-decipheriv) The same `Decipheriv` instance for method chaining.

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
[caveats when using strings as inputs to cryptographic APIs](#using-strings-as-inputs-to-cryptographic-apis).

### `decipher.setAutoPadding([autoPadding])`

Added in: v0.7.1

- `autoPadding` [`<boolean>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#boolean_type) **Default:** `true`
- Returns: [`<Decipheriv>`](15-crypto.md#class-decipheriv) The same `Decipheriv` instance for method chaining.

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

- `data` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) | [`<Buffer>`](07-buffer.md#class-buffer) | [`<TypedArray>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/TypedArray) | [`<DataView>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/DataView)
- `inputEncoding` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) The [encoding](07-buffer.md#buffers-and-character-encodings) of the `data` string.
- `outputEncoding` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) The [encoding](07-buffer.md#buffers-and-character-encodings) of the return value.
- Returns: [`<Buffer>`](07-buffer.md#class-buffer) | [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type)

Updates the decipher with `data`. If the `inputEncoding` argument is given,
the `data`
argument is a string using the specified encoding. If the `inputEncoding`
argument is not given, `data` must be a [`Buffer`](07-buffer.md), `TypedArray`, or
`DataView`. If `data` is a [`Buffer`](07-buffer.md), `TypedArray`, or `DataView`, then
`inputEncoding` is ignored.

The `outputEncoding` specifies the output format of the deciphered
data. If the `outputEncoding`
is specified, a string using the specified encoding is returned. If no
`outputEncoding` is provided, a [`Buffer`](07-buffer.md) is returned.
When `outputEncoding` is specified, it must use the same encoding as previous
calls to `decipher.update()`.

For most algorithms, `decipher.update()` can be called multiple times with new
data until [`decipher.final()`](#decipherfinaloutputencoding) is called. Some algorithms restrict calls to
`decipher.update()`. For example, [CCM mode](#ccm-mode), [CBC-CTS mode](#cbc-cts-mode), [XTS mode](#xts-mode),
[AES key wrap modes](#aes-key-wrap-modes), and [SIV and GCM-SIV modes](#siv-and-gcm-siv-modes) require the whole message
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
[`crypto.createDiffieHellman()`](#cryptocreatediffiehellmanprime-primeencoding-generator-generatorencoding) function.

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

- `otherPublicKey` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) | [`<ArrayBuffer>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/ArrayBuffer) | [`<Buffer>`](07-buffer.md#class-buffer) | [`<TypedArray>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/TypedArray) | [`<DataView>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/DataView)
- `inputEncoding` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) The [encoding](07-buffer.md#buffers-and-character-encodings) of an `otherPublicKey` string.
- `outputEncoding` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) The [encoding](07-buffer.md#buffers-and-character-encodings) of the return value.
- Returns: [`<Buffer>`](07-buffer.md#class-buffer) | [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type)

Computes the shared secret using `otherPublicKey` as the other
party's public key and returns the computed shared secret. The supplied
key is interpreted using the specified `inputEncoding`, and secret is
encoded using specified `outputEncoding`.
If the `inputEncoding` is not
provided, `otherPublicKey` is expected to be a [`Buffer`](07-buffer.md),
`TypedArray`, or `DataView`.

If `outputEncoding` is given a string is returned; otherwise, a
[`Buffer`](07-buffer.md) is returned.

### `diffieHellman.generateKeys([encoding])`

Added in: v0.5.0

- `encoding` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) The [encoding](07-buffer.md#buffers-and-character-encodings) of the return value.
- Returns: [`<Buffer>`](07-buffer.md#class-buffer) | [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type)

Generates private and public Diffie-Hellman key values unless they have been
generated or computed already, and returns
the public key in the specified `encoding`. This key should be
transferred to the other party.
If `encoding` is provided a string is returned; otherwise a
[`Buffer`](07-buffer.md) is returned.

This function is a thin wrapper around [`DH_generate_key()`](https://www.openssl.org/docs/man3.0/man3/DH_generate_key.html). In particular,
once a private key has been generated or set, calling this function only
recomputes the public key from the existing private key. Since the public key is
determined by the private key, the result will be the same unless the private key
has been changed via [`diffieHellman.setPrivateKey()`](#diffiehellmansetprivatekeyprivatekey-encoding).

### `diffieHellman.getGenerator([encoding])`

Added in: v0.5.0

- `encoding` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) The [encoding](07-buffer.md#buffers-and-character-encodings) of the return value.
- Returns: [`<Buffer>`](07-buffer.md#class-buffer) | [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type)

Returns the Diffie-Hellman generator in the specified `encoding`.
If `encoding` is provided a string is
returned; otherwise a [`Buffer`](07-buffer.md) is returned.

### `diffieHellman.getPrime([encoding])`

Added in: v0.5.0

- `encoding` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) The [encoding](07-buffer.md#buffers-and-character-encodings) of the return value.
- Returns: [`<Buffer>`](07-buffer.md#class-buffer) | [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type)

Returns the Diffie-Hellman prime in the specified `encoding`.
If `encoding` is provided a string is
returned; otherwise a [`Buffer`](07-buffer.md) is returned.

### `diffieHellman.getPrivateKey([encoding])`

Added in: v0.5.0

- `encoding` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) The [encoding](07-buffer.md#buffers-and-character-encodings) of the return value.
- Returns: [`<Buffer>`](07-buffer.md#class-buffer) | [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type)

Returns the Diffie-Hellman private key in the specified `encoding`.
If `encoding` is provided a
string is returned; otherwise a [`Buffer`](07-buffer.md) is returned.

### `diffieHellman.getPublicKey([encoding])`

Added in: v0.5.0

- `encoding` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) The [encoding](07-buffer.md#buffers-and-character-encodings) of the return value.
- Returns: [`<Buffer>`](07-buffer.md#class-buffer) | [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type)

Returns the Diffie-Hellman public key in the specified `encoding`.
If `encoding` is provided a
string is returned; otherwise a [`Buffer`](07-buffer.md) is returned.

### `diffieHellman.setPrivateKey(privateKey[, encoding])`

Added in: v0.5.0

- `privateKey` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) | [`<ArrayBuffer>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/ArrayBuffer) | [`<Buffer>`](07-buffer.md#class-buffer) | [`<TypedArray>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/TypedArray) | [`<DataView>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/DataView)
- `encoding` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) The [encoding](07-buffer.md#buffers-and-character-encodings) of the `privateKey` string.

Sets the Diffie-Hellman private key. If the `encoding` argument is provided,
`privateKey` is expected
to be a string. If no `encoding` is provided, `privateKey` is expected
to be a [`Buffer`](07-buffer.md), `TypedArray`, or `DataView`.

This function does not automatically compute the associated public key. Either
[`diffieHellman.setPublicKey()`](#diffiehellmansetpublickeypublickey-encoding) or [`diffieHellman.generateKeys()`](#diffiehellmangeneratekeysencoding) can be
used to manually provide the public key or to automatically derive it.

### `diffieHellman.setPublicKey(publicKey[, encoding])`

Added in: v0.5.0

- `publicKey` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) | [`<ArrayBuffer>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/ArrayBuffer) | [`<Buffer>`](07-buffer.md#class-buffer) | [`<TypedArray>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/TypedArray) | [`<DataView>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/DataView)
- `encoding` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) The [encoding](07-buffer.md#buffers-and-character-encodings) of the `publicKey` string.

Sets the Diffie-Hellman public key. If the `encoding` argument is provided,
`publicKey` is expected
to be a string. If no `encoding` is provided, `publicKey` is expected
to be a [`Buffer`](07-buffer.md), `TypedArray`, or `DataView`.

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

The following groups are still supported but deprecated (see [Caveats](#support-for-weak-or-compromised-algorithms)):

- `'modp1'` (768 bits, [RFC 2409](https://www.rfc-editor.org/rfc/rfc2409.txt) Section 6.1)
- `'modp2'` (1024 bits, [RFC 2409](https://www.rfc-editor.org/rfc/rfc2409.txt) Section 6.2)
- `'modp5'` (1536 bits, [RFC 3526](https://www.rfc-editor.org/rfc/rfc3526.txt) Section 2)

These deprecated groups might be removed in future versions of Node.js.

## Class: `ECDH`

Added in: v0.11.14

The `ECDH` class is a utility for creating Elliptic Curve Diffie-Hellman (ECDH)
key exchanges.

Instances of the `ECDH` class can be created using the
[`crypto.createECDH()`](#cryptocreateecdhcurvename) function.

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

- `key` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) | [`<ArrayBuffer>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/ArrayBuffer) | [`<Buffer>`](07-buffer.md#class-buffer) | [`<TypedArray>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/TypedArray) | [`<DataView>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/DataView)
- `curve` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type)
- `inputEncoding` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) The [encoding](07-buffer.md#buffers-and-character-encodings) of the `key` string.
- `outputEncoding` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) The [encoding](07-buffer.md#buffers-and-character-encodings) of the return value.
- `format` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) **Default:** `'uncompressed'`
- Returns: [`<Buffer>`](07-buffer.md#class-buffer) | [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type)

Converts the EC Diffie-Hellman public key specified by `key` and `curve` to the
format specified by `format`. The `format` argument specifies point encoding
and can be `'compressed'`, `'uncompressed'` or `'hybrid'`. The supplied key is
interpreted using the specified `inputEncoding`, and the returned key is encoded
using the specified `outputEncoding`.

Use [`crypto.getCurves()`](#cryptogetcurves) to obtain a list of available curve names.
On recent OpenSSL releases, `openssl ecparam -list_curves` will also display
the name and description of each available elliptic curve.

If `format` is not specified the point will be returned in `'uncompressed'`
format.

If the `inputEncoding` is not provided, `key` is expected to be a [`Buffer`](07-buffer.md),
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

- `otherPublicKey` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) | [`<ArrayBuffer>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/ArrayBuffer) | [`<Buffer>`](07-buffer.md#class-buffer) | [`<TypedArray>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/TypedArray) | [`<DataView>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/DataView)
- `inputEncoding` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) The [encoding](07-buffer.md#buffers-and-character-encodings) of the `otherPublicKey` string.
- `outputEncoding` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) The [encoding](07-buffer.md#buffers-and-character-encodings) of the return value.
- Returns: [`<Buffer>`](07-buffer.md#class-buffer) | [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type)

Computes the shared secret using `otherPublicKey` as the other
party's public key and returns the computed shared secret. The supplied
key is interpreted using specified `inputEncoding`, and the returned secret
is encoded using the specified `outputEncoding`.
If the `inputEncoding` is not
provided, `otherPublicKey` is expected to be a [`Buffer`](07-buffer.md), `TypedArray`, or
`DataView`.

If `outputEncoding` is given a string will be returned; otherwise a
[`Buffer`](07-buffer.md) is returned.

`ecdh.computeSecret` will throw an
`ERR_CRYPTO_ECDH_INVALID_PUBLIC_KEY` error when `otherPublicKey`
lies outside of the elliptic curve. Since `otherPublicKey` is
usually supplied from a remote user over an insecure network,
be sure to handle this exception accordingly.

### `ecdh.generateKeys([encoding[, format]])`

Added in: v0.11.14

- `encoding` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) The [encoding](07-buffer.md#buffers-and-character-encodings) of the return value.
- `format` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) **Default:** `'uncompressed'`
- Returns: [`<Buffer>`](07-buffer.md#class-buffer) | [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type)

Generates private and public EC Diffie-Hellman key values, and returns
the public key in the specified `format` and `encoding`. This key should be
transferred to the other party.

The `format` argument specifies point encoding and can be `'compressed'` or
`'uncompressed'`. If `format` is not specified, the point will be returned in
`'uncompressed'` format.

If `encoding` is provided a string is returned; otherwise a [`Buffer`](07-buffer.md)
is returned.

### `ecdh.getPrivateKey([encoding])`

Added in: v0.11.14

- `encoding` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) The [encoding](07-buffer.md#buffers-and-character-encodings) of the return value.
- Returns: [`<Buffer>`](07-buffer.md#class-buffer) | [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) The EC Diffie-Hellman in the specified `encoding`.

If `encoding` is specified, a string is returned; otherwise a [`Buffer`](07-buffer.md) is
returned.

### `ecdh.getPublicKey([encoding][, format])`

Added in: v0.11.14

- `encoding` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) The [encoding](07-buffer.md#buffers-and-character-encodings) of the return value.
- `format` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) **Default:** `'uncompressed'`
- Returns: [`<Buffer>`](07-buffer.md#class-buffer) | [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) The EC Diffie-Hellman public key in the specified `encoding` and `format`.

The `format` argument specifies point encoding and can be `'compressed'` or
`'uncompressed'`. If `format` is not specified the point will be returned in
`'uncompressed'` format.

If `encoding` is specified, a string is returned; otherwise a [`Buffer`](07-buffer.md) is
returned.

### `ecdh.setPrivateKey(privateKey[, encoding])`

Added in: v0.11.14

- `privateKey` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) | [`<ArrayBuffer>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/ArrayBuffer) | [`<Buffer>`](07-buffer.md#class-buffer) | [`<TypedArray>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/TypedArray) | [`<DataView>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/DataView)
- `encoding` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) The [encoding](07-buffer.md#buffers-and-character-encodings) of the `privateKey` string.

Sets the EC Diffie-Hellman private key.
If `encoding` is provided, `privateKey` is expected
to be a string; otherwise `privateKey` is expected to be a [`Buffer`](07-buffer.md),
`TypedArray`, or `DataView`.

If `privateKey` is not valid for the curve specified when the `ECDH` object was
created, an error is thrown. Upon setting the private key, the associated
public point (key) is also generated and set in the `ECDH` object.

### `ecdh.setPublicKey(publicKey[, encoding])`

Added in: v0.11.14Deprecated in: v5.2.0

Stability: 0 - Deprecated

- `publicKey` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) | [`<ArrayBuffer>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/ArrayBuffer) | [`<Buffer>`](07-buffer.md#class-buffer) | [`<TypedArray>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/TypedArray) | [`<DataView>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/DataView)
- `encoding` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) The [encoding](07-buffer.md#buffers-and-character-encodings) of the `publicKey` string.

Sets the EC Diffie-Hellman public key.
If `encoding` is provided `publicKey` is expected to
be a string; otherwise a [`Buffer`](07-buffer.md), `TypedArray`, or `DataView` is expected.

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

- Extends: [`<stream.Transform>`](51-stream.md#class-streamtransform)

The `Hash` class is a utility for creating hash digests of data. It can be
used in one of two ways:

- As a [stream](51-stream.md) that is both readable and writable, where data is written
  to produce a computed hash digest on the readable side, or
- Using the [`hash.update()`](#hashupdatedata-inputencoding) and [`hash.digest()`](#hashdigestencoding) methods to produce the
  computed hash.

The [`crypto.createHash()`](#cryptocreatehashalgorithm-options) method is used to create `Hash` instances. `Hash`
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

- `options` [`<Object>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Object) [`stream.transform` options](51-stream.md#new-streamtransformoptions)
- Returns: [`<Hash>`](15-crypto.md#class-hash)

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

- `encoding` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) The [encoding](07-buffer.md#buffers-and-character-encodings) of the return value.
- Returns: [`<Buffer>`](07-buffer.md#class-buffer) | [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type)

Calculates the digest of all of the data passed to be hashed (using the
[`hash.update()`](#hashupdatedata-inputencoding) method).
If `encoding` is provided a string will be returned; otherwise
a [`Buffer`](07-buffer.md) is returned.

The `Hash` object can not be used again after `hash.digest()` method has been
called. Multiple calls will cause an error to be thrown.

### `hash.update(data[, inputEncoding])`

Added in: v0.1.92

**History**

| Version | Changes |
| --- | --- |
| v6.0.0 | The default `inputEncoding` changed from `binary` to `utf8`. |

- `data` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) | [`<Buffer>`](07-buffer.md#class-buffer) | [`<TypedArray>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/TypedArray) | [`<DataView>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/DataView)
- `inputEncoding` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) The [encoding](07-buffer.md#buffers-and-character-encodings) of the `data` string.

Updates the hash content with the given `data`, the encoding of which
is given in `inputEncoding`.
If `encoding` is not provided, and the `data` is a string, an
encoding of `'utf8'` is enforced. If `data` is a [`Buffer`](07-buffer.md), `TypedArray`, or
`DataView`, then `inputEncoding` is ignored.

This can be called many times with new data as it is streamed.

## Class: `Hmac`

Added in: v0.1.94

- Extends: [`<stream.Transform>`](51-stream.md#class-streamtransform)

The `Hmac` class is a utility for creating cryptographic HMAC digests. It can
be used in one of two ways:

- As a [stream](51-stream.md) that is both readable and writable, where data is written
  to produce a computed HMAC digest on the readable side, or
- Using the [`hmac.update()`](#hmacupdatedata-inputencoding) and [`hmac.digest()`](#hmacdigestencoding) methods to produce the
  computed HMAC digest.

The [`crypto.createHmac()`](#cryptocreatehmacalgorithm-key-options) method is used to create `Hmac` instances. `Hmac`
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

- `encoding` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) The [encoding](07-buffer.md#buffers-and-character-encodings) of the return value.
- Returns: [`<Buffer>`](07-buffer.md#class-buffer) | [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type)

Calculates the HMAC digest of all of the data passed using [`hmac.update()`](#hmacupdatedata-inputencoding).
If `encoding` is
provided a string is returned; otherwise a [`Buffer`](07-buffer.md) is returned;

The `Hmac` object can not be used again after `hmac.digest()` has been
called. Multiple calls to `hmac.digest()` will result in an error being thrown.

### `hmac.update(data[, inputEncoding])`

Added in: v0.1.94

**History**

| Version | Changes |
| --- | --- |
| v6.0.0 | The default `inputEncoding` changed from `binary` to `utf8`. |

- `data` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) | [`<Buffer>`](07-buffer.md#class-buffer) | [`<TypedArray>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/TypedArray) | [`<DataView>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/DataView)
- `inputEncoding` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) The [encoding](07-buffer.md#buffers-and-character-encodings) of the `data` string.

Updates the `Hmac` content with the given `data`, the encoding of which
is given in `inputEncoding`.
If `encoding` is not provided, and the `data` is a string, an
encoding of `'utf8'` is enforced. If `data` is a [`Buffer`](07-buffer.md), `TypedArray`, or
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
[`crypto.createSecretKey()`](#cryptocreatesecretkeykey-encoding), [`crypto.createPublicKey()`](#cryptocreatepublickeykey) and
[`crypto.createPrivateKey()`](#cryptocreateprivatekeykey) methods are used to create `KeyObject`
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
- Returns: [`<KeyObject>`](15-crypto.md#class-keyobject)

Returns the underlying [`<KeyObject>`](15-crypto.md#class-keyobject) of a [`<CryptoKey>`](65-webcrypto.md#class-cryptokey). The returned [`<KeyObject>`](15-crypto.md#class-keyobject)
does not retain any of the restrictions imposed by the Web Crypto API on the
original [`<CryptoKey>`](65-webcrypto.md#class-cryptokey), such as the allowed key usages, the algorithm or hash
algorithm bindings, and the extractability flag. In particular, the underlying
key material of the returned [`<KeyObject>`](15-crypto.md#class-keyobject) can always be exported.

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
supported [asymmetric key types](#asymmetric-key-types).

This property is `undefined` for unrecognized `KeyObject` types and symmetric
keys.

### `keyObject.equals(otherKeyObject)`

Added in: v17.7.0, v16.15.0

- `otherKeyObject` [`<KeyObject>`](15-crypto.md#class-keyobject) A `KeyObject` with which to
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
- Returns: [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) | [`<Buffer>`](07-buffer.md#class-buffer) | [`<Object>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Object)

For symmetric keys, the following encoding options can be used:

- `format` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) Must be `'buffer'` (default) or `'jwk'`.

For public keys, the following encoding options can be used:

- `format` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) Must be `'pem'`, `'der'`, `'jwk'`, or `'raw-public'`.
  See [asymmetric key types](#asymmetric-key-types) for format support.
- `type` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) When `format` is `'pem'` or `'der'`, must be `'pkcs1'`
  (RSA only) or `'spki'`. For EC keys with `'raw-public'` format, may be
  `'uncompressed'` (default) or `'compressed'`. Ignored when `format` is
  `'jwk'`.

For private keys, the following encoding options can be used:

- `format` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) Must be `'pem'`, `'der'`, `'jwk'`, `'raw-private'`,
  or `'raw-seed'`. See [asymmetric key types](#asymmetric-key-types) for format support.
- `type` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) When `format` is `'pem'` or `'der'`, must be `'pkcs1'`
  (RSA only), `'pkcs8'`, or `'sec1'` (EC only). Ignored when `format` is
  `'jwk'`, `'raw-private'`, or `'raw-seed'`.
- `cipher` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) If specified, the private key will be encrypted with
  the given `cipher` and `passphrase` using PKCS#5 v2.0 password based
  encryption. Ignored when `format` is `'jwk'`, `'raw-private'`, or
  `'raw-seed'`.
- `passphrase` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) | [`<Buffer>`](07-buffer.md#class-buffer) The passphrase to use for encryption.
  Required when `cipher` is specified.

The result type depends on the selected encoding format, when PEM the
result is a string, when DER it will be a buffer containing the data
encoded as DER, when [JWK](https://tools.ietf.org/html/rfc7517) it will be an object. Raw formats return a
[`<Buffer>`](07-buffer.md#class-buffer) containing the raw key material.

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

- Extends: [`<stream.Transform>`](51-stream.md#class-streamtransform)

The `Mac` class computes message authentication codes using MAC
implementations supplied by OpenSSL providers. It can be used in one of two
ways:

- As a [stream](51-stream.md) that is both readable and writable, where data is written and
  one authentication tag is produced on the readable side when the writable
  side ends; or
- By calling [`mac.update()`](#macupdatedata-inputencoding) one or more times followed by [`mac.final()`](#macfinaloutputencoding).

Instances of `Mac` are created using [`crypto.createMac()`](#cryptocreatemacalgorithm-key-options). The `Mac` class
is not exported directly by the `node:crypto` module.

Calling `mac.end()` without first writing data computes the authentication tag
for an empty message. If the selected MAC produces a zero-byte tag, such as
when a provider accepts `outputLength: 0`, the readable side ends without
emitting a data chunk because Node.js streams do not emit zero-length chunks.
When using `mac.final()` instead, it returns a zero-length [`Buffer`](07-buffer.md) or an
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

- `outputEncoding` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) The [encoding](07-buffer.md#buffers-and-character-encodings) of the return value.
- Returns: [`<Buffer>`](07-buffer.md#class-buffer) | [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type)

Completes the MAC computation and returns the authentication tag. If
`outputEncoding` is omitted or is `'buffer'`, a [`Buffer`](07-buffer.md) is returned.
Otherwise, a string is returned.

To verify an authentication tag, compare equal-length [`Buffer`](07-buffer.md) values using
[`crypto.timingSafeEqual()`](#cryptotimingsafeequala-b).

The `Mac` object cannot be used again after finalization is attempted,
including when finalization fails. Later calls to `mac.update()` or
`mac.final()` throw `ERR_CRYPTO_MAC_FINALIZED`.

### `mac.update(data[, inputEncoding])`

Added in: v26.9.0

- `data` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) | [`<Buffer>`](07-buffer.md#class-buffer) | [`<TypedArray>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/TypedArray) | [`<DataView>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/DataView)
- `inputEncoding` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) The [encoding](07-buffer.md#buffers-and-character-encodings) of the `data` string.
- Returns: {Mac}

Updates the MAC with `data` and returns the `Mac` object so that calls can be
chained. When `data` is a string, `inputEncoding` defaults to `'utf8'`. When
`data` is a [`Buffer`](07-buffer.md), `TypedArray`, or `DataView`, `inputEncoding` is
ignored.

This method can be called multiple times before finalization. If an underlying
MAC update fails, the `Mac` object cannot be used again. Calling this method
after a previous underlying MAC update failure or after finalization throws
`ERR_CRYPTO_MAC_FINALIZED`.

## Class: `Sign`

Added in: v0.1.92

- Extends: [`<stream.Writable>`](51-stream.md#class-streamwritable)

The `Sign` class is a utility for generating signatures. It can be used in one
of two ways:

- As a writable [stream](51-stream.md), where data to be signed is written and the
  [`sign.sign()`](#signsignprivatekey-outputencoding) method is used to generate and return the signature, or
- Using the [`sign.update()`](#signupdatedata-inputencoding) and [`sign.sign()`](#signsignprivatekey-outputencoding) methods to produce the
  signature.

The [`crypto.createSign()`](#cryptocreatesignalgorithm-options) method is used to create `Sign` instances. The
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

- `privateKey` [`<Object>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Object) | [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) | [`<ArrayBuffer>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/ArrayBuffer) | [`<Buffer>`](07-buffer.md#class-buffer) | [`<TypedArray>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/TypedArray) | [`<DataView>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/DataView) | [`<KeyObject>`](15-crypto.md#class-keyobject) | [`<CryptoKey>`](65-webcrypto.md#class-cryptokey) | [`<URL>`](59-url.md#the-whatwg-url-api)
  - `dsaEncoding` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type)
  - `padding` [`<integer>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type)
  - `saltLength` [`<integer>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type)
- `outputEncoding` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) The [encoding](07-buffer.md#buffers-and-character-encodings) of the return value.
- Returns: [`<Buffer>`](07-buffer.md#class-buffer) | [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type)

Calculates the signature on all the data passed through using either
[`sign.update()`](#signupdatedata-inputencoding) or [`sign.write()`](51-stream.md#writablewritechunk-encoding-callback).

If `privateKey` is not a [`KeyObject`](#class-keyobject), this function behaves as if
`privateKey` had been passed to [`crypto.createPrivateKey()`](#cryptocreateprivatekeykey). When
`privateKey` is a string, `ArrayBuffer`, [`Buffer`](07-buffer.md), `TypedArray`, or
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

If `outputEncoding` is provided a string is returned; otherwise a [`Buffer`](07-buffer.md)
is returned.

The `Sign` object can not be again used after `sign.sign()` method has been
called. Multiple calls to `sign.sign()` will result in an error being thrown.

### `sign.update(data[, inputEncoding])`

Added in: v0.1.92

**History**

| Version | Changes |
| --- | --- |
| v6.0.0 | The default `inputEncoding` changed from `binary` to `utf8`. |

- `data` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) | [`<Buffer>`](07-buffer.md#class-buffer) | [`<TypedArray>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/TypedArray) | [`<DataView>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/DataView)
- `inputEncoding` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) The [encoding](07-buffer.md#buffers-and-character-encodings) of the `data` string.

Updates the `Sign` content with the given `data`, the encoding of which
is given in `inputEncoding`.
If `encoding` is not provided, and the `data` is a string, an
encoding of `'utf8'` is enforced. If `data` is a [`Buffer`](07-buffer.md), `TypedArray`, or
`DataView`, then `inputEncoding` is ignored.

This can be called many times with new data as it is streamed.

## Class: `Verify`

Added in: v0.1.92

- Extends: [`<stream.Writable>`](51-stream.md#class-streamwritable)

The `Verify` class is a utility for verifying signatures. It can be used in one
of two ways:

- As a writable [stream](51-stream.md) where written data is used to validate against the
  supplied signature, or
- Using the [`verify.update()`](#verifyupdatedata-inputencoding) and [`verify.verify()`](#verifyverifykey-signature-signatureencoding) methods to verify
  the signature.

The [`crypto.createVerify()`](#cryptocreateverifyalgorithm-options) method is used to create `Verify` instances.
`Verify` objects are not to be created directly using the `new` keyword.

See [`Sign`](#class-sign) for examples.

### `verify.update(data[, inputEncoding])`

Added in: v0.1.92

**History**

| Version | Changes |
| --- | --- |
| v6.0.0 | The default `inputEncoding` changed from `binary` to `utf8`. |

- `data` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) | [`<Buffer>`](07-buffer.md#class-buffer) | [`<TypedArray>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/TypedArray) | [`<DataView>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/DataView)
- `inputEncoding` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) The [encoding](07-buffer.md#buffers-and-character-encodings) of the `data` string.

Updates the `Verify` content with the given `data`, the encoding of which
is given in `inputEncoding`.
If `inputEncoding` is not provided, and the `data` is a string, an
encoding of `'utf8'` is enforced. If `data` is a [`Buffer`](07-buffer.md), `TypedArray`, or
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

- `key` [`<Object>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Object) | [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) | [`<ArrayBuffer>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/ArrayBuffer) | [`<Buffer>`](07-buffer.md#class-buffer) | [`<TypedArray>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/TypedArray) | [`<DataView>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/DataView) | [`<KeyObject>`](15-crypto.md#class-keyobject) | [`<CryptoKey>`](65-webcrypto.md#class-cryptokey)
  - `dsaEncoding` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type)
  - `padding` [`<integer>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type)
  - `saltLength` [`<integer>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type)
- `signature` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) | [`<ArrayBuffer>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/ArrayBuffer) | [`<Buffer>`](07-buffer.md#class-buffer) | [`<TypedArray>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/TypedArray) | [`<DataView>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/DataView)
- `signatureEncoding` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) The [encoding](07-buffer.md#buffers-and-character-encodings) of the `signature` string.
- Returns: [`<boolean>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#boolean_type) `true` or `false` depending on the validity of the
  signature for the data and public key.

Verifies the provided data using the given `key` and `signature`.

If `key` is not a [`KeyObject`](#class-keyobject), this function behaves as if
`key` had been passed to [`crypto.createPublicKey()`](#cryptocreatepublickeykey). When `key` is a string,
`ArrayBuffer`, [`Buffer`](07-buffer.md), `TypedArray`, or `DataView`, it must contain
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
string; otherwise `signature` is expected to be a [`Buffer`](07-buffer.md),
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

- `buffer` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) | [`<TypedArray>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/TypedArray) | [`<Buffer>`](07-buffer.md#class-buffer) | [`<DataView>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/DataView) A PEM or DER encoded
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

### `x509.checkHost(name[, options])`

Added in: v15.6.0

**History**

| Version | Changes |
| --- | --- |
| v18.0.0 | The subject option now defaults to `'default'`. |
| v17.5.0, v16.15.0 | The subject option can now be set to `'default'`. |

- `name` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type)
- `options` [`<Object>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Object)
  - `subject` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) `'default'`, `'always'`, or `'never'`.
    **Default:** `'default'`.
  - `wildcards` [`<boolean>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#boolean_type) **Default:** `true`.
  - `partialWildcards` [`<boolean>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#boolean_type) **Default:** `true`.
  - `multiLabelWildcards` [`<boolean>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#boolean_type) **Default:** `false`.
  - `singleLabelSubdomains` [`<boolean>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#boolean_type) **Default:** `false`.
- Returns: [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) | [`<undefined>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#undefined_type) Returns a subject name that matches `name`,
  or `undefined` if no subject name matches `name`.

Checks whether the certificate matches the given host name.

If the certificate matches the given host name, the matching subject name is
returned. The returned name might be an exact match (e.g., `foo.example.com`)
or it might contain wildcards (e.g., `*.example.com`). Because host name
comparisons are case-insensitive, the returned subject name might also differ
from the given `name` in capitalization.

If the `'subject'` option is undefined or set to `'default'`, the certificate
subject is considered according to OpenSSL's default behavior.

If the `'subject'` option is set to `'always'` and if the subject alternative
name extension either does not exist or does not contain a matching DNS name,
the certificate subject is considered.

If the `'subject'` option is set to `'never'`, the certificate subject is never
considered, even if the certificate contains no subject alternative names.

### `x509.checkIP(ip)`

Added in: v15.6.0

**History**

| Version | Changes |
| --- | --- |
| v17.5.0, v16.14.1 | The `options` argument has been removed since it had no effect. |

- `ip` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type)
- Returns: [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) | [`<undefined>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#undefined_type) Returns `ip` if the certificate matches,
  `undefined` if it does not.

Checks whether the certificate matches the given IP address (IPv4 or IPv6).

Only [RFC 5280](https://www.rfc-editor.org/rfc/rfc5280.txt) `iPAddress` subject alternative names are considered, and they
must match the given `ip` address exactly. Other subject alternative names as
well as the subject field of the certificate are ignored.

### `x509.checkIssued(otherCert)`

Added in: v15.6.0

- `otherCert` [`<X509Certificate>`](15-crypto.md#class-x509certificate)
- Returns: [`<boolean>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#boolean_type)

Checks whether this certificate was potentially issued by the given `otherCert`
by comparing the certificate metadata.

This is useful for pruning a list of possible issuer certificates which have been
selected using a more rudimentary filtering routine, i.e. just based on subject
and issuer names.

Finally, to verify that this certificate's signature was produced by a private key
corresponding to `otherCert`'s public key use [`x509.verify(publicKey)`](#x509verifypublickey)
with `otherCert`'s public key represented as a [`KeyObject`](#class-keyobject)
like so

```js
if (!x509.verify(otherCert.publicKey)) {
  throw new Error('otherCert did not issue x509');
}
```

### `x509.checkPrivateKey(privateKey)`

Added in: v15.6.0

- `privateKey` [`<KeyObject>`](15-crypto.md#class-keyobject) A private key.
- Returns: [`<boolean>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#boolean_type)

Checks whether the public key for this certificate is consistent with
the given private key.

### `x509.fingerprint`

Added in: v15.6.0

- Type: [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type)

The SHA-1 fingerprint of this certificate.

Because SHA-1 is cryptographically broken and because the security of SHA-1 is
significantly worse than that of algorithms that are commonly used to sign
certificates, consider using [`x509.fingerprint256`](#x509fingerprint256) instead.

### `x509.fingerprint256`

Added in: v15.6.0

- Type: [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type)

The SHA-256 fingerprint of this certificate.

### `x509.fingerprint512`

Added in: v17.2.0, v16.14.0

- Type: [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type)

The SHA-512 fingerprint of this certificate.

Because computing the SHA-256 fingerprint is usually faster and because it is
only half the size of the SHA-512 fingerprint, [`x509.fingerprint256`](#x509fingerprint256) may be
a better choice. While SHA-512 presumably provides a higher level of security in
general, the security of SHA-256 matches that of most algorithms that are
commonly used to sign certificates.

### `x509.infoAccess`

Added in: v15.6.0

**History**

| Version | Changes |
| --- | --- |
| v17.3.1, v16.13.2 | Parts of this string may be encoded as JSON string literals in response to CVE-2021-44532. |

- Type: [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type)

A textual representation of the certificate's authority information access
extension.

This is a line feed separated list of access descriptions. Each line begins with
the access method and the kind of the access location, followed by a colon and
the value associated with the access location.

After the prefix denoting the access method and the kind of the access location,
the remainder of each line might be enclosed in quotes to indicate that the
value is a JSON string literal. For backward compatibility, Node.js only uses
JSON string literals within this property when necessary to avoid ambiguity.
Third-party code should be prepared to handle both possible entry formats.

### `x509.issuer`

Added in: v15.6.0

- Type: [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type)

The issuer identification included in this certificate.

### `x509.issuerCertificate`

Added in: v15.9.0

- Type: [`<X509Certificate>`](15-crypto.md#class-x509certificate)

The issuer certificate or `undefined` if the issuer certificate is not
available.

### `x509.keyUsage`

Added in: v15.6.0

- Type: [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type)[]

An array detailing the key extended usages for this certificate.

### `x509.publicKey`

Added in: v15.6.0

- Type: [`<KeyObject>`](15-crypto.md#class-keyobject)

The public key [`<KeyObject>`](15-crypto.md#class-keyobject) for this certificate.

### `x509.raw`

Added in: v15.6.0

- Type: [`<Buffer>`](07-buffer.md#class-buffer)

A `Buffer` containing the DER encoding of this certificate.

### `x509.serialNumber`

Added in: v15.6.0

- Type: [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type)

The serial number of this certificate.

Serial numbers are assigned by certificate authorities and do not uniquely
identify certificates. Consider using [`x509.fingerprint256`](#x509fingerprint256) as a unique
identifier instead.

### `x509.subject`

Added in: v15.6.0

- Type: [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type)

The complete subject of this certificate.

### `x509.subjectAltName`

Added in: v15.6.0

**History**

| Version | Changes |
| --- | --- |
| v17.3.1, v16.13.2 | Parts of this string may be encoded as JSON string literals in response to CVE-2021-44532. |

- Type: [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type)

The subject alternative name specified for this certificate.

This is a comma-separated list of subject alternative names. Each entry begins
with a string identifying the kind of the subject alternative name followed by
a colon and the value associated with the entry.

Earlier versions of Node.js incorrectly assumed that it is safe to split this
property at the two-character sequence `', '` (see [CVE-2021-44532](https://cve.mitre.org/cgi-bin/cvename.cgi?name=CVE-2021-44532)). However,
both malicious and legitimate certificates can contain subject alternative names
that include this sequence when represented as a string.

After the prefix denoting the type of the entry, the remainder of each entry
might be enclosed in quotes to indicate that the value is a JSON string literal.
For backward compatibility, Node.js only uses JSON string literals within this
property when necessary to avoid ambiguity. Third-party code should be prepared
to handle both possible entry formats.

### `x509.toJSON()`

Added in: v15.6.0

- Type: [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type)

There is no standard JSON encoding for X509 certificates. The
`toJSON()` method returns a string containing the PEM encoded
certificate.

### `x509.toLegacyObject()`

Added in: v15.6.0

- Type: [`<Object>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Object)

Returns information about this certificate using the legacy
[certificate object](55-tls.md#certificate-object) encoding.

### `x509.toString()`

Added in: v15.6.0

- Type: [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type)

Returns the PEM-encoded certificate.

### `x509.validFrom`

Added in: v15.6.0

- Type: [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type)

The date/time from which this certificate is valid.

### `x509.validFromDate`

Added in: v23.0.0, v22.10.0

- Type: [`<Date>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Date)

The date/time from which this certificate is valid, encapsulated in a `Date` object.

### `x509.validTo`

Added in: v15.6.0

- Type: [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type)

The date/time until which this certificate is valid.

### `x509.validToDate`

Added in: v23.0.0, v22.10.0

- Type: [`<Date>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Date)

The date/time until which this certificate is valid, encapsulated in a `Date` object.

### `x509.signatureAlgorithm`

Added in: v24.9.0

- Type: [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) | [`<undefined>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#undefined_type)

The algorithm used to sign the certificate or `undefined` if the signature algorithm is unknown by OpenSSL.

### `x509.signatureAlgorithmOid`

Added in: v24.9.0

- Type: [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type)

The OID of the algorithm used to sign the certificate.

### `x509.verify(publicKey)`

Added in: v15.6.0

- `publicKey` [`<KeyObject>`](15-crypto.md#class-keyobject) A public key.
- Returns: [`<boolean>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#boolean_type)

Verifies that this certificate was signed by the given public key.
Does not perform any other validation checks on the certificate.

## `node:crypto` module methods and properties

### `crypto.argon2(algorithm, parameters, callback)`

Added in: v24.7.0

- `algorithm` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) Variant of Argon2, one of `"argon2d"`, `"argon2i"` or `"argon2id"`.
- `parameters` [`<Object>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Object)
  - `message` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) | [`<ArrayBuffer>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/ArrayBuffer) | [`<Buffer>`](07-buffer.md#class-buffer) | [`<TypedArray>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/TypedArray) | [`<DataView>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/DataView) REQUIRED, this is the password for password
    hashing applications of Argon2.
  - `nonce` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) | [`<ArrayBuffer>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/ArrayBuffer) | [`<Buffer>`](07-buffer.md#class-buffer) | [`<TypedArray>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/TypedArray) | [`<DataView>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/DataView) REQUIRED, must be at
    least 8 bytes long. This is the salt for password hashing applications of Argon2.
  - `parallelism` [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) REQUIRED, degree of parallelism determines how many computational chains (lanes)
    can be run. Must be at least `1` and at most `2**24-1`.
  - `tagLength` [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) REQUIRED, the length of the key to generate. Must be at least `4` and
    at most `2**32-1`.
  - `memory` [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) REQUIRED, memory cost in 1KiB blocks. Must be at least `8 * parallelism` and at most `2**32-1`. The actual number of blocks is rounded
    down to the nearest multiple of `4 * parallelism`.
  - `passes` [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) REQUIRED, number of passes (iterations). Must be at least `1` and at most
    `2**32-1`.
  - `secret` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) | [`<ArrayBuffer>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/ArrayBuffer) | [`<Buffer>`](07-buffer.md#class-buffer) | [`<TypedArray>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/TypedArray) | [`<DataView>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/DataView) | [`<undefined>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#undefined_type) OPTIONAL, Random additional input,
    similar to the salt, that should **NOT** be stored with the derived key. This is known as pepper in
    password hashing applications. If used, must have a length not greater than `2**32-1` bytes.
  - `associatedData` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) | [`<ArrayBuffer>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/ArrayBuffer) | [`<Buffer>`](07-buffer.md#class-buffer) | [`<TypedArray>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/TypedArray) | [`<DataView>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/DataView) | [`<undefined>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#undefined_type) OPTIONAL, Additional data to
    be added to the hash, functionally equivalent to salt or secret, but meant for
    non-random data. If used, must have a length not greater than `2**32-1` bytes.
- `callback` [`<Function>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Function)
  - `err` [`<Error>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Error)
  - `derivedKey` [`<Buffer>`](07-buffer.md#class-buffer)

Provides an asynchronous [Argon2](https://www.rfc-editor.org/rfc/rfc9106.html) implementation. Argon2 is a password-based
key derivation function that is designed to be expensive computationally and
memory-wise in order to make brute-force attacks unrewarding.

The `nonce` should be as unique as possible. It is recommended that a nonce is
random and at least 16 bytes long. See [NIST SP 800-132](https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-132.pdf) for details.

When passing strings for `message`, `nonce`, `secret` or `associatedData`, please
consider [caveats when using strings as inputs to cryptographic APIs](#using-strings-as-inputs-to-cryptographic-apis).

The `callback` function is called with two arguments: `err` and `derivedKey`.
`err` is an exception object when key derivation fails, otherwise `err` is
`null`. `derivedKey` is passed to the callback as a [`Buffer`](07-buffer.md).

An exception is thrown when any of the input arguments specify invalid values
or types.

*ES module version:*

```js
const { argon2, randomBytes } = await import('node:crypto');

const parameters = {
  message: 'password',
  nonce: randomBytes(16),
  parallelism: 4,
  tagLength: 64,
  memory: 65536,
  passes: 3,
};

argon2('argon2id', parameters, (err, derivedKey) => {
  if (err) throw err;
  console.log(derivedKey.toString('hex'));  // 'af91dad...9520f15'
});
```

*CommonJS version:*

```js
const { argon2, randomBytes } = require('node:crypto');

const parameters = {
  message: 'password',
  nonce: randomBytes(16),
  parallelism: 4,
  tagLength: 64,
  memory: 65536,
  passes: 3,
};

argon2('argon2id', parameters, (err, derivedKey) => {
  if (err) throw err;
  console.log(derivedKey.toString('hex'));  // 'af91dad...9520f15'
});
```

### `crypto.argon2Sync(algorithm, parameters)`

Added in: v24.7.0

- `algorithm` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) Variant of Argon2, one of `"argon2d"`, `"argon2i"` or `"argon2id"`.
- `parameters` [`<Object>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Object)
  - `message` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) | [`<ArrayBuffer>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/ArrayBuffer) | [`<Buffer>`](07-buffer.md#class-buffer) | [`<TypedArray>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/TypedArray) | [`<DataView>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/DataView) REQUIRED, this is the password for password
    hashing applications of Argon2.
  - `nonce` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) | [`<ArrayBuffer>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/ArrayBuffer) | [`<Buffer>`](07-buffer.md#class-buffer) | [`<TypedArray>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/TypedArray) | [`<DataView>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/DataView) REQUIRED, must be at
    least 8 bytes long. This is the salt for password hashing applications of Argon2.
  - `parallelism` [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) REQUIRED, degree of parallelism determines how many computational chains (lanes)
    can be run. Must be at least 1 and at most `2**24-1`.
  - `tagLength` [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) REQUIRED, the length of the key to generate. Must be at least `4` and
    at most `2**32-1`.
  - `memory` [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) REQUIRED, memory cost in 1KiB blocks. Must be at least `8 * parallelism` and at most `2**32-1`. The actual number of blocks is rounded
    down to the nearest multiple of `4 * parallelism`.
  - `passes` [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) REQUIRED, number of passes (iterations). Must be at least `1` and at most
    `2**32-1`.
  - `secret` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) | [`<ArrayBuffer>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/ArrayBuffer) | [`<Buffer>`](07-buffer.md#class-buffer) | [`<TypedArray>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/TypedArray) | [`<DataView>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/DataView) | [`<undefined>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#undefined_type) OPTIONAL, Random additional input,
    similar to the salt, that should **NOT** be stored with the derived key. This is known as pepper in
    password hashing applications. If used, must have a length not greater than `2**32-1` bytes.
  - `associatedData` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) | [`<ArrayBuffer>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/ArrayBuffer) | [`<Buffer>`](07-buffer.md#class-buffer) | [`<TypedArray>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/TypedArray) | [`<DataView>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/DataView) | [`<undefined>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#undefined_type) OPTIONAL, Additional data to
    be added to the hash, functionally equivalent to salt or secret, but meant for
    non-random data. If used, must have a length not greater than `2**32-1` bytes.
- Returns: [`<Buffer>`](07-buffer.md#class-buffer)

Provides a synchronous [Argon2](https://www.rfc-editor.org/rfc/rfc9106.html) implementation. Argon2 is a password-based
key derivation function that is designed to be expensive computationally and
memory-wise in order to make brute-force attacks unrewarding.

The `nonce` should be as unique as possible. It is recommended that a nonce is
random and at least 16 bytes long. See [NIST SP 800-132](https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-132.pdf) for details.

When passing strings for `message`, `nonce`, `secret` or `associatedData`, please
consider [caveats when using strings as inputs to cryptographic APIs](#using-strings-as-inputs-to-cryptographic-apis).

An exception is thrown when key derivation fails, otherwise the derived key is
returned as a [`Buffer`](07-buffer.md).

An exception is thrown when any of the input arguments specify invalid values
or types.

*ES module version:*

```js
const { argon2Sync, randomBytes } = await import('node:crypto');

const parameters = {
  message: 'password',
  nonce: randomBytes(16),
  parallelism: 4,
  tagLength: 64,
  memory: 65536,
  passes: 3,
};

const derivedKey = argon2Sync('argon2id', parameters);
console.log(derivedKey.toString('hex'));  // 'af91dad...9520f15'
```

*CommonJS version:*

```js
const { argon2Sync, randomBytes } = require('node:crypto');

const parameters = {
  message: 'password',
  nonce: randomBytes(16),
  parallelism: 4,
  tagLength: 64,
  memory: 65536,
  passes: 3,
};

const derivedKey = argon2Sync('argon2id', parameters);
console.log(derivedKey.toString('hex'));  // 'af91dad...9520f15'
```

### `crypto.checkPrime(candidate[, options], callback)`

Added in: v15.8.0

**History**

| Version | Changes |
| --- | --- |
| v18.0.0 | Passing an invalid callback to the `callback` argument now throws `ERR_INVALID_ARG_TYPE` instead of `ERR_INVALID_CALLBACK`. |

- `candidate` [`<ArrayBuffer>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/ArrayBuffer) | [`<SharedArrayBuffer>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/SharedArrayBuffer) | [`<TypedArray>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/TypedArray) | [`<Buffer>`](07-buffer.md#class-buffer) | [`<DataView>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/DataView) | [`<bigint>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#bigint_type)
  A possible prime encoded as a sequence of big endian octets of arbitrary
  length.
- `options` [`<Object>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Object)
  - `checks` [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) The number of Miller-Rabin probabilistic primality
    iterations to perform. When the value is `0` (zero), a number of checks
    is used that yields a false positive rate of at most 2-64 for
    random input. Care must be used when selecting a number of checks. Refer
    to the OpenSSL documentation for the [`BN_is_prime_ex`](https://www.openssl.org/docs/man1.1.1/man3/BN_is_prime_ex.html) function `nchecks`
    options for more details. **Default:** `0`
- `callback` [`<Function>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Function)
  - `err` [`<Error>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Error) Set to an [`<Error>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Error) object if an error occurred during check.
  - `result` [`<boolean>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#boolean_type) `true` if the candidate is a prime with an error
    probability less than `0.25 ** options.checks`.

Checks the primality of the `candidate`.

### `crypto.checkPrimeSync(candidate[, options])`

Added in: v15.8.0

- `candidate` [`<ArrayBuffer>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/ArrayBuffer) | [`<SharedArrayBuffer>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/SharedArrayBuffer) | [`<TypedArray>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/TypedArray) | [`<Buffer>`](07-buffer.md#class-buffer) | [`<DataView>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/DataView) | [`<bigint>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#bigint_type)
  A possible prime encoded as a sequence of big endian octets of arbitrary
  length.
- `options` [`<Object>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Object)
  - `checks` [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) The number of Miller-Rabin probabilistic primality
    iterations to perform. When the value is `0` (zero), a number of checks
    is used that yields a false positive rate of at most 2-64 for
    random input. Care must be used when selecting a number of checks. Refer
    to the OpenSSL documentation for the [`BN_is_prime_ex`](https://www.openssl.org/docs/man1.1.1/man3/BN_is_prime_ex.html) function `nchecks`
    options for more details. **Default:** `0`
- Returns: [`<boolean>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#boolean_type) `true` if the candidate is a prime with an error
  probability less than `0.25 ** options.checks`.

Checks the primality of the `candidate`.

### `crypto.constants`

Added in: v6.3.0

- Type: [`<Object>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Object)

An object containing commonly used constants for crypto and security related
operations. The specific constants currently defined are described in
[Crypto constants](#crypto-constants).

### `crypto.createCipheriv(algorithm, key, iv[, options])`

Added in: v0.1.94

**History**

| Version | Changes |
| --- | --- |
| v26.9.0 | Additional ciphers available through OpenSSL providers (e.g. SM4-GCM, SM4-CCM, SM4-XTS, CBC-CTS, and AES key-wrap variants) are now supported. The `ctsMode` and `xtsStandard` options were added. |
| v26.8.0 | Ciphers in SIV and GCM-SIV modes are now supported. |
| v26.0.0 | Passing a CryptoKey as `key` is deprecated. |
| v17.9.0, v16.17.0 | The `authTagLength` option is now optional when using the `chacha20-poly1305` cipher and defaults to 16 bytes. |
| v15.0.0 | The password and iv arguments can be an ArrayBuffer and are each limited to a maximum of 2 \*\* 31 - 1 bytes. |
| v11.6.0 | The `key` argument can now be a `KeyObject`. |
| v11.2.0, v10.17.0 | The cipher `chacha20-poly1305` (the IETF variant of ChaCha20-Poly1305) is now supported. |
| v10.10.0 | Ciphers in OCB mode are now supported. |
| v10.2.0 | The `authTagLength` option can now be used to produce shorter authentication tags in GCM mode and defaults to 16 bytes. |
| v9.9.0 | The `iv` parameter may now be `null` for ciphers which do not need an initialization vector. |

- `algorithm` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type)
- `key` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) | [`<ArrayBuffer>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/ArrayBuffer) | [`<Buffer>`](07-buffer.md#class-buffer) | [`<TypedArray>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/TypedArray) | [`<DataView>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/DataView) | [`<KeyObject>`](15-crypto.md#class-keyobject) | [`<CryptoKey>`](65-webcrypto.md#class-cryptokey)
- `iv` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) | [`<ArrayBuffer>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/ArrayBuffer) | [`<Buffer>`](07-buffer.md#class-buffer) | [`<TypedArray>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/TypedArray) | [`<DataView>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/DataView) | [`<null>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#null_type)
- `options` [`<Object>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Object) [`stream.transform` options](51-stream.md#new-streamtransformoptions) with these additional
  properties:
  - `authTagLength` [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) The authentication tag length in bytes. Its
    requirements and default depend on the authenticated cipher, as described
    below.
  - `ctsMode` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) The [CBC-CTS mode](#cbc-cts-mode) variant. One of `'CS1'`, `'CS2'`,
    or `'CS3'`. The values are case-sensitive. **Default:** `'CS1'`.
  - `encoding` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) The [encoding](07-buffer.md#buffers-and-character-encodings) to use when `key` is a string. This
    option does not affect a string `iv`, which is always interpreted as UTF-8.
    **Default:** `'utf8'`.
  - `xtsStandard` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) The standard used by `sm4-xts`. One of `'GB'` or
    `'IEEE'`. The values are case-sensitive. **Default:** `'GB'`.
- Returns: [`<Cipheriv>`](15-crypto.md#class-cipheriv)

Creates and returns a `Cipheriv` object, with the given `algorithm`, `key` and
initialization vector (`iv`).

The `options` argument controls cipher-specific settings and stream behavior.
It is optional except when a cipher in CCM or OCB mode (e.g. `'aes-128-ccm'`)
is used. In that case, the `authTagLength` option is required and specifies the
length of the authentication tag in bytes, see [CCM mode](#ccm-mode). In GCM mode, the
`authTagLength` option is not required but can be used to set the length of the
authentication tag that will be returned by `getAuthTag()` and defaults to 16
bytes.
For `SIV`, `GCM-SIV`, and `chacha20-poly1305`, the `authTagLength` option
defaults to 16 bytes. `SIV` and `GCM-SIV` only support 16-byte authentication
tags.

The `ctsMode` and `xtsStandard` options configure parameters exposed by OpenSSL
providers. They are available only with OpenSSL 3.0 or later and a provider
that supports the corresponding parameter. `ctsMode` applies only to CBC-CTS
ciphers, and `xtsStandard` applies only to `sm4-xts`. Supplying either option
for an available cipher implementation that does not support it throws an
`ERR_CRYPTO_UNSUPPORTED_OPERATION` error. See [CBC-CTS mode](#cbc-cts-mode) and [XTS mode](#xts-mode)
for details.

The available algorithms depend on OpenSSL. [`crypto.getCiphers()`](#cryptogetciphers) lists the
algorithms exposed by Node.js. On recent OpenSSL releases,
`openssl list -cipher-algorithms` displays the algorithms available to OpenSSL,
which can include algorithms that Node.js does not expose.

The `key` is the raw key used by the `algorithm` and `iv` is an
[initialization vector](https://en.wikipedia.org/wiki/Initialization_vector). Each may be a string, `ArrayBuffer`, [`Buffer`](07-buffer.md),
`TypedArray`, or `DataView`. A string `key` is decoded using `options.encoding`,
which defaults to `'utf8'`; a string `iv` is always decoded as UTF-8. The `key`
may optionally be a [`KeyObject`](#class-keyobject) of type `secret`. If the cipher does not
need an initialization vector, `iv` may be `null`.

When passing strings for `key` or `iv`, please consider
[caveats when using strings as inputs to cryptographic APIs](#using-strings-as-inputs-to-cryptographic-apis).

Initialization vector requirements depend on the algorithm. For some
algorithms, an IV must be unpredictable and unique; for others, uniqueness
alone is sufficient, a fixed value is required, or no IV is used. Follow the
requirements for the selected algorithm. IVs typically do not have to be secret
and can be transmitted with the ciphertext.

### `crypto.createDecipheriv(algorithm, key, iv[, options])`

Added in: v0.1.94

**History**

| Version | Changes |
| --- | --- |
| v26.9.0 | Additional ciphers available through OpenSSL providers (e.g. SM4-GCM, SM4-CCM, SM4-XTS, CBC-CTS, and AES key-wrap variants) are now supported. The `ctsMode` and `xtsStandard` options were added. |
| v26.8.0 | Ciphers in SIV and GCM-SIV modes are now supported. |
| v26.0.0 | Passing a CryptoKey as `key` is deprecated. |
| v17.9.0, v16.17.0 | The `authTagLength` option is now optional when using the `chacha20-poly1305` cipher and defaults to 16 bytes. |
| v11.6.0 | The `key` argument can now be a `KeyObject`. |
| v11.2.0, v10.17.0 | The cipher `chacha20-poly1305` (the IETF variant of ChaCha20-Poly1305) is now supported. |
| v10.10.0 | Ciphers in OCB mode are now supported. |
| v10.2.0 | The `authTagLength` option can now be used to restrict accepted GCM authentication tag lengths. |
| v9.9.0 | The `iv` parameter may now be `null` for ciphers which do not need an initialization vector. |

- `algorithm` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type)
- `key` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) | [`<ArrayBuffer>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/ArrayBuffer) | [`<Buffer>`](07-buffer.md#class-buffer) | [`<TypedArray>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/TypedArray) | [`<DataView>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/DataView) | [`<KeyObject>`](15-crypto.md#class-keyobject) | [`<CryptoKey>`](65-webcrypto.md#class-cryptokey)
- `iv` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) | [`<ArrayBuffer>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/ArrayBuffer) | [`<Buffer>`](07-buffer.md#class-buffer) | [`<TypedArray>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/TypedArray) | [`<DataView>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/DataView) | [`<null>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#null_type)
- `options` [`<Object>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Object) [`stream.transform` options](51-stream.md#new-streamtransformoptions) with these additional
  properties:
  - `authTagLength` [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) The authentication tag length in bytes. Its
    requirements and default depend on the authenticated cipher, as described
    below.
  - `ctsMode` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) The [CBC-CTS mode](#cbc-cts-mode) variant. One of `'CS1'`, `'CS2'`,
    or `'CS3'`. The values are case-sensitive. **Default:** `'CS1'`.
  - `encoding` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) The [encoding](07-buffer.md#buffers-and-character-encodings) to use when `key` is a string. This
    option does not affect a string `iv`, which is always interpreted as UTF-8.
    **Default:** `'utf8'`.
  - `xtsStandard` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) The standard used by `sm4-xts`. One of `'GB'` or
    `'IEEE'`. The values are case-sensitive. **Default:** `'GB'`.
- Returns: [`<Decipheriv>`](15-crypto.md#class-decipheriv)

Creates and returns a `Decipheriv` object that uses the given `algorithm`, `key`
and initialization vector (`iv`).

The `options` argument controls cipher-specific settings and stream behavior.
It is optional except when a cipher in CCM or OCB mode (e.g. `'aes-128-ccm'`)
is used. In that case, the `authTagLength` option is required and specifies the
length of the authentication tag in bytes, see [CCM mode](#ccm-mode). For GCM and
`chacha20-poly1305`, the `authTagLength` option defaults to 16 bytes and must be
set if a different length is used. For `SIV` and `GCM-SIV`, the `authTagLength`
option defaults to 16 bytes and only 16-byte authentication tags are supported.

The `ctsMode` and `xtsStandard` options configure parameters exposed by OpenSSL
providers. They are available only with OpenSSL 3.0 or later and a provider
that supports the corresponding parameter. `ctsMode` applies only to CBC-CTS
ciphers, and `xtsStandard` applies only to `sm4-xts`. Supplying either option
for an available cipher implementation that does not support it throws an
`ERR_CRYPTO_UNSUPPORTED_OPERATION` error. See [CBC-CTS mode](#cbc-cts-mode) and [XTS mode](#xts-mode)
for details.

The available algorithms depend on OpenSSL. [`crypto.getCiphers()`](#cryptogetciphers) lists the
algorithms exposed by Node.js. On recent OpenSSL releases,
`openssl list -cipher-algorithms` displays the algorithms available to OpenSSL,
which can include algorithms that Node.js does not expose.

The `key` is the raw key used by the `algorithm` and `iv` is an
[initialization vector](https://en.wikipedia.org/wiki/Initialization_vector). Each may be a string, `ArrayBuffer`, [`Buffer`](07-buffer.md),
`TypedArray`, or `DataView`. A string `key` is decoded using `options.encoding`,
which defaults to `'utf8'`; a string `iv` is always decoded as UTF-8. The `key`
may optionally be a [`KeyObject`](#class-keyobject) of type `secret`. If the cipher does not
need an initialization vector, `iv` may be `null`.

When passing strings for `key` or `iv`, please consider
[caveats when using strings as inputs to cryptographic APIs](#using-strings-as-inputs-to-cryptographic-apis).

Initialization vector requirements depend on the algorithm. For some
algorithms, an IV must be unpredictable and unique; for others, uniqueness
alone is sufficient, a fixed value is required, or no IV is used. Follow the
requirements for the selected algorithm. IVs typically do not have to be secret
and can be transmitted with the ciphertext.

### `crypto.createDiffieHellman(prime[, primeEncoding][, generator][, generatorEncoding])`

Added in: v0.11.12

**History**

| Version | Changes |
| --- | --- |
| v8.0.0 | The `prime` argument can be any `TypedArray` or `DataView` now. |
| v8.0.0 | The `prime` argument can be a `Uint8Array` now. |
| v6.0.0 | The default for the encoding parameters changed from `binary` to `utf8`. |

- `prime` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) | [`<ArrayBuffer>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/ArrayBuffer) | [`<Buffer>`](07-buffer.md#class-buffer) | [`<TypedArray>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/TypedArray) | [`<DataView>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/DataView)
- `primeEncoding` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) The [encoding](07-buffer.md#buffers-and-character-encodings) of the `prime` string.
- `generator` [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) | [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) | [`<ArrayBuffer>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/ArrayBuffer) | [`<Buffer>`](07-buffer.md#class-buffer) | [`<TypedArray>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/TypedArray) | [`<DataView>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/DataView) **Default:** `2`
- `generatorEncoding` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) The [encoding](07-buffer.md#buffers-and-character-encodings) of the `generator` string.
- Returns: [`<DiffieHellman>`](15-crypto.md#class-diffiehellman)

Creates a `DiffieHellman` key exchange object using the supplied `prime` and an
optional specific `generator`.

The `generator` argument can be a number, string, or [`Buffer`](07-buffer.md). If
`generator` is not specified, the value `2` is used.

If `primeEncoding` is specified, `prime` is expected to be a string; otherwise
a [`Buffer`](07-buffer.md), `TypedArray`, or `DataView` is expected.

If `generatorEncoding` is specified, `generator` is expected to be a string;
otherwise a number, [`Buffer`](07-buffer.md), `TypedArray`, or `DataView` is expected.

### `crypto.createDiffieHellman(primeLength[, generator])`

Added in: v0.5.0

- `primeLength` [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type)
- `generator` [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) **Default:** `2`
- Returns: [`<DiffieHellman>`](15-crypto.md#class-diffiehellman)

Creates a `DiffieHellman` key exchange object and generates a prime of
`primeLength` bits using an optional specific numeric `generator`.
If `generator` is not specified, the value `2` is used.

### `crypto.createDiffieHellmanGroup(name)`

Added in: v0.9.3

- `name` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type)
- Returns: [`<DiffieHellmanGroup>`](15-crypto.md#class-diffiehellmangroup)

An alias for [`crypto.getDiffieHellman()`](#cryptogetdiffiehellmangroupname)

### `crypto.createECDH(curveName)`

Added in: v0.11.14

- `curveName` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type)
- Returns: [`<ECDH>`](15-crypto.md#class-ecdh)

Creates an Elliptic Curve Diffie-Hellman (`ECDH`) key exchange object using a
predefined curve specified by the `curveName` string. Use
[`crypto.getCurves()`](#cryptogetcurves) to obtain a list of available curve names. On recent
OpenSSL releases, `openssl ecparam -list_curves` will also display the name
and description of each available elliptic curve.

### `crypto.createHash(algorithm[, options])`

Added in: v0.1.92

**History**

| Version | Changes |
| --- | --- |
| v26.9.0 | Hash algorithms exposed by OpenSSL providers are now supported. The `functionName` and `customization` options were added for cSHAKE hash functions. |
| v12.8.0 | The `outputLength` option was added for XOF hash functions. |

- `algorithm` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type)
- `options` [`<Object>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Object) [`stream.transform` options](51-stream.md#new-streamtransformoptions)
  - `customization` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) | [`<ArrayBuffer>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/ArrayBuffer) | [`<Buffer>`](07-buffer.md#class-buffer) | [`<TypedArray>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/TypedArray) | [`<DataView>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/DataView) For cSHAKE
    hash functions, specifies the customization byte string. **Default:** an
    empty byte string.
  - `functionName` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) | [`<ArrayBuffer>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/ArrayBuffer) | [`<Buffer>`](07-buffer.md#class-buffer) | [`<TypedArray>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/TypedArray) | [`<DataView>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/DataView) For cSHAKE
    hash functions, specifies the NIST function-name byte string. **Default:**
    an empty byte string.
  - `outputLength` [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) For XOF hash functions, specifies the desired
    output length in bytes.
- Returns: [`<Hash>`](15-crypto.md#class-hash)

Creates and returns a `Hash` object that can be used to generate hash digests
using the given `algorithm`. Optional `options` argument controls stream
behavior. For XOF hash functions such as `'shake256'`, the `outputLength` option
can be used to specify the desired output length in bytes.

The `functionName` and `customization` options apply only to cSHAKE-128 and
cSHAKE-256. They are supported only when Node.js is built with OpenSSL 4.0 or
later and the selected provider supports the corresponding digest parameters.
Strings are encoded as UTF-8, and neither strings nor byte values may contain
NUL bytes. Both options default to an empty byte string. For OpenSSL's built-in
providers, `functionName` is case-sensitive and must be `''`, `'TupleHash'`,
`'ParallelHash'`, or `'KMAC'`. Other providers can impose different
restrictions. With both options empty, cSHAKE produces the same output as the
corresponding SHAKE function for the same output length. `cshake-128` and
`cshake-256` default to output lengths of 32 and 64 bytes, respectively.

When the data is small (\< 5MB) and readily available, [`crypto.hash()`](#cryptohashalgorithm-data-options) is usually faster.

The available algorithms depend on the version and configuration of OpenSSL on
the platform. Examples are `'sha256'` and `'sha512'`. Use
[`crypto.getHashes()`](#cryptogethashes) to obtain the list of hash algorithms available to the
Node.js process.

Example: generating the sha256 sum of a file

*ES module version:*

```js
import {
  createReadStream,
} from 'node:fs';
import { argv } from 'node:process';
const {
  createHash,
} = await import('node:crypto');

const filename = argv[2];

const hash = createHash('sha256');

const input = createReadStream(filename);
input.on('readable', () => {
  // Only one element is going to be produced by the
  // hash stream.
  const data = input.read();
  if (data)
    hash.update(data);
  else {
    console.log(`${hash.digest('hex')} ${filename}`);
  }
});
```

*CommonJS version:*

```js
const {
  createReadStream,
} = require('node:fs');
const {
  createHash,
} = require('node:crypto');
const { argv } = require('node:process');

const filename = argv[2];

const hash = createHash('sha256');

const input = createReadStream(filename);
input.on('readable', () => {
  // Only one element is going to be produced by the
  // hash stream.
  const data = input.read();
  if (data)
    hash.update(data);
  else {
    console.log(`${hash.digest('hex')} ${filename}`);
  }
});
```

### `crypto.createHmac(algorithm, key[, options])`

Added in: v0.1.94

**History**

| Version | Changes |
| --- | --- |
| v26.0.0 | Passing a CryptoKey as `key` is deprecated. |
| v15.0.0 | The key can also be an ArrayBuffer or CryptoKey. The encoding option was added. The key cannot contain more than 2 \*\* 32 - 1 bytes. |
| v11.6.0 | The `key` argument can now be a `KeyObject`. |

- `algorithm` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type)
- `key` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) | [`<ArrayBuffer>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/ArrayBuffer) | [`<Buffer>`](07-buffer.md#class-buffer) | [`<TypedArray>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/TypedArray) | [`<DataView>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/DataView) | [`<KeyObject>`](15-crypto.md#class-keyobject) | [`<CryptoKey>`](65-webcrypto.md#class-cryptokey)
- `options` [`<Object>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Object) [`stream.transform` options](51-stream.md#new-streamtransformoptions)
  - `encoding` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) The string encoding to use when `key` is a string.
- Returns: [`<Hmac>`](15-crypto.md#class-hmac)

Creates and returns an `Hmac` object that uses the given `algorithm` and `key`.
Optional `options` argument controls stream behavior.

The available algorithms depend on the version and configuration of OpenSSL on
the platform. Examples are `'sha256'` and `'sha512'`.
[`crypto.getHashes()`](#cryptogethashes) lists algorithms available to the hashing APIs, but
HMAC imposes additional restrictions, so not every listed algorithm is
suitable.

The `key` is the HMAC key used to generate the cryptographic HMAC hash. If it is
a [`KeyObject`](#class-keyobject), its type must be `secret`. If it is a string, please consider
[caveats when using strings as inputs to cryptographic APIs](#using-strings-as-inputs-to-cryptographic-apis). If it was
obtained from a cryptographically secure source of entropy, such as
[`crypto.randomBytes()`](#cryptorandombytessize-callback) or [`crypto.generateKey()`](#cryptogeneratekeytype-options-callback), its length should not
exceed the block size of `algorithm` (e.g., 512 bits for SHA-256).

Example: generating the sha256 HMAC of a file

*ES module version:*

```js
import {
  createReadStream,
} from 'node:fs';
import { argv } from 'node:process';
const {
  createHmac,
} = await import('node:crypto');

const filename = argv[2];

const hmac = createHmac('sha256', 'a secret');

const input = createReadStream(filename);
input.on('readable', () => {
  // Only one element is going to be produced by the
  // hash stream.
  const data = input.read();
  if (data)
    hmac.update(data);
  else {
    console.log(`${hmac.digest('hex')} ${filename}`);
  }
});
```

*CommonJS version:*

```js
const {
  createReadStream,
} = require('node:fs');
const {
  createHmac,
} = require('node:crypto');
const { argv } = require('node:process');

const filename = argv[2];

const hmac = createHmac('sha256', 'a secret');

const input = createReadStream(filename);
input.on('readable', () => {
  // Only one element is going to be produced by the
  // hash stream.
  const data = input.read();
  if (data)
    hmac.update(data);
  else {
    console.log(`${hmac.digest('hex')} ${filename}`);
  }
});
```

### `crypto.createMac(algorithm, key[, options])`

Added in: v26.9.0

Stability: 1.2 - Release candidate

- `algorithm` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) The name of the MAC algorithm.
- `key` [`<ArrayBuffer>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/ArrayBuffer) | [`<Buffer>`](07-buffer.md#class-buffer) | [`<TypedArray>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/TypedArray) | [`<DataView>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/DataView) | [`<KeyObject>`](15-crypto.md#class-keyobject)
- `options` [`<Object>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Object) [`stream.transform` options](51-stream.md#new-streamtransformoptions)
  - `digest` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) The digest used by a MAC such as HMAC.
  - `cipher` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) The cipher used by a MAC such as CMAC or GMAC.
  - `iv` [`<ArrayBuffer>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/ArrayBuffer) | [`<Buffer>`](07-buffer.md#class-buffer) | [`<TypedArray>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/TypedArray) | [`<DataView>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/DataView) The initialization vector for
    a MAC such as GMAC.
  - `customization` [`<ArrayBuffer>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/ArrayBuffer) | [`<Buffer>`](07-buffer.md#class-buffer) | [`<TypedArray>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/TypedArray) | [`<DataView>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/DataView) A customization
    byte string for MACs that support it, such as KMAC.
  - `salt` [`<ArrayBuffer>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/ArrayBuffer) | [`<Buffer>`](07-buffer.md#class-buffer) | [`<TypedArray>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/TypedArray) | [`<DataView>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/DataView) A salt byte string for MACs
    that support it, such as BLAKE2 MACs.
  - `outputLength` [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) The requested provider output size in bytes. Must be
    an unsigned 32-bit integer. Provider-specific restrictions also apply.
- Returns: {Mac}

`algorithm` must be a non-empty provider MAC name. The MAC-specific properties
listed above are extensions to the standard [`stream.transform` options](51-stream.md#new-streamtransformoptions) and
are passed only when the selected provider implementation advertises the
corresponding parameter with the expected type. A supplied MAC-specific option
that the selected implementation does not support causes an error.

The following table summarizes the MAC-specific options accepted by MAC
implementations in OpenSSL's built-in providers. The `key` argument is required
for every MAC. The table lists only MAC-specific options; standard
[`stream.transform` options](51-stream.md#new-streamtransformoptions) remain available for every family.

| MAC family | Required options | Optional options | Notes |
| --- | --- | --- | --- |
| HMAC | `digest` | None |  |
| CMAC | `cipher` using CBC mode | None |  |
| GMAC | `cipher` using GCM mode, non-empty `iv` | None | Requires a unique IV for every message authenticated with a given key. |
| KMAC | None | `customization`, `outputLength` |  |
| BLAKE2 MAC | None | `customization`, `salt`, `outputLength` |  |
| Poly1305 | None | None | Each key must be used for only one message. |
| SipHash | None | `outputLength` |  |

`outputLength` configures the output size of the provider MAC. It is never
implemented by computing a longer tag and truncating it. A value of `0` is
passed to the provider and is accepted only when that provider can initialize
and finalize the MAC with a zero-byte output. When `outputLength` is omitted,
the provider's default output size is used and must be nonzero.

The `key` must contain bytes or be a [`KeyObject`](#class-keyobject) of type `secret`. Key
length and other key requirements are determined by the selected provider
implementation.

Available algorithms and their accepted parameters depend on the OpenSSL
version, loaded providers, and active default property query. Use
[`crypto.getMacs()`](#cryptogetmacs) to list fetchable MAC names. A listed name can still
require options or a key with provider-specific properties.

### `crypto.createPrivateKey(key)`

Added in: v11.6.0

**History**

| Version | Changes |
| --- | --- |
| v26.7.0 | The key can also be a URL referencing an object for an OpenSSL STORE loader. The `properties` option was added. |
| v26.7.0 | Passing a CryptoKey as `key` is no longer supported. |
| v26.1.0 | Added JWK format support for ML-KEM and SLH-DSA key types. |
| v26.0.0 | Passing a CryptoKey as `key` is deprecated. |
| v26.0.0 | Added support for `'raw-private'` and `'raw-seed'` formats. |
| v24.6.0 | Add support for ML-DSA keys. |
| v15.12.0 | The key can also be a JWK object. |
| v15.0.0 | The key can also be an ArrayBuffer. The encoding option was added. The key cannot contain more than 2 \*\* 32 - 1 bytes. |

- `key` [`<Object>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Object) | [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) | [`<ArrayBuffer>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/ArrayBuffer) | [`<Buffer>`](07-buffer.md#class-buffer) | [`<TypedArray>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/TypedArray) | [`<DataView>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/DataView) | [`<URL>`](59-url.md#the-whatwg-url-api)
  - `key` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) | [`<ArrayBuffer>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/ArrayBuffer) | [`<Buffer>`](07-buffer.md#class-buffer) | [`<TypedArray>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/TypedArray) | [`<DataView>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/DataView) | [`<Object>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Object) | [`<URL>`](59-url.md#the-whatwg-url-api) The key
    material, either in PEM, DER, JWK, or raw format, or a [`<URL>`](59-url.md#the-whatwg-url-api) referencing an
    object for an OpenSSL STORE loader.
  - `format` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) Must be `'pem'`, `'der'`, `'jwk'`, `'raw-private'`,
    or `'raw-seed'`. **Default:** `'pem'`.
  - `type` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) Must be `'pkcs1'`, `'pkcs8'` or `'sec1'`. This option is
    required only if the `format` is `'der'` and ignored otherwise.
  - `passphrase` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) | [`<Buffer>`](07-buffer.md#class-buffer) The passphrase to use for decryption. When `key` is a [`<URL>`](59-url.md#the-whatwg-url-api), this is the optional PIN/passphrase forwarded to the
    STORE loader.
  - `properties` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) The optional OpenSSL property query used when
    fetching the STORE loader for a [`<URL>`](59-url.md#the-whatwg-url-api) key.
  - `encoding` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) The string encoding to use when `key` is a string.
  - `asymmetricKeyType` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) Required when `format` is `'raw-private'`
    or `'raw-seed'` and ignored otherwise.
    Must be a [supported key type](#asymmetric-key-types).
  - `namedCurve` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) Name of the curve to use. Required when `asymmetricKeyType` is `'ec'` and ignored otherwise.
- Returns: [`<KeyObject>`](15-crypto.md#class-keyobject)

Creates and returns a new key object containing a private key. If `key` is a
string or `Buffer`, `format` is assumed to be `'pem'`; otherwise, `key`
must be an object with the properties described above.

If the private key is encrypted, a `passphrase` must be specified. The length
of the passphrase is limited to 1024 bytes.

#### Private keys from OpenSSL STORE loaders

Stability: 1.1 - Active development

If `key` is a [`<URL>`](59-url.md#the-whatwg-url-api) (or an object whose `key` is a [`<URL>`](59-url.md#the-whatwg-url-api)), the private key is
loaded through an OpenSSL STORE loader. The URL is passed to OpenSSL as a URI,
for example a `file:` URI or a provider-backed scheme such as `pkcs11:`. When
the [Permission Model](42-permissions.md#permission-model) is enabled, [`--allow-openssl-store`](13-cli.md#--allow-openssl-store) is required.

> **Warning**: A URI scheme does not pin an OpenSSL STORE loader or prove where
> the returned key came from. Node.js forwards the URI to OpenSSL, which chooses
> loaders according to its version and configuration. For example, OpenSSL may
> offer an opaque URI such as `pkcs11:object=...` (one without `//` after the
> scheme) to its `file` loader before trying the `pkcs11` loader. If the complete
> URI is a valid local path and that file exists, it may be loaded instead.
> Node.js does not verify which loader supplied the key. Do not rely on a
> provider-specific URI scheme as proof that a key came from that provider or
> from a hardware device.

Configured OpenSSL STORE loaders have broad authority and may access files,
devices, tokens, or the network. Access performed by a loader is not constrained
by the `fs.read`, `fs.write`, or `net` permission scopes.

When a [`<URL>`](59-url.md#the-whatwg-url-api) is used, `format`, `type`, `asymmetricKeyType`, and `namedCurve`
are ignored even when those options would otherwise depend on each other, such
as `type` with `format: 'der'` or `namedCurve` with
`asymmetricKeyType: 'ec'`. The input is passed to the STORE loader as a URI,
not handled as PEM, DER, JWK, or raw key material. `passphrase` is still used as
the optional PIN/passphrase passed to the loader, and `encoding` applies if that
`passphrase` is a string.

Use `passphrase` instead of embedding credentials in the URI passed to the
STORE loader. Node.js redacts the URI from its own permission-denial resource
and diagnostics. Errors reported by OpenSSL or a provider after loading begins
may include the URI.

When `properties` is specified with a [`<URL>`](59-url.md#the-whatwg-url-api) key, it is passed to OpenSSL as the
property query for selecting the STORE loader. It is not appended to the URL and
is distinct from provider-specific URI parameters.

### `crypto.createPublicKey(key)`

Added in: v11.6.0

**History**

| Version | Changes |
| --- | --- |
| v26.1.0 | Added JWK format support for ML-KEM and SLH-DSA key types. |
| v26.0.0 | Passing a CryptoKey as `key` is deprecated. |
| v26.0.0 | Added support for `'raw-public'` format. |
| v24.6.0 | Add support for ML-DSA keys. |
| v15.12.0 | The key can also be a JWK object. |
| v15.0.0 | The key can also be an ArrayBuffer. The encoding option was added. The key cannot contain more than 2 \*\* 32 - 1 bytes. |
| v11.13.0 | The `key` argument can now be a `KeyObject` with type `private`. |
| v11.7.0 | The `key` argument can now be a private key. |

- `key` [`<Object>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Object) | [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) | [`<ArrayBuffer>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/ArrayBuffer) | [`<Buffer>`](07-buffer.md#class-buffer) | [`<TypedArray>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/TypedArray) | [`<DataView>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/DataView)
  - `key` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) | [`<ArrayBuffer>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/ArrayBuffer) | [`<Buffer>`](07-buffer.md#class-buffer) | [`<TypedArray>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/TypedArray) | [`<DataView>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/DataView) | [`<Object>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Object) The key
    material, either in PEM, DER, JWK, or raw format.
  - `format` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) Must be `'pem'`, `'der'`, `'jwk'`, or `'raw-public'`.
    **Default:** `'pem'`.
  - `type` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) Must be `'pkcs1'` or `'spki'`. This option is
    required only if the `format` is `'der'` and ignored otherwise.
  - `encoding` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) The string encoding to use when `key` is a string.
  - `asymmetricKeyType` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) Required when `format` is `'raw-public'`
    and ignored otherwise.
    Must be a [supported key type](#asymmetric-key-types).
  - `namedCurve` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) Name of the curve to use. Required when `asymmetricKeyType` is `'ec'` and ignored otherwise.
- Returns: [`<KeyObject>`](15-crypto.md#class-keyobject)

Creates and returns a new key object containing a public key. If `key` is a
string or `Buffer`, `format` is assumed to be `'pem'`; if `key` is a `KeyObject`
with type `'private'`, the public key is derived from the given private key;
otherwise, `key` must be an object with the properties described above.

If the format is `'pem'`, the `'key'` may also be an X.509 certificate.

Because public keys can be derived from private keys, a private key may be
passed instead of a public key. In that case, this function behaves as if
[`crypto.createPrivateKey()`](#cryptocreateprivatekeykey) had been called, except that the type of the
returned `KeyObject` will be `'public'` and that the private key cannot be
extracted from the returned `KeyObject`. Similarly, if a `KeyObject` with type
`'private'` is given, a new `KeyObject` with type `'public'` will be returned
and it will be impossible to extract the private key from the returned object.

A store-backed private key can be used as a public key by first loading it with
[`crypto.createPrivateKey()`](#cryptocreateprivatekeykey); a [`<URL>`](59-url.md#the-whatwg-url-api) cannot be passed to `crypto.createPublicKey()` directly.

### `crypto.createSecretKey(key[, encoding])`

Added in: v11.6.0

**History**

| Version | Changes |
| --- | --- |
| v18.8.0, v16.18.0 | The key can now be zero-length. |
| v15.0.0 | The key can also be an ArrayBuffer or string. The encoding argument was added. The key cannot contain more than 2 \*\* 32 - 1 bytes. |

- `key` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) | [`<ArrayBuffer>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/ArrayBuffer) | [`<Buffer>`](07-buffer.md#class-buffer) | [`<TypedArray>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/TypedArray) | [`<DataView>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/DataView)
- `encoding` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) The string encoding when `key` is a string.
- Returns: [`<KeyObject>`](15-crypto.md#class-keyobject)

Creates and returns a new key object containing a secret key for symmetric
encryption or `Hmac`.

### `crypto.createSign(algorithm[, options])`

Added in: v0.1.92

- `algorithm` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type)
- `options` [`<Object>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Object) [`stream.Writable` options](51-stream.md#new-streamwritableoptions)
- Returns: [`<Sign>`](15-crypto.md#class-sign)

Creates and returns a `Sign` object that uses the given `algorithm`. Use
[`crypto.getHashes()`](#cryptogethashes) to obtain the names available to the hashing APIs. The
key type and signature scheme can impose additional restrictions on which
digests can be used. Optional `options` argument controls the
`stream.Writable` behavior.

In some cases, a `Sign` instance can be created using the name of a signature
algorithm, such as `'RSA-SHA256'`, instead of a digest algorithm. This will use
the corresponding digest algorithm. This does not work for all signature
algorithms, such as `'ecdsa-with-SHA256'`, so it is best to always use digest
algorithm names.

### `crypto.createVerify(algorithm[, options])`

Added in: v0.1.92

- `algorithm` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type)
- `options` [`<Object>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Object) [`stream.Writable` options](51-stream.md#new-streamwritableoptions)
- Returns: [`<Verify>`](15-crypto.md#class-verify)

Creates and returns a `Verify` object that uses the given algorithm.
Use [`crypto.getHashes()`](#cryptogethashes) to obtain the names available to the hashing APIs.
The key type and signature scheme can impose additional restrictions on which
digests can be used. Optional `options` argument controls the
`stream.Writable` behavior.

In some cases, a `Verify` instance can be created using the name of a signature
algorithm, such as `'RSA-SHA256'`, instead of a digest algorithm. This will use
the corresponding digest algorithm. This does not work for all signature
algorithms, such as `'ecdsa-with-SHA256'`, so it is best to always use digest
algorithm names.

### `crypto.decapsulate(key, ciphertext[, callback])`

Added in: v24.7.0

- `key` [`<Object>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Object) | [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) | [`<ArrayBuffer>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/ArrayBuffer) | [`<Buffer>`](07-buffer.md#class-buffer) | [`<TypedArray>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/TypedArray) | [`<DataView>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/DataView) | [`<KeyObject>`](15-crypto.md#class-keyobject) | [`<URL>`](59-url.md#the-whatwg-url-api) Private Key
- `ciphertext` [`<ArrayBuffer>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/ArrayBuffer) | [`<Buffer>`](07-buffer.md#class-buffer) | [`<TypedArray>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/TypedArray) | [`<DataView>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/DataView)
- `callback` [`<Function>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Function)
  - `err` [`<Error>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Error)
  - `sharedKey` [`<Buffer>`](07-buffer.md#class-buffer)
- Returns: [`<Buffer>`](07-buffer.md#class-buffer) if the `callback` function is not provided.

Key decapsulation using a KEM algorithm with a private key.

Supported key types and their KEM algorithms are:

- `'rsa'`[2](#user-content-fn-openssl30) RSA Secret Value Encapsulation
- `'ec'`[3](#user-content-fn-openssl32) DHKEM(P-256, HKDF-SHA256), DHKEM(P-384, HKDF-SHA256), DHKEM(P-521, HKDF-SHA256)
- `'x25519'`[3](#user-content-fn-openssl32) DHKEM(X25519, HKDF-SHA256)
- `'x448'`[3](#user-content-fn-openssl32) DHKEM(X448, HKDF-SHA512)
- `'ml-kem-512'`[1](#user-content-fn-openssl35) ML-KEM
- `'ml-kem-768'`[1](#user-content-fn-openssl35) ML-KEM
- `'ml-kem-1024'`[1](#user-content-fn-openssl35) ML-KEM

If `key` is not a [`KeyObject`](#class-keyobject), this function behaves as if `key` had been
passed to [`crypto.createPrivateKey()`](#cryptocreateprivatekeykey).

If the `callback` function is provided this function uses libuv's threadpool.

### `crypto.diffieHellman(options[, callback])`

Added in: v13.9.0, v12.17.0

**History**

| Version | Changes |
| --- | --- |
| v26.1.0 | Accept key data in addition to KeyObject instances. |
| v23.11.0 | Optional callback argument added. |

- `options` [`<Object>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Object)
  - `privateKey` [`<Object>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Object) | [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) | [`<ArrayBuffer>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/ArrayBuffer) | [`<Buffer>`](07-buffer.md#class-buffer) | [`<TypedArray>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/TypedArray) | [`<DataView>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/DataView) | [`<KeyObject>`](15-crypto.md#class-keyobject) | [`<URL>`](59-url.md#the-whatwg-url-api)
  - `publicKey` [`<Object>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Object) | [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) | [`<ArrayBuffer>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/ArrayBuffer) | [`<Buffer>`](07-buffer.md#class-buffer) | [`<TypedArray>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/TypedArray) | [`<DataView>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/DataView) | [`<KeyObject>`](15-crypto.md#class-keyobject)
- `callback` [`<Function>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Function)
  - `err` [`<Error>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Error)
  - `secret` [`<Buffer>`](07-buffer.md#class-buffer)
- Returns: [`<Buffer>`](07-buffer.md#class-buffer) if the `callback` function is not provided.

Computes the Diffie-Hellman shared secret based on a `privateKey` and a `publicKey`.
Both keys must represent the same asymmetric key type and must support either the DH or
ECDH operation.

If `options.privateKey` is not a [`KeyObject`](#class-keyobject), this function behaves as if
`options.privateKey` had been passed to [`crypto.createPrivateKey()`](#cryptocreateprivatekeykey).

If `options.publicKey` is not a [`KeyObject`](#class-keyobject), this function behaves as if
`options.publicKey` had been passed to [`crypto.createPublicKey()`](#cryptocreatepublickeykey).

If the `callback` function is provided this function uses libuv's threadpool.

### `crypto.encapsulate(key[, callback])`

Added in: v24.7.0

- `key` [`<Object>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Object) | [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) | [`<ArrayBuffer>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/ArrayBuffer) | [`<Buffer>`](07-buffer.md#class-buffer) | [`<TypedArray>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/TypedArray) | [`<DataView>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/DataView) | [`<KeyObject>`](15-crypto.md#class-keyobject) Public Key
- `callback` [`<Function>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Function)
  - `err` [`<Error>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Error)
  - `result` [`<Object>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Object)
    - `sharedKey` [`<Buffer>`](07-buffer.md#class-buffer)
    - `ciphertext` [`<Buffer>`](07-buffer.md#class-buffer)
- Returns: [`<Object>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Object) if the `callback` function is not provided.
  - `sharedKey` [`<Buffer>`](07-buffer.md#class-buffer)
  - `ciphertext` [`<Buffer>`](07-buffer.md#class-buffer)

Key encapsulation using a KEM algorithm with a public key.

Supported key types and their KEM algorithms are:

- `'rsa'`[2](#user-content-fn-openssl30) RSA Secret Value Encapsulation
- `'ec'`[3](#user-content-fn-openssl32) DHKEM(P-256, HKDF-SHA256), DHKEM(P-384, HKDF-SHA256), DHKEM(P-521, HKDF-SHA256)
- `'x25519'`[3](#user-content-fn-openssl32) DHKEM(X25519, HKDF-SHA256)
- `'x448'`[3](#user-content-fn-openssl32) DHKEM(X448, HKDF-SHA512)
- `'ml-kem-512'`[1](#user-content-fn-openssl35) ML-KEM
- `'ml-kem-768'`[1](#user-content-fn-openssl35) ML-KEM
- `'ml-kem-1024'`[1](#user-content-fn-openssl35) ML-KEM

If `key` is not a [`KeyObject`](#class-keyobject), this function behaves as if `key` had been
passed to [`crypto.createPublicKey()`](#cryptocreatepublickeykey).

If the `callback` function is provided this function uses libuv's threadpool.

### `crypto.fips`

Added in: v6.0.0Deprecated in: v10.0.0

Stability: 0 - Deprecated

Deprecated property for checking and controlling [FIPS mode](#fips-mode). Use
[`crypto.getFips()`](#cryptogetfips) and [`crypto.setFips()`](#cryptosetfipsbool) instead.

### `crypto.generateKey(type, options, callback)`

Added in: v15.0.0

**History**

| Version | Changes |
| --- | --- |
| v18.0.0 | Passing an invalid callback to the `callback` argument now throws `ERR_INVALID_ARG_TYPE` instead of `ERR_INVALID_CALLBACK`. |

- `type` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) The intended use of the generated secret key. Currently
  accepted values are `'hmac'` and `'aes'`.
- `options` [`<Object>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Object)
  - `length` [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) The bit length of the key to generate. This must be a
    value greater than 0.
    - If `type` is `'hmac'`, the minimum is 8, and the maximum length is
      231-1. If the value is not a multiple of 8, the generated
      key will be truncated to `Math.floor(length / 8)`.
    - If `type` is `'aes'`, the length must be one of `128`, `192`, or `256`.
- `callback` [`<Function>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Function)
  - `err` [`<Error>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Error)
  - `key` [`<KeyObject>`](15-crypto.md#class-keyobject)

Asynchronously generates a new random secret key of the given `length`. The
`type` will determine which validations will be performed on the `length`.

*ES module version:*

```js
const {
  generateKey,
} = await import('node:crypto');

generateKey('hmac', { length: 512 }, (err, key) => {
  if (err) throw err;
  console.log(key.export().toString('hex'));  // 46e..........620
});
```

*CommonJS version:*

```js
const {
  generateKey,
} = require('node:crypto');

generateKey('hmac', { length: 512 }, (err, key) => {
  if (err) throw err;
  console.log(key.export().toString('hex'));  // 46e..........620
});
```

The size of a generated HMAC key should not exceed the block size of the
underlying hash function. See [`crypto.createHmac()`](#cryptocreatehmacalgorithm-key-options) for more information.

### `crypto.generateKeyPair(type, options, callback)`

Added in: v10.12.0

**History**

| Version | Changes |
| --- | --- |
| v24.8.0 | Add support for SLH-DSA key pairs. |
| v24.7.0 | Add support for ML-KEM key pairs. |
| v24.6.0 | Add support for ML-DSA key pairs. |
| v18.0.0 | Passing an invalid callback to the `callback` argument now throws `ERR_INVALID_ARG_TYPE` instead of `ERR_INVALID_CALLBACK`. |
| v16.10.0 | Add ability to define `RSASSA-PSS-params` sequence parameters for RSA-PSS keys pairs. |
| v13.9.0, v12.17.0 | Add support for Diffie-Hellman. |
| v12.0.0 | Add support for RSA-PSS key pairs. |
| v12.0.0 | Add ability to generate X25519 and X448 key pairs. |
| v12.0.0 | Add ability to generate Ed25519 and Ed448 key pairs. |
| v11.6.0 | The `generateKeyPair` and `generateKeyPairSync` functions now produce key objects if no encoding was specified. |

- `type` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) The asymmetric key type to generate. See the
  supported [asymmetric key types](#asymmetric-key-types).
- `options` [`<Object>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Object)
  - `modulusLength` [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) Key size in bits (RSA, DSA).
  - `publicExponent` [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) Public exponent (RSA). **Default:** `0x10001`.
  - `hashAlgorithm` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) Name of the message digest (RSA-PSS).
  - `mgf1HashAlgorithm` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) Name of the message digest used by
    MGF1 (RSA-PSS).
  - `saltLength` [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) Minimal salt length in bytes (RSA-PSS).
  - `divisorLength` [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) Size of `q` in bits (DSA).
  - `namedCurve` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) Name of the curve to use (EC).
  - `prime` [`<Buffer>`](07-buffer.md#class-buffer) The prime parameter (DH).
  - `primeLength` [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) Prime length in bits (DH).
  - `generator` [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) Custom generator (DH). **Default:** `2`.
  - `groupName` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) Diffie-Hellman group name (DH). See [`crypto.getDiffieHellman()`](#cryptogetdiffiehellmangroupname).
  - `paramEncoding` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) Must be `'named'` or `'explicit'` (EC).
    **Default:** `'named'`.
  - `publicKeyEncoding` [`<Object>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Object) See [`keyObject.export()`](#keyobjectexportoptions).
  - `privateKeyEncoding` [`<Object>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Object) See [`keyObject.export()`](#keyobjectexportoptions).
- `callback` [`<Function>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Function)
  - `err` [`<Error>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Error)
  - `publicKey` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) | [`<Buffer>`](07-buffer.md#class-buffer) | [`<KeyObject>`](15-crypto.md#class-keyobject)
  - `privateKey` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) | [`<Buffer>`](07-buffer.md#class-buffer) | [`<KeyObject>`](15-crypto.md#class-keyobject)

Generates a new asymmetric key pair of the given `type`. See the
supported [asymmetric key types](#asymmetric-key-types).

For RSA-PSS keys, [`crypto.getHashes()`](#cryptogethashes) lists algorithms available to the
hashing APIs, but not every listed digest can be encoded in RSA-PSS parameters
or is supported by the active RSA implementation.

If a `publicKeyEncoding` or `privateKeyEncoding` was specified, this function
behaves as if [`keyObject.export()`](#keyobjectexportoptions) had been called on its result. Otherwise,
the respective part of the key is returned as a [`KeyObject`](#class-keyobject).

It is recommended to encode public keys as `'spki'` and private keys as
`'pkcs8'` with encryption for long-term storage:

*ES module version:*

```js
const {
  generateKeyPair,
} = await import('node:crypto');

generateKeyPair('rsa', {
  modulusLength: 4096,
  publicKeyEncoding: {
    type: 'spki',
    format: 'pem',
  },
  privateKeyEncoding: {
    type: 'pkcs8',
    format: 'pem',
    cipher: 'aes-256-cbc',
    passphrase: 'top secret',
  },
}, (err, publicKey, privateKey) => {
  // Handle errors and use the generated key pair.
});
```

*CommonJS version:*

```js
const {
  generateKeyPair,
} = require('node:crypto');

generateKeyPair('rsa', {
  modulusLength: 4096,
  publicKeyEncoding: {
    type: 'spki',
    format: 'pem',
  },
  privateKeyEncoding: {
    type: 'pkcs8',
    format: 'pem',
    cipher: 'aes-256-cbc',
    passphrase: 'top secret',
  },
}, (err, publicKey, privateKey) => {
  // Handle errors and use the generated key pair.
});
```

On completion, `callback` will be called with `err` set to `undefined` and
`publicKey` / `privateKey` representing the generated key pair.

If this method is invoked as its [`util.promisify()`](60-util.md#utilpromisifyoriginal)ed version, it returns
a `Promise` for an `Object` with `publicKey` and `privateKey` properties.

### `crypto.generateKeyPairSync(type, options)`

Added in: v10.12.0

**History**

| Version | Changes |
| --- | --- |
| v24.8.0 | Add support for SLH-DSA key pairs. |
| v24.7.0 | Add support for ML-KEM key pairs. |
| v24.6.0 | Add support for ML-DSA key pairs. |
| v16.10.0 | Add ability to define `RSASSA-PSS-params` sequence parameters for RSA-PSS keys pairs. |
| v13.9.0, v12.17.0 | Add support for Diffie-Hellman. |
| v12.0.0 | Add support for RSA-PSS key pairs. |
| v12.0.0 | Add ability to generate X25519 and X448 key pairs. |
| v12.0.0 | Add ability to generate Ed25519 and Ed448 key pairs. |
| v11.6.0 | The `generateKeyPair` and `generateKeyPairSync` functions now produce key objects if no encoding was specified. |

- `type` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) The asymmetric key type to generate. See the
  supported [asymmetric key types](#asymmetric-key-types).
- `options` [`<Object>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Object)
  - `modulusLength` [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) Key size in bits (RSA, DSA).
  - `publicExponent` [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) Public exponent (RSA). **Default:** `0x10001`.
  - `hashAlgorithm` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) Name of the message digest (RSA-PSS).
  - `mgf1HashAlgorithm` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) Name of the message digest used by
    MGF1 (RSA-PSS).
  - `saltLength` [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) Minimal salt length in bytes (RSA-PSS).
  - `divisorLength` [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) Size of `q` in bits (DSA).
  - `namedCurve` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) Name of the curve to use (EC).
  - `prime` [`<Buffer>`](07-buffer.md#class-buffer) The prime parameter (DH).
  - `primeLength` [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) Prime length in bits (DH).
  - `generator` [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) Custom generator (DH). **Default:** `2`.
  - `groupName` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) Diffie-Hellman group name (DH). See [`crypto.getDiffieHellman()`](#cryptogetdiffiehellmangroupname).
  - `paramEncoding` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) Must be `'named'` or `'explicit'` (EC).
    **Default:** `'named'`.
  - `publicKeyEncoding` [`<Object>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Object) See [`keyObject.export()`](#keyobjectexportoptions).
  - `privateKeyEncoding` [`<Object>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Object) See [`keyObject.export()`](#keyobjectexportoptions).
- Returns: [`<Object>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Object)
  - `publicKey` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) | [`<Buffer>`](07-buffer.md#class-buffer) | [`<KeyObject>`](15-crypto.md#class-keyobject)
  - `privateKey` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) | [`<Buffer>`](07-buffer.md#class-buffer) | [`<KeyObject>`](15-crypto.md#class-keyobject)

Generates a new asymmetric key pair of the given `type`. See the
supported [asymmetric key types](#asymmetric-key-types).

For RSA-PSS keys, [`crypto.getHashes()`](#cryptogethashes) lists algorithms available to the
hashing APIs, but not every listed digest can be encoded in RSA-PSS parameters
or is supported by the active RSA implementation.

If a `publicKeyEncoding` or `privateKeyEncoding` was specified, this function
behaves as if [`keyObject.export()`](#keyobjectexportoptions) had been called on its result. Otherwise,
the respective part of the key is returned as a [`KeyObject`](#class-keyobject).

When encoding public keys, it is recommended to use `'spki'`. When encoding
private keys, it is recommended to use `'pkcs8'` with a strong passphrase,
and to keep the passphrase confidential.

*ES module version:*

```js
const {
  generateKeyPairSync,
} = await import('node:crypto');

const {
  publicKey,
  privateKey,
} = generateKeyPairSync('rsa', {
  modulusLength: 4096,
  publicKeyEncoding: {
    type: 'spki',
    format: 'pem',
  },
  privateKeyEncoding: {
    type: 'pkcs8',
    format: 'pem',
    cipher: 'aes-256-cbc',
    passphrase: 'top secret',
  },
});
```

*CommonJS version:*

```js
const {
  generateKeyPairSync,
} = require('node:crypto');

const {
  publicKey,
  privateKey,
} = generateKeyPairSync('rsa', {
  modulusLength: 4096,
  publicKeyEncoding: {
    type: 'spki',
    format: 'pem',
  },
  privateKeyEncoding: {
    type: 'pkcs8',
    format: 'pem',
    cipher: 'aes-256-cbc',
    passphrase: 'top secret',
  },
});
```

The return value `{ publicKey, privateKey }` represents the generated key pair.
When PEM encoding was selected, the respective key will be a string, otherwise
it will be a buffer containing the data encoded as DER.

### `crypto.generateKeySync(type, options)`

Added in: v15.0.0

- `type` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) The intended use of the generated secret key. Currently
  accepted values are `'hmac'` and `'aes'`.
- `options` [`<Object>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Object)
  - `length` [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) The bit length of the key to generate.
    - If `type` is `'hmac'`, the minimum is 8, and the maximum length is
      231-1. If the value is not a multiple of 8, the generated
      key will be truncated to `Math.floor(length / 8)`.
    - If `type` is `'aes'`, the length must be one of `128`, `192`, or `256`.
- Returns: [`<KeyObject>`](15-crypto.md#class-keyobject)

Synchronously generates a new random secret key of the given `length`. The
`type` will determine which validations will be performed on the `length`.

*ES module version:*

```js
const {
  generateKeySync,
} = await import('node:crypto');

const key = generateKeySync('hmac', { length: 512 });
console.log(key.export().toString('hex'));  // e89..........41e
```

*CommonJS version:*

```js
const {
  generateKeySync,
} = require('node:crypto');

const key = generateKeySync('hmac', { length: 512 });
console.log(key.export().toString('hex'));  // e89..........41e
```

The size of a generated HMAC key should not exceed the block size of the
underlying hash function. See [`crypto.createHmac()`](#cryptocreatehmacalgorithm-key-options) for more information.

### `crypto.generatePrime(size[, options], callback)`

Added in: v15.8.0

**History**

| Version | Changes |
| --- | --- |
| v18.0.0 | Passing an invalid callback to the `callback` argument now throws `ERR_INVALID_ARG_TYPE` instead of `ERR_INVALID_CALLBACK`. |

- `size` [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) The size (in bits) of the prime to generate.
- `options` [`<Object>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Object)
  - `add` [`<ArrayBuffer>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/ArrayBuffer) | [`<SharedArrayBuffer>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/SharedArrayBuffer) | [`<TypedArray>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/TypedArray) | [`<Buffer>`](07-buffer.md#class-buffer) | [`<DataView>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/DataView) | [`<bigint>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#bigint_type)
  - `rem` [`<ArrayBuffer>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/ArrayBuffer) | [`<SharedArrayBuffer>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/SharedArrayBuffer) | [`<TypedArray>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/TypedArray) | [`<Buffer>`](07-buffer.md#class-buffer) | [`<DataView>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/DataView) | [`<bigint>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#bigint_type)
  - `safe` [`<boolean>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#boolean_type) **Default:** `false`.
  - `bigint` [`<boolean>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#boolean_type) When `true`, the generated prime is returned
    as a `bigint`.
- `callback` [`<Function>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Function)
  - `err` [`<Error>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Error)
  - `prime` [`<ArrayBuffer>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/ArrayBuffer) | [`<bigint>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#bigint_type)

Generates a pseudorandom prime of `size` bits.

If `options.safe` is `true`, the prime will be a safe prime -- that is,
`(prime - 1) / 2` will also be a prime.

The `options.add` and `options.rem` parameters can be used to enforce additional
requirements, e.g., for Diffie-Hellman:

- If `options.add` and `options.rem` are both set, the prime will satisfy the
  condition that `prime % add = rem`.
- If only `options.add` is set and `options.safe` is not `true`, the prime will
  satisfy the condition that `prime % add = 1`.
- If only `options.add` is set and `options.safe` is set to `true`, the prime
  will instead satisfy the condition that `prime % add = 3`. This is necessary
  because `prime % add = 1` for `options.add > 2` would contradict the condition
  enforced by `options.safe`.
- `options.rem` is ignored if `options.add` is not given.

Both `options.add` and `options.rem` must be encoded as big-endian sequences
if given as an `ArrayBuffer`, `SharedArrayBuffer`, `TypedArray`, `Buffer`, or
`DataView`.

By default, the prime is encoded as a big-endian sequence of octets
in an [`<ArrayBuffer>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/ArrayBuffer). If the `bigint` option is `true`, then a [`<bigint>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#bigint_type)
is provided.

The `size` of the prime will have a direct impact on how long it takes to
generate the prime. The larger the size, the longer it will take. Because
we use OpenSSL's `BN_generate_prime_ex` function, which provides only
minimal control over our ability to interrupt the generation process,
it is not recommended to generate overly large primes, as doing so may make
the process unresponsive.

### `crypto.generatePrimeSync(size[, options])`

Added in: v15.8.0

- `size` [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) The size (in bits) of the prime to generate.
- `options` [`<Object>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Object)
  - `add` [`<ArrayBuffer>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/ArrayBuffer) | [`<SharedArrayBuffer>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/SharedArrayBuffer) | [`<TypedArray>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/TypedArray) | [`<Buffer>`](07-buffer.md#class-buffer) | [`<DataView>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/DataView) | [`<bigint>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#bigint_type)
  - `rem` [`<ArrayBuffer>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/ArrayBuffer) | [`<SharedArrayBuffer>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/SharedArrayBuffer) | [`<TypedArray>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/TypedArray) | [`<Buffer>`](07-buffer.md#class-buffer) | [`<DataView>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/DataView) | [`<bigint>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#bigint_type)
  - `safe` [`<boolean>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#boolean_type) **Default:** `false`.
  - `bigint` [`<boolean>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#boolean_type) When `true`, the generated prime is returned
    as a `bigint`.
- Returns: [`<ArrayBuffer>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/ArrayBuffer) | [`<bigint>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#bigint_type)

Generates a pseudorandom prime of `size` bits.

If `options.safe` is `true`, the prime will be a safe prime -- that is,
`(prime - 1) / 2` will also be a prime.

The `options.add` and `options.rem` parameters can be used to enforce additional
requirements, e.g., for Diffie-Hellman:

- If `options.add` and `options.rem` are both set, the prime will satisfy the
  condition that `prime % add = rem`.
- If only `options.add` is set and `options.safe` is not `true`, the prime will
  satisfy the condition that `prime % add = 1`.
- If only `options.add` is set and `options.safe` is set to `true`, the prime
  will instead satisfy the condition that `prime % add = 3`. This is necessary
  because `prime % add = 1` for `options.add > 2` would contradict the condition
  enforced by `options.safe`.
- `options.rem` is ignored if `options.add` is not given.

Both `options.add` and `options.rem` must be encoded as big-endian sequences
if given as an `ArrayBuffer`, `SharedArrayBuffer`, `TypedArray`, `Buffer`, or
`DataView`.

By default, the prime is encoded as a big-endian sequence of octets
in an [`<ArrayBuffer>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/ArrayBuffer). If the `bigint` option is `true`, then a [`<bigint>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#bigint_type)
is provided.

The `size` of the prime will have a direct impact on how long it takes to
generate the prime. The larger the size, the longer it will take. Because
we use OpenSSL's `BN_generate_prime_ex` function, which provides only
minimal control over our ability to interrupt the generation process,
it is not recommended to generate overly large primes, as doing so may make
the process unresponsive.

### `crypto.getCipherInfo(nameOrNid[, options])`

Added in: v15.0.0

- `nameOrNid` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) | [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) The name or nid of the cipher to query.
- `options` [`<Object>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Object)
  - `keyLength` [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) A test key length.
  - `ivLength` [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) A test IV length.
- Returns: [`<Object>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Object)
  - `name` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) The name of the cipher
  - `nid` [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) | [`<undefined>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#undefined_type) The nid of the cipher. This property is `undefined` if the
    cipher has no OpenSSL nid.
  - `blockSize` [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) | [`<undefined>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#undefined_type) The block size of the cipher in bytes. This property
    is `undefined` when `mode` is `'stream'`.
  - `ivLength` [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) | [`<undefined>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#undefined_type) The expected or default initialization vector length in
    bytes. This property is `undefined` if the cipher does not use an initialization
    vector.
  - `keyLength` [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) The expected or default key length in bytes.
  - `mode` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) The cipher mode. One of `'cbc'`, `'ccm'`, `'cfb'`, `'ctr'`,
    `'ecb'`, `'gcm'`, `'gcm-siv'`, `'ocb'`, `'ofb'`, `'siv'`, `'stream'`,
    `'wrap'`, `'xts'`.

Returns information about a given cipher.

Some ciphers accept variable length keys and initialization vectors. By default,
the `crypto.getCipherInfo()` method will return the default values for these
ciphers. To test if a given key length or iv length is acceptable for given
cipher, use the `keyLength` and `ivLength` options. If the given values are
unacceptable, `undefined` will be returned.

### `crypto.getCiphers()`

Added in: v0.9.3

- Returns: [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type)[] An array with the names of the supported cipher
  algorithms.

*ES module version:*

```js
const {
  getCiphers,
} = await import('node:crypto');

console.log(getCiphers()); // ['aes-128-cbc', 'aes-128-ccm', ...]
```

*CommonJS version:*

```js
const {
  getCiphers,
} = require('node:crypto');

console.log(getCiphers()); // ['aes-128-cbc', 'aes-128-ccm', ...]
```

### `crypto.getCurves()`

Added in: v2.3.0

- Returns: [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type)[] An array with the names of the supported elliptic curves.

*ES module version:*

```js
const {
  getCurves,
} = await import('node:crypto');

console.log(getCurves()); // ['Oakley-EC2N-3', 'Oakley-EC2N-4', ...]
```

*CommonJS version:*

```js
const {
  getCurves,
} = require('node:crypto');

console.log(getCurves()); // ['Oakley-EC2N-3', 'Oakley-EC2N-4', ...]
```

### `crypto.getDiffieHellman(groupName)`

Added in: v0.7.5

- `groupName` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type)
- Returns: [`<DiffieHellmanGroup>`](15-crypto.md#class-diffiehellmangroup)

Creates a predefined `DiffieHellmanGroup` key exchange object. The
supported groups are listed in the documentation for [`DiffieHellmanGroup`](#class-diffiehellmangroup).

The returned object mimics the interface of objects created by
[`crypto.createDiffieHellman()`](#cryptocreatediffiehellmanprime-primeencoding-generator-generatorencoding), but will not allow changing
the keys (with [`diffieHellman.setPublicKey()`](#diffiehellmansetpublickeypublickey-encoding), for example). The
advantage of using this method is that the parties do not have to
generate nor exchange a group modulus beforehand, saving both processor
and communication time.

Example (obtaining a shared secret):

*ES module version:*

```js
const {
  getDiffieHellman,
} = await import('node:crypto');
const alice = getDiffieHellman('modp14');
const bob = getDiffieHellman('modp14');

alice.generateKeys();
bob.generateKeys();

const aliceSecret = alice.computeSecret(bob.getPublicKey(), null, 'hex');
const bobSecret = bob.computeSecret(alice.getPublicKey(), null, 'hex');

/* aliceSecret and bobSecret should be the same */
console.log(aliceSecret === bobSecret);
```

*CommonJS version:*

```js
const {
  getDiffieHellman,
} = require('node:crypto');

const alice = getDiffieHellman('modp14');
const bob = getDiffieHellman('modp14');

alice.generateKeys();
bob.generateKeys();

const aliceSecret = alice.computeSecret(bob.getPublicKey(), null, 'hex');
const bobSecret = bob.computeSecret(alice.getPublicKey(), null, 'hex');

/* aliceSecret and bobSecret should be the same */
console.log(aliceSecret === bobSecret);
```

### `crypto.getFips()`

Added in: v10.0.0

- Returns: [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) `1` if FIPS mode is enabled, `0` otherwise. A future
  semver-major release may change the return type of this API to a [`<boolean>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#boolean_type).

With OpenSSL 3, this reports whether the default property query includes
`fips=yes`. It does not establish that a FIPS provider is loaded or validated.
It can return `1` even when a requested cryptographic implementation cannot be
fetched because no loaded provider supplies a match for `fips=yes`. See [FIPS
mode](#fips-mode).

### `crypto.getHashes()`

Added in: v0.9.3

**History**

| Version | Changes |
| --- | --- |
| v26.9.0 | Names and aliases exposed by loaded OpenSSL providers that match the default property query are now included. |

- Returns: [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type)[] An array of the names of the supported hash algorithms,
  such as `'RSA-SHA256'`. Hash algorithms are also called "digest" algorithms.

This is the authoritative Node.js list of hash algorithms available to
[`crypto.createHash()`](#cryptocreatehashalgorithm-options) and [`crypto.hash()`](#cryptohashalgorithm-data-options) in the current process. With
OpenSSL 3 or later, the list depends on the loaded providers and the default
property query in effect when the list is first generated. Some listed
algorithms can require API-specific options, such as `outputLength` for XOF
hash functions.

A listed hash algorithm is not necessarily supported by APIs that combine a
digest with another cryptographic operation, such as HMAC, key derivation, or
signing. Those operations can apply additional restrictions.

*ES module version:*

```js
const {
  getHashes,
} = await import('node:crypto');

console.log(getHashes()); // ['DSA', 'DSA-SHA', 'DSA-SHA1', ...]
```

*CommonJS version:*

```js
const {
  getHashes,
} = require('node:crypto');

console.log(getHashes()); // ['DSA', 'DSA-SHA', 'DSA-SHA1', ...]
```

### `crypto.getMacs()`

Added in: v26.9.0

Stability: 1.2 - Release candidate

- Returns: [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type)[] A fresh array containing the sorted, lowercase names
  and aliases of fetchable MAC implementations.

Returns MAC names exposed by loaded OpenSSL providers that match the active
default property query. Duplicate names and numeric OID aliases are omitted.
On builds without OpenSSL `EVP_MAC` support, this function returns an empty
array.

The returned names describe implementations that OpenSSL can fetch. They do not
guarantee that [`crypto.createMac()`](#cryptocreatemacalgorithm-key-options) can initialize the MAC without
additional options. A provider can require additional parameters or a key with
algorithm-specific properties, and it can expose parameters that this API does
not support.

After a successful FIPS mode change made with [`crypto.setFips()`](#cryptosetfipsbool), subsequent
calls reflect the new mode, and newly created `Mac` objects use it. Existing
`Mac` objects continue using the provider implementation selected when they
were created.

```js
const { getMacs } = await import('node:crypto');

console.log(getMacs());
// ['blake2bmac', 'blake2smac', 'cmac', 'gmac', 'hmac', ...]
```

### `crypto.getRandomValues(typedArray)`

Added in: v17.4.0

- `typedArray` [`<Buffer>`](07-buffer.md#class-buffer) | [`<TypedArray>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/TypedArray) | [`<DataView>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/DataView) | [`<ArrayBuffer>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/ArrayBuffer)
- Returns: [`<Buffer>`](07-buffer.md#class-buffer) | [`<TypedArray>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/TypedArray) | [`<DataView>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/DataView) | [`<ArrayBuffer>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/ArrayBuffer) Returns `typedArray`.

A convenient alias for [`crypto.webcrypto.getRandomValues()`](65-webcrypto.md#cryptogetrandomvaluestypedarray). This
implementation is not compliant with the Web Crypto spec, to write
web-compatible code use [`crypto.webcrypto.getRandomValues()`](65-webcrypto.md#cryptogetrandomvaluestypedarray) instead.

### `crypto.hash(algorithm, data[, options])`

Added in: v21.7.0, v20.12.0

**History**

| Version | Changes |
| --- | --- |
| v26.9.0 | Hash algorithms exposed by OpenSSL providers are now supported. The `functionName` and `customization` options were added for cSHAKE hash functions. |
| v25.5.0, v24.13.1 | This API is no longer experimental. |
| v24.4.0 | The `outputLength` option was added for XOF hash functions. |

- `algorithm` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) | [`<undefined>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#undefined_type)
- `data` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) | [`<Buffer>`](07-buffer.md#class-buffer) | [`<TypedArray>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/TypedArray) | [`<DataView>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/DataView) When `data` is a
  string, it will be encoded as UTF-8 before being hashed. If a different
  input encoding is desired for a string input, user could encode the string
  into a `TypedArray` using either `TextEncoder` or `Buffer.from()` and passing
  the encoded `TypedArray` into this API instead.
- `options` [`<Object>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Object) | [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type)
  - `customization` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) | [`<ArrayBuffer>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/ArrayBuffer) | [`<Buffer>`](07-buffer.md#class-buffer) | [`<TypedArray>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/TypedArray) | [`<DataView>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/DataView) For cSHAKE
    hash functions, specifies the customization byte string. **Default:** an
    empty byte string.
  - `functionName` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) | [`<ArrayBuffer>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/ArrayBuffer) | [`<Buffer>`](07-buffer.md#class-buffer) | [`<TypedArray>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/TypedArray) | [`<DataView>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/DataView) For cSHAKE
    hash functions, specifies the NIST function-name byte string. **Default:**
    an empty byte string.
  - `outputEncoding` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) [Encoding](07-buffer.md#buffers-and-character-encodings) used to encode the
    returned digest. **Default:** `'hex'`.
  - `outputLength` [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) For XOF hash functions such as 'shake256',
    the outputLength option can be used to specify the desired output length in bytes.
- Returns: [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) | [`<Buffer>`](07-buffer.md#class-buffer)

A utility for creating one-shot hash digests of data. It can be faster than
the object-based `crypto.createHash()` when hashing a smaller amount of data
(\<= 5MB) that's readily available. If the data can be big or if it is streamed,
it's still recommended to use `crypto.createHash()` instead.

The available algorithms depend on the version and configuration of OpenSSL on
the platform. Examples are `'sha256'` and `'sha512'`. Use
[`crypto.getHashes()`](#cryptogethashes) to obtain the list of hash algorithms available to the
Node.js process.

The `functionName` and `customization` options apply only to cSHAKE-128 and
cSHAKE-256. They are supported only when Node.js is built with OpenSSL 4.0 or
later and the selected provider supports the corresponding digest parameters.
Strings are encoded as UTF-8, and neither strings nor byte values may contain
NUL bytes. Both options default to an empty byte string. For OpenSSL's built-in
providers, `functionName` is case-sensitive and must be `''`, `'TupleHash'`,
`'ParallelHash'`, or `'KMAC'`. Other providers can impose different
restrictions. With both options empty, cSHAKE produces the same output as the
corresponding SHAKE function for the same output length. `cshake-128` and
`cshake-256` default to output lengths of 32 and 64 bytes, respectively.

If `options` is a string, then it specifies the `outputEncoding`.

Example:

*CommonJS version:*

```js
const crypto = require('node:crypto');
const { Buffer } = require('node:buffer');

// Hashing a string and return the result as a hex-encoded string.
const string = 'Node.js';
// 10b3493287f831e81a438811a1ffba01f8cec4b7
console.log(crypto.hash('sha1', string));

// Encode a base64-encoded string into a Buffer, hash it and return
// the result as a buffer.
const base64 = 'Tm9kZS5qcw==';
// <Buffer 10 b3 49 32 87 f8 31 e8 1a 43 88 11 a1 ff ba 01 f8 ce c4 b7>
console.log(crypto.hash('sha1', Buffer.from(base64, 'base64'), 'buffer'));
```

*ES module version:*

```js
import crypto from 'node:crypto';
import { Buffer } from 'node:buffer';

// Hashing a string and return the result as a hex-encoded string.
const string = 'Node.js';
// 10b3493287f831e81a438811a1ffba01f8cec4b7
console.log(crypto.hash('sha1', string));

// Encode a base64-encoded string into a Buffer, hash it and return
// the result as a buffer.
const base64 = 'Tm9kZS5qcw==';
// <Buffer 10 b3 49 32 87 f8 31 e8 1a 43 88 11 a1 ff ba 01 f8 ce c4 b7>
console.log(crypto.hash('sha1', Buffer.from(base64, 'base64'), 'buffer'));
```

### `crypto.hkdf(digest, ikm, salt, info, keylen, callback)`

Added in: v15.0.0

**History**

| Version | Changes |
| --- | --- |
| v18.8.0, v16.18.0 | The input keying material can now be zero-length. |
| v18.0.0 | Passing an invalid callback to the `callback` argument now throws `ERR_INVALID_ARG_TYPE` instead of `ERR_INVALID_CALLBACK`. |

- `digest` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) The digest algorithm to use.
- `ikm` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) | [`<ArrayBuffer>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/ArrayBuffer) | [`<Buffer>`](07-buffer.md#class-buffer) | [`<TypedArray>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/TypedArray) | [`<DataView>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/DataView) | [`<KeyObject>`](15-crypto.md#class-keyobject) The input
  keying material. Must be provided but can be zero-length.
- `salt` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) | [`<ArrayBuffer>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/ArrayBuffer) | [`<Buffer>`](07-buffer.md#class-buffer) | [`<TypedArray>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/TypedArray) | [`<DataView>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/DataView) The salt value. Must
  be provided but can be zero-length.
- `info` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) | [`<ArrayBuffer>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/ArrayBuffer) | [`<Buffer>`](07-buffer.md#class-buffer) | [`<TypedArray>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/TypedArray) | [`<DataView>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/DataView) Additional info value.
  Must be provided but can be zero-length, and cannot be more than 1024 bytes.
- `keylen` [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) The length of the key to generate. Must be greater than 0.
  The maximum allowable value is `255` times the number of bytes produced by
  the selected digest function (e.g. `sha512` generates 64-byte hashes, making
  the maximum HKDF output 16320 bytes).
- `callback` [`<Function>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Function)
  - `err` [`<Error>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Error)
  - `derivedKey` [`<ArrayBuffer>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/ArrayBuffer)

HKDF is a simple key derivation function defined in RFC 5869. The given `ikm`,
`salt` and `info` are used with the `digest` to derive a key of `keylen` bytes.
The available digest algorithms depend on the version and configuration of
OpenSSL. HKDF uses HMAC internally. [`crypto.getHashes()`](#cryptogethashes) lists algorithms
available to the hashing APIs, but not every listed algorithm is necessarily
suitable for HMAC or HKDF.

The supplied `callback` function is called with two arguments: `err` and
`derivedKey`. If an error occurs while deriving the key, `err` will be set;
otherwise `err` will be `null`. The successfully generated `derivedKey` will
be passed to the callback as an [`<ArrayBuffer>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/ArrayBuffer). An error will be thrown if any
of the input arguments specify invalid values or types.

*ES module version:*

```js
import { Buffer } from 'node:buffer';
const {
  hkdf,
} = await import('node:crypto');

hkdf('sha512', 'key', 'salt', 'info', 64, (err, derivedKey) => {
  if (err) throw err;
  console.log(Buffer.from(derivedKey).toString('hex'));  // '24156e2...5391653'
});
```

*CommonJS version:*

```js
const {
  hkdf,
} = require('node:crypto');
const { Buffer } = require('node:buffer');

hkdf('sha512', 'key', 'salt', 'info', 64, (err, derivedKey) => {
  if (err) throw err;
  console.log(Buffer.from(derivedKey).toString('hex'));  // '24156e2...5391653'
});
```

### `crypto.hkdfSync(digest, ikm, salt, info, keylen)`

Added in: v15.0.0

**History**

| Version | Changes |
| --- | --- |
| v18.8.0, v16.18.0 | The input keying material can now be zero-length. |

- `digest` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) The digest algorithm to use.
- `ikm` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) | [`<ArrayBuffer>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/ArrayBuffer) | [`<Buffer>`](07-buffer.md#class-buffer) | [`<TypedArray>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/TypedArray) | [`<DataView>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/DataView) | [`<KeyObject>`](15-crypto.md#class-keyobject) The input
  keying material. Must be provided but can be zero-length.
- `salt` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) | [`<ArrayBuffer>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/ArrayBuffer) | [`<Buffer>`](07-buffer.md#class-buffer) | [`<TypedArray>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/TypedArray) | [`<DataView>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/DataView) The salt value. Must
  be provided but can be zero-length.
- `info` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) | [`<ArrayBuffer>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/ArrayBuffer) | [`<Buffer>`](07-buffer.md#class-buffer) | [`<TypedArray>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/TypedArray) | [`<DataView>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/DataView) Additional info value.
  Must be provided but can be zero-length, and cannot be more than 1024 bytes.
- `keylen` [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) The length of the key to generate. Must be greater than 0.
  The maximum allowable value is `255` times the number of bytes produced by
  the selected digest function (e.g. `sha512` generates 64-byte hashes, making
  the maximum HKDF output 16320 bytes).
- Returns: [`<ArrayBuffer>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/ArrayBuffer)

Provides a synchronous HKDF key derivation function as defined in RFC 5869. The
given `ikm`, `salt` and `info` are used with the `digest` to derive a key of
`keylen` bytes.
The available digest algorithms depend on the version and configuration of
OpenSSL. HKDF uses HMAC internally. [`crypto.getHashes()`](#cryptogethashes) lists algorithms
available to the hashing APIs, but not every listed algorithm is necessarily
suitable for HMAC or HKDF.

The successfully generated `derivedKey` will be returned as an [`<ArrayBuffer>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/ArrayBuffer).

An error will be thrown if any of the input arguments specify invalid values or
types, or if the derived key cannot be generated.

*ES module version:*

```js
import { Buffer } from 'node:buffer';
const {
  hkdfSync,
} = await import('node:crypto');

const derivedKey = hkdfSync('sha512', 'key', 'salt', 'info', 64);
console.log(Buffer.from(derivedKey).toString('hex'));  // '24156e2...5391653'
```

*CommonJS version:*

```js
const {
  hkdfSync,
} = require('node:crypto');
const { Buffer } = require('node:buffer');

const derivedKey = hkdfSync('sha512', 'key', 'salt', 'info', 64);
console.log(Buffer.from(derivedKey).toString('hex'));  // '24156e2...5391653'
```

### `crypto.parsePKCS12(bundle[, options])`

Added in: v26.10.0

- `bundle` [`<ArrayBuffer>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/ArrayBuffer) | [`<Buffer>`](07-buffer.md#class-buffer) | [`<TypedArray>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/TypedArray) | [`<DataView>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/DataView) A DER-encoded PKCS#12
  (`.p12` or `.pfx`) bundle.
- `options` [`<Object>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Object)
  - `passphrase` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) | [`<ArrayBuffer>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/ArrayBuffer) | [`<Buffer>`](07-buffer.md#class-buffer) | [`<TypedArray>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/TypedArray) | [`<DataView>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/DataView) The passphrase
    protecting the bundle. Omitting this option is equivalent to passing `''`.
- Returns: [`<Object>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Object)
  - `privateKey` [`<KeyObject>`](15-crypto.md#class-keyobject) | [`<null>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#null_type) The first private key in the bundle, or `null` if none is present.
  - `certificate` [`<X509Certificate>`](15-crypto.md#class-x509certificate) | [`<null>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#null_type) The certificate matching `privateKey`,
    or `null` if no matching certificate is present.
  - `additionalCertificates` [`<X509Certificate>`](15-crypto.md#class-x509certificate)[] All other certificates in
    the bundle. If there is no private key, this contains all certificates.
    May be empty.

Parses a PKCS#12 bundle, commonly stored with a `.p12` or `.pfx` extension,
and returns its private key and certificates.

```js
import { parsePKCS12 } from 'node:crypto';
import { readFileSync } from 'node:fs';

const { privateKey, certificate, additionalCertificates } = parsePKCS12(
  readFileSync('bundle.p12'),
  { passphrase: 'secret' },
);
```

### `crypto.pbkdf2(password, salt, iterations, keylen, digest, callback)`

Added in: v0.5.5

**History**

| Version | Changes |
| --- | --- |
| v18.0.0 | Passing an invalid callback to the `callback` argument now throws `ERR_INVALID_ARG_TYPE` instead of `ERR_INVALID_CALLBACK`. |
| v15.0.0 | The password and salt arguments can also be ArrayBuffer instances. |
| v14.0.0 | The `iterations` parameter is now restricted to positive values. Earlier releases treated other values as one. |
| v8.0.0 | The `digest` parameter is always required now. |
| v6.0.0 | Calling this function without passing the `digest` parameter is deprecated now and will emit a warning. |
| v6.0.0 | The default encoding for `password` if it is a string changed from `binary` to `utf8`. |

- `password` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) | [`<ArrayBuffer>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/ArrayBuffer) | [`<Buffer>`](07-buffer.md#class-buffer) | [`<TypedArray>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/TypedArray) | [`<DataView>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/DataView)
- `salt` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) | [`<ArrayBuffer>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/ArrayBuffer) | [`<Buffer>`](07-buffer.md#class-buffer) | [`<TypedArray>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/TypedArray) | [`<DataView>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/DataView)
- `iterations` [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type)
- `keylen` [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type)
- `digest` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type)
- `callback` [`<Function>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Function)
  - `err` [`<Error>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Error)
  - `derivedKey` [`<Buffer>`](07-buffer.md#class-buffer)

Provides an asynchronous Password-Based Key Derivation Function 2 (PBKDF2)
implementation. A selected HMAC digest algorithm specified by `digest` is
applied to derive a key of the requested byte length (`keylen`) from the
`password`, `salt` and `iterations`.

The supplied `callback` function is called with two arguments: `err` and
`derivedKey`. If an error occurs while deriving the key, `err` will be set;
otherwise `err` will be `null`. By default, the successfully generated
`derivedKey` will be passed to the callback as a [`Buffer`](07-buffer.md). An error will be
thrown if any of the input arguments specify invalid values or types.

The `iterations` argument must be a number set as high as possible. The
higher the number of iterations, the more secure the derived key will be,
but will take a longer amount of time to complete.

The `salt` should be as unique as possible. It is recommended that a salt is
random and at least 16 bytes long. See [NIST SP 800-132](https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-132.pdf) for details.

When passing strings for `password` or `salt`, please consider
[caveats when using strings as inputs to cryptographic APIs](#using-strings-as-inputs-to-cryptographic-apis).

*ES module version:*

```js
const {
  pbkdf2,
} = await import('node:crypto');

pbkdf2('secret', 'salt', 100000, 64, 'sha512', (err, derivedKey) => {
  if (err) throw err;
  console.log(derivedKey.toString('hex'));  // '3745e48...08d59ae'
});
```

*CommonJS version:*

```js
const {
  pbkdf2,
} = require('node:crypto');

pbkdf2('secret', 'salt', 100000, 64, 'sha512', (err, derivedKey) => {
  if (err) throw err;
  console.log(derivedKey.toString('hex'));  // '3745e48...08d59ae'
});
```

The available digest algorithms depend on the version and configuration of
OpenSSL. PBKDF2 uses HMAC internally. [`crypto.getHashes()`](#cryptogethashes) lists algorithms
available to the hashing APIs, but not every listed algorithm is necessarily
suitable for HMAC or PBKDF2.

This API uses libuv's threadpool, which can have surprising and
negative performance implications for some applications; see the
[`UV_THREADPOOL_SIZE`](13-cli.md#uv_threadpool_sizesize) documentation for more information.

### `crypto.pbkdf2Sync(password, salt, iterations, keylen, digest)`

Added in: v0.9.3

**History**

| Version | Changes |
| --- | --- |
| v15.0.0 | The password and salt arguments can also be ArrayBuffer instances. |
| v14.0.0 | The `iterations` parameter is now restricted to positive values. Earlier releases treated other values as one. |
| v6.0.0 | Calling this function without passing the `digest` parameter is deprecated now and will emit a warning. |
| v6.0.0 | The default encoding for `password` if it is a string changed from `binary` to `utf8`. |

- `password` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) | [`<ArrayBuffer>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/ArrayBuffer) | [`<Buffer>`](07-buffer.md#class-buffer) | [`<TypedArray>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/TypedArray) | [`<DataView>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/DataView)
- `salt` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) | [`<ArrayBuffer>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/ArrayBuffer) | [`<Buffer>`](07-buffer.md#class-buffer) | [`<TypedArray>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/TypedArray) | [`<DataView>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/DataView)
- `iterations` [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type)
- `keylen` [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type)
- `digest` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type)
- Returns: [`<Buffer>`](07-buffer.md#class-buffer)

Provides a synchronous Password-Based Key Derivation Function 2 (PBKDF2)
implementation. A selected HMAC digest algorithm specified by `digest` is
applied to derive a key of the requested byte length (`keylen`) from the
`password`, `salt` and `iterations`.

If an error occurs an `Error` will be thrown, otherwise the derived key will be
returned as a [`Buffer`](07-buffer.md).

The `iterations` argument must be a number set as high as possible. The
higher the number of iterations, the more secure the derived key will be,
but will take a longer amount of time to complete.

The `salt` should be as unique as possible. It is recommended that a salt is
random and at least 16 bytes long. See [NIST SP 800-132](https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-132.pdf) for details.

When passing strings for `password` or `salt`, please consider
[caveats when using strings as inputs to cryptographic APIs](#using-strings-as-inputs-to-cryptographic-apis).

*ES module version:*

```js
const {
  pbkdf2Sync,
} = await import('node:crypto');

const key = pbkdf2Sync('secret', 'salt', 100000, 64, 'sha512');
console.log(key.toString('hex'));  // '3745e48...08d59ae'
```

*CommonJS version:*

```js
const {
  pbkdf2Sync,
} = require('node:crypto');

const key = pbkdf2Sync('secret', 'salt', 100000, 64, 'sha512');
console.log(key.toString('hex'));  // '3745e48...08d59ae'
```

The available digest algorithms depend on the version and configuration of
OpenSSL. PBKDF2 uses HMAC internally. [`crypto.getHashes()`](#cryptogethashes) lists algorithms
available to the hashing APIs, but not every listed algorithm is necessarily
suitable for HMAC or PBKDF2.

### `crypto.privateDecrypt(privateKey, buffer)`

Added in: v0.11.14

**History**

| Version | Changes |
| --- | --- |
| v26.8.0 | The `mgf1Hash` option was added. |
| v21.6.2, v20.11.1, v18.19.1 | The `RSA_PKCS1_PADDING` padding was disabled unless the OpenSSL build supports implicit rejection. |
| v15.0.0 | Added string, ArrayBuffer, and CryptoKey as allowable key types. The oaepLabel can be an ArrayBuffer. The buffer can be a string or ArrayBuffer. All types that accept buffers are limited to a maximum of 2 \*\* 31 - 1 bytes. |
| v12.11.0 | The `oaepLabel` option was added. |
| v12.9.0 | The `oaepHash` option was added. |
| v11.6.0 | This function now supports key objects. |

- `privateKey` [`<Object>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Object) | [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) | [`<ArrayBuffer>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/ArrayBuffer) | [`<Buffer>`](07-buffer.md#class-buffer) | [`<TypedArray>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/TypedArray) | [`<DataView>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/DataView) | [`<KeyObject>`](15-crypto.md#class-keyobject) | [`<CryptoKey>`](65-webcrypto.md#class-cryptokey) | [`<URL>`](59-url.md#the-whatwg-url-api)
  - `oaepHash` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) The hash function to use for OAEP padding and, unless `mgf1Hash` is set, MGF1. **Default:** `'sha1'`
  - `mgf1Hash` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) The hash function to use for the MGF1 mask generation
    function of OAEP padding. If not specified, the value of `oaepHash` is used.
    This allows the OAEP digest and the MGF1 digest to differ.
  - `oaepLabel` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) | [`<ArrayBuffer>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/ArrayBuffer) | [`<Buffer>`](07-buffer.md#class-buffer) | [`<TypedArray>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/TypedArray) | [`<DataView>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/DataView) The label to
    use for OAEP padding. If not specified, no label is used.
  - `padding` [`<crypto.constants>`](15-crypto.md#cryptoconstants) An optional padding value defined in `crypto.constants`, which may be: `crypto.constants.RSA_NO_PADDING`,
    `crypto.constants.RSA_PKCS1_PADDING`, or
    `crypto.constants.RSA_PKCS1_OAEP_PADDING`.
- `buffer` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) | [`<ArrayBuffer>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/ArrayBuffer) | [`<Buffer>`](07-buffer.md#class-buffer) | [`<TypedArray>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/TypedArray) | [`<DataView>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/DataView)
- Returns: [`<Buffer>`](07-buffer.md#class-buffer) A new `Buffer` with the decrypted content.

Decrypts `buffer` with `privateKey`. `buffer` was previously encrypted using
the corresponding public key, for example using [`crypto.publicEncrypt()`](#cryptopublicencryptkey-buffer).

[`crypto.getHashes()`](#cryptogethashes) lists algorithms available to the hashing APIs, but
the active RSA implementation can impose additional restrictions on digests
used for OAEP or MGF1.

If `privateKey` is not a [`KeyObject`](#class-keyobject), this function behaves as if
`privateKey` had been passed to [`crypto.createPrivateKey()`](#cryptocreateprivatekeykey). If it is an
object, the `padding` property can be passed. Otherwise, this function uses
`RSA_PKCS1_OAEP_PADDING`.

Using `crypto.constants.RSA_PKCS1_PADDING` in [`crypto.privateDecrypt()`](#cryptoprivatedecryptprivatekey-buffer)
requires OpenSSL to support implicit rejection (`rsa_pkcs1_implicit_rejection`).
If the version of OpenSSL used by Node.js does not support this feature,
attempting to use `RSA_PKCS1_PADDING` will fail.

### `crypto.privateEncrypt(privateKey, buffer)`

Added in: v1.1.0

**History**

| Version | Changes |
| --- | --- |
| v15.0.0 | Added string, ArrayBuffer, and CryptoKey as allowable key types. The passphrase can be an ArrayBuffer. The buffer can be a string or ArrayBuffer. All types that accept buffers are limited to a maximum of 2 \*\* 31 - 1 bytes. |
| v11.6.0 | This function now supports key objects. |

- `privateKey` [`<Object>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Object) | [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) | [`<ArrayBuffer>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/ArrayBuffer) | [`<Buffer>`](07-buffer.md#class-buffer) | [`<TypedArray>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/TypedArray) | [`<DataView>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/DataView) | [`<KeyObject>`](15-crypto.md#class-keyobject) | [`<CryptoKey>`](65-webcrypto.md#class-cryptokey) | [`<URL>`](59-url.md#the-whatwg-url-api)
  - `key` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) | [`<ArrayBuffer>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/ArrayBuffer) | [`<Buffer>`](07-buffer.md#class-buffer) | [`<TypedArray>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/TypedArray) | [`<DataView>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/DataView) | [`<KeyObject>`](15-crypto.md#class-keyobject) | [`<CryptoKey>`](65-webcrypto.md#class-cryptokey) | [`<URL>`](59-url.md#the-whatwg-url-api)
    The private key material, a [`<KeyObject>`](15-crypto.md#class-keyobject), or a [`<URL>`](59-url.md#the-whatwg-url-api) referencing an object
    for an OpenSSL STORE loader.
  - `passphrase` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) | [`<ArrayBuffer>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/ArrayBuffer) | [`<Buffer>`](07-buffer.md#class-buffer) | [`<TypedArray>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/TypedArray) | [`<DataView>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/DataView) An optional
    passphrase for the private key.
  - `padding` [`<crypto.constants>`](15-crypto.md#cryptoconstants) An optional padding value defined in `crypto.constants`, which may be: `crypto.constants.RSA_NO_PADDING` or
    `crypto.constants.RSA_PKCS1_PADDING`.
  - `encoding` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) The string encoding to use when `buffer`, `key`,
    or `passphrase` are strings.
- `buffer` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) | [`<ArrayBuffer>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/ArrayBuffer) | [`<Buffer>`](07-buffer.md#class-buffer) | [`<TypedArray>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/TypedArray) | [`<DataView>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/DataView)
- Returns: [`<Buffer>`](07-buffer.md#class-buffer) A new `Buffer` with the encrypted content.

Encrypts `buffer` with `privateKey`. The returned data can be decrypted using
the corresponding public key, for example using [`crypto.publicDecrypt()`](#cryptopublicdecryptkey-buffer).

If `privateKey` is not a [`KeyObject`](#class-keyobject), this function behaves as if
`privateKey` had been passed to [`crypto.createPrivateKey()`](#cryptocreateprivatekeykey). If it is an
object, the `padding` property can be passed. Otherwise, this function uses
`RSA_PKCS1_PADDING`.

### `crypto.publicDecrypt(key, buffer)`

Added in: v1.1.0

**History**

| Version | Changes |
| --- | --- |
| v15.0.0 | Added string, ArrayBuffer, and CryptoKey as allowable key types. The passphrase can be an ArrayBuffer. The buffer can be a string or ArrayBuffer. All types that accept buffers are limited to a maximum of 2 \*\* 31 - 1 bytes. |
| v11.6.0 | This function now supports key objects. |

- `key` [`<Object>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Object) | [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) | [`<ArrayBuffer>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/ArrayBuffer) | [`<Buffer>`](07-buffer.md#class-buffer) | [`<TypedArray>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/TypedArray) | [`<DataView>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/DataView) | [`<KeyObject>`](15-crypto.md#class-keyobject) | [`<CryptoKey>`](65-webcrypto.md#class-cryptokey)
  - `passphrase` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) | [`<ArrayBuffer>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/ArrayBuffer) | [`<Buffer>`](07-buffer.md#class-buffer) | [`<TypedArray>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/TypedArray) | [`<DataView>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/DataView) An optional
    passphrase for the private key.
  - `padding` [`<crypto.constants>`](15-crypto.md#cryptoconstants) An optional padding value defined in `crypto.constants`, which may be: `crypto.constants.RSA_NO_PADDING` or
    `crypto.constants.RSA_PKCS1_PADDING`.
  - `encoding` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) The string encoding to use when `buffer`, `key`,
    or `passphrase` are strings.
- `buffer` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) | [`<ArrayBuffer>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/ArrayBuffer) | [`<Buffer>`](07-buffer.md#class-buffer) | [`<TypedArray>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/TypedArray) | [`<DataView>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/DataView)
- Returns: [`<Buffer>`](07-buffer.md#class-buffer) A new `Buffer` with the decrypted content.

Decrypts `buffer` with `key`. `buffer` was previously encrypted using
the corresponding private key, for example using [`crypto.privateEncrypt()`](#cryptoprivateencryptprivatekey-buffer).

If `key` is not a [`KeyObject`](#class-keyobject), this function behaves as if
`key` had been passed to [`crypto.createPublicKey()`](#cryptocreatepublickeykey). If it is an
object, the `padding` property can be passed. Otherwise, this function uses
`RSA_PKCS1_PADDING`.

Because RSA public keys can be derived from private keys, a private key may
be passed instead of a public key.

### `crypto.publicEncrypt(key, buffer)`

Added in: v0.11.14

**History**

| Version | Changes |
| --- | --- |
| v26.8.0 | The `mgf1Hash` option was added. |
| v15.0.0 | Added string, ArrayBuffer, and CryptoKey as allowable key types. The oaepLabel and passphrase can be ArrayBuffers. The buffer can be a string or ArrayBuffer. All types that accept buffers are limited to a maximum of 2 \*\* 31 - 1 bytes. |
| v12.11.0 | The `oaepLabel` option was added. |
| v12.9.0 | The `oaepHash` option was added. |
| v11.6.0 | This function now supports key objects. |

- `key` [`<Object>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Object) | [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) | [`<ArrayBuffer>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/ArrayBuffer) | [`<Buffer>`](07-buffer.md#class-buffer) | [`<TypedArray>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/TypedArray) | [`<DataView>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/DataView) | [`<KeyObject>`](15-crypto.md#class-keyobject) | [`<CryptoKey>`](65-webcrypto.md#class-cryptokey)
  - `key` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) | [`<ArrayBuffer>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/ArrayBuffer) | [`<Buffer>`](07-buffer.md#class-buffer) | [`<TypedArray>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/TypedArray) | [`<DataView>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/DataView) | [`<KeyObject>`](15-crypto.md#class-keyobject) | [`<CryptoKey>`](65-webcrypto.md#class-cryptokey)
    A PEM encoded public or private key, [`<KeyObject>`](15-crypto.md#class-keyobject), or [`<CryptoKey>`](65-webcrypto.md#class-cryptokey).
  - `oaepHash` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) The hash function to use for OAEP padding and, unless `mgf1Hash` is set, MGF1. **Default:** `'sha1'`
  - `mgf1Hash` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) The hash function to use for the MGF1 mask generation
    function of OAEP padding. If not specified, the value of `oaepHash` is used.
    This allows the OAEP digest and the MGF1 digest to differ.
  - `oaepLabel` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) | [`<ArrayBuffer>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/ArrayBuffer) | [`<Buffer>`](07-buffer.md#class-buffer) | [`<TypedArray>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/TypedArray) | [`<DataView>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/DataView) The label to
    use for OAEP padding. If not specified, no label is used.
  - `passphrase` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) | [`<ArrayBuffer>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/ArrayBuffer) | [`<Buffer>`](07-buffer.md#class-buffer) | [`<TypedArray>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/TypedArray) | [`<DataView>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/DataView) An optional
    passphrase for the private key.
  - `padding` [`<crypto.constants>`](15-crypto.md#cryptoconstants) An optional padding value defined in `crypto.constants`, which may be: `crypto.constants.RSA_NO_PADDING`,
    `crypto.constants.RSA_PKCS1_PADDING`, or
    `crypto.constants.RSA_PKCS1_OAEP_PADDING`.
  - `encoding` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) The string encoding to use when `buffer`, `key`,
    `oaepLabel`, or `passphrase` are strings.
- `buffer` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) | [`<ArrayBuffer>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/ArrayBuffer) | [`<Buffer>`](07-buffer.md#class-buffer) | [`<TypedArray>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/TypedArray) | [`<DataView>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/DataView)
- Returns: [`<Buffer>`](07-buffer.md#class-buffer) A new `Buffer` with the encrypted content.

Encrypts the content of `buffer` with `key` and returns a new
[`Buffer`](07-buffer.md) with encrypted content. The returned data can be decrypted using
the corresponding private key, for example using [`crypto.privateDecrypt()`](#cryptoprivatedecryptprivatekey-buffer).

[`crypto.getHashes()`](#cryptogethashes) lists algorithms available to the hashing APIs, but
the active RSA implementation can impose additional restrictions on digests
used for OAEP or MGF1.

If `key` is not a [`KeyObject`](#class-keyobject), this function behaves as if
`key` had been passed to [`crypto.createPublicKey()`](#cryptocreatepublickeykey). If it is an
object, the `padding` property can be passed. Otherwise, this function uses
`RSA_PKCS1_OAEP_PADDING`.

Because RSA public keys can be derived from private keys, a private key may
be passed instead of a public key.

### `crypto.randomBytes(size[, callback])`

Added in: v0.5.8

**History**

| Version | Changes |
| --- | --- |
| v18.0.0 | Passing an invalid callback to the `callback` argument now throws `ERR_INVALID_ARG_TYPE` instead of `ERR_INVALID_CALLBACK`. |
| v9.0.0 | Passing `null` as the `callback` argument now throws `ERR_INVALID_CALLBACK`. |

- `size` [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) The number of bytes to generate. The `size` must
  not be larger than `2**31 - 1`.
- `callback` [`<Function>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Function)
  - `err` [`<Error>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Error)
  - `buf` [`<Buffer>`](07-buffer.md#class-buffer)
- Returns: [`<Buffer>`](07-buffer.md#class-buffer) if the `callback` function is not provided.

Generates cryptographically strong pseudorandom data. The `size` argument
is a number indicating the number of bytes to generate.

If a `callback` function is provided, the bytes are generated asynchronously
and the `callback` function is invoked with two arguments: `err` and `buf`.
If an error occurs, `err` will be an `Error` object; otherwise it is `null`. The
`buf` argument is a [`Buffer`](07-buffer.md) containing the generated bytes.

*ES module version:*

```js
// Asynchronous
const {
  randomBytes,
} = await import('node:crypto');

randomBytes(256, (err, buf) => {
  if (err) throw err;
  console.log(`${buf.length} bytes of random data: ${buf.toString('hex')}`);
});
```

*CommonJS version:*

```js
// Asynchronous
const {
  randomBytes,
} = require('node:crypto');

randomBytes(256, (err, buf) => {
  if (err) throw err;
  console.log(`${buf.length} bytes of random data: ${buf.toString('hex')}`);
});
```

If the `callback` function is not provided, the random bytes are generated
synchronously and returned as a [`Buffer`](07-buffer.md). An error will be thrown if
there is a problem generating the bytes.

*ES module version:*

```js
// Synchronous
const {
  randomBytes,
} = await import('node:crypto');

const buf = randomBytes(256);
console.log(
  `${buf.length} bytes of random data: ${buf.toString('hex')}`);
```

*CommonJS version:*

```js
// Synchronous
const {
  randomBytes,
} = require('node:crypto');

const buf = randomBytes(256);
console.log(
  `${buf.length} bytes of random data: ${buf.toString('hex')}`);
```

The `crypto.randomBytes()` method will not complete until there is
sufficient entropy available.
This should normally never take longer than a few milliseconds. The only time
when generating the random bytes may conceivably block for a longer period of
time is right after boot, when the whole system is still low on entropy.

This API uses libuv's threadpool, which can have surprising and
negative performance implications for some applications; see the
[`UV_THREADPOOL_SIZE`](13-cli.md#uv_threadpool_sizesize) documentation for more information.

The asynchronous version of `crypto.randomBytes()` is carried out in a single
threadpool request. To minimize threadpool task length variation, partition
large `randomBytes` requests when doing so as part of fulfilling a client
request.

### `crypto.randomFill(buffer[, offset][, size], callback)`

Added in: v7.10.0, v6.13.0

**History**

| Version | Changes |
| --- | --- |
| v18.0.0 | Passing an invalid callback to the `callback` argument now throws `ERR_INVALID_ARG_TYPE` instead of `ERR_INVALID_CALLBACK`. |
| v9.0.0 | The `buffer` argument may be any `TypedArray` or `DataView`. |

- `buffer` [`<ArrayBuffer>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/ArrayBuffer) | [`<Buffer>`](07-buffer.md#class-buffer) | [`<TypedArray>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/TypedArray) | [`<DataView>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/DataView) Must be supplied. The
  size of the provided `buffer` must not be larger than `2**31 - 1`.
- `offset` [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) The start position, in elements for a `TypedArray` and in
  bytes for an `ArrayBuffer` or `DataView`. **Default:** `0`
- `size` [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) The amount to fill, in the same units as `offset`.
  **Default:** `buffer.length - offset` for a `TypedArray`, or
  `buffer.byteLength - offset` for an `ArrayBuffer` or `DataView`. The `size`
  must not be larger than `2**31 - 1`.
- `callback` [`<Function>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Function) `function(err, buf) {}`.

This function is similar to [`crypto.randomBytes()`](#cryptorandombytessize-callback) but requires the first
argument to be a [`Buffer`](07-buffer.md) that will be filled. It also
requires that a callback is passed in.

If the `callback` function is not provided, an error will be thrown.

*ES module version:*

```js
import { Buffer } from 'node:buffer';
const { randomFill } = await import('node:crypto');

const buf = Buffer.alloc(10);
randomFill(buf, (err, buf) => {
  if (err) throw err;
  console.log(buf.toString('hex'));
});

randomFill(buf, 5, (err, buf) => {
  if (err) throw err;
  console.log(buf.toString('hex'));
});

// The above is equivalent to the following:
randomFill(buf, 5, 5, (err, buf) => {
  if (err) throw err;
  console.log(buf.toString('hex'));
});
```

*CommonJS version:*

```js
const { randomFill } = require('node:crypto');
const { Buffer } = require('node:buffer');

const buf = Buffer.alloc(10);
randomFill(buf, (err, buf) => {
  if (err) throw err;
  console.log(buf.toString('hex'));
});

randomFill(buf, 5, (err, buf) => {
  if (err) throw err;
  console.log(buf.toString('hex'));
});

// The above is equivalent to the following:
randomFill(buf, 5, 5, (err, buf) => {
  if (err) throw err;
  console.log(buf.toString('hex'));
});
```

Any `ArrayBuffer`, `TypedArray`, or `DataView` instance may be passed as
`buffer`.

While this includes instances of `Float32Array` and `Float64Array`, this
function should not be used to generate random floating-point numbers. The
result may contain `+Infinity`, `-Infinity`, and `NaN`, and even if the array
contains finite numbers only, they are not drawn from a uniform random
distribution and have no meaningful lower or upper bounds.

*ES module version:*

```js
import { Buffer } from 'node:buffer';
const { randomFill } = await import('node:crypto');

const a = new Uint32Array(10);
randomFill(a, (err, buf) => {
  if (err) throw err;
  console.log(Buffer.from(buf.buffer, buf.byteOffset, buf.byteLength)
    .toString('hex'));
});

const b = new DataView(new ArrayBuffer(10));
randomFill(b, (err, buf) => {
  if (err) throw err;
  console.log(Buffer.from(buf.buffer, buf.byteOffset, buf.byteLength)
    .toString('hex'));
});

const c = new ArrayBuffer(10);
randomFill(c, (err, buf) => {
  if (err) throw err;
  console.log(Buffer.from(buf).toString('hex'));
});
```

*CommonJS version:*

```js
const { randomFill } = require('node:crypto');
const { Buffer } = require('node:buffer');

const a = new Uint32Array(10);
randomFill(a, (err, buf) => {
  if (err) throw err;
  console.log(Buffer.from(buf.buffer, buf.byteOffset, buf.byteLength)
    .toString('hex'));
});

const b = new DataView(new ArrayBuffer(10));
randomFill(b, (err, buf) => {
  if (err) throw err;
  console.log(Buffer.from(buf.buffer, buf.byteOffset, buf.byteLength)
    .toString('hex'));
});

const c = new ArrayBuffer(10);
randomFill(c, (err, buf) => {
  if (err) throw err;
  console.log(Buffer.from(buf).toString('hex'));
});
```

This API uses libuv's threadpool, which can have surprising and
negative performance implications for some applications; see the
[`UV_THREADPOOL_SIZE`](13-cli.md#uv_threadpool_sizesize) documentation for more information.

The asynchronous version of `crypto.randomFill()` is carried out in a single
threadpool request. To minimize threadpool task length variation, partition
large `randomFill` requests when doing so as part of fulfilling a client
request.

### `crypto.randomFillSync(buffer[, offset][, size])`

Added in: v7.10.0, v6.13.0

**History**

| Version | Changes |
| --- | --- |
| v9.0.0 | The `buffer` argument may be any `TypedArray` or `DataView`. |

- `buffer` [`<ArrayBuffer>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/ArrayBuffer) | [`<Buffer>`](07-buffer.md#class-buffer) | [`<TypedArray>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/TypedArray) | [`<DataView>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/DataView) Must be supplied. The
  size of the provided `buffer` must not be larger than `2**31 - 1`.
- `offset` [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) The start position, in elements for a `TypedArray` and in
  bytes for an `ArrayBuffer` or `DataView`. **Default:** `0`
- `size` [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) The amount to fill, in the same units as `offset`.
  **Default:** `buffer.length - offset` for a `TypedArray`, or
  `buffer.byteLength - offset` for an `ArrayBuffer` or `DataView`. The `size`
  must not be larger than `2**31 - 1`.
- Returns: [`<ArrayBuffer>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/ArrayBuffer) | [`<Buffer>`](07-buffer.md#class-buffer) | [`<TypedArray>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/TypedArray) | [`<DataView>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/DataView) The object passed as `buffer` argument.

Synchronous version of [`crypto.randomFill()`](#cryptorandomfillbuffer-offset-size-callback).

*ES module version:*

```js
import { Buffer } from 'node:buffer';
const { randomFillSync } = await import('node:crypto');

const buf = Buffer.alloc(10);
console.log(randomFillSync(buf).toString('hex'));

randomFillSync(buf, 5);
console.log(buf.toString('hex'));

// The above is equivalent to the following:
randomFillSync(buf, 5, 5);
console.log(buf.toString('hex'));
```

*CommonJS version:*

```js
const { randomFillSync } = require('node:crypto');
const { Buffer } = require('node:buffer');

const buf = Buffer.alloc(10);
console.log(randomFillSync(buf).toString('hex'));

randomFillSync(buf, 5);
console.log(buf.toString('hex'));

// The above is equivalent to the following:
randomFillSync(buf, 5, 5);
console.log(buf.toString('hex'));
```

Any `ArrayBuffer`, `TypedArray` or `DataView` instance may be passed as
`buffer`.

*ES module version:*

```js
import { Buffer } from 'node:buffer';
const { randomFillSync } = await import('node:crypto');

const a = new Uint32Array(10);
console.log(Buffer.from(randomFillSync(a).buffer,
                        a.byteOffset, a.byteLength).toString('hex'));

const b = new DataView(new ArrayBuffer(10));
console.log(Buffer.from(randomFillSync(b).buffer,
                        b.byteOffset, b.byteLength).toString('hex'));

const c = new ArrayBuffer(10);
console.log(Buffer.from(randomFillSync(c)).toString('hex'));
```

*CommonJS version:*

```js
const { randomFillSync } = require('node:crypto');
const { Buffer } = require('node:buffer');

const a = new Uint32Array(10);
console.log(Buffer.from(randomFillSync(a).buffer,
                        a.byteOffset, a.byteLength).toString('hex'));

const b = new DataView(new ArrayBuffer(10));
console.log(Buffer.from(randomFillSync(b).buffer,
                        b.byteOffset, b.byteLength).toString('hex'));

const c = new ArrayBuffer(10);
console.log(Buffer.from(randomFillSync(c)).toString('hex'));
```

### `crypto.randomInt([min, ]max[, callback])`

Added in: v14.10.0, v12.19.0

**History**

| Version | Changes |
| --- | --- |
| v18.0.0 | Passing an invalid callback to the `callback` argument now throws `ERR_INVALID_ARG_TYPE` instead of `ERR_INVALID_CALLBACK`. |

- `min` [`<integer>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) Start of random range (inclusive). **Default:** `0`.
- `max` [`<integer>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) End of random range (exclusive).
- `callback` [`<Function>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Function) `function(err, n) {}`.

Return a random integer `n` such that `min <= n < max`. This
implementation avoids [modulo bias](https://en.wikipedia.org/wiki/Fisher%E2%80%93Yates_shuffle#Modulo_bias).

The range (`max - min`) must be less than 248. `min` and `max` must
be [safe integers](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Number/isSafeInteger).

If the `callback` function is not provided, the random integer is
generated synchronously.

*ES module version:*

```js
// Asynchronous
const {
  randomInt,
} = await import('node:crypto');

randomInt(3, (err, n) => {
  if (err) throw err;
  console.log(`Random number chosen from (0, 1, 2): ${n}`);
});
```

*CommonJS version:*

```js
// Asynchronous
const {
  randomInt,
} = require('node:crypto');

randomInt(3, (err, n) => {
  if (err) throw err;
  console.log(`Random number chosen from (0, 1, 2): ${n}`);
});
```

*ES module version:*

```js
// Synchronous
const {
  randomInt,
} = await import('node:crypto');

const n = randomInt(3);
console.log(`Random number chosen from (0, 1, 2): ${n}`);
```

*CommonJS version:*

```js
// Synchronous
const {
  randomInt,
} = require('node:crypto');

const n = randomInt(3);
console.log(`Random number chosen from (0, 1, 2): ${n}`);
```

*ES module version:*

```js
// With `min` argument
const {
  randomInt,
} = await import('node:crypto');

const n = randomInt(1, 7);
console.log(`The dice rolled: ${n}`);
```

*CommonJS version:*

```js
// With `min` argument
const {
  randomInt,
} = require('node:crypto');

const n = randomInt(1, 7);
console.log(`The dice rolled: ${n}`);
```

### `crypto.randomUUID([options])`

Added in: v15.6.0, v14.17.0

- `options` [`<Object>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Object)
  - `disableEntropyCache` [`<boolean>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#boolean_type) By default, to improve performance,
    Node.js generates and caches enough
    random data to generate up to 128 random UUIDs. To generate a UUID
    without using the cache, set `disableEntropyCache` to `true`.
    **Default:** `false`.
- Returns: [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type)

Generates a random [RFC 4122](https://www.rfc-editor.org/rfc/rfc4122.txt) version 4 UUID. The UUID is generated using a
cryptographic pseudorandom number generator.

### `crypto.randomUUIDv7([options])`

Added in: v26.1.0

- `options` [`<Object>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Object)
  - `disableEntropyCache` [`<boolean>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#boolean_type) By default, to improve performance,
    Node.js generates and caches enough
    random data to generate up to 128 random UUIDs. To generate a UUID
    without using the cache, set `disableEntropyCache` to `true`.
    **Default:** `false`.
- Returns: [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type)

Generates a random [RFC 9562](https://www.rfc-editor.org/rfc/rfc9562.txt) version 7 UUID. The UUID contains a millisecond
precision Unix timestamp in the most significant 48 bits, followed by
cryptographically secure random bits for the remaining fields, making it
suitable for use as a database key with time-based sorting. The embedded
timestamp relies on a non-monotonic clock and is not guaranteed to be strictly
increasing.

### `crypto.scrypt(password, salt, keylen[, options], callback)`

Added in: v10.5.0

**History**

| Version | Changes |
| --- | --- |
| v18.0.0 | Passing an invalid callback to the `callback` argument now throws `ERR_INVALID_ARG_TYPE` instead of `ERR_INVALID_CALLBACK`. |
| v15.0.0 | The password and salt arguments can also be ArrayBuffer instances. |
| v12.8.0, v10.17.0 | The `maxmem` value can now be any safe integer. |
| v10.9.0 | The `cost`, `blockSize` and `parallelization` option names have been added. |

- `password` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) | [`<ArrayBuffer>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/ArrayBuffer) | [`<Buffer>`](07-buffer.md#class-buffer) | [`<TypedArray>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/TypedArray) | [`<DataView>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/DataView)
- `salt` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) | [`<ArrayBuffer>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/ArrayBuffer) | [`<Buffer>`](07-buffer.md#class-buffer) | [`<TypedArray>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/TypedArray) | [`<DataView>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/DataView)
- `keylen` [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type)
- `options` [`<Object>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Object)
  - `cost` [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) CPU/memory cost parameter. Must be a power of two greater
    than one. **Default:** `16384`.
  - `blockSize` [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) Block size parameter. **Default:** `8`.
  - `parallelization` [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) Parallelization parameter. **Default:** `1`.
  - `N` [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) Alias for `cost`. Only one of both may be specified.
  - `r` [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) Alias for `blockSize`. Only one of both may be specified.
  - `p` [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) Alias for `parallelization`. Only one of both may be specified.
  - `maxmem` [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) Memory upper bound. It is an error when (approximately) `128 * N * r > maxmem`. **Default:** `32 * 1024 * 1024`.
- `callback` [`<Function>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Function)
  - `err` [`<Error>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Error)
  - `derivedKey` [`<Buffer>`](07-buffer.md#class-buffer)

Provides an asynchronous [scrypt](https://en.wikipedia.org/wiki/Scrypt) implementation. Scrypt is a password-based
key derivation function that is designed to be expensive computationally and
memory-wise in order to make brute-force attacks unrewarding.

The `salt` should be as unique as possible. It is recommended that a salt is
random and at least 16 bytes long. See [NIST SP 800-132](https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-132.pdf) for details.

When passing strings for `password` or `salt`, please consider
[caveats when using strings as inputs to cryptographic APIs](#using-strings-as-inputs-to-cryptographic-apis).

The `callback` function is called with two arguments: `err` and `derivedKey`.
`err` is an exception object when key derivation fails, otherwise `err` is
`null`. `derivedKey` is passed to the callback as a [`Buffer`](07-buffer.md).

An exception is thrown when any of the input arguments specify invalid values
or types.

*ES module version:*

```js
const {
  scrypt,
} = await import('node:crypto');

// Using the factory defaults.
scrypt('password', 'salt', 64, (err, derivedKey) => {
  if (err) throw err;
  console.log(derivedKey.toString('hex'));  // '3745e48...08d59ae'
});
// Using a custom N parameter. Must be a power of two.
scrypt('password', 'salt', 64, { N: 1024 }, (err, derivedKey) => {
  if (err) throw err;
  console.log(derivedKey.toString('hex'));  // '3745e48...aa39b34'
});
```

*CommonJS version:*

```js
const {
  scrypt,
} = require('node:crypto');

// Using the factory defaults.
scrypt('password', 'salt', 64, (err, derivedKey) => {
  if (err) throw err;
  console.log(derivedKey.toString('hex'));  // '3745e48...08d59ae'
});
// Using a custom N parameter. Must be a power of two.
scrypt('password', 'salt', 64, { N: 1024 }, (err, derivedKey) => {
  if (err) throw err;
  console.log(derivedKey.toString('hex'));  // '3745e48...aa39b34'
});
```

### `crypto.scryptSync(password, salt, keylen[, options])`

Added in: v10.5.0

**History**

| Version | Changes |
| --- | --- |
| v12.8.0, v10.17.0 | The `maxmem` value can now be any safe integer. |
| v10.9.0 | The `cost`, `blockSize` and `parallelization` option names have been added. |

- `password` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) | [`<Buffer>`](07-buffer.md#class-buffer) | [`<TypedArray>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/TypedArray) | [`<DataView>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/DataView)
- `salt` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) | [`<Buffer>`](07-buffer.md#class-buffer) | [`<TypedArray>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/TypedArray) | [`<DataView>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/DataView)
- `keylen` [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type)
- `options` [`<Object>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Object)
  - `cost` [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) CPU/memory cost parameter. Must be a power of two greater
    than one. **Default:** `16384`.
  - `blockSize` [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) Block size parameter. **Default:** `8`.
  - `parallelization` [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) Parallelization parameter. **Default:** `1`.
  - `N` [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) Alias for `cost`. Only one of both may be specified.
  - `r` [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) Alias for `blockSize`. Only one of both may be specified.
  - `p` [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) Alias for `parallelization`. Only one of both may be specified.
  - `maxmem` [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) Memory upper bound. It is an error when (approximately) `128 * N * r > maxmem`. **Default:** `32 * 1024 * 1024`.
- Returns: [`<Buffer>`](07-buffer.md#class-buffer)

Provides a synchronous [scrypt](https://en.wikipedia.org/wiki/Scrypt) implementation. Scrypt is a password-based
key derivation function that is designed to be expensive computationally and
memory-wise in order to make brute-force attacks unrewarding.

The `salt` should be as unique as possible. It is recommended that a salt is
random and at least 16 bytes long. See [NIST SP 800-132](https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-132.pdf) for details.

When passing strings for `password` or `salt`, please consider
[caveats when using strings as inputs to cryptographic APIs](#using-strings-as-inputs-to-cryptographic-apis).

An exception is thrown when key derivation fails, otherwise the derived key is
returned as a [`Buffer`](07-buffer.md).

An exception is thrown when any of the input arguments specify invalid values
or types.

*ES module version:*

```js
const {
  scryptSync,
} = await import('node:crypto');
// Using the factory defaults.

const key1 = scryptSync('password', 'salt', 64);
console.log(key1.toString('hex'));  // '3745e48...08d59ae'
// Using a custom N parameter. Must be a power of two.
const key2 = scryptSync('password', 'salt', 64, { N: 1024 });
console.log(key2.toString('hex'));  // '3745e48...aa39b34'
```

*CommonJS version:*

```js
const {
  scryptSync,
} = require('node:crypto');
// Using the factory defaults.

const key1 = scryptSync('password', 'salt', 64);
console.log(key1.toString('hex'));  // '3745e48...08d59ae'
// Using a custom N parameter. Must be a power of two.
const key2 = scryptSync('password', 'salt', 64, { N: 1024 });
console.log(key2.toString('hex'));  // '3745e48...aa39b34'
```

### `crypto.secureHeapUsed()`

Added in: v15.6.0

- Returns: [`<Object>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Object)
  - `total` [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) The total allocated secure heap size as specified
    using the `--secure-heap=n` command-line flag.
  - `min` [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) The minimum allocation from the secure heap as
    specified using the `--secure-heap-min` command-line flag.
  - `used` [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) The total number of bytes currently allocated from
    the secure heap.
  - `utilization` [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) The calculated ratio of `used` to `total`
    allocated bytes.

### `crypto.setEngine(engine[, flags])`

Added in: v0.11.11

**History**

| Version | Changes |
| --- | --- |
| v22.4.0, v20.16.0 | Custom engine support in OpenSSL 3 is deprecated. |

Stability: 0 - Deprecated

- `engine` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type)
- `flags` [`<crypto.constants>`](15-crypto.md#cryptoconstants) **Default:** `crypto.constants.ENGINE_METHOD_ALL`

Load and set the `engine` for some or all OpenSSL functions (selected by flags).
Use of this API is deprecated because custom engine support has been deprecated
since OpenSSL 3.

`engine` could be either an id or a path to the engine's shared library.

The optional `flags` argument uses `ENGINE_METHOD_ALL` by default. The `flags`
is a bit field taking one of or a mix of the following flags (defined in
`crypto.constants`):

- `crypto.constants.ENGINE_METHOD_RSA`
- `crypto.constants.ENGINE_METHOD_DSA`
- `crypto.constants.ENGINE_METHOD_DH`
- `crypto.constants.ENGINE_METHOD_RAND`
- `crypto.constants.ENGINE_METHOD_EC`
- `crypto.constants.ENGINE_METHOD_CIPHERS`
- `crypto.constants.ENGINE_METHOD_DIGESTS`
- `crypto.constants.ENGINE_METHOD_PKEY_METHS`
- `crypto.constants.ENGINE_METHOD_PKEY_ASN1_METHS`
- `crypto.constants.ENGINE_METHOD_ALL`
- `crypto.constants.ENGINE_METHOD_NONE`

### `crypto.setFips(bool)`

Added in: v10.0.0

- `bool` [`<boolean>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#boolean_type) `true` to enable FIPS mode, `false` to disable it.

Changes [FIPS mode](#fips-mode). With OpenSSL 3, this only adds or removes `fips=yes` in
the default property query. It does not install, load, initialize, or validate
a FIPS provider. For a usable FIPS configuration, install the provider and
configure OpenSSL to load it when Node.js starts, as described in [FIPS
mode](#fips-mode).

If no loaded provider supplies a requested cryptographic implementation
matching `fips=yes`, the call can still succeed and `crypto.getFips()` can still
return `1`, but fetching that implementation fails. Affected `node:crypto`
operations typically fail with `ERR_OSSL_EVP_UNSUPPORTED`. Operations that do
not require a new fetch, including those using previously fetched
implementations or initialized operation contexts, may still succeed. Call this
method during application initialization, before application code uses other
OpenSSL-backed APIs.

This method only affects subsequent algorithm fetches. Node.js initializes some
OpenSSL state before application code runs. When the property query must be
active from process startup, set `default_properties = fips=yes` in the OpenSSL
configuration or use [`--enable-fips`](13-cli.md#--enable-fips) or [`--force-fips`](13-cli.md#--force-fips). The command-line
flags additionally require a configured provider named `fips` to initialize and
pass its self-test; Node.js fails to start otherwise.

Throws an error if OpenSSL cannot change the state. FIPS mode cannot be
disabled when Node.js was started with `--force-fips`. With OpenSSL 1.1.1,
enabling FIPS mode requires a FIPS-capable OpenSSL build.

### `crypto.sign(algorithm, data, key[, callback])`

Added in: v12.0.0

**History**

| Version | Changes |
| --- | --- |
| v26.0.0 | Add support for Ed25519 context parameter. |
| v24.8.0 | Add support for ML-DSA, Ed448, and SLH-DSA context parameter. |
| v24.8.0 | Add support for SLH-DSA signing. |
| v24.6.0 | Add support for ML-DSA signing. |
| v18.0.0 | Passing an invalid callback to the `callback` argument now throws `ERR_INVALID_ARG_TYPE` instead of `ERR_INVALID_CALLBACK`. |
| v15.12.0 | Optional callback argument added. |
| v13.2.0, v12.16.0 | This function now supports IEEE-P1363 DSA and ECDSA signatures. |

- `algorithm` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) | [`<null>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#null_type) | [`<undefined>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#undefined_type)
- `data` [`<ArrayBuffer>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/ArrayBuffer) | [`<Buffer>`](07-buffer.md#class-buffer) | [`<SharedArrayBuffer>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/SharedArrayBuffer) | [`<TypedArray>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/TypedArray) | [`<DataView>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/DataView) | [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type)
- `key` [`<Object>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Object) | [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) | [`<ArrayBuffer>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/ArrayBuffer) | [`<Buffer>`](07-buffer.md#class-buffer) | [`<TypedArray>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/TypedArray) | [`<DataView>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/DataView) | [`<KeyObject>`](15-crypto.md#class-keyobject) | [`<CryptoKey>`](65-webcrypto.md#class-cryptokey) | [`<URL>`](59-url.md#the-whatwg-url-api)
- `callback` [`<Function>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Function)
  - `err` [`<Error>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Error)
  - `signature` [`<Buffer>`](07-buffer.md#class-buffer)
- Returns: [`<Buffer>`](07-buffer.md#class-buffer) if the `callback` function is not provided.

Calculates and returns the signature for `data` using the given private key and
algorithm. If `algorithm` is `null` or `undefined`, then the algorithm is
dependent upon the key type.

`algorithm` is required to be `null` or `undefined` for Ed25519, Ed448, and
ML-DSA.

[`crypto.getHashes()`](#cryptogethashes) lists algorithms available to the hashing APIs, but
the key type and signature scheme determine whether a listed digest can be
used for signing.

If `key` is not a [`KeyObject`](#class-keyobject), this function behaves as if `key` had been
passed to [`crypto.createPrivateKey()`](#cryptocreateprivatekeykey). When `key` is a string, `ArrayBuffer`,
[`Buffer`](07-buffer.md), `TypedArray`, or `DataView`, it must contain PEM-encoded key
material. If it is an object, the following additional properties can be
passed:

- `dsaEncoding` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) For DSA and ECDSA, this option specifies the
  format of the generated signature. It can be one of the following:

  - `'der'` (default): DER-encoded ASN.1 signature structure encoding `(r, s)`.
  - `'ieee-p1363'`: Signature format `r || s` as proposed in IEEE-P1363.
- `padding` [`<integer>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) Optional padding value for RSA, one of the following:

  - `crypto.constants.RSA_PKCS1_PADDING` (default)
  - `crypto.constants.RSA_PKCS1_PSS_PADDING`

  `RSA_PKCS1_PSS_PADDING` will use MGF1 with the same hash function
  used to sign the message as specified in section 3.1 of [RFC 4055](https://www.rfc-editor.org/rfc/rfc4055.txt).
- `saltLength` [`<integer>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) Salt length for when padding is `RSA_PKCS1_PSS_PADDING`. The special value
  `crypto.constants.RSA_PSS_SALTLEN_DIGEST` sets the salt length to the digest
  size, `crypto.constants.RSA_PSS_SALTLEN_MAX_SIGN` (default) sets it to the
  maximum permissible value.
- `context` [`<ArrayBuffer>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/ArrayBuffer) | [`<Buffer>`](07-buffer.md#class-buffer) | [`<TypedArray>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/TypedArray) | [`<DataView>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/DataView) For Ed25519[3](#user-content-fn-openssl32)
  (using Ed25519ctx from [RFC 8032](https://www.rfc-editor.org/rfc/rfc8032.txt)), Ed448, ML-DSA, and SLH-DSA,
  this option specifies the optional context to differentiate signatures
  generated for different purposes with the same key.

If the `callback` function is provided this function uses libuv's threadpool.

### `crypto.subtle`

Added in: v17.4.0

- Type: [`<SubtleCrypto>`](65-webcrypto.md#class-subtlecrypto)

A convenient alias for [`crypto.webcrypto.subtle`](65-webcrypto.md#class-subtlecrypto).

### `crypto.timingSafeEqual(a, b)`

Added in: v6.6.0

**History**

| Version | Changes |
| --- | --- |
| v15.0.0 | The a and b arguments can also be ArrayBuffer. |

- `a` [`<ArrayBuffer>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/ArrayBuffer) | [`<Buffer>`](07-buffer.md#class-buffer) | [`<TypedArray>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/TypedArray) | [`<DataView>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/DataView)
- `b` [`<ArrayBuffer>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/ArrayBuffer) | [`<Buffer>`](07-buffer.md#class-buffer) | [`<TypedArray>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/TypedArray) | [`<DataView>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/DataView)
- Returns: [`<boolean>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#boolean_type)

This function compares the underlying bytes that represent the given
`ArrayBuffer`, `TypedArray`, or `DataView` instances using a constant-time
algorithm.

This function does not leak timing information that
would allow an attacker to guess one of the values. This is suitable for
comparing HMAC digests or secret values like authentication cookies or
[capability urls](https://www.w3.org/TR/capability-urls/).

`a` and `b` must both be `Buffer`s, `TypedArray`s, or `DataView`s, and they
must have the same byte length. An error is thrown if `a` and `b` have
different byte lengths.

If at least one of `a` and `b` is a `TypedArray` with more than one byte per
entry, such as `Uint16Array`, the result will be computed using the platform
byte order.

**When both of the inputs are `Float32Array`s or
`Float64Array`s, this function might return unexpected results due to IEEE 754
encoding of floating-point numbers. In particular, neither `x === y` nor
`Object.is(x, y)` implies that the byte representations of two floating-point
numbers `x` and `y` are equal.**

Use of `crypto.timingSafeEqual` does not guarantee that the *surrounding* code
is timing-safe. Care should be taken to ensure that the surrounding code does
not introduce timing vulnerabilities.

### `crypto.verify(algorithm, data, key, signature[, callback])`

Added in: v12.0.0

**History**

| Version | Changes |
| --- | --- |
| v26.0.0 | Add support for Ed25519 context parameter. |
| v24.8.0 | Add support for ML-DSA, Ed448, and SLH-DSA context parameter. |
| v24.8.0 | Add support for SLH-DSA signature verification. |
| v24.6.0 | Add support for ML-DSA signature verification. |
| v18.0.0 | Passing an invalid callback to the `callback` argument now throws `ERR_INVALID_ARG_TYPE` instead of `ERR_INVALID_CALLBACK`. |
| v15.12.0 | Optional callback argument added. |
| v15.0.0 | The data, key, and signature arguments can also be ArrayBuffer. |
| v13.2.0, v12.16.0 | This function now supports IEEE-P1363 DSA and ECDSA signatures. |

- `algorithm` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) | [`<null>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#null_type) | [`<undefined>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#undefined_type)
- `data` [`<ArrayBuffer>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/ArrayBuffer) | [`<Buffer>`](07-buffer.md#class-buffer) | [`<SharedArrayBuffer>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/SharedArrayBuffer) | [`<TypedArray>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/TypedArray) | [`<DataView>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/DataView) | [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type)
- `key` [`<Object>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Object) | [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) | [`<ArrayBuffer>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/ArrayBuffer) | [`<Buffer>`](07-buffer.md#class-buffer) | [`<TypedArray>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/TypedArray) | [`<DataView>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/DataView) | [`<KeyObject>`](15-crypto.md#class-keyobject) | [`<CryptoKey>`](65-webcrypto.md#class-cryptokey)
- `signature` [`<ArrayBuffer>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/ArrayBuffer) | [`<Buffer>`](07-buffer.md#class-buffer) | [`<SharedArrayBuffer>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/SharedArrayBuffer) | [`<TypedArray>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/TypedArray) | [`<DataView>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/DataView)
- `callback` [`<Function>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Function)
  - `err` [`<Error>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Error)
  - `result` [`<boolean>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#boolean_type)
- Returns: [`<boolean>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#boolean_type) `true` or `false` depending on the validity of the
  signature for the data and public key if the `callback` function is not
  provided.

Verifies the given signature for `data` using the given key and algorithm. If
`algorithm` is `null` or `undefined`, then the algorithm is dependent upon the
key type.

`algorithm` is required to be `null` or `undefined` for Ed25519, Ed448, and
ML-DSA.

[`crypto.getHashes()`](#cryptogethashes) lists algorithms available to the hashing APIs, but
the key type and signature scheme determine whether a listed digest can be
used for verification.

If `key` is not a [`KeyObject`](#class-keyobject), this function behaves as if `key` had been
passed to [`crypto.createPublicKey()`](#cryptocreatepublickeykey). When `key` is a string, `ArrayBuffer`,
[`Buffer`](07-buffer.md), `TypedArray`, or `DataView`, it must contain PEM-encoded key
material. If it is an object, the following additional properties can be
passed:

- `dsaEncoding` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) For DSA and ECDSA, this option specifies the
  format of the signature. It can be one of the following:

  - `'der'` (default): DER-encoded ASN.1 signature structure encoding `(r, s)`.
  - `'ieee-p1363'`: Signature format `r || s` as proposed in IEEE-P1363.
- `padding` [`<integer>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) Optional padding value for RSA, one of the following:

  - `crypto.constants.RSA_PKCS1_PADDING` (default)
  - `crypto.constants.RSA_PKCS1_PSS_PADDING`

  `RSA_PKCS1_PSS_PADDING` will use MGF1 with the same hash function
  used to sign the message as specified in section 3.1 of [RFC 4055](https://www.rfc-editor.org/rfc/rfc4055.txt).
- `saltLength` [`<integer>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) Salt length for when padding is `RSA_PKCS1_PSS_PADDING`. The special value
  `crypto.constants.RSA_PSS_SALTLEN_DIGEST` sets the salt length to the digest
  size, `crypto.constants.RSA_PSS_SALTLEN_MAX_SIGN` (default) sets it to the
  maximum permissible value.
- `context` [`<ArrayBuffer>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/ArrayBuffer) | [`<Buffer>`](07-buffer.md#class-buffer) | [`<TypedArray>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/TypedArray) | [`<DataView>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/DataView) For Ed25519[3](#user-content-fn-openssl32)
  (using Ed25519ctx from [RFC 8032](https://www.rfc-editor.org/rfc/rfc8032.txt)), Ed448, ML-DSA, and SLH-DSA,
  this option specifies the optional context to differentiate signatures
  generated for different purposes with the same key.

The `signature` argument is the previously calculated signature for the `data`.

Because public keys can be derived from private keys, a private key or a public
key may be passed for `key`.

If the `callback` function is provided this function uses libuv's threadpool.

### `crypto.webcrypto`

Added in: v15.0.0

Type: [`<Crypto>`](65-webcrypto.md#class-crypto) An implementation of the Web Crypto API standard.

See the [Web Crypto API documentation](65-webcrypto.md) for details.

## Notes

### Using strings as inputs to cryptographic APIs

For historical reasons, many cryptographic APIs provided by Node.js accept
strings as inputs where the underlying cryptographic algorithm works on byte
sequences. These instances include plaintexts, ciphertexts, symmetric keys,
initialization vectors, passphrases, salts, authentication tags,
and additional authenticated data.

When passing strings to cryptographic APIs, consider the following factors.

- Not all byte sequences are valid UTF-8 strings. Therefore, when a byte
  sequence of length `n` is derived from a string, its entropy is generally
  lower than the entropy of a random or pseudorandom `n` byte sequence.
  For example, no UTF-8 string will result in the byte sequence `c0 af`. Secret
  keys should almost exclusively be random or pseudorandom byte sequences.
- Similarly, when converting random or pseudorandom byte sequences to UTF-8
  strings, subsequences that do not represent valid code points may be replaced
  by the Unicode replacement character (`U+FFFD`). The byte representation of
  the resulting Unicode string may, therefore, not be equal to the byte sequence
  that the string was created from.

  ```js
  const original = [0xc0, 0xaf];
  const bytesAsString = Buffer.from(original).toString('utf8');
  const stringAsBytes = Buffer.from(bytesAsString, 'utf8');
  console.log(stringAsBytes);
  // Prints '<Buffer ef bf bd ef bf bd>'.
  ```

  The outputs of ciphers, hash functions, signature algorithms, and key
  derivation functions are pseudorandom byte sequences and should not be
  used as Unicode strings.
- When strings are obtained from user input, some Unicode characters can be
  represented in multiple equivalent ways that result in different byte
  sequences. For example, when passing a user passphrase to a key derivation
  function, such as PBKDF2 or scrypt, the result of the key derivation function
  depends on whether the string uses composed or decomposed characters. Node.js
  does not normalize character representations. Developers should consider using
  [`String.prototype.normalize()`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/String/normalize) on user inputs before passing them to
  cryptographic APIs.

### Legacy streams API (prior to Node.js 0.10)

The Crypto module was added to Node.js before there was the concept of a
unified Stream API, and before there were [`Buffer`](07-buffer.md) objects for handling
binary data. As such, many `crypto` classes have methods not
typically found on other Node.js classes that implement the [streams](51-stream.md)
API (e.g. `update()`, `final()`, or `digest()`). Also, many methods accepted
and returned `'latin1'` encoded strings by default rather than `Buffer`s. This
default was changed in Node.js 0.9.3 to use [`Buffer`](07-buffer.md) objects by default
instead.

### Support for weak or compromised algorithms

The `node:crypto` module still supports some algorithms which are already
compromised and are not recommended for use. The API also allows
the use of ciphers and hashes with a small key size that are too weak for safe
use.

Users should take full responsibility for selecting the crypto
algorithm and key size according to their security requirements.

Based on the recommendations of [NIST SP 800-131A](https://nvlpubs.nist.gov/nistpubs/SpecialPublications/NIST.SP.800-131Ar2.pdf):

- MD5 and SHA-1 are no longer acceptable where collision resistance is
  required such as digital signatures.
- The key used with RSA, DSA, and DH algorithms is recommended to have
  at least 2048 bits and that of the curve of ECDSA and ECDH at least
  224 bits, to be safe to use for several years.
- The DH groups of `modp1`, `modp2` and `modp5` have a key size
  smaller than 2048 bits and are not recommended.

See the reference for other recommendations and details.

Some algorithms that have known weaknesses and are of little relevance in
practice are only available through the [legacy provider](13-cli.md#--openssl-legacy-provider), which is not
enabled by default.

### CCM mode

CCM is one of the supported [AEAD algorithms](https://en.wikipedia.org/wiki/Authenticated_encryption). Applications which use this
mode must adhere to certain restrictions when using the cipher API:

- The authentication tag length must be specified during cipher creation by
  setting the `authTagLength` option and must be one of 4, 6, 8, 10, 12, 14 or
  16 bytes.
- The length of the initialization vector (nonce) `N` must be between 7 and 13
  bytes (`7 ≤ N ≤ 13`).
- The length of the plaintext is limited to `2 ** (8 * (15 - N))` bytes.
- When decrypting, the authentication tag must be set via `setAuthTag()` before
  calling `update()`.
  Otherwise, decryption will fail and `final()` will throw an error in
  compliance with section 2.6 of [RFC 3610](https://www.rfc-editor.org/rfc/rfc3610.txt).
- Using stream methods such as `write(data)`, `end(data)` or `pipe()` in CCM
  mode might fail as CCM cannot handle more than one chunk of data per instance.
- When passing additional authenticated data (AAD), the length of the actual
  message in bytes must be passed to `setAAD()` via the `plaintextLength`
  option.
  Many crypto libraries include the authentication tag in the ciphertext,
  which means that they produce ciphertexts of the length
  `plaintextLength + authTagLength`. Node.js does not include the authentication
  tag, so the ciphertext length is always `plaintextLength`.
  This is not necessary if no AAD is used.
- As CCM processes the whole message at once, `update()` must be called exactly
  once.
- Even though calling `update()` is sufficient to encrypt/decrypt the message,
  applications *must* call `final()` to compute or verify the
  authentication tag.

*ES module version:*

```js
import { Buffer } from 'node:buffer';
const {
  createCipheriv,
  createDecipheriv,
  randomBytes,
} = await import('node:crypto');

const key = 'keykeykeykeykeykeykeykey';
const nonce = randomBytes(12);

const aad = Buffer.from('0123456789', 'hex');

const cipher = createCipheriv('aes-192-ccm', key, nonce, {
  authTagLength: 16,
});
const plaintext = 'Hello world';
cipher.setAAD(aad, {
  plaintextLength: Buffer.byteLength(plaintext),
});
const ciphertext = cipher.update(plaintext, 'utf8');
cipher.final();
const tag = cipher.getAuthTag();

// Now transmit { ciphertext, nonce, tag }.

const decipher = createDecipheriv('aes-192-ccm', key, nonce, {
  authTagLength: 16,
});
decipher.setAuthTag(tag);
decipher.setAAD(aad, {
  plaintextLength: ciphertext.length,
});
const receivedPlaintext = decipher.update(ciphertext, null, 'utf8');

try {
  decipher.final();
} catch (err) {
  throw new Error('Authentication failed!', { cause: err });
}

console.log(receivedPlaintext);
```

*CommonJS version:*

```js
const { Buffer } = require('node:buffer');
const {
  createCipheriv,
  createDecipheriv,
  randomBytes,
} = require('node:crypto');

const key = 'keykeykeykeykeykeykeykey';
const nonce = randomBytes(12);

const aad = Buffer.from('0123456789', 'hex');

const cipher = createCipheriv('aes-192-ccm', key, nonce, {
  authTagLength: 16,
});
const plaintext = 'Hello world';
cipher.setAAD(aad, {
  plaintextLength: Buffer.byteLength(plaintext),
});
const ciphertext = cipher.update(plaintext, 'utf8');
cipher.final();
const tag = cipher.getAuthTag();

// Now transmit { ciphertext, nonce, tag }.

const decipher = createDecipheriv('aes-192-ccm', key, nonce, {
  authTagLength: 16,
});
decipher.setAuthTag(tag);
decipher.setAAD(aad, {
  plaintextLength: ciphertext.length,
});
const receivedPlaintext = decipher.update(ciphertext, null, 'utf8');

try {
  decipher.final();
} catch (err) {
  throw new Error('Authentication failed!', { cause: err });
}

console.log(receivedPlaintext);
```

### CBC-CTS mode

For CBC ciphertext stealing (CBC-CTS) ciphers, the `ctsMode` option to
[`crypto.createCipheriv()`](#cryptocreatecipherivalgorithm-key-iv-options) or [`crypto.createDecipheriv()`](#cryptocreatedecipherivalgorithm-key-iv-options) selects the
variant:

- `'CS1'` is the default. For block-aligned input, its output is the same as CBC
  mode.
- `'CS2'` is also the same as CBC for block-aligned input. For input with a
  partial final block, it swaps the final full and partial ciphertext blocks
  relative to CS1.
- `'CS3'` is the Kerberos 5 variant. It uses the CS2 ordering for a partial
  final block and swaps the final two ciphertext blocks even for block-aligned
  input.

Encryption and decryption must use the same variant. The option is available
only with CBC-CTS provider ciphers on OpenSSL 3.0 or later.

Applications which use this mode must adhere to these restrictions:

- The plaintext or ciphertext must be at least one block long.
- The ciphertext has the same length as the plaintext.
- `cipher.update()` or `decipher.update()` must be called exactly once with all
  input data. Stream methods such as `write(data)`, `end(data)`, or `pipe()` may
  fail because CBC-CTS does not accept multiple data updates.
- `cipher.final()` or `decipher.final()` must still be called to complete the
  operation.
- `crypto.getCipherInfo()` reports the base mode as `'cbc'`.

### XTS mode

XTS ciphers operate on independently tweakable data units. A `Cipheriv` or
`Decipheriv` instance represents one complete data unit, and its `iv` argument
provides the 16-byte tweak. Use a new instance with the appropriate positional
tweak for each different logical data unit.

Applications which use XTS mode must adhere to these restrictions:

- The plaintext or ciphertext must be at least one 16-byte block. Its length
  does not have to be a multiple of 16 bytes because XTS uses ciphertext
  stealing for a final partial block.
- The ciphertext has the same length as the plaintext.
- `cipher.update()` or `decipher.update()` must be called exactly once with all
  input data. Stream methods such as `write(data)`, `end(data)`, or `pipe()` may
  fail because XTS does not accept multiple data updates.
- `cipher.final()` or `decipher.final()` must still be called to complete the
  operation.

For `sm4-xts`, the `xtsStandard` option to [`crypto.createCipheriv()`](#cryptocreatecipherivalgorithm-key-iv-options) or
[`crypto.createDecipheriv()`](#cryptocreatedecipherivalgorithm-key-iv-options) selects either the default `'GB'` variant from
GB/T 17964-2021 or the `'IEEE'` variant from IEEE Std 1619-2007. Encryption and
decryption must use the same variant. The option is available only for
`sm4-xts`; it does not apply to AES-XTS ciphers. OpenSSL's default provider
supports `sm4-xts` in OpenSSL 3.2 or later.

### AES key wrap modes

AES key wrap (`AES-WRAP`) and AES key wrap with padding (`AES-WRAP-PAD`)
ciphers operate on a complete key-data value rather than on an incremental byte
stream. The inverse-transform variants have the same processing restrictions.

Applications which use an AES key wrap cipher must adhere to these
restrictions:

- `cipher.update()` or `decipher.update()` must be called exactly once with the
  complete, non-empty input value.
- Do not use AES key wrap ciphers as generic [`stream.Transform`](51-stream.md#class-streamtransform) streams.
  Methods such as `write(data)`, `end(data)`, and `pipe()` can split one value
  across multiple updates, with each update being treated as a separate wrap or
  unwrap operation.
- `cipher.final()` or `decipher.final()` must still be called to complete the
  operation.

### SIV and GCM-SIV modes

`SIV`[2](#user-content-fn-openssl30) and `GCM-SIV`[3](#user-content-fn-openssl32) are supported [AEAD algorithms](https://en.wikipedia.org/wiki/Authenticated_encryption)
when supported by OpenSSL. Applications which use these modes must adhere to
certain restrictions when using the cipher API:

- The authentication tag length is fixed at 16 bytes.
- `AES-SIV` keys are twice the named AES key size: `aes-128-siv` requires a
  32-byte key, `aes-192-siv` requires a 48-byte key, and `aes-256-siv`
  requires a 64-byte key.
- `AES-SIV` ciphers do not use an initialization vector. Pass `null` or a
  zero-length `iv` to [`crypto.createCipheriv()`](#cryptocreatecipherivalgorithm-key-iv-options) or
  [`crypto.createDecipheriv()`](#cryptocreatedecipherivalgorithm-key-iv-options).
- `AES-SIV` and `AES-GCM-SIV` support zero-length plaintext only with OpenSSL
  3.5 or later.
- `AES-SIV` does not have a separate nonce or IV parameter. RFC 5297 defines
  `AES-SIV` over an ordered list of associated-data inputs. Each `setAAD()`
  call supplies one input in that list. If a protocol uses a nonce with
  `AES-SIV`, call `setAAD(nonce)` after the other associated-data inputs and
  before `update()`. At most 126 associated-data inputs may be supplied.
- `AES-GCM-SIV` ciphers require a 12-byte initialization vector.
- When decrypting, the authentication tag must be set via `setAuthTag()` before
  calling `update()`.
- Using stream methods such as `write(data)`, `end(data)` or `pipe()` might
  fail as these modes cannot handle more than one chunk of data per instance.
- As these modes process the whole message at once, `update()` must be called
  exactly once.
- Even though calling `update()` is sufficient to encrypt/decrypt the message,
  applications *must* call `final()` to compute or verify the authentication
  tag.

### FIPS mode

Node.js exposes the FIPS support provided by the linked OpenSSL library. Node.js
is not itself FIPS validated. Validation belongs to a specific OpenSSL module or
provider and only applies when it is deployed according to its security policy.
Vendor-provided Node.js or OpenSSL builds can require a different configuration;
follow the vendor's documentation for those builds.

With OpenSSL 1.1.1, Node.js must be built against a FIPS-capable OpenSSL library.

With OpenSSL 3, FIPS support uses the provider model described in the
[OpenSSL FIPS module guide](https://docs.openssl.org/master/man7/fips_module/). Using FIPS-approved implementations requires:

- A correctly installed OpenSSL 3 FIPS provider.
- An OpenSSL 3 [FIPS module configuration file](https://docs.openssl.org/3.0/man5/fips_config/).
- The FIPS provider to be loaded into the OpenSSL library context used by
  Node.js, normally by activating it in an OpenSSL configuration file when
  Node.js starts.
- The default property query to include `fips=yes` when cryptographic
  implementations are fetched. This can be set from process startup by the
  OpenSSL configuration, [`--enable-fips`](13-cli.md#--enable-fips), or [`--force-fips`](13-cli.md#--force-fips), or for
  subsequent fetches by `crypto.setFips(true)`.

An example OpenSSL 3 configuration file looks like this:

```text
nodejs_conf = nodejs_init
config_diagnostics = 1

.include /<absolute path>/fipsmodule.cnf

[nodejs_init]
providers = provider_sect
alg_section = algorithm_sect

[provider_sect]
# The fips section name should match the section name inside the
# included fipsmodule.cnf.
fips = fips_sect
base = base_sect

[base_sect]
activate = 1

[algorithm_sect]
default_properties = fips=yes
```

The `fipsmodule.cnf` file is generated as part of the FIPS provider installation
and contains module integrity and self-test information. The exact command and
arguments are installation-specific; see [OpenSSL FIPS configuration](https://docs.openssl.org/3.0/man5/fips_config/) and the
[OpenSSL FIPS module guide](https://docs.openssl.org/master/man7/fips_module/). The installation uses `openssl fipsinstall`.

The example activates the provider and enables the `fips=yes` property query
when Node.js starts. To activate the provider at startup but enable the property
query later with `crypto.setFips(true)`, omit `alg_section = algorithm_sect` and
the `[algorithm_sect]` block. The provider must still be loaded; when using this
startup configuration, keep its activation enabled. `crypto.setFips(true)`
should be called before application code uses other OpenSSL-backed APIs. It is
not equivalent to enabling the property query from process startup because
Node.js initializes some OpenSSL state before application code runs. Use the
example as written, [`--enable-fips`](13-cli.md#--enable-fips), or [`--force-fips`](13-cli.md#--force-fips) when the property
query must be active from process startup.

`config_diagnostics` causes configuration errors to prevent startup instead of
being ignored. The `base` provider supplies non-cryptographic supporting
algorithms, such as encoders and decoders, that are commonly needed alongside
the FIPS provider. `default_properties = fips=yes` restricts OpenSSL's default
algorithm selection to implementations that match `fips=yes`.

Set `OPENSSL_CONF` to the OpenSSL configuration file. For a dynamically loaded
provider, `OPENSSL_MODULES` can set the directory containing the provider module.
For example:

```bash
export OPENSSL_CONF=/<path to configuration file>/nodejs.cnf
export OPENSSL_MODULES=/<path to openssl lib>/ossl-modules
```

The [`--openssl-config`](13-cli.md#--openssl-configfile) command-line option selects the configuration file and
takes precedence over `OPENSSL_CONF`. If neither is set, OpenSSL's default
configuration file is used.

By default, Node.js reads the `nodejs_conf` section instead of OpenSSL's usual
`openssl_conf` section. Use [`--openssl-shared-config`](13-cli.md#--openssl-shared-config) to read `openssl_conf`,
or build Node.js with `./configure --openssl-conf-name=<name>` to change the
default section name.

On OpenSSL 3, the configuration above enables the `fips=yes` property query at
startup. The following controls are also available:

- [`--enable-fips`](13-cli.md#--enable-fips) and [`--force-fips`](13-cli.md#--force-fips) enable the property query and
  additionally require the configured provider named `fips` to initialize and
  pass its self-test. Node.js exits if that check fails. `--force-fips` also
  prevents FIPS mode from being disabled from script code. With
  `--force-fips=strict`, Node.js also rejects non-approved operations reported
  through the OpenSSL FIPS indicator callback.
- [`crypto.setFips()`](#cryptosetfipsbool) changes the FIPS/property-query state. On OpenSSL 3, it
  does not install, load, initialize, or validate a provider. Implementations
  fetched before the call are not changed.
- [`crypto.getFips()`](#cryptogetfips) reports the FIPS/property-query state. On OpenSSL 3, a
  return value of `1` does not prove that a FIPS provider is loaded or validated.
- With [`--enable-fips-indicator-events`](13-cli.md#--enable-fips-indicator-events), the
  [`'crypto.fips.indicator'`](18-diagnostics-channel.md#event-cryptofipsindicator) diagnostics channel reports non-approved
  operations permitted by an OpenSSL 3.4 or later FIPS provider configured for
  backwards compatibility.

With OpenSSL 1.1.1, these controls use the library's FIPS mode support and
require a FIPS-capable OpenSSL build.

Only algorithms available under the active FIPS settings can be used. With
OpenSSL 3, if no loaded provider supplies a requested cryptographic
implementation matching `fips=yes`, fetching it fails, typically with
`ERR_OSSL_EVP_UNSUPPORTED`. The same error can occur for algorithms that
Node.js supports when FIPS mode is disabled but that are unavailable under the
active FIPS settings.

OpenSSL documents that the same FIPS provider cannot be used by multiple copies
of `libcrypto` in one process. This can affect native addons that load another
copy of `libcrypto`; OpenSSL's documented workaround is to use a separate copy
of the provider for each `libcrypto` instance. See [OpenSSL FIPS provider
limitations](https://docs.openssl.org/3.6/man7/OSSL_PROVIDER-FIPS/).

## Crypto constants

The following constants exported by `crypto.constants` apply to various uses of
the `node:crypto`, `node:tls`, and `node:https` modules and are generally
specific to OpenSSL.

### OpenSSL options

See the [list of SSL OP Flags](https://wiki.openssl.org/index.php/List_of_SSL_OP_Flags#Table_of_Options) for details.

| Constant | Description |
| --- | --- |
| `SSL_OP_ALL` | Applies multiple bug workarounds within OpenSSL. See <https://www.openssl.org/docs/man3.0/man3/SSL_CTX_set_options.html> for detail. |
| `SSL_OP_ALLOW_NO_DHE_KEX` | Instructs OpenSSL to allow a non-[EC]DHE-based key exchange mode for TLS v1.3 |
| `SSL_OP_ALLOW_UNSAFE_LEGACY_RENEGOTIATION` | Allows legacy insecure renegotiation between OpenSSL and unpatched clients or servers. See <https://www.openssl.org/docs/man3.0/man3/SSL_CTX_set_options.html>. |
| `SSL_OP_CIPHER_SERVER_PREFERENCE` | Attempts to use the server's preferences instead of the client's when selecting a cipher. Behavior depends on protocol version. See <https://www.openssl.org/docs/man3.0/man3/SSL_CTX_set_options.html>. |
| `SSL_OP_CISCO_ANYCONNECT` | Instructs OpenSSL to use Cisco's version identifier of DTLS\_BAD\_VER. |
| `SSL_OP_COOKIE_EXCHANGE` | Instructs OpenSSL to turn on cookie exchange. |
| `SSL_OP_CRYPTOPRO_TLSEXT_BUG` | Instructs OpenSSL to add server-hello extension from an early version of the cryptopro draft. |
| `SSL_OP_DONT_INSERT_EMPTY_FRAGMENTS` | Instructs OpenSSL to disable an SSL 3.0/TLS 1.0 vulnerability workaround added in OpenSSL 0.9.6d. |
| `SSL_OP_LEGACY_SERVER_CONNECT` | Allows initial connection to servers that do not support RI. |
| `SSL_OP_NO_COMPRESSION` | Instructs OpenSSL to disable support for SSL/TLS compression. |
| `SSL_OP_NO_ENCRYPT_THEN_MAC` | Instructs OpenSSL to disable encrypt-then-MAC. |
| `SSL_OP_NO_QUERY_MTU` |  |
| `SSL_OP_NO_RENEGOTIATION` | Instructs OpenSSL to disable renegotiation. |
| `SSL_OP_NO_SESSION_RESUMPTION_ON_RENEGOTIATION` | Instructs OpenSSL to always start a new session when performing renegotiation. |
| `SSL_OP_NO_SSLv2` | Instructs OpenSSL to turn off SSL v2 |
| `SSL_OP_NO_SSLv3` | Instructs OpenSSL to turn off SSL v3 |
| `SSL_OP_NO_TICKET` | Instructs OpenSSL to disable use of RFC4507bis tickets. |
| `SSL_OP_NO_TLSv1` | Instructs OpenSSL to turn off TLS v1 |
| `SSL_OP_NO_TLSv1_1` | Instructs OpenSSL to turn off TLS v1.1 |
| `SSL_OP_NO_TLSv1_2` | Instructs OpenSSL to turn off TLS v1.2 |
| `SSL_OP_NO_TLSv1_3` | Instructs OpenSSL to turn off TLS v1.3 |
| `SSL_OP_PRIORITIZE_CHACHA` | Instructs OpenSSL server to prioritize ChaCha20-Poly1305 when the client does. This option has no effect if `SSL_OP_CIPHER_SERVER_PREFERENCE` is not enabled. |
| `SSL_OP_TLS_ROLLBACK_BUG` | Instructs OpenSSL to disable version rollback attack detection. |

### OpenSSL engine constants

| Constant | Description |
| --- | --- |
| `ENGINE_METHOD_RSA` | Limit engine usage to RSA |
| `ENGINE_METHOD_DSA` | Limit engine usage to DSA |
| `ENGINE_METHOD_DH` | Limit engine usage to DH |
| `ENGINE_METHOD_RAND` | Limit engine usage to RAND |
| `ENGINE_METHOD_EC` | Limit engine usage to EC |
| `ENGINE_METHOD_CIPHERS` | Limit engine usage to CIPHERS |
| `ENGINE_METHOD_DIGESTS` | Limit engine usage to DIGESTS |
| `ENGINE_METHOD_PKEY_METHS` | Limit engine usage to PKEY\_METHS |
| `ENGINE_METHOD_PKEY_ASN1_METHS` | Limit engine usage to PKEY\_ASN1\_METHS |
| `ENGINE_METHOD_ALL` |  |
| `ENGINE_METHOD_NONE` |  |

### Other OpenSSL constants

| Constant | Description |
| --- | --- |
| `DH_CHECK_P_NOT_SAFE_PRIME` |  |
| `DH_CHECK_P_NOT_PRIME` |  |
| `DH_UNABLE_TO_CHECK_GENERATOR` |  |
| `DH_NOT_SUITABLE_GENERATOR` |  |
| `RSA_PKCS1_PADDING` |  |
| `RSA_SSLV23_PADDING` |  |
| `RSA_NO_PADDING` |  |
| `RSA_PKCS1_OAEP_PADDING` |  |
| `RSA_X931_PADDING` |  |
| `RSA_PKCS1_PSS_PADDING` |  |
| `RSA_PSS_SALTLEN_DIGEST` | Sets the salt length for `RSA_PKCS1_PSS_PADDING` to the digest size when signing or verifying. |
| `RSA_PSS_SALTLEN_MAX_SIGN` | Sets the salt length for `RSA_PKCS1_PSS_PADDING` to the maximum permissible value when signing data. |
| `RSA_PSS_SALTLEN_AUTO` | Causes the salt length for `RSA_PKCS1_PSS_PADDING` to be determined automatically when verifying a signature. |
| `POINT_CONVERSION_COMPRESSED` |  |
| `POINT_CONVERSION_UNCOMPRESSED` |  |
| `POINT_CONVERSION_HYBRID` |  |

### Node.js crypto constants

| Constant | Description |
| --- | --- |
| `defaultCoreCipherList` | Specifies the built-in default cipher list used by Node.js. |
| `defaultCipherList` | Specifies the active default cipher list used by the current Node.js process. |

## Footnotes

1. Requires OpenSSL >= 3.5
2. Requires OpenSSL >= 3.0
3. Requires OpenSSL >= 3.2

[Contents](../README.md) | [Previous: Console](14-console.md) | [Next: Debugger](16-debugger.md)
