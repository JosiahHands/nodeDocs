[Contents](../README.md) | [Previous: C++ addons](08-addons.md) | [Next: C/C++ addons with Node-API, part 2 of 4](09-n-api-part2.md)

Source: [https://nodejs.org/docs/latest/api/n-api.html](https://nodejs.org/docs/latest/api/n-api.html)

Part 1 of 4 of this section.

# Node-API

[Stability: 2](01-documentation.md#stability-index) - Stable

Node-API (formerly N-API) is an API for building native Addons. It is
independent from the underlying JavaScript runtime (for example, V8) and is
maintained as part of Node.js itself. This API will be Application Binary
Interface (ABI) stable across versions of Node.js. It is intended to insulate
addons from changes in the underlying JavaScript engine and allow modules
compiled for one major version to run on later major versions of Node.js without
recompilation. The [ABI Stability](https://nodejs.org/learn/modules/abi-stability) guide provides a more in-depth explanation.

Addons are built/packaged with the same approach/tools outlined in the section
titled [C++ Addons](08-addons.md). The only difference is the set of APIs that are used by
the native code. Instead of using the V8 or [Native Abstractions for Node.js](https://github.com/nodejs/nan)
APIs, the functions available in Node-API are used.

APIs exposed by Node-API are generally used to create and manipulate
JavaScript values. Concepts and operations generally map to ideas specified
in the ECMA-262 Language Specification. The APIs have the following
properties:

- All Node-API calls return a status code of type `napi_status`. This
  status indicates whether the API call succeeded or failed.
- The API's return value is passed via an out parameter.
- All JavaScript values are abstracted behind an opaque type named
  `napi_value`.
- In case of an error status code, additional information can be obtained
  using `napi_get_last_error_info`. More information can be found in the error
  handling section [Error handling](#error-handling).

## Writing addons in various programming languages

Node-API is a C API that ensures ABI stability across Node.js versions
and different compiler levels. With this stability guarantee, it is possible
to write addons in other programming languages on top of Node-API. Refer
to [language and engine bindings](https://github.com/nodejs/abi-stable-node/blob/doc/node-api-engine-bindings.md) for more programming languages and engines
support details.

[`node-addon-api`](https://github.com/nodejs/node-addon-api) is the official C++ binding that provides a more efficient way to
write C++ code that calls Node-API. This wrapper is a header-only library that offers an inlinable C++ API.
Binaries built with `node-addon-api` will depend on the symbols of the Node-API
C-based functions exported by Node.js. The following code snippet is an example
of `node-addon-api`:

```cpp
Object obj = Object::New(env);
obj["foo"] = String::New(env, "bar");
```

The above `node-addon-api` C++ code is equivalent to the following C-based
Node-API code:

```cpp
napi_status status;
napi_value object, string;
status = napi_create_object(env, &object);
if (status != napi_ok) {
  napi_throw_error(env, ...);
  return;
}

status = napi_create_string_utf8(env, "bar", NAPI_AUTO_LENGTH, &string);
if (status != napi_ok) {
  napi_throw_error(env, ...);
  return;
}

status = napi_set_named_property(env, object, "foo", string);
if (status != napi_ok) {
  napi_throw_error(env, ...);
  return;
}
```

The end result is that the addon only uses the exported C APIs. Even though
the addon is written in C++, it still gets the benefits of the ABI stability
provided by the C Node-API.

When using `node-addon-api` instead of the C APIs, start with the API [docs](https://github.com/nodejs/node-addon-api#api-documentation)
for `node-addon-api`.

The [Node-API Resource](https://nodejs.github.io/node-addon-examples/) offers
an excellent orientation and tips for developers just getting started with
Node-API and `node-addon-api`. Additional media resources can be found on the
[Node-API Media](https://github.com/nodejs/abi-stable-node/blob/HEAD/node-api-media.md) page.

## Implications of ABI stability

Although Node-API provides an ABI stability guarantee, other parts of Node.js do
not, and any external libraries used from the addon may not. In particular,
none of the following APIs provide an ABI stability guarantee across major
versions:

- the Node.js C++ APIs available via any of

  ```cpp
  #include <node.h>
  #include <node_buffer.h>
  #include <node_version.h>
  #include <node_object_wrap.h>
  ```
- the libuv APIs which are also included with Node.js and available via

  ```cpp
  #include <uv.h>
  ```
- the V8 API available via

  ```cpp
  #include <v8.h>
  ```

Thus, for an addon to remain ABI-compatible across Node.js major versions, it
must use Node-API exclusively by restricting itself to using

```c
#include <node_api.h>
```

and by checking, for all external libraries that it uses, that the external
library makes ABI stability guarantees similar to Node-API.

### Enum values in ABI stability

All enum data types defined in Node-API should be considered as a fixed size
`int32_t` value. Bit flag enum types should be explicitly documented, and they
work with bit operators like bit-OR (`|`) as a bit value. Unless otherwise
documented, an enum type should be considered to be extensible.

A new enum value will be added at the end of the enum definition. An enum value
will not be removed or renamed.

For an enum type returned from a Node-API function, or provided as an out
parameter of a Node-API function, the value is an integer value and an addon
should handle unknown values. New values are allowed to be introduced without
a version guard. For example, when checking `napi_status` in switch statements,
an addon should include a default branch, as new status codes may be introduced
in newer Node.js versions.

For an enum type used in an in-parameter, the result of passing an unknown
integer value to Node-API functions is undefined unless otherwise documented.
A new value is added with a version guard to indicate the Node-API version in
which it was introduced. For example, `napi_get_all_property_names` can be
extended with new enum value of `napi_key_filter`.

For an enum type used in both in-parameters and out-parameters, new values are
allowed to be introduced without a version guard.

## Building

Unlike modules written in JavaScript, developing and deploying Node.js
native addons using Node-API requires an additional set of tools. Besides the
basic tools required to develop for Node.js, the native addon developer
requires a toolchain that can compile C and C++ code into a binary. In
addition, depending upon how the native addon is deployed, the *user* of
the native addon will also need to have a C/C++ toolchain installed.

For Linux developers, the necessary C/C++ toolchain packages are readily
available. [GCC](https://gcc.gnu.org) is widely used in the Node.js community to build and
test across a variety of platforms. For many developers, the [LLVM](https://llvm.org)
compiler infrastructure is also a good choice.

For Mac developers, [Xcode](https://developer.apple.com/xcode/) offers all the required compiler tools.
However, it is not necessary to install the entire Xcode IDE. The following
command installs the necessary toolchain:

```bash
xcode-select --install
```

For Windows developers, [Visual Studio](https://visualstudio.microsoft.com) offers all the required compiler
tools. However, it is not necessary to install the entire Visual Studio
IDE. The following command installs the necessary toolchain:

```bash
npm install --global windows-build-tools
```

The sections below describe the additional tools available for developing
and deploying Node.js native addons.

### Build tools

Both the tools listed here require that *users* of the native
addon have a C/C++ toolchain installed in order to successfully install
the native addon.

#### node-gyp

[node-gyp](https://github.com/nodejs/node-gyp) is a build system based on the [gyp-next](https://github.com/nodejs/gyp-next) tool and comes bundled with npm.
node-gyp requires that Python be installed.

Historically, node-gyp has been the tool of choice for building native
addons. It has widespread adoption and documentation. However, some
developers have run into limitations in node-gyp.

#### CMake.js

[CMake.js](https://github.com/cmake-js/cmake-js) is an alternative build system based on [CMake](https://cmake.org).

CMake.js is a good choice for projects that already use CMake or for
developers affected by limitations in node-gyp. [`build_with_cmake`](https://github.com/nodejs/node-addon-examples/tree/main/src/8-tooling/build_with_cmake) is an
example of a CMake-based native addon project.

### Uploading precompiled binaries

The three tools listed here permit native addon developers and maintainers
to create and upload binaries to public or private servers. These tools are
typically integrated with CI/CD build systems like [Travis CI](https://travis-ci.org) and
[AppVeyor](https://www.appveyor.com) to build and upload binaries for a variety of platforms and
architectures. These binaries are then available for download by users who
do not need to have a C/C++ toolchain installed.

#### node-pre-gyp

[node-pre-gyp](https://github.com/mapbox/node-pre-gyp) is a tool based on node-gyp that adds the ability to
upload binaries to a server of the developer's choice. node-pre-gyp has
particularly good support for uploading binaries to Amazon S3.

#### prebuild

[prebuild](https://github.com/prebuild/prebuild) is a tool that supports builds using either node-gyp or
CMake.js. Unlike node-pre-gyp which supports a variety of servers, prebuild
uploads binaries only to [GitHub releases](https://help.github.com/en/github/administering-a-repository/about-releases). prebuild is a good choice for
GitHub projects using CMake.js.

#### prebuildify

[prebuildify](https://github.com/prebuild/prebuildify) is a tool based on node-gyp. The advantage of prebuildify is
that the built binaries are bundled with the native addon when it's
uploaded to npm. The binaries are downloaded from npm and are immediately
available to the module user when the native addon is installed.

## Usage

In order to use the Node-API functions, include the file [`node_api.h`](https://github.com/nodejs/node/blob/HEAD/src/node_api.h) which
is located in the src directory in the node development tree:

```c
#include <node_api.h>
```

This will opt into the default `NAPI_VERSION` for the given release of Node.js.
In order to ensure compatibility with specific versions of Node-API, the version
can be specified explicitly when including the header:

```c
#define NAPI_VERSION 3
#include <node_api.h>
```

This restricts the Node-API surface to just the functionality that was available
in the specified (and earlier) versions.

Some of the Node-API surface is experimental and requires explicit opt-in:

```c
#define NAPI_EXPERIMENTAL
#include <node_api.h>
```

In this case the entire API surface, including any experimental APIs, will be
available to the module code.

Occasionally, experimental features are introduced that affect already-released
and stable APIs. These features can be disabled by an opt-out:

```c
#define NAPI_EXPERIMENTAL
#define NODE_API_EXPERIMENTAL_<FEATURE_NAME>_OPT_OUT
#include <node_api.h>
```

where `<FEATURE_NAME>` is the name of an experimental feature that affects both
experimental and stable APIs.

## Node-API version matrix

Up until version 9, Node-API versions were additive and versioned
independently from Node.js. This meant that any version was
an extension to the previous version in that it had all of
the APIs from the previous version with some additions. Each
Node.js version only supported a single Node-API version.
For example v18.15.0 supports only Node-API version 8. ABI stability was
achieved because 8 was a strict superset of all previous versions.

As of version 9, while Node-API versions continue to be versioned
independently, an add-on that ran with Node-API version 9 may need
code updates to run with Node-API version 10. ABI stability
is maintained, however, because Node.js versions that support
Node-API versions higher than 8 will support all versions
between 8 and the highest version they support and will default
to providing the version 8 APIs unless an add-on opts into a
higher Node-API version. This approach provides the flexibility
of better optimizing existing Node-API functions while
maintaining ABI stability. Existing add-ons can continue to run without
recompilation using an earlier version of Node-API. If an add-on
needs functionality from a newer Node-API version, changes to existing
code and recompilation will be needed to use those new functions anyway.

In versions of Node.js that support Node-API version 9 and later, defining
`NAPI_VERSION=X` and using the existing add-on initialization macros
will bake in the requested Node-API version that will be used at runtime
into the add-on. If `NAPI_VERSION` is not set it will default to 8.

This table may not be up to date in older streams, the most up to date
information is in the latest API documentation in:
[Node-API version matrix](09-n-api-part1.md#node-api-version-matrix)

| Node-API version | Supported In |
| --- | --- |
| 10 | v22.14.0+, 23.6.0+ and all later versions |
| 9 | v18.17.0+, 20.3.0+, 21.0.0 and all later versions |
| 8 | v12.22.0+, v14.17.0+, v15.12.0+, 16.0.0 and all later versions |
| 7 | v10.23.0+, v12.19.0+, v14.12.0+, 15.0.0 and all later versions |
| 6 | v10.20.0+, v12.17.0+, 14.0.0 and all later versions |
| 5 | v10.17.0+, v12.11.0+, 13.0.0 and all later versions |
| 4 | v10.16.0+, v11.8.0+, 12.0.0 and all later versions |
| 3 | v6.14.2\*, 8.11.2+, v9.11.0+\*, 10.0.0 and all later versions |
| 2 | v8.10.0+\*, v9.3.0+\*, 10.0.0 and all later versions |
| 1 | v8.6.0+\*\*, v9.0.0+\*, 10.0.0 and all later versions |

\* Node-API was experimental.

\*\* Node.js 8.0.0 included Node-API as experimental. It was released as
Node-API version 1 but continued to evolve until Node.js 8.6.0. The API is
different in versions prior to Node.js 8.6.0. We recommend Node-API version 3 or
later.

Each API documented for Node-API will have a header named `added in:`, and APIs
which are stable will have the additional header `Node-API version:`.
APIs are directly usable when using a Node.js version which supports
the Node-API version shown in `Node-API version:` or higher.
When using a Node.js version that does not support the
`Node-API version:` listed or if there is no `Node-API version:` listed,
then the API will only be available if
`#define NAPI_EXPERIMENTAL` precedes the inclusion of `node_api.h`
or `js_native_api.h`. If an API appears not to be available on
a version of Node.js which is later than the one shown in `added in:` then
this is most likely the reason for the apparent absence.

The Node-APIs associated strictly with accessing ECMAScript features from native
code can be found separately in `js_native_api.h` and `js_native_api_types.h`.
The APIs defined in these headers are included in `node_api.h` and
`node_api_types.h`. The headers are structured in this way in order to allow
implementations of Node-API outside of Node.js. For those implementations the
Node.js specific APIs may not be applicable.

The Node.js-specific parts of an addon can be separated from the code that
exposes the actual functionality to the JavaScript environment so that the
latter may be used with multiple implementations of Node-API. In the example
below, `addon.c` and `addon.h` refer only to `js_native_api.h`. This ensures
that `addon.c` can be reused to compile against either the Node.js
implementation of Node-API or any implementation of Node-API outside of Node.js.

`addon_node.c` is a separate file that contains the Node.js specific entry point
to the addon and which instantiates the addon by calling into `addon.c` when the
addon is loaded into a Node.js environment.

```c
// addon.h
#ifndef _ADDON_H_
#define _ADDON_H_
#include <js_native_api.h>
napi_value create_addon(napi_env env);
#endif  // _ADDON_H_
```

```c
// addon.c
#include "addon.h"

#define NODE_API_CALL(env, call)                                  \
  do {                                                            \
    napi_status status = (call);                                  \
    if (status != napi_ok) {                                      \
      const napi_extended_error_info* error_info = NULL;          \
      napi_get_last_error_info((env), &error_info);               \
      const char* err_message = error_info->error_message;        \
      bool is_pending;                                            \
      napi_is_exception_pending((env), &is_pending);              \
      /* If an exception is already pending, don't rethrow it */  \
      if (!is_pending) {                                          \
        const char* message = (err_message == NULL)               \
            ? "empty error message"                               \
            : err_message;                                        \
        napi_throw_error((env), NULL, message);                   \
      }                                                           \
      return NULL;                                                \
    }                                                             \
  } while(0)

static napi_value
DoSomethingUseful(napi_env env, napi_callback_info info) {
  // Do something useful.
  return NULL;
}

napi_value create_addon(napi_env env) {
  napi_value result;
  NODE_API_CALL(env, napi_create_object(env, &result));

  napi_value exported_function;
  NODE_API_CALL(env, napi_create_function(env,
                                          "doSomethingUseful",
                                          NAPI_AUTO_LENGTH,
                                          DoSomethingUseful,
                                          NULL,
                                          &exported_function));

  NODE_API_CALL(env, napi_set_named_property(env,
                                             result,
                                             "doSomethingUseful",
                                             exported_function));

  return result;
}
```

```c
// addon_node.c
#include <node_api.h>
#include "addon.h"

NAPI_MODULE_INIT(/* napi_env env, napi_value exports */) {
  // This function body is expected to return a `napi_value`.
  // The variables `napi_env env` and `napi_value exports` may be used within
  // the body, as they are provided by the definition of `NAPI_MODULE_INIT()`.
  return create_addon(env);
}
```

## Environment life cycle APIs

[Section Agents](https://tc39.es/ecma262/#sec-agents) of the [ECMAScript Language Specification](https://tc39.es/ecma262/) defines the concept
of an "Agent" as a self-contained environment in which JavaScript code runs.
Multiple such Agents may be started and terminated either concurrently or in
sequence by the process.

A Node.js environment corresponds to an ECMAScript Agent. In the main process,
an environment is created at startup, and additional environments can be created
on separate threads to serve as [worker threads](67-worker-threads.md). When Node.js is embedded in
another application, the main thread of the application may also construct and
destroy a Node.js environment multiple times during the life cycle of the
application process such that each Node.js environment created by the
application may, in turn, during its life cycle create and destroy additional
environments as worker threads.

From the perspective of a native addon this means that the bindings it provides
may be called multiple times, from multiple contexts, and even concurrently from
multiple threads.

Native addons may need to allocate global state which they use during
their life cycle of an Node.js environment such that the state can be
unique to each instance of the addon.

To this end, Node-API provides a way to associate data such that its life cycle
is tied to the life cycle of a Node.js environment.

### `napi_set_instance_data`

Added in: v12.8.0, v10.20.0**N-API Version:** 6

```c
napi_status napi_set_instance_data(node_api_basic_env env,
                                   void* data,
                                   napi_finalize finalize_cb,
                                   void* finalize_hint);
```

- `[in] env`: The environment that the Node-API call is invoked under.
- `[in] data`: The data item to make available to bindings of this instance.
- `[in] finalize_cb`: The function to call when the environment is being torn
  down. The function receives `data` so that it might free it.
  [`napi_finalize`](#napi_finalize) provides more details.
- `[in] finalize_hint`: Optional hint to pass to the finalize callback during
  collection.

Returns `napi_ok` if the API succeeded.

This API associates `data` with the currently running Node.js environment. `data`
can later be retrieved using `napi_get_instance_data()`. Any existing data
associated with the currently running Node.js environment which was set by means
of a previous call to `napi_set_instance_data()` will be overwritten. If a
`finalize_cb` was provided by the previous call, it will not be called.

### `napi_get_instance_data`

Added in: v12.8.0, v10.20.0**N-API Version:** 6

```c
napi_status napi_get_instance_data(node_api_basic_env env,
                                   void** data);
```

- `[in] env`: The environment that the Node-API call is invoked under.
- `[out] data`: The data item that was previously associated with the currently
  running Node.js environment by a call to `napi_set_instance_data()`.

Returns `napi_ok` if the API succeeded.

This API retrieves data that was previously associated with the currently
running Node.js environment via `napi_set_instance_data()`. If no data is set,
the call will succeed and `data` will be set to `NULL`.

## Basic Node-API data types

Node-API exposes the following fundamental data types as abstractions that are
consumed by the various APIs. These APIs should be treated as opaque,
introspectable only with other Node-API calls.

### `napi_status`

Added in: v8.0.0**N-API Version:** 1

Integral status code indicating the success or failure of a Node-API call.
Currently, the following status codes are supported.

```c
typedef enum {
  napi_ok,
  napi_invalid_arg,
  napi_object_expected,
  napi_string_expected,
  napi_name_expected,
  napi_function_expected,
  napi_number_expected,
  napi_boolean_expected,
  napi_array_expected,
  napi_generic_failure,
  napi_pending_exception,
  napi_cancelled,
  napi_escape_called_twice,
  napi_handle_scope_mismatch,
  napi_callback_scope_mismatch,
  napi_queue_full,
  napi_closing,
  napi_bigint_expected,
  napi_date_expected,
  napi_arraybuffer_expected,
  napi_detachable_arraybuffer_expected,
  napi_would_deadlock,  /* unused */
  napi_no_external_buffers_allowed,
  napi_cannot_run_js
} napi_status;
```

If additional information is required upon an API returning a failed status,
it can be obtained by calling `napi_get_last_error_info`.

### `napi_extended_error_info`

Added in: v8.0.0**N-API Version:** 1

```c
typedef struct {
  const char* error_message;
  void* engine_reserved;
  uint32_t engine_error_code;
  napi_status error_code;
} napi_extended_error_info;
```

- `error_message`: UTF8-encoded string containing a VM-neutral description of
  the error.
- `engine_reserved`: Reserved for VM-specific error details. This is currently
  not implemented for any VM.
- `engine_error_code`: VM-specific error code. This is currently
  not implemented for any VM.
- `error_code`: The Node-API status code that originated with the last error.

See the [Error handling](#error-handling) section for additional information.

### `napi_env`

`napi_env` is used to represent a context that the underlying Node-API
implementation can use to persist VM-specific state. This structure is passed
to native functions when they're invoked, and it must be passed back when
making Node-API calls. Specifically, the same `napi_env` that was passed in when
the initial native function was called must be passed to any subsequent
nested Node-API calls. Caching the `napi_env` for the purpose of general reuse,
and passing the `napi_env` between instances of the same addon running on
different [`Worker`](67-worker-threads.md#class-worker) threads is not allowed. The `napi_env` becomes invalid
when an instance of a native addon is unloaded. Notification of this event is
delivered through the callbacks given to [`napi_add_env_cleanup_hook`](09-n-api-part2.md#napi_add_env_cleanup_hook) and
[`napi_set_instance_data`](#napi_set_instance_data).

### `node_api_basic_env`

Stability: 1 - Experimental

This variant of `napi_env` is passed to synchronous finalizers
([`node_api_basic_finalize`](#node_api_basic_finalize)). There is a subset of Node-APIs which accept
a parameter of type `node_api_basic_env` as their first argument. These APIs do
not access the state of the JavaScript engine and are thus safe to call from
synchronous finalizers. Passing a parameter of type `napi_env` to these APIs is
allowed, however, passing a parameter of type `node_api_basic_env` to APIs that
access the JavaScript engine state is not allowed. Attempting to do so without
a cast will produce a compiler warning or an error when add-ons are compiled
with flags which cause them to emit warnings and/or errors when incorrect
pointer types are passed into a function. Calling such APIs from a synchronous
finalizer will ultimately result in the termination of the application.

### `napi_value`

This is an opaque pointer that is used to represent a JavaScript value.

### `napi_threadsafe_function`

Added in: v10.6.0**N-API Version:** 4

This is an opaque pointer that represents a JavaScript function which can be
called asynchronously from multiple threads via
`napi_call_threadsafe_function()`.

### `napi_threadsafe_function_release_mode`

Added in: v10.6.0**N-API Version:** 4

A value to be given to `napi_release_threadsafe_function()` to indicate whether
the thread-safe function is to be closed immediately (`napi_tsfn_abort`) or
merely released (`napi_tsfn_release`) and thus available for subsequent use via
`napi_acquire_threadsafe_function()` and `napi_call_threadsafe_function()`.

```c
typedef enum {
  napi_tsfn_release,
  napi_tsfn_abort
} napi_threadsafe_function_release_mode;
```

### `napi_threadsafe_function_call_mode`

Added in: v10.6.0**N-API Version:** 4

A value to be given to `napi_call_threadsafe_function()` to indicate whether
the call should block whenever the queue associated with the thread-safe
function is full.

```c
typedef enum {
  napi_tsfn_nonblocking,
  napi_tsfn_blocking
} napi_threadsafe_function_call_mode;
```

### Node-API memory management types

#### `napi_handle_scope`

This is an abstraction used to control and modify the lifetime of objects
created within a particular scope. In general, Node-API values are created
within the context of a handle scope. When a native method is called from
JavaScript, a default handle scope will exist. If the user does not explicitly
create a new handle scope, Node-API values will be created in the default handle
scope. For any invocations of code outside the execution of a native method
(for instance, during a libuv callback invocation), the module is required to
create a scope before invoking any functions that can result in the creation
of JavaScript values.

Handle scopes are created using [`napi_open_handle_scope`](#napi_open_handle_scope) and are destroyed
using [`napi_close_handle_scope`](#napi_close_handle_scope). Closing the scope can indicate to the GC
that all `napi_value`s created during the lifetime of the handle scope are no
longer referenced from the current stack frame.

For more details, review the [Object lifetime management](#object-lifetime-management).

#### `napi_escapable_handle_scope`

Added in: v8.0.0**N-API Version:** 1

Escapable handle scopes are a special type of handle scope to return values
created within a particular handle scope to a parent scope.

#### `napi_ref`

Added in: v8.0.0**N-API Version:** 1

This is the abstraction to use to reference a `napi_value`. This allows for
users to manage the lifetimes of JavaScript values, including defining their
minimum lifetimes explicitly.

For more details, review the [Object lifetime management](#object-lifetime-management).

#### `napi_type_tag`

Added in: v14.8.0, v12.19.0**N-API Version:** 8

A 128-bit value stored as two unsigned 64-bit integers. It serves as a UUID
with which JavaScript objects or [externals](09-n-api-part2.md#napi_create_external) can be "tagged" in order to
ensure that they are of a certain type. This is a stronger check than
[`napi_instanceof`](09-n-api-part3.md#napi_instanceof), because the latter can report a false positive if the
object's prototype has been manipulated. Type-tagging is most useful in
conjunction with [`napi_wrap`](09-n-api-part4.md#napi_wrap) because it ensures that the pointer retrieved
from a wrapped object can be safely cast to the native type corresponding to the
type tag that had been previously applied to the JavaScript object.

```c
typedef struct {
  uint64_t lower;
  uint64_t upper;
} napi_type_tag;
```

#### `napi_async_cleanup_hook_handle`

Added in: v14.10.0, v12.19.0

An opaque value returned by [`napi_add_async_cleanup_hook`](09-n-api-part2.md#napi_add_async_cleanup_hook). It must be passed
to [`napi_remove_async_cleanup_hook`](09-n-api-part2.md#napi_remove_async_cleanup_hook) when the chain of asynchronous cleanup
events completes.

### Node-API callback types

#### `napi_callback_info`

Added in: v8.0.0**N-API Version:** 1

Opaque datatype that is passed to a callback function. It can be used for
getting additional information about the context in which the callback was
invoked.

#### `napi_callback`

Added in: v8.0.0**N-API Version:** 1

Function pointer type for user-provided native functions which are to be
exposed to JavaScript via Node-API. Callback functions should satisfy the
following signature:

```c
typedef napi_value (*napi_callback)(napi_env, napi_callback_info);
```

Unless for reasons discussed in [Object Lifetime Management](#object-lifetime-management), creating a
handle and/or callback scope inside a `napi_callback` is not necessary.

#### `node_api_basic_finalize`

Added in: v21.6.0, v20.12.0, v18.20.0

Stability: 1 - Experimental

Function pointer type for add-on provided functions that allow the user to be
notified when externally-owned data is ready to be cleaned up because the
object it was associated with has been garbage-collected. The user must provide
a function satisfying the following signature which would get called upon the
object's collection. Currently, `node_api_basic_finalize` can be used for
finding out when objects that have external data are collected.

```c
typedef void (*node_api_basic_finalize)(node_api_basic_env env,
                                      void* finalize_data,
                                      void* finalize_hint);
```

Unless for reasons discussed in [Object Lifetime Management](#object-lifetime-management), creating a
handle and/or callback scope inside the function body is not necessary.

Since these functions may be called while the JavaScript engine is in a state
where it cannot execute JavaScript code, only Node-APIs which accept a
`node_api_basic_env` as their first parameter may be called.
[`node_api_post_finalizer`](09-n-api-part4.md#node_api_post_finalizer) can be used to schedule Node-API calls that
require access to the JavaScript engine's state to run after the current
garbage collection cycle has completed.

In the case of [`node_api_create_external_string_latin1`](09-n-api-part2.md#node_api_create_external_string_latin1) and
[`node_api_create_external_string_utf16`](09-n-api-part2.md#node_api_create_external_string_utf16) the `env` parameter may be null,
because external strings can be collected during the latter part of environment
shutdown.

Change History:

- experimental (`NAPI_EXPERIMENTAL`):

  Only Node-API calls that accept a `node_api_basic_env` as their first
  parameter may be called, otherwise the application will be terminated with an
  appropriate error message. This feature can be turned off by defining
  `NODE_API_EXPERIMENTAL_BASIC_ENV_OPT_OUT`.

#### `napi_finalize`

Added in: v8.0.0**N-API Version:** 1

Function pointer type for add-on provided function that allow the user to
schedule a group of calls to Node-APIs in response to a garbage collection
event, after the garbage collection cycle has completed. These function
pointers can be used with [`node_api_post_finalizer`](09-n-api-part4.md#node_api_post_finalizer).

```c
typedef void (*napi_finalize)(napi_env env,
                              void* finalize_data,
                              void* finalize_hint);
```

Change History:

- experimental (`NAPI_EXPERIMENTAL` is defined):

  A function of this type may no longer be used as a finalizer, except with
  [`node_api_post_finalizer`](09-n-api-part4.md#node_api_post_finalizer). [`node_api_basic_finalize`](#node_api_basic_finalize) must be used
  instead. This feature can be turned off by defining
  `NODE_API_EXPERIMENTAL_BASIC_ENV_OPT_OUT`.

#### `napi_async_execute_callback`

Added in: v8.0.0**N-API Version:** 1

Function pointer used with functions that support asynchronous
operations. Callback functions must satisfy the following signature:

```c
typedef void (*napi_async_execute_callback)(napi_env env, void* data);
```

Implementations of this function must avoid making Node-API calls that execute
JavaScript or interact with JavaScript objects. Node-API calls should be in the
`napi_async_complete_callback` instead. Do not use the `napi_env` parameter as
it will likely result in execution of JavaScript.

#### `napi_async_complete_callback`

Added in: v8.0.0**N-API Version:** 1

Function pointer used with functions that support asynchronous
operations. Callback functions must satisfy the following signature:

```c
typedef void (*napi_async_complete_callback)(napi_env env,
                                             napi_status status,
                                             void* data);
```

Unless for reasons discussed in [Object Lifetime Management](#object-lifetime-management), creating a
handle and/or callback scope inside the function body is not necessary.

#### `napi_threadsafe_function_call_js`

Added in: v10.6.0**N-API Version:** 4

Function pointer used with asynchronous thread-safe function calls. The callback
will be called on the main thread. Its purpose is to use a data item arriving
via the queue from one of the secondary threads to construct the parameters
necessary for a call into JavaScript, usually via `napi_call_function`, and then
make the call into JavaScript.

The data arriving from the secondary thread via the queue is given in the `data`
parameter and the JavaScript function to call is given in the `js_callback`
parameter.

Node-API sets up the environment prior to calling this callback, so it is
sufficient to call the JavaScript function via `napi_call_function` rather than
via `napi_make_callback`.

Callback functions must satisfy the following signature:

```c
typedef void (*napi_threadsafe_function_call_js)(napi_env env,
                                                 napi_value js_callback,
                                                 void* context,
                                                 void* data);
```

- `[in] env`: The environment to use for API calls, or `NULL` if the thread-safe
  function is being torn down and `data` may need to be freed.
- `[in] js_callback`: The JavaScript function to call, or `NULL` if the
  thread-safe function is being torn down and `data` may need to be freed. It
  may also be `NULL` if the thread-safe function was created without
  `js_callback`.
- `[in] context`: The optional data with which the thread-safe function was
  created.
- `[in] data`: Data created by the secondary thread. It is the responsibility of
  the callback to convert this native data to JavaScript values (with Node-API
  functions) that can be passed as parameters when `js_callback` is invoked.
  This pointer is managed entirely by the threads and this callback. Thus this
  callback should free the data.

Unless for reasons discussed in [Object Lifetime Management](#object-lifetime-management), creating a
handle and/or callback scope inside the function body is not necessary.

#### `napi_cleanup_hook`

Added in: v19.2.0, v18.13.0**N-API Version:** 3

Function pointer used with [`napi_add_env_cleanup_hook`](09-n-api-part2.md#napi_add_env_cleanup_hook). It will be called
when the environment is being torn down.

Callback functions must satisfy the following signature:

```c
typedef void (*napi_cleanup_hook)(void* data);
```

- `[in] data`: The data that was passed to [`napi_add_env_cleanup_hook`](09-n-api-part2.md#napi_add_env_cleanup_hook).

#### `napi_async_cleanup_hook`

Added in: v14.10.0, v12.19.0

Function pointer used with [`napi_add_async_cleanup_hook`](09-n-api-part2.md#napi_add_async_cleanup_hook). It will be called
when the environment is being torn down.

Callback functions must satisfy the following signature:

```c
typedef void (*napi_async_cleanup_hook)(napi_async_cleanup_hook_handle handle,
                                        void* data);
```

- `[in] handle`: The handle that must be passed to
  [`napi_remove_async_cleanup_hook`](09-n-api-part2.md#napi_remove_async_cleanup_hook) after completion of the asynchronous
  cleanup.
- `[in] data`: The data that was passed to [`napi_add_async_cleanup_hook`](09-n-api-part2.md#napi_add_async_cleanup_hook).

The body of the function should initiate the asynchronous cleanup actions at the
end of which `handle` must be passed in a call to
[`napi_remove_async_cleanup_hook`](09-n-api-part2.md#napi_remove_async_cleanup_hook).

## Error handling

Node-API uses both return values and JavaScript exceptions for error handling.
The following sections explain the approach for each case.

### Return values

Added in: v8.0.0**N-API Version:** 1

All of the Node-API functions share the same error handling pattern. The
return type of all API functions is `napi_status`.

The return value will be `napi_ok` if the request was successful and
no uncaught JavaScript exception was thrown. If an error occurred AND
an exception was thrown, the `napi_status` value for the error
will be returned. If an exception was thrown, and no error occurred,
`napi_pending_exception` will be returned.

In cases where a return value other than `napi_ok` or
`napi_pending_exception` is returned, [`napi_is_exception_pending`](#napi_is_exception_pending)
must be called to check if an exception is pending.
See the section on exceptions for more details.

The full set of possible `napi_status` values is defined
in `napi_api_types.h`.

The `napi_status` return value provides a VM-independent representation of
the error which occurred. In some cases it is useful to be able to get
more detailed information, including a string representing the error as well as
VM (engine)-specific information.

In order to retrieve this information [`napi_get_last_error_info`](#napi_get_last_error_info)
is provided which returns a `napi_extended_error_info` structure.
The format of the `napi_extended_error_info` structure is as follows:

```c
typedef struct napi_extended_error_info {
  const char* error_message;
  void* engine_reserved;
  uint32_t engine_error_code;
  napi_status error_code;
};
```

- `error_message`: Textual representation of the error that occurred.
- `engine_reserved`: Opaque handle reserved for engine use only.
- `engine_error_code`: VM specific error code.
- `error_code`: Node-API status code for the last error.

[`napi_get_last_error_info`](#napi_get_last_error_info) returns the information for the last
Node-API call that was made.

Do not rely on the content or format of any of the extended information as it
is not subject to SemVer and may change at any time. It is intended only for
logging purposes.

#### `napi_get_last_error_info`

Added in: v8.0.0**N-API Version:** 1

```c
napi_status
napi_get_last_error_info(node_api_basic_env env,
                         const napi_extended_error_info** result);
```

- `[in] env`: The environment that the API is invoked under.
- `[out] result`: The `napi_extended_error_info` structure with more
  information about the error.

Returns `napi_ok` if the API succeeded.

This API retrieves a `napi_extended_error_info` structure with information
about the last error that occurred.

The content of the `napi_extended_error_info` returned is only valid up until
a Node-API function is called on the same `env`. This includes a call to
`napi_is_exception_pending` so it may often be necessary to make a copy
of the information so that it can be used later. The pointer returned
in `error_message` points to a statically-defined string so it is safe to use
that pointer if you have copied it out of the `error_message` field (which will
be overwritten) before another Node-API function was called.

Do not rely on the content or format of any of the extended information as it
is not subject to SemVer and may change at any time. It is intended only for
logging purposes.

This API can be called even if there is a pending JavaScript exception.

### Exceptions

Any Node-API function call may result in a pending JavaScript exception. This is
the case for any of the API functions, even those that may not cause the
execution of JavaScript.

If the `napi_status` returned by a function is `napi_ok` then no
exception is pending and no additional action is required. If the
`napi_status` returned is anything other than `napi_ok` or
`napi_pending_exception`, in order to try to recover and continue
instead of simply returning immediately, [`napi_is_exception_pending`](#napi_is_exception_pending)
must be called in order to determine if an exception is pending or not.

In many cases when a Node-API function is called and an exception is
already pending, the function will return immediately with a
`napi_status` of `napi_pending_exception`. However, this is not the case
for all functions. Node-API allows a subset of the functions to be
called to allow for some minimal cleanup before returning to JavaScript.
In that case, `napi_status` will reflect the status for the function. It
will not reflect previous pending exceptions. To avoid confusion, check
the error status after every function call.

When an exception is pending one of two approaches can be employed.

The first approach is to do any appropriate cleanup and then return so that
execution will return to JavaScript. As part of the transition back to
JavaScript, the exception will be thrown at the point in the JavaScript
code where the native method was invoked. The behavior of most Node-API calls
is unspecified while an exception is pending, and many will simply return
`napi_pending_exception`, so do as little as possible and then return to
JavaScript where the exception can be handled.

The second approach is to try to handle the exception. There will be cases
where the native code can catch the exception, take the appropriate action,
and then continue. This is only recommended in specific cases
where it is known that the exception can be safely handled. In these
cases [`napi_get_and_clear_last_exception`](#napi_get_and_clear_last_exception) can be used to get and
clear the exception. On success, result will contain the handle to
the last JavaScript `Object` thrown. If it is determined, after
retrieving the exception, the exception cannot be handled after all
it can be re-thrown it with [`napi_throw`](#napi_throw) where error is the
JavaScript value to be thrown.

The following utility functions are also available in case native code
needs to throw an exception or determine if a `napi_value` is an instance
of a JavaScript `Error` object: [`napi_throw_error`](#napi_throw_error),
[`napi_throw_type_error`](#napi_throw_type_error), [`napi_throw_range_error`](#napi_throw_range_error), [`node_api_throw_syntax_error`](#node_api_throw_syntax_error) and [`napi_is_error`](#napi_is_error).

The following utility functions are also available in case native
code needs to create an `Error` object: [`napi_create_error`](#napi_create_error),
[`napi_create_type_error`](#napi_create_type_error), [`napi_create_range_error`](#napi_create_range_error) and [`node_api_create_syntax_error`](#node_api_create_syntax_error),
where result is the `napi_value` that refers to the newly created
JavaScript `Error` object.

The Node.js project is adding error codes to all of the errors
generated internally. The goal is for applications to use these
error codes for all error checking. The associated error messages
will remain, but will only be meant to be used for logging and
display with the expectation that the message can change without
SemVer applying. In order to support this model with Node-API, both
in internal functionality and for module specific functionality
(as its good practice), the `throw_` and `create_` functions
take an optional code parameter which is the string for the code
to be added to the error object. If the optional parameter is `NULL`
then no code will be associated with the error. If a code is provided,
the name associated with the error is also updated to be:

```text
originalName [code]
```

where `originalName` is the original name associated with the error
and `code` is the code that was provided. For example, if the code
is `'ERR_ERROR_1'` and a `TypeError` is being created the name will be:

```text
TypeError [ERR_ERROR_1]
```

#### `napi_throw`

Added in: v8.0.0**N-API Version:** 1

```c
NAPI_EXTERN napi_status napi_throw(napi_env env, napi_value error);
```

- `[in] env`: The environment that the API is invoked under.
- `[in] error`: The JavaScript value to be thrown.

Returns `napi_ok` if the API succeeded.

This API throws the JavaScript value provided.

#### `napi_throw_error`

Added in: v8.0.0**N-API Version:** 1

```c
NAPI_EXTERN napi_status napi_throw_error(napi_env env,
                                         const char* code,
                                         const char* msg);
```

- `[in] env`: The environment that the API is invoked under.
- `[in] code`: Optional error code to be set on the error.
- `[in] msg`: C string representing the text to be associated with the error.

Returns `napi_ok` if the API succeeded.

This API throws a JavaScript `Error` with the text provided.

#### `napi_throw_type_error`

Added in: v8.0.0**N-API Version:** 1

```c
NAPI_EXTERN napi_status napi_throw_type_error(napi_env env,
                                              const char* code,
                                              const char* msg);
```

- `[in] env`: The environment that the API is invoked under.
- `[in] code`: Optional error code to be set on the error.
- `[in] msg`: C string representing the text to be associated with the error.

Returns `napi_ok` if the API succeeded.

This API throws a JavaScript `TypeError` with the text provided.

#### `napi_throw_range_error`

Added in: v8.0.0**N-API Version:** 1

```c
NAPI_EXTERN napi_status napi_throw_range_error(napi_env env,
                                               const char* code,
                                               const char* msg);
```

- `[in] env`: The environment that the API is invoked under.
- `[in] code`: Optional error code to be set on the error.
- `[in] msg`: C string representing the text to be associated with the error.

Returns `napi_ok` if the API succeeded.

This API throws a JavaScript `RangeError` with the text provided.

#### `node_api_throw_syntax_error`

Added in: v17.2.0, v16.14.0**N-API Version:** 9

```c
NAPI_EXTERN napi_status node_api_throw_syntax_error(napi_env env,
                                                    const char* code,
                                                    const char* msg);
```

- `[in] env`: The environment that the API is invoked under.
- `[in] code`: Optional error code to be set on the error.
- `[in] msg`: C string representing the text to be associated with the error.

Returns `napi_ok` if the API succeeded.

This API throws a JavaScript `SyntaxError` with the text provided.

#### `napi_is_error`

Added in: v8.0.0**N-API Version:** 1

```c
NAPI_EXTERN napi_status napi_is_error(napi_env env,
                                      napi_value value,
                                      bool* result);
```

- `[in] env`: The environment that the API is invoked under.
- `[in] value`: The `napi_value` to be checked.
- `[out] result`: Boolean value that is set to true if `napi_value` represents
  an error, false otherwise.

Returns `napi_ok` if the API succeeded.

This API queries a `napi_value` to check if it represents an error object.

#### `napi_create_error`

Added in: v8.0.0**N-API Version:** 1

```c
NAPI_EXTERN napi_status napi_create_error(napi_env env,
                                          napi_value code,
                                          napi_value msg,
                                          napi_value* result);
```

- `[in] env`: The environment that the API is invoked under.
- `[in] code`: Optional `napi_value` with the string for the error code to be
  associated with the error.
- `[in] msg`: `napi_value` that references a JavaScript `string` to be used as
  the message for the `Error`.
- `[out] result`: `napi_value` representing the error created.

Returns `napi_ok` if the API succeeded.

This API returns a JavaScript `Error` with the text provided.

#### `napi_create_type_error`

Added in: v8.0.0**N-API Version:** 1

```c
NAPI_EXTERN napi_status napi_create_type_error(napi_env env,
                                               napi_value code,
                                               napi_value msg,
                                               napi_value* result);
```

- `[in] env`: The environment that the API is invoked under.
- `[in] code`: Optional `napi_value` with the string for the error code to be
  associated with the error.
- `[in] msg`: `napi_value` that references a JavaScript `string` to be used as
  the message for the `Error`.
- `[out] result`: `napi_value` representing the error created.

Returns `napi_ok` if the API succeeded.

This API returns a JavaScript `TypeError` with the text provided.

#### `napi_create_range_error`

Added in: v8.0.0**N-API Version:** 1

```c
NAPI_EXTERN napi_status napi_create_range_error(napi_env env,
                                                napi_value code,
                                                napi_value msg,
                                                napi_value* result);
```

- `[in] env`: The environment that the API is invoked under.
- `[in] code`: Optional `napi_value` with the string for the error code to be
  associated with the error.
- `[in] msg`: `napi_value` that references a JavaScript `string` to be used as
  the message for the `Error`.
- `[out] result`: `napi_value` representing the error created.

Returns `napi_ok` if the API succeeded.

This API returns a JavaScript `RangeError` with the text provided.

#### `node_api_create_syntax_error`

Added in: v17.2.0, v16.14.0**N-API Version:** 9

```c
NAPI_EXTERN napi_status node_api_create_syntax_error(napi_env env,
                                                     napi_value code,
                                                     napi_value msg,
                                                     napi_value* result);
```

- `[in] env`: The environment that the API is invoked under.
- `[in] code`: Optional `napi_value` with the string for the error code to be
  associated with the error.
- `[in] msg`: `napi_value` that references a JavaScript `string` to be used as
  the message for the `Error`.
- `[out] result`: `napi_value` representing the error created.

Returns `napi_ok` if the API succeeded.

This API returns a JavaScript `SyntaxError` with the text provided.

#### `napi_get_and_clear_last_exception`

Added in: v8.0.0**N-API Version:** 1

```c
napi_status napi_get_and_clear_last_exception(napi_env env,
                                              napi_value* result);
```

- `[in] env`: The environment that the API is invoked under.
- `[out] result`: The exception if one is pending, `NULL` otherwise.

Returns `napi_ok` if the API succeeded.

This API can be called even if there is a pending JavaScript exception.

#### `napi_is_exception_pending`

Added in: v8.0.0**N-API Version:** 1

```c
napi_status napi_is_exception_pending(napi_env env, bool* result);
```

- `[in] env`: The environment that the API is invoked under.
- `[out] result`: Boolean value that is set to true if an exception is pending.

Returns `napi_ok` if the API succeeded.

This API can be called even if there is a pending JavaScript exception.

#### `napi_fatal_exception`

Added in: v9.10.0**N-API Version:** 3

```c
napi_status napi_fatal_exception(napi_env env, napi_value err);
```

- `[in] env`: The environment that the API is invoked under.
- `[in] err`: The error that is passed to `'uncaughtException'`.

Trigger an `'uncaughtException'` in JavaScript. Useful if an async
callback throws an exception with no way to recover.

### Fatal errors

In the event of an unrecoverable error in a native addon, a fatal error can be
thrown to immediately terminate the process.

#### `napi_fatal_error`

Added in: v8.2.0**N-API Version:** 1

```c
NAPI_NO_RETURN void napi_fatal_error(const char* location,
                                     size_t location_len,
                                     const char* message,
                                     size_t message_len);
```

- `[in] location`: Optional location at which the error occurred.
- `[in] location_len`: The length of the location in bytes, or
  `NAPI_AUTO_LENGTH` if it is null-terminated.
- `[in] message`: The message associated with the error.
- `[in] message_len`: The length of the message in bytes, or `NAPI_AUTO_LENGTH`
  if it is null-terminated.

The function call does not return, the process will be terminated.

This API can be called even if there is a pending JavaScript exception.

## Object lifetime management

As Node-API calls are made, handles to objects in the heap for the underlying
VM may be returned as `napi_values`. These handles must hold the
objects 'live' until they are no longer required by the native code,
otherwise the objects could be collected before the native code was
finished using them.

As object handles are returned they are associated with a
'scope'. The lifespan for the default scope is tied to the lifespan
of the native method call. The result is that, by default, handles
remain valid and the objects associated with these handles will be
held live for the lifespan of the native method call.

In many cases, however, it is necessary that the handles remain valid for
either a shorter or longer lifespan than that of the native method.
The sections which follow describe the Node-API functions that can be used
to change the handle lifespan from the default.

### Making handle lifespan shorter than that of the native method

It is often necessary to make the lifespan of handles shorter than
the lifespan of a native method. For example, consider a native method
that has a loop which iterates through the elements in a large array:

```c
for (int i = 0; i < 1000000; i++) {
  napi_value result;
  napi_status status = napi_get_element(env, object, i, &result);
  if (status != napi_ok) {
    break;
  }
  // do something with element
}
```

This would result in a large number of handles being created, consuming
substantial resources. In addition, even though the native code could only
use the most recent handle, all of the associated objects would also be
kept alive since they all share the same scope.

To handle this case, Node-API provides the ability to establish a new 'scope' to
which newly created handles will be associated. Once those handles
are no longer required, the scope can be 'closed' and any handles associated
with the scope are invalidated. The methods available to open/close scopes are
[`napi_open_handle_scope`](#napi_open_handle_scope) and [`napi_close_handle_scope`](#napi_close_handle_scope).

Node-API only supports a single nested hierarchy of scopes. There is only one
active scope at any time, and all new handles will be associated with that
scope while it is active. Scopes must be closed in the reverse order from
which they are opened. In addition, all scopes created within a native method
must be closed before returning from that method.

Taking the earlier example, adding calls to [`napi_open_handle_scope`](#napi_open_handle_scope) and
[`napi_close_handle_scope`](#napi_close_handle_scope) would ensure that at most a single handle
is valid throughout the execution of the loop:

```c
for (int i = 0; i < 1000000; i++) {
  napi_handle_scope scope;
  napi_status status = napi_open_handle_scope(env, &scope);
  if (status != napi_ok) {
    break;
  }
  napi_value result;
  status = napi_get_element(env, object, i, &result);
  if (status != napi_ok) {
    break;
  }
  // do something with element
  status = napi_close_handle_scope(env, scope);
  if (status != napi_ok) {
    break;
  }
}
```

When nesting scopes, there are cases where a handle from an
inner scope needs to live beyond the lifespan of that scope. Node-API supports
an 'escapable scope' in order to support this case. An escapable scope
allows one handle to be 'promoted' so that it 'escapes' the
current scope and the lifespan of the handle changes from the current
scope to that of the outer scope.

The methods available to open/close escapable scopes are
[`napi_open_escapable_handle_scope`](#napi_open_escapable_handle_scope) and
[`napi_close_escapable_handle_scope`](#napi_close_escapable_handle_scope).

The request to promote a handle is made through [`napi_escape_handle`](#napi_escape_handle) which
can only be called once.

#### `napi_open_handle_scope`

Added in: v8.0.0**N-API Version:** 1

```c
NAPI_EXTERN napi_status napi_open_handle_scope(napi_env env,
                                               napi_handle_scope* result);
```

- `[in] env`: The environment that the API is invoked under.
- `[out] result`: `napi_value` representing the new scope.

Returns `napi_ok` if the API succeeded.

This API opens a new scope.

#### `napi_close_handle_scope`

Added in: v8.0.0**N-API Version:** 1

```c
NAPI_EXTERN napi_status napi_close_handle_scope(napi_env env,
                                                napi_handle_scope scope);
```

- `[in] env`: The environment that the API is invoked under.
- `[in] scope`: `napi_value` representing the scope to be closed.

Returns `napi_ok` if the API succeeded.

This API closes the scope passed in. Scopes must be closed in the
reverse order from which they were created.

This API can be called even if there is a pending JavaScript exception.

#### `napi_open_escapable_handle_scope`

Added in: v8.0.0**N-API Version:** 1

```c
NAPI_EXTERN napi_status
    napi_open_escapable_handle_scope(napi_env env,
                                     napi_handle_scope* result);
```

- `[in] env`: The environment that the API is invoked under.
- `[out] result`: `napi_value` representing the new scope.

Returns `napi_ok` if the API succeeded.

This API opens a new scope from which one object can be promoted
to the outer scope.

#### `napi_close_escapable_handle_scope`

Added in: v8.0.0**N-API Version:** 1

```c
NAPI_EXTERN napi_status
    napi_close_escapable_handle_scope(napi_env env,
                                      napi_handle_scope scope);
```

- `[in] env`: The environment that the API is invoked under.
- `[in] scope`: `napi_value` representing the scope to be closed.

Returns `napi_ok` if the API succeeded.

This API closes the scope passed in. Scopes must be closed in the
reverse order from which they were created.

This API can be called even if there is a pending JavaScript exception.

#### `napi_escape_handle`

Added in: v8.0.0**N-API Version:** 1

```c
napi_status napi_escape_handle(napi_env env,
                               napi_escapable_handle_scope scope,
                               napi_value escapee,
                               napi_value* result);
```

- `[in] env`: The environment that the API is invoked under.
- `[in] scope`: `napi_value` representing the current scope.
- `[in] escapee`: `napi_value` representing the JavaScript `Object` to be
  escaped.
- `[out] result`: `napi_value` representing the handle to the escaped `Object`
  in the outer scope.

Returns `napi_ok` if the API succeeded.

This API promotes the handle to the JavaScript object so that it is valid
for the lifetime of the outer scope. It can only be called once per scope.
If it is called more than once an error will be returned.

This API can be called even if there is a pending JavaScript exception.

[Contents](../README.md) | [Previous: C++ addons](08-addons.md) | [Next: C/C++ addons with Node-API, part 2 of 4](09-n-api-part2.md)
