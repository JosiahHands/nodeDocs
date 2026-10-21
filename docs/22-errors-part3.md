[Contents](../README.md) | [Previous: Errors, part 2 of 5](22-errors-part2.md) | [Next: Errors, part 4 of 5](22-errors-part4.md)

Source: [https://nodejs.org/docs/latest/api/errors.html](https://nodejs.org/docs/latest/api/errors.html)

Part 3 of 5 of this section.

# Errors (part 3 of 5)

### `ERR_IPC_ONE_PIPE`

An attempt was made to create a child Node.js process using more than one IPC
communication channel. See the documentation for the [`child_process`](11-child-process.md) module
for more information.

### `ERR_IPC_SYNC_FORK`

An attempt was made to open an IPC communication channel with a synchronously
forked Node.js process. See the documentation for the [`child_process`](11-child-process.md) module
for more information.

### `ERR_IP_BLOCKED`

IP is blocked by `net.BlockList`.

### `ERR_LOADER_CHAIN_INCOMPLETE`

Added in: v18.6.0, v16.17.0

An ESM loader hook returned without calling `next()` and without explicitly
signaling a short circuit.

### `ERR_LOAD_SQLITE_EXTENSION`

Added in: v23.5.0, v22.13.0

An error occurred while loading a SQLite extension.

### `ERR_MEMORY_ALLOCATION_FAILED`

An attempt was made to allocate memory (usually in the C++ layer) but it
failed.

### `ERR_MESSAGE_TARGET_CONTEXT_UNAVAILABLE`

Added in: v14.5.0, v12.19.0

A message posted to a [`MessagePort`](67-worker-threads.md#class-messageport) could not be deserialized in the target
[vm](63-vm.md) `Context`. Not all Node.js objects can be successfully instantiated in
any context at this time, and attempting to transfer them using `postMessage()`
can fail on the receiving side in that case.

### `ERR_METHOD_NOT_IMPLEMENTED`

A method is required but not implemented.

### `ERR_MISSING_ARGS`

A required argument of a Node.js API was not passed. This is only used for
strict compliance with the API specification (which in some cases may accept
`func(undefined)` but not `func()`). In most native Node.js APIs,
`func(undefined)` and `func()` are treated identically, and the
[`ERR_INVALID_ARG_TYPE`](22-errors-part2.md#err_invalid_arg_type) error code may be used instead.

### `ERR_MISSING_OPTION`

For APIs that accept options objects, some options might be mandatory. This code
is thrown if a required option is missing.

### `ERR_MISSING_PASSPHRASE`

An attempt was made to read an encrypted key without specifying a passphrase.

### `ERR_MISSING_PLATFORM_FOR_WORKER`

The V8 platform used by this instance of Node.js does not support creating
Workers. This is caused by lack of embedder support for Workers. In particular,
this error will not occur with standard builds of Node.js.

### `ERR_MODULE_LINK_MISMATCH`

A module can not be linked because the same module requests in it are not
resolved to the same module.

### `ERR_MODULE_NOT_FOUND`

A module file could not be resolved by the ECMAScript modules loader while
attempting an `import` operation or when loading the program entry point.

### `ERR_MULTIPLE_CALLBACK`

A callback was called more than once.

A callback is almost always meant to only be called once as the query
can either be fulfilled or rejected but not both at the same time. The latter
would be possible by calling a callback more than once.

### `ERR_NAPI_CONS_FUNCTION`

While using `Node-API`, a constructor passed was not a function.

### `ERR_NAPI_INVALID_DATAVIEW_ARGS`

While calling `napi_create_dataview()`, a given `offset` was outside the bounds
of the dataview or `offset + length` was larger than a length of given `buffer`.

### `ERR_NAPI_INVALID_TYPEDARRAY_ALIGNMENT`

While calling `napi_create_typedarray()`, the provided `offset` was not a
multiple of the element size.

### `ERR_NAPI_INVALID_TYPEDARRAY_LENGTH`

While calling `napi_create_typedarray()`, `(length * size_of_element) + byte_offset` was larger than the length of given `buffer`.

### `ERR_NAPI_TSFN_CALL_JS`

An error occurred while invoking the JavaScript portion of the thread-safe
function.

### `ERR_NAPI_TSFN_GET_UNDEFINED`

An error occurred while attempting to retrieve the JavaScript `undefined`
value.

### `ERR_NON_CONTEXT_AWARE_DISABLED`

A non-context-aware native addon was loaded in a process that disallows them.

### `ERR_NOT_BUILDING_SNAPSHOT`

An attempt was made to use operations that can only be used when building
V8 startup snapshot even though Node.js isn't building one.

### `ERR_NOT_IN_SINGLE_EXECUTABLE_APPLICATION`

Added in: v21.7.0, v20.12.0

The operation cannot be performed when it's not in a single-executable
application.

### `ERR_NOT_SUPPORTED_IN_SNAPSHOT`

An attempt was made to perform operations that are not supported when
building a startup snapshot.

### `ERR_NO_CRYPTO`

An attempt was made to use crypto features while Node.js was not compiled with
OpenSSL crypto support.

### `ERR_NO_ICU`

An attempt was made to use features that require [ICU](31-intl.md#internationalization-support), but Node.js was not
compiled with ICU support.

### `ERR_NO_TEMPORAL`

Added in: v26.2.0

An attempt was made to use features that require [`Temporal`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Temporal), but Node.js was not
compiled with `Temporal` support or it has been disabled in the current environment
(for example, when running with `--no-harmony-temporal`).

### `ERR_NO_TYPESCRIPT`

Added in: v23.0.0, v22.12.0

An attempt was made to use features that require [Native TypeScript support](37-typescript.md#type-stripping), but Node.js was not
compiled with TypeScript support.

### `ERR_OPERATION_FAILED`

Added in: v15.0.0

An operation failed. This is typically used to signal the general failure
of an asynchronous operation.

### `ERR_OPTIONS_BEFORE_BOOTSTRAPPING`

Added in: v23.10.0, v22.16.0

An attempt was made to get options before the bootstrapping was completed.

### `ERR_OUT_OF_RANGE`

A given value is out of the accepted range.

### `ERR_PACKAGE_IMPORT_NOT_DEFINED`

The `package.json` [`"imports"`](36-packages.md#imports) field does not define the given internal
package specifier mapping.

### `ERR_PACKAGE_MAP_EXTERNAL_FILE`

Added in: v26.4.0

A module attempted to resolve a bare specifier using the [package map](36-packages.md#package-maps), but
the importing file is not located within any package defined in the map.

```console
$ node --experimental-package-map=./package-map.json /tmp/script.js
Error [ERR_PACKAGE_MAP_EXTERNAL_FILE]: Cannot resolve "dep-a" from "/tmp/script.js": file is not within any package defined in /path/to/package-map.json
```

To fix this error, ensure the importing file is inside one of the package
directories listed in the package map, or add a new package entry whose `url`
covers the importing file.

### `ERR_PACKAGE_MAP_INVALID`

Added in: v26.4.0

The [package map](36-packages.md#package-maps) configuration file is invalid. This can occur when:

- The file does not exist at the specified path.
- The file contains invalid JSON.
- The file is missing the required `packages` object.
- A package entry is missing the required `url` field.
- Two package entries have the same `url` value.

```console
$ node --experimental-package-map=./missing.json app.js
Error [ERR_PACKAGE_MAP_INVALID]: Invalid package map at "./missing.json": file not found
```

### `ERR_PACKAGE_MAP_KEY_NOT_FOUND`

Added in: v26.4.0

A package's `dependencies` object in the [package map](36-packages.md#package-maps) references a package
key that is not defined in the `packages` object.

```json
{
  "packages": {
    "app": {
      "url": "./app",
      "dependencies": {
        "foo": "nonexistent"
      }
    }
  }
}
```

In this example, `"nonexistent"` is referenced as a dependency target but not
defined in `packages`, which will throw this error.

To fix this error, ensure all package keys referenced in `dependencies` values
are defined in the `packages` object.

### `ERR_PACKAGE_PATH_NOT_EXPORTED`

The `package.json` [`"exports"`](36-packages.md#exports) field does not export the requested subpath.
Because exports are encapsulated, private internal modules that are not exported
cannot be imported through the package resolution, unless using an absolute URL.

### `ERR_PARSE_ARGS_INVALID_OPTION_VALUE`

Added in: v18.3.0, v16.17.0

When `strict` set to `true`, thrown by [`util.parseArgs()`](60-util-part2.md#utilparseargsconfig) if a [`<boolean>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#boolean_type)
value is provided for an option of type [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type), or if a [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type)
value is provided for an option of type [`<boolean>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#boolean_type).

### `ERR_PARSE_ARGS_UNEXPECTED_POSITIONAL`

Added in: v18.3.0, v16.17.0

Thrown by [`util.parseArgs()`](60-util-part2.md#utilparseargsconfig), when a positional argument is provided and
`allowPositionals` is set to `false`.

### `ERR_PARSE_ARGS_UNKNOWN_OPTION`

Added in: v18.3.0, v16.17.0

When `strict` set to `true`, thrown by [`util.parseArgs()`](60-util-part2.md#utilparseargsconfig) if an argument
is not configured in `options`.

### `ERR_PERFORMANCE_INVALID_TIMESTAMP`

An invalid timestamp value was provided for a performance mark or measure.

### `ERR_PERFORMANCE_MEASURE_INVALID_OPTIONS`

Invalid options were provided for a performance measure.

### `ERR_PROTO_ACCESS`

Accessing `Object.prototype.__proto__` has been forbidden using
[`--disable-proto=throw`](13-cli.md#--disable-protomode). [`Object.getPrototypeOf`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Object/getPrototypeOf) and
[`Object.setPrototypeOf`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Object/setPrototypeOf) should be used to get and set the prototype of an
object.

### `ERR_PROXY_INVALID_CONFIG`

Failed to proxy a request because the proxy configuration is invalid.

### `ERR_PROXY_TUNNEL`

Failed to establish proxy tunnel when `NODE_USE_ENV_PROXY` or `--use-env-proxy` is enabled.

### `ERR_QUIC_APPLICATION_ERROR`

Added in: v23.4.0, v22.13.0

[Stability: 1](01-documentation.md#stability-index) - Experimental

A QUIC application error occurred.

### `ERR_QUIC_CONNECTION_FAILED`

Added in: v23.0.0, v22.10.0

Stability: 1 - Experimental

Establishing a QUIC connection failed.

### `ERR_QUIC_ENDPOINT_CLOSED`

Added in: v23.0.0, v22.10.0

Stability: 1 - Experimental

A QUIC Endpoint closed with an error.

### `ERR_QUIC_OPEN_STREAM_FAILED`

Added in: v23.0.0, v22.10.0

Stability: 1 - Experimental

Opening a QUIC stream failed.

### `ERR_QUIC_STREAM_ABORTED`

Added in: v26.2.0

Stability: 1 - Experimental

The Node.js error code for a [`QuicError`](https://nodejs.org/docs/latest/api/quic.html#class-quicerror) thrown to abort a QUIC stream
or session with an explicit application or transport error code.

### `ERR_QUIC_STREAM_RESET`

Added in: v26.2.0

Stability: 1 - Experimental

A QUIC stream was reset by the peer. The error includes the reset code
provided by the peer.

### `ERR_QUIC_TRANSPORT_ERROR`

Added in: v23.4.0, v22.13.0

Stability: 1 - Experimental

A QUIC transport error occurred.

### `ERR_QUIC_VERSION_NEGOTIATION_ERROR`

Added in: v23.4.0, v22.13.0

Stability: 1 - Experimental

A QUIC session failed because version negotiation is required.

### `ERR_REQUIRE_ASYNC_MODULE`

**History**

| Version | Changes |
| --- | --- |
| v26.5.0 | Added the `requireStack` and `topLevelAwaitLocations` properties. |

When trying to `require()` an [ES Module](34-esm.md), the module turns out to be asynchronous.
That is, it contains top-level await.

When uncaught, the flag `--experimental-print-required-tla` prints
the locations of the top-level awaits in the graph to stderr.

This error has the following additional non-enumerable properties:

- `requireStack` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type)[] The chain of modules that led to the failing `require()`, starting with the module that required the asynchronous module.
- `topLevelAwaitLocations` [`<Object>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Object)[] The locations of the top-level awaits in
  the graph. Only populated when `--experimental-print-required-tla` is enabled.
  Each entry has the following properties:
  - `url` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) The URL of the module containing the top-level await.
  - `line` [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) The 1-based line number of the top-level await.
  - `column` [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) The 1-based column number of the top-level await.
  - `sourceLine` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) The source line containing the top-level await.

### `ERR_REQUIRE_CYCLE_MODULE`

When trying to `require()` an [ES Module](34-esm.md), a CommonJS to ESM or ESM to CommonJS edge
participates in an immediate cycle.
This is not allowed because ES Modules cannot be evaluated while they are
already being evaluated.

To avoid the cycle, the `require()` call involved in a cycle should not happen
at the top-level of either an ES Module (via `createRequire()`) or a CommonJS
module, and should be done lazily in an inner function.

### `ERR_REQUIRE_ESM`

**History**

| Version | Changes |
| --- | --- |
| v23.0.0, v22.12.0, v20.19.0 | require() now supports loading synchronous ES modules by default. |

Stability: 0 - Deprecated

An attempt was made to `require()` an [ES Module](34-esm.md).

This error has been deprecated since `require()` now supports loading synchronous
ES modules. When `require()` encounters an ES module that contains top-level
`await`, it will throw [`ERR_REQUIRE_ASYNC_MODULE`](#err_require_async_module) instead.

### `ERR_REQUIRE_ESM_RACE_CONDITION`

Added in: v26.1.0

Stability: 1 - Experimental.

An attempt was made to `require()` an [ES Module](34-esm.md) while another `import()` call
was already in progress to load it asynchronously.

### `ERR_RX_MEMORY_NOT_SUPPORTED`

Added in: v26.12.0

A feature requiring runtime allocation of executable memory was used in an
environment where that capability is unavailable. This includes creating an
otherwise eligible FFI Fast API function, or allocating an FFI callback when
libffi cannot provide a closure and executable memory is unavailable.

Node.js does not guarantee a non-generated-code fallback for these operations.
This error can be caught without terminating the process.

### `ERR_SCRIPT_EXECUTION_INTERRUPTED`

Script execution was interrupted by `SIGINT` (For
example, `Ctrl`+`C` was pressed.)

### `ERR_SCRIPT_EXECUTION_TIMEOUT`

Script execution timed out, possibly due to bugs in the script being executed.

### `ERR_SERVER_ALREADY_LISTEN`

The [`server.listen()`](38-net.md#serverlisten) method was called while a `net.Server` was already
listening. This applies to all instances of `net.Server`, including HTTP, HTTPS,
and HTTP/2 `Server` instances.

### `ERR_SERVER_NOT_RUNNING`

The [`server.close()`](38-net.md#serverclosecallback) method was called when a `net.Server` was not
running. This applies to all instances of `net.Server`, including HTTP, HTTPS,
and HTTP/2 `Server` instances.

### `ERR_SINGLE_EXECUTABLE_APPLICATION_ASSET_NOT_FOUND`

Added in: v21.7.0, v20.12.0

A key was passed to single executable application APIs to identify an asset,
but no match could be found.

### `ERR_SOCKET_ALREADY_BOUND`

An attempt was made to bind a socket that has already been bound.

### `ERR_SOCKET_BAD_BUFFER_SIZE`

An invalid (negative) size was passed for either the `recvBufferSize` or
`sendBufferSize` options in [`dgram.createSocket()`](58-dgram.md#dgramcreatesocketoptions-callback).

### `ERR_SOCKET_BAD_PORT`

An API function expecting a port >= 0 and \< 65536 received an invalid value.

### `ERR_SOCKET_BAD_TYPE`

An API function expecting a socket type (`udp4` or `udp6`) received an invalid
value.

### `ERR_SOCKET_BUFFER_SIZE`

While using [`dgram.createSocket()`](58-dgram.md#dgramcreatesocketoptions-callback), the size of the receive or send `Buffer`
could not be determined.

### `ERR_SOCKET_CLOSED`

An attempt was made to operate on an already closed socket.

### `ERR_SOCKET_CLOSED_BEFORE_CONNECTION`

When calling [`net.Socket.write()`](38-net.md#socketwritedata-encoding-callback) on a connecting socket and the socket was
closed before the connection was established.

### `ERR_SOCKET_CONNECTION_TIMEOUT`

The socket was unable to connect to any address returned by the DNS within the
allowed timeout when using the family autoselection algorithm.

### `ERR_SOCKET_DGRAM_IS_CONNECTED`

A [`dgram.connect()`](58-dgram.md#socketconnectport-address-callback) call was made on an already connected socket.

### `ERR_SOCKET_DGRAM_NOT_CONNECTED`

A [`dgram.disconnect()`](58-dgram.md#socketdisconnect) or [`dgram.remoteAddress()`](58-dgram.md#socketremoteaddress) call was made on a
disconnected socket.

### `ERR_SOCKET_DGRAM_NOT_RUNNING`

A call was made and the UDP subsystem was not running.

### `ERR_SOCKET_HANDLE_ADOPTED`

An operation was attempted on a [`BoundSocket`](38-net.md#class-netboundsocket) that had already been adopted
by a [`net.Server`](38-net.md#class-netserver) or [`net.Socket`](38-net.md#class-netsocket), or transferred to another thread.
Once a bound socket is adopted or transferred, its `address()` and `close()`
methods can no longer be used.

### `ERR_SOURCE_MAP_CORRUPT`

The source map could not be parsed because it does not exist, or is corrupt.

### `ERR_SOURCE_MAP_MISSING_SOURCE`

A file imported from a source map was not found.

### `ERR_SOURCE_PHASE_NOT_DEFINED`

Added in: v24.0.0

The provided module import does not provide a source phase imports representation for source phase
import syntax `import source x from 'x'` or `import.source(x)`.

### `ERR_SQLITE_ERROR`

Added in: v22.5.0

An error was returned from [SQLite](50-sqlite.md).

### `ERR_SRI_PARSE`

A string was provided for a Subresource Integrity check, but was unable to be
parsed. Check the format of integrity attributes by looking at the
[Subresource Integrity specification](https://www.w3.org/TR/SRI/#the-integrity-attribute).

### `ERR_STREAM_ALREADY_FINISHED`

A stream method was called that cannot complete because the stream was
finished.

### `ERR_STREAM_CANNOT_PIPE`

An attempt was made to call [`stream.pipe()`](51-stream-part1.md#readablepipedestination-options) on a [`Writable`](51-stream-part1.md#class-streamwritable) stream.

### `ERR_STREAM_DESTROYED`

A stream method was called that cannot complete because the stream was
destroyed using `stream.destroy()`.

### `ERR_STREAM_ITER_MISSING_FLAG`

A stream/iter API was used without the `--experimental-stream-iter` CLI flag
enabled.

### `ERR_STREAM_NULL_VALUES`

An attempt was made to call [`stream.write()`](51-stream-part1.md#writablewritechunk-encoding-callback) with a `null` chunk.

### `ERR_STREAM_PREMATURE_CLOSE`

An error returned by `stream.finished()` and `stream.pipeline()`, when a stream
or a pipeline ends non gracefully with no explicit error.

### `ERR_STREAM_PUSH_AFTER_EOF`

An attempt was made to call [`stream.push()`](51-stream-part3.md#readablepushchunk-encoding) after a `null`(EOF) had been
pushed to the stream.

### `ERR_STREAM_UNABLE_TO_PIPE`

An attempt was made to pipe to a closed or destroyed stream in a pipeline.

### `ERR_STREAM_UNSHIFT_AFTER_END_EVENT`

An attempt was made to call [`stream.unshift()`](51-stream-part2.md#readableunshiftchunk-encoding) after the `'end'` event was
emitted.

### `ERR_STREAM_WRAP`

Prevents an abort if a string decoder was set on the Socket or if the decoder
is in `objectMode`.

```js
const Socket = require('node:net').Socket;
const instance = new Socket();

instance.setEncoding('utf8');
```

### `ERR_STREAM_WRITE_AFTER_END`

An attempt was made to call [`stream.write()`](51-stream-part1.md#writablewritechunk-encoding-callback) after `stream.end()` has been
called.

### `ERR_STRING_TOO_LONG`

An attempt has been made to create a string longer than the maximum allowed
length.

### `ERR_SYNTHETIC`

An artificial error object used to capture the call stack for diagnostic
reports.

### `ERR_SYSTEM_ERROR`

An unspecified or non-specific system error has occurred within the Node.js
process. The error object will have an `err.info` object property with
additional details.

### `ERR_TEST_FAILURE`

This error represents a failed test. Additional information about the failure
is available via the `cause` property. The `failureType` property specifies
what the test was doing when the failure occurred.

### `ERR_THROTTLED`

A call was dropped because a throttled function could not invoke it immediately
or its pending queue was full.

### `ERR_TLS_ALPN_CALLBACK_INVALID_RESULT`

This error is thrown when an `ALPNCallback` returns a value that is not in the
list of ALPN protocols offered by the client.

### `ERR_TLS_ALPN_CALLBACK_WITH_PROTOCOLS`

This error is thrown when creating a `TLSServer` if the TLS options include
both `ALPNProtocols` and `ALPNCallback`. These options are mutually exclusive.

### `ERR_TLS_CERT_ALTNAME_FORMAT`

This error is thrown by `checkServerIdentity` if a user-supplied
`subjectaltname` property violates encoding rules. Certificate objects produced
by Node.js itself always comply with encoding rules and will never cause
this error.

### `ERR_TLS_CERT_ALTNAME_INVALID`

While using TLS, the host name/IP of the peer did not match any of the
`subjectAltNames` in its certificate.

### `ERR_TLS_DH_PARAM_SIZE`

While using TLS, the parameter offered for the Diffie-Hellman (`DH`)
key-agreement protocol is too small. By default, the key length must be greater
than or equal to 1024 bits to avoid vulnerabilities, even though it is strongly
recommended to use 2048 bits or larger for stronger security.

### `ERR_TLS_HANDSHAKE_TIMEOUT`

A TLS/SSL handshake timed out. In this case, the server must also abort the
connection.

### `ERR_TLS_INVALID_CONTEXT`

Added in: v13.3.0

The context must be a `SecureContext`.

### `ERR_TLS_INVALID_PROTOCOL_METHOD`

The specified `secureProtocol` method is invalid. It is either unknown, or
disabled because it is insecure.

### `ERR_TLS_INVALID_PROTOCOL_VERSION`

Valid TLS protocol versions are `'TLSv1'`, `'TLSv1.1'`, or `'TLSv1.2'`.

### `ERR_TLS_INVALID_STATE`

Added in: v13.10.0, v12.17.0

The TLS socket must be connected and securely established. Ensure the 'secure'
event is emitted before continuing.

### `ERR_TLS_PROTOCOL_VERSION_CONFLICT`

Attempting to set a TLS protocol `minVersion` or `maxVersion` conflicts with an
attempt to set the `secureProtocol` explicitly. Use one mechanism or the other.

### `ERR_TLS_PSK_SET_IDENTITY_HINT_FAILED`

Failed to set PSK identity hint. Hint may be too long.

### `ERR_TLS_RENEGOTIATION_DISABLED`

An attempt was made to renegotiate TLS on a socket instance with renegotiation
disabled.

### `ERR_TLS_RENEGOTIATION_UNSUPPORTED`

An attempt was made to renegotiate TLS, but the TLS implementation does not
support caller-initiated renegotiation.

### `ERR_TLS_REQUIRED_SERVER_NAME`

While using TLS, the `server.addContext()` method was called without providing
a host name in the first parameter.

### `ERR_TLS_SESSION_ATTACK`

An excessive amount of TLS renegotiations is detected, which is a potential
vector for denial-of-service attacks.

### `ERR_TLS_SNI_FROM_SERVER`

An attempt was made to issue Server Name Indication from a TLS server-side
socket, which is only valid from a client.

### `ERR_TRACE_EVENTS_CATEGORY_REQUIRED`

The `trace_events.createTracing()` method requires at least one trace event
category.

### `ERR_TRACE_EVENTS_UNAVAILABLE`

The `node:trace_events` module could not be loaded because Node.js was compiled
with the `--without-v8-platform` flag, or because the process was initialized by
an embedder that provides its own V8 platform.

### `ERR_TRAILING_JUNK_AFTER_STREAM_END`

Trailing junk found after the end of the compressed stream.
This error is thrown when extra, unexpected data is detected
after the end of a compressed stream (for example, in zlib
or gzip decompression).

### `ERR_TRANSFORM_ALREADY_TRANSFORMING`

A `Transform` stream finished while it was still transforming.

### `ERR_TRANSFORM_WITH_LENGTH_0`

A `Transform` stream finished with data still in the write buffer.

### `ERR_TTY_INIT_FAILED`

The initialization of a TTY failed due to a system error.

### `ERR_UNAVAILABLE_DURING_EXIT`

Function was called within a [`process.on('exit')`](43-process-part1.md#event-exit) handler that shouldn't be
called within [`process.on('exit')`](43-process-part1.md#event-exit) handler.

### `ERR_UNCAUGHT_EXCEPTION_CAPTURE_ALREADY_SET`

[`process.setUncaughtExceptionCaptureCallback()`](43-process-part3.md#processsetuncaughtexceptioncapturecallbackfn) was called twice,
without first resetting the callback to `null`.

This error is designed to prevent accidentally overwriting a callback registered
from another module.

[Contents](../README.md) | [Previous: Errors, part 2 of 5](22-errors-part2.md) | [Next: Errors, part 4 of 5](22-errors-part4.md)
