[Contents](../README.md) | [Previous: C/C++ addons with Node-API, part 1 of 4](09-n-api-part1.md) | [Next: C/C++ addons with Node-API, part 3 of 4](09-n-api-part3.md)

Source: [https://nodejs.org/docs/latest/api/n-api.html](https://nodejs.org/docs/latest/api/n-api.html)

Part 2 of 4 of this section.

# C/C++ addons with Node-API (part 2 of 4)

Continues: Object lifetime management.

### References to values with a lifespan longer than that of the native method

In some cases, an addon will need to be able to create and reference values
with a lifespan longer than that of a single native method invocation. For
example, to create a constructor and later use that constructor
in a request to create instances, it must be possible to reference
the constructor object across many different instance creation requests. This
would not be possible with a normal handle returned as a `napi_value` as
described in the earlier section. The lifespan of a normal handle is
managed by scopes and all scopes must be closed before the end of a native
method.

Node-API provides methods for creating persistent references to values.
Currently Node-API only allows references to be created for a
limited set of value types, including object, external, function, and symbol.

Each reference has an associated count with a value of 0 or higher,
which determines whether the reference will keep the corresponding value alive.
References with a count of 0 do not prevent values from being collected.
Values of object (object, function, external) and symbol types are becoming
'weak' references and can still be accessed while they are not collected.
Any count greater than 0 will prevent the values from being collected.

Symbol values have different flavors. The true weak reference behavior is
only supported by local symbols created with the `napi_create_symbol` function
or the JavaScript `Symbol()` constructor calls. Globally registered symbols
created with the `node_api_symbol_for` function or JavaScript `Symbol.for()`
function calls remain always strong references because the garbage collector
does not collect them. The same is true for well-known symbols such as
`Symbol.iterator`. They are also never collected by the garbage collector.

References can be created with an initial reference count. The count can
then be modified through [`napi_reference_ref`](#napi_reference_ref) and
[`napi_reference_unref`](#napi_reference_unref). If an object is collected while the count
for a reference is 0, all subsequent calls to
get the object associated with the reference [`napi_get_reference_value`](#napi_get_reference_value)
will return `NULL` for the returned `napi_value`. An attempt to call
[`napi_reference_ref`](#napi_reference_ref) for a reference whose object has been collected
results in an error.

References must be deleted once they are no longer required by the addon. When
a reference is deleted, it will no longer prevent the corresponding object from
being collected. Failure to delete a persistent reference results in
a 'memory leak' with both the native memory for the persistent reference and
the corresponding object on the heap being retained forever.

There can be multiple persistent references created which refer to the same
object, each of which will either keep the object live or not based on its
individual count. Multiple persistent references to the same object
can result in unexpectedly keeping alive native memory. The native structures
for a persistent reference must be kept alive until finalizers for the
referenced object are executed. If a new persistent reference is created
for the same object, the finalizers for that object will not be
run and the native memory pointed by the earlier persistent reference
will not be freed. This can be avoided by calling
`napi_delete_reference` in addition to `napi_reference_unref` when possible.

**Change History:**

- Version 10 (`NAPI_VERSION` is defined as `10` or higher):

  References can be created for all value types. The new supported value
  types do not support weak reference semantic and the values of these types
  are released when the reference count becomes 0 and cannot be accessed from
  the reference anymore.

#### `napi_create_reference`

Added in: v8.0.0**N-API Version:** 1

```c
NAPI_EXTERN napi_status napi_create_reference(napi_env env,
                                              napi_value value,
                                              uint32_t initial_refcount,
                                              napi_ref* result);
```

- `[in] env`: The environment that the API is invoked under.
- `[in] value`: The `napi_value` for which a reference is being created.
- `[in] initial_refcount`: Initial reference count for the new reference.
- `[out] result`: `napi_ref` pointing to the new reference.

Returns `napi_ok` if the API succeeded.

This API creates a new reference with the specified reference count
to the value passed in.

#### `napi_delete_reference`

Added in: v8.0.0**N-API Version:** 1

```c
NAPI_EXTERN napi_status napi_delete_reference(napi_env env, napi_ref ref);
```

- `[in] env`: The environment that the API is invoked under.
- `[in] ref`: `napi_ref` to be deleted.

Returns `napi_ok` if the API succeeded.

This API deletes the reference passed in.

This API can be called even if there is a pending JavaScript exception.

#### `napi_reference_ref`

Added in: v8.0.0**N-API Version:** 1

```c
NAPI_EXTERN napi_status napi_reference_ref(napi_env env,
                                           napi_ref ref,
                                           uint32_t* result);
```

- `[in] env`: The environment that the API is invoked under.
- `[in] ref`: `napi_ref` for which the reference count will be incremented.
- `[out] result`: The new reference count.

Returns `napi_ok` if the API succeeded.

This API increments the reference count for the reference
passed in and returns the resulting reference count.

#### `napi_reference_unref`

Added in: v8.0.0**N-API Version:** 1

```c
NAPI_EXTERN napi_status napi_reference_unref(napi_env env,
                                             napi_ref ref,
                                             uint32_t* result);
```

- `[in] env`: The environment that the API is invoked under.
- `[in] ref`: `napi_ref` for which the reference count will be decremented.
- `[out] result`: The new reference count.

Returns `napi_ok` if the API succeeded.

This API decrements the reference count for the reference
passed in and returns the resulting reference count.

#### `napi_get_reference_value`

Added in: v8.0.0**N-API Version:** 1

```c
NAPI_EXTERN napi_status napi_get_reference_value(napi_env env,
                                                 napi_ref ref,
                                                 napi_value* result);
```

- `[in] env`: The environment that the API is invoked under.
- `[in] ref`: The `napi_ref` for which the corresponding value is
  being requested.
- `[out] result`: The `napi_value` referenced by the `napi_ref`.

Returns `napi_ok` if the API succeeded.

If still valid, this API returns the `napi_value` representing the
JavaScript value associated with the `napi_ref`. Otherwise, result
will be `NULL`.

### Cleanup on exit of the current Node.js environment

While a Node.js process typically releases all its resources when exiting,
embedders of Node.js, or future Worker support, may require addons to register
clean-up hooks that will be run once the current Node.js environment exits.

Node-API provides functions for registering and un-registering such callbacks.
When those callbacks are run, all resources that are being held by the addon
should be freed up.

#### `napi_add_env_cleanup_hook`

Added in: v10.2.0**N-API Version:** 3

```c
NODE_EXTERN napi_status napi_add_env_cleanup_hook(node_api_basic_env env,
                                                  napi_cleanup_hook fun,
                                                  void* arg);
```

Registers `fun` as a function to be run with the `arg` parameter once the
current Node.js environment exits.

A function can safely be specified multiple times with different
`arg` values. In that case, it will be called multiple times as well.
Providing the same `fun` and `arg` values multiple times is not allowed
and will lead the process to abort.

The hooks will be called in reverse order, i.e. the most recently added one
will be called first.

Removing this hook can be done by using [`napi_remove_env_cleanup_hook`](#napi_remove_env_cleanup_hook).
Typically, that happens when the resource for which this hook was added
is being torn down anyway.

For asynchronous cleanup, [`napi_add_async_cleanup_hook`](#napi_add_async_cleanup_hook) is available.

#### `napi_remove_env_cleanup_hook`

Added in: v10.2.0**N-API Version:** 3

```c
NAPI_EXTERN napi_status napi_remove_env_cleanup_hook(node_api_basic_env env,
                                                     void (*fun)(void* arg),
                                                     void* arg);
```

Unregisters `fun` as a function to be run with the `arg` parameter once the
current Node.js environment exits. Both the argument and the function value
need to be exact matches.

The function must have originally been registered
with `napi_add_env_cleanup_hook`, otherwise the process will abort.

#### `napi_add_async_cleanup_hook`

Added in: v14.8.0, v12.19.0**N-API Version:** 8

**History**

| Version | Changes |
| --- | --- |
| v14.10.0, v12.19.0 | Changed signature of the `hook` callback. |

```c
NAPI_EXTERN napi_status napi_add_async_cleanup_hook(
    node_api_basic_env env,
    napi_async_cleanup_hook hook,
    void* arg,
    napi_async_cleanup_hook_handle* remove_handle);
```

- `[in] env`: The environment that the API is invoked under.
- `[in] hook`: The function pointer to call at environment teardown.
- `[in] arg`: The pointer to pass to `hook` when it gets called.
- `[out] remove_handle`: Optional handle that refers to the asynchronous cleanup
  hook.

Registers `hook`, which is a function of type [`napi_async_cleanup_hook`](09-n-api-part1.md#napi_async_cleanup_hook), as
a function to be run with the `remove_handle` and `arg` parameters once the
current Node.js environment exits.

Unlike [`napi_add_env_cleanup_hook`](#napi_add_env_cleanup_hook), the hook is allowed to be asynchronous.

Otherwise, behavior generally matches that of [`napi_add_env_cleanup_hook`](#napi_add_env_cleanup_hook).

If `remove_handle` is not `NULL`, an opaque value will be stored in it
that must later be passed to [`napi_remove_async_cleanup_hook`](#napi_remove_async_cleanup_hook),
regardless of whether the hook has already been invoked.
Typically, that happens when the resource for which this hook was added
is being torn down anyway.

#### `napi_remove_async_cleanup_hook`

Added in: v14.8.0, v12.19.0

**History**

| Version | Changes |
| --- | --- |
| v14.10.0, v12.19.0 | Removed `env` parameter. |

```c
NAPI_EXTERN napi_status napi_remove_async_cleanup_hook(
    napi_async_cleanup_hook_handle remove_handle);
```

- `[in] remove_handle`: The handle to an asynchronous cleanup hook that was
  created with [`napi_add_async_cleanup_hook`](#napi_add_async_cleanup_hook).

Unregisters the cleanup hook corresponding to `remove_handle`. This will prevent
the hook from being executed, unless it has already started executing.
This must be called on any `napi_async_cleanup_hook_handle` value obtained
from [`napi_add_async_cleanup_hook`](#napi_add_async_cleanup_hook).

### Finalization on the exit of the Node.js environment

The Node.js environment may be torn down at an arbitrary time as soon as
possible with JavaScript execution disallowed, like on the request of
[`worker.terminate()`](67-worker-threads.md#workerterminate). When the environment is being torn down, the
registered `napi_finalize` callbacks of JavaScript objects, thread-safe
functions and environment instance data are invoked immediately and
independently.

The invocation of `napi_finalize` callbacks is scheduled after the manually
registered cleanup hooks. In order to ensure a proper order of addon
finalization during environment shutdown to avoid use-after-free in the
`napi_finalize` callback, addons should register a cleanup hook with
`napi_add_env_cleanup_hook` and `napi_add_async_cleanup_hook` to manually
release the allocated resource in a proper order.

## Module registration

Node-API modules are registered in a manner similar to other modules
except that instead of using the `NODE_MODULE` macro the following
is used:

```c
NAPI_MODULE(NODE_GYP_MODULE_NAME, Init)
```

The next difference is the signature for the `Init` method. For a Node-API
module it is as follows:

```c
napi_value Init(napi_env env, napi_value exports);
```

The return value from `Init` is treated as the `exports` object for the module.
The `Init` method is passed an empty object via the `exports` parameter as a
convenience. If `Init` returns `NULL`, the parameter passed as `exports` is
exported by the module. Node-API modules cannot modify the `module` object but
can specify anything as the `exports` property of the module.

To add the method `hello` as a function so that it can be called as a method
provided by the addon:

```c
napi_value Init(napi_env env, napi_value exports) {
  napi_status status;
  napi_property_descriptor desc = {
    "hello",
    NULL,
    Method,
    NULL,
    NULL,
    NULL,
    napi_writable | napi_enumerable | napi_configurable,
    NULL
  };
  status = napi_define_properties(env, exports, 1, &desc);
  if (status != napi_ok) return NULL;
  return exports;
}
```

To set a function to be returned by the `require()` for the addon:

```c
napi_value Init(napi_env env, napi_value exports) {
  napi_value method;
  napi_status status;
  status = napi_create_function(env, "exports", NAPI_AUTO_LENGTH, Method, NULL, &method);
  if (status != napi_ok) return NULL;
  return method;
}
```

To define a class so that new instances can be created (often used with
[Object wrap](09-n-api-part4.md#object-wrap)):

```c
// NOTE: partial example, not all referenced code is included
napi_value Init(napi_env env, napi_value exports) {
  napi_status status;
  napi_property_descriptor properties[] = {
    { "value", NULL, NULL, GetValue, SetValue, NULL, napi_writable | napi_configurable, NULL },
    DECLARE_NAPI_METHOD("plusOne", PlusOne),
    DECLARE_NAPI_METHOD("multiply", Multiply),
  };

  napi_value cons;
  status =
      napi_define_class(env, "MyObject", New, NULL, 3, properties, &cons);
  if (status != napi_ok) return NULL;

  status = napi_create_reference(env, cons, 1, &constructor);
  if (status != napi_ok) return NULL;

  status = napi_set_named_property(env, exports, "MyObject", cons);
  if (status != napi_ok) return NULL;

  return exports;
}
```

You can also use the `NAPI_MODULE_INIT` macro, which acts as a shorthand
for `NAPI_MODULE` and defining an `Init` function:

```c
NAPI_MODULE_INIT(/* napi_env env, napi_value exports */) {
  napi_value answer;
  napi_status result;

  status = napi_create_int64(env, 42, &answer);
  if (status != napi_ok) return NULL;

  status = napi_set_named_property(env, exports, "answer", answer);
  if (status != napi_ok) return NULL;

  return exports;
}
```

The parameters `env` and `exports` are provided to the body of the
`NAPI_MODULE_INIT` macro.

All Node-API addons are context-aware, meaning they may be loaded multiple
times. There are a few design considerations when declaring such a module.
The documentation on [context-aware addons](08-addons.md#context-aware-addons) provides more details.

The variables `env` and `exports` will be available inside the function body
following the macro invocation.

For more details on setting properties on objects, see the section on
[Working with JavaScript properties](09-n-api-part3.md#working-with-javascript-properties).

For more details on building addon modules in general, refer to the existing
API.

## Working with JavaScript values

Node-API exposes a set of APIs to create all types of JavaScript values.
Some of these types are documented under [Section language types](https://tc39.es/ecma262/#sec-ecmascript-data-types-and-values)
of the [ECMAScript Language Specification](https://tc39.es/ecma262/).

Fundamentally, these APIs are used to do one of the following:

1. Create a new JavaScript object
2. Convert from a primitive C type to a Node-API value
3. Convert from Node-API value to a primitive C type
4. Get global instances including `undefined` and `null`

Node-API values are represented by the type `napi_value`.
Any Node-API call that requires a JavaScript value takes in a `napi_value`.
In some cases, the API does check the type of the `napi_value` up-front.
However, for better performance, it's better for the caller to make sure that
the `napi_value` in question is of the JavaScript type expected by the API.

### Enum types

#### `napi_key_collection_mode`

Added in: v13.7.0, v12.17.0, v10.20.0**N-API Version:** 6

```c
typedef enum {
  napi_key_include_prototypes,
  napi_key_own_only
} napi_key_collection_mode;
```

Describes the `Keys/Properties` filter enums:

`napi_key_collection_mode` limits the range of collected properties.

`napi_key_own_only` limits the collected properties to the given
object only. `napi_key_include_prototypes` will include all keys
of the objects's prototype chain as well.

#### `napi_key_filter`

Added in: v13.7.0, v12.17.0, v10.20.0**N-API Version:** 6

```c
typedef enum {
  napi_key_all_properties = 0,
  napi_key_writable = 1,
  napi_key_enumerable = 1 << 1,
  napi_key_configurable = 1 << 2,
  napi_key_skip_strings = 1 << 3,
  napi_key_skip_symbols = 1 << 4
} napi_key_filter;
```

Property filter bit flag. This works with bit operators to build a composite filter.

#### `napi_key_conversion`

Added in: v13.7.0, v12.17.0, v10.20.0**N-API Version:** 6

```c
typedef enum {
  napi_key_keep_numbers,
  napi_key_numbers_to_strings
} napi_key_conversion;
```

`napi_key_numbers_to_strings` will convert integer indexes to
strings. `napi_key_keep_numbers` will return numbers for integer
indexes.

#### `napi_valuetype`

```c
typedef enum {
  // ES6 types (corresponds to typeof)
  napi_undefined,
  napi_null,
  napi_boolean,
  napi_number,
  napi_string,
  napi_symbol,
  napi_object,
  napi_function,
  napi_external,
  napi_bigint,
} napi_valuetype;
```

Describes the type of a `napi_value`. This generally corresponds to the types
described in [Section language types](https://tc39.es/ecma262/#sec-ecmascript-data-types-and-values) of the ECMAScript Language Specification.
In addition to types in that section, `napi_valuetype` can also represent
`Function`s and `Object`s with external data.

A JavaScript value of type `napi_external` appears in JavaScript as a plain
object such that no properties can be set on it, and no prototype.

#### `napi_typedarray_type`

**History**

| Version | Changes |
| --- | --- |
| v25.5.0, v24.13.1 | Added `napi_float16_array` for Float16Array support. |

```c
typedef enum {
  napi_int8_array,
  napi_uint8_array,
  napi_uint8_clamped_array,
  napi_int16_array,
  napi_uint16_array,
  napi_int32_array,
  napi_uint32_array,
  napi_float32_array,
  napi_float64_array,
  napi_bigint64_array,
  napi_biguint64_array,
  napi_float16_array,
} napi_typedarray_type;
```

This represents the underlying binary scalar datatype of the `TypedArray`.
Elements of this enum correspond to
[Section TypedArray objects](https://tc39.es/ecma262/#sec-typedarray-objects) of the [ECMAScript Language Specification](https://tc39.es/ecma262/).

### Object creation functions

#### `napi_create_array`

Added in: v8.0.0**N-API Version:** 1

```c
napi_status napi_create_array(napi_env env, napi_value* result)
```

- `[in] env`: The environment that the Node-API call is invoked under.
- `[out] result`: A `napi_value` representing a JavaScript `Array`.

Returns `napi_ok` if the API succeeded.

This API returns a Node-API value corresponding to a JavaScript `Array` type.
JavaScript arrays are described in
[Section Array objects](https://tc39.es/ecma262/#sec-array-objects) of the ECMAScript Language Specification.

#### `napi_create_array_with_length`

Added in: v8.0.0**N-API Version:** 1

```c
napi_status napi_create_array_with_length(napi_env env,
                                          size_t length,
                                          napi_value* result)
```

- `[in] env`: The environment that the API is invoked under.
- `[in] length`: The initial length of the `Array`.
- `[out] result`: A `napi_value` representing a JavaScript `Array`.

Returns `napi_ok` if the API succeeded.

This API returns a Node-API value corresponding to a JavaScript `Array` type.
The `Array`'s length property is set to the passed-in length parameter.
However, the underlying buffer is not guaranteed to be pre-allocated by the VM
when the array is created. That behavior is left to the underlying VM
implementation. If the buffer must be a contiguous block of memory that can be
directly read and/or written via C, consider using
[`napi_create_external_arraybuffer`](#napi_create_external_arraybuffer).

JavaScript arrays are described in
[Section Array objects](https://tc39.es/ecma262/#sec-array-objects) of the ECMAScript Language Specification.

#### `napi_create_arraybuffer`

Added in: v8.0.0**N-API Version:** 1

```c
napi_status napi_create_arraybuffer(napi_env env,
                                    size_t byte_length,
                                    void** data,
                                    napi_value* result)
```

- `[in] env`: The environment that the API is invoked under.
- `[in] length`: The length in bytes of the array buffer to create.
- `[out] data`: Pointer to the underlying byte buffer of the `ArrayBuffer`.
  `data` can optionally be ignored by passing `NULL`.
- `[out] result`: A `napi_value` representing a JavaScript `ArrayBuffer`.

Returns `napi_ok` if the API succeeded.

This API returns a Node-API value corresponding to a JavaScript `ArrayBuffer`.
`ArrayBuffer`s are used to represent fixed-length binary data buffers. They are
normally used as a backing-buffer for `TypedArray` objects.
The `ArrayBuffer` allocated will have an underlying byte buffer whose size is
determined by the `length` parameter that's passed in.
The underlying buffer is optionally returned back to the caller in case the
caller wants to directly manipulate the buffer. This buffer can only be
written to directly from native code. To write to this buffer from JavaScript,
a typed array or `DataView` object would need to be created.

JavaScript `ArrayBuffer` objects are described in
[Section ArrayBuffer objects](https://tc39.es/ecma262/#sec-arraybuffer-objects) of the ECMAScript Language Specification.

#### `napi_create_buffer`

Added in: v8.0.0**N-API Version:** 1

```c
napi_status napi_create_buffer(napi_env env,
                               size_t size,
                               void** data,
                               napi_value* result)
```

- `[in] env`: The environment that the API is invoked under.
- `[in] size`: Size in bytes of the underlying buffer.
- `[out] data`: Raw pointer to the underlying buffer.
  `data` can optionally be ignored by passing `NULL`.
- `[out] result`: A `napi_value` representing a `node::Buffer`.

Returns `napi_ok` if the API succeeded.

This API allocates a `node::Buffer` object. While this is still a
fully-supported data structure, in most cases using a `TypedArray` will suffice.

#### `napi_create_buffer_copy`

Added in: v8.0.0**N-API Version:** 1

```c
napi_status napi_create_buffer_copy(napi_env env,
                                    size_t length,
                                    const void* data,
                                    void** result_data,
                                    napi_value* result)
```

- `[in] env`: The environment that the API is invoked under.
- `[in] size`: Size in bytes of the input buffer (should be the same as the size
  of the new buffer).
- `[in] data`: Raw pointer to the underlying buffer to copy from.
- `[out] result_data`: Pointer to the new `Buffer`'s underlying data buffer.
  `result_data` can optionally be ignored by passing `NULL`.
- `[out] result`: A `napi_value` representing a `node::Buffer`.

Returns `napi_ok` if the API succeeded.

This API allocates a `node::Buffer` object and initializes it with data copied
from the passed-in buffer. While this is still a fully-supported data
structure, in most cases using a `TypedArray` will suffice.

#### `napi_create_date`

Added in: v11.11.0, v10.17.0**N-API Version:** 5

```c
napi_status napi_create_date(napi_env env,
                             double time,
                             napi_value* result);
```

- `[in] env`: The environment that the API is invoked under.
- `[in] time`: ECMAScript time value in milliseconds since 01 January, 1970 UTC.
- `[out] result`: A `napi_value` representing a JavaScript `Date`.

Returns `napi_ok` if the API succeeded.

This API does not observe leap seconds; they are ignored, as
ECMAScript aligns with POSIX time specification.

This API allocates a JavaScript `Date` object.

JavaScript `Date` objects are described in
[Section Date objects](https://tc39.es/ecma262/#sec-date-objects) of the ECMAScript Language Specification.

#### `napi_create_external`

Added in: v8.0.0**N-API Version:** 1

```c
napi_status napi_create_external(napi_env env,
                                 void* data,
                                 napi_finalize finalize_cb,
                                 void* finalize_hint,
                                 napi_value* result)
```

- `[in] env`: The environment that the API is invoked under.
- `[in] data`: Raw pointer to the external data.
- `[in] finalize_cb`: Optional callback to call when the external value is being
  collected. [`napi_finalize`](09-n-api-part1.md#napi_finalize) provides more details.
- `[in] finalize_hint`: Optional hint to pass to the finalize callback during
  collection.
- `[out] result`: A `napi_value` representing an external value.

Returns `napi_ok` if the API succeeded.

This API allocates a JavaScript value with external data attached to it. This
is used to pass external data through JavaScript code, so it can be retrieved
later by native code using [`napi_get_value_external`](09-n-api-part3.md#napi_get_value_external).

The API adds a `napi_finalize` callback which will be called when the JavaScript
object just created has been garbage collected.

The created value is not an object, and therefore does not support additional
properties. It is considered a distinct value type: calling `napi_typeof()` with
an external value yields `napi_external`.

#### `napi_create_external_arraybuffer`

Added in: v8.0.0**N-API Version:** 1

```c
napi_status
napi_create_external_arraybuffer(napi_env env,
                                 void* external_data,
                                 size_t byte_length,
                                 napi_finalize finalize_cb,
                                 void* finalize_hint,
                                 napi_value* result)
```

- `[in] env`: The environment that the API is invoked under.
- `[in] external_data`: Pointer to the underlying byte buffer of the
  `ArrayBuffer`.
- `[in] byte_length`: The length in bytes of the underlying buffer.
- `[in] finalize_cb`: Optional callback to call when the `ArrayBuffer` is being
  collected. [`napi_finalize`](09-n-api-part1.md#napi_finalize) provides more details.
- `[in] finalize_hint`: Optional hint to pass to the finalize callback during
  collection.
- `[out] result`: A `napi_value` representing a JavaScript `ArrayBuffer`.

Returns `napi_ok` if the API succeeded.

**Some runtimes other than Node.js have dropped support for external buffers**.
On runtimes other than Node.js this method may return
`napi_no_external_buffers_allowed` to indicate that external
buffers are not supported. One such runtime is Electron as
described in this issue
[electron/issues/35801](https://github.com/electron/electron/issues/35801).

In order to maintain broadest compatibility with all runtimes
you may define `NODE_API_NO_EXTERNAL_BUFFERS_ALLOWED` in your addon before
includes for the node-api headers. Doing so will hide the 2 functions
that create external buffers. This will ensure a compilation error
occurs if you accidentally use one of these methods.

This API returns a Node-API value corresponding to a JavaScript `ArrayBuffer`.
The underlying byte buffer of the `ArrayBuffer` is externally allocated and
managed. The caller must ensure that the byte buffer remains valid until the
finalize callback is called.

The API adds a `napi_finalize` callback which will be called when the JavaScript
object just created has been garbage collected.

JavaScript `ArrayBuffer`s are described in
[Section ArrayBuffer objects](https://tc39.es/ecma262/#sec-arraybuffer-objects) of the ECMAScript Language Specification.

#### `node_api_create_external_sharedarraybuffer`

Added in: v26.1.0

```c
napi_status
node_api_create_external_sharedarraybuffer(napi_env env,
                                           void* external_data,
                                           size_t byte_length,
                                           node_api_noenv_finalize finalize_cb,
                                           void* finalize_hint,
                                           napi_value* result)
```

- `[in] env`: The environment that the API is invoked under.
- `[in] external_data`: Pointer to the underlying byte buffer of the
  `SharedArrayBuffer`.
- `[in] byte_length`: The length in bytes of the underlying buffer.
- `[in] finalize_cb`: Optional callback to call when the `SharedArrayBuffer` is
  being collected. Called on an arbitrary thread. Because a `SharedArrayBuffer`
  can outlive the environment it's created in, the callback does not receive a
  reference to `env`.
- `[in] finalize_hint`: Optional hint to pass to the finalize callback during
  collection.
- `[out] result`: A `napi_value` representing a JavaScript `SharedArrayBuffer`.

Returns `napi_ok` if the API succeeded.

Create a `SharedArrayBuffer` with externally managed memory.

See the entry on [`napi_create_external_arraybuffer`](#napi_create_external_arraybuffer) for runtime
compatibility.

#### `napi_create_external_buffer`

Added in: v8.0.0**N-API Version:** 1

```c
napi_status napi_create_external_buffer(napi_env env,
                                        size_t length,
                                        void* data,
                                        napi_finalize finalize_cb,
                                        void* finalize_hint,
                                        napi_value* result)
```

- `[in] env`: The environment that the API is invoked under.
- `[in] length`: Size in bytes of the input buffer (should be the same as the
  size of the new buffer).
- `[in] data`: Raw pointer to the underlying buffer to expose to JavaScript.
- `[in] finalize_cb`: Optional callback to call when the `ArrayBuffer` is being
  collected. [`napi_finalize`](09-n-api-part1.md#napi_finalize) provides more details.
- `[in] finalize_hint`: Optional hint to pass to the finalize callback during
  collection.
- `[out] result`: A `napi_value` representing a `node::Buffer`.

Returns `napi_ok` if the API succeeded.

**Some runtimes other than Node.js have dropped support for external buffers**.
On runtimes other than Node.js this method may return
`napi_no_external_buffers_allowed` to indicate that external
buffers are not supported. One such runtime is Electron as
described in this issue
[electron/issues/35801](https://github.com/electron/electron/issues/35801).

In order to maintain broadest compatibility with all runtimes
you may define `NODE_API_NO_EXTERNAL_BUFFERS_ALLOWED` in your addon before
includes for the node-api headers. Doing so will hide the 2 functions
that create external buffers. This will ensure a compilation error
occurs if you accidentally use one of these methods.

This API allocates a `node::Buffer` object and initializes it with data
backed by the passed in buffer. While this is still a fully-supported data
structure, in most cases using a `TypedArray` will suffice.

The API adds a `napi_finalize` callback which will be called when the JavaScript
object just created has been garbage collected.

For Node.js >=4 `Buffers` are `Uint8Array`s.

#### `napi_create_object`

Added in: v8.0.0**N-API Version:** 1

```c
napi_status napi_create_object(napi_env env, napi_value* result)
```

- `[in] env`: The environment that the API is invoked under.
- `[out] result`: A `napi_value` representing a JavaScript `Object`.

Returns `napi_ok` if the API succeeded.

This API allocates a default JavaScript `Object`.
It is the equivalent of doing `new Object()` in JavaScript.

The JavaScript `Object` type is described in [Section object type](https://tc39.es/ecma262/#sec-object-type) of the
ECMAScript Language Specification.

#### `node_api_create_object_with_properties`

Added in: v25.2.0, v24.12.0

Stability: 1 - Experimental

```cpp
napi_status node_api_create_object_with_properties(napi_env env,
                                                   napi_value prototype_or_null,
                                                   const napi_value* property_names,
                                                   const napi_value* property_values,
                                                   size_t property_count,
                                                   napi_value* result)
```

- `[in] env`: The environment that the API is invoked under.
- `[in] prototype_or_null`: The prototype object for the new object. Can be a
  `napi_value` representing a JavaScript object to use as the prototype, a
  `napi_value` representing JavaScript `null`, or a `nullptr` that will be converted to `null`.
- `[in] property_names`: Array of `napi_value` representing the property names.
- `[in] property_values`: Array of `napi_value` representing the property values.
- `[in] property_count`: Number of properties in the arrays.
- `[out] result`: A `napi_value` representing a JavaScript `Object`.

Returns `napi_ok` if the API succeeded.

This API creates a JavaScript `Object` with the specified prototype and
properties. This is more efficient than calling `napi_create_object` followed
by multiple `napi_set_property` calls, as it can create the object with all
properties atomically, avoiding potential V8 map transitions.

The arrays `property_names` and `property_values` must have the same length
specified by `property_count`. The properties are added to the object in the
order they appear in the arrays.

#### `napi_create_symbol`

Added in: v8.0.0**N-API Version:** 1

```c
napi_status napi_create_symbol(napi_env env,
                               napi_value description,
                               napi_value* result)
```

- `[in] env`: The environment that the API is invoked under.
- `[in] description`: Optional `napi_value` which refers to a JavaScript
  `string` to be set as the description for the symbol.
- `[out] result`: A `napi_value` representing a JavaScript `symbol`.

Returns `napi_ok` if the API succeeded.

This API creates a JavaScript `symbol` value from a UTF8-encoded C string.

The JavaScript `symbol` type is described in [Section symbol type](https://tc39.es/ecma262/#sec-ecmascript-language-types-symbol-type)
of the ECMAScript Language Specification.

#### `node_api_symbol_for`

Added in: v17.5.0, v16.15.0**N-API Version:** 9

```c
napi_status node_api_symbol_for(napi_env env,
                                const char* utf8description,
                                size_t length,
                                napi_value* result)
```

- `[in] env`: The environment that the API is invoked under.
- `[in] utf8description`: UTF-8 C string representing the text to be used as the
  description for the symbol.
- `[in] length`: The length of the description string in bytes, or
  `NAPI_AUTO_LENGTH` if it is null-terminated.
- `[out] result`: A `napi_value` representing a JavaScript `symbol`.

Returns `napi_ok` if the API succeeded.

This API searches in the global registry for an existing symbol with the given
description. If the symbol already exists it will be returned, otherwise a new
symbol will be created in the registry.

The JavaScript `symbol` type is described in [Section symbol type](https://tc39.es/ecma262/#sec-ecmascript-language-types-symbol-type) of the ECMAScript
Language Specification.

#### `napi_create_typedarray`

Added in: v8.0.0**N-API Version:** 1

**History**

| Version | Changes |
| --- | --- |
| v26.2.0 | Added support for `SharedArrayBuffer`. |

```c
napi_status napi_create_typedarray(napi_env env,
                                   napi_typedarray_type type,
                                   size_t length,
                                   napi_value arraybuffer,
                                   size_t byte_offset,
                                   napi_value* result)
```

- `[in] env`: The environment that the API is invoked under.
- `[in] type`: Scalar datatype of the elements within the `TypedArray`.
- `[in] length`: Number of elements in the `TypedArray`.
- `[in] arraybuffer`: `ArrayBuffer` or `SharedArrayBuffer` underlying the
  typed array.
- `[in] byte_offset`: The byte offset within the `ArrayBuffer` or
  `SharedArrayBuffer` from which to start projecting the `TypedArray`.
- `[out] result`: A `napi_value` representing a JavaScript `TypedArray`.

Returns `napi_ok` if the API succeeded.

This API creates a JavaScript `TypedArray` object over an existing
`ArrayBuffer` or `SharedArrayBuffer`. `TypedArray` objects provide an
array-like view over an underlying data buffer where each element has the same
underlying binary scalar datatype.

It is required that `(length * size_of_element) + byte_offset` is less than or
equal to the size in bytes of the `ArrayBuffer` or `SharedArrayBuffer` passed
in. If not, a `RangeError` exception is raised.

For element sizes greater than 1, `byte_offset` is required to be a multiple
of the element size. If not, a `RangeError` exception is raised.

JavaScript `TypedArray` objects are described in
[Section TypedArray objects](https://tc39.es/ecma262/#sec-typedarray-objects) of the ECMAScript Language Specification.

#### `node_api_create_buffer_from_arraybuffer`

Added in: v23.0.0, v22.12.0**N-API Version:** 10

```c
napi_status NAPI_CDECL node_api_create_buffer_from_arraybuffer(napi_env env,
                                                              napi_value arraybuffer,
                                                              size_t byte_offset,
                                                              size_t byte_length,
                                                              napi_value* result)
```

- **`[in] env`**: The environment that the API is invoked under.
- **`[in] arraybuffer`**: The `ArrayBuffer` from which the buffer will be created.
- **`[in] byte_offset`**: The byte offset within the `ArrayBuffer` from which to start creating the buffer.
- **`[in] byte_length`**: The length in bytes of the buffer to be created from the `ArrayBuffer`.
- **`[out] result`**: A `napi_value` representing the created JavaScript `Buffer` object.

Returns `napi_ok` if the API succeeded.

This API creates a JavaScript `Buffer` object from an existing `ArrayBuffer`.
The `Buffer` object is a Node.js-specific class that provides a way to work with binary data directly in JavaScript.

The byte range `[byte_offset, byte_offset + byte_length)`
must be within the bounds of the `ArrayBuffer`. If `byte_offset + byte_length`
exceeds the size of the `ArrayBuffer`, a `RangeError` exception is raised.

#### `napi_create_dataview`

Added in: v8.3.0**N-API Version:** 1

**History**

| Version | Changes |
| --- | --- |
| v25.5.0, v24.13.1 | Added support for `SharedArrayBuffer`. |

```c
napi_status napi_create_dataview(napi_env env,
                                 size_t byte_length,
                                 napi_value arraybuffer,
                                 size_t byte_offset,
                                 napi_value* result)
```

- `[in] env`: The environment that the API is invoked under.
- `[in] length`: Number of elements in the `DataView`.
- `[in] arraybuffer`: `ArrayBuffer` or `SharedArrayBuffer` underlying the
  `DataView`.
- `[in] byte_offset`: The byte offset within the `ArrayBuffer` from which to
  start projecting the `DataView`.
- `[out] result`: A `napi_value` representing a JavaScript `DataView`.

Returns `napi_ok` if the API succeeded.

This API creates a JavaScript `DataView` object over an existing `ArrayBuffer`
or `SharedArrayBuffer`. `DataView` objects provide an array-like view over an
underlying data buffer, but one which allows items of different size and type in
the `ArrayBuffer` or `SharedArrayBuffer`.

It is required that `byte_length + byte_offset` is less than or equal to the
size in bytes of the array passed in. If not, a `RangeError` exception is
raised.

JavaScript `DataView` objects are described in
[Section DataView objects](https://tc39.es/ecma262/#sec-dataview-objects) of the ECMAScript Language Specification.

### Functions to convert from C types to Node-API

#### `napi_create_int32`

Added in: v8.4.0**N-API Version:** 1

```c
napi_status napi_create_int32(napi_env env, int32_t value, napi_value* result)
```

- `[in] env`: The environment that the API is invoked under.
- `[in] value`: Integer value to be represented in JavaScript.
- `[out] result`: A `napi_value` representing a JavaScript `number`.

Returns `napi_ok` if the API succeeded.

This API is used to convert from the C `int32_t` type to the JavaScript
`number` type.

The JavaScript `number` type is described in
[Section number type](https://tc39.es/ecma262/#sec-ecmascript-language-types-number-type) of the ECMAScript Language Specification.

#### `napi_create_uint32`

Added in: v8.4.0**N-API Version:** 1

```c
napi_status napi_create_uint32(napi_env env, uint32_t value, napi_value* result)
```

- `[in] env`: The environment that the API is invoked under.
- `[in] value`: Unsigned integer value to be represented in JavaScript.
- `[out] result`: A `napi_value` representing a JavaScript `number`.

Returns `napi_ok` if the API succeeded.

This API is used to convert from the C `uint32_t` type to the JavaScript
`number` type.

The JavaScript `number` type is described in
[Section number type](https://tc39.es/ecma262/#sec-ecmascript-language-types-number-type) of the ECMAScript Language Specification.

#### `napi_create_int64`

Added in: v8.4.0**N-API Version:** 1

```c
napi_status napi_create_int64(napi_env env, int64_t value, napi_value* result)
```

- `[in] env`: The environment that the API is invoked under.
- `[in] value`: Integer value to be represented in JavaScript.
- `[out] result`: A `napi_value` representing a JavaScript `number`.

Returns `napi_ok` if the API succeeded.

This API is used to convert from the C `int64_t` type to the JavaScript
`number` type.

The JavaScript `number` type is described in [Section number type](https://tc39.es/ecma262/#sec-ecmascript-language-types-number-type)
of the ECMAScript Language Specification. Note the complete range of `int64_t`
cannot be represented with full precision in JavaScript. Integer values
outside the range of [`Number.MIN_SAFE_INTEGER`](https://tc39.es/ecma262/#sec-number.min_safe_integer) `-(2**53 - 1)` -
[`Number.MAX_SAFE_INTEGER`](https://tc39.es/ecma262/#sec-number.max_safe_integer) `(2**53 - 1)` will lose precision.

#### `napi_create_double`

Added in: v8.4.0**N-API Version:** 1

```c
napi_status napi_create_double(napi_env env, double value, napi_value* result)
```

- `[in] env`: The environment that the API is invoked under.
- `[in] value`: Double-precision value to be represented in JavaScript.
- `[out] result`: A `napi_value` representing a JavaScript `number`.

Returns `napi_ok` if the API succeeded.

This API is used to convert from the C `double` type to the JavaScript
`number` type.

The JavaScript `number` type is described in
[Section number type](https://tc39.es/ecma262/#sec-ecmascript-language-types-number-type) of the ECMAScript Language Specification.

#### `napi_create_bigint_int64`

Added in: v10.7.0**N-API Version:** 6

```c
napi_status napi_create_bigint_int64(napi_env env,
                                     int64_t value,
                                     napi_value* result);
```

- `[in] env`: The environment that the API is invoked under.
- `[in] value`: Integer value to be represented in JavaScript.
- `[out] result`: A `napi_value` representing a JavaScript `BigInt`.

Returns `napi_ok` if the API succeeded.

This API converts the C `int64_t` type to the JavaScript `BigInt` type.

#### `napi_create_bigint_uint64`

Added in: v10.7.0**N-API Version:** 6

```c
napi_status napi_create_bigint_uint64(napi_env env,
                                      uint64_t value,
                                      napi_value* result);
```

- `[in] env`: The environment that the API is invoked under.
- `[in] value`: Unsigned integer value to be represented in JavaScript.
- `[out] result`: A `napi_value` representing a JavaScript `BigInt`.

Returns `napi_ok` if the API succeeded.

This API converts the C `uint64_t` type to the JavaScript `BigInt` type.

#### `napi_create_bigint_words`

Added in: v10.7.0**N-API Version:** 6

```c
napi_status napi_create_bigint_words(napi_env env,
                                     int sign_bit,
                                     size_t word_count,
                                     const uint64_t* words,
                                     napi_value* result);
```

- `[in] env`: The environment that the API is invoked under.
- `[in] sign_bit`: Determines if the resulting `BigInt` will be positive or
  negative.
- `[in] word_count`: The length of the `words` array.
- `[in] words`: An array of `uint64_t` little-endian 64-bit words.
- `[out] result`: A `napi_value` representing a JavaScript `BigInt`.

Returns `napi_ok` if the API succeeded.

This API converts an array of unsigned 64-bit words into a single `BigInt`
value.

The resulting `BigInt` is calculated as: (–1)`sign_bit` (`words[0]`
× (264)0 + `words[1]` × (264)1 + …)

#### `napi_create_string_latin1`

Added in: v8.0.0**N-API Version:** 1

```c
napi_status napi_create_string_latin1(napi_env env,
                                      const char* str,
                                      size_t length,
                                      napi_value* result);
```

- `[in] env`: The environment that the API is invoked under.
- `[in] str`: Character buffer representing an ISO-8859-1-encoded string.
- `[in] length`: The length of the string in bytes, or `NAPI_AUTO_LENGTH` if it
  is null-terminated.
- `[out] result`: A `napi_value` representing a JavaScript `string`.

Returns `napi_ok` if the API succeeded.

This API creates a JavaScript `string` value from an ISO-8859-1-encoded C
string. The native string is copied.

The JavaScript `string` type is described in
[Section string type](https://tc39.es/ecma262/#sec-ecmascript-language-types-string-type) of the ECMAScript Language Specification.

#### `node_api_create_external_string_latin1`

Added in: v20.4.0, v18.18.0**N-API Version:** 10

```c
napi_status
node_api_create_external_string_latin1(napi_env env,
                                       char* str,
                                       size_t length,
                                       napi_finalize finalize_callback,
                                       void* finalize_hint,
                                       napi_value* result,
                                       bool* copied);
```

- `[in] env`: The environment that the API is invoked under.
- `[in] str`: Character buffer representing an ISO-8859-1-encoded string.
- `[in] length`: The length of the string in bytes, or `NAPI_AUTO_LENGTH` if it
  is null-terminated.
- `[in] finalize_callback`: The function to call when the string is being
  collected. The function will be called with the following parameters:
  - `[in] env`: The environment in which the add-on is running. This value
    may be null if the string is being collected as part of the termination
    of the worker or the main Node.js instance.
  - `[in] data`: This is the value `str` as a `void*` pointer.
  - `[in] finalize_hint`: This is the value `finalize_hint` that was given
    to the API.
    [`napi_finalize`](09-n-api-part1.md#napi_finalize) provides more details.
    This parameter is optional. Passing a null value means that the add-on
    doesn't need to be notified when the corresponding JavaScript string is
    collected.
- `[in] finalize_hint`: Optional hint to pass to the finalize callback during
  collection.
- `[out] result`: A `napi_value` representing a JavaScript `string`.
- `[out] copied`: Whether the string was copied. If it was, the finalizer will
  already have been invoked to destroy `str`.

Returns `napi_ok` if the API succeeded.

This API creates a JavaScript `string` value from an ISO-8859-1-encoded C
string. The native string may not be copied and must thus exist for the entire
life cycle of the JavaScript value.

The JavaScript `string` type is described in
[Section string type](https://tc39.es/ecma262/#sec-ecmascript-language-types-string-type) of the ECMAScript Language Specification.

#### `napi_create_string_utf16`

Added in: v8.0.0**N-API Version:** 1

```c
napi_status napi_create_string_utf16(napi_env env,
                                     const char16_t* str,
                                     size_t length,
                                     napi_value* result)
```

- `[in] env`: The environment that the API is invoked under.
- `[in] str`: Character buffer representing a UTF16-LE-encoded string.
- `[in] length`: The length of the string in two-byte code units, or
  `NAPI_AUTO_LENGTH` if it is null-terminated.
- `[out] result`: A `napi_value` representing a JavaScript `string`.

Returns `napi_ok` if the API succeeded.

This API creates a JavaScript `string` value from a UTF16-LE-encoded C string.
The native string is copied.

The JavaScript `string` type is described in
[Section string type](https://tc39.es/ecma262/#sec-ecmascript-language-types-string-type) of the ECMAScript Language Specification.

#### `node_api_create_external_string_utf16`

Added in: v20.4.0, v18.18.0**N-API Version:** 10

```c
napi_status
node_api_create_external_string_utf16(napi_env env,
                                      char16_t* str,
                                      size_t length,
                                      napi_finalize finalize_callback,
                                      void* finalize_hint,
                                      napi_value* result,
                                      bool* copied);
```

- `[in] env`: The environment that the API is invoked under.
- `[in] str`: Character buffer representing a UTF16-LE-encoded string.
- `[in] length`: The length of the string in two-byte code units, or
  `NAPI_AUTO_LENGTH` if it is null-terminated.
- `[in] finalize_callback`: The function to call when the string is being
  collected. The function will be called with the following parameters:
  - `[in] env`: The environment in which the add-on is running. This value
    may be null if the string is being collected as part of the termination
    of the worker or the main Node.js instance.
  - `[in] data`: This is the value `str` as a `void*` pointer.
  - `[in] finalize_hint`: This is the value `finalize_hint` that was given
    to the API.
    [`napi_finalize`](09-n-api-part1.md#napi_finalize) provides more details.
    This parameter is optional. Passing a null value means that the add-on
    doesn't need to be notified when the corresponding JavaScript string is
    collected.
- `[in] finalize_hint`: Optional hint to pass to the finalize callback during
  collection.
- `[out] result`: A `napi_value` representing a JavaScript `string`.
- `[out] copied`: Whether the string was copied. If it was, the finalizer will
  already have been invoked to destroy `str`.

Returns `napi_ok` if the API succeeded.

This API creates a JavaScript `string` value from a UTF16-LE-encoded C string.
The native string may not be copied and must thus exist for the entire life
cycle of the JavaScript value.

The JavaScript `string` type is described in
[Section string type](https://tc39.es/ecma262/#sec-ecmascript-language-types-string-type) of the ECMAScript Language Specification.

#### `napi_create_string_utf8`

Added in: v8.0.0**N-API Version:** 1

```c
napi_status napi_create_string_utf8(napi_env env,
                                    const char* str,
                                    size_t length,
                                    napi_value* result)
```

- `[in] env`: The environment that the API is invoked under.
- `[in] str`: Character buffer representing a UTF8-encoded string.
- `[in] length`: The length of the string in bytes, or `NAPI_AUTO_LENGTH` if it
  is null-terminated.
- `[out] result`: A `napi_value` representing a JavaScript `string`.

Returns `napi_ok` if the API succeeded.

This API creates a JavaScript `string` value from a UTF8-encoded C string.
The native string is copied.

The JavaScript `string` type is described in
[Section string type](https://tc39.es/ecma262/#sec-ecmascript-language-types-string-type) of the ECMAScript Language Specification.

### Functions to create optimized property keys

Many JavaScript engines including V8 use internalized strings as keys
to set and get property values. They typically use a hash table to create
and lookup such strings. While it adds some cost per key creation, it improves
the performance after that by enabling comparison of string pointers instead
of the whole strings.

If a new JavaScript string is intended to be used as a property key, then for
some JavaScript engines it will be more efficient to use the functions in this
section. Otherwise, use the `napi_create_string_utf8` or
`node_api_create_external_string_utf8` series functions as there may be
additional overhead in creating/storing strings with the property key
creation methods.

#### `node_api_create_property_key_latin1`

Added in: v22.9.0, v20.18.0**N-API Version:** 10

```c
napi_status NAPI_CDECL node_api_create_property_key_latin1(napi_env env,
                                                           const char* str,
                                                           size_t length,
                                                           napi_value* result);
```

- `[in] env`: The environment that the API is invoked under.
- `[in] str`: Character buffer representing an ISO-8859-1-encoded string.
- `[in] length`: The length of the string in bytes, or `NAPI_AUTO_LENGTH` if it
  is null-terminated.
- `[out] result`: A `napi_value` representing an optimized JavaScript `string`
  to be used as a property key for objects.

Returns `napi_ok` if the API succeeded.

This API creates an optimized JavaScript `string` value from
an ISO-8859-1-encoded C string to be used as a property key for objects.
The native string is copied. In contrast with `napi_create_string_latin1`,
subsequent calls to this function with the same `str` pointer may benefit from a speedup
in the creation of the requested `napi_value`, depending on the engine.

The JavaScript `string` type is described in
[Section string type](https://tc39.es/ecma262/#sec-ecmascript-language-types-string-type) of the ECMAScript Language Specification.

#### `node_api_create_property_key_utf16`

Added in: v21.7.0, v20.12.0**N-API Version:** 10

```c
napi_status NAPI_CDECL node_api_create_property_key_utf16(napi_env env,
                                                          const char16_t* str,
                                                          size_t length,
                                                          napi_value* result);
```

- `[in] env`: The environment that the API is invoked under.
- `[in] str`: Character buffer representing a UTF16-LE-encoded string.
- `[in] length`: The length of the string in two-byte code units, or
  `NAPI_AUTO_LENGTH` if it is null-terminated.
- `[out] result`: A `napi_value` representing an optimized JavaScript `string`
  to be used as a property key for objects.

Returns `napi_ok` if the API succeeded.

This API creates an optimized JavaScript `string` value from
a UTF16-LE-encoded C string to be used as a property key for objects.
The native string is copied.

The JavaScript `string` type is described in
[Section string type](https://tc39.es/ecma262/#sec-ecmascript-language-types-string-type) of the ECMAScript Language Specification.

#### `node_api_create_property_key_utf8`

Added in: v22.9.0, v20.18.0**N-API Version:** 10

```c
napi_status NAPI_CDECL node_api_create_property_key_utf8(napi_env env,
                                                         const char* str,
                                                         size_t length,
                                                         napi_value* result);
```

- `[in] env`: The environment that the API is invoked under.
- `[in] str`: Character buffer representing a UTF8-encoded string.
- `[in] length`: The length of the string in two-byte code units, or
  `NAPI_AUTO_LENGTH` if it is null-terminated.
- `[out] result`: A `napi_value` representing an optimized JavaScript `string`
  to be used as a property key for objects.

Returns `napi_ok` if the API succeeded.

This API creates an optimized JavaScript `string` value from
a UTF8-encoded C string to be used as a property key for objects.
The native string is copied.

The JavaScript `string` type is described in
[Section string type](https://tc39.es/ecma262/#sec-ecmascript-language-types-string-type) of the ECMAScript Language Specification.

### Functions to convert from Node-API to C types

#### `napi_get_array_length`

Added in: v8.0.0**N-API Version:** 1

```c
napi_status napi_get_array_length(napi_env env,
                                  napi_value value,
                                  uint32_t* result)
```

- `[in] env`: The environment that the API is invoked under.
- `[in] value`: `napi_value` representing the JavaScript `Array` whose length is
  being queried.
- `[out] result`: `uint32` representing length of the array.

Returns `napi_ok` if the API succeeded.

This API returns the length of an array.

`Array` length is described in [Section Array instance length](https://tc39.es/ecma262/#sec-properties-of-array-instances-length) of the ECMAScript Language
Specification.

#### `napi_get_arraybuffer_info`

Added in: v8.0.0**N-API Version:** 1

**History**

| Version | Changes |
| --- | --- |
| v24.9.0, v22.21.0 | Added support for `SharedArrayBuffer`. |

```c
napi_status napi_get_arraybuffer_info(napi_env env,
                                      napi_value arraybuffer,
                                      void** data,
                                      size_t* byte_length)
```

- `[in] env`: The environment that the API is invoked under.
- `[in] arraybuffer`: `napi_value` representing the `ArrayBuffer` or `SharedArrayBuffer` being queried.
- `[out] data`: The underlying data buffer of the `ArrayBuffer` or `SharedArrayBuffer`
  is `0`, this may be `NULL` or any other pointer value.
- `[out] byte_length`: Length in bytes of the underlying data buffer.

Returns `napi_ok` if the API succeeded.

This API is used to retrieve the underlying data buffer of an `ArrayBuffer` or `SharedArrayBuffer` and its length.

*WARNING*: Use caution while using this API. The lifetime of the underlying data
buffer is managed by the `ArrayBuffer` or `SharedArrayBuffer` even after it's returned. A
possible safe way to use this API is in conjunction with
[`napi_create_reference`](#napi_create_reference), which can be used to guarantee control over the
lifetime of the `ArrayBuffer` or `SharedArrayBuffer`. It's also safe to use the returned data buffer
within the same callback as long as there are no calls to other APIs that might
trigger a GC.

#### `napi_get_buffer_info`

Added in: v8.0.0**N-API Version:** 1

```c
napi_status napi_get_buffer_info(napi_env env,
                                 napi_value value,
                                 void** data,
                                 size_t* length)
```

- `[in] env`: The environment that the API is invoked under.
- `[in] value`: `napi_value` representing the `node::Buffer` or `Uint8Array`
  being queried.
- `[out] data`: The underlying data buffer of the `node::Buffer` or
  `Uint8Array`. If length is `0`, this may be `NULL` or any other pointer value.
- `[out] length`: Length in bytes of the underlying data buffer.

Returns `napi_ok` if the API succeeded.

This method returns the identical `data` and `byte_length` as
[`napi_get_typedarray_info`](09-n-api-part3.md#napi_get_typedarray_info). And `napi_get_typedarray_info` accepts a
`node::Buffer` (a Uint8Array) as the value too.

This API is used to retrieve the underlying data buffer of a `node::Buffer`
and its length.

*Warning*: Use caution while using this API since the underlying data buffer's
lifetime is not guaranteed if it's managed by the VM.

[Contents](../README.md) | [Previous: C/C++ addons with Node-API, part 1 of 4](09-n-api-part1.md) | [Next: C/C++ addons with Node-API, part 3 of 4](09-n-api-part3.md)
