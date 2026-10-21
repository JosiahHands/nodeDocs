[Contents](../README.md) | [Previous: Crypto, part 3 of 6](15-crypto-part3.md) | [Next: Crypto, part 5 of 6](15-crypto-part5.md)

Source: [https://nodejs.org/docs/latest/api/crypto.html](https://nodejs.org/docs/latest/api/crypto.html)

Part 4 of 6 of this section.

# Crypto (part 4 of 6)

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

- `key` [`<Object>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Object) | [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) | [`<ArrayBuffer>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/ArrayBuffer) | [`<Buffer>`](07-buffer-part1.md#class-buffer) | [`<TypedArray>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/TypedArray) | [`<DataView>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/DataView)
  - `key` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) | [`<ArrayBuffer>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/ArrayBuffer) | [`<Buffer>`](07-buffer-part1.md#class-buffer) | [`<TypedArray>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/TypedArray) | [`<DataView>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/DataView) | [`<Object>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Object) The key
    material, either in PEM, DER, JWK, or raw format.
  - `format` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) Must be `'pem'`, `'der'`, `'jwk'`, or `'raw-public'`.
    **Default:** `'pem'`.
  - `type` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) Must be `'pkcs1'` or `'spki'`. This option is
    required only if the `format` is `'der'` and ignored otherwise.
  - `encoding` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) The string encoding to use when `key` is a string.
  - `asymmetricKeyType` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) Required when `format` is `'raw-public'`
    and ignored otherwise.
    Must be a [supported key type](15-crypto-part1.md#asymmetric-key-types).
  - `namedCurve` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) Name of the curve to use. Required when `asymmetricKeyType` is `'ec'` and ignored otherwise.
- Returns: [`<KeyObject>`](15-crypto-part2.md#class-keyobject)

Creates and returns a new key object containing a public key. If `key` is a
string or `Buffer`, `format` is assumed to be `'pem'`; if `key` is a `KeyObject`
with type `'private'`, the public key is derived from the given private key;
otherwise, `key` must be an object with the properties described above.

If the format is `'pem'`, the `'key'` may also be an X.509 certificate.

Because public keys can be derived from private keys, a private key may be
passed instead of a public key. In that case, this function behaves as if
[`crypto.createPrivateKey()`](15-crypto-part3.md#cryptocreateprivatekeykey) had been called, except that the type of the
returned `KeyObject` will be `'public'` and that the private key cannot be
extracted from the returned `KeyObject`. Similarly, if a `KeyObject` with type
`'private'` is given, a new `KeyObject` with type `'public'` will be returned
and it will be impossible to extract the private key from the returned object.

A store-backed private key can be used as a public key by first loading it with
[`crypto.createPrivateKey()`](15-crypto-part3.md#cryptocreateprivatekeykey); a [`<URL>`](59-url.md#the-whatwg-url-api) cannot be passed to `crypto.createPublicKey()` directly.

### `crypto.createSecretKey(key[, encoding])`

Added in: v11.6.0

**History**

| Version | Changes |
| --- | --- |
| v18.8.0, v16.18.0 | The key can now be zero-length. |
| v15.0.0 | The key can also be an ArrayBuffer or string. The encoding argument was added. The key cannot contain more than 2 \*\* 32 - 1 bytes. |

- `key` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) | [`<ArrayBuffer>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/ArrayBuffer) | [`<Buffer>`](07-buffer-part1.md#class-buffer) | [`<TypedArray>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/TypedArray) | [`<DataView>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/DataView)
- `encoding` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) The string encoding when `key` is a string.
- Returns: [`<KeyObject>`](15-crypto-part2.md#class-keyobject)

Creates and returns a new key object containing a secret key for symmetric
encryption or `Hmac`.

### `crypto.createSign(algorithm[, options])`

Added in: v0.1.92

- `algorithm` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type)
- `options` [`<Object>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Object) [`stream.Writable` options](51-stream-part3.md#new-streamwritableoptions)
- Returns: [`<Sign>`](15-crypto-part2.md#class-sign)

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
- `options` [`<Object>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Object) [`stream.Writable` options](51-stream-part3.md#new-streamwritableoptions)
- Returns: [`<Verify>`](15-crypto-part2.md#class-verify)

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

- `key` [`<Object>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Object) | [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) | [`<ArrayBuffer>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/ArrayBuffer) | [`<Buffer>`](07-buffer-part1.md#class-buffer) | [`<TypedArray>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/TypedArray) | [`<DataView>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/DataView) | [`<KeyObject>`](15-crypto-part2.md#class-keyobject) | [`<URL>`](59-url.md#the-whatwg-url-api) Private Key
- `ciphertext` [`<ArrayBuffer>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/ArrayBuffer) | [`<Buffer>`](07-buffer-part1.md#class-buffer) | [`<TypedArray>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/TypedArray) | [`<DataView>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/DataView)
- `callback` [`<Function>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Function)
  - `err` [`<Error>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Error)
  - `sharedKey` [`<Buffer>`](07-buffer-part1.md#class-buffer)
- Returns: [`<Buffer>`](07-buffer-part1.md#class-buffer) if the `callback` function is not provided.

Key decapsulation using a KEM algorithm with a private key.

Supported key types and their KEM algorithms are:

- `'rsa'`[2](#user-content-fn-openssl30) RSA Secret Value Encapsulation
- `'ec'`[3](#user-content-fn-openssl32) DHKEM(P-256, HKDF-SHA256), DHKEM(P-384, HKDF-SHA256), DHKEM(P-521, HKDF-SHA256)
- `'x25519'`[3](#user-content-fn-openssl32) DHKEM(X25519, HKDF-SHA256)
- `'x448'`[3](#user-content-fn-openssl32) DHKEM(X448, HKDF-SHA512)
- `'ml-kem-512'`[1](#user-content-fn-openssl35) ML-KEM
- `'ml-kem-768'`[1](#user-content-fn-openssl35) ML-KEM
- `'ml-kem-1024'`[1](#user-content-fn-openssl35) ML-KEM

If `key` is not a [`KeyObject`](15-crypto-part2.md#class-keyobject), this function behaves as if `key` had been
passed to [`crypto.createPrivateKey()`](15-crypto-part3.md#cryptocreateprivatekeykey).

If the `callback` function is provided this function uses libuv's threadpool.

### `crypto.diffieHellman(options[, callback])`

Added in: v13.9.0, v12.17.0

**History**

| Version | Changes |
| --- | --- |
| v26.1.0 | Accept key data in addition to KeyObject instances. |
| v23.11.0 | Optional callback argument added. |

- `options` [`<Object>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Object)
  - `privateKey` [`<Object>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Object) | [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) | [`<ArrayBuffer>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/ArrayBuffer) | [`<Buffer>`](07-buffer-part1.md#class-buffer) | [`<TypedArray>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/TypedArray) | [`<DataView>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/DataView) | [`<KeyObject>`](15-crypto-part2.md#class-keyobject) | [`<URL>`](59-url.md#the-whatwg-url-api)
  - `publicKey` [`<Object>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Object) | [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) | [`<ArrayBuffer>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/ArrayBuffer) | [`<Buffer>`](07-buffer-part1.md#class-buffer) | [`<TypedArray>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/TypedArray) | [`<DataView>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/DataView) | [`<KeyObject>`](15-crypto-part2.md#class-keyobject)
- `callback` [`<Function>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Function)
  - `err` [`<Error>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Error)
  - `secret` [`<Buffer>`](07-buffer-part1.md#class-buffer)
- Returns: [`<Buffer>`](07-buffer-part1.md#class-buffer) if the `callback` function is not provided.

Computes the Diffie-Hellman shared secret based on a `privateKey` and a `publicKey`.
Both keys must represent the same asymmetric key type and must support either the DH or
ECDH operation.

If `options.privateKey` is not a [`KeyObject`](15-crypto-part2.md#class-keyobject), this function behaves as if
`options.privateKey` had been passed to [`crypto.createPrivateKey()`](15-crypto-part3.md#cryptocreateprivatekeykey).

If `options.publicKey` is not a [`KeyObject`](15-crypto-part2.md#class-keyobject), this function behaves as if
`options.publicKey` had been passed to [`crypto.createPublicKey()`](#cryptocreatepublickeykey).

If the `callback` function is provided this function uses libuv's threadpool.

### `crypto.encapsulate(key[, callback])`

Added in: v24.7.0

- `key` [`<Object>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Object) | [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) | [`<ArrayBuffer>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/ArrayBuffer) | [`<Buffer>`](07-buffer-part1.md#class-buffer) | [`<TypedArray>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/TypedArray) | [`<DataView>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/DataView) | [`<KeyObject>`](15-crypto-part2.md#class-keyobject) Public Key
- `callback` [`<Function>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Function)
  - `err` [`<Error>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Error)
  - `result` [`<Object>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Object)
    - `sharedKey` [`<Buffer>`](07-buffer-part1.md#class-buffer)
    - `ciphertext` [`<Buffer>`](07-buffer-part1.md#class-buffer)
- Returns: [`<Object>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Object) if the `callback` function is not provided.
  - `sharedKey` [`<Buffer>`](07-buffer-part1.md#class-buffer)
  - `ciphertext` [`<Buffer>`](07-buffer-part1.md#class-buffer)

Key encapsulation using a KEM algorithm with a public key.

Supported key types and their KEM algorithms are:

- `'rsa'`[2](#user-content-fn-openssl30) RSA Secret Value Encapsulation
- `'ec'`[3](#user-content-fn-openssl32) DHKEM(P-256, HKDF-SHA256), DHKEM(P-384, HKDF-SHA256), DHKEM(P-521, HKDF-SHA256)
- `'x25519'`[3](#user-content-fn-openssl32) DHKEM(X25519, HKDF-SHA256)
- `'x448'`[3](#user-content-fn-openssl32) DHKEM(X448, HKDF-SHA512)
- `'ml-kem-512'`[1](#user-content-fn-openssl35) ML-KEM
- `'ml-kem-768'`[1](#user-content-fn-openssl35) ML-KEM
- `'ml-kem-1024'`[1](#user-content-fn-openssl35) ML-KEM

If `key` is not a [`KeyObject`](15-crypto-part2.md#class-keyobject), this function behaves as if `key` had been
passed to [`crypto.createPublicKey()`](#cryptocreatepublickeykey).

If the `callback` function is provided this function uses libuv's threadpool.

### `crypto.fips`

Added in: v6.0.0Deprecated in: v10.0.0

Stability: 0 - Deprecated

Deprecated property for checking and controlling [FIPS mode](15-crypto-part6.md#fips-mode). Use
[`crypto.getFips()`](#cryptogetfips) and [`crypto.setFips()`](15-crypto-part5.md#cryptosetfipsbool) instead.

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
  - `key` [`<KeyObject>`](15-crypto-part2.md#class-keyobject)

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
underlying hash function. See [`crypto.createHmac()`](15-crypto-part3.md#cryptocreatehmacalgorithm-key-options) for more information.

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
  supported [asymmetric key types](15-crypto-part1.md#asymmetric-key-types).
- `options` [`<Object>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Object)
  - `modulusLength` [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) Key size in bits (RSA, DSA).
  - `publicExponent` [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) Public exponent (RSA). **Default:** `0x10001`.
  - `hashAlgorithm` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) Name of the message digest (RSA-PSS).
  - `mgf1HashAlgorithm` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) Name of the message digest used by
    MGF1 (RSA-PSS).
  - `saltLength` [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) Minimal salt length in bytes (RSA-PSS).
  - `divisorLength` [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) Size of `q` in bits (DSA).
  - `namedCurve` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) Name of the curve to use (EC).
  - `prime` [`<Buffer>`](07-buffer-part1.md#class-buffer) The prime parameter (DH).
  - `primeLength` [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) Prime length in bits (DH).
  - `generator` [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) Custom generator (DH). **Default:** `2`.
  - `groupName` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) Diffie-Hellman group name (DH). See [`crypto.getDiffieHellman()`](#cryptogetdiffiehellmangroupname).
  - `paramEncoding` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) Must be `'named'` or `'explicit'` (EC).
    **Default:** `'named'`.
  - `publicKeyEncoding` [`<Object>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Object) See [`keyObject.export()`](15-crypto-part2.md#keyobjectexportoptions).
  - `privateKeyEncoding` [`<Object>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Object) See [`keyObject.export()`](15-crypto-part2.md#keyobjectexportoptions).
- `callback` [`<Function>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Function)
  - `err` [`<Error>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Error)
  - `publicKey` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) | [`<Buffer>`](07-buffer-part1.md#class-buffer) | [`<KeyObject>`](15-crypto-part2.md#class-keyobject)
  - `privateKey` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) | [`<Buffer>`](07-buffer-part1.md#class-buffer) | [`<KeyObject>`](15-crypto-part2.md#class-keyobject)

Generates a new asymmetric key pair of the given `type`. See the
supported [asymmetric key types](15-crypto-part1.md#asymmetric-key-types).

For RSA-PSS keys, [`crypto.getHashes()`](#cryptogethashes) lists algorithms available to the
hashing APIs, but not every listed digest can be encoded in RSA-PSS parameters
or is supported by the active RSA implementation.

If a `publicKeyEncoding` or `privateKeyEncoding` was specified, this function
behaves as if [`keyObject.export()`](15-crypto-part2.md#keyobjectexportoptions) had been called on its result. Otherwise,
the respective part of the key is returned as a [`KeyObject`](15-crypto-part2.md#class-keyobject).

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

If this method is invoked as its [`util.promisify()`](60-util-part2.md#utilpromisifyoriginal)ed version, it returns
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
  supported [asymmetric key types](15-crypto-part1.md#asymmetric-key-types).
- `options` [`<Object>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Object)
  - `modulusLength` [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) Key size in bits (RSA, DSA).
  - `publicExponent` [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) Public exponent (RSA). **Default:** `0x10001`.
  - `hashAlgorithm` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) Name of the message digest (RSA-PSS).
  - `mgf1HashAlgorithm` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) Name of the message digest used by
    MGF1 (RSA-PSS).
  - `saltLength` [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) Minimal salt length in bytes (RSA-PSS).
  - `divisorLength` [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) Size of `q` in bits (DSA).
  - `namedCurve` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) Name of the curve to use (EC).
  - `prime` [`<Buffer>`](07-buffer-part1.md#class-buffer) The prime parameter (DH).
  - `primeLength` [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) Prime length in bits (DH).
  - `generator` [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) Custom generator (DH). **Default:** `2`.
  - `groupName` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) Diffie-Hellman group name (DH). See [`crypto.getDiffieHellman()`](#cryptogetdiffiehellmangroupname).
  - `paramEncoding` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) Must be `'named'` or `'explicit'` (EC).
    **Default:** `'named'`.
  - `publicKeyEncoding` [`<Object>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Object) See [`keyObject.export()`](15-crypto-part2.md#keyobjectexportoptions).
  - `privateKeyEncoding` [`<Object>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Object) See [`keyObject.export()`](15-crypto-part2.md#keyobjectexportoptions).
- Returns: [`<Object>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Object)
  - `publicKey` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) | [`<Buffer>`](07-buffer-part1.md#class-buffer) | [`<KeyObject>`](15-crypto-part2.md#class-keyobject)
  - `privateKey` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) | [`<Buffer>`](07-buffer-part1.md#class-buffer) | [`<KeyObject>`](15-crypto-part2.md#class-keyobject)

Generates a new asymmetric key pair of the given `type`. See the
supported [asymmetric key types](15-crypto-part1.md#asymmetric-key-types).

For RSA-PSS keys, [`crypto.getHashes()`](#cryptogethashes) lists algorithms available to the
hashing APIs, but not every listed digest can be encoded in RSA-PSS parameters
or is supported by the active RSA implementation.

If a `publicKeyEncoding` or `privateKeyEncoding` was specified, this function
behaves as if [`keyObject.export()`](15-crypto-part2.md#keyobjectexportoptions) had been called on its result. Otherwise,
the respective part of the key is returned as a [`KeyObject`](15-crypto-part2.md#class-keyobject).

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
- Returns: [`<KeyObject>`](15-crypto-part2.md#class-keyobject)

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
underlying hash function. See [`crypto.createHmac()`](15-crypto-part3.md#cryptocreatehmacalgorithm-key-options) for more information.

### `crypto.generatePrime(size[, options], callback)`

Added in: v15.8.0

**History**

| Version | Changes |
| --- | --- |
| v18.0.0 | Passing an invalid callback to the `callback` argument now throws `ERR_INVALID_ARG_TYPE` instead of `ERR_INVALID_CALLBACK`. |

- `size` [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) The size (in bits) of the prime to generate.
- `options` [`<Object>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Object)
  - `add` [`<ArrayBuffer>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/ArrayBuffer) | [`<SharedArrayBuffer>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/SharedArrayBuffer) | [`<TypedArray>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/TypedArray) | [`<Buffer>`](07-buffer-part1.md#class-buffer) | [`<DataView>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/DataView) | [`<bigint>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#bigint_type)
  - `rem` [`<ArrayBuffer>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/ArrayBuffer) | [`<SharedArrayBuffer>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/SharedArrayBuffer) | [`<TypedArray>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/TypedArray) | [`<Buffer>`](07-buffer-part1.md#class-buffer) | [`<DataView>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/DataView) | [`<bigint>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#bigint_type)
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
  - `add` [`<ArrayBuffer>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/ArrayBuffer) | [`<SharedArrayBuffer>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/SharedArrayBuffer) | [`<TypedArray>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/TypedArray) | [`<Buffer>`](07-buffer-part1.md#class-buffer) | [`<DataView>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/DataView) | [`<bigint>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#bigint_type)
  - `rem` [`<ArrayBuffer>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/ArrayBuffer) | [`<SharedArrayBuffer>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/SharedArrayBuffer) | [`<TypedArray>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/TypedArray) | [`<Buffer>`](07-buffer-part1.md#class-buffer) | [`<DataView>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/DataView) | [`<bigint>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#bigint_type)
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
- Returns: [`<DiffieHellmanGroup>`](15-crypto-part2.md#class-diffiehellmangroup)

Creates a predefined `DiffieHellmanGroup` key exchange object. The
supported groups are listed in the documentation for [`DiffieHellmanGroup`](15-crypto-part2.md#class-diffiehellmangroup).

The returned object mimics the interface of objects created by
[`crypto.createDiffieHellman()`](15-crypto-part3.md#cryptocreatediffiehellmanprime-primeencoding-generator-generatorencoding), but will not allow changing
the keys (with [`diffieHellman.setPublicKey()`](15-crypto-part2.md#diffiehellmansetpublickeypublickey-encoding), for example). The
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
mode](15-crypto-part6.md#fips-mode).

### `crypto.getHashes()`

Added in: v0.9.3

**History**

| Version | Changes |
| --- | --- |
| v26.9.0 | Names and aliases exposed by loaded OpenSSL providers that match the default property query are now included. |

- Returns: [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type)[] An array of the names of the supported hash algorithms,
  such as `'RSA-SHA256'`. Hash algorithms are also called "digest" algorithms.

This is the authoritative Node.js list of hash algorithms available to
[`crypto.createHash()`](15-crypto-part3.md#cryptocreatehashalgorithm-options) and [`crypto.hash()`](#cryptohashalgorithm-data-options) in the current process. With
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
guarantee that [`crypto.createMac()`](15-crypto-part3.md#cryptocreatemacalgorithm-key-options) can initialize the MAC without
additional options. A provider can require additional parameters or a key with
algorithm-specific properties, and it can expose parameters that this API does
not support.

After a successful FIPS mode change made with [`crypto.setFips()`](15-crypto-part5.md#cryptosetfipsbool), subsequent
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

- `typedArray` [`<Buffer>`](07-buffer-part1.md#class-buffer) | [`<TypedArray>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/TypedArray) | [`<DataView>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/DataView) | [`<ArrayBuffer>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/ArrayBuffer)
- Returns: [`<Buffer>`](07-buffer-part1.md#class-buffer) | [`<TypedArray>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/TypedArray) | [`<DataView>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/DataView) | [`<ArrayBuffer>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/ArrayBuffer) Returns `typedArray`.

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
- `data` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) | [`<Buffer>`](07-buffer-part1.md#class-buffer) | [`<TypedArray>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/TypedArray) | [`<DataView>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/DataView) When `data` is a
  string, it will be encoded as UTF-8 before being hashed. If a different
  input encoding is desired for a string input, user could encode the string
  into a `TypedArray` using either `TextEncoder` or `Buffer.from()` and passing
  the encoded `TypedArray` into this API instead.
- `options` [`<Object>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Object) | [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type)
  - `customization` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) | [`<ArrayBuffer>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/ArrayBuffer) | [`<Buffer>`](07-buffer-part1.md#class-buffer) | [`<TypedArray>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/TypedArray) | [`<DataView>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/DataView) For cSHAKE
    hash functions, specifies the customization byte string. **Default:** an
    empty byte string.
  - `functionName` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) | [`<ArrayBuffer>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/ArrayBuffer) | [`<Buffer>`](07-buffer-part1.md#class-buffer) | [`<TypedArray>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/TypedArray) | [`<DataView>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/DataView) For cSHAKE
    hash functions, specifies the NIST function-name byte string. **Default:**
    an empty byte string.
  - `outputEncoding` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) [Encoding](07-buffer-part1.md#buffers-and-character-encodings) used to encode the
    returned digest. **Default:** `'hex'`.
  - `outputLength` [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) For XOF hash functions such as 'shake256',
    the outputLength option can be used to specify the desired output length in bytes.
- Returns: [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) | [`<Buffer>`](07-buffer-part1.md#class-buffer)

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
- `ikm` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) | [`<ArrayBuffer>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/ArrayBuffer) | [`<Buffer>`](07-buffer-part1.md#class-buffer) | [`<TypedArray>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/TypedArray) | [`<DataView>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/DataView) | [`<KeyObject>`](15-crypto-part2.md#class-keyobject) The input
  keying material. Must be provided but can be zero-length.
- `salt` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) | [`<ArrayBuffer>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/ArrayBuffer) | [`<Buffer>`](07-buffer-part1.md#class-buffer) | [`<TypedArray>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/TypedArray) | [`<DataView>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/DataView) The salt value. Must
  be provided but can be zero-length.
- `info` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) | [`<ArrayBuffer>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/ArrayBuffer) | [`<Buffer>`](07-buffer-part1.md#class-buffer) | [`<TypedArray>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/TypedArray) | [`<DataView>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/DataView) Additional info value.
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

[Contents](../README.md) | [Previous: Crypto, part 3 of 6](15-crypto-part3.md) | [Next: Crypto, part 5 of 6](15-crypto-part5.md)
