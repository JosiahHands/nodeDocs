[Contents](../README.md) | [Previous: Errors, part 1 of 5](22-errors-part1.md) | [Next: Errors, part 3 of 5](22-errors-part3.md)

Source: [https://nodejs.org/docs/latest/api/errors.html](https://nodejs.org/docs/latest/api/errors.html)

Part 2 of 5 of this section.

# Errors (part 2 of 5)

### `ERR_FS_CP_DIR_TO_NON_DIR`

Added in: v16.7.0

An attempt was made to copy a directory to a non-directory (file, symlink,
etc.) using [`fs.cp()`](24-fs-part3.md#fscpsrc-dest-options-callback).

### `ERR_FS_CP_EEXIST`

Added in: v16.7.0

An attempt was made to copy over a file that already existed with
[`fs.cp()`](24-fs-part3.md#fscpsrc-dest-options-callback), with the `force` and `errorOnExist` set to `true`.

### `ERR_FS_CP_EINVAL`

Added in: v16.7.0

When using [`fs.cp()`](24-fs-part3.md#fscpsrc-dest-options-callback), `src` or `dest` pointed to an invalid path.

### `ERR_FS_CP_FIFO_PIPE`

Added in: v16.7.0

An attempt was made to copy a named pipe with [`fs.cp()`](24-fs-part3.md#fscpsrc-dest-options-callback).

### `ERR_FS_CP_NON_DIR_TO_DIR`

Added in: v16.7.0

An attempt was made to copy a non-directory (file, symlink, etc.) to a directory
using [`fs.cp()`](24-fs-part3.md#fscpsrc-dest-options-callback).

### `ERR_FS_CP_SOCKET`

Added in: v16.7.0

An attempt was made to copy to a socket with [`fs.cp()`](24-fs-part3.md#fscpsrc-dest-options-callback).

### `ERR_FS_CP_SYMLINK_TO_SUBDIRECTORY`

Added in: v16.7.0

When using [`fs.cp()`](24-fs-part3.md#fscpsrc-dest-options-callback), a symlink in `dest` pointed to a subdirectory
of `src`.

### `ERR_FS_CP_UNKNOWN`

Added in: v16.7.0

An attempt was made to copy to an unknown file type with [`fs.cp()`](24-fs-part3.md#fscpsrc-dest-options-callback).

### `ERR_FS_EISDIR`

Path is a directory.

### `ERR_FS_FILE_TOO_LARGE`

An attempt was made to read a file larger than the supported 2 GiB limit for
`fs.readFile()`. This is not a limitation of `Buffer`, but an internal I/O constraint.
For handling larger files, consider using `fs.createReadStream()` to read the
file in chunks.

### `ERR_FS_WATCH_QUEUE_OVERFLOW`

The number of file system events queued without being handled exceeded the size specified in
`maxQueue` in `fs.watch()`.

### `ERR_HTTP2_ALTSVC_INVALID_ORIGIN`

HTTP/2 ALTSVC frames require a valid origin.

### `ERR_HTTP2_ALTSVC_LENGTH`

HTTP/2 ALTSVC frames are limited to a maximum of 16,382 payload bytes.

### `ERR_HTTP2_CONNECT_AUTHORITY`

For HTTP/2 requests using the `CONNECT` method, the `:authority` pseudo-header
is required.

### `ERR_HTTP2_CONNECT_PATH`

For HTTP/2 requests using the `CONNECT` method, the `:path` pseudo-header is
forbidden.

### `ERR_HTTP2_CONNECT_SCHEME`

For HTTP/2 requests using the `CONNECT` method, the `:scheme` pseudo-header is
forbidden.

### `ERR_HTTP2_ERROR`

A non-specific HTTP/2 error has occurred.

### `ERR_HTTP2_GOAWAY_SESSION`

New HTTP/2 Streams may not be opened after the `Http2Session` has received a
`GOAWAY` frame from the connected peer.

### `ERR_HTTP2_HEADERS_AFTER_RESPOND`

Additional headers were specified after an HTTP/2 response was initiated.

### `ERR_HTTP2_HEADERS_SENT`

An attempt was made to send multiple response headers.

### `ERR_HTTP2_HEADER_SINGLE_VALUE`

Multiple values were provided for an HTTP/2 header field that was required to
have only a single value.

### `ERR_HTTP2_INFO_STATUS_NOT_ALLOWED`

Informational HTTP status codes (`1xx`) may not be set as the response status
code on HTTP/2 responses.

### `ERR_HTTP2_INVALID_CONNECTION_HEADERS`

HTTP/1 connection specific headers are forbidden to be used in HTTP/2
requests and responses.

### `ERR_HTTP2_INVALID_HEADER_VALUE`

An invalid HTTP/2 header value was specified.

### `ERR_HTTP2_INVALID_INFO_STATUS`

An invalid HTTP informational status code has been specified. Informational
status codes must be an integer between `100` and `199` (inclusive).

### `ERR_HTTP2_INVALID_ORIGIN`

HTTP/2 `ORIGIN` frames require a valid origin.

### `ERR_HTTP2_INVALID_PACKED_SETTINGS_LENGTH`

Input `Buffer` and `Uint8Array` instances passed to the
`http2.getUnpackedSettings()` API must have a length that is a multiple of
six.

### `ERR_HTTP2_INVALID_PSEUDOHEADER`

Only valid HTTP/2 pseudoheaders (`:status`, `:path`, `:authority`, `:scheme`,
and `:method`) may be used.

### `ERR_HTTP2_INVALID_SESSION`

An action was performed on an `Http2Session` object that had already been
destroyed.

### `ERR_HTTP2_INVALID_SETTING_VALUE`

An invalid value has been specified for an HTTP/2 setting.

### `ERR_HTTP2_INVALID_STREAM`

An operation was performed on a stream that had already been destroyed.

### `ERR_HTTP2_MAX_PENDING_SETTINGS_ACK`

Whenever an HTTP/2 `SETTINGS` frame is sent to a connected peer, the peer is
required to send an acknowledgment that it has received and applied the new
`SETTINGS`. By default, a maximum number of unacknowledged `SETTINGS` frames may
be sent at any given time. This error code is used when that limit has been
reached.

### `ERR_HTTP2_NESTED_PUSH`

An attempt was made to initiate a new push stream from within a push stream.
Nested push streams are not permitted.

### `ERR_HTTP2_NO_MEM`

Out of memory when using the `http2session.setLocalWindowSize(windowSize)` API.

### `ERR_HTTP2_NO_SOCKET_MANIPULATION`

An attempt was made to directly manipulate (read, write, pause, resume, etc.) a
socket attached to an `Http2Session`.

### `ERR_HTTP2_ORIGIN_LENGTH`

HTTP/2 `ORIGIN` frames are limited to a length of 16382 bytes.

### `ERR_HTTP2_OUT_OF_STREAMS`

The number of streams created on a single HTTP/2 session reached the maximum
limit.

### `ERR_HTTP2_PAYLOAD_FORBIDDEN`

A message payload was specified for an HTTP response code for which a payload is
forbidden.

### `ERR_HTTP2_PING_CANCEL`

An HTTP/2 ping was canceled.

### `ERR_HTTP2_PING_LENGTH`

HTTP/2 ping payloads must be exactly 8 bytes in length.

### `ERR_HTTP2_PSEUDOHEADER_NOT_ALLOWED`

An HTTP/2 pseudo-header has been used inappropriately. Pseudo-headers are header
key names that begin with the `:` prefix.

### `ERR_HTTP2_PUSH_DISABLED`

An attempt was made to create a push stream, which had been disabled by the
client.

### `ERR_HTTP2_SEND_FILE`

An attempt was made to use the `Http2Stream.prototype.responseWithFile()` API to
send a directory.

### `ERR_HTTP2_SEND_FILE_NOSEEK`

An attempt was made to use the `Http2Stream.prototype.responseWithFile()` API to
send something other than a regular file, but `offset` or `length` options were
provided.

### `ERR_HTTP2_SESSION_ERROR`

The `Http2Session` closed with a non-zero error code.

### `ERR_HTTP2_SETTINGS_CANCEL`

The `Http2Session` settings canceled.

### `ERR_HTTP2_SOCKET_BOUND`

An attempt was made to connect a `Http2Session` object to a `net.Socket` or
`tls.TLSSocket` that had already been bound to another `Http2Session` object.

### `ERR_HTTP2_SOCKET_UNBOUND`

An attempt was made to use the `socket` property of an `Http2Session` that
has already been closed.

### `ERR_HTTP2_STATUS_101`

Use of the `101` Informational status code is forbidden in HTTP/2.

### `ERR_HTTP2_STATUS_INVALID`

An invalid HTTP status code has been specified. Status codes must be an integer
between `100` and `599` (inclusive).

### `ERR_HTTP2_STREAM_CANCEL`

An `Http2Stream` was destroyed before any data was transmitted to the connected
peer.

### `ERR_HTTP2_STREAM_ERROR`

A non-zero error code was been specified in an `RST_STREAM` frame.

### `ERR_HTTP2_STREAM_SELF_DEPENDENCY`

When setting the priority for an HTTP/2 stream, the stream may be marked as
a dependency for a parent stream. This error code is used when an attempt is
made to mark a stream and dependent of itself.

### `ERR_HTTP2_TOO_MANY_CUSTOM_SETTINGS`

The number of supported custom settings (10) has been exceeded.

### `ERR_HTTP2_TOO_MANY_INVALID_FRAMES`

Added in: v15.14.0

The limit of acceptable invalid HTTP/2 protocol frames sent by the peer,
as specified through the `maxSessionInvalidFrames` option, has been exceeded.

### `ERR_HTTP2_TOO_MANY_ORIGINS`

Added in: v26.3.1

The number of uniq origin sent by the server has exceeded the value defined in
`options.maxOriginSetSize`.

### `ERR_HTTP2_TRAILERS_ALREADY_SENT`

Trailing headers have already been sent on the `Http2Stream`.

### `ERR_HTTP2_TRAILERS_NOT_READY`

The `http2stream.sendTrailers()` method cannot be called until after the
`'wantTrailers'` event is emitted on an `Http2Stream` object. The
`'wantTrailers'` event will only be emitted if the `waitForTrailers` option
is set for the `Http2Stream`.

### `ERR_HTTP2_UNSUPPORTED_PROTOCOL`

`http2.connect()` was passed a URL that uses any protocol other than `http:` or
`https:`.

### `ERR_HTTP_BODY_NOT_ALLOWED`

An error is thrown when writing to an HTTP response which does not allow
contents.

### `ERR_HTTP_CONTENT_LENGTH_MISMATCH`

Response body size doesn't match with the specified content-length header value.

### `ERR_HTTP_HEADERS_SENT`

An attempt was made to add more headers after the headers had already been sent.

### `ERR_HTTP_INVALID_HEADER_VALUE`

An invalid HTTP header value was specified.

### `ERR_HTTP_INVALID_STATUS_CODE`

Status code was outside the regular status code range (100-999).

### `ERR_HTTP_REQUEST_TIMEOUT`

The client has not sent the entire request within the allowed time.

### `ERR_HTTP_SOCKET_ASSIGNED`

The given [`ServerResponse`](27-http-part2.md#class-httpserverresponse) was already assigned a socket.

### `ERR_HTTP_SOCKET_ENCODING`

Changing the socket encoding is not allowed per [RFC 7230 Section 3](https://tools.ietf.org/html/rfc7230#section-3).

### `ERR_HTTP_TRAILER_INVALID`

The `Trailer` header was set even though the transfer encoding does not support
that.

### `ERR_ILLEGAL_CONSTRUCTOR`

An attempt was made to construct an object using a non-public constructor.

### `ERR_IMPORT_ATTRIBUTE_MISSING`

Added in: v21.1.0

An import attribute is missing, preventing the specified module to be imported.

### `ERR_IMPORT_ATTRIBUTE_TYPE_INCOMPATIBLE`

Added in: v21.1.0

An import `type` attribute was provided, but the specified module is of a
different type.

### `ERR_IMPORT_ATTRIBUTE_UNSUPPORTED`

Added in: v21.0.0, v20.10.0, v18.19.0

An import attribute is not supported by this version of Node.js.

### `ERR_INCOMPATIBLE_OPTION_PAIR`

An option pair is incompatible with each other and cannot be used at the same
time.

### `ERR_INPUT_TYPE_NOT_ALLOWED`

The `--input-type` flag was used to attempt to execute a file. This flag can
only be used with input via `--eval`, `--print`, or `STDIN`.

### `ERR_INSPECTOR_ALREADY_ACTIVATED`

While using the `node:inspector` module, an attempt was made to activate the
inspector when it already started to listen on a port. Use `inspector.close()`
before activating it on a different address.

### `ERR_INSPECTOR_ALREADY_CONNECTED`

While using the `node:inspector` module, an attempt was made to connect when the
inspector was already connected.

### `ERR_INSPECTOR_CLOSED`

While using the `node:inspector` module, an attempt was made to use the
inspector after the session had already closed.

### `ERR_INSPECTOR_COMMAND`

An error occurred while issuing a command via the `node:inspector` module.

### `ERR_INSPECTOR_NOT_ACTIVE`

The `inspector` is not active when `inspector.waitForDebugger()` is called.

### `ERR_INSPECTOR_NOT_AVAILABLE`

The `node:inspector` module is not available for use.

### `ERR_INSPECTOR_NOT_CONNECTED`

While using the `node:inspector` module, an attempt was made to use the
inspector before it was connected.

### `ERR_INSPECTOR_NOT_WORKER`

An API was called on the main thread that can only be used from
the worker thread.

### `ERR_INTERNAL_ASSERTION`

There was a bug in Node.js or incorrect usage of Node.js internals.
To fix the error, open an issue at <https://github.com/nodejs/node/issues>.

### `ERR_INVALID_ADDRESS`

The provided address is not understood by the Node.js API.

### `ERR_INVALID_ADDRESS_FAMILY`

The provided address family is not understood by the Node.js API.

### `ERR_INVALID_ARG_TYPE`

An argument of the wrong type was passed to a Node.js API.

### `ERR_INVALID_ARG_VALUE`

An invalid or unsupported value was passed for a given argument.

### `ERR_INVALID_ASYNC_ID`

An invalid `asyncId` or `triggerAsyncId` was passed using `AsyncHooks`. An id
less than -1 should never happen.

### `ERR_INVALID_BUFFER_SIZE`

A swap was performed on a `Buffer` but its size was not compatible with the
operation.

### `ERR_INVALID_CHAR`

Invalid characters were detected in headers.

### `ERR_INVALID_CURSOR_POS`

A cursor on a given stream cannot be moved to a specified row without a
specified column.

### `ERR_INVALID_FD`

A file descriptor ('fd') was not valid (e.g. it was a negative value).

### `ERR_INVALID_FD_TYPE`

A file descriptor ('fd') type was not valid.

### `ERR_INVALID_FILE_URL_HOST`

A Node.js API that consumes `file:` URLs (such as certain functions in the
[`fs`](24-fs-part1.md) module) encountered a file URL with an incompatible host. This
situation can only occur on Unix-like systems where only `localhost` or an empty
host is supported.

### `ERR_INVALID_FILE_URL_PATH`

A Node.js API that consumes `file:` URLs (such as certain functions in the
[`fs`](24-fs-part1.md) module) encountered a file URL with an incompatible path. The exact
semantics for determining whether a path can be used is platform-dependent.

The thrown error object includes an `input` property that contains the URL object
of the invalid `file:` URL.

### `ERR_INVALID_HANDLE_TYPE`

An attempt was made to send an unsupported "handle" over an IPC communication
channel to a child process. See [`subprocess.send()`](11-child-process.md#subprocesssendmessage-sendhandle-options-callback) and [`process.send()`](43-process-part3.md#processsendmessage-sendhandle-options-callback)
for more information.

### `ERR_INVALID_HTTP_TOKEN`

An invalid HTTP token was supplied.

### `ERR_INVALID_IP_ADDRESS`

An IP address is not valid.

### `ERR_INVALID_MIME_SYNTAX`

The syntax of a MIME is not valid.

### `ERR_INVALID_MODULE`

Added in: v15.0.0, v14.18.0

An attempt was made to load a module that does not exist or was otherwise not
valid.

### `ERR_INVALID_MODULE_SPECIFIER`

The imported module string is an invalid URL, package name, or package subpath
specifier.

### `ERR_INVALID_OBJECT_DEFINE_PROPERTY`

An error occurred while setting an invalid attribute on the property of
an object.

### `ERR_INVALID_PACKAGE_CONFIG`

An invalid [`package.json`](36-packages.md#nodejs-packagejson-field-definitions) file failed parsing.

### `ERR_INVALID_PACKAGE_TARGET`

The `package.json` [`"exports"`](36-packages.md#exports) field contains an invalid target mapping
value for the attempted module resolution.

### `ERR_INVALID_PROTOCOL`

An invalid `options.protocol` was passed to `http.request()`.

### `ERR_INVALID_REPL_EVAL_CONFIG`

Both `breakEvalOnSigint` and `eval` options were set in the [`REPL`](47-repl.md) config,
which is not supported.

### `ERR_INVALID_REPL_INPUT`

The input may not be used in the [`REPL`](47-repl.md). The conditions under which this
error is used are described in the [`REPL`](47-repl.md) documentation.

### `ERR_INVALID_RETURN_PROPERTY`

Thrown in case a function option does not provide a valid value for one of its
returned object properties on execution.

### `ERR_INVALID_RETURN_PROPERTY_VALUE`

Thrown in case a function option does not provide an expected value
type for one of its returned object properties on execution.

### `ERR_INVALID_RETURN_VALUE`

Thrown in case a function option does not return an expected value
type on execution, such as when a function is expected to return a promise.

### `ERR_INVALID_STATE`

Added in: v15.0.0

Indicates that an operation cannot be completed due to an invalid state.
For instance, an object may have already been destroyed, or may be
performing another operation.

### `ERR_INVALID_SYNC_FORK_INPUT`

A `Buffer`, `TypedArray`, `DataView`, or `string` was provided as stdio input to
an asynchronous fork. See the documentation for the [`child_process`](11-child-process.md) module
for more information.

### `ERR_INVALID_THIS`

A Node.js API function was called with an incompatible `this` value.

```js
const urlSearchParams = new URLSearchParams('foo=bar&baz=new');

const buf = Buffer.alloc(1);
urlSearchParams.has.call(buf, 'foo');
// Throws a TypeError with code 'ERR_INVALID_THIS'
```

### `ERR_INVALID_TUPLE`

An element in the `iterable` provided to the [WHATWG](59-url.md#the-whatwg-url-api)
[`URLSearchParams` constructor](59-url.md#new-urlsearchparamsiterable) did not
represent a `[name, value]` tuple – that is, if an element is not iterable, or
does not consist of exactly two elements.

### `ERR_INVALID_TYPESCRIPT_SYNTAX`

Added in: v23.0.0, v22.10.0

**History**

| Version | Changes |
| --- | --- |
| v23.7.0, v22.14.0 | This error is no longer thrown on valid yet unsupported syntax. |

The provided TypeScript syntax is not valid.

### `ERR_INVALID_URI`

An invalid URI was passed.

### `ERR_INVALID_URL`

An invalid URL was passed to the [WHATWG](59-url.md#the-whatwg-url-api) [`URL`
constructor](59-url.md#new-urlinput-base) or the legacy [`url.parse()`](59-url.md#urlparseurlstring-parsequerystring-slashesdenotehost) to be parsed.
The thrown error object typically has an additional property `'input'` that
contains the URL that failed to parse.

### `ERR_INVALID_URL_PATTERN`

An invalid URLPattern was passed to the [WHATWG](59-url.md#the-whatwg-url-api)
[`URLPattern` constructor](59-url.md#new-urlpatternstring-baseurl-options) to be parsed.

### `ERR_INVALID_URL_SCHEME`

An attempt was made to use a URL of an incompatible scheme (protocol) for a
specific purpose. It is only used in the [WHATWG URL API](59-url.md#the-whatwg-url-api) support in the
[`fs`](24-fs-part1.md) module (which only accepts URLs with `'file'` scheme), but may be used
in other Node.js APIs as well in the future.

### `ERR_IPC_CHANNEL_CLOSED`

An attempt was made to use an IPC communication channel that was already closed.

### `ERR_IPC_DISCONNECTED`

An attempt was made to disconnect an IPC communication channel that was already
disconnected. See the documentation for the [`child_process`](11-child-process.md) module
for more information.

[Contents](../README.md) | [Previous: Errors, part 1 of 5](22-errors-part1.md) | [Next: Errors, part 3 of 5](22-errors-part3.md)
