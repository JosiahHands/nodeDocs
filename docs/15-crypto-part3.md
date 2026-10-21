[Contents](../README.md) | [Previous: Crypto, part 2 of 6](15-crypto-part2.md) | [Next: Crypto, part 4 of 6](15-crypto-part4.md)

Source: [https://nodejs.org/docs/latest/api/crypto.html](https://nodejs.org/docs/latest/api/crypto.html)

Part 3 of 6 of this section.

# Crypto (part 3 of 6)

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

- `otherCert` [`<X509Certificate>`](15-crypto-part2.md#class-x509certificate)
- Returns: [`<boolean>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#boolean_type)

Checks whether this certificate was potentially issued by the given `otherCert`
by comparing the certificate metadata.

This is useful for pruning a list of possible issuer certificates which have been
selected using a more rudimentary filtering routine, i.e. just based on subject
and issuer names.

Finally, to verify that this certificate's signature was produced by a private key
corresponding to `otherCert`'s public key use [`x509.verify(publicKey)`](#x509verifypublickey)
with `otherCert`'s public key represented as a [`KeyObject`](15-crypto-part2.md#class-keyobject)
like so

```js
if (!x509.verify(otherCert.publicKey)) {
  throw new Error('otherCert did not issue x509');
}
```

### `x509.checkPrivateKey(privateKey)`

Added in: v15.6.0

- `privateKey` [`<KeyObject>`](15-crypto-part2.md#class-keyobject) A private key.
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

- Type: [`<X509Certificate>`](15-crypto-part2.md#class-x509certificate)

The issuer certificate or `undefined` if the issuer certificate is not
available.

### `x509.keyUsage`

Added in: v15.6.0

- Type: [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type)[]

An array detailing the key extended usages for this certificate.

### `x509.publicKey`

Added in: v15.6.0

- Type: [`<KeyObject>`](15-crypto-part2.md#class-keyobject)

The public key [`<KeyObject>`](15-crypto-part2.md#class-keyobject) for this certificate.

### `x509.raw`

Added in: v15.6.0

- Type: [`<Buffer>`](07-buffer-part1.md#class-buffer)

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

- `publicKey` [`<KeyObject>`](15-crypto-part2.md#class-keyobject) A public key.
- Returns: [`<boolean>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#boolean_type)

Verifies that this certificate was signed by the given public key.
Does not perform any other validation checks on the certificate.

## `node:crypto` module methods and properties

### `crypto.argon2(algorithm, parameters, callback)`

Added in: v24.7.0

- `algorithm` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) Variant of Argon2, one of `"argon2d"`, `"argon2i"` or `"argon2id"`.
- `parameters` [`<Object>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Object)
  - `message` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) | [`<ArrayBuffer>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/ArrayBuffer) | [`<Buffer>`](07-buffer-part1.md#class-buffer) | [`<TypedArray>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/TypedArray) | [`<DataView>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/DataView) REQUIRED, this is the password for password
    hashing applications of Argon2.
  - `nonce` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) | [`<ArrayBuffer>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/ArrayBuffer) | [`<Buffer>`](07-buffer-part1.md#class-buffer) | [`<TypedArray>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/TypedArray) | [`<DataView>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/DataView) REQUIRED, must be at
    least 8 bytes long. This is the salt for password hashing applications of Argon2.
  - `parallelism` [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) REQUIRED, degree of parallelism determines how many computational chains (lanes)
    can be run. Must be at least `1` and at most `2**24-1`.
  - `tagLength` [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) REQUIRED, the length of the key to generate. Must be at least `4` and
    at most `2**32-1`.
  - `memory` [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) REQUIRED, memory cost in 1KiB blocks. Must be at least `8 * parallelism` and at most `2**32-1`. The actual number of blocks is rounded
    down to the nearest multiple of `4 * parallelism`.
  - `passes` [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) REQUIRED, number of passes (iterations). Must be at least `1` and at most
    `2**32-1`.
  - `secret` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) | [`<ArrayBuffer>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/ArrayBuffer) | [`<Buffer>`](07-buffer-part1.md#class-buffer) | [`<TypedArray>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/TypedArray) | [`<DataView>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/DataView) | [`<undefined>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#undefined_type) OPTIONAL, Random additional input,
    similar to the salt, that should **NOT** be stored with the derived key. This is known as pepper in
    password hashing applications. If used, must have a length not greater than `2**32-1` bytes.
  - `associatedData` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) | [`<ArrayBuffer>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/ArrayBuffer) | [`<Buffer>`](07-buffer-part1.md#class-buffer) | [`<TypedArray>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/TypedArray) | [`<DataView>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/DataView) | [`<undefined>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#undefined_type) OPTIONAL, Additional data to
    be added to the hash, functionally equivalent to salt or secret, but meant for
    non-random data. If used, must have a length not greater than `2**32-1` bytes.
- `callback` [`<Function>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Function)
  - `err` [`<Error>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Error)
  - `derivedKey` [`<Buffer>`](07-buffer-part1.md#class-buffer)

Provides an asynchronous [Argon2](https://www.rfc-editor.org/rfc/rfc9106.html) implementation. Argon2 is a password-based
key derivation function that is designed to be expensive computationally and
memory-wise in order to make brute-force attacks unrewarding.

The `nonce` should be as unique as possible. It is recommended that a nonce is
random and at least 16 bytes long. See [NIST SP 800-132](https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-132.pdf) for details.

When passing strings for `message`, `nonce`, `secret` or `associatedData`, please
consider [caveats when using strings as inputs to cryptographic APIs](15-crypto-part6.md#using-strings-as-inputs-to-cryptographic-apis).

The `callback` function is called with two arguments: `err` and `derivedKey`.
`err` is an exception object when key derivation fails, otherwise `err` is
`null`. `derivedKey` is passed to the callback as a [`Buffer`](07-buffer-part1.md).

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
  - `message` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) | [`<ArrayBuffer>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/ArrayBuffer) | [`<Buffer>`](07-buffer-part1.md#class-buffer) | [`<TypedArray>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/TypedArray) | [`<DataView>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/DataView) REQUIRED, this is the password for password
    hashing applications of Argon2.
  - `nonce` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) | [`<ArrayBuffer>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/ArrayBuffer) | [`<Buffer>`](07-buffer-part1.md#class-buffer) | [`<TypedArray>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/TypedArray) | [`<DataView>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/DataView) REQUIRED, must be at
    least 8 bytes long. This is the salt for password hashing applications of Argon2.
  - `parallelism` [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) REQUIRED, degree of parallelism determines how many computational chains (lanes)
    can be run. Must be at least 1 and at most `2**24-1`.
  - `tagLength` [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) REQUIRED, the length of the key to generate. Must be at least `4` and
    at most `2**32-1`.
  - `memory` [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) REQUIRED, memory cost in 1KiB blocks. Must be at least `8 * parallelism` and at most `2**32-1`. The actual number of blocks is rounded
    down to the nearest multiple of `4 * parallelism`.
  - `passes` [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) REQUIRED, number of passes (iterations). Must be at least `1` and at most
    `2**32-1`.
  - `secret` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) | [`<ArrayBuffer>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/ArrayBuffer) | [`<Buffer>`](07-buffer-part1.md#class-buffer) | [`<TypedArray>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/TypedArray) | [`<DataView>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/DataView) | [`<undefined>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#undefined_type) OPTIONAL, Random additional input,
    similar to the salt, that should **NOT** be stored with the derived key. This is known as pepper in
    password hashing applications. If used, must have a length not greater than `2**32-1` bytes.
  - `associatedData` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) | [`<ArrayBuffer>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/ArrayBuffer) | [`<Buffer>`](07-buffer-part1.md#class-buffer) | [`<TypedArray>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/TypedArray) | [`<DataView>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/DataView) | [`<undefined>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#undefined_type) OPTIONAL, Additional data to
    be added to the hash, functionally equivalent to salt or secret, but meant for
    non-random data. If used, must have a length not greater than `2**32-1` bytes.
- Returns: [`<Buffer>`](07-buffer-part1.md#class-buffer)

Provides a synchronous [Argon2](https://www.rfc-editor.org/rfc/rfc9106.html) implementation. Argon2 is a password-based
key derivation function that is designed to be expensive computationally and
memory-wise in order to make brute-force attacks unrewarding.

The `nonce` should be as unique as possible. It is recommended that a nonce is
random and at least 16 bytes long. See [NIST SP 800-132](https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-132.pdf) for details.

When passing strings for `message`, `nonce`, `secret` or `associatedData`, please
consider [caveats when using strings as inputs to cryptographic APIs](15-crypto-part6.md#using-strings-as-inputs-to-cryptographic-apis).

An exception is thrown when key derivation fails, otherwise the derived key is
returned as a [`Buffer`](07-buffer-part1.md).

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

- `candidate` [`<ArrayBuffer>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/ArrayBuffer) | [`<SharedArrayBuffer>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/SharedArrayBuffer) | [`<TypedArray>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/TypedArray) | [`<Buffer>`](07-buffer-part1.md#class-buffer) | [`<DataView>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/DataView) | [`<bigint>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#bigint_type)
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

- `candidate` [`<ArrayBuffer>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/ArrayBuffer) | [`<SharedArrayBuffer>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/SharedArrayBuffer) | [`<TypedArray>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/TypedArray) | [`<Buffer>`](07-buffer-part1.md#class-buffer) | [`<DataView>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/DataView) | [`<bigint>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#bigint_type)
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
[Crypto constants](15-crypto-part6.md#crypto-constants).

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
- `key` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) | [`<ArrayBuffer>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/ArrayBuffer) | [`<Buffer>`](07-buffer-part1.md#class-buffer) | [`<TypedArray>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/TypedArray) | [`<DataView>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/DataView) | [`<KeyObject>`](15-crypto-part2.md#class-keyobject) | [`<CryptoKey>`](65-webcrypto.md#class-cryptokey)
- `iv` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) | [`<ArrayBuffer>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/ArrayBuffer) | [`<Buffer>`](07-buffer-part1.md#class-buffer) | [`<TypedArray>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/TypedArray) | [`<DataView>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/DataView) | [`<null>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#null_type)
- `options` [`<Object>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Object) [`stream.transform` options](51-stream-part3.md#new-streamtransformoptions) with these additional
  properties:
  - `authTagLength` [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) The authentication tag length in bytes. Its
    requirements and default depend on the authenticated cipher, as described
    below.
  - `ctsMode` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) The [CBC-CTS mode](15-crypto-part6.md#cbc-cts-mode) variant. One of `'CS1'`, `'CS2'`,
    or `'CS3'`. The values are case-sensitive. **Default:** `'CS1'`.
  - `encoding` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) The [encoding](07-buffer-part1.md#buffers-and-character-encodings) to use when `key` is a string. This
    option does not affect a string `iv`, which is always interpreted as UTF-8.
    **Default:** `'utf8'`.
  - `xtsStandard` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) The standard used by `sm4-xts`. One of `'GB'` or
    `'IEEE'`. The values are case-sensitive. **Default:** `'GB'`.
- Returns: [`<Cipheriv>`](15-crypto-part1.md#class-cipheriv)

Creates and returns a `Cipheriv` object, with the given `algorithm`, `key` and
initialization vector (`iv`).

The `options` argument controls cipher-specific settings and stream behavior.
It is optional except when a cipher in CCM or OCB mode (e.g. `'aes-128-ccm'`)
is used. In that case, the `authTagLength` option is required and specifies the
length of the authentication tag in bytes, see [CCM mode](15-crypto-part6.md#ccm-mode). In GCM mode, the
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
`ERR_CRYPTO_UNSUPPORTED_OPERATION` error. See [CBC-CTS mode](15-crypto-part6.md#cbc-cts-mode) and [XTS mode](15-crypto-part6.md#xts-mode)
for details.

The available algorithms depend on OpenSSL. [`crypto.getCiphers()`](15-crypto-part4.md#cryptogetciphers) lists the
algorithms exposed by Node.js. On recent OpenSSL releases,
`openssl list -cipher-algorithms` displays the algorithms available to OpenSSL,
which can include algorithms that Node.js does not expose.

The `key` is the raw key used by the `algorithm` and `iv` is an
[initialization vector](https://en.wikipedia.org/wiki/Initialization_vector). Each may be a string, `ArrayBuffer`, [`Buffer`](07-buffer-part1.md),
`TypedArray`, or `DataView`. A string `key` is decoded using `options.encoding`,
which defaults to `'utf8'`; a string `iv` is always decoded as UTF-8. The `key`
may optionally be a [`KeyObject`](15-crypto-part2.md#class-keyobject) of type `secret`. If the cipher does not
need an initialization vector, `iv` may be `null`.

When passing strings for `key` or `iv`, please consider
[caveats when using strings as inputs to cryptographic APIs](15-crypto-part6.md#using-strings-as-inputs-to-cryptographic-apis).

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
- `key` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) | [`<ArrayBuffer>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/ArrayBuffer) | [`<Buffer>`](07-buffer-part1.md#class-buffer) | [`<TypedArray>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/TypedArray) | [`<DataView>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/DataView) | [`<KeyObject>`](15-crypto-part2.md#class-keyobject) | [`<CryptoKey>`](65-webcrypto.md#class-cryptokey)
- `iv` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) | [`<ArrayBuffer>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/ArrayBuffer) | [`<Buffer>`](07-buffer-part1.md#class-buffer) | [`<TypedArray>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/TypedArray) | [`<DataView>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/DataView) | [`<null>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#null_type)
- `options` [`<Object>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Object) [`stream.transform` options](51-stream-part3.md#new-streamtransformoptions) with these additional
  properties:
  - `authTagLength` [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) The authentication tag length in bytes. Its
    requirements and default depend on the authenticated cipher, as described
    below.
  - `ctsMode` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) The [CBC-CTS mode](15-crypto-part6.md#cbc-cts-mode) variant. One of `'CS1'`, `'CS2'`,
    or `'CS3'`. The values are case-sensitive. **Default:** `'CS1'`.
  - `encoding` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) The [encoding](07-buffer-part1.md#buffers-and-character-encodings) to use when `key` is a string. This
    option does not affect a string `iv`, which is always interpreted as UTF-8.
    **Default:** `'utf8'`.
  - `xtsStandard` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) The standard used by `sm4-xts`. One of `'GB'` or
    `'IEEE'`. The values are case-sensitive. **Default:** `'GB'`.
- Returns: [`<Decipheriv>`](15-crypto-part1.md#class-decipheriv)

Creates and returns a `Decipheriv` object that uses the given `algorithm`, `key`
and initialization vector (`iv`).

The `options` argument controls cipher-specific settings and stream behavior.
It is optional except when a cipher in CCM or OCB mode (e.g. `'aes-128-ccm'`)
is used. In that case, the `authTagLength` option is required and specifies the
length of the authentication tag in bytes, see [CCM mode](15-crypto-part6.md#ccm-mode). For GCM and
`chacha20-poly1305`, the `authTagLength` option defaults to 16 bytes and must be
set if a different length is used. For `SIV` and `GCM-SIV`, the `authTagLength`
option defaults to 16 bytes and only 16-byte authentication tags are supported.

The `ctsMode` and `xtsStandard` options configure parameters exposed by OpenSSL
providers. They are available only with OpenSSL 3.0 or later and a provider
that supports the corresponding parameter. `ctsMode` applies only to CBC-CTS
ciphers, and `xtsStandard` applies only to `sm4-xts`. Supplying either option
for an available cipher implementation that does not support it throws an
`ERR_CRYPTO_UNSUPPORTED_OPERATION` error. See [CBC-CTS mode](15-crypto-part6.md#cbc-cts-mode) and [XTS mode](15-crypto-part6.md#xts-mode)
for details.

The available algorithms depend on OpenSSL. [`crypto.getCiphers()`](15-crypto-part4.md#cryptogetciphers) lists the
algorithms exposed by Node.js. On recent OpenSSL releases,
`openssl list -cipher-algorithms` displays the algorithms available to OpenSSL,
which can include algorithms that Node.js does not expose.

The `key` is the raw key used by the `algorithm` and `iv` is an
[initialization vector](https://en.wikipedia.org/wiki/Initialization_vector). Each may be a string, `ArrayBuffer`, [`Buffer`](07-buffer-part1.md),
`TypedArray`, or `DataView`. A string `key` is decoded using `options.encoding`,
which defaults to `'utf8'`; a string `iv` is always decoded as UTF-8. The `key`
may optionally be a [`KeyObject`](15-crypto-part2.md#class-keyobject) of type `secret`. If the cipher does not
need an initialization vector, `iv` may be `null`.

When passing strings for `key` or `iv`, please consider
[caveats when using strings as inputs to cryptographic APIs](15-crypto-part6.md#using-strings-as-inputs-to-cryptographic-apis).

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

- `prime` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) | [`<ArrayBuffer>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/ArrayBuffer) | [`<Buffer>`](07-buffer-part1.md#class-buffer) | [`<TypedArray>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/TypedArray) | [`<DataView>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/DataView)
- `primeEncoding` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) The [encoding](07-buffer-part1.md#buffers-and-character-encodings) of the `prime` string.
- `generator` [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) | [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) | [`<ArrayBuffer>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/ArrayBuffer) | [`<Buffer>`](07-buffer-part1.md#class-buffer) | [`<TypedArray>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/TypedArray) | [`<DataView>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/DataView) **Default:** `2`
- `generatorEncoding` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) The [encoding](07-buffer-part1.md#buffers-and-character-encodings) of the `generator` string.
- Returns: [`<DiffieHellman>`](15-crypto-part1.md#class-diffiehellman)

Creates a `DiffieHellman` key exchange object using the supplied `prime` and an
optional specific `generator`.

The `generator` argument can be a number, string, or [`Buffer`](07-buffer-part1.md). If
`generator` is not specified, the value `2` is used.

If `primeEncoding` is specified, `prime` is expected to be a string; otherwise
a [`Buffer`](07-buffer-part1.md), `TypedArray`, or `DataView` is expected.

If `generatorEncoding` is specified, `generator` is expected to be a string;
otherwise a number, [`Buffer`](07-buffer-part1.md), `TypedArray`, or `DataView` is expected.

### `crypto.createDiffieHellman(primeLength[, generator])`

Added in: v0.5.0

- `primeLength` [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type)
- `generator` [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) **Default:** `2`
- Returns: [`<DiffieHellman>`](15-crypto-part1.md#class-diffiehellman)

Creates a `DiffieHellman` key exchange object and generates a prime of
`primeLength` bits using an optional specific numeric `generator`.
If `generator` is not specified, the value `2` is used.

### `crypto.createDiffieHellmanGroup(name)`

Added in: v0.9.3

- `name` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type)
- Returns: [`<DiffieHellmanGroup>`](15-crypto-part2.md#class-diffiehellmangroup)

An alias for [`crypto.getDiffieHellman()`](15-crypto-part4.md#cryptogetdiffiehellmangroupname)

### `crypto.createECDH(curveName)`

Added in: v0.11.14

- `curveName` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type)
- Returns: [`<ECDH>`](15-crypto-part2.md#class-ecdh)

Creates an Elliptic Curve Diffie-Hellman (`ECDH`) key exchange object using a
predefined curve specified by the `curveName` string. Use
[`crypto.getCurves()`](15-crypto-part4.md#cryptogetcurves) to obtain a list of available curve names. On recent
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
- `options` [`<Object>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Object) [`stream.transform` options](51-stream-part3.md#new-streamtransformoptions)
  - `customization` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) | [`<ArrayBuffer>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/ArrayBuffer) | [`<Buffer>`](07-buffer-part1.md#class-buffer) | [`<TypedArray>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/TypedArray) | [`<DataView>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/DataView) For cSHAKE
    hash functions, specifies the customization byte string. **Default:** an
    empty byte string.
  - `functionName` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) | [`<ArrayBuffer>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/ArrayBuffer) | [`<Buffer>`](07-buffer-part1.md#class-buffer) | [`<TypedArray>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/TypedArray) | [`<DataView>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/DataView) For cSHAKE
    hash functions, specifies the NIST function-name byte string. **Default:**
    an empty byte string.
  - `outputLength` [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) For XOF hash functions, specifies the desired
    output length in bytes.
- Returns: [`<Hash>`](15-crypto-part2.md#class-hash)

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

When the data is small (\< 5MB) and readily available, [`crypto.hash()`](15-crypto-part4.md#cryptohashalgorithm-data-options) is usually faster.

The available algorithms depend on the version and configuration of OpenSSL on
the platform. Examples are `'sha256'` and `'sha512'`. Use
[`crypto.getHashes()`](15-crypto-part4.md#cryptogethashes) to obtain the list of hash algorithms available to the
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
- `key` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) | [`<ArrayBuffer>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/ArrayBuffer) | [`<Buffer>`](07-buffer-part1.md#class-buffer) | [`<TypedArray>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/TypedArray) | [`<DataView>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/DataView) | [`<KeyObject>`](15-crypto-part2.md#class-keyobject) | [`<CryptoKey>`](65-webcrypto.md#class-cryptokey)
- `options` [`<Object>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Object) [`stream.transform` options](51-stream-part3.md#new-streamtransformoptions)
  - `encoding` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) The string encoding to use when `key` is a string.
- Returns: [`<Hmac>`](15-crypto-part2.md#class-hmac)

Creates and returns an `Hmac` object that uses the given `algorithm` and `key`.
Optional `options` argument controls stream behavior.

The available algorithms depend on the version and configuration of OpenSSL on
the platform. Examples are `'sha256'` and `'sha512'`.
[`crypto.getHashes()`](15-crypto-part4.md#cryptogethashes) lists algorithms available to the hashing APIs, but
HMAC imposes additional restrictions, so not every listed algorithm is
suitable.

The `key` is the HMAC key used to generate the cryptographic HMAC hash. If it is
a [`KeyObject`](15-crypto-part2.md#class-keyobject), its type must be `secret`. If it is a string, please consider
[caveats when using strings as inputs to cryptographic APIs](15-crypto-part6.md#using-strings-as-inputs-to-cryptographic-apis). If it was
obtained from a cryptographically secure source of entropy, such as
[`crypto.randomBytes()`](15-crypto-part5.md#cryptorandombytessize-callback) or [`crypto.generateKey()`](15-crypto-part4.md#cryptogeneratekeytype-options-callback), its length should not
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
- `key` [`<ArrayBuffer>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/ArrayBuffer) | [`<Buffer>`](07-buffer-part1.md#class-buffer) | [`<TypedArray>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/TypedArray) | [`<DataView>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/DataView) | [`<KeyObject>`](15-crypto-part2.md#class-keyobject)
- `options` [`<Object>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Object) [`stream.transform` options](51-stream-part3.md#new-streamtransformoptions)
  - `digest` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) The digest used by a MAC such as HMAC.
  - `cipher` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) The cipher used by a MAC such as CMAC or GMAC.
  - `iv` [`<ArrayBuffer>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/ArrayBuffer) | [`<Buffer>`](07-buffer-part1.md#class-buffer) | [`<TypedArray>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/TypedArray) | [`<DataView>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/DataView) The initialization vector for
    a MAC such as GMAC.
  - `customization` [`<ArrayBuffer>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/ArrayBuffer) | [`<Buffer>`](07-buffer-part1.md#class-buffer) | [`<TypedArray>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/TypedArray) | [`<DataView>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/DataView) A customization
    byte string for MACs that support it, such as KMAC.
  - `salt` [`<ArrayBuffer>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/ArrayBuffer) | [`<Buffer>`](07-buffer-part1.md#class-buffer) | [`<TypedArray>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/TypedArray) | [`<DataView>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/DataView) A salt byte string for MACs
    that support it, such as BLAKE2 MACs.
  - `outputLength` [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) The requested provider output size in bytes. Must be
    an unsigned 32-bit integer. Provider-specific restrictions also apply.
- Returns: {Mac}

`algorithm` must be a non-empty provider MAC name. The MAC-specific properties
listed above are extensions to the standard [`stream.transform` options](51-stream-part3.md#new-streamtransformoptions) and
are passed only when the selected provider implementation advertises the
corresponding parameter with the expected type. A supplied MAC-specific option
that the selected implementation does not support causes an error.

The following table summarizes the MAC-specific options accepted by MAC
implementations in OpenSSL's built-in providers. The `key` argument is required
for every MAC. The table lists only MAC-specific options; standard
[`stream.transform` options](51-stream-part3.md#new-streamtransformoptions) remain available for every family.

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

The `key` must contain bytes or be a [`KeyObject`](15-crypto-part2.md#class-keyobject) of type `secret`. Key
length and other key requirements are determined by the selected provider
implementation.

Available algorithms and their accepted parameters depend on the OpenSSL
version, loaded providers, and active default property query. Use
[`crypto.getMacs()`](15-crypto-part4.md#cryptogetmacs) to list fetchable MAC names. A listed name can still
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

- `key` [`<Object>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Object) | [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) | [`<ArrayBuffer>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/ArrayBuffer) | [`<Buffer>`](07-buffer-part1.md#class-buffer) | [`<TypedArray>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/TypedArray) | [`<DataView>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/DataView) | [`<URL>`](59-url.md#the-whatwg-url-api)
  - `key` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) | [`<ArrayBuffer>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/ArrayBuffer) | [`<Buffer>`](07-buffer-part1.md#class-buffer) | [`<TypedArray>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/TypedArray) | [`<DataView>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/DataView) | [`<Object>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Object) | [`<URL>`](59-url.md#the-whatwg-url-api) The key
    material, either in PEM, DER, JWK, or raw format, or a [`<URL>`](59-url.md#the-whatwg-url-api) referencing an
    object for an OpenSSL STORE loader.
  - `format` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) Must be `'pem'`, `'der'`, `'jwk'`, `'raw-private'`,
    or `'raw-seed'`. **Default:** `'pem'`.
  - `type` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) Must be `'pkcs1'`, `'pkcs8'` or `'sec1'`. This option is
    required only if the `format` is `'der'` and ignored otherwise.
  - `passphrase` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) | [`<Buffer>`](07-buffer-part1.md#class-buffer) The passphrase to use for decryption. When `key` is a [`<URL>`](59-url.md#the-whatwg-url-api), this is the optional PIN/passphrase forwarded to the
    STORE loader.
  - `properties` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) The optional OpenSSL property query used when
    fetching the STORE loader for a [`<URL>`](59-url.md#the-whatwg-url-api) key.
  - `encoding` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) The string encoding to use when `key` is a string.
  - `asymmetricKeyType` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) Required when `format` is `'raw-private'`
    or `'raw-seed'` and ignored otherwise.
    Must be a [supported key type](15-crypto-part1.md#asymmetric-key-types).
  - `namedCurve` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) Name of the curve to use. Required when `asymmetricKeyType` is `'ec'` and ignored otherwise.
- Returns: [`<KeyObject>`](15-crypto-part2.md#class-keyobject)

Creates and returns a new key object containing a private key. If `key` is a
string or `Buffer`, `format` is assumed to be `'pem'`; otherwise, `key`
must be an object with the properties described above.

If the private key is encrypted, a `passphrase` must be specified. The length
of the passphrase is limited to 1024 bytes.

[Contents](../README.md) | [Previous: Crypto, part 2 of 6](15-crypto-part2.md) | [Next: Crypto, part 4 of 6](15-crypto-part4.md)
