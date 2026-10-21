[Contents](../README.md) | [Previous: Errors, part 4 of 5](22-errors-part4.md) | [Next: Events](23-events.md)

Source: [https://nodejs.org/docs/latest/api/errors.html](https://nodejs.org/docs/latest/api/errors.html)

Part 5 of 5 of this section.

# Errors (part 5 of 5)

#### `UNABLE_TO_VERIFY_LEAF_SIGNATURE`

No signatures could be verified because the chain contains only one certificate
and it is not self signed.

#### `CERT_UNTRUSTED`

The root certificate authority (CA) is not marked as trusted for the specified
purpose.

### Basic Extension Errors

#### `INVALID_CA`

A CA certificate is invalid. Either it is not a CA or its extensions are not
consistent with the supplied purpose.

#### `PATH_LENGTH_EXCEEDED`

The basicConstraints pathlength parameter has been exceeded.

### Name Related Errors

#### `HOSTNAME_MISMATCH`

Certificate does not match provided name.

### Usage and Policy Errors

#### `INVALID_PURPOSE`

The supplied certificate cannot be used for the specified purpose.

#### `CERT_REJECTED`

The root CA is marked to reject the specified purpose.

### Formatting Errors

#### `CERT_SIGNATURE_FAILURE`

The signature of the certificate is invalid.

#### `CRL_SIGNATURE_FAILURE`

The signature of the certificate revocation list (CRL) is invalid.

#### `ERROR_IN_CERT_NOT_BEFORE_FIELD`

The certificate notBefore field contains an invalid time.

#### `ERROR_IN_CERT_NOT_AFTER_FIELD`

The certificate notAfter field contains an invalid time.

#### `ERROR_IN_CRL_LAST_UPDATE_FIELD`

The CRL lastUpdate field contains an invalid time.

#### `ERROR_IN_CRL_NEXT_UPDATE_FIELD`

The CRL nextUpdate field contains an invalid time.

#### `UNABLE_TO_DECRYPT_CERT_SIGNATURE`

The certificate signature could not be decrypted. This means that the actual
signature value could not be determined rather than it not matching the expected
value, this is only meaningful for RSA keys.

#### `UNABLE_TO_DECRYPT_CRL_SIGNATURE`

The certificate revocation list (CRL) signature could not be decrypted: this
means that the actual signature value could not be determined rather than it not
matching the expected value.

#### `UNABLE_TO_DECODE_ISSUER_PUBLIC_KEY`

The public key in the certificate SubjectPublicKeyInfo could not be read.

### Other OpenSSL Errors

#### `OUT_OF_MEM`

An error occurred trying to allocate memory. This should never happen.

[Contents](../README.md) | [Previous: Errors, part 4 of 5](22-errors-part4.md) | [Next: Events](23-events.md)
