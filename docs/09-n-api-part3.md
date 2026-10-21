[Contents](../README.md) | [Previous: C/C++ addons with Node-API, part 2 of 4](09-n-api-part2.md) | [Next: C/C++ addons with Node-API, part 4 of 4](09-n-api-part4.md)

Source: [https://nodejs.org/docs/latest/api/n-api.html](https://nodejs.org/docs/latest/api/n-api.html)

Part 3 of 4 of this section.

# C/C++ addons with Node-API (part 3 of 4)

Continues: Working with JavaScript values. Subsection: Functions to convert from Node-API to C types.

#### `napi_get_prototype`

Added in: v8.0.0**N-API Version:** 1

```c
napi_status napi_get_prototype(napi_env env,
                               napi_value object,
                               napi_value* result)
```

- `[in] env`: The environment that the API is invoked under.
- `[in] object`: `napi_value` representing JavaScript `Object` whose prototype
  to return. This returns the equivalent of `Object.getPrototypeOf` (which is
  not the same as the function's `prototype` property).
- `[out] result`: `napi_value` representing prototype of the given object.

Returns `napi_ok` if the API succeeded.

#### `napi_get_typedarray_info`

Added in: v8.0.0**N-API Version:** 1

```c
napi_status napi_get_typedarray_info(napi_env env,
                                     napi_value typedarray,
                                     napi_typedarray_type* type,
                                     size_t* length,
                                     void** data,
                                     napi_value* arraybuffer,
                                     size_t* byte_offset)
```

- `[in] env`: The environment that the API is invoked under.
- `[in] typedarray`: `napi_value` representing the `TypedArray` whose
  properties to query.
- `[out] type`: Scalar datatype of the elements within the `TypedArray`.
- `[out] length`: The number of elements in the `TypedArray`.
- `[out] data`: The data buffer underlying the `TypedArray` adjusted by
  the `byte_offset` value so that it points to the first element in the
  `TypedArray`. If the length of the array is `0`, this may be `NULL` or
  any other pointer value.
- `[out] arraybuffer`: The `ArrayBuffer` or `SharedArrayBuffer` underlying the
  `TypedArray`.
- `[out] byte_offset`: The byte offset within the underlying native array
  at which the first element of the arrays is located. The value for the data
  parameter has already been adjusted so that data points to the first element
  in the array. Therefore, the first byte of the native array would be at
  `data - byte_offset`.

Returns `napi_ok` if the API succeeded.

This API returns various properties of a typed array.

Any of the out parameters may be `NULL` if that property is unneeded.

*Warning*: Use caution while using this API since the underlying data buffer
is managed by the VM.

#### `napi_get_dataview_info`

Added in: v8.3.0**N-API Version:** 1

```c
napi_status napi_get_dataview_info(napi_env env,
                                   napi_value dataview,
                                   size_t* byte_length,
                                   void** data,
                                   napi_value* arraybuffer,
                                   size_t* byte_offset)
```

- `[in] env`: The environment that the API is invoked under.
- `[in] dataview`: `napi_value` representing the `DataView` whose
  properties to query.
- `[out] byte_length`: Number of bytes in the `DataView`.
- `[out] data`: The data buffer underlying the `DataView`.
  If byte\_length is `0`, this may be `NULL` or any other pointer value.
- `[out] arraybuffer`: `ArrayBuffer` underlying the `DataView`.
- `[out] byte_offset`: The byte offset within the data buffer from which
  to start projecting the `DataView`.

Returns `napi_ok` if the API succeeded.

Any of the out parameters may be `NULL` if that property is unneeded.

This API returns various properties of a `DataView`.

#### `napi_get_date_value`

Added in: v11.11.0, v10.17.0**N-API Version:** 5

```c
napi_status napi_get_date_value(napi_env env,
                                napi_value value,
                                double* result)
```

- `[in] env`: The environment that the API is invoked under.
- `[in] value`: `napi_value` representing a JavaScript `Date`.
- `[out] result`: Time value as a `double` represented as milliseconds since
  midnight at the beginning of 01 January, 1970 UTC.

This API does not observe leap seconds; they are ignored, as
ECMAScript aligns with POSIX time specification.

Returns `napi_ok` if the API succeeded. If a non-date `napi_value` is passed
in it returns `napi_date_expected`.

This API returns the C double primitive of time value for the given JavaScript
`Date`.

#### `napi_get_value_bool`

Added in: v8.0.0**N-API Version:** 1

```c
napi_status napi_get_value_bool(napi_env env, napi_value value, bool* result)
```

- `[in] env`: The environment that the API is invoked under.
- `[in] value`: `napi_value` representing JavaScript `Boolean`.
- `[out] result`: C boolean primitive equivalent of the given JavaScript
  `Boolean`.

Returns `napi_ok` if the API succeeded. If a non-boolean `napi_value` is
passed in it returns `napi_boolean_expected`.

This API returns the C boolean primitive equivalent of the given JavaScript
`Boolean`.

#### `napi_get_value_double`

Added in: v8.0.0**N-API Version:** 1

```c
napi_status napi_get_value_double(napi_env env,
                                  napi_value value,
                                  double* result)
```

- `[in] env`: The environment that the API is invoked under.
- `[in] value`: `napi_value` representing JavaScript `number`.
- `[out] result`: C double primitive equivalent of the given JavaScript
  `number`.

Returns `napi_ok` if the API succeeded. If a non-number `napi_value` is passed
in it returns `napi_number_expected`.

This API returns the C double primitive equivalent of the given JavaScript
`number`.

#### `napi_get_value_bigint_int64`

Added in: v10.7.0**N-API Version:** 6

```c
napi_status napi_get_value_bigint_int64(napi_env env,
                                        napi_value value,
                                        int64_t* result,
                                        bool* lossless);
```

- `[in] env`: The environment that the API is invoked under
- `[in] value`: `napi_value` representing JavaScript `BigInt`.
- `[out] result`: C `int64_t` primitive equivalent of the given JavaScript
  `BigInt`.
- `[out] lossless`: Indicates whether the `BigInt` value was converted
  losslessly.

Returns `napi_ok` if the API succeeded. If a non-`BigInt` is passed in it
returns `napi_bigint_expected`.

This API returns the C `int64_t` primitive equivalent of the given JavaScript
`BigInt`. If needed it will truncate the value, setting `lossless` to `false`.

#### `napi_get_value_bigint_uint64`

Added in: v10.7.0**N-API Version:** 6

```c
napi_status napi_get_value_bigint_uint64(napi_env env,
                                        napi_value value,
                                        uint64_t* result,
                                        bool* lossless);
```

- `[in] env`: The environment that the API is invoked under.
- `[in] value`: `napi_value` representing JavaScript `BigInt`.
- `[out] result`: C `uint64_t` primitive equivalent of the given JavaScript
  `BigInt`.
- `[out] lossless`: Indicates whether the `BigInt` value was converted
  losslessly.

Returns `napi_ok` if the API succeeded. If a non-`BigInt` is passed in it
returns `napi_bigint_expected`.

This API returns the C `uint64_t` primitive equivalent of the given JavaScript
`BigInt`. If needed it will truncate the value, setting `lossless` to `false`.

#### `napi_get_value_bigint_words`

Added in: v10.7.0**N-API Version:** 6

```c
napi_status napi_get_value_bigint_words(napi_env env,
                                        napi_value value,
                                        int* sign_bit,
                                        size_t* word_count,
                                        uint64_t* words);
```

- `[in] env`: The environment that the API is invoked under.
- `[in] value`: `napi_value` representing JavaScript `BigInt`.
- `[out] sign_bit`: Integer representing if the JavaScript `BigInt` is positive
  or negative.
- `[in/out] word_count`: Must be initialized to the length of the `words`
  array. Upon return, it will be set to the actual number of words that
  would be needed to store this `BigInt`.
- `[out] words`: Pointer to a pre-allocated 64-bit word array.

Returns `napi_ok` if the API succeeded.

This API converts a single `BigInt` value into a sign bit, 64-bit little-endian
array, and the number of elements in the array. `sign_bit` and `words` may be
both set to `NULL`, in order to get only `word_count`.

#### `napi_get_value_external`

Added in: v8.0.0**N-API Version:** 1

```c
napi_status napi_get_value_external(napi_env env,
                                    napi_value value,
                                    void** result)
```

- `[in] env`: The environment that the API is invoked under.
- `[in] value`: `napi_value` representing JavaScript external value.
- `[out] result`: Pointer to the data wrapped by the JavaScript external value.

Returns `napi_ok` if the API succeeded. If a non-external `napi_value` is
passed in it returns `napi_invalid_arg`.

This API retrieves the external data pointer that was previously passed to
`napi_create_external()`.

#### `napi_get_value_int32`

Added in: v8.0.0**N-API Version:** 1

```c
napi_status napi_get_value_int32(napi_env env,
                                 napi_value value,
                                 int32_t* result)
```

- `[in] env`: The environment that the API is invoked under.
- `[in] value`: `napi_value` representing JavaScript `number`.
- `[out] result`: C `int32` primitive equivalent of the given JavaScript
  `number`.

Returns `napi_ok` if the API succeeded. If a non-number `napi_value`
is passed in `napi_number_expected`.

This API returns the C `int32` primitive equivalent
of the given JavaScript `number`.

If the number exceeds the range of the 32 bit integer, then the result is
truncated to the equivalent of the bottom 32 bits. This can result in a large
positive number becoming a negative number if the value is > 231 - 1.

Non-finite number values (`NaN`, `+Infinity`, or `-Infinity`) set the
result to zero.

#### `napi_get_value_int64`

Added in: v8.0.0**N-API Version:** 1

```c
napi_status napi_get_value_int64(napi_env env,
                                 napi_value value,
                                 int64_t* result)
```

- `[in] env`: The environment that the API is invoked under.
- `[in] value`: `napi_value` representing JavaScript `number`.
- `[out] result`: C `int64` primitive equivalent of the given JavaScript
  `number`.

Returns `napi_ok` if the API succeeded. If a non-number `napi_value`
is passed in it returns `napi_number_expected`.

This API returns the C `int64` primitive equivalent of the given JavaScript
`number`.

`number` values outside the range of [`Number.MIN_SAFE_INTEGER`](https://tc39.es/ecma262/#sec-number.min_safe_integer)
`-(2**53 - 1)` - [`Number.MAX_SAFE_INTEGER`](https://tc39.es/ecma262/#sec-number.max_safe_integer) `(2**53 - 1)` will lose
precision.

Non-finite number values (`NaN`, `+Infinity`, or `-Infinity`) set the
result to zero.

#### `napi_get_value_string_latin1`

Added in: v8.0.0**N-API Version:** 1

```c
napi_status napi_get_value_string_latin1(napi_env env,
                                         napi_value value,
                                         char* buf,
                                         size_t bufsize,
                                         size_t* result)
```

- `[in] env`: The environment that the API is invoked under.
- `[in] value`: `napi_value` representing JavaScript string.
- `[in] buf`: Buffer to write the ISO-8859-1-encoded string into. If `NULL` is
  passed in, the length of the string in bytes and excluding the null terminator
  is returned in `result`.
- `[in] bufsize`: Size of the destination buffer. When this value is
  insufficient, the returned string is truncated and null-terminated.
  If this value is zero, then the string is not returned and no changes are done
  to the buffer.
- `[out] result`: Number of bytes copied into the buffer, excluding the null
  terminator.

Returns `napi_ok` if the API succeeded. If a non-`string` `napi_value`
is passed in it returns `napi_string_expected`.

This API returns the ISO-8859-1-encoded string corresponding the value passed
in.

#### `napi_get_value_string_utf8`

Added in: v8.0.0**N-API Version:** 1

```c
napi_status napi_get_value_string_utf8(napi_env env,
                                       napi_value value,
                                       char* buf,
                                       size_t bufsize,
                                       size_t* result)
```

- `[in] env`: The environment that the API is invoked under.
- `[in] value`: `napi_value` representing JavaScript string.
- `[in] buf`: Buffer to write the UTF8-encoded string into. If `NULL` is passed
  in, the length of the string in bytes and excluding the null terminator is
  returned in `result`.
- `[in] bufsize`: Size of the destination buffer. When this value is
  insufficient, the returned string is truncated and null-terminated.
  If this value is zero, then the string is not returned and no changes are done
  to the buffer.
- `[out] result`: Number of bytes copied into the buffer, excluding the null
  terminator.

Returns `napi_ok` if the API succeeded. If a non-`string` `napi_value`
is passed in it returns `napi_string_expected`.

This API returns the UTF8-encoded string corresponding the value passed in.

#### `napi_get_value_string_utf16`

Added in: v8.0.0**N-API Version:** 1

```c
napi_status napi_get_value_string_utf16(napi_env env,
                                        napi_value value,
                                        char16_t* buf,
                                        size_t bufsize,
                                        size_t* result)
```

- `[in] env`: The environment that the API is invoked under.
- `[in] value`: `napi_value` representing JavaScript string.
- `[in] buf`: Buffer to write the UTF16-LE-encoded string into. If `NULL` is
  passed in, the length of the string in 2-byte code units and excluding the
  null terminator is returned.
- `[in] bufsize`: Size of the destination buffer. When this value is
  insufficient, the returned string is truncated and null-terminated.
  If this value is zero, then the string is not returned and no changes are done
  to the buffer.
- `[out] result`: Number of 2-byte code units copied into the buffer, excluding
  the null terminator.

Returns `napi_ok` if the API succeeded. If a non-`string` `napi_value`
is passed in it returns `napi_string_expected`.

This API returns the UTF16-encoded string corresponding the value passed in.

#### `napi_get_value_uint32`

Added in: v8.0.0**N-API Version:** 1

```c
napi_status napi_get_value_uint32(napi_env env,
                                  napi_value value,
                                  uint32_t* result)
```

- `[in] env`: The environment that the API is invoked under.
- `[in] value`: `napi_value` representing JavaScript `number`.
- `[out] result`: C primitive equivalent of the given `napi_value` as a
  `uint32_t`.

Returns `napi_ok` if the API succeeded. If a non-number `napi_value`
is passed in it returns `napi_number_expected`.

This API returns the C primitive equivalent of the given `napi_value` as a
`uint32_t`.

### Functions to get global instances

#### `napi_get_boolean`

Added in: v8.0.0**N-API Version:** 1

```c
napi_status napi_get_boolean(napi_env env, bool value, napi_value* result)
```

- `[in] env`: The environment that the API is invoked under.
- `[in] value`: The value of the boolean to retrieve.
- `[out] result`: `napi_value` representing JavaScript `Boolean` singleton to
  retrieve.

Returns `napi_ok` if the API succeeded.

This API is used to return the JavaScript singleton object that is used to
represent the given boolean value.

#### `napi_get_global`

Added in: v8.0.0**N-API Version:** 1

```c
napi_status napi_get_global(napi_env env, napi_value* result)
```

- `[in] env`: The environment that the API is invoked under.
- `[out] result`: `napi_value` representing JavaScript `global` object.

Returns `napi_ok` if the API succeeded.

This API returns the `global` object.

#### `napi_get_null`

Added in: v8.0.0**N-API Version:** 1

```c
napi_status napi_get_null(napi_env env, napi_value* result)
```

- `[in] env`: The environment that the API is invoked under.
- `[out] result`: `napi_value` representing JavaScript `null` object.

Returns `napi_ok` if the API succeeded.

This API returns the `null` object.

#### `napi_get_undefined`

Added in: v8.0.0**N-API Version:** 1

```c
napi_status napi_get_undefined(napi_env env, napi_value* result)
```

- `[in] env`: The environment that the API is invoked under.
- `[out] result`: `napi_value` representing JavaScript Undefined value.

Returns `napi_ok` if the API succeeded.

This API returns the Undefined object.

## Working with JavaScript values and abstract operations

Node-API exposes a set of APIs to perform some abstract operations on JavaScript
values.

These APIs support doing one of the following:

1. Coerce JavaScript values to specific JavaScript types (such as `number` or
   `string`).
2. Check the type of a JavaScript value.
3. Check for equality between two JavaScript values.

### `napi_coerce_to_bool`

Added in: v8.0.0**N-API Version:** 1

```c
napi_status napi_coerce_to_bool(napi_env env,
                                napi_value value,
                                napi_value* result)
```

- `[in] env`: The environment that the API is invoked under.
- `[in] value`: The JavaScript value to coerce.
- `[out] result`: `napi_value` representing the coerced JavaScript `Boolean`.

Returns `napi_ok` if the API succeeded.

This API implements the abstract operation `ToBoolean()` as defined in
[Section ToBoolean](https://tc39.es/ecma262/#sec-toboolean) of the ECMAScript Language Specification.

### `napi_coerce_to_number`

Added in: v8.0.0**N-API Version:** 1

```c
napi_status napi_coerce_to_number(napi_env env,
                                  napi_value value,
                                  napi_value* result)
```

- `[in] env`: The environment that the API is invoked under.
- `[in] value`: The JavaScript value to coerce.
- `[out] result`: `napi_value` representing the coerced JavaScript `number`.

Returns `napi_ok` if the API succeeded.

This API implements the abstract operation `ToNumber()` as defined in
[Section ToNumber](https://tc39.es/ecma262/#sec-tonumber) of the ECMAScript Language Specification.
This function potentially runs JS code if the passed-in value is an
object.

### `napi_coerce_to_object`

Added in: v8.0.0**N-API Version:** 1

```c
napi_status napi_coerce_to_object(napi_env env,
                                  napi_value value,
                                  napi_value* result)
```

- `[in] env`: The environment that the API is invoked under.
- `[in] value`: The JavaScript value to coerce.
- `[out] result`: `napi_value` representing the coerced JavaScript `Object`.

Returns `napi_ok` if the API succeeded.

This API implements the abstract operation `ToObject()` as defined in
[Section ToObject](https://tc39.es/ecma262/#sec-toobject) of the ECMAScript Language Specification.

### `napi_coerce_to_string`

Added in: v8.0.0**N-API Version:** 1

```c
napi_status napi_coerce_to_string(napi_env env,
                                  napi_value value,
                                  napi_value* result)
```

- `[in] env`: The environment that the API is invoked under.
- `[in] value`: The JavaScript value to coerce.
- `[out] result`: `napi_value` representing the coerced JavaScript `string`.

Returns `napi_ok` if the API succeeded.

This API implements the abstract operation `ToString()` as defined in
[Section ToString](https://tc39.es/ecma262/#sec-tostring) of the ECMAScript Language Specification.
This function potentially runs JS code if the passed-in value is an
object.

### `napi_typeof`

Added in: v8.0.0**N-API Version:** 1

```c
napi_status napi_typeof(napi_env env, napi_value value, napi_valuetype* result)
```

- `[in] env`: The environment that the API is invoked under.
- `[in] value`: The JavaScript value whose type to query.
- `[out] result`: The type of the JavaScript value.

Returns `napi_ok` if the API succeeded.

- `napi_invalid_arg` if the type of `value` is not a known ECMAScript type and
  `value` is not an External value.

This API represents behavior similar to invoking the `typeof` Operator on
the object as defined in [Section typeof operator](https://tc39.es/ecma262/#sec-typeof-operator) of the ECMAScript Language
Specification. However, there are some differences:

1. It has support for detecting an External value.
2. It detects `null` as a separate type, while ECMAScript `typeof` would detect
   `object`.

If `value` has a type that is invalid, an error is returned.

### `napi_instanceof`

Added in: v8.0.0**N-API Version:** 1

```c
napi_status napi_instanceof(napi_env env,
                            napi_value object,
                            napi_value constructor,
                            bool* result)
```

- `[in] env`: The environment that the API is invoked under.
- `[in] object`: The JavaScript value to check.
- `[in] constructor`: The JavaScript function object of the constructor function
  to check against.
- `[out] result`: Boolean that is set to true if `object instanceof constructor`
  is true.

Returns `napi_ok` if the API succeeded.

This API represents invoking the `instanceof` Operator on the object as
defined in [Section instanceof operator](https://tc39.es/ecma262/#sec-instanceofoperator) of the ECMAScript Language Specification.

### `napi_is_array`

Added in: v8.0.0**N-API Version:** 1

```c
napi_status napi_is_array(napi_env env, napi_value value, bool* result)
```

- `[in] env`: The environment that the API is invoked under.
- `[in] value`: The JavaScript value to check.
- `[out] result`: Whether the given object is an array.

Returns `napi_ok` if the API succeeded.

This API represents invoking the `IsArray` operation on the object
as defined in [Section IsArray](https://tc39.es/ecma262/#sec-isarray) of the ECMAScript Language Specification.

### `napi_is_arraybuffer`

Added in: v8.0.0**N-API Version:** 1

```c
napi_status napi_is_arraybuffer(napi_env env, napi_value value, bool* result)
```

- `[in] env`: The environment that the API is invoked under.
- `[in] value`: The JavaScript value to check.
- `[out] result`: Whether the given object is an `ArrayBuffer`.

Returns `napi_ok` if the API succeeded.

This API checks if the `Object` passed in is an array buffer.

### `napi_is_buffer`

Added in: v8.0.0**N-API Version:** 1

```c
napi_status napi_is_buffer(napi_env env, napi_value value, bool* result)
```

- `[in] env`: The environment that the API is invoked under.
- `[in] value`: The JavaScript value to check.
- `[out] result`: Whether the given `napi_value` represents a `node::Buffer` or
  `Uint8Array` object.

Returns `napi_ok` if the API succeeded.

This API checks if the `Object` passed in is a buffer or Uint8Array.
[`napi_is_typedarray`](#napi_is_typedarray) should be preferred if the caller needs to check if the
value is a Uint8Array.

### `napi_is_date`

Added in: v11.11.0, v10.17.0**N-API Version:** 5

```c
napi_status napi_is_date(napi_env env, napi_value value, bool* result)
```

- `[in] env`: The environment that the API is invoked under.
- `[in] value`: The JavaScript value to check.
- `[out] result`: Whether the given `napi_value` represents a JavaScript `Date`
  object.

Returns `napi_ok` if the API succeeded.

This API checks if the `Object` passed in is a date.

### `napi_is_error`

Added in: v8.0.0**N-API Version:** 1

```c
napi_status napi_is_error(napi_env env, napi_value value, bool* result)
```

- `[in] env`: The environment that the API is invoked under.
- `[in] value`: The JavaScript value to check.
- `[out] result`: Whether the given `napi_value` represents an `Error` object.

Returns `napi_ok` if the API succeeded.

This API checks if the `Object` passed in is an `Error`.

### `napi_is_typedarray`

Added in: v8.0.0**N-API Version:** 1

```c
napi_status napi_is_typedarray(napi_env env, napi_value value, bool* result)
```

- `[in] env`: The environment that the API is invoked under.
- `[in] value`: The JavaScript value to check.
- `[out] result`: Whether the given `napi_value` represents a `TypedArray`.

Returns `napi_ok` if the API succeeded.

This API checks if the `Object` passed in is a typed array.

### `napi_is_dataview`

Added in: v8.3.0**N-API Version:** 1

```c
napi_status napi_is_dataview(napi_env env, napi_value value, bool* result)
```

- `[in] env`: The environment that the API is invoked under.
- `[in] value`: The JavaScript value to check.
- `[out] result`: Whether the given `napi_value` represents a `DataView`.

Returns `napi_ok` if the API succeeded.

This API checks if the `Object` passed in is a `DataView`.

### `napi_strict_equals`

Added in: v8.0.0**N-API Version:** 1

```c
napi_status napi_strict_equals(napi_env env,
                               napi_value lhs,
                               napi_value rhs,
                               bool* result)
```

- `[in] env`: The environment that the API is invoked under.
- `[in] lhs`: The JavaScript value to check.
- `[in] rhs`: The JavaScript value to check against.
- `[out] result`: Whether the two `napi_value` objects are equal.

Returns `napi_ok` if the API succeeded.

This API represents the invocation of the Strict Equality algorithm as
defined in [Section IsStrictlyEqual](https://tc39.es/ecma262/#sec-strict-equality-comparison) of the ECMAScript Language Specification.

### `napi_detach_arraybuffer`

Added in: v13.0.0, v12.16.0, v10.22.0**N-API Version:** 7

```c
napi_status napi_detach_arraybuffer(napi_env env,
                                    napi_value arraybuffer)
```

- `[in] env`: The environment that the API is invoked under.
- `[in] arraybuffer`: The JavaScript `ArrayBuffer` to be detached.

Returns `napi_ok` if the API succeeded. If a non-detachable `ArrayBuffer` is
passed in it returns `napi_detachable_arraybuffer_expected`.

Generally, an `ArrayBuffer` is non-detachable if it has been detached before.
The engine may impose additional conditions on whether an `ArrayBuffer` is
detachable. For example, V8 requires that the `ArrayBuffer` be external,
that is, created with [`napi_create_external_arraybuffer`](09-n-api-part2.md#napi_create_external_arraybuffer).

This API represents the invocation of the `ArrayBuffer` detach operation as
defined in [Section detachArrayBuffer](https://tc39.es/ecma262/#sec-detacharraybuffer) of the ECMAScript Language Specification.

### `napi_is_detached_arraybuffer`

Added in: v13.3.0, v12.16.0, v10.22.0**N-API Version:** 7

```c
napi_status napi_is_detached_arraybuffer(napi_env env,
                                         napi_value arraybuffer,
                                         bool* result)
```

- `[in] env`: The environment that the API is invoked under.
- `[in] arraybuffer`: The JavaScript `ArrayBuffer` to be checked.
- `[out] result`: Whether the `arraybuffer` is detached.

Returns `napi_ok` if the API succeeded.

The `ArrayBuffer` is considered detached if its internal data is `null`.

This API represents the invocation of the `ArrayBuffer` `IsDetachedBuffer`
operation as defined in [Section isDetachedBuffer](https://tc39.es/ecma262/#sec-isdetachedbuffer) of the ECMAScript Language
Specification.

### `node_api_is_sharedarraybuffer`

Added in: v24.9.0, v22.21.0

Stability: 1 - Experimental

```c
napi_status node_api_is_sharedarraybuffer(napi_env env, napi_value value, bool* result)
```

- `[in] env`: The environment that the API is invoked under.
- `[in] value`: The JavaScript value to check.
- `[out] result`: Whether the given `napi_value` represents a `SharedArrayBuffer`.

Returns `napi_ok` if the API succeeded.

This API checks if the Object passed in is a `SharedArrayBuffer`.

### `node_api_create_sharedarraybuffer`

Added in: v24.9.0, v22.21.0

Stability: 1 - Experimental

```c
napi_status node_api_create_sharedarraybuffer(napi_env env,
                                             size_t byte_length,
                                             void** data,
                                             napi_value* result)
```

- `[in] env`: The environment that the API is invoked under.
- `[in] byte_length`: The length in bytes of the shared array buffer to create.
- `[out] data`: Pointer to the underlying byte buffer of the `SharedArrayBuffer`.
  `data` can optionally be ignored by passing `NULL`.
- `[out] result`: A `napi_value` representing a JavaScript `SharedArrayBuffer`.

Returns `napi_ok` if the API succeeded.

This API returns a Node-API value corresponding to a JavaScript `SharedArrayBuffer`.
`SharedArrayBuffer`s are used to represent fixed-length binary data buffers that
can be shared across multiple workers.

The `SharedArrayBuffer` allocated will have an underlying byte buffer whose size is
determined by the `byte_length` parameter that's passed in.
The underlying buffer is optionally returned back to the caller in case the
caller wants to directly manipulate the buffer. This buffer can only be
written to directly from native code. To write to this buffer from JavaScript,
a typed array or `DataView` object would need to be created.

JavaScript `SharedArrayBuffer` objects are described in
[Section SharedArrayBuffer objects](https://tc39.es/ecma262/#sec-sharedarraybuffer-objects) of the ECMAScript Language Specification.

## Working with JavaScript properties

Node-API exposes a set of APIs to get and set properties on JavaScript
objects.

Properties in JavaScript are represented as a tuple of a key and a value.
Fundamentally, all property keys in Node-API can be represented in one of the
following forms:

- Named: a simple UTF8-encoded string
- Integer-Indexed: an index value represented by `uint32_t`
- JavaScript value: these are represented in Node-API by `napi_value`. This can
  be a `napi_value` representing a `string`, `number`, or `symbol`.

Node-API values are represented by the type `napi_value`.
Any Node-API call that requires a JavaScript value takes in a `napi_value`.
However, it's the caller's responsibility to make sure that the
`napi_value` in question is of the JavaScript type expected by the API.

The APIs documented in this section provide a simple interface to
get and set properties on arbitrary JavaScript objects represented by
`napi_value`.

For instance, consider the following JavaScript code snippet:

```js
const obj = {};
obj.myProp = 123;
```

The equivalent can be done using Node-API values with the following snippet:

```c
napi_status status = napi_generic_failure;

// const obj = {}
napi_value obj, value;
status = napi_create_object(env, &obj);
if (status != napi_ok) return status;

// Create a napi_value for 123
status = napi_create_int32(env, 123, &value);
if (status != napi_ok) return status;

// obj.myProp = 123
status = napi_set_named_property(env, obj, "myProp", value);
if (status != napi_ok) return status;
```

Indexed properties can be set in a similar manner. Consider the following
JavaScript snippet:

```js
const arr = [];
arr[123] = 'hello';
```

The equivalent can be done using Node-API values with the following snippet:

```c
napi_status status = napi_generic_failure;

// const arr = [];
napi_value arr, value;
status = napi_create_array(env, &arr);
if (status != napi_ok) return status;

// Create a napi_value for 'hello'
status = napi_create_string_utf8(env, "hello", NAPI_AUTO_LENGTH, &value);
if (status != napi_ok) return status;

// arr[123] = 'hello';
status = napi_set_element(env, arr, 123, value);
if (status != napi_ok) return status;
```

Properties can be retrieved using the APIs described in this section.
Consider the following JavaScript snippet:

```js
const arr = [];
const value = arr[123];
```

The following is the approximate equivalent of the Node-API counterpart:

```c
napi_status status = napi_generic_failure;

// const arr = []
napi_value arr, value;
status = napi_create_array(env, &arr);
if (status != napi_ok) return status;

// const value = arr[123]
status = napi_get_element(env, arr, 123, &value);
if (status != napi_ok) return status;
```

Finally, multiple properties can also be defined on an object for performance
reasons. Consider the following JavaScript:

```js
const obj = {};
Object.defineProperties(obj, {
  'foo': { value: 123, writable: true, configurable: true, enumerable: true },
  'bar': { value: 456, writable: true, configurable: true, enumerable: true },
});
```

The following is the approximate equivalent of the Node-API counterpart:

```c
napi_status status = napi_status_generic_failure;

// const obj = {};
napi_value obj;
status = napi_create_object(env, &obj);
if (status != napi_ok) return status;

// Create napi_values for 123 and 456
napi_value fooValue, barValue;
status = napi_create_int32(env, 123, &fooValue);
if (status != napi_ok) return status;
status = napi_create_int32(env, 456, &barValue);
if (status != napi_ok) return status;

// Set the properties
napi_property_descriptor descriptors[] = {
  { "foo", NULL, NULL, NULL, NULL, fooValue, napi_writable | napi_configurable, NULL },
  { "bar", NULL, NULL, NULL, NULL, barValue, napi_writable | napi_configurable, NULL }
}
status = napi_define_properties(env,
                                obj,
                                sizeof(descriptors) / sizeof(descriptors[0]),
                                descriptors);
if (status != napi_ok) return status;
```

### Structures

#### `napi_property_attributes`

**History**

| Version | Changes |
| --- | --- |
| v14.12.0 | added `napi_default_method` and `napi_default_property`. |

```c
typedef enum {
  napi_default = 0,
  napi_writable = 1 << 0,
  napi_enumerable = 1 << 1,
  napi_configurable = 1 << 2,

  // Used with napi_define_class to distinguish static properties
  // from instance properties. Ignored by napi_define_properties.
  napi_static = 1 << 10,

  // Default for class methods.
  napi_default_method = napi_writable | napi_configurable,

  // Default for object properties, like in JS obj[prop].
  napi_default_jsproperty = napi_writable |
                          napi_enumerable |
                          napi_configurable,
} napi_property_attributes;
```

`napi_property_attributes` are bit flags used to control the behavior of
properties set on a JavaScript object. Other than `napi_static` they
correspond to the attributes listed in [Section property attributes](https://tc39.es/ecma262/#sec-property-attributes)
of the [ECMAScript Language Specification](https://tc39.es/ecma262/).
They can be one or more of the following bit flags:

- `napi_default`: No explicit attributes are set on the property. By default, a
  property is read only, not enumerable and not configurable.
- `napi_writable`: The property is writable.
- `napi_enumerable`: The property is enumerable.
- `napi_configurable`: The property is configurable as defined in
  [Section property attributes](https://tc39.es/ecma262/#sec-property-attributes) of the [ECMAScript Language Specification](https://tc39.es/ecma262/).
- `napi_static`: The property will be defined as a static property on a class as
  opposed to an instance property, which is the default. This is used only by
  [`napi_define_class`](09-n-api-part4.md#napi_define_class). It is ignored by `napi_define_properties`.
- `napi_default_method`: Like a method in a JS class, the property is
  configurable and writable, but not enumerable.
- `napi_default_jsproperty`: Like a property set via assignment in JavaScript,
  the property is writable, enumerable, and configurable.

#### `napi_property_descriptor`

```c
typedef struct {
  // One of utf8name or name should be NULL.
  const char* utf8name;
  napi_value name;

  napi_callback method;
  napi_callback getter;
  napi_callback setter;
  napi_value value;

  napi_property_attributes attributes;
  void* data;
} napi_property_descriptor;
```

- `utf8name`: Optional string describing the key for the property,
  encoded as UTF8. One of `utf8name` or `name` must be provided for the
  property.
- `name`: Optional `napi_value` that points to a JavaScript string or symbol
  to be used as the key for the property. One of `utf8name` or `name` must
  be provided for the property.
- `value`: The value that's retrieved by a get access of the property if the
  property is a data property. If this is passed in, set `getter`, `setter`,
  `method` and `data` to `NULL` (since these members won't be used).
- `getter`: A function to call when a get access of the property is performed.
  If this is passed in, set `value` and `method` to `NULL` (since these members
  won't be used). The given function is called implicitly by the runtime when
  the property is accessed from JavaScript code (or if a get on the property is
  performed using a Node-API call). [`napi_callback`](09-n-api-part1.md#napi_callback) provides more details.
- `setter`: A function to call when a set access of the property is performed.
  If this is passed in, set `value` and `method` to `NULL` (since these members
  won't be used). The given function is called implicitly by the runtime when
  the property is set from JavaScript code (or if a set on the property is
  performed using a Node-API call). [`napi_callback`](09-n-api-part1.md#napi_callback) provides more details.
- `method`: Set this to make the property descriptor object's `value`
  property to be a JavaScript function represented by `method`. If this is
  passed in, set `value`, `getter` and `setter` to `NULL` (since these members
  won't be used). [`napi_callback`](09-n-api-part1.md#napi_callback) provides more details.
- `attributes`: The attributes associated with the particular property. See
  [`napi_property_attributes`](#napi_property_attributes).
- `data`: The callback data passed into `method`, `getter` and `setter` if this
  function is invoked.

### Functions

#### `napi_get_property_names`

Added in: v8.0.0**N-API Version:** 1

```c
napi_status napi_get_property_names(napi_env env,
                                    napi_value object,
                                    napi_value* result);
```

- `[in] env`: The environment that the Node-API call is invoked under.
- `[in] object`: The object from which to retrieve the properties.
- `[out] result`: A `napi_value` representing an array of JavaScript values
  that represent the property names of the object. The API can be used to
  iterate over `result` using [`napi_get_array_length`](09-n-api-part2.md#napi_get_array_length)
  and [`napi_get_element`](#napi_get_element).

Returns `napi_ok` if the API succeeded.

This API returns the names of the enumerable properties of `object` as an array
of strings. The properties of `object` whose key is a symbol will not be
included.

#### `napi_get_all_property_names`

Added in: v13.7.0, v12.17.0, v10.20.0**N-API Version:** 6

```c
napi_get_all_property_names(napi_env env,
                            napi_value object,
                            napi_key_collection_mode key_mode,
                            napi_key_filter key_filter,
                            napi_key_conversion key_conversion,
                            napi_value* result);
```

- `[in] env`: The environment that the Node-API call is invoked under.
- `[in] object`: The object from which to retrieve the properties.
- `[in] key_mode`: Whether to retrieve prototype properties as well.
- `[in] key_filter`: Which properties to retrieve
  (enumerable/readable/writable).
- `[in] key_conversion`: Whether to convert numbered property keys to strings.
- `[out] result`: A `napi_value` representing an array of JavaScript values
  that represent the property names of the object. [`napi_get_array_length`](09-n-api-part2.md#napi_get_array_length)
  and [`napi_get_element`](#napi_get_element) can be used to iterate over `result`.

Returns `napi_ok` if the API succeeded.

This API returns an array containing the names of the available properties
of this object.

#### `napi_set_property`

Added in: v8.0.0**N-API Version:** 1

```c
napi_status napi_set_property(napi_env env,
                              napi_value object,
                              napi_value key,
                              napi_value value);
```

- `[in] env`: The environment that the Node-API call is invoked under.
- `[in] object`: The object on which to set the property.
- `[in] key`: The name of the property to set.
- `[in] value`: The property value.

Returns `napi_ok` if the API succeeded.

This API set a property on the `Object` passed in.

#### `napi_get_property`

Added in: v8.0.0**N-API Version:** 1

```c
napi_status napi_get_property(napi_env env,
                              napi_value object,
                              napi_value key,
                              napi_value* result);
```

- `[in] env`: The environment that the Node-API call is invoked under.
- `[in] object`: The object from which to retrieve the property.
- `[in] key`: The name of the property to retrieve.
- `[out] result`: The value of the property.

Returns `napi_ok` if the API succeeded.

This API gets the requested property from the `Object` passed in.

#### `napi_has_property`

Added in: v8.0.0**N-API Version:** 1

```c
napi_status napi_has_property(napi_env env,
                              napi_value object,
                              napi_value key,
                              bool* result);
```

- `[in] env`: The environment that the Node-API call is invoked under.
- `[in] object`: The object to query.
- `[in] key`: The name of the property whose existence to check.
- `[out] result`: Whether the property exists on the object or not.

Returns `napi_ok` if the API succeeded.

This API checks if the `Object` passed in has the named property.

#### `napi_delete_property`

Added in: v8.2.0**N-API Version:** 1

```c
napi_status napi_delete_property(napi_env env,
                                 napi_value object,
                                 napi_value key,
                                 bool* result);
```

- `[in] env`: The environment that the Node-API call is invoked under.
- `[in] object`: The object to query.
- `[in] key`: The name of the property to delete.
- `[out] result`: Whether the property deletion succeeded or not. `result` can
  optionally be ignored by passing `NULL`.

Returns `napi_ok` if the API succeeded.

This API attempts to delete the `key` own property from `object`.

#### `napi_has_own_property`

Added in: v8.2.0**N-API Version:** 1

```c
napi_status napi_has_own_property(napi_env env,
                                  napi_value object,
                                  napi_value key,
                                  bool* result);
```

- `[in] env`: The environment that the Node-API call is invoked under.
- `[in] object`: The object to query.
- `[in] key`: The name of the own property whose existence to check.
- `[out] result`: Whether the own property exists on the object or not.

Returns `napi_ok` if the API succeeded.

This API checks if the `Object` passed in has the named own property. `key` must
be a `string` or a `symbol`, or an error will be thrown. Node-API will not
perform any conversion between data types.

#### `napi_set_named_property`

Added in: v8.0.0**N-API Version:** 1

```c
napi_status napi_set_named_property(napi_env env,
                                    napi_value object,
                                    const char* utf8Name,
                                    napi_value value);
```

- `[in] env`: The environment that the Node-API call is invoked under.
- `[in] object`: The object on which to set the property.
- `[in] utf8Name`: The name of the property to set.
- `[in] value`: The property value.

Returns `napi_ok` if the API succeeded.

This method is equivalent to calling [`napi_set_property`](#napi_set_property) with a `napi_value`
created from the string passed in as `utf8Name`.

#### `napi_get_named_property`

Added in: v8.0.0**N-API Version:** 1

```c
napi_status napi_get_named_property(napi_env env,
                                    napi_value object,
                                    const char* utf8Name,
                                    napi_value* result);
```

- `[in] env`: The environment that the Node-API call is invoked under.
- `[in] object`: The object from which to retrieve the property.
- `[in] utf8Name`: The name of the property to get.
- `[out] result`: The value of the property.

Returns `napi_ok` if the API succeeded.

This method is equivalent to calling [`napi_get_property`](#napi_get_property) with a `napi_value`
created from the string passed in as `utf8Name`.

#### `napi_has_named_property`

Added in: v8.0.0**N-API Version:** 1

```c
napi_status napi_has_named_property(napi_env env,
                                    napi_value object,
                                    const char* utf8Name,
                                    bool* result);
```

- `[in] env`: The environment that the Node-API call is invoked under.
- `[in] object`: The object to query.
- `[in] utf8Name`: The name of the property whose existence to check.
- `[out] result`: Whether the property exists on the object or not.

Returns `napi_ok` if the API succeeded.

This method is equivalent to calling [`napi_has_property`](#napi_has_property) with a `napi_value`
created from the string passed in as `utf8Name`.

#### `napi_set_element`

Added in: v8.0.0**N-API Version:** 1

```c
napi_status napi_set_element(napi_env env,
                             napi_value object,
                             uint32_t index,
                             napi_value value);
```

- `[in] env`: The environment that the Node-API call is invoked under.
- `[in] object`: The object from which to set the properties.
- `[in] index`: The index of the property to set.
- `[in] value`: The property value.

Returns `napi_ok` if the API succeeded.

This API sets an element on the `Object` passed in.

#### `napi_get_element`

Added in: v8.0.0**N-API Version:** 1

```c
napi_status napi_get_element(napi_env env,
                             napi_value object,
                             uint32_t index,
                             napi_value* result);
```

- `[in] env`: The environment that the Node-API call is invoked under.
- `[in] object`: The object from which to retrieve the property.
- `[in] index`: The index of the property to get.
- `[out] result`: The value of the property.

Returns `napi_ok` if the API succeeded.

This API gets the element at the requested index.

#### `napi_has_element`

Added in: v8.0.0**N-API Version:** 1

```c
napi_status napi_has_element(napi_env env,
                             napi_value object,
                             uint32_t index,
                             bool* result);
```

- `[in] env`: The environment that the Node-API call is invoked under.
- `[in] object`: The object to query.
- `[in] index`: The index of the property whose existence to check.
- `[out] result`: Whether the property exists on the object or not.

Returns `napi_ok` if the API succeeded.

This API returns if the `Object` passed in has an element at the
requested index.

#### `napi_delete_element`

Added in: v8.2.0**N-API Version:** 1

```c
napi_status napi_delete_element(napi_env env,
                                napi_value object,
                                uint32_t index,
                                bool* result);
```

- `[in] env`: The environment that the Node-API call is invoked under.
- `[in] object`: The object to query.
- `[in] index`: The index of the property to delete.
- `[out] result`: Whether the element deletion succeeded or not. `result` can
  optionally be ignored by passing `NULL`.

Returns `napi_ok` if the API succeeded.

This API attempts to delete the specified `index` from `object`.

#### `napi_define_properties`

Added in: v8.0.0**N-API Version:** 1

```c
napi_status napi_define_properties(napi_env env,
                                   napi_value object,
                                   size_t property_count,
                                   const napi_property_descriptor* properties);
```

- `[in] env`: The environment that the Node-API call is invoked under.
- `[in] object`: The object from which to retrieve the properties.
- `[in] property_count`: The number of elements in the `properties` array.
- `[in] properties`: The array of property descriptors.

Returns `napi_ok` if the API succeeded.

This method allows the efficient definition of multiple properties on a given
object. The properties are defined using property descriptors (see
[`napi_property_descriptor`](#napi_property_descriptor)). Given an array of such property descriptors,
this API will set the properties on the object one at a time, as defined by
`DefineOwnProperty()` (described in [Section DefineOwnProperty](https://tc39.es/ecma262/#sec-ordinary-object-internal-methods-and-internal-slots-defineownproperty-p-desc) of the ECMA-262
specification).

#### `napi_object_freeze`

Added in: v14.14.0, v12.20.0**N-API Version:** 8

```c
napi_status napi_object_freeze(napi_env env,
                               napi_value object);
```

- `[in] env`: The environment that the Node-API call is invoked under.
- `[in] object`: The object to freeze.

Returns `napi_ok` if the API succeeded.

This method freezes a given object. This prevents new properties from
being added to it, existing properties from being removed, prevents
changing the enumerability, configurability, or writability of existing
properties, and prevents the values of existing properties from being changed.
It also prevents the object's prototype from being changed. This is described
in [Section 19.1.2.6](https://tc39.es/ecma262/#sec-object.freeze) of the
ECMA-262 specification.

#### `napi_object_seal`

Added in: v14.14.0, v12.20.0**N-API Version:** 8

```c
napi_status napi_object_seal(napi_env env,
                             napi_value object);
```

- `[in] env`: The environment that the Node-API call is invoked under.
- `[in] object`: The object to seal.

Returns `napi_ok` if the API succeeded.

This method seals a given object. This prevents new properties from being
added to it, as well as marking all existing properties as non-configurable.
This is described in [Section 19.1.2.20](https://tc39.es/ecma262/#sec-object.seal)
of the ECMA-262 specification.

#### `node_api_set_prototype`

Added in: v25.4.0, v24.13.1

Stability: 1 - Experimental

```c
napi_status node_api_set_prototype(napi_env env,
                                   napi_value object,
                                   napi_value value);
```

- `[in] env`: The environment that the Node-API call is invoked under.
- `[in] object`: The object on which to set the prototype.
- `[in] value`: The prototype value.

Returns `napi_ok` if the API succeeded.

This API sets the prototype of the `Object` passed in.

## Working with JavaScript functions

Node-API provides a set of APIs that allow JavaScript code to
call back into native code. Node-APIs that support calling back
into native code take in a callback functions represented by
the `napi_callback` type. When the JavaScript VM calls back to
native code, the `napi_callback` function provided is invoked. The APIs
documented in this section allow the callback function to do the
following:

- Get information about the context in which the callback was invoked.
- Get the arguments passed into the callback.
- Return a `napi_value` back from the callback.

Additionally, Node-API provides a set of functions which allow calling
JavaScript functions from native code. One can either call a function
like a regular JavaScript function call, or as a constructor
function.

Any non-`NULL` data which is passed to this API via the `data` field of the
`napi_property_descriptor` items can be associated with `object` and freed
whenever `object` is garbage-collected by passing both `object` and the data to
[`napi_add_finalizer`](09-n-api-part4.md#napi_add_finalizer).

### `napi_call_function`

Added in: v8.0.0**N-API Version:** 1

```c
NAPI_EXTERN napi_status napi_call_function(napi_env env,
                                           napi_value recv,
                                           napi_value func,
                                           size_t argc,
                                           const napi_value* argv,
                                           napi_value* result);
```

- `[in] env`: The environment that the API is invoked under.
- `[in] recv`: The `this` value passed to the called function.
- `[in] func`: `napi_value` representing the JavaScript function to be invoked.
- `[in] argc`: The count of elements in the `argv` array.
- `[in] argv`: Array of `napi_values` representing JavaScript values passed in
  as arguments to the function.
- `[out] result`: `napi_value` representing the JavaScript object returned.

Returns `napi_ok` if the API succeeded.

This method allows a JavaScript function object to be called from a native
add-on. This is the primary mechanism of calling back *from* the add-on's
native code *into* JavaScript. For the special case of calling into JavaScript
after an async operation, see [`napi_make_callback`](09-n-api-part4.md#napi_make_callback).

A sample use case might look as follows. Consider the following JavaScript
snippet:

```js
function AddTwo(num) {
  return num + 2;
}
global.AddTwo = AddTwo;
```

Then, the above function can be invoked from a native add-on using the
following code:

```c
// Get the function named "AddTwo" on the global object
napi_value global, add_two, arg;
napi_status status = napi_get_global(env, &global);
if (status != napi_ok) return;

status = napi_get_named_property(env, global, "AddTwo", &add_two);
if (status != napi_ok) return;

// const arg = 1337
status = napi_create_int32(env, 1337, &arg);
if (status != napi_ok) return;

napi_value* argv = &arg;
size_t argc = 1;

// AddTwo(arg);
napi_value return_val;
status = napi_call_function(env, global, add_two, argc, argv, &return_val);
if (status != napi_ok) return;

// Convert the result back to a native type
int32_t result;
status = napi_get_value_int32(env, return_val, &result);
if (status != napi_ok) return;
```

### `napi_create_function`

Added in: v8.0.0**N-API Version:** 1

```c
napi_status napi_create_function(napi_env env,
                                 const char* utf8name,
                                 size_t length,
                                 napi_callback cb,
                                 void* data,
                                 napi_value* result);
```

- `[in] env`: The environment that the API is invoked under.
- `[in] utf8Name`: Optional name of the function encoded as UTF8. This is
  visible within JavaScript as the new function object's `name` property.
- `[in] length`: The length of the `utf8name` in bytes, or `NAPI_AUTO_LENGTH` if
  it is null-terminated.
- `[in] cb`: The native function which should be called when this function
  object is invoked. [`napi_callback`](09-n-api-part1.md#napi_callback) provides more details.
- `[in] data`: User-provided data context. This will be passed back into the
  function when invoked later.
- `[out] result`: `napi_value` representing the JavaScript function object for
  the newly created function.

Returns `napi_ok` if the API succeeded.

This API allows an add-on author to create a function object in native code.
This is the primary mechanism to allow calling *into* the add-on's native code
*from* JavaScript.

The newly created function is not automatically visible from script after this
call. Instead, a property must be explicitly set on any object that is visible
to JavaScript, in order for the function to be accessible from script.

In order to expose a function as part of the
add-on's module exports, set the newly created function on the exports
object. A sample module might look as follows:

```c
napi_value SayHello(napi_env env, napi_callback_info info) {
  printf("Hello\n");
  return NULL;
}

napi_value Init(napi_env env, napi_value exports) {
  napi_status status;

  napi_value fn;
  status = napi_create_function(env, NULL, 0, SayHello, NULL, &fn);
  if (status != napi_ok) return NULL;

  status = napi_set_named_property(env, exports, "sayHello", fn);
  if (status != napi_ok) return NULL;

  return exports;
}

NAPI_MODULE(NODE_GYP_MODULE_NAME, Init)
```

Given the above code, the add-on can be used from JavaScript as follows:

```js
const myaddon = require('./addon');
myaddon.sayHello();
```

The string passed to `require()` is the name of the target in `binding.gyp`
responsible for creating the `.node` file.

Any non-`NULL` data which is passed to this API via the `data` parameter can
be associated with the resulting JavaScript function (which is returned in the
`result` parameter) and freed whenever the function is garbage-collected by
passing both the JavaScript function and the data to [`napi_add_finalizer`](09-n-api-part4.md#napi_add_finalizer).

JavaScript `Function`s are described in [Section Function objects](https://tc39.es/ecma262/#sec-function-objects) of the ECMAScript
Language Specification.

### `napi_get_cb_info`

Added in: v8.0.0**N-API Version:** 1

```c
napi_status napi_get_cb_info(napi_env env,
                             napi_callback_info cbinfo,
                             size_t* argc,
                             napi_value* argv,
                             napi_value* thisArg,
                             void** data)
```

- `[in] env`: The environment that the API is invoked under.
- `[in] cbinfo`: The callback info passed into the callback function.
- `[in-out] argc`: Specifies the length of the provided `argv` array and
  receives the actual count of arguments. `argc` can
  optionally be ignored by passing `NULL`.
- `[out] argv`: C array of `napi_value`s to which the arguments will be
  copied. If there are more arguments than the provided count, only the
  requested number of arguments are copied. If there are fewer arguments
  provided than claimed, the rest of `argv` is filled with `napi_value` values
  that represent `undefined`. `argv` can optionally be ignored by
  passing `NULL`.
- `[out] thisArg`: Receives the JavaScript `this` argument for the call.
  `thisArg` can optionally be ignored by passing `NULL`.
- `[out] data`: Receives the data pointer for the callback. `data` can
  optionally be ignored by passing `NULL`.

Returns `napi_ok` if the API succeeded.

This method is used within a callback function to retrieve details about the
call like the arguments and the `this` pointer from a given callback info.

### `napi_get_new_target`

Added in: v8.6.0**N-API Version:** 1

```c
napi_status napi_get_new_target(napi_env env,
                                napi_callback_info cbinfo,
                                napi_value* result)
```

- `[in] env`: The environment that the API is invoked under.
- `[in] cbinfo`: The callback info passed into the callback function.
- `[out] result`: The `new.target` of the constructor call.

Returns `napi_ok` if the API succeeded.

This API returns the `new.target` of the constructor call. If the current
callback is not a constructor call, the result is `NULL`.

### `napi_new_instance`

Added in: v8.0.0**N-API Version:** 1

```c
napi_status napi_new_instance(napi_env env,
                              napi_value cons,
                              size_t argc,
                              napi_value* argv,
                              napi_value* result)
```

- `[in] env`: The environment that the API is invoked under.
- `[in] cons`: `napi_value` representing the JavaScript function to be invoked
  as a constructor.
- `[in] argc`: The count of elements in the `argv` array.
- `[in] argv`: Array of JavaScript values as `napi_value` representing the
  arguments to the constructor. If `argc` is zero this parameter may be
  omitted by passing in `NULL`.
- `[out] result`: `napi_value` representing the JavaScript object returned,
  which in this case is the constructed object.

This method is used to instantiate a new JavaScript value using a given
`napi_value` that represents the constructor for the object. For example,
consider the following snippet:

```js
function MyObject(param) {
  this.param = param;
}

const arg = 'hello';
const value = new MyObject(arg);
```

The following can be approximated in Node-API using the following snippet:

```c
// Get the constructor function MyObject
napi_value global, constructor, arg, value;
napi_status status = napi_get_global(env, &global);
if (status != napi_ok) return;

status = napi_get_named_property(env, global, "MyObject", &constructor);
if (status != napi_ok) return;

// const arg = "hello"
status = napi_create_string_utf8(env, "hello", NAPI_AUTO_LENGTH, &arg);
if (status != napi_ok) return;

napi_value* argv = &arg;
size_t argc = 1;

// const value = new MyObject(arg)
status = napi_new_instance(env, constructor, argc, argv, &value);
```

Returns `napi_ok` if the API succeeded.

[Contents](../README.md) | [Previous: C/C++ addons with Node-API, part 2 of 4](09-n-api-part2.md) | [Next: C/C++ addons with Node-API, part 4 of 4](09-n-api-part4.md)
