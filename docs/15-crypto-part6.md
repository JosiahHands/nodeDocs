[Contents](../README.md) | [Previous: Crypto, part 5 of 6](15-crypto-part5.md) | [Next: Debugger](16-debugger.md)

Source: [https://nodejs.org/docs/latest/api/crypto.html](https://nodejs.org/docs/latest/api/crypto.html)

Part 6 of 6 of this section.

# Crypto (part 6 of 6)

Continues: node:crypto module methods and properties.

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

- `a` [`<ArrayBuffer>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/ArrayBuffer) | [`<Buffer>`](07-buffer-part1.md#class-buffer) | [`<TypedArray>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/TypedArray) | [`<DataView>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/DataView)
- `b` [`<ArrayBuffer>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/ArrayBuffer) | [`<Buffer>`](07-buffer-part1.md#class-buffer) | [`<TypedArray>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/TypedArray) | [`<DataView>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/DataView)
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
- `data` [`<ArrayBuffer>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/ArrayBuffer) | [`<Buffer>`](07-buffer-part1.md#class-buffer) | [`<SharedArrayBuffer>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/SharedArrayBuffer) | [`<TypedArray>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/TypedArray) | [`<DataView>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/DataView) | [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type)
- `key` [`<Object>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Object) | [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) | [`<ArrayBuffer>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/ArrayBuffer) | [`<Buffer>`](07-buffer-part1.md#class-buffer) | [`<TypedArray>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/TypedArray) | [`<DataView>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/DataView) | [`<KeyObject>`](15-crypto-part2.md#class-keyobject) | [`<CryptoKey>`](65-webcrypto.md#class-cryptokey)
- `signature` [`<ArrayBuffer>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/ArrayBuffer) | [`<Buffer>`](07-buffer-part1.md#class-buffer) | [`<SharedArrayBuffer>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/SharedArrayBuffer) | [`<TypedArray>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/TypedArray) | [`<DataView>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/DataView)
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

[`crypto.getHashes()`](15-crypto-part4.md#cryptogethashes) lists algorithms available to the hashing APIs, but
the key type and signature scheme determine whether a listed digest can be
used for verification.

If `key` is not a [`KeyObject`](15-crypto-part2.md#class-keyobject), this function behaves as if `key` had been
passed to [`crypto.createPublicKey()`](15-crypto-part4.md#cryptocreatepublickeykey). When `key` is a string, `ArrayBuffer`,
[`Buffer`](07-buffer-part1.md), `TypedArray`, or `DataView`, it must contain PEM-encoded key
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
- `context` [`<ArrayBuffer>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/ArrayBuffer) | [`<Buffer>`](07-buffer-part1.md#class-buffer) | [`<TypedArray>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/TypedArray) | [`<DataView>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/DataView) For Ed25519[3](#user-content-fn-openssl32)
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
unified Stream API, and before there were [`Buffer`](07-buffer-part1.md) objects for handling
binary data. As such, many `crypto` classes have methods not
typically found on other Node.js classes that implement the [streams](51-stream-part1.md)
API (e.g. `update()`, `final()`, or `digest()`). Also, many methods accepted
and returned `'latin1'` encoded strings by default rather than `Buffer`s. This
default was changed in Node.js 0.9.3 to use [`Buffer`](07-buffer-part1.md) objects by default
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
[`crypto.createCipheriv()`](15-crypto-part3.md#cryptocreatecipherivalgorithm-key-iv-options) or [`crypto.createDecipheriv()`](15-crypto-part3.md#cryptocreatedecipherivalgorithm-key-iv-options) selects the
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

For `sm4-xts`, the `xtsStandard` option to [`crypto.createCipheriv()`](15-crypto-part3.md#cryptocreatecipherivalgorithm-key-iv-options) or
[`crypto.createDecipheriv()`](15-crypto-part3.md#cryptocreatedecipherivalgorithm-key-iv-options) selects either the default `'GB'` variant from
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
- Do not use AES key wrap ciphers as generic [`stream.Transform`](51-stream-part2.md#class-streamtransform) streams.
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
  zero-length `iv` to [`crypto.createCipheriv()`](15-crypto-part3.md#cryptocreatecipherivalgorithm-key-iv-options) or
  [`crypto.createDecipheriv()`](15-crypto-part3.md#cryptocreatedecipherivalgorithm-key-iv-options).
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
- [`crypto.setFips()`](15-crypto-part5.md#cryptosetfipsbool) changes the FIPS/property-query state. On OpenSSL 3, it
  does not install, load, initialize, or validate a provider. Implementations
  fetched before the call are not changed.
- [`crypto.getFips()`](15-crypto-part4.md#cryptogetfips) reports the FIPS/property-query state. On OpenSSL 3, a
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

[Contents](../README.md) | [Previous: Crypto, part 5 of 6](15-crypto-part5.md) | [Next: Debugger](16-debugger.md)
