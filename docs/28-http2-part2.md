[Contents](../README.md) | [Previous: HTTP/2, part 1 of 4](28-http2-part1.md) | [Next: HTTP/2, part 3 of 4](28-http2-part3.md)

Source: [https://nodejs.org/docs/latest/api/http2.html](https://nodejs.org/docs/latest/api/http2.html)

Part 2 of 4 of this section.

# HTTP/2 (part 2 of 4)

#### `http2stream.pushStream(headers[, options], callback)`

Added in: v8.4.0

**History**

| Version | Changes |
| --- | --- |
| v18.0.0 | Passing an invalid callback to the `callback` argument now throws `ERR_INVALID_ARG_TYPE` instead of `ERR_INVALID_CALLBACK`. |

- `headers` [`<HTTP/2 Headers Object>`](28-http2-part3.md#headers-object)
- `options` [`<Object>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Object)
  - `exclusive` [`<boolean>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#boolean_type) When `true` and `parent` identifies a parent Stream,
    the created stream is made the sole direct dependency of the parent, with
    all other existing dependents made a dependent of the newly created stream.
    **Default:** `false`.
  - `parent` [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) Specifies the numeric identifier of a stream the newly
    created stream is dependent on.
- `callback` [`<Function>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Function) Callback that is called once the push stream has been
  initiated.
  - `err` [`<Error>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Error)
  - `pushStream` [`<ServerHttp2Stream>`](28-http2-part1.md#class-serverhttp2stream) The returned `pushStream` object.
  - `headers` [`<HTTP/2 Headers Object>`](28-http2-part3.md#headers-object) Headers object the `pushStream` was
    initiated with.

Initiates a push stream. The callback is invoked with the new `Http2Stream`
instance created for the push stream passed as the second argument, or an
`Error` passed as the first argument.

*ES module version:*

```js
import { createServer } from 'node:http2';
const server = createServer();
server.on('stream', (stream) => {
  stream.respond({ ':status': 200 });
  stream.pushStream({ ':path': '/' }, (err, pushStream, headers) => {
    if (err) throw err;
    pushStream.respond({ ':status': 200 });
    pushStream.end('some pushed data');
  });
  stream.end('some data');
});
```

*CommonJS version:*

```js
const http2 = require('node:http2');
const server = http2.createServer();
server.on('stream', (stream) => {
  stream.respond({ ':status': 200 });
  stream.pushStream({ ':path': '/' }, (err, pushStream, headers) => {
    if (err) throw err;
    pushStream.respond({ ':status': 200 });
    pushStream.end('some pushed data');
  });
  stream.end('some data');
});
```

Setting the weight of a push stream is not allowed in the `HEADERS` frame. Pass
a `weight` value to `http2stream.priority` with the `silent` option set to
`true` to enable server-side bandwidth balancing between concurrent streams.

Calling `http2stream.pushStream()` from within a pushed stream is not permitted
and will throw an error.

#### `http2stream.respond([headers[, options]])`

Added in: v8.4.0

**History**

| Version | Changes |
| --- | --- |
| v24.7.0, v22.20.0 | Allow passing headers in raw array format. |
| v14.5.0, v12.19.0 | Allow explicitly setting date headers. |

- `headers` [`<HTTP/2 Headers Object>`](28-http2-part3.md#headers-object) | [`<HTTP/2 Raw Headers>`](28-http2-part3.md#raw-headers)
- `options` [`<Object>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Object)
  - `endStream` [`<boolean>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#boolean_type) Set to `true` to indicate that the response will not
    include payload data.
  - `waitForTrailers` [`<boolean>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#boolean_type) When `true`, the `Http2Stream` will emit the
    `'wantTrailers'` event after the final `DATA` frame has been sent.

*ES module version:*

```js
import { createServer } from 'node:http2';
const server = createServer();
server.on('stream', (stream) => {
  stream.respond({ ':status': 200 });
  stream.end('some data');
});
```

*CommonJS version:*

```js
const http2 = require('node:http2');
const server = http2.createServer();
server.on('stream', (stream) => {
  stream.respond({ ':status': 200 });
  stream.end('some data');
});
```

Initiates a response. When the `options.waitForTrailers` option is set, the
`'wantTrailers'` event will be emitted immediately after queuing the last chunk
of payload data to be sent. The `http2stream.sendTrailers()` method can then be
used to send trailing header fields to the peer.

When `options.waitForTrailers` is set, the `Http2Stream` will not automatically
close when the final `DATA` frame is transmitted. User code must call either
`http2stream.sendTrailers()` or `http2stream.close()` to close the
`Http2Stream`.

*ES module version:*

```js
import { createServer } from 'node:http2';
const server = createServer();
server.on('stream', (stream) => {
  stream.respond({ ':status': 200 }, { waitForTrailers: true });
  stream.on('wantTrailers', () => {
    stream.sendTrailers({ ABC: 'some value to send' });
  });
  stream.end('some data');
});
```

*CommonJS version:*

```js
const http2 = require('node:http2');
const server = http2.createServer();
server.on('stream', (stream) => {
  stream.respond({ ':status': 200 }, { waitForTrailers: true });
  stream.on('wantTrailers', () => {
    stream.sendTrailers({ ABC: 'some value to send' });
  });
  stream.end('some data');
});
```

#### `http2stream.respondWithFD(fd[, headers[, options]])`

Added in: v8.4.0

**History**

| Version | Changes |
| --- | --- |
| v14.5.0, v12.19.0 | Allow explicitly setting date headers. |
| v12.12.0 | The `fd` option may now be a `FileHandle`. |
| v10.0.0 | Any readable file descriptor, not necessarily for a regular file, is supported now. |

- `fd` [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) | [`<FileHandle>`](24-fs-part1.md#class-filehandle) A readable file descriptor.
- `headers` [`<HTTP/2 Headers Object>`](28-http2-part3.md#headers-object)
- `options` [`<Object>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Object)
  - `statCheck` [`<Function>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Function)
  - `waitForTrailers` [`<boolean>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#boolean_type) When `true`, the `Http2Stream` will emit the
    `'wantTrailers'` event after the final `DATA` frame has been sent.
  - `offset` [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) The offset position at which to begin reading.
  - `length` [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) The amount of data from the fd to send.

Initiates a response whose data is read from the given file descriptor. No
validation is performed on the given file descriptor. If an error occurs while
attempting to read data using the file descriptor, the `Http2Stream` will be
closed using an `RST_STREAM` frame using the standard `INTERNAL_ERROR` code.

When used, the `Http2Stream` object's `Duplex` interface will be closed
automatically.

*ES module version:*

```js
import { createServer } from 'node:http2';
import { openSync, fstatSync, closeSync } from 'node:fs';

const server = createServer();
server.on('stream', (stream) => {
  const fd = openSync('/some/file', 'r');

  const stat = fstatSync(fd);
  const headers = {
    'content-length': stat.size,
    'last-modified': stat.mtime.toUTCString(),
    'content-type': 'text/plain; charset=utf-8',
  };
  stream.respondWithFD(fd, headers);
  stream.on('close', () => closeSync(fd));
});
```

*CommonJS version:*

```js
const http2 = require('node:http2');
const fs = require('node:fs');

const server = http2.createServer();
server.on('stream', (stream) => {
  const fd = fs.openSync('/some/file', 'r');

  const stat = fs.fstatSync(fd);
  const headers = {
    'content-length': stat.size,
    'last-modified': stat.mtime.toUTCString(),
    'content-type': 'text/plain; charset=utf-8',
  };
  stream.respondWithFD(fd, headers);
  stream.on('close', () => fs.closeSync(fd));
});
```

The optional `options.statCheck` function may be specified to give user code
an opportunity to set additional content headers based on the `fs.Stat` details
of the given fd. If the `statCheck` function is provided, the
`http2stream.respondWithFD()` method will perform an `fs.fstat()` call to
collect details on the provided file descriptor.

The `offset` and `length` options may be used to limit the response to a
specific range subset. This can be used, for instance, to support HTTP Range
requests.

The file descriptor or `FileHandle` is not closed when the stream is closed,
so it will need to be closed manually once it is no longer needed.
Using the same file descriptor concurrently for multiple streams
is not supported and may result in data loss. Re-using a file descriptor
after a stream has finished is supported.

When the `options.waitForTrailers` option is set, the `'wantTrailers'` event
will be emitted immediately after queuing the last chunk of payload data to be
sent. The `http2stream.sendTrailers()` method can then be used to send trailing
header fields to the peer.

When `options.waitForTrailers` is set, the `Http2Stream` will not automatically
close when the final `DATA` frame is transmitted. User code *must* call either
`http2stream.sendTrailers()` or `http2stream.close()` to close the
`Http2Stream`.

*ES module version:*

```js
import { createServer } from 'node:http2';
import { openSync, fstatSync, closeSync } from 'node:fs';

const server = createServer();
server.on('stream', (stream) => {
  const fd = openSync('/some/file', 'r');

  const stat = fstatSync(fd);
  const headers = {
    'content-length': stat.size,
    'last-modified': stat.mtime.toUTCString(),
    'content-type': 'text/plain; charset=utf-8',
  };
  stream.respondWithFD(fd, headers, { waitForTrailers: true });
  stream.on('wantTrailers', () => {
    stream.sendTrailers({ ABC: 'some value to send' });
  });

  stream.on('close', () => closeSync(fd));
});
```

*CommonJS version:*

```js
const http2 = require('node:http2');
const fs = require('node:fs');

const server = http2.createServer();
server.on('stream', (stream) => {
  const fd = fs.openSync('/some/file', 'r');

  const stat = fs.fstatSync(fd);
  const headers = {
    'content-length': stat.size,
    'last-modified': stat.mtime.toUTCString(),
    'content-type': 'text/plain; charset=utf-8',
  };
  stream.respondWithFD(fd, headers, { waitForTrailers: true });
  stream.on('wantTrailers', () => {
    stream.sendTrailers({ ABC: 'some value to send' });
  });

  stream.on('close', () => fs.closeSync(fd));
});
```

#### `http2stream.respondWithFile(path[, headers[, options]])`

Added in: v8.4.0

**History**

| Version | Changes |
| --- | --- |
| v14.5.0, v12.19.0 | Allow explicitly setting date headers. |
| v10.0.0 | Any readable file, not necessarily a regular file, is supported now. |

- `path` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) | [`<Buffer>`](07-buffer-part1.md#class-buffer) | [`<URL>`](59-url.md#the-whatwg-url-api)
- `headers` [`<HTTP/2 Headers Object>`](28-http2-part3.md#headers-object)
- `options` [`<Object>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Object)
  - `statCheck` [`<Function>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Function)
  - `onError` [`<Function>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Function) Callback function invoked in the case of an
    error before send.
  - `waitForTrailers` [`<boolean>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#boolean_type) When `true`, the `Http2Stream` will emit the
    `'wantTrailers'` event after the final `DATA` frame has been sent.
  - `offset` [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) The offset position at which to begin reading.
  - `length` [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) The amount of data from the fd to send.

Sends a regular file as the response. The `path` must specify a regular file
or an `'error'` event will be emitted on the `Http2Stream` object.

When used, the `Http2Stream` object's `Duplex` interface will be closed
automatically.

The optional `options.statCheck` function may be specified to give user code
an opportunity to set additional content headers based on the `fs.Stat` details
of the given file:

If an error occurs while attempting to read the file data, the `Http2Stream`
will be closed using an `RST_STREAM` frame using the standard `INTERNAL_ERROR`
code. If the `onError` callback is defined, then it will be called. Otherwise
the stream will be destroyed.

Example using a file path:

*ES module version:*

```js
import { createServer } from 'node:http2';
const server = createServer();
server.on('stream', (stream) => {
  function statCheck(stat, headers) {
    headers['last-modified'] = stat.mtime.toUTCString();
  }

  function onError(err) {
    // stream.respond() can throw if the stream has been destroyed by
    // the other side.
    try {
      if (err.code === 'ENOENT') {
        stream.respond({ ':status': 404 });
      } else {
        stream.respond({ ':status': 500 });
      }
    } catch (err) {
      // Perform actual error handling.
      console.error(err);
    }
    stream.end();
  }

  stream.respondWithFile('/some/file',
                         { 'content-type': 'text/plain; charset=utf-8' },
                         { statCheck, onError });
});
```

*CommonJS version:*

```js
const http2 = require('node:http2');
const server = http2.createServer();
server.on('stream', (stream) => {
  function statCheck(stat, headers) {
    headers['last-modified'] = stat.mtime.toUTCString();
  }

  function onError(err) {
    // stream.respond() can throw if the stream has been destroyed by
    // the other side.
    try {
      if (err.code === 'ENOENT') {
        stream.respond({ ':status': 404 });
      } else {
        stream.respond({ ':status': 500 });
      }
    } catch (err) {
      // Perform actual error handling.
      console.error(err);
    }
    stream.end();
  }

  stream.respondWithFile('/some/file',
                         { 'content-type': 'text/plain; charset=utf-8' },
                         { statCheck, onError });
});
```

The `options.statCheck` function may also be used to cancel the send operation
by returning `false`. For instance, a conditional request may check the stat
results to determine if the file has been modified to return an appropriate
`304` response:

*ES module version:*

```js
import { createServer } from 'node:http2';
const server = createServer();
server.on('stream', (stream) => {
  function statCheck(stat, headers) {
    // Check the stat here...
    stream.respond({ ':status': 304 });
    return false; // Cancel the send operation
  }
  stream.respondWithFile('/some/file',
                         { 'content-type': 'text/plain; charset=utf-8' },
                         { statCheck });
});
```

*CommonJS version:*

```js
const http2 = require('node:http2');
const server = http2.createServer();
server.on('stream', (stream) => {
  function statCheck(stat, headers) {
    // Check the stat here...
    stream.respond({ ':status': 304 });
    return false; // Cancel the send operation
  }
  stream.respondWithFile('/some/file',
                         { 'content-type': 'text/plain; charset=utf-8' },
                         { statCheck });
});
```

The `content-length` header field will be automatically set.

The `offset` and `length` options may be used to limit the response to a
specific range subset. This can be used, for instance, to support HTTP Range
requests.

The `options.onError` function may also be used to handle all the errors
that could happen before the delivery of the file is initiated. The
default behavior is to destroy the stream.

When the `options.waitForTrailers` option is set, the `'wantTrailers'` event
will be emitted immediately after queuing the last chunk of payload data to be
sent. The `http2stream.sendTrailers()` method can then be used to send trailing
header fields to the peer.

When `options.waitForTrailers` is set, the `Http2Stream` will not automatically
close when the final `DATA` frame is transmitted. User code must call either
`http2stream.sendTrailers()` or `http2stream.close()` to close the
`Http2Stream`.

*ES module version:*

```js
import { createServer } from 'node:http2';
const server = createServer();
server.on('stream', (stream) => {
  stream.respondWithFile('/some/file',
                         { 'content-type': 'text/plain; charset=utf-8' },
                         { waitForTrailers: true });
  stream.on('wantTrailers', () => {
    stream.sendTrailers({ ABC: 'some value to send' });
  });
});
```

*CommonJS version:*

```js
const http2 = require('node:http2');
const server = http2.createServer();
server.on('stream', (stream) => {
  stream.respondWithFile('/some/file',
                         { 'content-type': 'text/plain; charset=utf-8' },
                         { waitForTrailers: true });
  stream.on('wantTrailers', () => {
    stream.sendTrailers({ ABC: 'some value to send' });
  });
});
```

### Class: `Http2Server`

Added in: v8.4.0

- Extends: [`<net.Server>`](38-net.md#class-netserver)

Instances of `Http2Server` are created using the `http2.createServer()`
function. The `Http2Server` class is not exported directly by the
`node:http2` module.

#### Event: `'checkContinue'`

Added in: v8.5.0

- `request` [`<http2.Http2ServerRequest>`](28-http2-part3.md#class-http2http2serverrequest)
- `response` [`<http2.Http2ServerResponse>`](28-http2-part3.md#class-http2http2serverresponse)

If a [`'request'`](#event-request) listener is registered or [`http2.createServer()`](#http2createserveroptions-onrequesthandler) is
supplied a callback function, the `'checkContinue'` event is emitted each time
a request with an HTTP `Expect: 100-continue` is received. If this event is
not listened for, the server will automatically respond with a status
`100 Continue` as appropriate.

Handling this event involves calling [`response.writeContinue()`](28-http2-part3.md#responsewritecontinue) if the
client should continue to send the request body, or generating an appropriate
HTTP response (e.g. 400 Bad Request) if the client should not continue to send
the request body.

When this event is emitted and handled, the [`'request'`](#event-request) event will
not be emitted.

#### Event: `'connection'`

Added in: v8.4.0

- `socket` [`<stream.Duplex>`](51-stream-part2.md#class-streamduplex)

This event is emitted when a new TCP stream is established. `socket` is
typically an object of type [`net.Socket`](38-net.md#class-netsocket). Usually users will not want to
access this event.

This event can also be explicitly emitted by users to inject connections
into the HTTP server. In that case, any [`Duplex`](51-stream-part2.md#class-streamduplex) stream can be passed.

#### Event: `'request'`

Added in: v8.4.0

- `request` [`<http2.Http2ServerRequest>`](28-http2-part3.md#class-http2http2serverrequest)
- `response` [`<http2.Http2ServerResponse>`](28-http2-part3.md#class-http2http2serverresponse)

Emitted each time there is a request. There may be multiple requests
per session. See the [Compatibility API](28-http2-part3.md#compatibility-api).

#### Event: `'session'`

Added in: v8.4.0

- `session` [`<ServerHttp2Session>`](28-http2-part1.md#class-serverhttp2session)

The `'session'` event is emitted when a new `Http2Session` is created by the
`Http2Server`.

#### Event: `'sessionError'`

Added in: v8.4.0

- `error` [`<Error>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Error)
- `session` [`<ServerHttp2Session>`](28-http2-part1.md#class-serverhttp2session)

The `'sessionError'` event is emitted when an `'error'` event is emitted by
an `Http2Session` object associated with the `Http2Server`.

#### Event: `'stream'`

Added in: v8.4.0

- `stream` [`<Http2Stream>`](28-http2-part1.md#class-http2stream) A reference to the stream
- `headers` [`<HTTP/2 Headers Object>`](28-http2-part3.md#headers-object) An object describing the headers
- `flags` [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) The associated numeric flags
- `rawHeaders` [`<HTTP/2 Raw Headers>`](28-http2-part3.md#raw-headers) An array containing the raw headers

The `'stream'` event is emitted when a `'stream'` event has been emitted by
an `Http2Session` associated with the server.

See also [`Http2Session`'s `'stream'` event](28-http2-part1.md#event-stream).

*ES module version:*

```js
import { createServer, constants } from 'node:http2';
const {
  HTTP2_HEADER_METHOD,
  HTTP2_HEADER_PATH,
  HTTP2_HEADER_STATUS,
  HTTP2_HEADER_CONTENT_TYPE,
} = constants;

const server = createServer();
server.on('stream', (stream, headers, flags) => {
  const method = headers[HTTP2_HEADER_METHOD];
  const path = headers[HTTP2_HEADER_PATH];
  // ...
  stream.respond({
    [HTTP2_HEADER_STATUS]: 200,
    [HTTP2_HEADER_CONTENT_TYPE]: 'text/plain; charset=utf-8',
  });
  stream.write('hello ');
  stream.end('world');
});
```

*CommonJS version:*

```js
const http2 = require('node:http2');
const {
  HTTP2_HEADER_METHOD,
  HTTP2_HEADER_PATH,
  HTTP2_HEADER_STATUS,
  HTTP2_HEADER_CONTENT_TYPE,
} = http2.constants;

const server = http2.createServer();
server.on('stream', (stream, headers, flags) => {
  const method = headers[HTTP2_HEADER_METHOD];
  const path = headers[HTTP2_HEADER_PATH];
  // ...
  stream.respond({
    [HTTP2_HEADER_STATUS]: 200,
    [HTTP2_HEADER_CONTENT_TYPE]: 'text/plain; charset=utf-8',
  });
  stream.write('hello ');
  stream.end('world');
});
```

#### Event: `'timeout'`

Added in: v8.4.0

**History**

| Version | Changes |
| --- | --- |
| v13.0.0 | The default timeout changed from 120s to 0 (no timeout). |

The `'timeout'` event is emitted when there is no activity on the Server for
a given number of milliseconds set using `http2server.setTimeout()`.
**Default:** 0 (no timeout)

#### `server.close([callback])`

Added in: v8.4.0

- `callback` [`<Function>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Function)

Stops the server from establishing new sessions and streams.

If `callback` is provided, it is not invoked until all active sessions have been
closed, although the server has already stopped allowing new sessions. See
[`net.Server.close()`](38-net.md#serverclosecallback) for more details.

#### `server[Symbol.asyncDispose]()`

Added in: v20.4.0

**History**

| Version | Changes |
| --- | --- |
| v24.2.0 | No longer experimental. |

Calls [`server.close()`](#serverclosecallback) and returns a promise that fulfills when the
server has closed.

#### `server.setTimeout([msecs][, callback])`

Added in: v8.4.0

**History**

| Version | Changes |
| --- | --- |
| v18.0.0 | Passing an invalid callback to the `callback` argument now throws `ERR_INVALID_ARG_TYPE` instead of `ERR_INVALID_CALLBACK`. |
| v13.0.0 | The default timeout changed from 120s to 0 (no timeout). |

- `msecs` [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) **Default:** 0 (no timeout)
- `callback` [`<Function>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Function)
- Returns: [`<Http2Server>`](28-http2-part2.md#class-http2server)

Used to set the timeout value for http2 server requests,
and sets a callback function that is called when there is no activity
on the `Http2Server` after `msecs` milliseconds.

The given callback is registered as a listener on the `'timeout'` event.

In case if `callback` is not a function, a new `ERR_INVALID_ARG_TYPE`
error will be thrown.

#### `server.timeout`

Added in: v8.4.0

**History**

| Version | Changes |
| --- | --- |
| v13.0.0 | The default timeout changed from 120s to 0 (no timeout). |

- Type: [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) Timeout in milliseconds. **Default:** 0 (no timeout)

The number of milliseconds of inactivity before a socket is presumed
to have timed out.

A value of `0` will disable the timeout behavior on incoming connections.

The socket timeout logic is set up on connection, so changing this
value only affects new connections to the server, not any existing connections.

#### `server.updateSettings([settings])`

Added in: v15.1.0, v14.17.0

- `settings` [`<HTTP/2 Settings Object>`](28-http2-part3.md#settings-object)

Used to update the server with the provided settings.

Throws `ERR_HTTP2_INVALID_SETTING_VALUE` for invalid `settings` values.

Throws `ERR_INVALID_ARG_TYPE` for invalid `settings` argument.

### Class: `Http2SecureServer`

Added in: v8.4.0

- Extends: [`<tls.Server>`](55-tls.md#class-tlsserver)

Instances of `Http2SecureServer` are created using the
`http2.createSecureServer()` function. The `Http2SecureServer` class is not
exported directly by the `node:http2` module.

#### Event: `'checkContinue'`

Added in: v8.5.0

- `request` [`<http2.Http2ServerRequest>`](28-http2-part3.md#class-http2http2serverrequest)
- `response` [`<http2.Http2ServerResponse>`](28-http2-part3.md#class-http2http2serverresponse)

If a [`'request'`](#event-request) listener is registered or [`http2.createSecureServer()`](#http2createsecureserveroptions-onrequesthandler)
is supplied a callback function, the `'checkContinue'` event is emitted each
time a request with an HTTP `Expect: 100-continue` is received. If this event
is not listened for, the server will automatically respond with a status
`100 Continue` as appropriate.

Handling this event involves calling [`response.writeContinue()`](28-http2-part3.md#responsewritecontinue) if the
client should continue to send the request body, or generating an appropriate
HTTP response (e.g. 400 Bad Request) if the client should not continue to send
the request body.

When this event is emitted and handled, the [`'request'`](#event-request) event will
not be emitted.

#### Event: `'connection'`

Added in: v8.4.0

- `socket` [`<stream.Duplex>`](51-stream-part2.md#class-streamduplex)

This event is emitted when a new TCP stream is established, before the TLS
handshake begins. `socket` is typically an object of type [`net.Socket`](38-net.md#class-netsocket).
Usually users will not want to access this event.

This event can also be explicitly emitted by users to inject connections
into the HTTP server. In that case, any [`Duplex`](51-stream-part2.md#class-streamduplex) stream can be passed.

#### Event: `'request'`

Added in: v8.4.0

- `request` [`<http2.Http2ServerRequest>`](28-http2-part3.md#class-http2http2serverrequest)
- `response` [`<http2.Http2ServerResponse>`](28-http2-part3.md#class-http2http2serverresponse)

Emitted each time there is a request. There may be multiple requests
per session. See the [Compatibility API](28-http2-part3.md#compatibility-api).

#### Event: `'session'`

Added in: v8.4.0

- `session` [`<ServerHttp2Session>`](28-http2-part1.md#class-serverhttp2session)

The `'session'` event is emitted when a new `Http2Session` is created by the
`Http2SecureServer`.

#### Event: `'sessionError'`

Added in: v8.4.0

- `error` [`<Error>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Error)
- `session` [`<ServerHttp2Session>`](28-http2-part1.md#class-serverhttp2session)

The `'sessionError'` event is emitted when an `'error'` event is emitted by
an `Http2Session` object associated with the `Http2SecureServer`.

#### Event: `'stream'`

Added in: v8.4.0

- `stream` [`<Http2Stream>`](28-http2-part1.md#class-http2stream) A reference to the stream
- `headers` [`<HTTP/2 Headers Object>`](28-http2-part3.md#headers-object) An object describing the headers
- `flags` [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) The associated numeric flags
- `rawHeaders` [`<HTTP/2 Raw Headers>`](28-http2-part3.md#raw-headers) An array containing the raw headers

The `'stream'` event is emitted when a `'stream'` event has been emitted by
an `Http2Session` associated with the server.

See also [`Http2Session`'s `'stream'` event](28-http2-part1.md#event-stream).

*ES module version:*

```js
import { createSecureServer, constants } from 'node:http2';
const {
  HTTP2_HEADER_METHOD,
  HTTP2_HEADER_PATH,
  HTTP2_HEADER_STATUS,
  HTTP2_HEADER_CONTENT_TYPE,
} = constants;

const options = getOptionsSomehow();

const server = createSecureServer(options);
server.on('stream', (stream, headers, flags) => {
  const method = headers[HTTP2_HEADER_METHOD];
  const path = headers[HTTP2_HEADER_PATH];
  // ...
  stream.respond({
    [HTTP2_HEADER_STATUS]: 200,
    [HTTP2_HEADER_CONTENT_TYPE]: 'text/plain; charset=utf-8',
  });
  stream.write('hello ');
  stream.end('world');
});
```

*CommonJS version:*

```js
const http2 = require('node:http2');
const {
  HTTP2_HEADER_METHOD,
  HTTP2_HEADER_PATH,
  HTTP2_HEADER_STATUS,
  HTTP2_HEADER_CONTENT_TYPE,
} = http2.constants;

const options = getOptionsSomehow();

const server = http2.createSecureServer(options);
server.on('stream', (stream, headers, flags) => {
  const method = headers[HTTP2_HEADER_METHOD];
  const path = headers[HTTP2_HEADER_PATH];
  // ...
  stream.respond({
    [HTTP2_HEADER_STATUS]: 200,
    [HTTP2_HEADER_CONTENT_TYPE]: 'text/plain; charset=utf-8',
  });
  stream.write('hello ');
  stream.end('world');
});
```

#### Event: `'timeout'`

Added in: v8.4.0

**History**

| Version | Changes |
| --- | --- |
| v13.0.0 | The default timeout changed from 120s to 0 (no timeout). |

The `'timeout'` event is emitted when there is no activity on the Server for
a given number of milliseconds set using `http2secureServer.setTimeout()`.
**Default:** 0 (no timeout)

#### Event: `'unknownProtocol'`

Added in: v8.4.0

**History**

| Version | Changes |
| --- | --- |
| v19.0.0 | This event will only be emitted if the client did not transmit an ALPN extension during the TLS handshake. |

- `socket` [`<stream.Duplex>`](51-stream-part2.md#class-streamduplex)

The `'unknownProtocol'` event is emitted when a connecting client fails to
negotiate an allowed protocol (i.e. HTTP/2 or HTTP/1.1). The event handler
receives the socket for handling. If no listener is registered for this event,
the connection is terminated. A timeout may be specified using the
`'unknownProtocolTimeout'` option passed to [`http2.createSecureServer()`](#http2createsecureserveroptions-onrequesthandler).

In earlier versions of Node.js, this event would be emitted if `allowHTTP1` is
`false` and, during the TLS handshake, the client either does not send an ALPN
extension or sends an ALPN extension that does not include HTTP/2 (`h2`). Newer
versions of Node.js only emit this event if `allowHTTP1` is `false` and the
client does not send an ALPN extension. If the client sends an ALPN extension
that does not include HTTP/2 (or HTTP/1.1 if `allowHTTP1` is `true`), the TLS
handshake will fail and no secure connection will be established.

See the [Compatibility API](28-http2-part3.md#compatibility-api).

#### `server.close([callback])`

Added in: v8.4.0

- `callback` [`<Function>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Function)

Stops the server from establishing new sessions and streams.

If `callback` is provided, it is not invoked until all active sessions have been
closed, although the server has already stopped allowing new sessions. See
[`tls.Server.close()`](55-tls.md#serverclosecallback) for more details.

#### `server.setTimeout([msecs][, callback])`

Added in: v8.4.0

**History**

| Version | Changes |
| --- | --- |
| v18.0.0 | Passing an invalid callback to the `callback` argument now throws `ERR_INVALID_ARG_TYPE` instead of `ERR_INVALID_CALLBACK`. |

- `msecs` [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) **Default:** `120000` (2 minutes)
- `callback` [`<Function>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Function)
- Returns: [`<Http2SecureServer>`](28-http2-part2.md#class-http2secureserver)

Used to set the timeout value for http2 secure server requests,
and sets a callback function that is called when there is no activity
on the `Http2SecureServer` after `msecs` milliseconds.

The given callback is registered as a listener on the `'timeout'` event.

In case if `callback` is not a function, a new `ERR_INVALID_ARG_TYPE`
error will be thrown.

#### `server.timeout`

Added in: v8.4.0

**History**

| Version | Changes |
| --- | --- |
| v13.0.0 | The default timeout changed from 120s to 0 (no timeout). |

- Type: [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) Timeout in milliseconds. **Default:** 0 (no timeout)

The number of milliseconds of inactivity before a socket is presumed
to have timed out.

A value of `0` will disable the timeout behavior on incoming connections.

The socket timeout logic is set up on connection, so changing this
value only affects new connections to the server, not any existing connections.

#### `server.updateSettings([settings])`

Added in: v15.1.0, v14.17.0

- `settings` [`<HTTP/2 Settings Object>`](28-http2-part3.md#settings-object)

Used to update the server with the provided settings.

Throws `ERR_HTTP2_INVALID_SETTING_VALUE` for invalid `settings` values.

Throws `ERR_INVALID_ARG_TYPE` for invalid `settings` argument.

### `http2.createServer([options][, onRequestHandler])`

Added in: v8.4.0

**History**

| Version | Changes |
| --- | --- |
| v26.11.0 | Added the `connectionWindowSize` option. |
| v25.7.0 | Added the `strictSingleValueFields` option. |
| v25.7.0 | Added `http1Options` option. The `Http1IncomingMessage` and `Http1ServerResponse` options are now deprecated. |
| v23.0.0, v22.10.0 | Added `streamResetBurst` and `streamResetRate`. |
| v15.10.0, v14.16.0, v12.21.0, v10.24.0 | Added `unknownProtocolTimeout` option with a default of 10000. |
| v14.4.0, v12.18.0, v10.21.0 | Added `maxSettings` option with a default of 32. |
| v13.3.0, v12.16.0 | Added `maxSessionRejectedStreams` option with a default of 100. |
| v13.3.0, v12.16.0 | Added `maxSessionInvalidFrames` option with a default of 1000. |
| v13.0.0 | The `PADDING_STRATEGY_CALLBACK` has been made equivalent to providing `PADDING_STRATEGY_ALIGNED` and `selectPadding` has been removed. |
| v12.4.0 | The `options` parameter now supports `net.createServer()` options. |
| v9.6.0 | Added the `Http1IncomingMessage` and `Http1ServerResponse` option. |
| v8.9.3 | Added the `maxOutstandingPings` option with a default limit of 10. |
| v8.9.3 | Added the `maxHeaderListPairs` option with a default limit of 128 header pairs. |

- `options` [`<Object>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Object)
  - `maxDeflateDynamicTableSize` [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) Sets the maximum dynamic table size
    for deflating header fields. **Default:** `4Kib`.
  - `maxSettings` [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) Sets the maximum number of settings entries per `SETTINGS` frame. The minimum value allowed is `1`. **Default:** `32`.
  - `maxSessionMemory`[`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) Sets the maximum memory that the `Http2Session`
    is permitted to use. The value is expressed in terms of number of megabytes,
    e.g. `1` equal 1 megabyte. The minimum value allowed is `1`.
    This is a credit based limit, existing `Http2Stream`s may cause this
    limit to be exceeded, but new `Http2Stream` instances will be rejected
    while this limit is exceeded. The current number of `Http2Stream` sessions,
    the current memory use of the header compression tables, header blocks
    retained by open streams, current data queued to be sent, and
    unacknowledged `PING` and `SETTINGS` frames are all counted towards the
    current limit. **Default:** `10`.
  - `maxHeaderListPairs` [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) Sets the maximum number of header entries.
    This is similar to [`server.maxHeadersCount`](27-http-part2.md#servermaxheaderscount) or
    [`request.maxHeadersCount`](27-http-part1.md#requestmaxheaderscount) in the `node:http` module. The minimum value
    is `4`. **Default:** `128`.
  - `maxOutstandingPings` [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) Sets the maximum number of outstanding,
    unacknowledged pings. **Default:** `10`.
  - `maxSendHeaderBlockLength` [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) Sets the maximum allowed size for a
    serialized, compressed block of headers. Attempts to send headers that
    exceed this limit will result in a `'frameError'` event being emitted
    and the stream being closed and destroyed.
    While this sets the maximum allowed size to the entire block of headers,
    `nghttp2` (the internal http2 library) has a limit of `65536`
    for each decompressed key/value pair.
  - `paddingStrategy` [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) The strategy used for determining the amount of
    padding to use for `HEADERS` and `DATA` frames. **Default:**
    `http2.constants.PADDING_STRATEGY_NONE`. Value may be one of:
    - `http2.constants.PADDING_STRATEGY_NONE`: No padding is applied.
    - `http2.constants.PADDING_STRATEGY_MAX`: The maximum amount of padding,
      determined by the internal implementation, is applied.
    - `http2.constants.PADDING_STRATEGY_ALIGNED`: Attempts to apply enough
      padding to ensure that the total frame length, including the 9-byte
      header, is a multiple of 8. For each frame, there is a maximum allowed
      number of padding bytes that is determined by current flow control state
      and settings. If this maximum is less than the calculated amount needed to
      ensure alignment, the maximum is used and the total frame length is not
      necessarily aligned at 8 bytes.
  - `peerMaxConcurrentStreams` [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) Sets the maximum number of concurrent
    streams for the remote peer as if a `SETTINGS` frame had been received. Will
    be overridden if the remote peer sets its own value for
    `maxConcurrentStreams`. **Default:** `100`.
  - `maxSessionInvalidFrames` [`<integer>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) Sets the maximum number of invalid
    frames that will be tolerated before the session is closed. **Default:** `1000`.
  - `maxSessionRejectedStreams` [`<integer>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) Sets the maximum number of rejected
    upon creation streams that will be tolerated before the session is closed.
    Each rejection is associated with an `NGHTTP2_ENHANCE_YOUR_CALM`
    error that should tell the peer to not open any more streams, continuing
    to open streams is therefore regarded as a sign of a misbehaving peer.
    **Default:** `100`.
  - `connectionWindowSize` [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) Sets the initial flow control window for
    each session, in bytes. This is the total amount of data the remote peer
    may send across all streams before it has to wait for a `WINDOW_UPDATE`.
    The equivalent per-stream limit is `settings.initialWindowSize`. The
    minimum allowed value is `1` and the maximum is 231-1. Values
    below 65535 will not take effect until the initial protocol-default
    window of 65535 has been used.
  - `settings` [`<HTTP/2 Settings Object>`](28-http2-part3.md#settings-object) The initial settings to send to the
    remote peer upon connection.
  - `streamResetBurst` [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) and `streamResetRate` [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) Sets the rate
    limit for the incoming stream reset (RST\_STREAM frame). Both settings must
    be set to have any effect, and default to 1000 and 33 respectively.
  - `remoteCustomSettings` [`<Array>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Array) The array of integer values determines the
    settings types, which are included in the `CustomSettings`-property of
    the received remoteSettings. Please see the `CustomSettings`-property of
    the `Http2Settings` object for more information,
    on the allowed setting types.
  - `Http1IncomingMessage` [`<http.IncomingMessage>`](27-http-part2.md#class-httpincomingmessage) Specifies the `IncomingMessage` class to used for HTTP/1 fallback. Useful for extending
    the original `http.IncomingMessage`. **Default:** `http.IncomingMessage`.
    **Deprecated.** Use `http1Options.IncomingMessage` instead. See
    [DEP0202](17-deprecations.md#dep0202-http1incomingmessage-and-http1serverresponse-options-of-http2-servers).
  - `Http1ServerResponse` [`<http.ServerResponse>`](27-http-part2.md#class-httpserverresponse) Specifies the `ServerResponse`
    class to used for HTTP/1 fallback. Useful for extending the original
    `http.ServerResponse`. **Default:** `http.ServerResponse`.
    **Deprecated.** Use `http1Options.ServerResponse` instead. See
    [DEP0202](17-deprecations.md#dep0202-http1incomingmessage-and-http1serverresponse-options-of-http2-servers).
  - `http1Options` [`<Object>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Object) An options object for configuring the HTTP/1
    fallback when `allowHTTP1` is `true`. These options are passed to the
    underlying HTTP/1 server. See [`http.createServer()`](27-http-part3.md#httpcreateserveroptions-requestlistener) for available
    options. Among others, the following are supported:
    - `IncomingMessage` [`<http.IncomingMessage>`](27-http-part2.md#class-httpincomingmessage) Specifies the `IncomingMessage` class to use for HTTP/1 fallback.
      **Default:** `http.IncomingMessage`.
    - `ServerResponse` [`<http.ServerResponse>`](27-http-part2.md#class-httpserverresponse) Specifies the `ServerResponse`
      class to use for HTTP/1 fallback.
      **Default:** `http.ServerResponse`.
    - `keepAliveTimeout` [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) The number of milliseconds of inactivity
      a server needs to wait for additional incoming data, after it has
      finished writing the last response, before a socket will be destroyed. **Default:** `5000`.
  - `Http2ServerRequest` [`<http2.Http2ServerRequest>`](28-http2-part3.md#class-http2http2serverrequest) Specifies the `Http2ServerRequest` class to use.
    Useful for extending the original `Http2ServerRequest`.
    **Default:** `Http2ServerRequest`.
  - `Http2ServerResponse` [`<http2.Http2ServerResponse>`](28-http2-part3.md#class-http2http2serverresponse) Specifies the `Http2ServerResponse` class to use.
    Useful for extending the original `Http2ServerResponse`.
    **Default:** `Http2ServerResponse`.
  - `unknownProtocolTimeout` [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) Specifies a timeout in milliseconds that
    a server should wait when an [`'unknownProtocol'`](#event-unknownprotocol) is emitted. If the
    socket has not been destroyed by that time the server will destroy it.
    **Default:** `10000`.
  - `strictFieldWhitespaceValidation` [`<boolean>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#boolean_type) If `true`, it turns on strict leading
    and trailing whitespace validation for HTTP/2 header field names and values
    as per [RFC-9113](https://www.rfc-editor.org/rfc/rfc9113.html#section-8.2.1).
    **Default:** `true`.
  - `strictSingleValueFields` [`<boolean>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#boolean_type) If `true`, strict validation is used
    for headers and trailers defined as having only a single value, such that
    an error is thrown if multiple values are provided.
    **Default:** `true`.
  - `...options` [`<Object>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Object) Any [`net.createServer()`](38-net.md#netcreateserveroptions-connectionlistener) option can be provided.
- `onRequestHandler` [`<Function>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Function) See [Compatibility API](28-http2-part3.md#compatibility-api)
- Returns: [`<Http2Server>`](28-http2-part2.md#class-http2server)

Returns a `net.Server` instance that creates and manages `Http2Session`
instances.

Since there are no browsers known that support
[unencrypted HTTP/2](https://http2.github.io/faq/#does-http2-require-encryption), the use of
[`http2.createSecureServer()`](#http2createsecureserveroptions-onrequesthandler) is necessary when communicating
with browser clients.

*ES module version:*

```js
import { createServer } from 'node:http2';

// Create an unencrypted HTTP/2 server.
// Since there are no browsers known that support
// unencrypted HTTP/2, the use of `createSecureServer()`
// is necessary when communicating with browser clients.
const server = createServer();

server.on('stream', (stream, headers) => {
  stream.respond({
    'content-type': 'text/html; charset=utf-8',
    ':status': 200,
  });
  stream.end('<h1>Hello World</h1>');
});

server.listen(8000);
```

*CommonJS version:*

```js
const http2 = require('node:http2');

// Create an unencrypted HTTP/2 server.
// Since there are no browsers known that support
// unencrypted HTTP/2, the use of `http2.createSecureServer()`
// is necessary when communicating with browser clients.
const server = http2.createServer();

server.on('stream', (stream, headers) => {
  stream.respond({
    'content-type': 'text/html; charset=utf-8',
    ':status': 200,
  });
  stream.end('<h1>Hello World</h1>');
});

server.listen(8000);
```

### `http2.createSecureServer(options[, onRequestHandler])`

Added in: v8.4.0

**History**

| Version | Changes |
| --- | --- |
| v26.11.0 | Added the `connectionWindowSize` option. |
| v25.7.0 | Added the `strictSingleValueFields` option. |
| v25.7.0 | Added `http1Options` option. |
| v15.10.0, v14.16.0, v12.21.0, v10.24.0 | Added `unknownProtocolTimeout` option with a default of 10000. |
| v14.4.0, v12.18.0, v10.21.0 | Added `maxSettings` option with a default of 32. |
| v13.3.0, v12.16.0 | Added `maxSessionRejectedStreams` option with a default of 100. |
| v13.3.0, v12.16.0 | Added `maxSessionInvalidFrames` option with a default of 1000. |
| v13.0.0 | The `PADDING_STRATEGY_CALLBACK` has been made equivalent to providing `PADDING_STRATEGY_ALIGNED` and `selectPadding` has been removed. |
| v10.12.0 | Added the `origins` option to automatically send an `ORIGIN` frame on `Http2Session` startup. |
| v8.9.3 | Added the `maxOutstandingPings` option with a default limit of 10. |
| v8.9.3 | Added the `maxHeaderListPairs` option with a default limit of 128 header pairs. |

- `options` [`<Object>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Object)
  - `allowHTTP1` [`<boolean>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#boolean_type) Incoming client connections that do not support
    HTTP/2 will be downgraded to HTTP/1.x when set to `true`.
    See the [`'unknownProtocol'`](#event-unknownprotocol) event. See [ALPN negotiation](28-http2-part3.md#alpn-negotiation).
    **Default:** `false`.
  - `maxDeflateDynamicTableSize` [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) Sets the maximum dynamic table size
    for deflating header fields. **Default:** `4Kib`.
  - `maxSettings` [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) Sets the maximum number of settings entries per `SETTINGS` frame. The minimum value allowed is `1`. **Default:** `32`.
  - `maxSessionMemory`[`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) Sets the maximum memory that the `Http2Session`
    is permitted to use. The value is expressed in terms of number of megabytes,
    e.g. `1` equal 1 megabyte. The minimum value allowed is `1`. This is a
    credit based limit, existing `Http2Stream`s may cause this
    limit to be exceeded, but new `Http2Stream` instances will be rejected
    while this limit is exceeded. The current number of `Http2Stream` sessions,
    the current memory use of the header compression tables, header blocks
    retained by open streams, current data queued to be sent, and
    unacknowledged `PING` and `SETTINGS` frames are all counted towards the
    current limit. **Default:** `10`.
  - `maxHeaderListPairs` [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) Sets the maximum number of header entries.
    This is similar to [`server.maxHeadersCount`](27-http-part2.md#servermaxheaderscount) or
    [`request.maxHeadersCount`](27-http-part1.md#requestmaxheaderscount) in the `node:http` module. The minimum value
    is `4`. **Default:** `128`.
  - `maxOutstandingPings` [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) Sets the maximum number of outstanding,
    unacknowledged pings. **Default:** `10`.
  - `maxSendHeaderBlockLength` [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) Sets the maximum allowed size for a
    serialized, compressed block of headers. Attempts to send headers that
    exceed this limit will result in a `'frameError'` event being emitted
    and the stream being closed and destroyed.
  - `paddingStrategy` [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) Strategy used for determining the amount of
    padding to use for `HEADERS` and `DATA` frames. **Default:**
    `http2.constants.PADDING_STRATEGY_NONE`. Value may be one of:
    - `http2.constants.PADDING_STRATEGY_NONE`: No padding is applied.
    - `http2.constants.PADDING_STRATEGY_MAX`: The maximum amount of padding,
      determined by the internal implementation, is applied.
    - `http2.constants.PADDING_STRATEGY_ALIGNED`: Attempts to apply enough
      padding to ensure that the total frame length, including the
      9-byte header, is a multiple of 8. For each frame, there is a maximum
      allowed number of padding bytes that is determined by current flow control
      state and settings. If this maximum is less than the calculated amount
      needed to ensure alignment, the maximum is used and the total frame length
      is not necessarily aligned at 8 bytes.
  - `peerMaxConcurrentStreams` [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) Sets the maximum number of concurrent
    streams for the remote peer as if a `SETTINGS` frame had been received. Will
    be overridden if the remote peer sets its own value for
    `maxConcurrentStreams`. **Default:** `100`.
  - `maxSessionInvalidFrames` [`<integer>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) Sets the maximum number of invalid
    frames that will be tolerated before the session is closed. **Default:** `1000`.
  - `maxSessionRejectedStreams` [`<integer>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) Sets the maximum number of rejected
    upon creation streams that will be tolerated before the session is closed.
    Each rejection is associated with an `NGHTTP2_ENHANCE_YOUR_CALM`
    error that should tell the peer to not open any more streams, continuing
    to open streams is therefore regarded as a sign of a misbehaving peer.
    **Default:** `100`.
  - `connectionWindowSize` [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) Sets the initial flow control window for
    each session, in bytes. This is the total amount of data the remote peer
    may send across all streams before it has to wait for a `WINDOW_UPDATE`.
    The equivalent per-stream limit is `settings.initialWindowSize`. The
    minimum allowed value is `1` and the maximum is 231-1. Values
    below 65535 will not take effect until the initial protocol-default
    window of 65535 has been used.
    **Default:** `65535`.
  - `settings` [`<HTTP/2 Settings Object>`](28-http2-part3.md#settings-object) The initial settings to send to the
    remote peer upon connection.
  - `streamResetBurst` [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) and `streamResetRate` [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) Sets the rate
    limit for the incoming stream reset (RST\_STREAM frame). Both settings must
    be set to have any effect, and default to 1000 and 33 respectively.
  - `remoteCustomSettings` [`<Array>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Array) The array of integer values determines the
    settings types, which are included in the `customSettings`-property of the
    received remoteSettings. Please see the `customSettings`-property of the
    `Http2Settings` object for more information, on the allowed setting types.
  - `...options` [`<Object>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Object) Any [`tls.createServer()`](55-tls.md#tlscreateserveroptions-secureconnectionlistener) options can be provided.
    For servers, the identity options (`pfx` or `key`/`cert`) are usually required.
  - `origins` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type)[] An array of origin strings to send within an `ORIGIN`
    frame immediately following creation of a new server `Http2Session`.
  - `unknownProtocolTimeout` [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) Specifies a timeout in milliseconds that
    a server should wait when an [`'unknownProtocol'`](#event-unknownprotocol) event is emitted. If
    the socket has not been destroyed by that time the server will destroy it.
    **Default:** `10000`.
  - `strictFieldWhitespaceValidation` [`<boolean>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#boolean_type) If `true`, it turns on strict leading
    and trailing whitespace validation for HTTP/2 header field names and values
    as per [RFC-9113](https://www.rfc-editor.org/rfc/rfc9113.html#section-8.2.1).
    **Default:** `true`.
  - `strictSingleValueFields` [`<boolean>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#boolean_type) If `true`, strict validation is used
    for headers and trailers defined as having only a single value, such that
    an error is thrown if multiple values are provided.
    **Default:** `true`.
  - `http1Options` [`<Object>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Object) An options object for configuring the HTTP/1
    fallback when `allowHTTP1` is `true`. These options are passed to the
    underlying HTTP/1 server. See [`http.createServer()`](27-http-part3.md#httpcreateserveroptions-requestlistener) for available
    options. Among others, the following are supported:
    - `IncomingMessage` [`<http.IncomingMessage>`](27-http-part2.md#class-httpincomingmessage) Specifies the `IncomingMessage` class to use for HTTP/1 fallback.
      **Default:** `http.IncomingMessage`.
    - `ServerResponse` [`<http.ServerResponse>`](27-http-part2.md#class-httpserverresponse) Specifies the `ServerResponse`
      class to use for HTTP/1 fallback.
      **Default:** `http.ServerResponse`.
    - `keepAliveTimeout` [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) The number of milliseconds of inactivity
      a server needs to wait for additional incoming data, after it has
      finished writing the last response, before a socket will be destroyed. **Default:** `5000`.
- `onRequestHandler` [`<Function>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Function) See [Compatibility API](28-http2-part3.md#compatibility-api)
- Returns: [`<Http2SecureServer>`](28-http2-part2.md#class-http2secureserver)

Returns a `tls.Server` instance that creates and manages `Http2Session`
instances.

*ES module version:*

```js
import { createSecureServer } from 'node:http2';
import { readFileSync } from 'node:fs';

const options = {
  key: readFileSync('server-key.pem'),
  cert: readFileSync('server-cert.pem'),
};

// Create a secure HTTP/2 server
const server = createSecureServer(options);

server.on('stream', (stream, headers) => {
  stream.respond({
    'content-type': 'text/html; charset=utf-8',
    ':status': 200,
  });
  stream.end('<h1>Hello World</h1>');
});

server.listen(8443);
```

*CommonJS version:*

```js
const http2 = require('node:http2');
const fs = require('node:fs');

const options = {
  key: fs.readFileSync('server-key.pem'),
  cert: fs.readFileSync('server-cert.pem'),
};

// Create a secure HTTP/2 server
const server = http2.createSecureServer(options);

server.on('stream', (stream, headers) => {
  stream.respond({
    'content-type': 'text/html; charset=utf-8',
    ':status': 200,
  });
  stream.end('<h1>Hello World</h1>');
});

server.listen(8443);
```

### `http2.connect(authority[, options][, listener])`

Added in: v8.4.0

**History**

| Version | Changes |
| --- | --- |
| v26.11.0 | Added the `connectionWindowSize` option. |
| v15.10.0, v14.16.0, v12.21.0, v10.24.0 | Added `unknownProtocolTimeout` option with a default of 10000. |
| v14.4.0, v12.18.0, v10.21.0 | Added `maxSettings` option with a default of 32. |
| v13.0.0 | The `PADDING_STRATEGY_CALLBACK` has been made equivalent to providing `PADDING_STRATEGY_ALIGNED` and `selectPadding` has been removed. |
| v8.9.3 | Added the `maxOutstandingPings` option with a default limit of 10. |
| v8.9.3 | Added the `maxHeaderListPairs` option with a default limit of 128 header pairs. |

- `authority` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) | [`<URL>`](59-url.md#the-whatwg-url-api) The remote HTTP/2 server to connect to. This must
  be in the form of a minimal, valid URL with the `http://` or `https://`
  prefix, host name, and IP port (if a non-default port is used). Userinfo
  (user ID and password), path, querystring, and fragment details in the
  URL will be ignored.
- `options` [`<Object>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Object)
  - `maxDeflateDynamicTableSize` [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) Sets the maximum dynamic table size
    for deflating header fields. **Default:** `4Kib`.
  - `maxSettings` [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) Sets the maximum number of settings entries per `SETTINGS` frame. The minimum value allowed is `1`. **Default:** `32`.
  - `maxSessionMemory`[`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) Sets the maximum memory that the `Http2Session`
    is permitted to use. The value is expressed in terms of number of megabytes,
    e.g. `1` equal 1 megabyte. The minimum value allowed is `1`.
    This is a credit based limit, existing `Http2Stream`s may cause this
    limit to be exceeded, but new `Http2Stream` instances will be rejected
    while this limit is exceeded. The current number of `Http2Stream` sessions,
    the current memory use of the header compression tables, header blocks
    retained by open streams, current data queued to be sent, and
    unacknowledged `PING` and `SETTINGS` frames are all counted towards the
    current limit. **Default:** `10`.
  - `maxHeaderListPairs` [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) Sets the maximum number of header entries.
    This is similar to [`server.maxHeadersCount`](27-http-part2.md#servermaxheaderscount) or
    [`request.maxHeadersCount`](27-http-part1.md#requestmaxheaderscount) in the `node:http` module. The minimum value
    is `1`. **Default:** `128`.
  - `maxOriginSetSize` [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) Sets the maximum number of uniq origin the sever
    can send via ORIGIN frames. **Default:** `128`.
  - `maxOutstandingPings` [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) Sets the maximum number of outstanding,
    unacknowledged pings. **Default:** `10`.
  - `maxReservedRemoteStreams` [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) Sets the maximum number of reserved push
    streams the client will accept at any given time. Once the current number of
    currently reserved push streams exceeds reaches this limit, new push streams
    sent by the server will be automatically rejected. The minimum allowed value
    is 0. The maximum allowed value is 232-1. A negative value sets
    this option to the maximum allowed value. **Default:** `200`.
  - `maxSendHeaderBlockLength` [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) Sets the maximum allowed size for a
    serialized, compressed block of headers. Attempts to send headers that
    exceed this limit will result in a `'frameError'` event being emitted
    and the stream being closed and destroyed.
  - `paddingStrategy` [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) Strategy used for determining the amount of
    padding to use for `HEADERS` and `DATA` frames. **Default:**
    `http2.constants.PADDING_STRATEGY_NONE`. Value may be one of:
    - `http2.constants.PADDING_STRATEGY_NONE`: No padding is applied.
    - `http2.constants.PADDING_STRATEGY_MAX`: The maximum amount of padding,
      determined by the internal implementation, is applied.
    - `http2.constants.PADDING_STRATEGY_ALIGNED`: Attempts to apply enough
      padding to ensure that the total frame length, including the
      9-byte header, is a multiple of 8. For each frame, there is a maximum
      allowed number of padding bytes that is determined by current flow control
      state and settings. If this maximum is less than the calculated amount
      needed to ensure alignment, the maximum is used and the total frame length
      is not necessarily aligned at 8 bytes.
  - `peerMaxConcurrentStreams` [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) Sets the maximum number of concurrent
    streams for the remote peer as if a `SETTINGS` frame had been received. Will
    be overridden if the remote peer sets its own value for
    `maxConcurrentStreams`. **Default:** `100`.
  - `protocol` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) The protocol to connect with, if not set in the `authority`. Value may be either `'http:'` or `'https:'`. **Default:**
    `'https:'`
  - `connectionWindowSize` [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) Sets the initial flow control window for
    this session, in bytes. This is the total amount of data the remote peer
    may send across all streams before it has to wait for a `WINDOW_UPDATE`.
    The equivalent per-stream limit is `settings.initialWindowSize`. The
    minimum allowed value is `1` and the maximum is 231-1. Values
    below 65535 will not take effect until the initial protocol-default
    window of 65535 has been used.
    **Default:** `65535`.
  - `settings` [`<HTTP/2 Settings Object>`](28-http2-part3.md#settings-object) The initial settings to send to the
    remote peer upon connection.
  - `remoteCustomSettings` [`<Array>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Array) The array of integer values determines the
    settings types, which are included in the `CustomSettings`-property of the
    received remoteSettings. Please see the `CustomSettings`-property of the
    `Http2Settings` object for more information, on the allowed setting types.
  - `createConnection` [`<Function>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Function) An optional callback that receives the `URL`
    instance passed to `connect` and the `options` object, and returns any
    [`Duplex`](51-stream-part2.md#class-streamduplex) stream that is to be used as the connection for this session.
  - `...options` [`<Object>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Object) Any [`net.connect()`](38-net.md#netconnect) or [`tls.connect()`](55-tls.md#tlsconnectoptions-callback) options
    can be provided.
  - `unknownProtocolTimeout` [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) Specifies a timeout in milliseconds that
    a server should wait when an [`'unknownProtocol'`](#event-unknownprotocol) event is emitted. If
    the socket has not been destroyed by that time the server will destroy it.
    **Default:** `10000`.
  - `strictFieldWhitespaceValidation` [`<boolean>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#boolean_type) If `true`, it turns on strict leading
    and trailing whitespace validation for HTTP/2 header field names and values
    as per [RFC-9113](https://www.rfc-editor.org/rfc/rfc9113.html#section-8.2.1).
    **Default:** `true`.
- `listener` [`<Function>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Function) Will be registered as a one-time listener of the [`'connect'`](28-http2-part1.md#event-connect) event.
- Returns: [`<ClientHttp2Session>`](28-http2-part1.md#class-clienthttp2session)

Returns a `ClientHttp2Session` instance.

*ES module version:*

```js
import { connect } from 'node:http2';
const client = connect('https://localhost:1234');

/* Use the client */

client.close();
```

*CommonJS version:*

```js
const http2 = require('node:http2');
const client = http2.connect('https://localhost:1234');

/* Use the client */

client.close();
```

[Contents](../README.md) | [Previous: HTTP/2, part 1 of 4](28-http2-part1.md) | [Next: HTTP/2, part 3 of 4](28-http2-part3.md)
