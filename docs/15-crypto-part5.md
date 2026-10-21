[Contents](../README.md) | [Previous: Crypto, part 4 of 6](15-crypto-part4.md) | [Next: Crypto, part 6 of 6](15-crypto-part6.md)

Source: [https://nodejs.org/docs/latest/api/crypto.html](https://nodejs.org/docs/latest/api/crypto.html)

Part 5 of 6 of this section.

# Crypto (part 5 of 6)

### `crypto.hkdfSync(digest, ikm, salt, info, keylen)`

Added in: v15.0.0

**History**

| Version | Changes |
| --- | --- |
| v18.8.0, v16.18.0 | The input keying material can now be zero-length. |

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
- Returns: [`<ArrayBuffer>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/ArrayBuffer)

Provides a synchronous HKDF key derivation function as defined in RFC 5869. The
given `ikm`, `salt` and `info` are used with the `digest` to derive a key of
`keylen` bytes.
The available digest algorithms depend on the version and configuration of
OpenSSL. HKDF uses HMAC internally. [`crypto.getHashes()`](15-crypto-part4.md#cryptogethashes) lists algorithms
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

- `bundle` [`<ArrayBuffer>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/ArrayBuffer) | [`<Buffer>`](07-buffer-part1.md#class-buffer) | [`<TypedArray>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/TypedArray) | [`<DataView>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/DataView) A DER-encoded PKCS#12
  (`.p12` or `.pfx`) bundle.
- `options` [`<Object>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Object)
  - `passphrase` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) | [`<ArrayBuffer>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/ArrayBuffer) | [`<Buffer>`](07-buffer-part1.md#class-buffer) | [`<TypedArray>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/TypedArray) | [`<DataView>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/DataView) The passphrase
    protecting the bundle. Omitting this option is equivalent to passing `''`.
- Returns: [`<Object>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Object)
  - `privateKey` [`<KeyObject>`](15-crypto-part2.md#class-keyobject) | [`<null>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#null_type) The first private key in the bundle, or `null` if none is present.
  - `certificate` [`<X509Certificate>`](15-crypto-part2.md#class-x509certificate) | [`<null>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#null_type) The certificate matching `privateKey`,
    or `null` if no matching certificate is present.
  - `additionalCertificates` [`<X509Certificate>`](15-crypto-part2.md#class-x509certificate)[] All other certificates in
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

- `password` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) | [`<ArrayBuffer>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/ArrayBuffer) | [`<Buffer>`](07-buffer-part1.md#class-buffer) | [`<TypedArray>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/TypedArray) | [`<DataView>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/DataView)
- `salt` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) | [`<ArrayBuffer>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/ArrayBuffer) | [`<Buffer>`](07-buffer-part1.md#class-buffer) | [`<TypedArray>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/TypedArray) | [`<DataView>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/DataView)
- `iterations` [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type)
- `keylen` [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type)
- `digest` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type)
- `callback` [`<Function>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Function)
  - `err` [`<Error>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Error)
  - `derivedKey` [`<Buffer>`](07-buffer-part1.md#class-buffer)

Provides an asynchronous Password-Based Key Derivation Function 2 (PBKDF2)
implementation. A selected HMAC digest algorithm specified by `digest` is
applied to derive a key of the requested byte length (`keylen`) from the
`password`, `salt` and `iterations`.

The supplied `callback` function is called with two arguments: `err` and
`derivedKey`. If an error occurs while deriving the key, `err` will be set;
otherwise `err` will be `null`. By default, the successfully generated
`derivedKey` will be passed to the callback as a [`Buffer`](07-buffer-part1.md). An error will be
thrown if any of the input arguments specify invalid values or types.

The `iterations` argument must be a number set as high as possible. The
higher the number of iterations, the more secure the derived key will be,
but will take a longer amount of time to complete.

The `salt` should be as unique as possible. It is recommended that a salt is
random and at least 16 bytes long. See [NIST SP 800-132](https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-132.pdf) for details.

When passing strings for `password` or `salt`, please consider
[caveats when using strings as inputs to cryptographic APIs](15-crypto-part6.md#using-strings-as-inputs-to-cryptographic-apis).

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
OpenSSL. PBKDF2 uses HMAC internally. [`crypto.getHashes()`](15-crypto-part4.md#cryptogethashes) lists algorithms
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

- `password` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) | [`<ArrayBuffer>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/ArrayBuffer) | [`<Buffer>`](07-buffer-part1.md#class-buffer) | [`<TypedArray>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/TypedArray) | [`<DataView>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/DataView)
- `salt` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) | [`<ArrayBuffer>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/ArrayBuffer) | [`<Buffer>`](07-buffer-part1.md#class-buffer) | [`<TypedArray>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/TypedArray) | [`<DataView>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/DataView)
- `iterations` [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type)
- `keylen` [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type)
- `digest` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type)
- Returns: [`<Buffer>`](07-buffer-part1.md#class-buffer)

Provides a synchronous Password-Based Key Derivation Function 2 (PBKDF2)
implementation. A selected HMAC digest algorithm specified by `digest` is
applied to derive a key of the requested byte length (`keylen`) from the
`password`, `salt` and `iterations`.

If an error occurs an `Error` will be thrown, otherwise the derived key will be
returned as a [`Buffer`](07-buffer-part1.md).

The `iterations` argument must be a number set as high as possible. The
higher the number of iterations, the more secure the derived key will be,
but will take a longer amount of time to complete.

The `salt` should be as unique as possible. It is recommended that a salt is
random and at least 16 bytes long. See [NIST SP 800-132](https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-132.pdf) for details.

When passing strings for `password` or `salt`, please consider
[caveats when using strings as inputs to cryptographic APIs](15-crypto-part6.md#using-strings-as-inputs-to-cryptographic-apis).

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
OpenSSL. PBKDF2 uses HMAC internally. [`crypto.getHashes()`](15-crypto-part4.md#cryptogethashes) lists algorithms
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

- `privateKey` [`<Object>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Object) | [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) | [`<ArrayBuffer>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/ArrayBuffer) | [`<Buffer>`](07-buffer-part1.md#class-buffer) | [`<TypedArray>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/TypedArray) | [`<DataView>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/DataView) | [`<KeyObject>`](15-crypto-part2.md#class-keyobject) | [`<CryptoKey>`](65-webcrypto.md#class-cryptokey) | [`<URL>`](59-url.md#the-whatwg-url-api)
  - `oaepHash` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) The hash function to use for OAEP padding and, unless `mgf1Hash` is set, MGF1. **Default:** `'sha1'`
  - `mgf1Hash` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) The hash function to use for the MGF1 mask generation
    function of OAEP padding. If not specified, the value of `oaepHash` is used.
    This allows the OAEP digest and the MGF1 digest to differ.
  - `oaepLabel` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) | [`<ArrayBuffer>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/ArrayBuffer) | [`<Buffer>`](07-buffer-part1.md#class-buffer) | [`<TypedArray>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/TypedArray) | [`<DataView>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/DataView) The label to
    use for OAEP padding. If not specified, no label is used.
  - `padding` [`<crypto.constants>`](15-crypto-part3.md#cryptoconstants) An optional padding value defined in `crypto.constants`, which may be: `crypto.constants.RSA_NO_PADDING`,
    `crypto.constants.RSA_PKCS1_PADDING`, or
    `crypto.constants.RSA_PKCS1_OAEP_PADDING`.
- `buffer` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) | [`<ArrayBuffer>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/ArrayBuffer) | [`<Buffer>`](07-buffer-part1.md#class-buffer) | [`<TypedArray>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/TypedArray) | [`<DataView>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/DataView)
- Returns: [`<Buffer>`](07-buffer-part1.md#class-buffer) A new `Buffer` with the decrypted content.

Decrypts `buffer` with `privateKey`. `buffer` was previously encrypted using
the corresponding public key, for example using [`crypto.publicEncrypt()`](#cryptopublicencryptkey-buffer).

[`crypto.getHashes()`](15-crypto-part4.md#cryptogethashes) lists algorithms available to the hashing APIs, but
the active RSA implementation can impose additional restrictions on digests
used for OAEP or MGF1.

If `privateKey` is not a [`KeyObject`](15-crypto-part2.md#class-keyobject), this function behaves as if
`privateKey` had been passed to [`crypto.createPrivateKey()`](15-crypto-part3.md#cryptocreateprivatekeykey). If it is an
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

- `privateKey` [`<Object>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Object) | [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) | [`<ArrayBuffer>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/ArrayBuffer) | [`<Buffer>`](07-buffer-part1.md#class-buffer) | [`<TypedArray>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/TypedArray) | [`<DataView>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/DataView) | [`<KeyObject>`](15-crypto-part2.md#class-keyobject) | [`<CryptoKey>`](65-webcrypto.md#class-cryptokey) | [`<URL>`](59-url.md#the-whatwg-url-api)
  - `key` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) | [`<ArrayBuffer>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/ArrayBuffer) | [`<Buffer>`](07-buffer-part1.md#class-buffer) | [`<TypedArray>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/TypedArray) | [`<DataView>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/DataView) | [`<KeyObject>`](15-crypto-part2.md#class-keyobject) | [`<CryptoKey>`](65-webcrypto.md#class-cryptokey) | [`<URL>`](59-url.md#the-whatwg-url-api)
    The private key material, a [`<KeyObject>`](15-crypto-part2.md#class-keyobject), or a [`<URL>`](59-url.md#the-whatwg-url-api) referencing an object
    for an OpenSSL STORE loader.
  - `passphrase` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) | [`<ArrayBuffer>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/ArrayBuffer) | [`<Buffer>`](07-buffer-part1.md#class-buffer) | [`<TypedArray>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/TypedArray) | [`<DataView>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/DataView) An optional
    passphrase for the private key.
  - `padding` [`<crypto.constants>`](15-crypto-part3.md#cryptoconstants) An optional padding value defined in `crypto.constants`, which may be: `crypto.constants.RSA_NO_PADDING` or
    `crypto.constants.RSA_PKCS1_PADDING`.
  - `encoding` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) The string encoding to use when `buffer`, `key`,
    or `passphrase` are strings.
- `buffer` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) | [`<ArrayBuffer>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/ArrayBuffer) | [`<Buffer>`](07-buffer-part1.md#class-buffer) | [`<TypedArray>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/TypedArray) | [`<DataView>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/DataView)
- Returns: [`<Buffer>`](07-buffer-part1.md#class-buffer) A new `Buffer` with the encrypted content.

Encrypts `buffer` with `privateKey`. The returned data can be decrypted using
the corresponding public key, for example using [`crypto.publicDecrypt()`](#cryptopublicdecryptkey-buffer).

If `privateKey` is not a [`KeyObject`](15-crypto-part2.md#class-keyobject), this function behaves as if
`privateKey` had been passed to [`crypto.createPrivateKey()`](15-crypto-part3.md#cryptocreateprivatekeykey). If it is an
object, the `padding` property can be passed. Otherwise, this function uses
`RSA_PKCS1_PADDING`.

### `crypto.publicDecrypt(key, buffer)`

Added in: v1.1.0

**History**

| Version | Changes |
| --- | --- |
| v15.0.0 | Added string, ArrayBuffer, and CryptoKey as allowable key types. The passphrase can be an ArrayBuffer. The buffer can be a string or ArrayBuffer. All types that accept buffers are limited to a maximum of 2 \*\* 31 - 1 bytes. |
| v11.6.0 | This function now supports key objects. |

- `key` [`<Object>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Object) | [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) | [`<ArrayBuffer>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/ArrayBuffer) | [`<Buffer>`](07-buffer-part1.md#class-buffer) | [`<TypedArray>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/TypedArray) | [`<DataView>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/DataView) | [`<KeyObject>`](15-crypto-part2.md#class-keyobject) | [`<CryptoKey>`](65-webcrypto.md#class-cryptokey)
  - `passphrase` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) | [`<ArrayBuffer>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/ArrayBuffer) | [`<Buffer>`](07-buffer-part1.md#class-buffer) | [`<TypedArray>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/TypedArray) | [`<DataView>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/DataView) An optional
    passphrase for the private key.
  - `padding` [`<crypto.constants>`](15-crypto-part3.md#cryptoconstants) An optional padding value defined in `crypto.constants`, which may be: `crypto.constants.RSA_NO_PADDING` or
    `crypto.constants.RSA_PKCS1_PADDING`.
  - `encoding` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) The string encoding to use when `buffer`, `key`,
    or `passphrase` are strings.
- `buffer` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) | [`<ArrayBuffer>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/ArrayBuffer) | [`<Buffer>`](07-buffer-part1.md#class-buffer) | [`<TypedArray>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/TypedArray) | [`<DataView>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/DataView)
- Returns: [`<Buffer>`](07-buffer-part1.md#class-buffer) A new `Buffer` with the decrypted content.

Decrypts `buffer` with `key`. `buffer` was previously encrypted using
the corresponding private key, for example using [`crypto.privateEncrypt()`](#cryptoprivateencryptprivatekey-buffer).

If `key` is not a [`KeyObject`](15-crypto-part2.md#class-keyobject), this function behaves as if
`key` had been passed to [`crypto.createPublicKey()`](15-crypto-part4.md#cryptocreatepublickeykey). If it is an
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

- `key` [`<Object>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Object) | [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) | [`<ArrayBuffer>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/ArrayBuffer) | [`<Buffer>`](07-buffer-part1.md#class-buffer) | [`<TypedArray>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/TypedArray) | [`<DataView>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/DataView) | [`<KeyObject>`](15-crypto-part2.md#class-keyobject) | [`<CryptoKey>`](65-webcrypto.md#class-cryptokey)
  - `key` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) | [`<ArrayBuffer>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/ArrayBuffer) | [`<Buffer>`](07-buffer-part1.md#class-buffer) | [`<TypedArray>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/TypedArray) | [`<DataView>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/DataView) | [`<KeyObject>`](15-crypto-part2.md#class-keyobject) | [`<CryptoKey>`](65-webcrypto.md#class-cryptokey)
    A PEM encoded public or private key, [`<KeyObject>`](15-crypto-part2.md#class-keyobject), or [`<CryptoKey>`](65-webcrypto.md#class-cryptokey).
  - `oaepHash` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) The hash function to use for OAEP padding and, unless `mgf1Hash` is set, MGF1. **Default:** `'sha1'`
  - `mgf1Hash` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) The hash function to use for the MGF1 mask generation
    function of OAEP padding. If not specified, the value of `oaepHash` is used.
    This allows the OAEP digest and the MGF1 digest to differ.
  - `oaepLabel` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) | [`<ArrayBuffer>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/ArrayBuffer) | [`<Buffer>`](07-buffer-part1.md#class-buffer) | [`<TypedArray>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/TypedArray) | [`<DataView>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/DataView) The label to
    use for OAEP padding. If not specified, no label is used.
  - `passphrase` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) | [`<ArrayBuffer>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/ArrayBuffer) | [`<Buffer>`](07-buffer-part1.md#class-buffer) | [`<TypedArray>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/TypedArray) | [`<DataView>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/DataView) An optional
    passphrase for the private key.
  - `padding` [`<crypto.constants>`](15-crypto-part3.md#cryptoconstants) An optional padding value defined in `crypto.constants`, which may be: `crypto.constants.RSA_NO_PADDING`,
    `crypto.constants.RSA_PKCS1_PADDING`, or
    `crypto.constants.RSA_PKCS1_OAEP_PADDING`.
  - `encoding` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) The string encoding to use when `buffer`, `key`,
    `oaepLabel`, or `passphrase` are strings.
- `buffer` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) | [`<ArrayBuffer>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/ArrayBuffer) | [`<Buffer>`](07-buffer-part1.md#class-buffer) | [`<TypedArray>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/TypedArray) | [`<DataView>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/DataView)
- Returns: [`<Buffer>`](07-buffer-part1.md#class-buffer) A new `Buffer` with the encrypted content.

Encrypts the content of `buffer` with `key` and returns a new
[`Buffer`](07-buffer-part1.md) with encrypted content. The returned data can be decrypted using
the corresponding private key, for example using [`crypto.privateDecrypt()`](#cryptoprivatedecryptprivatekey-buffer).

[`crypto.getHashes()`](15-crypto-part4.md#cryptogethashes) lists algorithms available to the hashing APIs, but
the active RSA implementation can impose additional restrictions on digests
used for OAEP or MGF1.

If `key` is not a [`KeyObject`](15-crypto-part2.md#class-keyobject), this function behaves as if
`key` had been passed to [`crypto.createPublicKey()`](15-crypto-part4.md#cryptocreatepublickeykey). If it is an
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
  - `buf` [`<Buffer>`](07-buffer-part1.md#class-buffer)
- Returns: [`<Buffer>`](07-buffer-part1.md#class-buffer) if the `callback` function is not provided.

Generates cryptographically strong pseudorandom data. The `size` argument
is a number indicating the number of bytes to generate.

If a `callback` function is provided, the bytes are generated asynchronously
and the `callback` function is invoked with two arguments: `err` and `buf`.
If an error occurs, `err` will be an `Error` object; otherwise it is `null`. The
`buf` argument is a [`Buffer`](07-buffer-part1.md) containing the generated bytes.

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
synchronously and returned as a [`Buffer`](07-buffer-part1.md). An error will be thrown if
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

- `buffer` [`<ArrayBuffer>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/ArrayBuffer) | [`<Buffer>`](07-buffer-part1.md#class-buffer) | [`<TypedArray>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/TypedArray) | [`<DataView>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/DataView) Must be supplied. The
  size of the provided `buffer` must not be larger than `2**31 - 1`.
- `offset` [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) The start position, in elements for a `TypedArray` and in
  bytes for an `ArrayBuffer` or `DataView`. **Default:** `0`
- `size` [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) The amount to fill, in the same units as `offset`.
  **Default:** `buffer.length - offset` for a `TypedArray`, or
  `buffer.byteLength - offset` for an `ArrayBuffer` or `DataView`. The `size`
  must not be larger than `2**31 - 1`.
- `callback` [`<Function>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Function) `function(err, buf) {}`.

This function is similar to [`crypto.randomBytes()`](#cryptorandombytessize-callback) but requires the first
argument to be a [`Buffer`](07-buffer-part1.md) that will be filled. It also
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

- `buffer` [`<ArrayBuffer>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/ArrayBuffer) | [`<Buffer>`](07-buffer-part1.md#class-buffer) | [`<TypedArray>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/TypedArray) | [`<DataView>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/DataView) Must be supplied. The
  size of the provided `buffer` must not be larger than `2**31 - 1`.
- `offset` [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) The start position, in elements for a `TypedArray` and in
  bytes for an `ArrayBuffer` or `DataView`. **Default:** `0`
- `size` [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) The amount to fill, in the same units as `offset`.
  **Default:** `buffer.length - offset` for a `TypedArray`, or
  `buffer.byteLength - offset` for an `ArrayBuffer` or `DataView`. The `size`
  must not be larger than `2**31 - 1`.
- Returns: [`<ArrayBuffer>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/ArrayBuffer) | [`<Buffer>`](07-buffer-part1.md#class-buffer) | [`<TypedArray>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/TypedArray) | [`<DataView>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/DataView) The object passed as `buffer` argument.

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

- `password` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) | [`<ArrayBuffer>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/ArrayBuffer) | [`<Buffer>`](07-buffer-part1.md#class-buffer) | [`<TypedArray>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/TypedArray) | [`<DataView>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/DataView)
- `salt` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) | [`<ArrayBuffer>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/ArrayBuffer) | [`<Buffer>`](07-buffer-part1.md#class-buffer) | [`<TypedArray>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/TypedArray) | [`<DataView>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/DataView)
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
  - `derivedKey` [`<Buffer>`](07-buffer-part1.md#class-buffer)

Provides an asynchronous [scrypt](https://en.wikipedia.org/wiki/Scrypt) implementation. Scrypt is a password-based
key derivation function that is designed to be expensive computationally and
memory-wise in order to make brute-force attacks unrewarding.

The `salt` should be as unique as possible. It is recommended that a salt is
random and at least 16 bytes long. See [NIST SP 800-132](https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-132.pdf) for details.

When passing strings for `password` or `salt`, please consider
[caveats when using strings as inputs to cryptographic APIs](15-crypto-part6.md#using-strings-as-inputs-to-cryptographic-apis).

The `callback` function is called with two arguments: `err` and `derivedKey`.
`err` is an exception object when key derivation fails, otherwise `err` is
`null`. `derivedKey` is passed to the callback as a [`Buffer`](07-buffer-part1.md).

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

- `password` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) | [`<Buffer>`](07-buffer-part1.md#class-buffer) | [`<TypedArray>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/TypedArray) | [`<DataView>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/DataView)
- `salt` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) | [`<Buffer>`](07-buffer-part1.md#class-buffer) | [`<TypedArray>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/TypedArray) | [`<DataView>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/DataView)
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
- Returns: [`<Buffer>`](07-buffer-part1.md#class-buffer)

Provides a synchronous [scrypt](https://en.wikipedia.org/wiki/Scrypt) implementation. Scrypt is a password-based
key derivation function that is designed to be expensive computationally and
memory-wise in order to make brute-force attacks unrewarding.

The `salt` should be as unique as possible. It is recommended that a salt is
random and at least 16 bytes long. See [NIST SP 800-132](https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-132.pdf) for details.

When passing strings for `password` or `salt`, please consider
[caveats when using strings as inputs to cryptographic APIs](15-crypto-part6.md#using-strings-as-inputs-to-cryptographic-apis).

An exception is thrown when key derivation fails, otherwise the derived key is
returned as a [`Buffer`](07-buffer-part1.md).

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
- `flags` [`<crypto.constants>`](15-crypto-part3.md#cryptoconstants) **Default:** `crypto.constants.ENGINE_METHOD_ALL`

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

Changes [FIPS mode](15-crypto-part6.md#fips-mode). With OpenSSL 3, this only adds or removes `fips=yes` in
the default property query. It does not install, load, initialize, or validate
a FIPS provider. For a usable FIPS configuration, install the provider and
configure OpenSSL to load it when Node.js starts, as described in [FIPS
mode](15-crypto-part6.md#fips-mode).

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
- `data` [`<ArrayBuffer>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/ArrayBuffer) | [`<Buffer>`](07-buffer-part1.md#class-buffer) | [`<SharedArrayBuffer>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/SharedArrayBuffer) | [`<TypedArray>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/TypedArray) | [`<DataView>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/DataView) | [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type)
- `key` [`<Object>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Object) | [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) | [`<ArrayBuffer>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/ArrayBuffer) | [`<Buffer>`](07-buffer-part1.md#class-buffer) | [`<TypedArray>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/TypedArray) | [`<DataView>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/DataView) | [`<KeyObject>`](15-crypto-part2.md#class-keyobject) | [`<CryptoKey>`](65-webcrypto.md#class-cryptokey) | [`<URL>`](59-url.md#the-whatwg-url-api)
- `callback` [`<Function>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Function)
  - `err` [`<Error>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Error)
  - `signature` [`<Buffer>`](07-buffer-part1.md#class-buffer)
- Returns: [`<Buffer>`](07-buffer-part1.md#class-buffer) if the `callback` function is not provided.

Calculates and returns the signature for `data` using the given private key and
algorithm. If `algorithm` is `null` or `undefined`, then the algorithm is
dependent upon the key type.

`algorithm` is required to be `null` or `undefined` for Ed25519, Ed448, and
ML-DSA.

[`crypto.getHashes()`](15-crypto-part4.md#cryptogethashes) lists algorithms available to the hashing APIs, but
the key type and signature scheme determine whether a listed digest can be
used for signing.

If `key` is not a [`KeyObject`](15-crypto-part2.md#class-keyobject), this function behaves as if `key` had been
passed to [`crypto.createPrivateKey()`](15-crypto-part3.md#cryptocreateprivatekeykey). When `key` is a string, `ArrayBuffer`,
[`Buffer`](07-buffer-part1.md), `TypedArray`, or `DataView`, it must contain PEM-encoded key
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
- `context` [`<ArrayBuffer>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/ArrayBuffer) | [`<Buffer>`](07-buffer-part1.md#class-buffer) | [`<TypedArray>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/TypedArray) | [`<DataView>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/DataView) For Ed25519[3](#user-content-fn-openssl32)
  (using Ed25519ctx from [RFC 8032](https://www.rfc-editor.org/rfc/rfc8032.txt)), Ed448, ML-DSA, and SLH-DSA,
  this option specifies the optional context to differentiate signatures
  generated for different purposes with the same key.

If the `callback` function is provided this function uses libuv's threadpool.

[Contents](../README.md) | [Previous: Crypto, part 4 of 6](15-crypto-part4.md) | [Next: Crypto, part 6 of 6](15-crypto-part6.md)
