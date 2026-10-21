[Contents](../README.md) | [Previous: C/C++ addons with Node-API, part 3 of 4](09-n-api-part3.md) | [Next: C++ embedder API](10-embedding.md)

Source: [https://nodejs.org/docs/latest/api/n-api.html](https://nodejs.org/docs/latest/api/n-api.html)

Part 4 of 4 of this section.

# C/C++ addons with Node-API (part 4 of 4)

## Object wrap

Node-API offers a way to "wrap" C++ classes and instances so that the class
constructor and methods can be called from JavaScript.

1. The [`napi_define_class`](#napi_define_class) API defines a JavaScript class with constructor,
   static properties and methods, and instance properties and methods that
   correspond to the C++ class.
2. When JavaScript code invokes the constructor, the constructor callback
   uses [`napi_wrap`](#napi_wrap) to wrap a new C++ instance in a JavaScript object,
   then returns the wrapper object.
3. When JavaScript code invokes a method or property accessor on the class,
   the corresponding `napi_callback` C++ function is invoked. For an instance
   callback, [`napi_unwrap`](#napi_unwrap) obtains the C++ instance that is the target of
   the call.

For wrapped objects it may be difficult to distinguish between a function
called on a class prototype and a function called on an instance of a class.
A common pattern used to address this problem is to save a persistent
reference to the class constructor for later `instanceof` checks.

```c
napi_value MyClass_constructor = NULL;
status = napi_get_reference_value(env, MyClass::es_constructor, &MyClass_constructor);
assert(napi_ok == status);
bool is_instance = false;
status = napi_instanceof(env, es_this, MyClass_constructor, &is_instance);
assert(napi_ok == status);
if (is_instance) {
  // napi_unwrap() ...
} else {
  // otherwise...
}
```

The reference must be freed once it is no longer needed.

There are occasions where `napi_instanceof()` is insufficient for ensuring that
a JavaScript object is a wrapper for a certain native type. This is the case
especially when wrapped JavaScript objects are passed back into the addon via
static methods rather than as the `this` value of prototype methods. In such
cases there is a chance that they may be unwrapped incorrectly.

```js
const myAddon = require('./build/Release/my_addon.node');

// `openDatabase()` returns a JavaScript object that wraps a native database
// handle.
const dbHandle = myAddon.openDatabase();

// `query()` returns a JavaScript object that wraps a native query handle.
const queryHandle = myAddon.query(dbHandle, 'Gimme ALL the things!');

// There is an accidental error in the line below. The first parameter to
// `myAddon.queryHasRecords()` should be the database handle (`dbHandle`), not
// the query handle (`query`), so the correct condition for the while-loop
// should be
//
// myAddon.queryHasRecords(dbHandle, queryHandle)
//
while (myAddon.queryHasRecords(queryHandle, dbHandle)) {
  // retrieve records
}
```

In the above example `myAddon.queryHasRecords()` is a method that accepts two
arguments. The first is a database handle and the second is a query handle.
Internally, it unwraps the first argument and casts the resulting pointer to a
native database handle. It then unwraps the second argument and casts the
resulting pointer to a query handle. If the arguments are passed in the wrong
order, the casts will work, however, there is a good chance that the underlying
database operation will fail, or will even cause an invalid memory access.

To ensure that the pointer retrieved from the first argument is indeed a pointer
to a database handle and, similarly, that the pointer retrieved from the second
argument is indeed a pointer to a query handle, the implementation of
`queryHasRecords()` has to perform a type validation. Retaining the JavaScript
class constructor from which the database handle was instantiated and the
constructor from which the query handle was instantiated in `napi_ref`s can
help, because `napi_instanceof()` can then be used to ensure that the instances
passed into `queryHashRecords()` are indeed of the correct type.

Unfortunately, `napi_instanceof()` does not protect against prototype
manipulation. For example, the prototype of the database handle instance can be
set to the prototype of the constructor for query handle instances. In this
case, the database handle instance can appear as a query handle instance, and it
will pass the `napi_instanceof()` test for a query handle instance, while still
containing a pointer to a database handle.

To this end, Node-API provides type-tagging capabilities.

A type tag is a 128-bit integer unique to the addon. Node-API provides the
`napi_type_tag` structure for storing a type tag. When such a value is passed
along with a JavaScript object or [external](09-n-api-part2.md#napi_create_external) stored in a `napi_value` to
`napi_type_tag_object()`, the JavaScript object will be "marked" with the
type tag. The "mark" is invisible on the JavaScript side. When a JavaScript
object arrives into a native binding, `napi_check_object_type_tag()` can be used
along with the original type tag to determine whether the JavaScript object was
previously "marked" with the type tag. This creates a type-checking capability
of a higher fidelity than `napi_instanceof()` can provide, because such type-
tagging survives prototype manipulation and addon unloading/reloading.

Continuing the above example, the following skeleton addon implementation
illustrates the use of `napi_type_tag_object()` and
`napi_check_object_type_tag()`.

```c
// This value is the type tag for a database handle. The command
//
//   uuidgen | sed -r -e 's/-//g' -e 's/(.{16})(.*)/0x\1, 0x\2/'
//
// can be used to obtain the two values with which to initialize the structure.
static const napi_type_tag DatabaseHandleTypeTag = {
  0x1edf75a38336451d, 0xa5ed9ce2e4c00c38
};

// This value is the type tag for a query handle.
static const napi_type_tag QueryHandleTypeTag = {
  0x9c73317f9fad44a3, 0x93c3920bf3b0ad6a
};

static napi_value
openDatabase(napi_env env, napi_callback_info info) {
  napi_status status;
  napi_value result;

  // Perform the underlying action which results in a database handle.
  DatabaseHandle* dbHandle = open_database();

  // Create a new, empty JS object.
  status = napi_create_object(env, &result);
  if (status != napi_ok) return NULL;

  // Tag the object to indicate that it holds a pointer to a `DatabaseHandle`.
  status = napi_type_tag_object(env, result, &DatabaseHandleTypeTag);
  if (status != napi_ok) return NULL;

  // Store the pointer to the `DatabaseHandle` structure inside the JS object.
  status = napi_wrap(env, result, dbHandle, NULL, NULL, NULL);
  if (status != napi_ok) return NULL;

  return result;
}

// Later when we receive a JavaScript object purporting to be a database handle
// we can use `napi_check_object_type_tag()` to ensure that it is indeed such a
// handle.

static napi_value
query(napi_env env, napi_callback_info info) {
  napi_status status;
  size_t argc = 2;
  napi_value argv[2];
  bool is_db_handle;

  status = napi_get_cb_info(env, info, &argc, argv, NULL, NULL);
  if (status != napi_ok) return NULL;

  // Check that the object passed as the first parameter has the previously
  // applied tag.
  status = napi_check_object_type_tag(env,
                                      argv[0],
                                      &DatabaseHandleTypeTag,
                                      &is_db_handle);
  if (status != napi_ok) return NULL;

  // Throw a `TypeError` if it doesn't.
  if (!is_db_handle) {
    // Throw a TypeError.
    return NULL;
  }
}
```

### `napi_define_class`

Added in: v8.0.0**N-API Version:** 1

```c
napi_status napi_define_class(napi_env env,
                              const char* utf8name,
                              size_t length,
                              napi_callback constructor,
                              void* data,
                              size_t property_count,
                              const napi_property_descriptor* properties,
                              napi_value* result);
```

- `[in] env`: The environment that the API is invoked under.
- `[in] utf8name`: Name of the JavaScript constructor function. For clarity,
  it is recommended to use the C++ class name when wrapping a C++ class.
- `[in] length`: The length of the `utf8name` in bytes, or `NAPI_AUTO_LENGTH`
  if it is null-terminated.
- `[in] constructor`: Callback function that handles constructing instances
  of the class. When wrapping a C++ class, this method must be a static member
  with the [`napi_callback`](09-n-api-part1.md#napi_callback) signature. A C++ class constructor cannot be
  used. [`napi_callback`](09-n-api-part1.md#napi_callback) provides more details.
- `[in] data`: Optional data to be passed to the constructor callback as
  the `data` property of the callback info.
- `[in] property_count`: Number of items in the `properties` array argument.
- `[in] properties`: Array of property descriptors describing static and
  instance data properties, accessors, and methods on the class
  See `napi_property_descriptor`.
- `[out] result`: A `napi_value` representing the constructor function for
  the class.

Returns `napi_ok` if the API succeeded.

Defines a JavaScript class, including:

- A JavaScript constructor function that has the class name. When wrapping a
  corresponding C++ class, the callback passed via `constructor` can be used to
  instantiate a new C++ class instance, which can then be placed inside the
  JavaScript object instance being constructed using [`napi_wrap`](#napi_wrap).
- Properties on the constructor function whose implementation can call
  corresponding *static* data properties, accessors, and methods of the C++
  class (defined by property descriptors with the `napi_static` attribute).
- Properties on the constructor function's `prototype` object. When wrapping a
  C++ class, *non-static* data properties, accessors, and methods of the C++
  class can be called from the static functions given in the property
  descriptors without the `napi_static` attribute after retrieving the C++ class
  instance placed inside the JavaScript object instance by using
  [`napi_unwrap`](#napi_unwrap).

When wrapping a C++ class, the C++ constructor callback passed via `constructor`
should be a static method on the class that calls the actual class constructor,
then wraps the new C++ instance in a JavaScript object, and returns the wrapper
object. See [`napi_wrap`](#napi_wrap) for details.

The JavaScript constructor function returned from [`napi_define_class`](#napi_define_class) is
often saved and used later to construct new instances of the class from native
code, and/or to check whether provided values are instances of the class. In
that case, to prevent the function value from being garbage-collected, a
strong persistent reference to it can be created using
[`napi_create_reference`](09-n-api-part2.md#napi_create_reference), ensuring that the reference count is kept >= 1.

Any non-`NULL` data which is passed to this API via the `data` parameter or via
the `data` field of the `napi_property_descriptor` array items can be associated
with the resulting JavaScript constructor (which is returned in the `result`
parameter) and freed whenever the class is garbage-collected by passing both
the JavaScript function and the data to [`napi_add_finalizer`](#napi_add_finalizer).

### `napi_wrap`

Added in: v8.0.0**N-API Version:** 1

```c
napi_status napi_wrap(napi_env env,
                      napi_value js_object,
                      void* native_object,
                      napi_finalize finalize_cb,
                      void* finalize_hint,
                      napi_ref* result);
```

- `[in] env`: The environment that the API is invoked under.
- `[in] js_object`: The JavaScript object that will be the wrapper for the
  native object.
- `[in] native_object`: The native instance that will be wrapped in the
  JavaScript object.
- `[in] finalize_cb`: Optional native callback that can be used to free the
  native instance when the JavaScript object has been garbage-collected.
  [`napi_finalize`](09-n-api-part1.md#napi_finalize) provides more details.
- `[in] finalize_hint`: Optional contextual hint that is passed to the
  finalize callback.
- `[out] result`: Optional reference to the wrapped object.

Returns `napi_ok` if the API succeeded.

Wraps a native instance in a JavaScript object. The native instance can be
retrieved later using `napi_unwrap()`.

When JavaScript code invokes a constructor for a class that was defined using
`napi_define_class()`, the `napi_callback` for the constructor is invoked.
After constructing an instance of the native class, the callback must then call
`napi_wrap()` to wrap the newly constructed instance in the already-created
JavaScript object that is the `this` argument to the constructor callback.
(That `this` object was created from the constructor function's `prototype`,
so it already has definitions of all the instance properties and methods.)

Typically when wrapping a class instance, a finalize callback should be
provided that simply deletes the native instance that is received as the `data`
argument to the finalize callback.

The optional returned reference is initially a weak reference, meaning it
has a reference count of 0. Typically this reference count would be incremented
temporarily during async operations that require the instance to remain valid.

*Caution*: The optional returned reference (if obtained) should be deleted via
[`napi_delete_reference`](09-n-api-part2.md#napi_delete_reference) ONLY in response to the finalize callback
invocation. If it is deleted before then, then the finalize callback may never
be invoked. Therefore, when obtaining a reference a finalize callback is also
required in order to enable correct disposal of the reference.

Finalizer callbacks may be deferred, leaving a window where the object has
been garbage collected (and the weak reference is invalid) but the finalizer
hasn't been called yet. When using `napi_get_reference_value()` on weak
references returned by `napi_wrap()`, you should still handle an empty result.

Calling `napi_wrap()` a second time on an object will return an error. To
associate another native instance with the object, use `napi_remove_wrap()`
first.

### `napi_unwrap`

Added in: v8.0.0**N-API Version:** 1

```c
napi_status napi_unwrap(napi_env env,
                        napi_value js_object,
                        void** result);
```

- `[in] env`: The environment that the API is invoked under.
- `[in] js_object`: The object associated with the native instance.
- `[out] result`: Pointer to the wrapped native instance.

Returns `napi_ok` if the API succeeded.

Retrieves a native instance that was previously wrapped in a JavaScript
object using `napi_wrap()`.

When JavaScript code invokes a method or property accessor on the class, the
corresponding `napi_callback` is invoked. If the callback is for an instance
method or accessor, then the `this` argument to the callback is the wrapper
object; the wrapped C++ instance that is the target of the call can be obtained
then by calling `napi_unwrap()` on the wrapper object.

### `napi_remove_wrap`

Added in: v8.5.0**N-API Version:** 1

```c
napi_status napi_remove_wrap(napi_env env,
                             napi_value js_object,
                             void** result);
```

- `[in] env`: The environment that the API is invoked under.
- `[in] js_object`: The object associated with the native instance.
- `[out] result`: Pointer to the wrapped native instance.

Returns `napi_ok` if the API succeeded.

Retrieves a native instance that was previously wrapped in the JavaScript
object `js_object` using `napi_wrap()` and removes the wrapping. If a finalize
callback was associated with the wrapping, it will no longer be called when the
JavaScript object becomes garbage-collected.

### `napi_type_tag_object`

Added in: v14.8.0, v12.19.0**N-API Version:** 8

```c
napi_status napi_type_tag_object(napi_env env,
                                 napi_value js_object,
                                 const napi_type_tag* type_tag);
```

- `[in] env`: The environment that the API is invoked under.
- `[in] js_object`: The JavaScript object or [external](09-n-api-part2.md#napi_create_external) to be marked.
- `[in] type_tag`: The tag with which the object is to be marked.

Returns `napi_ok` if the API succeeded.

Associates the value of the `type_tag` pointer with the JavaScript object or
[external](09-n-api-part2.md#napi_create_external). `napi_check_object_type_tag()` can then be used to compare the tag
that was attached to the object with one owned by the addon to ensure that the
object has the right type.

If the object already has an associated type tag, this API will return
`napi_invalid_arg`.

### `napi_check_object_type_tag`

Added in: v14.8.0, v12.19.0**N-API Version:** 8

```c
napi_status napi_check_object_type_tag(napi_env env,
                                       napi_value js_object,
                                       const napi_type_tag* type_tag,
                                       bool* result);
```

- `[in] env`: The environment that the API is invoked under.
- `[in] js_object`: The JavaScript object or [external](09-n-api-part2.md#napi_create_external) whose type tag to
  examine.
- `[in] type_tag`: The tag with which to compare any tag found on the object.
- `[out] result`: Whether the type tag given matched the type tag on the
  object. `false` is also returned if no type tag was found on the object.

Returns `napi_ok` if the API succeeded.

Compares the pointer given as `type_tag` with any that can be found on
`js_object`. If no tag is found on `js_object` or, if a tag is found but it does
not match `type_tag`, then `result` is set to `false`. If a tag is found and it
matches `type_tag`, then `result` is set to `true`.

### `napi_add_finalizer`

Added in: v8.0.0**N-API Version:** 5

```c
napi_status napi_add_finalizer(napi_env env,
                               napi_value js_object,
                               void* finalize_data,
                               node_api_basic_finalize finalize_cb,
                               void* finalize_hint,
                               napi_ref* result);
```

- `[in] env`: The environment that the API is invoked under.
- `[in] js_object`: The JavaScript object to which the native data will be
  attached.
- `[in] finalize_data`: Optional data to be passed to `finalize_cb`.
- `[in] finalize_cb`: Native callback that will be used to free the
  native data when the JavaScript object has been garbage-collected.
  [`napi_finalize`](09-n-api-part1.md#napi_finalize) provides more details.
- `[in] finalize_hint`: Optional contextual hint that is passed to the
  finalize callback.
- `[out] result`: Optional reference to the JavaScript object.

Returns `napi_ok` if the API succeeded.

Adds a `napi_finalize` callback which will be called when the JavaScript object
in `js_object` has been garbage-collected.

This API can be called multiple times on a single JavaScript object.

*Caution*: The optional returned reference (if obtained) should be deleted via
[`napi_delete_reference`](09-n-api-part2.md#napi_delete_reference) ONLY in response to the finalize callback
invocation. If it is deleted before then, then the finalize callback may never
be invoked. Therefore, when obtaining a reference a finalize callback is also
required in order to enable correct disposal of the reference.

#### `node_api_post_finalizer`

Added in: v21.0.0, v20.10.0, v18.19.0

Stability: 1 - Experimental

```c
napi_status node_api_post_finalizer(node_api_basic_env env,
                                    napi_finalize finalize_cb,
                                    void* finalize_data,
                                    void* finalize_hint);
```

- `[in] env`: The environment that the API is invoked under.
- `[in] finalize_cb`: Native callback that will be used to free the
  native data when the JavaScript object has been garbage-collected.
  [`napi_finalize`](09-n-api-part1.md#napi_finalize) provides more details.
- `[in] finalize_data`: Optional data to be passed to `finalize_cb`.
- `[in] finalize_hint`: Optional contextual hint that is passed to the
  finalize callback.

Returns `napi_ok` if the API succeeded.

Schedules a `napi_finalize` callback to be called asynchronously in the
event loop.

Normally, finalizers are called while the GC (garbage collector) collects
objects. At that point calling any Node-API that may cause changes in the GC
state will be disabled and will crash Node.js.

`node_api_post_finalizer` helps to work around this limitation by allowing the
add-on to defer calls to such Node-APIs to a point in time outside of the GC
finalization.

## Simple asynchronous operations

Addon modules often need to leverage async helpers from libuv as part of their
implementation. This allows them to schedule work to be executed asynchronously
so that their methods can return in advance of the work being completed. This
allows them to avoid blocking overall execution of the Node.js application.

Node-API provides an ABI-stable interface for these
supporting functions which covers the most common asynchronous use cases.

Node-API defines the `napi_async_work` structure which is used to manage
asynchronous workers. Instances are created/deleted with
[`napi_create_async_work`](#napi_create_async_work) and [`napi_delete_async_work`](#napi_delete_async_work).

The `execute` and `complete` callbacks are functions that will be
invoked when the executor is ready to execute and when it completes its
task respectively.

The `execute` function should avoid making any Node-API calls
that could result in the execution of JavaScript or interaction with
JavaScript objects. Most often, any code that needs to make Node-API
calls should be made in `complete` callback instead.
Avoid using the `napi_env` parameter in the execute callback as
it will likely execute JavaScript.

These functions implement the following interfaces:

```c
typedef void (*napi_async_execute_callback)(napi_env env,
                                            void* data);
typedef void (*napi_async_complete_callback)(napi_env env,
                                             napi_status status,
                                             void* data);
```

When these methods are invoked, the `data` parameter passed will be the
addon-provided `void*` data that was passed into the
`napi_create_async_work` call.

Once created the async worker can be queued
for execution using the [`napi_queue_async_work`](#napi_queue_async_work) function:

```c
napi_status napi_queue_async_work(node_api_basic_env env,
                                  napi_async_work work);
```

[`napi_cancel_async_work`](#napi_cancel_async_work) can be used if the work needs
to be cancelled before the work has started execution.

After calling [`napi_cancel_async_work`](#napi_cancel_async_work), the `complete` callback
will be invoked with a status value of `napi_cancelled`.
The work should not be deleted before the `complete`
callback invocation, even when it was cancelled.

### `napi_create_async_work`

Added in: v8.0.0**N-API Version:** 1

**History**

| Version | Changes |
| --- | --- |
| v8.6.0 | Added `async_resource` and `async_resource_name` parameters. |

```c
napi_status napi_create_async_work(napi_env env,
                                   napi_value async_resource,
                                   napi_value async_resource_name,
                                   napi_async_execute_callback execute,
                                   napi_async_complete_callback complete,
                                   void* data,
                                   napi_async_work* result);
```

- `[in] env`: The environment that the API is invoked under.
- `[in] async_resource`: An optional object associated with the async work
  that will be passed to possible `async_hooks` [`init` hooks](05-async-hooks.md#initasyncid-type-triggerasyncid-resource).
- `[in] async_resource_name`: Identifier for the kind of resource that is being
  provided for diagnostic information exposed by the `async_hooks` API.
- `[in] execute`: The native function which should be called to execute the
  logic asynchronously. The given function is called from a worker pool thread
  and can execute in parallel with the main event loop thread.
- `[in] complete`: The native function which will be called when the
  asynchronous logic is completed or is cancelled. The given function is called
  from the main event loop thread. [`napi_async_complete_callback`](09-n-api-part1.md#napi_async_complete_callback) provides
  more details.
- `[in] data`: User-provided data context. This will be passed back into the
  execute and complete functions.
- `[out] result`: `napi_async_work*` which is the handle to the newly created
  async work.

Returns `napi_ok` if the API succeeded.

This API allocates a work object that is used to execute logic asynchronously.
It should be freed using [`napi_delete_async_work`](#napi_delete_async_work) once the work is no longer
required.

`async_resource_name` should be a null-terminated, UTF-8-encoded string.

The `async_resource_name` identifier is provided by the user and should be
representative of the type of async work being performed. It is also recommended
to apply namespacing to the identifier, e.g. by including the module name. See
the [`async_hooks` documentation](05-async-hooks.md#type) for more information.

### `napi_delete_async_work`

Added in: v8.0.0**N-API Version:** 1

```c
napi_status napi_delete_async_work(napi_env env,
                                   napi_async_work work);
```

- `[in] env`: The environment that the API is invoked under.
- `[in] work`: The handle returned by the call to `napi_create_async_work`.

Returns `napi_ok` if the API succeeded.

This API frees a previously allocated work object.

This API can be called even if there is a pending JavaScript exception.

### `napi_queue_async_work`

Added in: v8.0.0**N-API Version:** 1

```c
napi_status napi_queue_async_work(node_api_basic_env env,
                                  napi_async_work work);
```

- `[in] env`: The environment that the API is invoked under.
- `[in] work`: The handle returned by the call to `napi_create_async_work`.

Returns `napi_ok` if the API succeeded.

This API requests that the previously allocated work be scheduled
for execution. Once it returns successfully, this API must not be called again
with the same `napi_async_work` item or the result will be undefined.

### `napi_cancel_async_work`

Added in: v8.0.0**N-API Version:** 1

```c
napi_status napi_cancel_async_work(node_api_basic_env env,
                                   napi_async_work work);
```

- `[in] env`: The environment that the API is invoked under.
- `[in] work`: The handle returned by the call to `napi_create_async_work`.

Returns `napi_ok` if the API succeeded.

This API cancels queued work if it has not yet
been started. If it has already started executing, it cannot be
cancelled and `napi_generic_failure` will be returned. If successful,
the `complete` callback will be invoked with a status value of
`napi_cancelled`. The work should not be deleted before the `complete`
callback invocation, even if it has been successfully cancelled.

This API can be called even if there is a pending JavaScript exception.

## Custom asynchronous operations

The simple asynchronous work APIs above may not be appropriate for every
scenario. When using any other asynchronous mechanism, the following APIs
are necessary to ensure an asynchronous operation is properly tracked by
the runtime.

### `napi_async_init`

Added in: v8.6.0**N-API Version:** 1

**History**

| Version | Changes |
| --- | --- |
| v25.0.0 | The `async_resource` object will now be held as a strong reference. |

```c
napi_status napi_async_init(napi_env env,
                            napi_value async_resource,
                            napi_value async_resource_name,
                            napi_async_context* result)
```

- `[in] env`: The environment that the API is invoked under.
- `[in] async_resource`: Object associated with the async work
  that will be passed to possible `async_hooks` [`init` hooks](05-async-hooks.md#initasyncid-type-triggerasyncid-resource) and can be
  accessed by [`async_hooks.executionAsyncResource()`](05-async-hooks.md#async_hooksexecutionasyncresource).
- `[in] async_resource_name`: Identifier for the kind of resource that is being
  provided for diagnostic information exposed by the `async_hooks` API.
- `[out] result`: The initialized async context.

Returns `napi_ok` if the API succeeded.

In order to retain ABI compatibility with previous versions, passing `NULL`
for `async_resource` does not result in an error. However, this is not
recommended as this will result in undesirable behavior with `async_hooks`
[`init` hooks](05-async-hooks.md#initasyncid-type-triggerasyncid-resource) and `async_hooks.executionAsyncResource()` as the resource is
now required by the underlying `async_hooks` implementation in order to provide
the linkage between async callbacks.

Previous versions of this API were not maintaining a strong reference to
`async_resource` while the `napi_async_context` object existed and instead
expected the caller to hold a strong reference. This has been changed, as a
corresponding call to [`napi_async_destroy`](#napi_async_destroy) for every call to
`napi_async_init()` is a requirement in any case to avoid memory leaks.

### `napi_async_destroy`

Added in: v8.6.0**N-API Version:** 1

```c
napi_status napi_async_destroy(napi_env env,
                               napi_async_context async_context);
```

- `[in] env`: The environment that the API is invoked under.
- `[in] async_context`: The async context to be destroyed.

Returns `napi_ok` if the API succeeded.

This API can be called even if there is a pending JavaScript exception.

### `napi_make_callback`

Added in: v8.0.0**N-API Version:** 1

**History**

| Version | Changes |
| --- | --- |
| v8.6.0 | Added `async_context` parameter. |

```c
NAPI_EXTERN napi_status napi_make_callback(napi_env env,
                                           napi_async_context async_context,
                                           napi_value recv,
                                           napi_value func,
                                           size_t argc,
                                           const napi_value* argv,
                                           napi_value* result);
```

- `[in] env`: The environment that the API is invoked under.
- `[in] async_context`: Context for the async operation that is
  invoking the callback. This should normally be a value previously
  obtained from [`napi_async_init`](#napi_async_init).
  In order to retain ABI compatibility with previous versions, passing `NULL`
  for `async_context` does not result in an error. However, this results
  in incorrect operation of async hooks. Potential issues include loss of
  async context when using the `AsyncLocalStorage` API.
- `[in] recv`: The `this` value passed to the called function.
- `[in] func`: `napi_value` representing the JavaScript function to be invoked.
- `[in] argc`: The count of elements in the `argv` array.
- `[in] argv`: Array of JavaScript values as `napi_value` representing the
  arguments to the function. If `argc` is zero this parameter may be
  omitted by passing in `NULL`.
- `[out] result`: `napi_value` representing the JavaScript object returned.

Returns `napi_ok` if the API succeeded.

This method allows a JavaScript function object to be called from a native
add-on. This API is similar to `napi_call_function`. However, it is used to call
*from* native code back *into* JavaScript *after* returning from an async
operation (when there is no other script on the stack). It is a fairly simple
wrapper around `node::MakeCallback`.

Note it is *not* necessary to use `napi_make_callback` from within a
`napi_async_complete_callback`; in that situation the callback's async
context has already been set up, so a direct call to `napi_call_function`
is sufficient and appropriate. Use of the `napi_make_callback` function
may be required when implementing custom async behavior that does not use
`napi_create_async_work`.

Any `process.nextTick`s or Promises scheduled on the microtask queue by
JavaScript during the callback are ran before returning back to C/C++.

### `napi_open_callback_scope`

Added in: v9.6.0**N-API Version:** 3

```c
NAPI_EXTERN napi_status napi_open_callback_scope(napi_env env,
                                                 napi_value resource_object,
                                                 napi_async_context context,
                                                 napi_callback_scope* result)
```

- `[in] env`: The environment that the API is invoked under.
- `[in] resource_object`: An object associated with the async work
  that will be passed to possible `async_hooks` [`init` hooks](05-async-hooks.md#initasyncid-type-triggerasyncid-resource). This
  parameter has been deprecated and is ignored at runtime. Use the
  `async_resource` parameter in [`napi_async_init`](#napi_async_init) instead.
- `[in] context`: Context for the async operation that is invoking the callback.
  This should be a value previously obtained from [`napi_async_init`](#napi_async_init).
- `[out] result`: The newly created scope.

There are cases (for example, resolving promises) where it is
necessary to have the equivalent of the scope associated with a callback
in place when making certain Node-API calls. If there is no other script on
the stack the [`napi_open_callback_scope`](#napi_open_callback_scope) and
[`napi_close_callback_scope`](#napi_close_callback_scope) functions can be used to open/close
the required scope.

### `napi_close_callback_scope`

Added in: v9.6.0**N-API Version:** 3

```c
NAPI_EXTERN napi_status napi_close_callback_scope(napi_env env,
                                                  napi_callback_scope scope)
```

- `[in] env`: The environment that the API is invoked under.
- `[in] scope`: The scope to be closed.

This API can be called even if there is a pending JavaScript exception.

## Version management

### `napi_get_node_version`

Added in: v8.4.0**N-API Version:** 1

```c
typedef struct {
  uint32_t major;
  uint32_t minor;
  uint32_t patch;
  const char* release;
} napi_node_version;

napi_status napi_get_node_version(node_api_basic_env env,
                                  const napi_node_version** version);
```

- `[in] env`: The environment that the API is invoked under.
- `[out] version`: A pointer to version information for Node.js itself.

Returns `napi_ok` if the API succeeded.

This function fills the `version` struct with the major, minor, and patch
version of Node.js that is currently running, and the `release` field with the
value of [`process.release.name`](43-process-part2.md#processrelease).

The returned buffer is statically allocated and does not need to be freed.

### `napi_get_version`

Added in: v8.0.0**N-API Version:** 1

```c
napi_status napi_get_version(node_api_basic_env env,
                             uint32_t* result);
```

- `[in] env`: The environment that the API is invoked under.
- `[out] result`: The highest version of Node-API supported.

Returns `napi_ok` if the API succeeded.

This API returns the highest Node-API version supported by the
Node.js runtime. Node-API is planned to be additive such that
newer releases of Node.js may support additional API functions.
In order to allow an addon to use a newer function when running with
versions of Node.js that support it, while providing
fallback behavior when running with Node.js versions that don't
support it:

- Call `napi_get_version()` to determine if the API is available.
- If available, dynamically load a pointer to the function using `uv_dlsym()`.
- Use the dynamically loaded pointer to invoke the function.
- If the function is not available, provide an alternate implementation
  that does not use the function.

## Memory management

### `napi_adjust_external_memory`

Added in: v8.5.0**N-API Version:** 1

```c
NAPI_EXTERN napi_status napi_adjust_external_memory(node_api_basic_env env,
                                                    int64_t change_in_bytes,
                                                    int64_t* result);
```

- `[in] env`: The environment that the API is invoked under.
- `[in] change_in_bytes`: The change in externally allocated memory that is kept
  alive by JavaScript objects.
- `[out] result`: The adjusted value. This value should reflect the
  total amount of external memory with the given `change_in_bytes` included.
  The absolute value of the returned value should not be depended on.
  For example, implementations may use a single counter for all addons, or a
  counter for each addon.

Returns `napi_ok` if the API succeeded.

This function gives the runtime an indication of the amount of externally
allocated memory that is kept alive by JavaScript objects
(i.e. a JavaScript object that points to its own memory allocated by a
native addon). Registering externally allocated memory may, but is not
guaranteed to, trigger global garbage collections more
often than it would otherwise.

This function is expected to be called in a manner such that an
addon does not decrease the external memory more than it has
increased the external memory.

## Promises

Node-API provides facilities for creating `Promise` objects as described in
[Section Promise objects](https://tc39.es/ecma262/#sec-promise-objects) of the ECMA specification. It implements promises as a pair of
objects. When a promise is created by `napi_create_promise()`, a "deferred"
object is created and returned alongside the `Promise`. The deferred object is
bound to the created `Promise` and is the only means to resolve or reject the
`Promise` using `napi_resolve_deferred()` or `napi_reject_deferred()`. The
deferred object that is created by `napi_create_promise()` is freed by
`napi_resolve_deferred()` or `napi_reject_deferred()`. The `Promise` object may
be returned to JavaScript where it can be used in the usual fashion.

For example, to create a promise and pass it to an asynchronous worker:

```c
napi_deferred deferred;
napi_value promise;
napi_status status;

// Create the promise.
status = napi_create_promise(env, &deferred, &promise);
if (status != napi_ok) return NULL;

// Pass the deferred to a function that performs an asynchronous action.
do_something_asynchronous(deferred);

// Return the promise to JS
return promise;
```

The above function `do_something_asynchronous()` would perform its asynchronous
action and then it would resolve or reject the deferred, thereby concluding the
promise and freeing the deferred:

```c
napi_deferred deferred;
napi_value undefined;
napi_status status;

// Create a value with which to conclude the deferred.
status = napi_get_undefined(env, &undefined);
if (status != napi_ok) return NULL;

// Resolve or reject the promise associated with the deferred depending on
// whether the asynchronous action succeeded.
if (asynchronous_action_succeeded) {
  status = napi_resolve_deferred(env, deferred, undefined);
} else {
  status = napi_reject_deferred(env, deferred, undefined);
}
if (status != napi_ok) return NULL;

// At this point the deferred has been freed, so we should assign NULL to it.
deferred = NULL;
```

### `napi_create_promise`

Added in: v8.5.0**N-API Version:** 1

```c
napi_status napi_create_promise(napi_env env,
                                napi_deferred* deferred,
                                napi_value* promise);
```

- `[in] env`: The environment that the API is invoked under.
- `[out] deferred`: A newly created deferred object which can later be passed to
  `napi_resolve_deferred()` or `napi_reject_deferred()` to resolve resp. reject
  the associated promise.
- `[out] promise`: The JavaScript promise associated with the deferred object.

Returns `napi_ok` if the API succeeded.

This API creates a deferred object and a JavaScript promise.

### `napi_resolve_deferred`

Added in: v8.5.0**N-API Version:** 1

```c
napi_status napi_resolve_deferred(napi_env env,
                                  napi_deferred deferred,
                                  napi_value resolution);
```

- `[in] env`: The environment that the API is invoked under.
- `[in] deferred`: The deferred object whose associated promise to resolve.
- `[in] resolution`: The value with which to resolve the promise.

This API resolves a JavaScript promise by way of the deferred object
with which it is associated. Thus, it can only be used to resolve JavaScript
promises for which the corresponding deferred object is available. This
effectively means that the promise must have been created using
`napi_create_promise()` and the deferred object returned from that call must
have been retained in order to be passed to this API.

The deferred object is freed upon successful completion.

### `napi_reject_deferred`

Added in: v8.5.0**N-API Version:** 1

```c
napi_status napi_reject_deferred(napi_env env,
                                 napi_deferred deferred,
                                 napi_value rejection);
```

- `[in] env`: The environment that the API is invoked under.
- `[in] deferred`: The deferred object whose associated promise to resolve.
- `[in] rejection`: The value with which to reject the promise.

This API rejects a JavaScript promise by way of the deferred object
with which it is associated. Thus, it can only be used to reject JavaScript
promises for which the corresponding deferred object is available. This
effectively means that the promise must have been created using
`napi_create_promise()` and the deferred object returned from that call must
have been retained in order to be passed to this API.

The deferred object is freed upon successful completion.

### `napi_is_promise`

Added in: v8.5.0**N-API Version:** 1

```c
napi_status napi_is_promise(napi_env env,
                            napi_value value,
                            bool* is_promise);
```

- `[in] env`: The environment that the API is invoked under.
- `[in] value`: The value to examine
- `[out] is_promise`: Flag indicating whether `promise` is a native promise
  object (that is, a promise object created by the underlying engine).

## Script execution

Node-API provides an API for executing a string containing JavaScript using the
underlying JavaScript engine.

### `napi_run_script`

Added in: v8.5.0**N-API Version:** 1

```c
NAPI_EXTERN napi_status napi_run_script(napi_env env,
                                        napi_value script,
                                        napi_value* result);
```

- `[in] env`: The environment that the API is invoked under.
- `[in] script`: A JavaScript string containing the script to execute.
- `[out] result`: The value resulting from having executed the script.

This function executes a string of JavaScript code and returns its result with
the following caveats:

- Unlike `eval`, this function does not allow the script to access the current
  lexical scope, and therefore also does not allow to access the
  [module scope](33-modules.md#the-module-scope), meaning that pseudo-globals such as `require` will not be
  available.
- The script can access the [global scope](26-globals.md). Function and `var` declarations
  in the script will be added to the [`global`](26-globals.md#global) object. Variable declarations
  made using `let` and `const` will be visible globally, but will not be added
  to the [`global`](26-globals.md#global) object.
- The value of `this` is [`global`](26-globals.md#global) within the script.

## libuv event loop

Node-API provides a function for getting the current event loop associated with
a specific `napi_env`.

### `napi_get_uv_event_loop`

Added in: v9.3.0, v8.10.0**N-API Version:** 2

```c
NAPI_EXTERN napi_status napi_get_uv_event_loop(node_api_basic_env env,
                                               struct uv_loop_s** loop);
```

- `[in] env`: The environment that the API is invoked under.
- `[out] loop`: The current libuv loop instance.

Note: While libuv only [guarantees ABI stability](https://github.com/libuv/libuv?tab=readme-ov-file#versioning)
in a major version, its use may result in an addon that does not work across
Node.js major versions.

[ThreadSafeFunction](#asynchronous-thread-safe-function-calls)
is an ABI-stable alternative for many use cases to calling into the
JavaScript thread from another thread.

## Asynchronous thread-safe function calls

JavaScript functions can normally only be called from a native addon's main
thread. If an addon creates additional threads, then Node-API functions that
require a `napi_env`, `napi_value`, or `napi_ref` must not be called from those
threads.

When an addon has additional threads and JavaScript functions need to be invoked
based on the processing completed by those threads, those threads must
communicate with the addon's main thread so that the main thread can invoke the
JavaScript function on their behalf. The thread-safe function APIs provide an
easy way to do this.

These APIs provide the type `napi_threadsafe_function` as well as APIs to
create, destroy, and call objects of this type.
`napi_create_threadsafe_function()` creates a persistent reference to a
`napi_value` that holds a JavaScript function which can be called from multiple
threads. The calls happen asynchronously. This means that values with which the
JavaScript callback is to be called will be placed in a queue, and, for each
value in the queue, a call will eventually be made to the JavaScript function.

Upon creation of a `napi_threadsafe_function` a `napi_finalize` callback can be
provided. This callback will be invoked on the main thread when the thread-safe
function is about to be destroyed. It receives the context and the finalize data
given during construction, and provides an opportunity for cleaning up after the
threads e.g. by calling `uv_thread_join()`. **Aside from the main loop thread,
no threads should be using the thread-safe function after the finalize callback
completes.**

The `context` given during the call to `napi_create_threadsafe_function()` can
be retrieved from any thread with a call to
`napi_get_threadsafe_function_context()`.

### Calling a thread-safe function

`napi_call_threadsafe_function()` can be used for initiating a call into
JavaScript. `napi_call_threadsafe_function()` accepts a parameter which controls
whether the API behaves blockingly. If set to `napi_tsfn_nonblocking`, the API
behaves non-blockingly, returning `napi_queue_full` if the queue was full,
preventing data from being successfully added to the queue. If set to
`napi_tsfn_blocking`, the API blocks until space becomes available in the queue.
`napi_call_threadsafe_function()` never blocks if the thread-safe function was
created with a maximum queue size of 0.

`napi_call_threadsafe_function()` should not be called with `napi_tsfn_blocking`
from a JavaScript thread, because, if the queue is full, it may cause the
JavaScript thread to deadlock.

The actual call into JavaScript is controlled by the callback given via the
`call_js_cb` parameter. `call_js_cb` is invoked on the main thread once for each
value that was placed into the queue by a successful call to
`napi_call_threadsafe_function()`. If such a callback is not given, a default
callback will be used, and the resulting JavaScript call will have no arguments.
The `call_js_cb` callback receives the JavaScript function to call as a
`napi_value` in its parameters, as well as the `void*` context pointer used when
creating the `napi_threadsafe_function`, and the next data pointer that was
created by one of the secondary threads. The callback can then use an API such
as `napi_call_function()` to call into JavaScript.

The callback may also be invoked with `env` and `call_js_cb` both set to `NULL`
to indicate that calls into JavaScript are no longer possible, while items
remain in the queue that may need to be freed. This normally occurs when the
Node.js process exits while there is a thread-safe function still active.

It is not necessary to call into JavaScript via `napi_make_callback()` because
Node-API runs `call_js_cb` in a context appropriate for callbacks.

Zero or more queued items may be invoked in each tick of the event loop.
Applications should not depend on a specific behavior other than progress in
invoking callbacks will be made and events will be invoked
as time moves forward.

### Reference counting of thread-safe functions

Threads can be added to and removed from a `napi_threadsafe_function` object
during its existence. Thus, in addition to specifying an initial number of
threads upon creation, `napi_acquire_threadsafe_function` can be called to
indicate that a new thread will start making use of the thread-safe function.
Similarly, `napi_release_threadsafe_function` can be called to indicate that an
existing thread will stop making use of the thread-safe function.

`napi_threadsafe_function` objects are destroyed when every thread which uses
the object has called `napi_release_threadsafe_function()` or has received a
return status of `napi_closing` in response to a call to
`napi_call_threadsafe_function`. The queue is emptied before the
`napi_threadsafe_function` is destroyed. `napi_release_threadsafe_function()`
should be the last API call made in conjunction with a given
`napi_threadsafe_function`, because after the call completes, there is no
guarantee that the `napi_threadsafe_function` is still allocated. For the same
reason, do not use a thread-safe function
after receiving a return value of `napi_closing` in response to a call to
`napi_call_threadsafe_function`. Data associated with the
`napi_threadsafe_function` can be freed in its `napi_finalize` callback which
was passed to `napi_create_threadsafe_function()`. The parameter
`initial_thread_count` of `napi_create_threadsafe_function` marks the initial
number of acquisitions of the thread-safe functions, instead of calling
`napi_acquire_threadsafe_function` multiple times at creation.

Once the number of threads making use of a `napi_threadsafe_function` reaches
zero, no further threads can start making use of it by calling
`napi_acquire_threadsafe_function()`. In fact, all subsequent API calls
associated with it, except `napi_release_threadsafe_function()`, will return an
error value of `napi_closing`.

The thread-safe function can be "aborted" by giving a value of `napi_tsfn_abort`
to `napi_release_threadsafe_function()`. This will cause all subsequent APIs
associated with the thread-safe function except
`napi_release_threadsafe_function()` to return `napi_closing` even before its
reference count reaches zero. In particular, `napi_call_threadsafe_function()`
will return `napi_closing`, thus informing the threads that it is no longer
possible to make asynchronous calls to the thread-safe function. This can be
used as a criterion for terminating the thread. **Upon receiving a return value
of `napi_closing` from `napi_call_threadsafe_function()` a thread must not use
the thread-safe function anymore because it is no longer guaranteed to
be allocated.**

### Deciding whether to keep the process running

Similarly to libuv handles, thread-safe functions can be "referenced" and
"unreferenced". A "referenced" thread-safe function will cause the event loop on
the thread on which it is created to remain alive until the thread-safe function
is destroyed. In contrast, an "unreferenced" thread-safe function will not
prevent the event loop from exiting. The APIs `napi_ref_threadsafe_function` and
`napi_unref_threadsafe_function` exist for this purpose.

Neither does `napi_unref_threadsafe_function` mark the thread-safe functions as
able to be destroyed nor does `napi_ref_threadsafe_function` prevent it from
being destroyed.

### `napi_create_threadsafe_function`

Added in: v10.6.0**N-API Version:** 4

**History**

| Version | Changes |
| --- | --- |
| v12.6.0, v10.17.0 | Made `func` parameter optional with custom `call_js_cb`. |

```c
NAPI_EXTERN napi_status
napi_create_threadsafe_function(napi_env env,
                                napi_value func,
                                napi_value async_resource,
                                napi_value async_resource_name,
                                size_t max_queue_size,
                                size_t initial_thread_count,
                                void* thread_finalize_data,
                                napi_finalize thread_finalize_cb,
                                void* context,
                                napi_threadsafe_function_call_js call_js_cb,
                                napi_threadsafe_function* result);
```

- `[in] env`: The environment that the API is invoked under.
- `[in] func`: An optional JavaScript function to call from another thread. It
  must be provided if `NULL` is passed to `call_js_cb`.
- `[in] async_resource`: An optional object associated with the async work that
  will be passed to possible `async_hooks` [`init` hooks](05-async-hooks.md#initasyncid-type-triggerasyncid-resource).
- `[in] async_resource_name`: A JavaScript string to provide an identifier for
  the kind of resource that is being provided for diagnostic information exposed
  by the `async_hooks` API.
- `[in] max_queue_size`: Maximum size of the queue. `0` for no limit.
- `[in] initial_thread_count`: The initial number of acquisitions, i.e. the
  initial number of threads, including the main thread, which will be making use
  of this function.
- `[in] thread_finalize_data`: Optional data to be passed to `thread_finalize_cb`.
- `[in] thread_finalize_cb`: Optional function to call when the
  `napi_threadsafe_function` is being destroyed.
- `[in] context`: Optional data to attach to the resulting
  `napi_threadsafe_function`.
- `[in] call_js_cb`: Optional callback which calls the JavaScript function in
  response to a call on a different thread. This callback will be called on the
  main thread. If not given, the JavaScript function will be called with no
  parameters and with `undefined` as its `this` value.
  [`napi_threadsafe_function_call_js`](09-n-api-part1.md#napi_threadsafe_function_call_js) provides more details.
- `[out] result`: The asynchronous thread-safe JavaScript function.

**Change History:**

- Version 10 (`NAPI_VERSION` is defined as `10` or higher):

  Uncaught exceptions thrown in `call_js_cb` are handled with the
  [`'uncaughtException'`](43-process-part1.md#event-uncaughtexception) event, instead of being ignored.

### `napi_get_threadsafe_function_context`

Added in: v10.6.0**N-API Version:** 4

```c
NAPI_EXTERN napi_status
napi_get_threadsafe_function_context(napi_threadsafe_function func,
                                     void** result);
```

- `[in] func`: The thread-safe function for which to retrieve the context.
- `[out] result`: The location where to store the context.

This API may be called from any thread which makes use of `func`.

### `napi_call_threadsafe_function`

Added in: v10.6.0**N-API Version:** 4

**History**

| Version | Changes |
| --- | --- |
| v14.5.0 | Support for `napi_would_deadlock` has been reverted. |
| v14.1.0 | Return `napi_would_deadlock` when called with `napi_tsfn_blocking` from the main thread or a worker thread and the queue is full. |

```c
NAPI_EXTERN napi_status
napi_call_threadsafe_function(napi_threadsafe_function func,
                              void* data,
                              napi_threadsafe_function_call_mode is_blocking);
```

- `[in] func`: The asynchronous thread-safe JavaScript function to invoke.
- `[in] data`: Data to send into JavaScript via the callback `call_js_cb`
  provided during the creation of the thread-safe JavaScript function.
- `[in] is_blocking`: Flag whose value can be either `napi_tsfn_blocking` to
  indicate that the call should block if the queue is full or
  `napi_tsfn_nonblocking` to indicate that the call should return immediately
  with a status of `napi_queue_full` whenever the queue is full.

This API should not be called with `napi_tsfn_blocking` from a JavaScript
thread, because, if the queue is full, it may cause the JavaScript thread to
deadlock.

This API will return `napi_closing` if `napi_release_threadsafe_function()` was
called with `abort` set to `napi_tsfn_abort` from any thread. The value is only
added to the queue if the API returns `napi_ok`.

This API may be called from any thread which makes use of `func`.

### `napi_acquire_threadsafe_function`

Added in: v10.6.0**N-API Version:** 4

```c
NAPI_EXTERN napi_status
napi_acquire_threadsafe_function(napi_threadsafe_function func);
```

- `[in] func`: The asynchronous thread-safe JavaScript function to start making
  use of.

A thread should call this API before passing `func` to any other thread-safe
function APIs to indicate that it will be making use of `func`. This prevents
`func` from being destroyed when all other threads have stopped making use of
it.

This API may be called from any thread which will start making use of `func`.

### `napi_release_threadsafe_function`

Added in: v10.6.0**N-API Version:** 4

```c
NAPI_EXTERN napi_status
napi_release_threadsafe_function(napi_threadsafe_function func,
                                 napi_threadsafe_function_release_mode mode);
```

- `[in] func`: The asynchronous thread-safe JavaScript function whose reference
  count to decrement.
- `[in] mode`: Flag whose value can be either `napi_tsfn_release` to indicate
  that the current thread will make no further calls to the thread-safe
  function, or `napi_tsfn_abort` to indicate that in addition to the current
  thread, no other thread should make any further calls to the thread-safe
  function. If set to `napi_tsfn_abort`, further calls to
  `napi_call_threadsafe_function()` will return `napi_closing`, and no further
  values will be placed in the queue.

A thread should call this API when it stops making use of `func`. Passing `func`
to any thread-safe APIs after having called this API has undefined results, as
`func` may have been destroyed.

This API may be called from any thread which will stop making use of `func`.

### `napi_ref_threadsafe_function`

Added in: v10.6.0**N-API Version:** 4

```c
NAPI_EXTERN napi_status
napi_ref_threadsafe_function(node_api_basic_env env, napi_threadsafe_function func);
```

- `[in] env`: The environment that the API is invoked under.
- `[in] func`: The thread-safe function to reference.

This API is used to indicate that the event loop running on the main thread
should not exit until `func` has been destroyed. Similar to [`uv_ref`](https://docs.libuv.org/en/v1.x/handle.html#c.uv_ref) it is
also idempotent.

Neither does `napi_unref_threadsafe_function` mark the thread-safe functions as
able to be destroyed nor does `napi_ref_threadsafe_function` prevent it from
being destroyed. `napi_acquire_threadsafe_function` and
`napi_release_threadsafe_function` are available for that purpose.

This API may only be called from the main thread.

### `napi_unref_threadsafe_function`

Added in: v10.6.0**N-API Version:** 4

```c
NAPI_EXTERN napi_status
napi_unref_threadsafe_function(node_api_basic_env env, napi_threadsafe_function func);
```

- `[in] env`: The environment that the API is invoked under.
- `[in] func`: The thread-safe function to unreference.

This API is used to indicate that the event loop running on the main thread
may exit before `func` is destroyed. Similar to [`uv_unref`](https://docs.libuv.org/en/v1.x/handle.html#c.uv_unref) it is also
idempotent.

This API may only be called from the main thread.

## Miscellaneous utilities

### `node_api_get_module_file_name`

Added in: v15.9.0, v14.18.0, v12.22.0**N-API Version:** 9

```c
NAPI_EXTERN napi_status
node_api_get_module_file_name(node_api_basic_env env, const char** result);
```

- `[in] env`: The environment that the API is invoked under.
- `[out] result`: A URL containing the absolute path of the
  location from which the add-on was loaded. For a file on the local
  file system it will start with `file://`. The string is null-terminated and
  owned by `env` and must thus not be modified or freed.

`result` may be an empty string if the add-on loading process fails to establish
the add-on's file name during loading.

[Contents](../README.md) | [Previous: C/C++ addons with Node-API, part 3 of 4](09-n-api-part3.md) | [Next: C++ embedder API](10-embedding.md)
