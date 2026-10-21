[Contents](../README.md) | [Previous: WASI](64-wasi.md) | [Next: Web Streams API](66-webstreams.md)

Source: [https://nodejs.org/docs/latest/api/webcrypto.html](https://nodejs.org/docs/latest/api/webcrypto.html)

# Web Crypto API

**History**

| Version | Changes |
| --- | --- |
| v26.10.0 | Hybrid KEM algorithms are now supported. |
| v25.9.0 | TurboSHAKE and KangarooTwelve algorithms are now supported. |
| v24.8.0 | KMAC algorithms are now supported. |
| v24.8.0 | Argon2 algorithms are now supported. |
| v24.7.0 | AES-OCB algorithm is now supported. |
| v24.7.0 | ML-KEM algorithms are now supported. |
| v24.7.0 | ChaCha20-Poly1305 algorithm is now supported. |
| v24.7.0 | SHA-3 algorithms are now supported. |
| v24.7.0 | SHAKE algorithms are now supported. |
| v24.7.0 | ML-DSA algorithms are now supported. |
| v23.5.0, v22.13.0, v20.19.3 | Algorithms `Ed25519` and `X25519` are now stable. |
| v20.0.0, v18.17.0 | Arguments are now coerced and validated as per their WebIDL definitions like in other Web Crypto API implementations. |
| v19.0.0 | No longer experimental except for the `Ed25519`, `Ed448`, `X25519`, and `X448` algorithms. |
| v18.4.0, v16.17.0 | Removed proprietary `'node.keyObject'` import/export format. |
| v18.4.0, v16.17.0 | Removed proprietary `'NODE-DSA'`, `'NODE-DH'`, and `'NODE-SCRYPT'` algorithms. |
| v18.4.0, v16.17.0 | Added `'Ed25519'`, `'Ed448'`, `'X25519'`, and `'X448'` algorithms. |
| v18.4.0, v16.17.0 | Removed proprietary `'NODE-ED25519'` and `'NODE-ED448'` algorithms. |
| v18.4.0, v16.17.0 | Removed proprietary `'NODE-X25519'` and `'NODE-X448'` named curves from the `'ECDH'` algorithm. |

[Stability: 2](01-documentation.md#stability-index) - Stable

Node.js provides an implementation of the [Web Crypto API](https://www.w3.org/TR/WebCryptoAPI/) standard.

Use `globalThis.crypto` or `require('node:crypto').webcrypto` to access this
module.

```js
const { subtle } = globalThis.crypto;

(async function() {

  const key = await subtle.generateKey({
    name: 'HMAC',
    hash: 'SHA-256',
    length: 256,
  }, true, ['sign', 'verify']);

  const enc = new TextEncoder();
  const message = enc.encode('I love cupcakes');

  const digest = await subtle.sign({
    name: 'HMAC',
  }, key, message);

})();
```

## Modern Algorithms in the Web Cryptography API

Stability: 1.1 - Active development

Node.js provides an implementation of the following features from the
[Modern Algorithms in the Web Cryptography API](https://wicg.github.io/webcrypto-modern-algos/)
WICG proposal:

Algorithms:

- `'AES-OCB'`[1](#user-content-fn-openssl30)
- `'Argon2d'`[2](#user-content-fn-openssl32)
- `'Argon2i'`[2](#user-content-fn-openssl32)
- `'Argon2id'`[2](#user-content-fn-openssl32)
- `'ChaCha20-Poly1305'`
- `'cSHAKE128'`
- `'cSHAKE256'`
- `'KMAC128'`[1](#user-content-fn-openssl30)
- `'KMAC256'`[1](#user-content-fn-openssl30)
- `'KT128'`
- `'KT256'`
- `'ML-DSA-44'`[3](#user-content-fn-openssl35)
- `'ML-DSA-65'`[3](#user-content-fn-openssl35)
- `'ML-DSA-87'`[3](#user-content-fn-openssl35)
- `'ML-KEM-512'`[3](#user-content-fn-openssl35)
- `'ML-KEM-768'`[3](#user-content-fn-openssl35)
- `'ML-KEM-1024'`[3](#user-content-fn-openssl35)
- `'MLKEM768-P256'`[3](#user-content-fn-openssl35)
- `'MLKEM768-X25519'`[3](#user-content-fn-openssl35)
- `'MLKEM1024-P384'`[3](#user-content-fn-openssl35)
- `'SHA3-256'`
- `'SHA3-384'`
- `'SHA3-512'`
- `'TurboSHAKE128'`
- `'TurboSHAKE256'`

Key Formats:

- `'raw-public'`
- `'raw-secret'`
- `'raw-seed'`

Methods:

- [`subtle.decapsulateBits()`](#subtledecapsulatebitsdecapsulationalgorithm-decapsulationkey-ciphertext)
- [`subtle.decapsulateKey()`](#subtledecapsulatekeydecapsulationalgorithm-decapsulationkey-ciphertext-sharedkeyalgorithm-extractable-keyusages)
- [`subtle.encapsulateBits()`](#subtleencapsulatebitsencapsulationalgorithm-encapsulationkey)
- [`subtle.encapsulateKey()`](#subtleencapsulatekeyencapsulationalgorithm-encapsulationkey-sharedkeyalgorithm-extractable-keyusages)
- [`subtle.getPublicKey()`](#subtlegetpublickeykey-keyusages)
- [`SubtleCrypto.supports()`](#static-method-subtlecryptosupportsoperation-algorithm-lengthoradditionalalgorithm)

## Secure Curves in the Web Cryptography API

Stability: 1.1 - Active development

Node.js provides an implementation of the following features from the
[Secure Curves in the Web Cryptography API](https://wicg.github.io/webcrypto-secure-curves/)
WICG proposal:

Algorithms:

- `'Ed448'`
- `'X448'`

## Examples

### Generating keys

The [`<SubtleCrypto>`](65-webcrypto.md#class-subtlecrypto) class can be used to generate symmetric (secret) keys
or asymmetric key pairs (public key and private key).

#### AES keys

```js
const { subtle } = globalThis.crypto;

async function generateAesKey(length = 256) {
  const key = await subtle.generateKey({
    name: 'AES-CBC',
    length,
  }, true, ['encrypt', 'decrypt']);

  return key;
}
```

#### ECDSA key pairs

```js
const { subtle } = globalThis.crypto;

async function generateEcKey(namedCurve = 'P-521') {
  const {
    publicKey,
    privateKey,
  } = await subtle.generateKey({
    name: 'ECDSA',
    namedCurve,
  }, true, ['sign', 'verify']);

  return { publicKey, privateKey };
}
```

#### Ed25519/X25519 key pairs

```js
const { subtle } = globalThis.crypto;

async function generateEd25519Key() {
  return subtle.generateKey({
    name: 'Ed25519',
  }, true, ['sign', 'verify']);
}

async function generateX25519Key() {
  return subtle.generateKey({
    name: 'X25519',
  }, true, ['deriveKey']);
}
```

#### HMAC keys

```js
const { subtle } = globalThis.crypto;

async function generateHmacKey(hash = 'SHA-256') {
  const key = await subtle.generateKey({
    name: 'HMAC',
    hash,
  }, true, ['sign', 'verify']);

  return key;
}
```

#### RSA key pairs

```js
const { subtle } = globalThis.crypto;
const publicExponent = new Uint8Array([1, 0, 1]);

async function generateRsaKey(modulusLength = 2048, hash = 'SHA-256') {
  const {
    publicKey,
    privateKey,
  } = await subtle.generateKey({
    name: 'RSASSA-PKCS1-v1_5',
    modulusLength,
    publicExponent,
    hash,
  }, true, ['sign', 'verify']);

  return { publicKey, privateKey };
}
```

### Encryption and decryption

```js
const crypto = globalThis.crypto;

async function aesEncrypt(plaintext) {
  const ec = new TextEncoder();
  const key = await generateAesKey();
  const iv = crypto.getRandomValues(new Uint8Array(16));

  const ciphertext = await crypto.subtle.encrypt({
    name: 'AES-CBC',
    iv,
  }, key, ec.encode(plaintext));

  return {
    key,
    iv,
    ciphertext,
  };
}

async function aesDecrypt(ciphertext, key, iv) {
  const dec = new TextDecoder();
  const plaintext = await crypto.subtle.decrypt({
    name: 'AES-CBC',
    iv,
  }, key, ciphertext);

  return dec.decode(plaintext);
}
```

### Exporting and importing keys

```js
const { subtle } = globalThis.crypto;

async function generateAndExportHmacKey(format = 'jwk', hash = 'SHA-512') {
  const key = await subtle.generateKey({
    name: 'HMAC',
    hash,
  }, true, ['sign', 'verify']);

  return subtle.exportKey(format, key);
}

async function importHmacKey(keyData, format = 'jwk', hash = 'SHA-512') {
  const key = await subtle.importKey(format, keyData, {
    name: 'HMAC',
    hash,
  }, true, ['sign', 'verify']);

  return key;
}
```

### Wrapping and unwrapping keys

```js
const { subtle } = globalThis.crypto;

async function generateAndWrapHmacKey(format = 'jwk', hash = 'SHA-512') {
  const [
    key,
    wrappingKey,
  ] = await Promise.all([
    subtle.generateKey({
      name: 'HMAC', hash,
    }, true, ['sign', 'verify']),
    subtle.generateKey({
      name: 'AES-KW',
      length: 256,
    }, true, ['wrapKey', 'unwrapKey']),
  ]);

  const wrappedKey = await subtle.wrapKey(format, key, wrappingKey, 'AES-KW');

  return { wrappedKey, wrappingKey };
}

async function unwrapHmacKey(
  wrappedKey,
  wrappingKey,
  format = 'jwk',
  hash = 'SHA-512') {

  const key = await subtle.unwrapKey(
    format,
    wrappedKey,
    wrappingKey,
    'AES-KW',
    { name: 'HMAC', hash },
    true,
    ['sign', 'verify']);

  return key;
}
```

### Sign and verify

```js
const { subtle } = globalThis.crypto;

async function sign(key, data) {
  const ec = new TextEncoder();
  const signature =
    await subtle.sign('RSASSA-PKCS1-v1_5', key, ec.encode(data));
  return signature;
}

async function verify(key, signature, data) {
  const ec = new TextEncoder();
  const verified =
    await subtle.verify(
      'RSASSA-PKCS1-v1_5',
      key,
      signature,
      ec.encode(data));
  return verified;
}
```

### Deriving bits and keys

```js
const { subtle } = globalThis.crypto;

async function pbkdf2(pass, salt, iterations = 1000, length = 256) {
  const ec = new TextEncoder();
  const key = await subtle.importKey(
    'raw',
    ec.encode(pass),
    'PBKDF2',
    false,
    ['deriveBits']);
  const bits = await subtle.deriveBits({
    name: 'PBKDF2',
    hash: 'SHA-512',
    salt: ec.encode(salt),
    iterations,
  }, key, length);
  return bits;
}

async function pbkdf2Key(pass, salt, iterations = 1000, length = 256) {
  const ec = new TextEncoder();
  const keyMaterial = await subtle.importKey(
    'raw',
    ec.encode(pass),
    'PBKDF2',
    false,
    ['deriveKey']);
  const key = await subtle.deriveKey({
    name: 'PBKDF2',
    hash: 'SHA-512',
    salt: ec.encode(salt),
    iterations,
  }, keyMaterial, {
    name: 'AES-GCM',
    length,
  }, true, ['encrypt', 'decrypt']);
  return key;
}
```

### Digest

```js
const { subtle } = globalThis.crypto;

async function digest(data, algorithm = 'SHA-512') {
  const ec = new TextEncoder();
  const digest = await subtle.digest(algorithm, ec.encode(data));
  return digest;
}
```

### Checking for runtime algorithm support

[`SubtleCrypto.supports()`](#static-method-subtlecryptosupportsoperation-algorithm-lengthoradditionalalgorithm) allows feature detection in Web Crypto API,
which can be used to detect whether a given algorithm identifier
(including its parameters) is supported for the given operation.

This example derives a key from a password using Argon2, if available,
or PBKDF2, otherwise; and then encrypts and decrypts some text with it
using AES-OCB, if available, and AES-GCM, otherwise.

```js
const { SubtleCrypto, crypto } = globalThis;

const password = 'correct horse battery staple';
const derivationAlg =
  SubtleCrypto.supports?.('importKey', 'Argon2id') ?
    'Argon2id' :
    'PBKDF2';
const encryptionAlg =
  SubtleCrypto.supports?.('importKey', 'AES-OCB') ?
    'AES-OCB' :
    'AES-GCM';
const passwordKey = await crypto.subtle.importKey(
  derivationAlg === 'Argon2id' ? 'raw-secret' : 'raw',
  new TextEncoder().encode(password),
  derivationAlg,
  false,
  ['deriveKey'],
);
const nonce = crypto.getRandomValues(new Uint8Array(16));
const derivationParams =
  derivationAlg === 'Argon2id' ?
    {
      nonce,
      parallelism: 4,
      memory: 2 ** 21,
      passes: 1,
    } :
    {
      salt: nonce,
      iterations: 100_000,
      hash: 'SHA-256',
    };
const key = await crypto.subtle.deriveKey(
  {
    name: derivationAlg,
    ...derivationParams,
  },
  passwordKey,
  {
    name: encryptionAlg,
    length: 256,
  },
  false,
  ['encrypt', 'decrypt'],
);
const plaintext = 'Hello, world!';
const iv = crypto.getRandomValues(new Uint8Array(12));
const encrypted = await crypto.subtle.encrypt(
  { name: encryptionAlg, iv },
  key,
  new TextEncoder().encode(plaintext),
);
const decrypted = new TextDecoder().decode(await crypto.subtle.decrypt(
  { name: encryptionAlg, iv },
  key,
  encrypted,
));
```

## Algorithm support

The following sections detail the algorithms supported by the Node.js Web
Crypto API implementation and the APIs supported for each:

### Key Management APIs

- [`subtle.generateKey()`](#subtlegeneratekeyalgorithm-extractable-keyusages), [`subtle.exportKey()`](#subtleexportkeyformat-key), and
  [`subtle.importKey()`](#subtleimportkeyformat-keydata-algorithm-extractable-keyusages) support `'AES-CBC'`, `'AES-CTR'`, `'AES-GCM'`,
  `'AES-KW'`, `'AES-OCB'`, `'ChaCha20-Poly1305'`[4](#user-content-fn-modern-algos), `'HMAC'`,
  `'KMAC128'`[4](#user-content-fn-modern-algos), and `'KMAC256'`[4](#user-content-fn-modern-algos).
- [`subtle.importKey()`](#subtleimportkeyformat-keydata-algorithm-extractable-keyusages) supports `'Argon2d'`, `'Argon2i'`, `'Argon2id'`,
  `'HKDF'`, and `'PBKDF2'`.
- [`subtle.generateKey()`](#subtlegeneratekeyalgorithm-extractable-keyusages), [`subtle.exportKey()`](#subtleexportkeyformat-key),
  [`subtle.importKey()`](#subtleimportkeyformat-keydata-algorithm-extractable-keyusages), and [`subtle.getPublicKey()`](#subtlegetpublickeykey-keyusages) support `'ECDH'`,
  `'ECDSA'`, `'Ed25519'`, `'Ed448'`[5](#user-content-fn-secure-curves),
  `'ML-DSA-44'`[4](#user-content-fn-modern-algos), `'ML-DSA-65'`[4](#user-content-fn-modern-algos),
  `'ML-DSA-87'`[4](#user-content-fn-modern-algos), `'ML-KEM-512'`[4](#user-content-fn-modern-algos),
  `'ML-KEM-768'`[4](#user-content-fn-modern-algos), `'ML-KEM-1024'`[4](#user-content-fn-modern-algos),
  `'MLKEM768-P256'`[4](#user-content-fn-modern-algos), `'MLKEM768-X25519'`[4](#user-content-fn-modern-algos),
  `'MLKEM1024-P384'`[4](#user-content-fn-modern-algos), `'RSA-OAEP'`, `'RSA-PSS'`,
  `'RSASSA-PKCS1-v1_5'`, `'X25519'`, and `'X448'`[5](#user-content-fn-secure-curves).

### Crypto Operation APIs

- [`subtle.encrypt()`](#subtleencryptalgorithm-key-data) and [`subtle.decrypt()`](#subtledecryptalgorithm-key-data) support `'AES-CBC'`,
  `'AES-CTR'`, `'AES-GCM'`, `'AES-OCB'`,
  `'ChaCha20-Poly1305'`[4](#user-content-fn-modern-algos), and `'RSA-OAEP'`.
- [`subtle.sign()`](#subtlesignalgorithm-key-data) and [`subtle.verify()`](#subtleverifyalgorithm-key-signature-data) support `'ECDSA'`, `'Ed25519'`,
  `'Ed448'`[5](#user-content-fn-secure-curves), `'HMAC'`, `'KMAC128'`[4](#user-content-fn-modern-algos),
  `'KMAC256'`[4](#user-content-fn-modern-algos), `'ML-DSA-44'`[4](#user-content-fn-modern-algos),
  `'ML-DSA-65'`[4](#user-content-fn-modern-algos), `'ML-DSA-87'`[4](#user-content-fn-modern-algos), `'RSA-PSS'`, and
  `'RSASSA-PKCS1-v1_5'`.
- [`subtle.deriveBits()`](#subtlederivebitsalgorithm-basekey-length) and [`subtle.deriveKey()`](#subtlederivekeyalgorithm-basekey-derivedkeytype-extractable-keyusages) support `'Argon2d'`,
  `'Argon2i'`, `'Argon2id'`, `'ECDH'`, `'HKDF'`, `'PBKDF2'`, `'X25519'`, and
  `'X448'`[5](#user-content-fn-secure-curves).
- [`subtle.wrapKey()`](#subtlewrapkeyformat-key-wrappingkey-wrapalgorithm) and [`subtle.unwrapKey()`](#subtleunwrapkeyformat-wrappedkey-unwrappingkey-unwrapalgorithm-unwrappedkeyalgorithm-extractable-keyusages) support `'AES-CBC'`,
  `'AES-CTR'`, `'AES-GCM'`, `'AES-KW'`, `'AES-OCB'`,
  `'ChaCha20-Poly1305'`[4](#user-content-fn-modern-algos), and `'RSA-OAEP'`.
- [`subtle.encapsulateBits()`](#subtleencapsulatebitsencapsulationalgorithm-encapsulationkey), [`subtle.decapsulateBits()`](#subtledecapsulatebitsdecapsulationalgorithm-decapsulationkey-ciphertext),
  [`subtle.encapsulateKey()`](#subtleencapsulatekeyencapsulationalgorithm-encapsulationkey-sharedkeyalgorithm-extractable-keyusages), and [`subtle.decapsulateKey()`](#subtledecapsulatekeydecapsulationalgorithm-decapsulationkey-ciphertext-sharedkeyalgorithm-extractable-keyusages) support
  `'ML-KEM-512'`[4](#user-content-fn-modern-algos), `'ML-KEM-768'`[4](#user-content-fn-modern-algos),
  `'ML-KEM-1024'`[4](#user-content-fn-modern-algos), `'MLKEM768-P256'`[4](#user-content-fn-modern-algos),
  `'MLKEM768-X25519'`[4](#user-content-fn-modern-algos), and
  `'MLKEM1024-P384'`[4](#user-content-fn-modern-algos).
- [`subtle.digest()`](#subtledigestalgorithm-data) supports `'cSHAKE128'`[4](#user-content-fn-modern-algos),
  `'cSHAKE256'`[4](#user-content-fn-modern-algos), `'KT128'`[4](#user-content-fn-modern-algos),
  `'KT256'`[4](#user-content-fn-modern-algos), `'SHA-1'`, `'SHA-256'`, `'SHA-384'`, `'SHA-512'`,
  `'SHA3-256'`[4](#user-content-fn-modern-algos), `'SHA3-384'`[4](#user-content-fn-modern-algos),
  `'SHA3-512'`[4](#user-content-fn-modern-algos), `'TurboSHAKE128'`[4](#user-content-fn-modern-algos), and
  `'TurboSHAKE256'`[4](#user-content-fn-modern-algos).

### Key Formats

The following list describes the formats supported by [`subtle.importKey()`](#subtleimportkeyformat-keydata-algorithm-extractable-keyusages)
and [`subtle.exportKey()`](#subtleexportkeyformat-key).

- **`'AES-CBC'`, `'AES-CTR'`, `'AES-GCM'`, `'AES-KW'`, and `'HMAC'`** can
  be imported and exported using `'jwk'`, `'raw'`, and
  `'raw-secret'`[4](#user-content-fn-modern-algos).
- **`'AES-OCB'`[4](#user-content-fn-modern-algos), `'ChaCha20-Poly1305'`[4](#user-content-fn-modern-algos),
  `'KMAC128'`[4](#user-content-fn-modern-algos), and `'KMAC256'`[4](#user-content-fn-modern-algos)** can be imported
  and exported using `'jwk'` and `'raw-secret'`[4](#user-content-fn-modern-algos).
- **`'Argon2d'`[4](#user-content-fn-modern-algos), `'Argon2i'`[4](#user-content-fn-modern-algos), and
  `'Argon2id'`[4](#user-content-fn-modern-algos)** can be imported using
  `'raw-secret'`[4](#user-content-fn-modern-algos); export is not supported.
- **`'ECDH'`, `'ECDSA'`, `'Ed25519'`, `'Ed448'`[5](#user-content-fn-secure-curves), `'X25519'`,
  and `'X448'`[5](#user-content-fn-secure-curves)** can be imported and exported using `'spki'`,
  `'pkcs8'`, `'jwk'`, `'raw'`, and `'raw-public'`[4](#user-content-fn-modern-algos).
- **`'HKDF'` and `'PBKDF2'`** can be imported using `'raw'` and
  `'raw-secret'`[4](#user-content-fn-modern-algos); export is not supported.
- **`'ML-DSA-44'`[4](#user-content-fn-modern-algos), `'ML-DSA-65'`[4](#user-content-fn-modern-algos),
  `'ML-DSA-87'`[4](#user-content-fn-modern-algos), `'ML-KEM-512'`[4](#user-content-fn-modern-algos),
  `'ML-KEM-768'`[4](#user-content-fn-modern-algos), and `'ML-KEM-1024'`[4](#user-content-fn-modern-algos)** can be
  imported and exported using `'spki'`, `'pkcs8'`, `'jwk'`,
  `'raw-public'`[4](#user-content-fn-modern-algos), and `'raw-seed'`[4](#user-content-fn-modern-algos).
- **`'MLKEM768-P256'`[4](#user-content-fn-modern-algos),
  `'MLKEM768-X25519'`[4](#user-content-fn-modern-algos), and
  `'MLKEM1024-P384'`[4](#user-content-fn-modern-algos)** can be imported and exported using
  `'jwk'`, `'raw-public'`[4](#user-content-fn-modern-algos), and `'raw-seed'`[4](#user-content-fn-modern-algos).
- **`'RSA-OAEP'`, `'RSA-PSS'`, and `'RSASSA-PKCS1-v1_5'`** can be imported
  and exported using `'spki'`, `'pkcs8'`, and `'jwk'`.

## Class: `Crypto`

Added in: v15.0.0

`globalThis.crypto` is an instance of the `Crypto`
class. `Crypto` is a singleton that provides access to the remainder of the
crypto API.

### `crypto.subtle`

Added in: v15.0.0

- Type: [`<SubtleCrypto>`](65-webcrypto.md#class-subtlecrypto)

Provides access to the `SubtleCrypto` API.

### `crypto.getRandomValues(typedArray)`

Added in: v15.0.0

- `typedArray` [`<Buffer>`](07-buffer-part1.md#class-buffer) | [`<TypedArray>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/TypedArray)
- Returns: [`<Buffer>`](07-buffer-part1.md#class-buffer) | [`<TypedArray>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/TypedArray)

Generates cryptographically strong random values. The given `typedArray` is
filled with random values, and a reference to `typedArray` is returned.

The given `typedArray` must be an integer-based instance of [`<TypedArray>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/TypedArray),
i.e. `Float32Array` and `Float64Array` are not accepted.

An error will be thrown if the given `typedArray` is larger than 65,536 bytes.

### `crypto.randomUUID()`

Added in: v16.7.0

- Returns: [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type)

Generates a random [RFC 4122](https://www.rfc-editor.org/rfc/rfc4122.txt) version 4 UUID. The UUID is generated using a
cryptographic pseudorandom number generator.

## Class: `CryptoKey`

Added in: v15.0.0

### `cryptoKey.algorithm`

Added in: v15.0.0

- Type: [`<KeyAlgorithm>`](65-webcrypto.md#class-keyalgorithm) | [`<RsaHashedKeyAlgorithm>`](65-webcrypto.md#class-rsahashedkeyalgorithm) | [`<EcKeyAlgorithm>`](65-webcrypto.md#class-eckeyalgorithm) | [`<AesKeyAlgorithm>`](65-webcrypto.md#class-aeskeyalgorithm) | [`<HmacKeyAlgorithm>`](65-webcrypto.md#class-hmackeyalgorithm) | [`<KmacKeyAlgorithm>`](65-webcrypto.md#class-kmackeyalgorithm)

An object detailing the algorithm for which the key can be used along with
additional algorithm-specific parameters.

Read-only.

### `cryptoKey.extractable`

Added in: v15.0.0

- Type: [`<boolean>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#boolean_type)

When `true`, the [`<CryptoKey>`](65-webcrypto.md#class-cryptokey) can be extracted using either [`subtle.exportKey()`](#subtleexportkeyformat-key) or [`subtle.wrapKey()`](#subtlewrapkeyformat-key-wrappingkey-wrapalgorithm).

Read-only.

### `cryptoKey.type`

Added in: v15.0.0

- Type: [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) One of `'secret'`, `'private'`, or `'public'`.

A string identifying whether the key is a symmetric (`'secret'`) or
asymmetric (`'private'` or `'public'`) key.

### `cryptoKey.usages`

Added in: v15.0.0

- Type: [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type)[]

An array of strings identifying the operations for which the
key may be used.

The possible usages are:

- `'encrypt'` - Enable using the key with [`subtle.encrypt()`](#subtleencryptalgorithm-key-data)
- `'decrypt'` - Enable using the key with [`subtle.decrypt()`](#subtledecryptalgorithm-key-data)
- `'sign'` - Enable using the key with [`subtle.sign()`](#subtlesignalgorithm-key-data)
- `'verify'` - Enable using the key with [`subtle.verify()`](#subtleverifyalgorithm-key-signature-data)
- `'deriveKey'` - Enable using the key with [`subtle.deriveKey()`](#subtlederivekeyalgorithm-basekey-derivedkeytype-extractable-keyusages)
- `'deriveBits'` - Enable using the key with [`subtle.deriveBits()`](#subtlederivebitsalgorithm-basekey-length)
- `'encapsulateBits'` - Enable using the key with [`subtle.encapsulateBits()`](#subtleencapsulatebitsencapsulationalgorithm-encapsulationkey)
- `'decapsulateBits'` - Enable using the key with [`subtle.decapsulateBits()`](#subtledecapsulatebitsdecapsulationalgorithm-decapsulationkey-ciphertext)
- `'encapsulateKey'` - Enable using the key with [`subtle.encapsulateKey()`](#subtleencapsulatekeyencapsulationalgorithm-encapsulationkey-sharedkeyalgorithm-extractable-keyusages)
- `'decapsulateKey'` - Enable using the key with [`subtle.decapsulateKey()`](#subtledecapsulatekeydecapsulationalgorithm-decapsulationkey-ciphertext-sharedkeyalgorithm-extractable-keyusages)
- `'wrapKey'` - Enable using the key with [`subtle.wrapKey()`](#subtlewrapkeyformat-key-wrappingkey-wrapalgorithm)
- `'unwrapKey'` - Enable using the key with [`subtle.unwrapKey()`](#subtleunwrapkeyformat-wrappedkey-unwrappingkey-unwrapalgorithm-unwrappedkeyalgorithm-extractable-keyusages)

Valid key usages depend on the key algorithm (identified by
`cryptokey.algorithm.name`). See [Crypto operation APIs](#crypto-operation-apis) for the operations
supported by each key algorithm.

## Class: `CryptoKeyPair`

Added in: v15.0.0

The `CryptoKeyPair` is a simple dictionary object with `publicKey` and
`privateKey` properties, representing an asymmetric key pair.

### `cryptoKeyPair.privateKey`

Added in: v15.0.0

- Type: [`<CryptoKey>`](65-webcrypto.md#class-cryptokey) A [`<CryptoKey>`](65-webcrypto.md#class-cryptokey) whose `type` will be `'private'`.

### `cryptoKeyPair.publicKey`

Added in: v15.0.0

- Type: [`<CryptoKey>`](65-webcrypto.md#class-cryptokey) A [`<CryptoKey>`](65-webcrypto.md#class-cryptokey) whose `type` will be `'public'`.

## Class: `SubtleCrypto`

Added in: v15.0.0

### Static method: `SubtleCrypto.supports(operation, algorithm[, lengthOrAdditionalAlgorithm])`

Added in: v24.7.0

Stability: 1.1 - Active development

- `operation` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) "encrypt", "decrypt", "sign", "verify", "digest", "generateKey", "deriveKey", "deriveBits", "importKey", "exportKey", "getPublicKey", "wrapKey", "unwrapKey", "encapsulateBits", "encapsulateKey", "decapsulateBits", or "decapsulateKey"
- `algorithm` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) | [`<Algorithm>`](65-webcrypto.md#class-algorithm)
- `lengthOrAdditionalAlgorithm` [`<null>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#null_type) | [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) | [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) | [`<Algorithm>`](65-webcrypto.md#class-algorithm) | [`<undefined>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#undefined_type) Depending on the operation this is either ignored, the value of the length argument when operation is "deriveBits", the algorithm of key to be derived when operation is "deriveKey", the algorithm of key to be exported before wrapping when operation is "wrapKey", the algorithm of key to be imported after unwrapping when operation is "unwrapKey", or the algorithm of key to be imported after en/decapsulating a key when operation is "encapsulateKey" or "decapsulateKey". **Default:** `null` when operation is "deriveBits", `undefined` otherwise.
- Returns: [`<boolean>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#boolean_type) Indicating whether the implementation supports the given operation

Allows feature detection in Web Crypto API,
which can be used to detect whether a given algorithm identifier
(including its parameters) is supported for the given operation.

See [Checking for runtime algorithm support](#checking-for-runtime-algorithm-support) for an example use of this method.

### `subtle.decapsulateBits(decapsulationAlgorithm, decapsulationKey, ciphertext)`

Added in: v24.7.0

Stability: 1.1 - Active development

- `decapsulationAlgorithm` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) | [`<Algorithm>`](65-webcrypto.md#class-algorithm)
- `decapsulationKey` [`<CryptoKey>`](65-webcrypto.md#class-cryptokey)
- `ciphertext` [`<ArrayBuffer>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/ArrayBuffer) | [`<TypedArray>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/TypedArray) | [`<DataView>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/DataView) | [`<Buffer>`](07-buffer-part1.md#class-buffer)
- Returns: [`<Promise>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Promise) Fulfills with [`<ArrayBuffer>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/ArrayBuffer) upon success.

A message recipient uses their asymmetric private key to decrypt an
"encapsulated key" (ciphertext), thereby recovering a temporary symmetric
key (represented as [`<ArrayBuffer>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/ArrayBuffer)) which is then used to decrypt a message.

The algorithms currently supported include:

- `'ML-KEM-512'`[4](#user-content-fn-modern-algos)
- `'ML-KEM-768'`[4](#user-content-fn-modern-algos)
- `'ML-KEM-1024'`[4](#user-content-fn-modern-algos)
- `'MLKEM768-P256'`[4](#user-content-fn-modern-algos)
- `'MLKEM768-X25519'`[4](#user-content-fn-modern-algos)
- `'MLKEM1024-P384'`[4](#user-content-fn-modern-algos)

### `subtle.decapsulateKey(decapsulationAlgorithm, decapsulationKey, ciphertext, sharedKeyAlgorithm, extractable, keyUsages)`

Added in: v24.7.0

Stability: 1.1 - Active development

- `decapsulationAlgorithm` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) | [`<Algorithm>`](65-webcrypto.md#class-algorithm)
- `decapsulationKey` [`<CryptoKey>`](65-webcrypto.md#class-cryptokey)
- `ciphertext` [`<ArrayBuffer>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/ArrayBuffer) | [`<TypedArray>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/TypedArray) | [`<DataView>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/DataView) | [`<Buffer>`](07-buffer-part1.md#class-buffer)
- `sharedKeyAlgorithm` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) | [`<Algorithm>`](65-webcrypto.md#class-algorithm) | [`<HmacImportParams>`](65-webcrypto.md#class-hmacimportparams) | [`<AesDerivedKeyParams>`](65-webcrypto.md#class-aesderivedkeyparams) | [`<KmacImportParams>`](65-webcrypto.md#class-kmacimportparams)
- `extractable` [`<boolean>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#boolean_type)
- `keyUsages` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type)[] See [Key usages](#cryptokeyusages).
- Returns: [`<Promise>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Promise) Fulfills with [`<CryptoKey>`](65-webcrypto.md#class-cryptokey) upon success.

A message recipient uses their asymmetric private key to decrypt an
"encapsulated key" (ciphertext), thereby recovering a temporary symmetric
key (represented as [`<CryptoKey>`](65-webcrypto.md#class-cryptokey)) which is then used to decrypt a message.

The algorithms currently supported include:

- `'ML-KEM-512'`[4](#user-content-fn-modern-algos)
- `'ML-KEM-768'`[4](#user-content-fn-modern-algos)
- `'ML-KEM-1024'`[4](#user-content-fn-modern-algos)
- `'MLKEM768-P256'`[4](#user-content-fn-modern-algos)
- `'MLKEM768-X25519'`[4](#user-content-fn-modern-algos)
- `'MLKEM1024-P384'`[4](#user-content-fn-modern-algos)

### `subtle.decrypt(algorithm, key, data)`

Added in: v15.0.0

**History**

| Version | Changes |
| --- | --- |
| v24.7.0 | AES-OCB algorithm is now supported. |
| v24.7.0 | ChaCha20-Poly1305 algorithm is now supported. |

- `algorithm` [`<RsaOaepParams>`](65-webcrypto.md#class-rsaoaepparams) | [`<AesCtrParams>`](65-webcrypto.md#class-aesctrparams) | [`<AesCbcParams>`](65-webcrypto.md#class-aescbcparams) | [`<AeadParams>`](65-webcrypto.md#class-aeadparams)
- `key` [`<CryptoKey>`](65-webcrypto.md#class-cryptokey)
- `data` [`<ArrayBuffer>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/ArrayBuffer) | [`<TypedArray>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/TypedArray) | [`<DataView>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/DataView) | [`<Buffer>`](07-buffer-part1.md#class-buffer)
- Returns: [`<Promise>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Promise) Fulfills with an [`<ArrayBuffer>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/ArrayBuffer) upon success.

Using the method and parameters specified in `algorithm` and the keying
material provided by `key`, this method attempts to decipher the
provided `data`. If successful, the returned promise will be resolved with
an [`<ArrayBuffer>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/ArrayBuffer) containing the plaintext result.

The algorithms currently supported include:

- `'AES-CBC'`
- `'AES-CTR'`
- `'AES-GCM'`
- `'AES-OCB'`[4](#user-content-fn-modern-algos)
- `'ChaCha20-Poly1305'`[4](#user-content-fn-modern-algos)
- `'RSA-OAEP'`

### `subtle.deriveBits(algorithm, baseKey[, length])`

Added in: v15.0.0

**History**

| Version | Changes |
| --- | --- |
| v24.8.0 | Argon2 algorithms are now supported. |
| v22.5.0, v20.17.0, v18.20.5 | The length parameter is now optional for `'ECDH'`, `'X25519'`, and `'X448'`. |
| v18.4.0, v16.17.0 | Added `'X25519'`, and `'X448'` algorithms. |

- `algorithm` [`<EcdhKeyDeriveParams>`](65-webcrypto.md#class-ecdhkeyderiveparams) | [`<HkdfParams>`](65-webcrypto.md#class-hkdfparams) | [`<Pbkdf2Params>`](65-webcrypto.md#class-pbkdf2params) | [`<Argon2Params>`](65-webcrypto.md#class-argon2params)
- `baseKey` [`<CryptoKey>`](65-webcrypto.md#class-cryptokey)
- `length` [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) | [`<null>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#null_type) **Default:** `null`
- Returns: [`<Promise>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Promise) Fulfills with an [`<ArrayBuffer>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/ArrayBuffer) upon success.

Using the method and parameters specified in `algorithm` and the keying
material provided by `baseKey`, this method attempts to generate
`length` bits.

When `length` is not provided or `null` the maximum number of bits for a given
algorithm is generated. This is allowed for the `'ECDH'`, `'X25519'`, and `'X448'`[5](#user-content-fn-secure-curves)
algorithms, for other algorithms `length` is required to be a number.

If successful, the returned promise will be resolved with an [`<ArrayBuffer>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/ArrayBuffer)
containing the generated data.

The algorithms currently supported include:

- `'Argon2d'`[4](#user-content-fn-modern-algos)
- `'Argon2i'`[4](#user-content-fn-modern-algos)
- `'Argon2id'`[4](#user-content-fn-modern-algos)
- `'ECDH'`
- `'HKDF'`
- `'PBKDF2'`
- `'X25519'`
- `'X448'`[5](#user-content-fn-secure-curves)

### `subtle.deriveKey(algorithm, baseKey, derivedKeyType, extractable, keyUsages)`

Added in: v15.0.0

**History**

| Version | Changes |
| --- | --- |
| v24.8.0 | Argon2 algorithms are now supported. |
| v18.4.0, v16.17.0 | Added `'X25519'`, and `'X448'` algorithms. |

- `algorithm` [`<EcdhKeyDeriveParams>`](65-webcrypto.md#class-ecdhkeyderiveparams) | [`<HkdfParams>`](65-webcrypto.md#class-hkdfparams) | [`<Pbkdf2Params>`](65-webcrypto.md#class-pbkdf2params) | [`<Argon2Params>`](65-webcrypto.md#class-argon2params)
- `baseKey` [`<CryptoKey>`](65-webcrypto.md#class-cryptokey)
- `derivedKeyType` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) | [`<Algorithm>`](65-webcrypto.md#class-algorithm) | [`<HmacImportParams>`](65-webcrypto.md#class-hmacimportparams) | [`<AesDerivedKeyParams>`](65-webcrypto.md#class-aesderivedkeyparams) | [`<KmacImportParams>`](65-webcrypto.md#class-kmacimportparams)
- `extractable` [`<boolean>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#boolean_type)
- `keyUsages` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type)[] See [Key usages](#cryptokeyusages).
- Returns: [`<Promise>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Promise) Fulfills with a [`<CryptoKey>`](65-webcrypto.md#class-cryptokey) upon success.

Using the method and parameters specified in `algorithm`, and the keying
material provided by `baseKey`, this method attempts to generate
a new [`<CryptoKey>`](65-webcrypto.md#class-cryptokey) based on the method and parameters in `derivedKeyType`.

Calling this method is equivalent to calling [`subtle.deriveBits()`](#subtlederivebitsalgorithm-basekey-length) to
generate raw keying material, then passing the result into the
[`subtle.importKey()`](#subtleimportkeyformat-keydata-algorithm-extractable-keyusages) method using the `derivedKeyType`, `extractable`, and
`keyUsages` parameters as input.

The algorithms currently supported include:

- `'Argon2d'`[4](#user-content-fn-modern-algos)
- `'Argon2i'`[4](#user-content-fn-modern-algos)
- `'Argon2id'`[4](#user-content-fn-modern-algos)
- `'ECDH'`
- `'HKDF'`
- `'PBKDF2'`
- `'X25519'`
- `'X448'`[5](#user-content-fn-secure-curves)

### `subtle.digest(algorithm, data)`

Added in: v15.0.0

**History**

| Version | Changes |
| --- | --- |
| v25.9.0 | TurboSHAKE and KangarooTwelve algorithms are now supported. |
| v24.7.0 | SHA-3 algorithms are now supported. |
| v24.7.0 | SHAKE algorithms are now supported. |

- `algorithm` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) | [`<Algorithm>`](65-webcrypto.md#class-algorithm) | [`<CShakeParams>`](65-webcrypto.md#class-cshakeparams) | [`<TurboShakeParams>`](65-webcrypto.md#class-turboshakeparams) | [`<KangarooTwelveParams>`](65-webcrypto.md#class-kangarootwelveparams)
- `data` [`<ArrayBuffer>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/ArrayBuffer) | [`<TypedArray>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/TypedArray) | [`<DataView>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/DataView) | [`<Buffer>`](07-buffer-part1.md#class-buffer)
- Returns: [`<Promise>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Promise) Fulfills with an [`<ArrayBuffer>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/ArrayBuffer) upon success.

Using the method identified by `algorithm`, this method attempts to
generate a digest of `data`. If successful, the returned promise is resolved
with an [`<ArrayBuffer>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/ArrayBuffer) containing the computed digest.

If `algorithm` is provided as a [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type), it must be one of:

- `'cSHAKE128'`[4](#user-content-fn-modern-algos)
- `'cSHAKE256'`[4](#user-content-fn-modern-algos)
- `'KT128'`[4](#user-content-fn-modern-algos)
- `'KT256'`[4](#user-content-fn-modern-algos)
- `'SHA-1'`
- `'SHA-256'`
- `'SHA-384'`
- `'SHA-512'`
- `'SHA3-256'`[4](#user-content-fn-modern-algos)
- `'SHA3-384'`[4](#user-content-fn-modern-algos)
- `'SHA3-512'`[4](#user-content-fn-modern-algos)
- `'TurboSHAKE128'`[4](#user-content-fn-modern-algos)
- `'TurboSHAKE256'`[4](#user-content-fn-modern-algos)

If `algorithm` is provided as an [`<Object>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Object), it must have a `name` property
whose value is one of the above.

### `subtle.encapsulateBits(encapsulationAlgorithm, encapsulationKey)`

Added in: v24.7.0

Stability: 1.1 - Active development

- `encapsulationAlgorithm` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) | [`<Algorithm>`](65-webcrypto.md#class-algorithm)
- `encapsulationKey` [`<CryptoKey>`](65-webcrypto.md#class-cryptokey)
- Returns: [`<Promise>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Promise) Fulfills with [`<EncapsulatedBits>`](65-webcrypto.md#class-encapsulatedbits) upon success.

Uses a message recipient's asymmetric public key to encrypt a temporary symmetric key.
This encrypted key is the "encapsulated key" represented as [`<EncapsulatedBits>`](65-webcrypto.md#class-encapsulatedbits).

The algorithms currently supported include:

- `'ML-KEM-512'`[4](#user-content-fn-modern-algos)
- `'ML-KEM-768'`[4](#user-content-fn-modern-algos)
- `'ML-KEM-1024'`[4](#user-content-fn-modern-algos)
- `'MLKEM768-P256'`[4](#user-content-fn-modern-algos)
- `'MLKEM768-X25519'`[4](#user-content-fn-modern-algos)
- `'MLKEM1024-P384'`[4](#user-content-fn-modern-algos)

### `subtle.encapsulateKey(encapsulationAlgorithm, encapsulationKey, sharedKeyAlgorithm, extractable, keyUsages)`

Added in: v24.7.0

Stability: 1.1 - Active development

- `encapsulationAlgorithm` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) | [`<Algorithm>`](65-webcrypto.md#class-algorithm)
- `encapsulationKey` [`<CryptoKey>`](65-webcrypto.md#class-cryptokey)
- `sharedKeyAlgorithm` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) | [`<Algorithm>`](65-webcrypto.md#class-algorithm) | [`<HmacImportParams>`](65-webcrypto.md#class-hmacimportparams) | [`<AesDerivedKeyParams>`](65-webcrypto.md#class-aesderivedkeyparams) | [`<KmacImportParams>`](65-webcrypto.md#class-kmacimportparams)
- `extractable` [`<boolean>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#boolean_type)
- `keyUsages` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type)[] See [Key usages](#cryptokeyusages).
- Returns: [`<Promise>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Promise) Fulfills with [`<EncapsulatedKey>`](65-webcrypto.md#class-encapsulatedkey) upon success.

Uses a message recipient's asymmetric public key to encrypt a temporary symmetric key.
This encrypted key is the "encapsulated key" represented as [`<EncapsulatedKey>`](65-webcrypto.md#class-encapsulatedkey).

The algorithms currently supported include:

- `'ML-KEM-512'`[4](#user-content-fn-modern-algos)
- `'ML-KEM-768'`[4](#user-content-fn-modern-algos)
- `'ML-KEM-1024'`[4](#user-content-fn-modern-algos)
- `'MLKEM768-P256'`[4](#user-content-fn-modern-algos)
- `'MLKEM768-X25519'`[4](#user-content-fn-modern-algos)
- `'MLKEM1024-P384'`[4](#user-content-fn-modern-algos)

### `subtle.encrypt(algorithm, key, data)`

Added in: v15.0.0

**History**

| Version | Changes |
| --- | --- |
| v24.7.0 | AES-OCB algorithm is now supported. |
| v24.7.0 | ChaCha20-Poly1305 algorithm is now supported. |

- `algorithm` [`<RsaOaepParams>`](65-webcrypto.md#class-rsaoaepparams) | [`<AesCtrParams>`](65-webcrypto.md#class-aesctrparams) | [`<AesCbcParams>`](65-webcrypto.md#class-aescbcparams) | [`<AeadParams>`](65-webcrypto.md#class-aeadparams)
- `key` [`<CryptoKey>`](65-webcrypto.md#class-cryptokey)
- `data` [`<ArrayBuffer>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/ArrayBuffer) | [`<TypedArray>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/TypedArray) | [`<DataView>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/DataView) | [`<Buffer>`](07-buffer-part1.md#class-buffer)
- Returns: [`<Promise>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Promise) Fulfills with an [`<ArrayBuffer>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/ArrayBuffer) upon success.

Using the method and parameters specified by `algorithm` and the keying
material provided by `key`, this method attempts to encipher `data`.
If successful, the returned promise is resolved with an [`<ArrayBuffer>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/ArrayBuffer)
containing the encrypted result.

The algorithms currently supported include:

- `'AES-CBC'`
- `'AES-CTR'`
- `'AES-GCM'`
- `'AES-OCB'`[4](#user-content-fn-modern-algos)
- `'ChaCha20-Poly1305'`[4](#user-content-fn-modern-algos)
- `'RSA-OAEP'`

### `subtle.exportKey(format, key)`

Added in: v15.0.0

**History**

| Version | Changes |
| --- | --- |
| v26.10.0 | Hybrid KEM algorithms are now supported. |
| v26.1.0 | Added JWK format support for ML-KEM key types. |
| v24.8.0 | KMAC algorithms are now supported. |
| v24.7.0 | ML-KEM algorithms are now supported. |
| v24.7.0 | ChaCha20-Poly1305 algorithm is now supported. |
| v24.7.0 | ML-DSA algorithms are now supported. |
| v18.4.0, v16.17.0 | Added `'Ed25519'`, `'Ed448'`, `'X25519'`, and `'X448'` algorithms. |
| v15.9.0 | Removed `'NODE-DSA'` JWK export. |

- `format` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) Must be one of `'raw'`, `'pkcs8'`, `'spki'`, `'jwk'`, `'raw-secret'`[4](#user-content-fn-modern-algos),
  `'raw-public'`[4](#user-content-fn-modern-algos), or `'raw-seed'`[4](#user-content-fn-modern-algos).
- `key` [`<CryptoKey>`](65-webcrypto.md#class-cryptokey)
- Returns: [`<Promise>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Promise) Fulfills with an [`<ArrayBuffer>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/ArrayBuffer) | [`<Object>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Object) upon success.

Exports the given key into the specified format, if supported.

If the [`<CryptoKey>`](65-webcrypto.md#class-cryptokey) is not extractable, the returned promise will reject.

When `format` is either `'pkcs8'` or `'spki'` and the export is successful,
the returned promise will be resolved with an [`<ArrayBuffer>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/ArrayBuffer) containing the
exported key data.

When `format` is `'jwk'` and the export is successful, the returned promise
will be resolved with a JavaScript object conforming to the [JSON Web Key](https://tools.ietf.org/html/rfc7517)
specification.

See [Key formats](#key-formats) for the formats supported by each algorithm.

### `subtle.getPublicKey(key, keyUsages)`

Added in: v24.7.0

Stability: 1.1 - Active development

- `key` [`<CryptoKey>`](65-webcrypto.md#class-cryptokey) A private key from which to derive the corresponding public key.
- `keyUsages` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type)[] See [Key usages](#cryptokeyusages).
- Returns: [`<Promise>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Promise) Fulfills with a [`<CryptoKey>`](65-webcrypto.md#class-cryptokey) upon success.

Derives the public key from a given private key.

### `subtle.generateKey(algorithm, extractable, keyUsages)`

Added in: v15.0.0

**History**

| Version | Changes |
| --- | --- |
| v26.10.0 | Hybrid KEM algorithms are now supported. |
| v24.8.0 | KMAC algorithms are now supported. |
| v24.7.0 | ML-KEM algorithms are now supported. |
| v24.7.0 | ChaCha20-Poly1305 algorithm is now supported. |
| v24.7.0 | ML-DSA algorithms are now supported. |

- `algorithm` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) | [`<Algorithm>`](65-webcrypto.md#class-algorithm) | [`<RsaHashedKeyGenParams>`](65-webcrypto.md#class-rsahashedkeygenparams) | [`<EcKeyGenParams>`](65-webcrypto.md#class-eckeygenparams) | [`<HmacKeyGenParams>`](65-webcrypto.md#class-hmackeygenparams) | [`<AesKeyGenParams>`](65-webcrypto.md#class-aeskeygenparams) | [`<KmacKeyGenParams>`](65-webcrypto.md#class-kmackeygenparams)

- `extractable` [`<boolean>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#boolean_type)
- `keyUsages` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type)[] See [Key usages](#cryptokeyusages).
- Returns: [`<Promise>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Promise) Fulfills with a [`<CryptoKey>`](65-webcrypto.md#class-cryptokey) | [`<CryptoKeyPair>`](65-webcrypto.md#class-cryptokeypair) upon success.

Using the parameters provided in `algorithm`, this method
attempts to generate new keying material. Depending on the algorithm used
either a single [`<CryptoKey>`](65-webcrypto.md#class-cryptokey) or a [`<CryptoKeyPair>`](65-webcrypto.md#class-cryptokeypair) is generated.

The [`<CryptoKeyPair>`](65-webcrypto.md#class-cryptokeypair) (public and private key) generating algorithms supported
include:

- `'ECDH'`
- `'ECDSA'`
- `'Ed25519'`
- `'Ed448'`[5](#user-content-fn-secure-curves)
- `'ML-DSA-44'`[4](#user-content-fn-modern-algos)
- `'ML-DSA-65'`[4](#user-content-fn-modern-algos)
- `'ML-DSA-87'`[4](#user-content-fn-modern-algos)
- `'ML-KEM-512'`[4](#user-content-fn-modern-algos)
- `'ML-KEM-768'`[4](#user-content-fn-modern-algos)
- `'ML-KEM-1024'`[4](#user-content-fn-modern-algos)
- `'MLKEM768-P256'`[4](#user-content-fn-modern-algos)
- `'MLKEM768-X25519'`[4](#user-content-fn-modern-algos)
- `'MLKEM1024-P384'`[4](#user-content-fn-modern-algos)
- `'RSA-OAEP'`
- `'RSA-PSS'`
- `'RSASSA-PKCS1-v1_5'`
- `'X25519'`
- `'X448'`[5](#user-content-fn-secure-curves)

The [`<CryptoKey>`](65-webcrypto.md#class-cryptokey) (secret key) generating algorithms supported include:

- `'AES-CBC'`
- `'AES-CTR'`
- `'AES-GCM'`
- `'AES-KW'`
- `'AES-OCB'`[4](#user-content-fn-modern-algos)
- `'ChaCha20-Poly1305'`[4](#user-content-fn-modern-algos)
- `'HMAC'`
- `'KMAC128'`[4](#user-content-fn-modern-algos)
- `'KMAC256'`[4](#user-content-fn-modern-algos)

### `subtle.importKey(format, keyData, algorithm, extractable, keyUsages)`

Added in: v15.0.0

**History**

| Version | Changes |
| --- | --- |
| v26.10.0 | Hybrid KEM algorithms are now supported. |
| v26.1.0 | Added JWK format support for ML-KEM key types. |
| v25.9.0 | Importing ML-DSA and ML-KEM PKCS#8 keys without a seed is no longer supported. |
| v24.8.0 | KMAC algorithms are now supported. |
| v24.7.0 | ML-KEM algorithms are now supported. |
| v24.7.0 | ChaCha20-Poly1305 algorithm is now supported. |
| v24.7.0 | ML-DSA algorithms are now supported. |
| v18.4.0, v16.17.0 | Added `'Ed25519'`, `'Ed448'`, `'X25519'`, and `'X448'` algorithms. |
| v15.9.0 | Removed `'NODE-DSA'` JWK import. |

- `format` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) Must be one of `'raw'`, `'pkcs8'`, `'spki'`, `'jwk'`, `'raw-secret'`[4](#user-content-fn-modern-algos),
  `'raw-public'`[4](#user-content-fn-modern-algos), or `'raw-seed'`[4](#user-content-fn-modern-algos).
- `keyData` [`<ArrayBuffer>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/ArrayBuffer) | [`<TypedArray>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/TypedArray) | [`<DataView>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/DataView) | [`<Buffer>`](07-buffer-part1.md#class-buffer) | [`<Object>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Object)

- `algorithm` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) | [`<Algorithm>`](65-webcrypto.md#class-algorithm) | [`<RsaHashedImportParams>`](65-webcrypto.md#class-rsahashedimportparams) | [`<EcKeyImportParams>`](65-webcrypto.md#class-eckeyimportparams) | [`<HmacImportParams>`](65-webcrypto.md#class-hmacimportparams) | [`<KmacImportParams>`](65-webcrypto.md#class-kmacimportparams)

- `extractable` [`<boolean>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#boolean_type)
- `keyUsages` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type)[] See [Key usages](#cryptokeyusages).
- Returns: [`<Promise>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Promise) Fulfills with a [`<CryptoKey>`](65-webcrypto.md#class-cryptokey) upon success.

This method attempts to interpret the provided `keyData`
as the given `format` to create a [`<CryptoKey>`](65-webcrypto.md#class-cryptokey) instance using the provided `algorithm`, `extractable`, and `keyUsages` arguments. If the import is
successful, the returned promise will be resolved with a [`<CryptoKey>`](65-webcrypto.md#class-cryptokey)
representation of the key material.

If importing KDF algorithm keys, `extractable` must be `false`.

See [Key formats](#key-formats) for the algorithms and formats currently supported.

### `subtle.sign(algorithm, key, data)`

Added in: v15.0.0

**History**

| Version | Changes |
| --- | --- |
| v24.8.0 | KMAC algorithms are now supported. |
| v24.7.0 | ML-DSA algorithms are now supported. |
| v18.4.0, v16.17.0 | Added `'Ed25519'`, and `'Ed448'` algorithms. |

- `algorithm` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) | [`<Algorithm>`](65-webcrypto.md#class-algorithm) | [`<RsaPssParams>`](65-webcrypto.md#class-rsapssparams) | [`<EcdsaParams>`](65-webcrypto.md#class-ecdsaparams) | [`<ContextParams>`](65-webcrypto.md#class-contextparams) | [`<KmacParams>`](65-webcrypto.md#class-kmacparams)
- `key` [`<CryptoKey>`](65-webcrypto.md#class-cryptokey)
- `data` [`<ArrayBuffer>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/ArrayBuffer) | [`<TypedArray>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/TypedArray) | [`<DataView>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/DataView) | [`<Buffer>`](07-buffer-part1.md#class-buffer)
- Returns: [`<Promise>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Promise) Fulfills with an [`<ArrayBuffer>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/ArrayBuffer) upon success.

Using the method and parameters given by `algorithm` and the keying material
provided by `key`, this method attempts to generate a cryptographic
signature of `data`. If successful, the returned promise is resolved with
an [`<ArrayBuffer>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/ArrayBuffer) containing the generated signature.

The algorithms currently supported include:

- `'ECDSA'`
- `'Ed25519'`
- `'Ed448'`[5](#user-content-fn-secure-curves)
- `'HMAC'`
- `'KMAC128'`[4](#user-content-fn-modern-algos)
- `'KMAC256'`[4](#user-content-fn-modern-algos)
- `'ML-DSA-44'`[4](#user-content-fn-modern-algos)
- `'ML-DSA-65'`[4](#user-content-fn-modern-algos)
- `'ML-DSA-87'`[4](#user-content-fn-modern-algos)
- `'RSA-PSS'`
- `'RSASSA-PKCS1-v1_5'`

### `subtle.unwrapKey(format, wrappedKey, unwrappingKey, unwrapAlgorithm, unwrappedKeyAlgorithm, extractable, keyUsages)`

Added in: v15.0.0

**History**

| Version | Changes |
| --- | --- |
| v24.7.0 | AES-OCB algorithm is now supported. |
| v24.7.0 | ChaCha20-Poly1305 algorithm is now supported. |

- `format` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) Must be one of `'raw'`, `'pkcs8'`, `'spki'`, `'jwk'`, `'raw-secret'`[4](#user-content-fn-modern-algos),
  `'raw-public'`[4](#user-content-fn-modern-algos), or `'raw-seed'`[4](#user-content-fn-modern-algos).
- `wrappedKey` [`<ArrayBuffer>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/ArrayBuffer) | [`<TypedArray>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/TypedArray) | [`<DataView>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/DataView) | [`<Buffer>`](07-buffer-part1.md#class-buffer)
- `unwrappingKey` [`<CryptoKey>`](65-webcrypto.md#class-cryptokey)

- `unwrapAlgorithm` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) | [`<Algorithm>`](65-webcrypto.md#class-algorithm) | [`<RsaOaepParams>`](65-webcrypto.md#class-rsaoaepparams) | [`<AesCtrParams>`](65-webcrypto.md#class-aesctrparams) | [`<AesCbcParams>`](65-webcrypto.md#class-aescbcparams) | [`<AeadParams>`](65-webcrypto.md#class-aeadparams)
- `unwrappedKeyAlgorithm` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) | [`<Algorithm>`](65-webcrypto.md#class-algorithm) | [`<RsaHashedImportParams>`](65-webcrypto.md#class-rsahashedimportparams) | [`<EcKeyImportParams>`](65-webcrypto.md#class-eckeyimportparams) | [`<HmacImportParams>`](65-webcrypto.md#class-hmacimportparams) | [`<KmacImportParams>`](65-webcrypto.md#class-kmacimportparams)

- `extractable` [`<boolean>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#boolean_type)
- `keyUsages` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type)[] See [Key usages](#cryptokeyusages).
- Returns: [`<Promise>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Promise) Fulfills with a [`<CryptoKey>`](65-webcrypto.md#class-cryptokey) upon success.

In cryptography, "wrapping a key" refers to exporting and then encrypting the
keying material. This method attempts to decrypt a wrapped
key and create a [`<CryptoKey>`](65-webcrypto.md#class-cryptokey) instance. It is equivalent to calling [`subtle.decrypt()`](#subtledecryptalgorithm-key-data) first on the encrypted key data (using the `wrappedKey`,
`unwrapAlgorithm`, and `unwrappingKey` arguments as input) then passing the results
to the [`subtle.importKey()`](#subtleimportkeyformat-keydata-algorithm-extractable-keyusages) method using the `unwrappedKeyAlgorithm`,
`extractable`, and `keyUsages` arguments as inputs. If successful, the returned
promise is resolved with a [`<CryptoKey>`](65-webcrypto.md#class-cryptokey) object.

The wrapping algorithms currently supported include:

- `'AES-CBC'`
- `'AES-CTR'`
- `'AES-GCM'`
- `'AES-KW'`
- `'AES-OCB'`[4](#user-content-fn-modern-algos)
- `'ChaCha20-Poly1305'`[4](#user-content-fn-modern-algos)
- `'RSA-OAEP'`

The unwrapped key algorithms supported include:

- `'AES-CBC'`
- `'AES-CTR'`
- `'AES-GCM'`
- `'AES-KW'`
- `'AES-OCB'`[4](#user-content-fn-modern-algos)
- `'ChaCha20-Poly1305'`[4](#user-content-fn-modern-algos)
- `'ECDH'`
- `'ECDSA'`
- `'Ed25519'`
- `'Ed448'`[5](#user-content-fn-secure-curves)
- `'HMAC'`
- `'KMAC128'`[4](#user-content-fn-modern-algos)
- `'KMAC256'`[4](#user-content-fn-modern-algos)
- `'ML-DSA-44'`[4](#user-content-fn-modern-algos)
- `'ML-DSA-65'`[4](#user-content-fn-modern-algos)
- `'ML-DSA-87'`[4](#user-content-fn-modern-algos)
- `'ML-KEM-512'`[4](#user-content-fn-modern-algos)
- `'ML-KEM-768'`[4](#user-content-fn-modern-algos)
- `'ML-KEM-1024'`[4](#user-content-fn-modern-algos)
- `'MLKEM768-P256'`[4](#user-content-fn-modern-algos)
- `'MLKEM768-X25519'`[4](#user-content-fn-modern-algos)
- `'MLKEM1024-P384'`[4](#user-content-fn-modern-algos)
- `'RSA-OAEP'`
- `'RSA-PSS'`
- `'RSASSA-PKCS1-v1_5'`
- `'X25519'`
- `'X448'`[5](#user-content-fn-secure-curves)

### `subtle.verify(algorithm, key, signature, data)`

Added in: v15.0.0

**History**

| Version | Changes |
| --- | --- |
| v24.8.0 | KMAC algorithms are now supported. |
| v24.7.0 | ML-DSA algorithms are now supported. |
| v18.4.0, v16.17.0 | Added `'Ed25519'`, and `'Ed448'` algorithms. |

- `algorithm` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) | [`<Algorithm>`](65-webcrypto.md#class-algorithm) | [`<RsaPssParams>`](65-webcrypto.md#class-rsapssparams) | [`<EcdsaParams>`](65-webcrypto.md#class-ecdsaparams) | [`<ContextParams>`](65-webcrypto.md#class-contextparams) | [`<KmacParams>`](65-webcrypto.md#class-kmacparams)
- `key` [`<CryptoKey>`](65-webcrypto.md#class-cryptokey)
- `signature` [`<ArrayBuffer>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/ArrayBuffer) | [`<TypedArray>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/TypedArray) | [`<DataView>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/DataView) | [`<Buffer>`](07-buffer-part1.md#class-buffer)
- `data` [`<ArrayBuffer>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/ArrayBuffer) | [`<TypedArray>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/TypedArray) | [`<DataView>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/DataView) | [`<Buffer>`](07-buffer-part1.md#class-buffer)
- Returns: [`<Promise>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Promise) Fulfills with a [`<boolean>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#boolean_type) upon success.

Using the method and parameters given in `algorithm` and the keying material
provided by `key`, this method attempts to verify that `signature` is
a valid cryptographic signature of `data`. The returned promise is resolved
with either `true` or `false`.

The algorithms currently supported include:

- `'ECDSA'`
- `'Ed25519'`
- `'Ed448'`[5](#user-content-fn-secure-curves)
- `'HMAC'`
- `'KMAC128'`[4](#user-content-fn-modern-algos)
- `'KMAC256'`[4](#user-content-fn-modern-algos)
- `'ML-DSA-44'`[4](#user-content-fn-modern-algos)
- `'ML-DSA-65'`[4](#user-content-fn-modern-algos)
- `'ML-DSA-87'`[4](#user-content-fn-modern-algos)
- `'RSA-PSS'`
- `'RSASSA-PKCS1-v1_5'`

### `subtle.wrapKey(format, key, wrappingKey, wrapAlgorithm)`

Added in: v15.0.0

**History**

| Version | Changes |
| --- | --- |
| v24.7.0 | AES-OCB algorithm is now supported. |
| v24.7.0 | ChaCha20-Poly1305 algorithm is now supported. |

- `format` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) Must be one of `'raw'`, `'pkcs8'`, `'spki'`, `'jwk'`, `'raw-secret'`[4](#user-content-fn-modern-algos),
  `'raw-public'`[4](#user-content-fn-modern-algos), or `'raw-seed'`[4](#user-content-fn-modern-algos).
- `key` [`<CryptoKey>`](65-webcrypto.md#class-cryptokey)
- `wrappingKey` [`<CryptoKey>`](65-webcrypto.md#class-cryptokey)
- `wrapAlgorithm` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) | [`<Algorithm>`](65-webcrypto.md#class-algorithm) | [`<RsaOaepParams>`](65-webcrypto.md#class-rsaoaepparams) | [`<AesCtrParams>`](65-webcrypto.md#class-aesctrparams) | [`<AesCbcParams>`](65-webcrypto.md#class-aescbcparams) | [`<AeadParams>`](65-webcrypto.md#class-aeadparams)
- Returns: [`<Promise>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Promise) Fulfills with an [`<ArrayBuffer>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/ArrayBuffer) upon success.

In cryptography, "wrapping a key" refers to exporting and then encrypting the
keying material. This method exports the keying material into
the format identified by `format`, then encrypts it using the method and
parameters specified by `wrapAlgorithm` and the keying material provided by
`wrappingKey`. It is the equivalent to calling [`subtle.exportKey()`](#subtleexportkeyformat-key) using
`format` and `key` as the arguments, then passing the result to the
[`subtle.encrypt()`](#subtleencryptalgorithm-key-data) method using `wrappingKey` and `wrapAlgorithm` as inputs. If
successful, the returned promise will be resolved with an [`<ArrayBuffer>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/ArrayBuffer)
containing the encrypted key data.

The wrapping algorithms currently supported include:

- `'AES-CBC'`
- `'AES-CTR'`
- `'AES-GCM'`
- `'AES-KW'`
- `'AES-OCB'`[4](#user-content-fn-modern-algos)
- `'ChaCha20-Poly1305'`[4](#user-content-fn-modern-algos)
- `'RSA-OAEP'`

## Algorithm parameters

The algorithm parameter objects define the methods and parameters used by
the various [`<SubtleCrypto>`](65-webcrypto.md#class-subtlecrypto) methods. While described here as "classes", they
are simple JavaScript dictionary objects.

### Class: `Algorithm`

Added in: v15.0.0

#### `Algorithm.name`

Added in: v15.0.0

- Type: [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type)

### Class: `AeadParams`

Added in: v15.0.0

#### `aeadParams.additionalData`

Added in: v15.0.0

- Type: [`<ArrayBuffer>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/ArrayBuffer) | [`<TypedArray>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/TypedArray) | [`<DataView>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/DataView) | [`<Buffer>`](07-buffer-part1.md#class-buffer) | [`<undefined>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#undefined_type)

Extra input that is not encrypted but is included in the authentication
of the data. The use of `additionalData` is optional.

#### `aeadParams.iv`

Added in: v15.0.0

- Type: [`<ArrayBuffer>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/ArrayBuffer) | [`<TypedArray>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/TypedArray) | [`<DataView>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/DataView) | [`<Buffer>`](07-buffer-part1.md#class-buffer)

The initialization vector must be unique for every encryption operation using a
given key.

#### `aeadParams.name`

Added in: v15.0.0

- Type: [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) Must be `'AES-GCM'`, `'AES-OCB'`, or `'ChaCha20-Poly1305'`.

#### `aeadParams.tagLength`

Added in: v15.0.0

- Type: [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) The size in bits of the generated authentication tag.

### Class: `AesDerivedKeyParams`

Added in: v15.0.0

#### `aesDerivedKeyParams.name`

Added in: v15.0.0

- Type: [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) Must be one of `'AES-CBC'`, `'AES-CTR'`, `'AES-GCM'`, `'AES-OCB'`, or `'AES-KW'`

#### `aesDerivedKeyParams.length`

Added in: v15.0.0

- Type: [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type)

The length of the AES key to be derived. This must be either `128`, `192`,
or `256`.

### Class: `AesCbcParams`

Added in: v15.0.0

#### `aesCbcParams.iv`

Added in: v15.0.0

- Type: [`<ArrayBuffer>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/ArrayBuffer) | [`<TypedArray>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/TypedArray) | [`<DataView>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/DataView) | [`<Buffer>`](07-buffer-part1.md#class-buffer)

Provides the initialization vector. It must be exactly 16-bytes in length
and should be unpredictable and cryptographically random.

#### `aesCbcParams.name`

Added in: v15.0.0

- Type: [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) Must be `'AES-CBC'`.

### Class: `AesCtrParams`

Added in: v15.0.0

#### `aesCtrParams.counter`

Added in: v15.0.0

- Type: [`<ArrayBuffer>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/ArrayBuffer) | [`<TypedArray>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/TypedArray) | [`<DataView>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/DataView) | [`<Buffer>`](07-buffer-part1.md#class-buffer)

The initial value of the counter block. This must be exactly 16 bytes long.

The `AES-CTR` method uses the rightmost `length` bits of the block as the
counter and the remaining bits as the nonce.

#### `aesCtrParams.length`

Added in: v15.0.0

- Type: [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) The number of bits in the `aesCtrParams.counter` that are
  to be used as the counter.

#### `aesCtrParams.name`

Added in: v15.0.0

- Type: [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) Must be `'AES-CTR'`.

### Class: `AesKeyAlgorithm`

Added in: v15.0.0

#### `aesKeyAlgorithm.length`

Added in: v15.0.0

- Type: [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type)

The length of the AES key in bits.

#### `aesKeyAlgorithm.name`

Added in: v15.0.0

- Type: [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type)

### Class: `AesKeyGenParams`

Added in: v15.0.0

#### `aesKeyGenParams.length`

Added in: v15.0.0

- Type: [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type)

The length of the AES key to be generated. This must be either `128`, `192`,
or `256`.

#### `aesKeyGenParams.name`

Added in: v15.0.0

- Type: [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) Must be one of `'AES-CBC'`, `'AES-CTR'`, `'AES-GCM'`, or
  `'AES-KW'`

### Class: `Argon2Params`

Added in: v24.8.0

#### `argon2Params.associatedData`

Added in: v24.8.0

- Type: [`<ArrayBuffer>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/ArrayBuffer) | [`<TypedArray>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/TypedArray) | [`<DataView>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/DataView) | [`<Buffer>`](07-buffer-part1.md#class-buffer)

Represents the optional associated data.

#### `argon2Params.memory`

Added in: v24.8.0

- Type: [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type)

Represents the memory size in kibibytes. It must be at least 8 times the degree of parallelism.

#### `argon2Params.name`

Added in: v24.8.0

- Type: [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) Must be one of `'Argon2d'`, `'Argon2i'`, or `'Argon2id'`.

#### `argon2Params.nonce`

Added in: v24.8.0

- Type: [`<ArrayBuffer>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/ArrayBuffer) | [`<TypedArray>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/TypedArray) | [`<DataView>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/DataView) | [`<Buffer>`](07-buffer-part1.md#class-buffer)

Represents the nonce, which is a salt for password hashing applications.

#### `argon2Params.parallelism`

Added in: v24.8.0

- Type: [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type)

Represents the degree of parallelism.

#### `argon2Params.passes`

Added in: v24.8.0

- Type: [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type)

Represents the number of passes.

#### `argon2Params.secretValue`

Added in: v24.8.0

- Type: [`<ArrayBuffer>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/ArrayBuffer) | [`<TypedArray>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/TypedArray) | [`<DataView>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/DataView) | [`<Buffer>`](07-buffer-part1.md#class-buffer)

Represents the optional secret value.

#### `argon2Params.version`

Added in: v24.8.0

- Type: [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type)

Represents the Argon2 version number. The default and currently only defined version is `19` (`0x13`).

### Class: `ContextParams`

Added in: v24.7.0

#### `contextParams.name`

Added in: v24.7.0

- Type: [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) Must be `'Ed448'`[5](#user-content-fn-secure-curves), `'ML-DSA-44'`[4](#user-content-fn-modern-algos),
  `'ML-DSA-65'`[4](#user-content-fn-modern-algos), or `'ML-DSA-87'`[4](#user-content-fn-modern-algos).

#### `contextParams.context`

Added in: v24.7.0

**History**

| Version | Changes |
| --- | --- |
| v24.8.0 | Non-empty context is now supported. |

- Type: [`<ArrayBuffer>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/ArrayBuffer) | [`<TypedArray>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/TypedArray) | [`<DataView>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/DataView) | [`<Buffer>`](07-buffer-part1.md#class-buffer) | [`<undefined>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#undefined_type)

The `context` member represents the optional context data to associate with
the message.

### Class: `CShakeParams`

Added in: v24.7.0

**History**

| Version | Changes |
| --- | --- |
| v25.9.0 | Renamed `cShakeParams.length` to `cShakeParams.outputLength`. |

When both `functionName` and `customization` are empty or `undefined`, cSHAKE is
equivalent to plain SHAKE.

#### `cShakeParams.name`

Added in: v24.7.0

- Type: [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) Must be `'cSHAKE128'`[4](#user-content-fn-modern-algos) or `'cSHAKE256'`[4](#user-content-fn-modern-algos).

#### `cShakeParams.outputLength`

Added in: v25.9.0

- Type: [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) represents the requested output length in bits. Must be a
  multiple of 8.

#### `cShakeParams.functionName`

Added in: v24.7.0

**History**

| Version | Changes |
| --- | --- |
| v26.4.0 | Named cSHAKE variants are now accepted. |

- Type: [`<ArrayBuffer>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/ArrayBuffer) | [`<TypedArray>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/TypedArray) | [`<DataView>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/DataView) | [`<Buffer>`](07-buffer-part1.md#class-buffer) | [`<undefined>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#undefined_type)

The `functionName` member represents the NIST function-name byte string used to
domain-separate functions built on top of cSHAKE. Non-empty values require
OpenSSL 4.0 or later. Accepted values are:

- empty or `undefined`
- the ASCII byte sequence `'KMAC'`
- the ASCII byte sequence `'TupleHash'`
- the ASCII byte sequence `'ParallelHash'`

#### `cShakeParams.customization`

Added in: v24.7.0

**History**

| Version | Changes |
| --- | --- |
| v26.4.0 | Non-empty customization is now supported. |

- Type: [`<ArrayBuffer>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/ArrayBuffer) | [`<TypedArray>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/TypedArray) | [`<DataView>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/DataView) | [`<Buffer>`](07-buffer-part1.md#class-buffer) | [`<undefined>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#undefined_type)

The `customization` member represents the customization data. Non-empty values
require OpenSSL 4.0 or later. Accepted values are:

- empty or `undefined`
- up to 512 bytes of data without null bytes

### Class: `EcdhKeyDeriveParams`

Added in: v15.0.0

#### `ecdhKeyDeriveParams.name`

Added in: v15.0.0

- Type: [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) Must be `'ECDH'`, `'X25519'`, or `'X448'`[5](#user-content-fn-secure-curves).

#### `ecdhKeyDeriveParams.public`

Added in: v15.0.0

- Type: [`<CryptoKey>`](65-webcrypto.md#class-cryptokey)

ECDH key derivation operates by taking as input one party's private key and
another party's public key -- using both to generate a common shared secret.
The `ecdhKeyDeriveParams.public` property is set to the other party's public
key.

### Class: `EcdsaParams`

Added in: v15.0.0

#### `ecdsaParams.hash`

Added in: v15.0.0

**History**

| Version | Changes |
| --- | --- |
| v24.7.0 | SHA-3 algorithms are now supported. |

- Type: [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) | [`<Algorithm>`](65-webcrypto.md#class-algorithm)

If represented as a [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type), the value must be one of:

- `'SHA-1'`
- `'SHA-256'`
- `'SHA-384'`
- `'SHA-512'`
- `'SHA3-256'`[4](#user-content-fn-modern-algos)
- `'SHA3-384'`[4](#user-content-fn-modern-algos)
- `'SHA3-512'`[4](#user-content-fn-modern-algos)

If represented as an [`<Algorithm>`](65-webcrypto.md#class-algorithm), the object's `name` property
must be one of the above listed values.

#### `ecdsaParams.name`

Added in: v15.0.0

- Type: [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) Must be `'ECDSA'`.

### Class: `EcKeyAlgorithm`

Added in: v15.0.0

#### `ecKeyAlgorithm.name`

Added in: v15.0.0

- Type: [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type)

#### `ecKeyAlgorithm.namedCurve`

Added in: v15.0.0

- Type: [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type)

### Class: `EcKeyGenParams`

Added in: v15.0.0

#### `ecKeyGenParams.name`

Added in: v15.0.0

- Type: [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) Must be one of `'ECDSA'` or `'ECDH'`.

#### `ecKeyGenParams.namedCurve`

Added in: v15.0.0

- Type: [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) Must be one of `'P-256'`, `'P-384'`, `'P-521'`.

### Class: `EcKeyImportParams`

Added in: v15.0.0

#### `ecKeyImportParams.name`

Added in: v15.0.0

- Type: [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) Must be one of `'ECDSA'` or `'ECDH'`.

#### `ecKeyImportParams.namedCurve`

Added in: v15.0.0

- Type: [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) Must be one of `'P-256'`, `'P-384'`, `'P-521'`.

### Class: `EncapsulatedBits`

Added in: v24.7.0

A temporary symmetric secret key (represented as [`<ArrayBuffer>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/ArrayBuffer)) for message encryption
and the ciphertext (that can be transmitted to the message recipient along with the
message) encrypted by this shared key. The recipient uses their private key to determine
what the shared key is which then allows them to decrypt the message.

#### `encapsulatedBits.ciphertext`

Added in: v24.7.0

- Type: [`<ArrayBuffer>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/ArrayBuffer)

#### `encapsulatedBits.sharedKey`

Added in: v24.7.0

- Type: [`<ArrayBuffer>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/ArrayBuffer)

### Class: `EncapsulatedKey`

Added in: v24.7.0

A temporary symmetric secret key (represented as [`<CryptoKey>`](65-webcrypto.md#class-cryptokey)) for message encryption
and the ciphertext (that can be transmitted to the message recipient along with the
message) encrypted by this shared key. The recipient uses their private key to determine
what the shared key is which then allows them to decrypt the message.

#### `encapsulatedKey.ciphertext`

Added in: v24.7.0

- Type: [`<ArrayBuffer>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/ArrayBuffer)

#### `encapsulatedKey.sharedKey`

Added in: v24.7.0

- Type: [`<CryptoKey>`](65-webcrypto.md#class-cryptokey)

### Class: `HkdfParams`

Added in: v15.0.0

#### `hkdfParams.hash`

Added in: v15.0.0

**History**

| Version | Changes |
| --- | --- |
| v24.7.0 | SHA-3 algorithms are now supported. |

- Type: [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) | [`<Algorithm>`](65-webcrypto.md#class-algorithm)

If represented as a [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type), the value must be one of:

- `'SHA-1'`
- `'SHA-256'`
- `'SHA-384'`
- `'SHA-512'`
- `'SHA3-256'`[4](#user-content-fn-modern-algos)
- `'SHA3-384'`[4](#user-content-fn-modern-algos)
- `'SHA3-512'`[4](#user-content-fn-modern-algos)

If represented as an [`<Algorithm>`](65-webcrypto.md#class-algorithm), the object's `name` property
must be one of the above listed values.

#### `hkdfParams.info`

Added in: v15.0.0

- Type: [`<ArrayBuffer>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/ArrayBuffer) | [`<TypedArray>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/TypedArray) | [`<DataView>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/DataView) | [`<Buffer>`](07-buffer-part1.md#class-buffer)

Provides application-specific contextual input to the HKDF algorithm.
This can be zero-length but must be provided.

#### `hkdfParams.name`

Added in: v15.0.0

- Type: [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) Must be `'HKDF'`.

#### `hkdfParams.salt`

Added in: v15.0.0

- Type: [`<ArrayBuffer>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/ArrayBuffer) | [`<TypedArray>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/TypedArray) | [`<DataView>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/DataView) | [`<Buffer>`](07-buffer-part1.md#class-buffer)

The salt value significantly improves the strength of the HKDF algorithm.
It should be random or pseudorandom and should be the same length as the
output of the digest function (for instance, if using `'SHA-256'` as the
digest, the salt should be 256-bits of random data).

### Class: `HmacImportParams`

Added in: v15.0.0

#### `hmacImportParams.hash`

Added in: v15.0.0

**History**

| Version | Changes |
| --- | --- |
| v24.7.0 | SHA-3 algorithms are now supported. |

- Type: [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) | [`<Algorithm>`](65-webcrypto.md#class-algorithm)

If represented as a [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type), the value must be one of:

- `'SHA-1'`
- `'SHA-256'`
- `'SHA-384'`
- `'SHA-512'`
- `'SHA3-256'`[4](#user-content-fn-modern-algos)
- `'SHA3-384'`[4](#user-content-fn-modern-algos)
- `'SHA3-512'`[4](#user-content-fn-modern-algos)

If represented as an [`<Algorithm>`](65-webcrypto.md#class-algorithm), the object's `name` property
must be one of the above listed values.

#### `hmacImportParams.length`

Added in: v15.0.0

- Type: [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type)

The optional number of bits in the HMAC key. This is optional and should
be omitted for most cases.

#### `hmacImportParams.name`

Added in: v15.0.0

- Type: [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) Must be `'HMAC'`.

### Class: `HmacKeyAlgorithm`

Added in: v15.0.0

#### `hmacKeyAlgorithm.hash`

Added in: v15.0.0

- Type: [`<Algorithm>`](65-webcrypto.md#class-algorithm)

#### `hmacKeyAlgorithm.length`

Added in: v15.0.0

- Type: [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type)

The length of the HMAC key in bits.

#### `hmacKeyAlgorithm.name`

Added in: v15.0.0

- Type: [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type)

### Class: `HmacKeyGenParams`

Added in: v15.0.0

#### `hmacKeyGenParams.hash`

Added in: v15.0.0

**History**

| Version | Changes |
| --- | --- |
| v24.7.0 | SHA-3 algorithms are now supported. |

- Type: [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) | [`<Algorithm>`](65-webcrypto.md#class-algorithm)

If represented as a [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type), the value must be one of:

- `'SHA-1'`
- `'SHA-256'`
- `'SHA-384'`
- `'SHA-512'`
- `'SHA3-256'`[4](#user-content-fn-modern-algos)
- `'SHA3-384'`[4](#user-content-fn-modern-algos)
- `'SHA3-512'`[4](#user-content-fn-modern-algos)

If represented as an [`<Algorithm>`](65-webcrypto.md#class-algorithm), the object's `name` property
must be one of the above listed values.

#### `hmacKeyGenParams.length`

Added in: v15.0.0

- Type: [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type)

The number of bits to generate for the HMAC key. If omitted,
the length will be determined by the hash algorithm used.
This is optional and should be omitted for most cases.

#### `hmacKeyGenParams.name`

Added in: v15.0.0

- Type: [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) Must be `'HMAC'`.

### Class: `KeyAlgorithm`

Added in: v15.0.0

#### `keyAlgorithm.name`

Added in: v15.0.0

- Type: [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type)

### Class: `KangarooTwelveParams`

Added in: v25.9.0

#### `kangarooTwelveParams.customization`

Added in: v25.9.0

**History**

| Version | Changes |
| --- | --- |
| v26.6.0 | Limit customization to 512 bytes. |

- Type: [`<ArrayBuffer>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/ArrayBuffer) | [`<TypedArray>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/TypedArray) | [`<DataView>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/DataView) | [`<Buffer>`](07-buffer-part1.md#class-buffer) | [`<undefined>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#undefined_type)

The optional customization string for KangarooTwelve. It must not exceed 512
bytes.

#### `kangarooTwelveParams.name`

Added in: v25.9.0

- Type: [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) Must be `'KT128'`[4](#user-content-fn-modern-algos) or `'KT256'`[4](#user-content-fn-modern-algos).

#### `kangarooTwelveParams.outputLength`

Added in: v25.9.0

- Type: [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) represents the requested output length in bits.

### Class: `KmacImportParams`

Added in: v24.8.0

#### `kmacImportParams.length`

Added in: v24.8.0

- Type: [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type)

The optional number of bits in the KMAC key. This is optional and should
be omitted for most cases. The key length must be at least 32 and a multiple of 8.

#### `kmacImportParams.name`

Added in: v24.8.0

- Type: [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) Must be `'KMAC128'` or `'KMAC256'`.

### Class: `KmacKeyAlgorithm`

Added in: v24.8.0

#### `kmacKeyAlgorithm.length`

Added in: v24.8.0

- Type: [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type)

The length of the KMAC key in bits.

#### `kmacKeyAlgorithm.name`

Added in: v24.8.0

- Type: [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type)

### Class: `KmacKeyGenParams`

Added in: v24.8.0

#### `kmacKeyGenParams.length`

Added in: v24.8.0

- Type: [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type)

The number of bits to generate for the KMAC key. If omitted,
the length will be determined by the KMAC algorithm used.
This is optional and should be omitted for most cases. Must be at least 32 and a
multiple of 8.

#### `kmacKeyGenParams.name`

Added in: v24.8.0

- Type: [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) Must be `'KMAC128'` or `'KMAC256'`.

### Class: `KmacParams`

Added in: v24.8.0

**History**

| Version | Changes |
| --- | --- |
| v25.9.0 | Renamed `kmacParams.length` to `kmacParams.outputLength`. |

#### `kmacParams.algorithm`

Added in: v24.8.0

- Type: [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) Must be `'KMAC128'` or `'KMAC256'`.

#### `kmacParams.outputLength`

Added in: v25.9.0

- Type: [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) represents the requested output length in bits. Must be a
  multiple of 8.

#### `kmacParams.customization`

Added in: v24.8.0

- Type: [`<ArrayBuffer>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/ArrayBuffer) | [`<TypedArray>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/TypedArray) | [`<DataView>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/DataView) | [`<Buffer>`](07-buffer-part1.md#class-buffer) | [`<undefined>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#undefined_type)

The `customization` member represents the optional customization string.

### Class: `Pbkdf2Params`

Added in: v15.0.0

#### `pbkdf2Params.hash`

Added in: v15.0.0

**History**

| Version | Changes |
| --- | --- |
| v24.7.0 | SHA-3 algorithms are now supported. |

- Type: [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) | [`<Algorithm>`](65-webcrypto.md#class-algorithm)

If represented as a [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type), the value must be one of:

- `'SHA-1'`
- `'SHA-256'`
- `'SHA-384'`
- `'SHA-512'`
- `'SHA3-256'`[4](#user-content-fn-modern-algos)
- `'SHA3-384'`[4](#user-content-fn-modern-algos)
- `'SHA3-512'`[4](#user-content-fn-modern-algos)

If represented as an [`<Algorithm>`](65-webcrypto.md#class-algorithm), the object's `name` property
must be one of the above listed values.

#### `pbkdf2Params.iterations`

Added in: v15.0.0

- Type: [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type)

The number of iterations the PBKDF2 algorithm should make when deriving bits.

#### `pbkdf2Params.name`

Added in: v15.0.0

- Type: [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) Must be `'PBKDF2'`.

#### `pbkdf2Params.salt`

Added in: v15.0.0

- Type: [`<ArrayBuffer>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/ArrayBuffer) | [`<TypedArray>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/TypedArray) | [`<DataView>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/DataView) | [`<Buffer>`](07-buffer-part1.md#class-buffer)

Should be at least 16 random or pseudorandom bytes.

### Class: `RsaHashedImportParams`

Added in: v15.0.0

#### `rsaHashedImportParams.hash`

Added in: v15.0.0

**History**

| Version | Changes |
| --- | --- |
| v24.7.0 | SHA-3 algorithms are now supported. |

- Type: [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) | [`<Algorithm>`](65-webcrypto.md#class-algorithm)

If represented as a [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type), the value must be one of:

- `'SHA-1'`
- `'SHA-256'`
- `'SHA-384'`
- `'SHA-512'`
- `'SHA3-256'`[4](#user-content-fn-modern-algos)
- `'SHA3-384'`[4](#user-content-fn-modern-algos)
- `'SHA3-512'`[4](#user-content-fn-modern-algos)

If represented as an [`<Algorithm>`](65-webcrypto.md#class-algorithm), the object's `name` property
must be one of the above listed values.

#### `rsaHashedImportParams.name`

Added in: v15.0.0

- Type: [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) Must be one of `'RSASSA-PKCS1-v1_5'`, `'RSA-PSS'`, or
  `'RSA-OAEP'`.

### Class: `RsaHashedKeyAlgorithm`

Added in: v15.0.0

#### `rsaHashedKeyAlgorithm.hash`

Added in: v15.0.0

- Type: [`<Algorithm>`](65-webcrypto.md#class-algorithm)

#### `rsaHashedKeyAlgorithm.modulusLength`

Added in: v15.0.0

- Type: [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type)

The length in bits of the RSA modulus.

#### `rsaHashedKeyAlgorithm.name`

Added in: v15.0.0

- Type: [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type)

#### `rsaHashedKeyAlgorithm.publicExponent`

Added in: v15.0.0

- Type: [`<Uint8Array>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Uint8Array)

The RSA public exponent.

### Class: `RsaHashedKeyGenParams`

Added in: v15.0.0

#### `rsaHashedKeyGenParams.hash`

Added in: v15.0.0

**History**

| Version | Changes |
| --- | --- |
| v24.7.0 | SHA-3 algorithms are now supported. |

- Type: [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) | [`<Algorithm>`](65-webcrypto.md#class-algorithm)

If represented as a [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type), the value must be one of:

- `'SHA-1'`
- `'SHA-256'`
- `'SHA-384'`
- `'SHA-512'`
- `'SHA3-256'`[4](#user-content-fn-modern-algos)
- `'SHA3-384'`[4](#user-content-fn-modern-algos)
- `'SHA3-512'`[4](#user-content-fn-modern-algos)

If represented as an [`<Algorithm>`](65-webcrypto.md#class-algorithm), the object's `name` property
must be one of the above listed values.

#### `rsaHashedKeyGenParams.modulusLength`

Added in: v15.0.0

- Type: [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type)

The length in bits of the RSA modulus. As a best practice, this should be
at least `2048`.

#### `rsaHashedKeyGenParams.name`

Added in: v15.0.0

- Type: [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) Must be one of `'RSASSA-PKCS1-v1_5'`, `'RSA-PSS'`, or
  `'RSA-OAEP'`.

#### `rsaHashedKeyGenParams.publicExponent`

Added in: v15.0.0

- Type: [`<Uint8Array>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Uint8Array)

The RSA public exponent. This must be a [`<Uint8Array>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Uint8Array) containing a big-endian,
unsigned integer that must fit within 32-bits. The [`<Uint8Array>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Uint8Array) may contain an
arbitrary number of leading zero-bits. The value must be a prime number. Unless
there is reason to use a different value, use `new Uint8Array([1, 0, 1])`
(65537) as the public exponent.

### Class: `RsaOaepParams`

Added in: v15.0.0

#### `rsaOaepParams.label`

Added in: v15.0.0

- Type: [`<ArrayBuffer>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/ArrayBuffer) | [`<TypedArray>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/TypedArray) | [`<DataView>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/DataView) | [`<Buffer>`](07-buffer-part1.md#class-buffer)

An additional collection of bytes that will not be encrypted, but will be bound
to the generated ciphertext.

The `rsaOaepParams.label` parameter is optional.

#### `rsaOaepParams.name`

Added in: v15.0.0

- Type: [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) must be `'RSA-OAEP'`.

### Class: `RsaPssParams`

Added in: v15.0.0

#### `rsaPssParams.name`

Added in: v15.0.0

- Type: [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) Must be `'RSA-PSS'`.

#### `rsaPssParams.saltLength`

Added in: v15.0.0

- Type: [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type)

The length (in bytes) of the random salt to use.

### Class: `TurboShakeParams`

Added in: v25.9.0

#### `turboShakeParams.domainSeparation`

Added in: v25.9.0

- Type: [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) | [`<undefined>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#undefined_type)

The optional domain separation byte (0x01-0x7f). Defaults to `0x1f`.

#### `turboShakeParams.name`

Added in: v25.9.0

- Type: [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) Must be `'TurboSHAKE128'`[4](#user-content-fn-modern-algos) or `'TurboSHAKE256'`[4](#user-content-fn-modern-algos).

#### `turboShakeParams.outputLength`

Added in: v25.9.0

- Type: [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) represents the requested output length in bits.

## Footnotes

1. Requires OpenSSL >= 3.0
2. Requires OpenSSL >= 3.2
3. Requires OpenSSL >= 3.5
4. See [Modern Algorithms in the Web Cryptography API](#modern-algorithms-in-the-web-cryptography-api)
5. See [Secure Curves in the Web Cryptography API](#secure-curves-in-the-web-cryptography-api)

[Contents](../README.md) | [Previous: WASI](64-wasi.md) | [Next: Web Streams API](66-webstreams.md)
