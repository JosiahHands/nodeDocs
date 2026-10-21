[Contents](../README.md) | [Previous: Errors, part 3 of 5](22-errors-part3.md) | [Next: Errors, part 5 of 5](22-errors-part5.md)

Source: [https://nodejs.org/docs/latest/api/errors.html](https://nodejs.org/docs/latest/api/errors.html)

Part 4 of 5 of this section.

# Errors (part 4 of 5)

Continues: Node.js error codes.

### `ERR_UNESCAPED_CHARACTERS`

A string that contained unescaped characters was received.

### `ERR_UNHANDLED_ERROR`

An unhandled error occurred (for instance, when an `'error'` event is emitted
by an [`EventEmitter`](23-events.md#class-eventemitter) but an `'error'` handler is not registered).

### `ERR_UNKNOWN_BUILTIN_MODULE`

Used to identify a specific kind of internal Node.js error that should not
typically be triggered by user code. Instances of this error point to an
internal bug within the Node.js binary itself.

### `ERR_UNKNOWN_CREDENTIAL`

A Unix group or user identifier that does not exist was passed.

### `ERR_UNKNOWN_ENCODING`

An invalid or unknown encoding option was passed to an API.

### `ERR_UNKNOWN_FILE_EXTENSION`

An attempt was made to load a module with an unknown or unsupported file
extension.

### `ERR_UNKNOWN_MODULE_FORMAT`

An attempt was made to load a module with an unknown or unsupported format.

### `ERR_UNKNOWN_SIGNAL`

An invalid or unknown process signal was passed to an API expecting a valid
signal (such as [`subprocess.kill()`](11-child-process.md#subprocesskillsignal)).

### `ERR_UNSUPPORTED_DIR_IMPORT`

`import` a directory URL is unsupported. Instead,
[self-reference a package using its name](36-packages.md#self-referencing-a-package-using-its-name) and [define a custom subpath](36-packages.md#subpath-exports) in
the [`"exports"`](36-packages.md#exports) field of the [`package.json`](36-packages.md#nodejs-packagejson-field-definitions) file.

```js
import './'; // unsupported
import './index.js'; // supported
import 'package-name'; // supported
```

### `ERR_UNSUPPORTED_ESM_URL_SCHEME`

`import` with URL schemes other than `file` and `data` is unsupported.

### `ERR_UNSUPPORTED_NODE_MODULES_TYPE_STRIPPING`

Added in: v22.6.0

Type stripping is not supported for files descendant of a `node_modules` directory.

### `ERR_UNSUPPORTED_RESOLVE_REQUEST`

An attempt was made to resolve an invalid module referrer. This can happen when
importing or calling `import.meta.resolve()` with either:

- a bare specifier that is not a builtin module from a module whose URL scheme
  is not `file`.
- a [relative URL](https://url.spec.whatwg.org/#relative-url-string) from a module whose URL scheme is not a [special scheme](https://url.spec.whatwg.org/#special-scheme).

```js
try {
  // Trying to import the package 'bare-specifier' from a `data:` URL module:
  await import('data:text/javascript,import "bare-specifier"');
} catch (e) {
  console.log(e.code); // ERR_UNSUPPORTED_RESOLVE_REQUEST
}
```

### `ERR_UNSUPPORTED_TYPESCRIPT_SYNTAX`

Added in: v23.7.0, v22.14.0

The provided TypeScript syntax is unsupported.
This could happen when using TypeScript syntax that requires
transformation with [type-stripping](37-typescript.md#type-stripping).

### `ERR_USE_AFTER_CLOSE`

An attempt was made to use something that was already closed.

### `ERR_VALID_PERFORMANCE_ENTRY_TYPE`

While using the Performance Timing API (`perf_hooks`), no valid performance
entry types are found.

### `ERR_VFS_INVALID_TARGET`

A `--vfs-load` source does not exist, is neither a regular file nor a
directory, or is a source no provider claims.

### `ERR_VM_DYNAMIC_IMPORT_CALLBACK_MISSING`

A dynamic import callback was not specified.

### `ERR_VM_DYNAMIC_IMPORT_CALLBACK_MISSING_FLAG`

A dynamic import callback was invoked without `--experimental-vm-modules`.

### `ERR_VM_MODULE_ALREADY_LINKED`

The module attempted to be linked is not eligible for linking, because of one of
the following reasons:

- It has already been linked (`linkingStatus` is `'linked'`)
- It is being linked (`linkingStatus` is `'linking'`)
- Linking has failed for this module (`linkingStatus` is `'errored'`)

### `ERR_VM_MODULE_CACHED_DATA_REJECTED`

The `cachedData` option passed to a module constructor is invalid.

### `ERR_VM_MODULE_CANNOT_CREATE_CACHED_DATA`

Cached data cannot be created for modules which have already been evaluated.

### `ERR_VM_MODULE_DIFFERENT_CONTEXT`

The module being returned from the linker function is from a different context
than the parent module. Linked modules must share the same context.

### `ERR_VM_MODULE_LINK_FAILURE`

The module was unable to be linked due to a failure.

### `ERR_VM_MODULE_NOT_MODULE`

The fulfilled value of a linking promise is not a `vm.Module` object.

### `ERR_VM_MODULE_STATUS`

The current module's status does not allow for this operation. The specific
meaning of the error depends on the specific function.

### `ERR_WASI_ALREADY_STARTED`

The WASI instance has already started.

### `ERR_WASI_NOT_STARTED`

The WASI instance has not been started.

### `ERR_WEBASSEMBLY_NOT_SUPPORTED`

A feature requiring WebAssembly was used, but WebAssembly is not supported or
has been disabled in the current environment (for example, when running with
`--jitless`).

TypeScript parsing and WebAssembly module imports, including source phase
imports, report this error when WebAssembly is unavailable. Node.js does not
provide an alternative implementation for these operations. This error can be
caught without terminating the process.

### `ERR_WEBASSEMBLY_RESPONSE`

Added in: v18.1.0

The `Response` that has been passed to `WebAssembly.compileStreaming` or to
`WebAssembly.instantiateStreaming` is not a valid WebAssembly response.

### `ERR_WORKER_HANDLE_NOT_TRANSFERABLE`

An attempt was made to transfer a `net.Socket`, `net.Server` or
`net.BoundSocket` to another thread via a `worker_threads` `postMessage()` call
while it was not in a transferable state, for example because it had already
started reading, had buffered data, or had already been adopted.

### `ERR_WORKER_INIT_FAILED`

The `Worker` initialization failed.

### `ERR_WORKER_INVALID_EXEC_ARGV`

The `execArgv` option passed to the `Worker` constructor contains
invalid flags.

### `ERR_WORKER_MESSAGING_ERRORED`

Added in: v22.5.0

Stability: 1.1 - Active development

The destination thread threw an error while processing a message sent via [`postMessageToThread()`](67-worker-threads.md#worker_threadspostmessagetothreadthreadid-value-transferlist-timeout).

### `ERR_WORKER_MESSAGING_FAILED`

Added in: v22.5.0

Stability: 1.1 - Active development

The thread requested in [`postMessageToThread()`](67-worker-threads.md#worker_threadspostmessagetothreadthreadid-value-transferlist-timeout) is invalid or has no `workerMessage` listener.

### `ERR_WORKER_MESSAGING_SAME_THREAD`

Added in: v22.5.0

Stability: 1.1 - Active development

The thread id requested in [`postMessageToThread()`](67-worker-threads.md#worker_threadspostmessagetothreadthreadid-value-transferlist-timeout) is the current thread id.

### `ERR_WORKER_MESSAGING_TIMEOUT`

Added in: v22.5.0

Stability: 1.1 - Active development

Sending a message via [`postMessageToThread()`](67-worker-threads.md#worker_threadspostmessagetothreadthreadid-value-transferlist-timeout) timed out.

### `ERR_WORKER_NOT_RUNNING`

An operation failed because the `Worker` instance is not currently running.

### `ERR_WORKER_OUT_OF_MEMORY`

The `Worker` instance terminated because it reached its memory limit.

### `ERR_WORKER_PATH`

The path for the main script of a worker is neither an absolute path
nor a relative path starting with `./` or `../`.

### `ERR_WORKER_UNSERIALIZABLE_ERROR`

All attempts at serializing an uncaught exception from a worker thread failed.

### `ERR_WORKER_UNSUPPORTED_OPERATION`

The requested functionality is not supported in worker threads.

### `ERR_ZIP_ARCHIVE_TOO_LARGE`

An archive-level structure exceeds a limit: the archive comment exceeds the
65,535-byte encoded length that the ZIP format allows, or the archive's
central directory is too large to buffer in memory.

### `ERR_ZIP_ENTRY_CORRUPT`

A ZIP archive entry failed CRC-32 verification, or produced more or fewer
bytes than its declared uncompressed size, while being read.

### `ERR_ZIP_ENTRY_NOT_FOUND`

A named entry was requested from a [`ZipFile`](68-zlib.md#class-zlibzipfile) or [`ZipBuffer`](68-zlib.md#class-zlibzipbuffer) that does
not contain an entry with that name.

### `ERR_ZIP_ENTRY_TOO_LARGE`

A ZIP archive entry's declared size exceeds the configured limit, or a
provided entry name or comment exceeds the 65,535-byte encoded length that
the ZIP format allows.

### `ERR_ZIP_INVALID_ARCHIVE`

Data that was expected to be a ZIP archive, or a structure within one, is
missing, out of bounds, or otherwise inconsistent with the ZIP format.

### `ERR_ZIP_NOT_WRITABLE`

A mutating method (such as `zipFile.addEntry()` or `zipFile.delete()`) was
called on a [`ZipFile`](68-zlib.md#class-zlibzipfile) that was not opened with `{ writable: true }`.

### `ERR_ZIP_UNSUPPORTED_FEATURE`

A ZIP archive uses a feature outside of what this implementation supports,
such as entry encryption, an unsupported compression method, or a multi-disk
archive.

### `ERR_ZLIB_INITIALIZATION_FAILED`

Creation of a [`zlib`](68-zlib.md) object failed due to incorrect configuration.

### `ERR_ZSTD_INVALID_PARAM`

An invalid parameter key was passed during construction of a Zstd stream.

### `HPE_CHUNK_EXTENSIONS_OVERFLOW`

Added in: v21.6.2, v20.11.1, v18.19.1

Too much data was received for a chunk extensions. In order to protect against
malicious or malconfigured clients, if more than 16 KiB of data is received
then an `Error` with this code will be emitted.

### `HPE_HEADER_OVERFLOW`

**History**

| Version | Changes |
| --- | --- |
| v11.4.0, v10.15.0 | Max header size in `http_parser` was set to 8 KiB. |

Too much HTTP header data was received. In order to protect against malicious or
malconfigured clients, if more than `maxHeaderSize` of HTTP header data is received then
HTTP parsing will abort without a request or response object being created, and
an `Error` with this code will be emitted.

### `HPE_UNEXPECTED_CONTENT_LENGTH`

Server is sending both a `Content-Length` header and `Transfer-Encoding: chunked`.

`Transfer-Encoding: chunked` allows the server to maintain an HTTP persistent
connection for dynamically generated content.
In this case, the `Content-Length` HTTP header cannot be used.

Use `Content-Length` or `Transfer-Encoding: chunked`.

### `MODULE_NOT_FOUND`

**History**

| Version | Changes |
| --- | --- |
| v12.0.0 | Added `requireStack` property. |

A module file could not be resolved by the CommonJS modules loader while
attempting a [`require()`](33-modules.md#requireid) operation or when loading the program entry point.

## Legacy Node.js error codes

Stability: 0 - Deprecated. These error codes are either inconsistent, or have
been removed.

### `ERR_CANNOT_TRANSFER_OBJECT`

Added in: v10.5.0Removed in: v12.5.0

The value passed to `postMessage()` contained an object that is not supported
for transferring.

### `ERR_CPU_USAGE`

Removed in: v15.0.0

The native call from `process.cpuUsage` could not be processed.

### `ERR_CRYPTO_HASH_DIGEST_NO_UTF16`

Added in: v9.0.0Removed in: v12.12.0

The UTF-16 encoding was used with [`hash.digest()`](15-crypto-part2.md#hashdigestencoding). While the
`hash.digest()` method does allow an `encoding` argument to be passed in,
causing the method to return a string rather than a `Buffer`, the UTF-16
encoding (e.g. `ucs` or `utf16le`) is not supported.

### `ERR_CRYPTO_SCRYPT_INVALID_PARAMETER`

Removed in: v23.0.0

An incompatible combination of options was passed to [`crypto.scrypt()`](15-crypto-part5.md#cryptoscryptpassword-salt-keylen-options-callback) or
[`crypto.scryptSync()`](15-crypto-part5.md#cryptoscryptsyncpassword-salt-keylen-options). New versions of Node.js use the error code
[`ERR_INCOMPATIBLE_OPTION_PAIR`](22-errors-part2.md#err_incompatible_option_pair) instead, which is consistent with other APIs.

### `ERR_FS_INVALID_SYMLINK_TYPE`

Removed in: v23.0.0

An invalid symlink type was passed to the [`fs.symlink()`](24-fs-part4.md#fssymlinktarget-path-type-callback) or
[`fs.symlinkSync()`](24-fs-part6.md#fssymlinksynctarget-path-type) methods.

### `ERR_HTTP2_FRAME_ERROR`

Added in: v9.0.0Removed in: v10.0.0

Used when a failure occurs sending an individual frame on the HTTP/2
session.

### `ERR_HTTP2_HEADERS_OBJECT`

Added in: v9.0.0Removed in: v10.0.0

Used when an HTTP/2 Headers Object is expected.

### `ERR_HTTP2_HEADER_REQUIRED`

Added in: v9.0.0Removed in: v10.0.0

Used when a required header is missing in an HTTP/2 message.

### `ERR_HTTP2_INFO_HEADERS_AFTER_RESPOND`

Added in: v9.0.0Removed in: v10.0.0

HTTP/2 informational headers must only be sent *prior* to calling the
`Http2Stream.prototype.respond()` method.

### `ERR_HTTP2_STREAM_CLOSED`

Added in: v9.0.0Removed in: v10.0.0

Used when an action has been performed on an HTTP/2 Stream that has already
been closed.

### `ERR_HTTP_INVALID_CHAR`

Added in: v9.0.0Removed in: v10.0.0

Used when an invalid character is found in an HTTP response status message
(reason phrase).

### `ERR_IMPORT_ASSERTION_TYPE_FAILED`

Added in: v17.1.0, v16.14.0Removed in: v21.1.0

An import assertion has failed, preventing the specified module to be imported.

### `ERR_IMPORT_ASSERTION_TYPE_MISSING`

Added in: v17.1.0, v16.14.0Removed in: v21.1.0

An import assertion is missing, preventing the specified module to be imported.

### `ERR_IMPORT_ASSERTION_TYPE_UNSUPPORTED`

Added in: v17.1.0, v16.14.0Removed in: v21.1.0

An import attribute is not supported by this version of Node.js.

### `ERR_INDEX_OUT_OF_RANGE`

Added in: v10.0.0Removed in: v11.0.0

A given index was out of the accepted range (e.g. negative offsets).

### `ERR_INVALID_OPT_VALUE`

Added in: v8.0.0Removed in: v15.0.0

An invalid or unexpected value was passed in an options object.

### `ERR_INVALID_OPT_VALUE_ENCODING`

Added in: v9.0.0Removed in: v15.0.0

An invalid or unknown file encoding was passed.

### `ERR_INVALID_PERFORMANCE_MARK`

Added in: v8.5.0Removed in: v16.7.0

While using the Performance Timing API (`perf_hooks`), a performance mark is
invalid.

### `ERR_INVALID_TRANSFER_OBJECT`

Removed in: v21.0.0

**History**

| Version | Changes |
| --- | --- |
| v21.0.0 | A `DOMException` is thrown instead. |

An invalid transfer object was passed to `postMessage()`.

### `ERR_MANIFEST_ASSERT_INTEGRITY`

Removed in: v22.2.0

An attempt was made to load a resource, but the resource did not match the
integrity defined by the policy manifest. See the documentation for policy
manifests for more information.

### `ERR_MANIFEST_DEPENDENCY_MISSING`

Removed in: v22.2.0

An attempt was made to load a resource, but the resource was not listed as a
dependency from the location that attempted to load it. See the documentation
for policy manifests for more information.

### `ERR_MANIFEST_INTEGRITY_MISMATCH`

Removed in: v22.2.0

An attempt was made to load a policy manifest, but the manifest had multiple
entries for a resource which did not match each other. Update the manifest
entries to match in order to resolve this error. See the documentation for
policy manifests for more information.

### `ERR_MANIFEST_INVALID_RESOURCE_FIELD`

Removed in: v22.2.0

A policy manifest resource had an invalid value for one of its fields. Update
the manifest entry to match in order to resolve this error. See the
documentation for policy manifests for more information.

### `ERR_MANIFEST_INVALID_SPECIFIER`

Removed in: v22.2.0

A policy manifest resource had an invalid value for one of its dependency
mappings. Update the manifest entry to match to resolve this error. See the
documentation for policy manifests for more information.

### `ERR_MANIFEST_PARSE_POLICY`

Removed in: v22.2.0

An attempt was made to load a policy manifest, but the manifest was unable to
be parsed. See the documentation for policy manifests for more information.

### `ERR_MANIFEST_TDZ`

Removed in: v22.2.0

An attempt was made to read from a policy manifest, but the manifest
initialization has not yet taken place. This is likely a bug in Node.js.

### `ERR_MANIFEST_UNKNOWN_ONERROR`

Removed in: v22.2.0

A policy manifest was loaded, but had an unknown value for its "onerror"
behavior. See the documentation for policy manifests for more information.

### `ERR_MISSING_MESSAGE_PORT_IN_TRANSFER_LIST`

Removed in: v15.0.0

This error code was replaced by [`ERR_MISSING_TRANSFERABLE_IN_TRANSFER_LIST`](#err_missing_transferable_in_transfer_list)
in Node.js 15.0.0, because it is no longer accurate as other types of
transferable objects also exist now.

### `ERR_MISSING_TRANSFERABLE_IN_TRANSFER_LIST`

Added in: v15.0.0Removed in: v21.0.0

**History**

| Version | Changes |
| --- | --- |
| v21.0.0 | A `DOMException` is thrown instead. |

An object that needs to be explicitly listed in the `transferList` argument
is in the object passed to a [`postMessage()`](67-worker-threads.md#portpostmessagevalue-transferlist) call, but is not provided
in the `transferList` for that call. Usually, this is a `MessagePort`.

In Node.js versions prior to v15.0.0, the error code being used here was
[`ERR_MISSING_MESSAGE_PORT_IN_TRANSFER_LIST`](#err_missing_message_port_in_transfer_list). However, the set of
transferable object types has been expanded to cover more types than
`MessagePort`.

### `ERR_NAPI_CONS_PROTOTYPE_OBJECT`

Added in: v9.0.0Removed in: v10.0.0

Used by the `Node-API` when `Constructor.prototype` is not an object.

### `ERR_NAPI_TSFN_START_IDLE_LOOP`

Added in: v10.6.0, v8.16.0Removed in: v14.2.0, v12.17.0

On the main thread, values are removed from the queue associated with the
thread-safe function in an idle loop. This error indicates that an error
has occurred when attempting to start the loop.

### `ERR_NAPI_TSFN_STOP_IDLE_LOOP`

Added in: v10.6.0, v8.16.0Removed in: v14.2.0, v12.17.0

Once no more items are left in the queue, the idle loop must be suspended. This
error indicates that the idle loop has failed to stop.

### `ERR_NO_LONGER_SUPPORTED`

A Node.js API was called in an unsupported manner, such as
`Buffer.write(string, encoding, offset[, length])`.

### `ERR_OUTOFMEMORY`

Added in: v9.0.0Removed in: v10.0.0

Used generically to identify that an operation caused an out of memory
condition.

### `ERR_PARSE_HISTORY_DATA`

Added in: v9.0.0Removed in: v10.0.0

The `node:repl` module was unable to parse data from the REPL history file.

### `ERR_SOCKET_CANNOT_SEND`

Added in: v9.0.0Removed in: v14.0.0

Data could not be sent on a socket.

### `ERR_STDERR_CLOSE`

Removed in: v10.12.0

**History**

| Version | Changes |
| --- | --- |
| v10.12.0 | Rather than emitting an error, `process.stderr.end()` now only closes the stream side but not the underlying resource, making this error obsolete. |

An attempt was made to close the `process.stderr` stream. By design, Node.js
does not allow `stdout` or `stderr` streams to be closed by user code.

### `ERR_STDOUT_CLOSE`

Removed in: v10.12.0

**History**

| Version | Changes |
| --- | --- |
| v10.12.0 | Rather than emitting an error, `process.stderr.end()` now only closes the stream side but not the underlying resource, making this error obsolete. |

An attempt was made to close the `process.stdout` stream. By design, Node.js
does not allow `stdout` or `stderr` streams to be closed by user code.

### `ERR_STREAM_READ_NOT_IMPLEMENTED`

Added in: v9.0.0Removed in: v10.0.0

Used when an attempt is made to use a readable stream that has not implemented
[`readable._read()`](51-stream-part3.md#readable_readsize).

### `ERR_TAP_LEXER_ERROR`

An error representing a failing lexer state.

### `ERR_TAP_PARSER_ERROR`

An error representing a failing parser state. Additional information about
the token causing the error is available via the `cause` property.

### `ERR_TAP_VALIDATION_ERROR`

This error represents a failed TAP validation.

### `ERR_TLS_RENEGOTIATION_FAILED`

Added in: v9.0.0Removed in: v10.0.0

Used when a TLS renegotiation request has failed in a non-specific way.

### `ERR_TRANSFERRING_EXTERNALIZED_SHAREDARRAYBUFFER`

Added in: v10.5.0Removed in: v14.0.0

A `SharedArrayBuffer` whose memory is not managed by the JavaScript engine
or by Node.js was encountered during serialization. Such a `SharedArrayBuffer`
cannot be serialized.

This can only happen when native addons create `SharedArrayBuffer`s in
"externalized" mode, or put existing `SharedArrayBuffer` into externalized mode.

### `ERR_UNKNOWN_STDIN_TYPE`

Added in: v8.0.0Removed in: v11.7.0

An attempt was made to launch a Node.js process with an unknown `stdin` file
type. This error is usually an indication of a bug within Node.js itself,
although it is possible for user code to trigger it.

### `ERR_UNKNOWN_STREAM_TYPE`

Added in: v8.0.0Removed in: v11.7.0

An attempt was made to launch a Node.js process with an unknown `stdout` or
`stderr` file type. This error is usually an indication of a bug within Node.js
itself, although it is possible for user code to trigger it.

### `ERR_V8BREAKITERATOR`

The V8 `BreakIterator` API was used but the full ICU data set is not installed.

### `ERR_VALUE_OUT_OF_RANGE`

Added in: v9.0.0Removed in: v10.0.0

Used when a given value is out of the accepted range.

### `ERR_VM_MODULE_LINKING_ERRORED`

Added in: v10.0.0Removed in: v18.1.0, v16.17.0

The linker function returned a module for which linking has failed.

### `ERR_VM_MODULE_NOT_LINKED`

The module must be successfully linked before instantiation.

### `ERR_WORKER_UNSUPPORTED_EXTENSION`

Added in: v11.0.0Removed in: v16.9.0

The pathname used for the main script of a worker has an
unknown file extension.

### `ERR_ZLIB_BINDING_CLOSED`

Added in: v9.0.0Removed in: v10.0.0

Used when an attempt is made to use a `zlib` object after it has already been
closed.

## OpenSSL Error Codes

### Time Validity Errors

#### `CERT_NOT_YET_VALID`

The certificate is not yet valid: the notBefore date is after the current time.

#### `CERT_HAS_EXPIRED`

The certificate has expired: the notAfter date is before the current time.

#### `CRL_NOT_YET_VALID`

The certificate revocation list (CRL) has a future issue date.

#### `CRL_HAS_EXPIRED`

The certificate revocation list (CRL) has expired.

#### `CERT_REVOKED`

The certificate has been revoked; it is on a certificate revocation list (CRL).

### Trust or Chain Related Errors

#### `UNABLE_TO_GET_ISSUER_CERT`

The issuer certificate of a looked up certificate could not be found. This
normally means the list of trusted certificates is not complete.

#### `UNABLE_TO_GET_ISSUER_CERT_LOCALLY`

The certificate’s issuer is not known. This is the case if the issuer is not
included in the trusted certificate list.

#### `DEPTH_ZERO_SELF_SIGNED_CERT`

The passed certificate is self-signed and the same certificate cannot be found
in the list of trusted certificates.

#### `SELF_SIGNED_CERT_IN_CHAIN`

The certificate’s issuer is not known. This is the case if the issuer is not
included in the trusted certificate list.

#### `CERT_CHAIN_TOO_LONG`

The certificate chain length is greater than the maximum depth.

#### `UNABLE_TO_GET_CRL`

The CRL reference by the certificate could not be found.

[Contents](../README.md) | [Previous: Errors, part 3 of 5](22-errors-part3.md) | [Next: Errors, part 5 of 5](22-errors-part5.md)
