[Contents](../README.md) | [Previous: HTTP, part 3 of 3](27-http-part3.md) | [Next: HTTP/2, part 2 of 4](28-http2-part2.md)

Source: [https://nodejs.org/docs/latest/api/http2.html](https://nodejs.org/docs/latest/api/http2.html)

Part 1 of 4 of this section.

# HTTP/2

Added in: v8.4.0

**History**

| Version | Changes |
| --- | --- |
| v15.3.0, v14.17.0 | It is possible to abort a request with an AbortSignal. |
| v15.0.0 | Requests with the `host` header (with or without `:authority`) can now be sent/received. |
| v10.10.0 | HTTP/2 is now Stable. Previously, it had been Experimental. |

[Stability: 2](01-documentation.md#stability-index) - Stable

The `node:http2` module provides an implementation of the [HTTP/2](https://tools.ietf.org/html/rfc7540) protocol.
It can be accessed using:

```js
const http2 = require('node:http2');
```

## Determining if crypto support is unavailable

It is possible for Node.js to be built without including support for the
`node:crypto` module. In such cases, attempting to `import` from `node:http2` or
calling `require('node:http2')` will result in an error being thrown.

When using CommonJS, the error thrown can be caught using try/catch:

```js
let http2;
try {
  http2 = require('node:http2');
} catch (err) {
  console.error('http2 support is disabled!');
}
```

When using the lexical ESM `import` keyword, the error can only be
caught if a handler for `process.on('uncaughtException')` is registered
*before* any attempt to load the module is made (using, for instance,
a preload module).

When using ESM, if there is a chance that the code may be run on a build
of Node.js where crypto support is not enabled, consider using the
[`import()`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/import) function instead of the lexical `import` keyword:

```js
let http2;
try {
  http2 = await import('node:http2');
} catch (err) {
  console.error('http2 support is disabled!');
}
```

## Core API

The Core API provides a low-level interface designed specifically around
support for HTTP/2 protocol features. It is specifically *not* designed for
compatibility with the existing [HTTP/1](27-http-part1.md) module API. However,
the [Compatibility API](28-http2-part3.md#compatibility-api) is.

The `http2` Core API is much more symmetric between client and server than the
`http` API. For instance, most events, like `'error'`, `'connect'` and
`'stream'`, can be emitted either by client-side code or server-side code.

### Server-side example

The following illustrates a simple HTTP/2 server using the Core API.
Since there are no browsers known that support
[unencrypted HTTP/2](https://http2.github.io/faq/#does-http2-require-encryption), the use of
[`http2.createSecureServer()`](28-http2-part2.md#http2createsecureserveroptions-onrequesthandler) is necessary when communicating
with browser clients.

*ES module version:*

```js
import { createSecureServer } from 'node:http2';
import { readFileSync } from 'node:fs';

const server = createSecureServer({
  key: readFileSync('localhost-privkey.pem'),
  cert: readFileSync('localhost-cert.pem'),
});

server.on('error', (err) => console.error(err));

server.on('stream', (stream, headers) => {
  // stream is a Duplex
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

const server = http2.createSecureServer({
  key: fs.readFileSync('localhost-privkey.pem'),
  cert: fs.readFileSync('localhost-cert.pem'),
});
server.on('error', (err) => console.error(err));

server.on('stream', (stream, headers) => {
  // stream is a Duplex
  stream.respond({
    'content-type': 'text/html; charset=utf-8',
    ':status': 200,
  });
  stream.end('<h1>Hello World</h1>');
});

server.listen(8443);
```

To generate the certificate and key for this example, run:

```bash
openssl req -x509 -newkey rsa:2048 -nodes -sha256 -subj '/CN=localhost' \
  -keyout localhost-privkey.pem -out localhost-cert.pem
```

### Client-side example

The following illustrates an HTTP/2 client:

*ES module version:*

```js
import { connect } from 'node:http2';
import { readFileSync } from 'node:fs';

const client = connect('https://localhost:8443', {
  ca: readFileSync('localhost-cert.pem'),
});
client.on('error', (err) => console.error(err));

const req = client.request({ ':path': '/' });

req.on('response', (headers, flags) => {
  for (const name in headers) {
    console.log(`${name}: ${headers[name]}`);
  }
});

req.setEncoding('utf8');
let data = '';
req.on('data', (chunk) => { data += chunk; });
req.on('end', () => {
  console.log(`\n${data}`);
  client.close();
});
req.end();
```

*CommonJS version:*

```js
const http2 = require('node:http2');
const fs = require('node:fs');

const client = http2.connect('https://localhost:8443', {
  ca: fs.readFileSync('localhost-cert.pem'),
});
client.on('error', (err) => console.error(err));

const req = client.request({ ':path': '/' });

req.on('response', (headers, flags) => {
  for (const name in headers) {
    console.log(`${name}: ${headers[name]}`);
  }
});

req.setEncoding('utf8');
let data = '';
req.on('data', (chunk) => { data += chunk; });
req.on('end', () => {
  console.log(`\n${data}`);
  client.close();
});
req.end();
```

### Class: `Http2Session`

Added in: v8.4.0

- Extends: [`<EventEmitter>`](23-events.md#class-eventemitter)

Instances of the `http2.Http2Session` class represent an active communications
session between an HTTP/2 client and server. Instances of this class are *not*
intended to be constructed directly by user code.

Each `Http2Session` instance will exhibit slightly different behaviors
depending on whether it is operating as a server or a client. The
`http2session.type` property can be used to determine the mode in which an
`Http2Session` is operating. On the server side, user code should rarely
have occasion to work with the `Http2Session` object directly, with most
actions typically taken through interactions with either the `Http2Server` or
`Http2Stream` objects.

User code will not create `Http2Session` instances directly. Server-side
`Http2Session` instances are created by the `Http2Server` instance when a
new HTTP/2 connection is received. Client-side `Http2Session` instances are
created using the `http2.connect()` method.

#### `Http2Session` and sockets

Every `Http2Session` instance is associated with exactly one [`net.Socket`](38-net.md#class-netsocket) or
[`tls.TLSSocket`](55-tls.md#class-tlstlssocket) when it is created. When either the `Socket` or the
`Http2Session` are destroyed, both will be destroyed.

Because of the specific serialization and processing requirements imposed
by the HTTP/2 protocol, it is not recommended for user code to read data from
or write data to a `Socket` instance bound to a `Http2Session`. Doing so can
put the HTTP/2 session into an indeterminate state causing the session and
the socket to become unusable.

Once a `Socket` has been bound to an `Http2Session`, user code should rely
solely on the API of the `Http2Session`.

#### Event: `'close'`

Added in: v8.4.0

The `'close'` event is emitted once the `Http2Session` has been destroyed. Its
listener does not expect any arguments.

#### Event: `'connect'`

Added in: v8.4.0

- `session` [`<Http2Session>`](28-http2-part1.md#class-http2session)
- `socket` [`<net.Socket>`](38-net.md#class-netsocket)

The `'connect'` event is emitted once the `Http2Session` has been successfully
connected to the remote peer and communication may begin.

User code will typically not listen for this event directly.

#### Event: `'error'`

Added in: v8.4.0

- `error` [`<Error>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Error)

The `'error'` event is emitted when an error occurs during the processing of
an `Http2Session`.

#### Event: `'frameError'`

Added in: v8.4.0

- `type` [`<integer>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) The frame type.
- `code` [`<integer>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) The error code.
- `id` [`<integer>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) The stream id (or `0` if the frame isn't associated with a
  stream).

The `'frameError'` event is emitted when an error occurs while attempting to
send a frame on the session. If the frame that could not be sent is associated
with a specific `Http2Stream`, an attempt to emit a `'frameError'` event on the
`Http2Stream` is made.

If the `'frameError'` event is associated with a stream, the stream will be
closed and destroyed immediately following the `'frameError'` event. If the
event is not associated with a stream, the `Http2Session` will be shut down
immediately following the `'frameError'` event.

#### Event: `'goaway'`

Added in: v8.4.0

- `errorCode` [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) The HTTP/2 error code specified in the `GOAWAY` frame.
- `lastStreamID` [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) The ID of the last stream the remote peer successfully
  processed (or `0` if no ID is specified).
- `opaqueData` [`<Buffer>`](07-buffer-part1.md#class-buffer) If additional opaque data was included in the `GOAWAY`
  frame, a `Buffer` instance will be passed containing that data.

The `'goaway'` event is emitted when a `GOAWAY` frame is received.

The `Http2Session` instance will be shut down automatically when the `'goaway'`
event is emitted.

#### Event: `'localSettings'`

Added in: v8.4.0

- `settings` [`<HTTP/2 Settings Object>`](28-http2-part3.md#settings-object) A copy of the `SETTINGS` frame received.

The `'localSettings'` event is emitted when an acknowledgment `SETTINGS` frame
has been received.

When using `http2session.settings()` to submit new settings, the modified
settings do not take effect until the `'localSettings'` event is emitted.

```js
session.settings({ enablePush: false });

session.on('localSettings', (settings) => {
  /* Use the new settings */
});
```

#### Event: `'ping'`

Added in: v10.12.0

- `payload` [`<Buffer>`](07-buffer-part1.md#class-buffer) The `PING` frame 8-byte payload

The `'ping'` event is emitted whenever a `PING` frame is received from the
connected peer.

#### Event: `'remoteSettings'`

Added in: v8.4.0

- `settings` [`<HTTP/2 Settings Object>`](28-http2-part3.md#settings-object) A copy of the `SETTINGS` frame received.

The `'remoteSettings'` event is emitted when a new `SETTINGS` frame is received
from the connected peer.

```js
session.on('remoteSettings', (settings) => {
  /* Use the new settings */
});
```

#### Event: `'stream'`

Added in: v8.4.0

- `stream` [`<Http2Stream>`](28-http2-part1.md#class-http2stream) A reference to the stream
- `headers` [`<HTTP/2 Headers Object>`](28-http2-part3.md#headers-object) An object describing the headers
- `flags` [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) The associated numeric flags
- `rawHeaders` [`<HTTP/2 Raw Headers>`](28-http2-part3.md#raw-headers) An array containing the raw headers

The `'stream'` event is emitted when a new `Http2Stream` is created.

```js
session.on('stream', (stream, headers, flags) => {
  const method = headers[':method'];
  const path = headers[':path'];
  // ...
  stream.respond({
    ':status': 200,
    'content-type': 'text/plain; charset=utf-8',
  });
  stream.write('hello ');
  stream.end('world');
});
```

On the server side, user code will typically not listen for this event directly,
and would instead register a handler for the `'stream'` event emitted by the
`net.Server` or `tls.Server` instances returned by `http2.createServer()` and
`http2.createSecureServer()`, respectively, as in the example below:

*ES module version:*

```js
import { createServer } from 'node:http2';

// Create an unencrypted HTTP/2 server
const server = createServer();

server.on('stream', (stream, headers) => {
  stream.respond({
    'content-type': 'text/html; charset=utf-8',
    ':status': 200,
  });
  stream.on('error', (error) => console.error(error));
  stream.end('<h1>Hello World</h1>');
});

server.listen(8000);
```

*CommonJS version:*

```js
const http2 = require('node:http2');

// Create an unencrypted HTTP/2 server
const server = http2.createServer();

server.on('stream', (stream, headers) => {
  stream.respond({
    'content-type': 'text/html; charset=utf-8',
    ':status': 200,
  });
  stream.on('error', (error) => console.error(error));
  stream.end('<h1>Hello World</h1>');
});

server.listen(8000);
```

Even though HTTP/2 streams and network sockets are not in a 1:1 correspondence,
a network error will destroy each individual stream and must be handled on the
stream level, as shown above.

#### Event: `'timeout'`

Added in: v8.4.0

After the `http2session.setTimeout()` method is used to set the timeout period
for this `Http2Session`, the `'timeout'` event is emitted if there is no
activity on the `Http2Session` after the configured number of milliseconds.
Its listener does not expect any arguments.

```js
session.setTimeout(2000);
session.on('timeout', () => { /* .. */ });
```

#### `http2session.alpnProtocol`

Added in: v9.4.0

- Type: [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) | [`<undefined>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#undefined_type)

Value will be `undefined` if the `Http2Session` is not yet connected to a
socket, `h2c` if the `Http2Session` is not connected to a `TLSSocket`, or
will return the value of the connected `TLSSocket`'s own `alpnProtocol`
property.

#### `http2session.close([callback])`

Added in: v9.4.0

- `callback` [`<Function>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Function)

Gracefully closes the `Http2Session`, allowing any existing streams to
complete on their own and preventing new `Http2Stream` instances from being
created. Once closed, `http2session.destroy()` *might* be called if there
are no open `Http2Stream` instances.

If specified, the `callback` function is registered as a handler for the
`'close'` event.

#### `http2session.closed`

Added in: v9.4.0

- Type: [`<boolean>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#boolean_type)

Will be `true` if this `Http2Session` instance has been closed, otherwise
`false`.

#### `http2session.connecting`

Added in: v10.0.0

- Type: [`<boolean>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#boolean_type)

Will be `true` if this `Http2Session` instance is still connecting, will be set
to `false` before emitting `connect` event and/or calling the `http2.connect`
callback.

#### `http2session.destroy([error][, code])`

Added in: v8.4.0

- `error` [`<Error>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Error) An `Error` object if the `Http2Session` is being destroyed
  due to an error.
- `code` [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) The HTTP/2 error code to send in the final `GOAWAY` frame.
  If unspecified, and `error` is not undefined, the default is `INTERNAL_ERROR`,
  otherwise defaults to `NO_ERROR`.

Immediately terminates the `Http2Session` and the associated `net.Socket` or
`tls.TLSSocket`.

Once destroyed, the `Http2Session` will emit the `'close'` event. If `error`
is not undefined, an `'error'` event will be emitted immediately before the
`'close'` event.

If there are any remaining open `Http2Streams` associated with the
`Http2Session`, those will also be destroyed.

#### `http2session.destroyed`

Added in: v8.4.0

- Type: [`<boolean>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#boolean_type)

Will be `true` if this `Http2Session` instance has been destroyed and must no
longer be used, otherwise `false`.

#### `http2session.encrypted`

Added in: v9.4.0

- Type: [`<boolean>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#boolean_type) | [`<undefined>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#undefined_type)

Value is `undefined` if the `Http2Session` session socket has not yet been
connected, `true` if the `Http2Session` is connected with a `TLSSocket`,
and `false` if the `Http2Session` is connected to any other kind of socket
or stream.

#### `http2session.goaway([code[, lastStreamID[, opaqueData]]])`

Added in: v9.4.0

- `code` [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) An HTTP/2 error code
- `lastStreamID` [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) The numeric ID of the last processed `Http2Stream`
- `opaqueData` [`<Buffer>`](07-buffer-part1.md#class-buffer) | [`<TypedArray>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/TypedArray) | [`<DataView>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/DataView) A `TypedArray` or `DataView`
  instance containing additional data to be carried within the `GOAWAY` frame.

Transmits a `GOAWAY` frame to the connected peer *without* shutting down the
`Http2Session`.

#### `http2session.localSettings`

Added in: v8.4.0

- Type: [`<HTTP/2 Settings Object>`](28-http2-part3.md#settings-object)

A prototype-less object describing the current local settings of this
`Http2Session`. The local settings are local to *this* `Http2Session` instance.

#### `http2session.originSet`

Added in: v9.4.0

- Type: [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type)[] | [`<undefined>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#undefined_type)

If the `Http2Session` is connected to a `TLSSocket`, the `originSet` property
will return an `Array` of origins for which the `Http2Session` may be
considered authoritative.

The `originSet` property is only available when using a secure TLS connection.

#### `http2session.pendingSettingsAck`

Added in: v8.4.0

- Type: [`<boolean>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#boolean_type)

Indicates whether the `Http2Session` is currently waiting for acknowledgment of
a sent `SETTINGS` frame. Will be `true` after calling the
`http2session.settings()` method. Will be `false` once all sent `SETTINGS`
frames have been acknowledged.

#### `http2session.ping([payload, ]callback)`

Added in: v8.9.3

**History**

| Version | Changes |
| --- | --- |
| v18.0.0 | Passing an invalid callback to the `callback` argument now throws `ERR_INVALID_ARG_TYPE` instead of `ERR_INVALID_CALLBACK`. |

- `payload` [`<Buffer>`](07-buffer-part1.md#class-buffer) | [`<TypedArray>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/TypedArray) | [`<DataView>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/DataView) Optional ping payload.
- `callback` [`<Function>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Function)
- Returns: [`<boolean>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#boolean_type)

Sends a `PING` frame to the connected HTTP/2 peer. A `callback` function must
be provided. The method will return `true` if the `PING` was sent, `false`
otherwise.

The maximum number of outstanding (unacknowledged) pings is determined by the
`maxOutstandingPings` configuration option. The default maximum is 10.

If provided, the `payload` must be a `Buffer`, `TypedArray`, or `DataView`
containing 8 bytes of data that will be transmitted with the `PING` and
returned with the ping acknowledgment.

The callback will be invoked with three arguments: an error argument that will
be `null` if the `PING` was successfully acknowledged, a `duration` argument
that reports the number of milliseconds elapsed since the ping was sent and the
acknowledgment was received, and a `Buffer` containing the 8-byte `PING`
payload.

```js
session.ping(Buffer.from('abcdefgh'), (err, duration, payload) => {
  if (!err) {
    console.log(`Ping acknowledged in ${duration} milliseconds`);
    console.log(`With payload '${payload.toString()}'`);
  }
});
```

If the `payload` argument is not specified, the default payload will be the
64-bit timestamp (little endian) marking the start of the `PING` duration.

#### `http2session.ref()`

Added in: v9.4.0

Calls [`ref()`](38-net.md#socketref) on this `Http2Session`
instance's underlying [`net.Socket`](38-net.md#class-netsocket).

#### `http2session.remoteSettings`

Added in: v8.4.0

- Type: [`<HTTP/2 Settings Object>`](28-http2-part3.md#settings-object)

A prototype-less object describing the current remote settings of this
`Http2Session`. The remote settings are set by the *connected* HTTP/2 peer.

#### `http2session.setLocalWindowSize(windowSize)`

Added in: v15.3.0, v14.18.0

- `windowSize` [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type)

Sets the local endpoint's connection-level window size.
The `windowSize` is the total window size to set, not
the delta.

Increases take effect immediately, but decreases only apply as the window
already advertised to the peer is consumed, since a window that has been
advertised cannot be retracted. To use a window smaller than the default from
the start of the connection, set the `connectionWindowSize` option when
creating the server or client session instead.

*ES module version:*

```js
import { createServer } from 'node:http2';

const server = createServer();
const expectedWindowSize = 2 ** 20;
server.on('session', (session) => {

  // Set local window size to be 2 ** 20
  session.setLocalWindowSize(expectedWindowSize);
});
```

*CommonJS version:*

```js
const http2 = require('node:http2');

const server = http2.createServer();
const expectedWindowSize = 2 ** 20;
server.on('session', (session) => {

  // Set local window size to be 2 ** 20
  session.setLocalWindowSize(expectedWindowSize);
});
```

For http2 clients the proper event is either `'connect'` or `'remoteSettings'`.

#### `http2session.setTimeout(msecs, callback)`

Added in: v8.4.0

**History**

| Version | Changes |
| --- | --- |
| v18.0.0 | Passing an invalid callback to the `callback` argument now throws `ERR_INVALID_ARG_TYPE` instead of `ERR_INVALID_CALLBACK`. |

- `msecs` [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type)
- `callback` [`<Function>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Function)

Used to set a callback function that is called when there is no activity on
the `Http2Session` after `msecs` milliseconds. The given `callback` is
registered as a listener on the `'timeout'` event.

#### `http2session.socket`

Added in: v8.4.0

**History**

| Version | Changes |
| --- | --- |
| v26.6.0 | Calling `destroy` no longer throws and instead destroys the `Http2Session`. |

- Type: [`<net.Socket>`](38-net.md#class-netsocket) | [`<tls.TLSSocket>`](55-tls.md#tlstlssocket)

Returns a `Proxy` object that acts as a `net.Socket` (or `tls.TLSSocket`) but
limits available methods to ones safe to use with HTTP/2.

`emit`, `end`, `pause`, `read`, `resume`, and `write` will throw
an error with code `ERR_HTTP2_NO_SOCKET_MANIPULATION`. See
[`Http2Session` and Sockets](#http2session-and-sockets) for more information.

`destroy`, `setTimeout`, `ref`, and `unref` methods will be called on this
`Http2Session`.

All other interactions will be routed directly to the socket.

#### `http2session.state`

Added in: v8.4.0

Provides miscellaneous information about the current state of the
`Http2Session`.

- Type: [`<Object>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Object)
  - `effectiveLocalWindowSize` [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) The current local (receive)
    flow control window size for the `Http2Session`.
  - `effectiveRecvDataLength` [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) The current number of bytes
    that have been received since the last flow control `WINDOW_UPDATE`.
  - `nextStreamID` [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) The numeric identifier to be used the
    next time a new `Http2Stream` is created by this `Http2Session`.
  - `localWindowSize` [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) The number of bytes that the remote peer can
    send without receiving a `WINDOW_UPDATE`.
  - `lastProcStreamID` [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) The numeric id of the `Http2Stream`
    for which a `HEADERS` or `DATA` frame was most recently received.
  - `remoteWindowSize` [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) The number of bytes that this `Http2Session`
    may send without receiving a `WINDOW_UPDATE`.
  - `outboundQueueSize` [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) The number of frames currently within the
    outbound queue for this `Http2Session`.
  - `deflateDynamicTableSize` [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) The current size in bytes of the
    outbound header compression state table.
  - `inflateDynamicTableSize` [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) The current size in bytes of the
    inbound header compression state table.

An object describing the current status of this `Http2Session`.

#### `http2session.settings([settings][, callback])`

Added in: v8.4.0

**History**

| Version | Changes |
| --- | --- |
| v18.0.0 | Passing an invalid callback to the `callback` argument now throws `ERR_INVALID_ARG_TYPE` instead of `ERR_INVALID_CALLBACK`. |

- `settings` [`<HTTP/2 Settings Object>`](28-http2-part3.md#settings-object)
- `callback` [`<Function>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Function) Callback that is called once the session is connected or
  right away if the session is already connected.
  - `err` [`<Error>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Error) | [`<null>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#null_type)
  - `settings` [`<HTTP/2 Settings Object>`](28-http2-part3.md#settings-object) The updated `settings` object.
  - `duration` [`<integer>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type)

Updates the current local settings for this `Http2Session` and sends a new
`SETTINGS` frame to the connected HTTP/2 peer.

Once called, the `http2session.pendingSettingsAck` property will be `true`
while the session is waiting for the remote peer to acknowledge the new
settings.

The new settings will not become effective until the `SETTINGS` acknowledgment
is received and the `'localSettings'` event is emitted. It is possible to send
multiple `SETTINGS` frames while acknowledgment is still pending.

#### `http2session.type`

Added in: v8.4.0

- Type: [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type)

The `http2session.type` will be equal to
`http2.constants.NGHTTP2_SESSION_SERVER` if this `Http2Session` instance is a
server, and `http2.constants.NGHTTP2_SESSION_CLIENT` if the instance is a
client.

#### `http2session.unref()`

Added in: v9.4.0

Calls [`unref()`](38-net.md#socketunref) on this `Http2Session`
instance's underlying [`net.Socket`](38-net.md#class-netsocket).

### Class: `ServerHttp2Session`

Added in: v8.4.0

- Extends: [`<Http2Session>`](28-http2-part1.md#class-http2session)

#### `serverhttp2session.altsvc(alt, originOrStream)`

Added in: v9.4.0

- `alt` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) A description of the alternative service configuration as
  defined by [RFC 7838](https://tools.ietf.org/html/rfc7838).
- `originOrStream` [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) | [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) | [`<URL>`](59-url.md#the-whatwg-url-api) | [`<Object>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Object) Either a URL string specifying
  the origin (or an `Object` with an `origin` property) or the numeric
  identifier of an active `Http2Stream` as given by the `http2stream.id`
  property.

Submits an `ALTSVC` frame (as defined by [RFC 7838](https://tools.ietf.org/html/rfc7838)) to the connected client.

*ES module version:*

```js
import { createServer } from 'node:http2';

const server = createServer();
server.on('session', (session) => {
  // Set altsvc for origin https://example.org:80
  session.altsvc('h2=":8000"', 'https://example.org:80');
});

server.on('stream', (stream) => {
  // Set altsvc for a specific stream
  stream.session.altsvc('h2=":8000"', stream.id);
});
```

*CommonJS version:*

```js
const http2 = require('node:http2');

const server = http2.createServer();
server.on('session', (session) => {
  // Set altsvc for origin https://example.org:80
  session.altsvc('h2=":8000"', 'https://example.org:80');
});

server.on('stream', (stream) => {
  // Set altsvc for a specific stream
  stream.session.altsvc('h2=":8000"', stream.id);
});
```

Sending an `ALTSVC` frame with a specific stream ID indicates that the alternate
service is associated with the origin of the given `Http2Stream`.

The `alt` and origin string *must* contain only ASCII bytes and are
strictly interpreted as a sequence of ASCII bytes. The special value `'clear'`
may be passed to clear any previously set alternative service for a given
domain.

When a string is passed for the `originOrStream` argument, it will be parsed as
a URL and the origin will be derived. For instance, the origin for the
HTTP URL `'https://example.org/foo/bar'` is the ASCII string
`'https://example.org'`. An error will be thrown if either the given string
cannot be parsed as a URL or if a valid origin cannot be derived.

A `URL` object, or any object with an `origin` property, may be passed as
`originOrStream`, in which case the value of the `origin` property will be
used. The value of the `origin` property *must* be a properly serialized
ASCII origin.

#### Specifying alternative services

The format of the `alt` parameter is strictly defined by [RFC 7838](https://tools.ietf.org/html/rfc7838) as an
ASCII string containing a comma-delimited list of "alternative" protocols
associated with a specific host and port.

For example, the value `'h2="example.org:81"'` indicates that the HTTP/2
protocol is available on the host `'example.org'` on TCP/IP port 81. The
host and port *must* be contained within the quote (`"`) characters.

Multiple alternatives may be specified, for instance: `'h2="example.org:81", h2=":82"'`.

The protocol identifier (`'h2'` in the examples) may be any valid
[ALPN Protocol ID](https://www.iana.org/assignments/tls-extensiontype-values/tls-extensiontype-values.xhtml#alpn-protocol-ids).

The syntax of these values is not validated by the Node.js implementation and
are passed through as provided by the user or received from the peer.

#### `serverhttp2session.origin(...origins)`

Added in: v10.12.0

- `origins` { string | URL | Object } One or more URL Strings passed as
  separate arguments.

Submits an `ORIGIN` frame (as defined by [RFC 8336](https://tools.ietf.org/html/rfc8336)) to the connected client
to advertise the set of origins for which the server is capable of providing
authoritative responses.

*ES module version:*

```js
import { createSecureServer } from 'node:http2';
const options = getSecureOptionsSomehow();
const server = createSecureServer(options);
server.on('stream', (stream) => {
  stream.respond();
  stream.end('ok');
});
server.on('session', (session) => {
  session.origin('https://example.com', 'https://example.org');
});
```

*CommonJS version:*

```js
const http2 = require('node:http2');
const options = getSecureOptionsSomehow();
const server = http2.createSecureServer(options);
server.on('stream', (stream) => {
  stream.respond();
  stream.end('ok');
});
server.on('session', (session) => {
  session.origin('https://example.com', 'https://example.org');
});
```

When a string is passed as an `origin`, it will be parsed as a URL and the
origin will be derived. For instance, the origin for the HTTP URL
`'https://example.org/foo/bar'` is the ASCII string
`'https://example.org'`. An error will be thrown if either the given string
cannot be parsed as a URL or if a valid origin cannot be derived.

A `URL` object, or any object with an `origin` property, may be passed as
an `origin`, in which case the value of the `origin` property will be
used. The value of the `origin` property *must* be a properly serialized
ASCII origin.

Alternatively, the `origins` option may be used when creating a new HTTP/2
server using the `http2.createSecureServer()` method:

*ES module version:*

```js
import { createSecureServer } from 'node:http2';
const options = getSecureOptionsSomehow();
options.origins = ['https://example.com', 'https://example.org'];
const server = createSecureServer(options);
server.on('stream', (stream) => {
  stream.respond();
  stream.end('ok');
});
```

*CommonJS version:*

```js
const http2 = require('node:http2');
const options = getSecureOptionsSomehow();
options.origins = ['https://example.com', 'https://example.org'];
const server = http2.createSecureServer(options);
server.on('stream', (stream) => {
  stream.respond();
  stream.end('ok');
});
```

### Class: `ClientHttp2Session`

Added in: v8.4.0

- Extends: [`<Http2Session>`](28-http2-part1.md#class-http2session)

#### Event: `'altsvc'`

Added in: v9.4.0

- `alt` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type)
- `origin` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type)
- `streamId` [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type)

The `'altsvc'` event is emitted whenever an `ALTSVC` frame is received by
the client. The event is emitted with the `ALTSVC` value, origin, and stream
ID. If no `origin` is provided in the `ALTSVC` frame, `origin` will
be an empty string.

*ES module version:*

```js
import { connect } from 'node:http2';
const client = connect('https://example.org');

client.on('altsvc', (alt, origin, streamId) => {
  console.log(alt);
  console.log(origin);
  console.log(streamId);
});
```

*CommonJS version:*

```js
const http2 = require('node:http2');
const client = http2.connect('https://example.org');

client.on('altsvc', (alt, origin, streamId) => {
  console.log(alt);
  console.log(origin);
  console.log(streamId);
});
```

#### Event: `'origin'`

Added in: v10.12.0

- `origins` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type)[]

The `'origin'` event is emitted whenever an `ORIGIN` frame is received by
the client. The event is emitted with an array of `origin` strings. The
`http2session.originSet` will be updated to include the received
origins.

*ES module version:*

```js
import { connect } from 'node:http2';
const client = connect('https://example.org');

client.on('origin', (origins) => {
  for (let n = 0; n < origins.length; n++)
    console.log(origins[n]);
});
```

*CommonJS version:*

```js
const http2 = require('node:http2');
const client = http2.connect('https://example.org');

client.on('origin', (origins) => {
  for (let n = 0; n < origins.length; n++)
    console.log(origins[n]);
});
```

The `'origin'` event is only emitted when using a secure TLS connection.

#### `clienthttp2session.request(headers[, options])`

Added in: v8.4.0

**History**

| Version | Changes |
| --- | --- |
| v24.2.0 | The `weight` option is now ignored, setting it will trigger a runtime warning. |
| v24.2.0, v22.17.0, v20.19.6 | Following the deprecation of priority signaling as of RFC 9113, `weight` option is deprecated. |
| v24.0.0, v22.17.0 | Allow passing headers in raw array format. |

- `headers` [`<HTTP/2 Headers Object>`](28-http2-part3.md#headers-object) | [`<HTTP/2 Raw Headers>`](28-http2-part3.md#raw-headers)
- `options` [`<Object>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Object)

  - `endStream` [`<boolean>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#boolean_type) `true` if the `Http2Stream` *writable* side should
    be closed initially, such as when sending a `GET` request that should not
    expect a payload body.
  - `exclusive` [`<boolean>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#boolean_type) When `true` and `parent` identifies a parent Stream,
    the created stream is made the sole direct dependency of the parent, with
    all other existing dependents made a dependent of the newly created stream.
    **Default:** `false`.
  - `parent` [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) Specifies the numeric identifier of a stream the newly
    created stream is dependent on.
  - `waitForTrailers` [`<boolean>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#boolean_type) When `true`, the `Http2Stream` will emit the
    `'wantTrailers'` event after the final `DATA` frame has been sent.
  - `signal` [`<AbortSignal>`](26-globals.md#class-abortsignal) An AbortSignal that may be used to abort an ongoing
    request.
- Returns: [`<ClientHttp2Stream>`](28-http2-part1.md#class-clienthttp2stream)

For HTTP/2 Client `Http2Session` instances only, the `http2session.request()`
creates and returns an `Http2Stream` instance that can be used to send an
HTTP/2 request to the connected server.

When a `ClientHttp2Session` is first created, the socket may not yet be
connected. If `clienthttp2session.request()` is called during this time, the
actual request will be deferred until the socket is ready to go.

If the session becomes unavailable before the request can be created, the
returned stream will emit `ERR_HTTP2_GOAWAY_SESSION` or
`ERR_HTTP2_INVALID_SESSION` asynchronously.

This method is only available if `http2session.type` is equal to
`http2.constants.NGHTTP2_SESSION_CLIENT`.

*ES module version:*

```js
import { connect, constants } from 'node:http2';
const clientSession = connect('https://localhost:1234');
const {
  HTTP2_HEADER_PATH,
  HTTP2_HEADER_STATUS,
} = constants;

const req = clientSession.request({ [HTTP2_HEADER_PATH]: '/' });
req.on('response', (headers) => {
  console.log(headers[HTTP2_HEADER_STATUS]);
  req.on('data', (chunk) => { /* .. */ });
  req.on('end', () => { /* .. */ });
});
```

*CommonJS version:*

```js
const http2 = require('node:http2');
const clientSession = http2.connect('https://localhost:1234');
const {
  HTTP2_HEADER_PATH,
  HTTP2_HEADER_STATUS,
} = http2.constants;

const req = clientSession.request({ [HTTP2_HEADER_PATH]: '/' });
req.on('response', (headers) => {
  console.log(headers[HTTP2_HEADER_STATUS]);
  req.on('data', (chunk) => { /* .. */ });
  req.on('end', () => { /* .. */ });
});
```

When the `options.waitForTrailers` option is set, the `'wantTrailers'` event
is emitted immediately after queuing the last chunk of payload data to be sent.
The `http2stream.sendTrailers()` method can then be called to send trailing
headers to the peer.

When `options.waitForTrailers` is set, the `Http2Stream` will not automatically
close when the final `DATA` frame is transmitted. User code must call either
`http2stream.sendTrailers()` or `http2stream.close()` to close the
`Http2Stream`.

When `options.signal` is set with an `AbortSignal` and then `abort` on the
corresponding `AbortController` is called, the request will emit an `'error'`
event with an `AbortError` error.

The `:method` and `:path` pseudo-headers are not specified within `headers`,
they respectively default to:

- `:method` = `'GET'`
- `:path` = `/`

### Class: `Http2Stream`

Added in: v8.4.0

- Extends: [`<stream.Duplex>`](51-stream-part2.md#class-streamduplex)

Each instance of the `Http2Stream` class represents a bidirectional HTTP/2
communications stream over an `Http2Session` instance. Any single `Http2Session`
may have up to 231-1 `Http2Stream` instances over its lifetime.

User code will not construct `Http2Stream` instances directly. Rather, these
are created, managed, and provided to user code through the `Http2Session`
instance. On the server, `Http2Stream` instances are created either in response
to an incoming HTTP request (and handed off to user code via the `'stream'`
event), or in response to a call to the `http2stream.pushStream()` method.
On the client, `Http2Stream` instances are created and returned when either the
`http2session.request()` method is called, or in response to an incoming
`'push'` event.

The `Http2Stream` class is a base for the [`ServerHttp2Stream`](#class-serverhttp2stream) and
[`ClientHttp2Stream`](#class-clienthttp2stream) classes, each of which is used specifically by either
the Server or Client side, respectively.

All `Http2Stream` instances are [`Duplex`](51-stream-part2.md#class-streamduplex) streams. The `Writable` side of the
`Duplex` is used to send data to the connected peer, while the `Readable` side
is used to receive data sent by the connected peer.

The default text character encoding for an `Http2Stream` is UTF-8. When using an
`Http2Stream` to send text, use the `'content-type'` header to set the character
encoding.

```js
stream.respond({
  'content-type': 'text/html; charset=utf-8',
  ':status': 200,
});
```

#### `Http2Stream` Lifecycle

##### Creation

On the server side, instances of [`ServerHttp2Stream`](#class-serverhttp2stream) are created either
when:

- A new HTTP/2 `HEADERS` frame with a previously unused stream ID is received;
- The `http2stream.pushStream()` method is called.

On the client side, instances of [`ClientHttp2Stream`](#class-clienthttp2stream) are created when the
`http2session.request()` method is called.

On the client, the `Http2Stream` instance returned by `http2session.request()`
may not be immediately ready for use if the parent `Http2Session` has not yet
been fully established. In such cases, operations called on the `Http2Stream`
will be buffered until the `'ready'` event is emitted. User code should rarely,
if ever, need to handle the `'ready'` event directly. The ready status of an
`Http2Stream` can be determined by checking the value of `http2stream.id`. If
the value is `undefined`, the stream is not yet ready for use.

##### Destruction

All [`Http2Stream`](#class-http2stream) instances are destroyed either when:

- An `RST_STREAM` frame for the stream is received by the connected peer,
  and (for client streams only) pending data has been read.
- The `http2stream.close()` method is called, and (for client streams only)
  pending data has been read.
- The `http2stream.destroy()` or `http2session.destroy()` methods are called.

When an `Http2Stream` instance is destroyed, an attempt will be made to send an
`RST_STREAM` frame to the connected peer.

When the `Http2Stream` instance is destroyed, the `'close'` event will
be emitted. Because `Http2Stream` is an instance of `stream.Duplex`, the
`'end'` event will also be emitted if the stream data is currently flowing.
The `'error'` event may also be emitted if `http2stream.destroy()` was called
with an `Error` passed as the first argument.

After the `Http2Stream` has been destroyed, the `http2stream.destroyed`
property will be `true` and the `http2stream.rstCode` property will specify the
`RST_STREAM` error code. The `Http2Stream` instance is no longer usable once
destroyed.

#### Event: `'aborted'`

Added in: v8.4.0

The `'aborted'` event is emitted whenever a `Http2Stream` instance is
abnormally aborted in mid-communication.
Its listener does not expect any arguments.

The `'aborted'` event will only be emitted if the `Http2Stream` writable side
has not been ended.

#### Event: `'close'`

Added in: v8.4.0

The `'close'` event is emitted when the `Http2Stream` is destroyed. Once
this event is emitted, the `Http2Stream` instance is no longer usable.

The HTTP/2 error code used when closing the stream can be retrieved using
the `http2stream.rstCode` property. If the code is any value other than
`NGHTTP2_NO_ERROR` (`0`), an `'error'` event will have also been emitted.

#### Event: `'error'`

Added in: v8.4.0

- `error` [`<Error>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Error)

The `'error'` event is emitted when an error occurs during the processing of
an `Http2Stream`.

#### Event: `'frameError'`

Added in: v8.4.0

- `type` [`<integer>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) The frame type.
- `code` [`<integer>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) The error code.
- `id` [`<integer>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) The stream id (or `0` if the frame isn't associated with a
  stream).

The `'frameError'` event is emitted when an error occurs while attempting to
send a frame. When invoked, the handler function will receive an integer
argument identifying the frame type, and an integer argument identifying the
error code. The `Http2Stream` instance will be destroyed immediately after the
`'frameError'` event is emitted.

#### Event: `'ready'`

Added in: v8.4.0

The `'ready'` event is emitted when the `Http2Stream` has been opened, has
been assigned an `id`, and can be used. The listener does not expect any
arguments.

#### Event: `'timeout'`

Added in: v8.4.0

The `'timeout'` event is emitted after no activity is received for this
`Http2Stream` within the number of milliseconds set using
`http2stream.setTimeout()`.
Its listener does not expect any arguments.

#### Event: `'trailers'`

Added in: v8.4.0

- `headers` [`<HTTP/2 Headers Object>`](28-http2-part3.md#headers-object) An object describing the headers
- `flags` [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) The associated numeric flags
- `rawHeaders` [`<HTTP/2 Raw Headers>`](28-http2-part3.md#raw-headers)

The `'trailers'` event is emitted when a block of headers associated with
trailing header fields is received. The listener callback is passed the [HTTP/2 Headers Object](28-http2-part3.md#headers-object), flags associated
with the headers, and the headers in raw format (see [HTTP/2 Raw Headers](28-http2-part3.md#raw-headers)).

This event might not be emitted if `http2stream.end()` is called
before trailers are received and the incoming data is not being read or
listened for.

```js
stream.on('trailers', (headers, flags) => {
  console.log(headers);
});
```

#### Event: `'wantTrailers'`

Added in: v10.0.0

The `'wantTrailers'` event is emitted when the `Http2Stream` has queued the
final `DATA` frame to be sent on a frame and the `Http2Stream` is ready to send
trailing headers. When initiating a request or response, the `waitForTrailers`
option must be set for this event to be emitted.

#### `http2stream.aborted`

Added in: v8.4.0

- Type: [`<boolean>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#boolean_type)

Set to `true` if the `Http2Stream` instance was aborted abnormally. When set,
the `'aborted'` event will have been emitted.

#### `http2stream.bufferSize`

Added in: v11.2.0, v10.16.0

- Type: [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type)

This property shows the number of characters currently buffered to be written.
See [`net.Socket.bufferSize`](38-net.md#socketbuffersize) for details.

#### `http2stream.close(code[, callback])`

Added in: v8.4.0

**History**

| Version | Changes |
| --- | --- |
| v18.0.0 | Passing an invalid callback to the `callback` argument now throws `ERR_INVALID_ARG_TYPE` instead of `ERR_INVALID_CALLBACK`. |

- `code` [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) Unsigned 32-bit integer identifying the error code. **Default:** `http2.constants.NGHTTP2_NO_ERROR` (`0x00`).
- `callback` [`<Function>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Function) An optional function registered to listen for the `'close'` event.

Closes the `Http2Stream` instance by sending an `RST_STREAM` frame to the
connected HTTP/2 peer.

#### `http2stream.closed`

Added in: v9.4.0

- Type: [`<boolean>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#boolean_type)

Set to `true` if the `Http2Stream` instance has been closed.

#### `http2stream.destroyed`

Added in: v8.4.0

- Type: [`<boolean>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#boolean_type)

Set to `true` if the `Http2Stream` instance has been destroyed and is no longer
usable.

#### `http2stream.endAfterHeaders`

Added in: v10.11.0

- Type: [`<boolean>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#boolean_type)

Set to `true` if the `END_STREAM` flag was set in the request or response
HEADERS frame received, indicating that no additional data should be received
and the readable side of the `Http2Stream` will be closed.

#### `http2stream.id`

Added in: v8.4.0

- Type: [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) | [`<undefined>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#undefined_type)

The numeric stream identifier of this `Http2Stream` instance. Set to `undefined`
if the stream identifier has not yet been assigned.

#### `http2stream.pending`

Added in: v9.4.0

- Type: [`<boolean>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#boolean_type)

Set to `true` if the `Http2Stream` instance has not yet been assigned a
numeric stream identifier.

#### `http2stream.priority(options)`

Added in: v8.4.0Deprecated in: v24.2.0, v22.17.0, v20.19.6

**History**

| Version | Changes |
| --- | --- |
| v24.2.0 | This method no longer sets the priority of the stream. Using it now triggers a runtime warning. |

Stability: 0 - Deprecated: support for priority signaling has been deprecated
in the [RFC 9113](https://datatracker.ietf.org/doc/html/rfc9113#section-5.3.1) and is no longer supported in Node.js.

Empty method, only there to maintain some backward compatibility.

#### `http2stream.rstCode`

Added in: v8.4.0

- Type: [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type)

Set to the `RST_STREAM` [error code](28-http2-part3.md#error-codes-for-rst_stream-and-goaway) reported when the `Http2Stream` is
destroyed after either receiving an `RST_STREAM` frame from the connected peer,
calling `http2stream.close()`, or `http2stream.destroy()`. Will be
`undefined` if the `Http2Stream` has not been closed.

#### `http2stream.sentHeaders`

Added in: v9.5.0

- Type: [`<HTTP/2 Headers Object>`](28-http2-part3.md#headers-object)

An object containing the outbound headers sent for this `Http2Stream`.

#### `http2stream.sentInfoHeaders`

Added in: v9.5.0

- Type: [`<HTTP/2 Headers Object>`](28-http2-part3.md#headers-object)[]

An array of objects containing the outbound informational (additional) headers
sent for this `Http2Stream`.

#### `http2stream.sentTrailers`

Added in: v9.5.0

- Type: [`<HTTP/2 Headers Object>`](28-http2-part3.md#headers-object)

An object containing the outbound trailers sent for this `HttpStream`.

#### `http2stream.session`

Added in: v8.4.0

- Type: [`<Http2Session>`](28-http2-part1.md#class-http2session)

A reference to the `Http2Session` instance that owns this `Http2Stream`. The
value will be `undefined` after the `Http2Stream` instance is destroyed.

#### `http2stream.setTimeout(msecs, callback)`

Added in: v8.4.0

**History**

| Version | Changes |
| --- | --- |
| v18.0.0 | Passing an invalid callback to the `callback` argument now throws `ERR_INVALID_ARG_TYPE` instead of `ERR_INVALID_CALLBACK`. |

- `msecs` [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type)
- `callback` [`<Function>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Function)

*ES module version:*

```js
import { connect, constants } from 'node:http2';
const client = connect('http://example.org:8000');
const { NGHTTP2_CANCEL } = constants;
const req = client.request({ ':path': '/' });

// Cancel the stream if there's no activity after 5 seconds
req.setTimeout(5000, () => req.close(NGHTTP2_CANCEL));
```

*CommonJS version:*

```js
const http2 = require('node:http2');
const client = http2.connect('http://example.org:8000');
const { NGHTTP2_CANCEL } = http2.constants;
const req = client.request({ ':path': '/' });

// Cancel the stream if there's no activity after 5 seconds
req.setTimeout(5000, () => req.close(NGHTTP2_CANCEL));
```

#### `http2stream.state`

Added in: v8.4.0

**History**

| Version | Changes |
| --- | --- |
| v24.2.0 | The `state.weight` property is now always set to 16 and `sumDependencyWeight` is always set to 0. |
| v24.2.0, v22.17.0, v20.19.6 | Following the deprecation of priority signaling as of RFC 9113, `weight` and `sumDependencyWeight` options are deprecated. |

Provides miscellaneous information about the current state of the
`Http2Stream`.

- Type: [`<Object>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Object)
  - `localWindowSize` [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) The number of bytes the connected peer may send
    for this `Http2Stream` without receiving a `WINDOW_UPDATE`.
  - `state` [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) A flag indicating the low-level current state of the `Http2Stream` as determined by `nghttp2`.
  - `localClose` [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) `1` if this `Http2Stream` has been closed locally.
  - `remoteClose` [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) `1` if this `Http2Stream` has been closed
    remotely.
  - `sumDependencyWeight` [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) Legacy property, always set to `0`.
  - `weight` [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) Legacy property, always set to `16`.

A current state of this `Http2Stream`.

#### `http2stream.sendTrailers(headers)`

Added in: v10.0.0

- `headers` [`<HTTP/2 Headers Object>`](28-http2-part3.md#headers-object)

Sends a trailing `HEADERS` frame to the connected HTTP/2 peer. This method
will cause the `Http2Stream` to be immediately closed and must only be
called after the `'wantTrailers'` event has been emitted. When sending a
request or sending a response, the `options.waitForTrailers` option must be set
in order to keep the `Http2Stream` open after the final `DATA` frame so that
trailers can be sent.

*ES module version:*

```js
import { createServer } from 'node:http2';
const server = createServer();
server.on('stream', (stream) => {
  stream.respond(undefined, { waitForTrailers: true });
  stream.on('wantTrailers', () => {
    stream.sendTrailers({ xyz: 'abc' });
  });
  stream.end('Hello World');
});
```

*CommonJS version:*

```js
const http2 = require('node:http2');
const server = http2.createServer();
server.on('stream', (stream) => {
  stream.respond(undefined, { waitForTrailers: true });
  stream.on('wantTrailers', () => {
    stream.sendTrailers({ xyz: 'abc' });
  });
  stream.end('Hello World');
});
```

The HTTP/1 specification forbids trailers from containing HTTP/2 pseudo-header
fields (e.g. `':method'`, `':path'`, etc).

### Class: `ClientHttp2Stream`

Added in: v8.4.0

- Extends [`<Http2Stream>`](28-http2-part1.md#class-http2stream)

The `ClientHttp2Stream` class is an extension of `Http2Stream` that is
used exclusively on HTTP/2 Clients. `Http2Stream` instances on the client
provide events such as `'response'` and `'push'` that are only relevant on
the client.

#### Event: `'continue'`

Added in: v8.5.0

Emitted when the server sends a `100 Continue` status, usually because
the request contained `Expect: 100-continue`. This is an instruction that
the client should send the request body.

#### Event: `'headers'`

Added in: v8.4.0

- `headers` [`<HTTP/2 Headers Object>`](28-http2-part3.md#headers-object)
- `flags` [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type)
- `rawHeaders` [`<HTTP/2 Raw Headers>`](28-http2-part3.md#raw-headers)

The `'headers'` event is emitted when an additional block of headers is received
for a stream, such as when a block of `1xx` informational headers is received.
The listener callback is passed the [HTTP/2 Headers Object](28-http2-part3.md#headers-object), flags associated
with the headers, and the headers in raw format (see [HTTP/2 Raw Headers](28-http2-part3.md#raw-headers)).

```js
stream.on('headers', (headers, flags) => {
  console.log(headers);
});
```

#### Event: `'push'`

Added in: v8.4.0

- `headers` [`<HTTP/2 Headers Object>`](28-http2-part3.md#headers-object)
- `flags` [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type)
- `rawHeaders` [`<HTTP/2 Raw Headers>`](28-http2-part3.md#raw-headers)

The `'push'` event is emitted when response headers for a Server Push stream
are received. The listener callback is passed the [HTTP/2 Headers Object](28-http2-part3.md#headers-object), flags associated
with the headers, and the headers in raw format (see [HTTP/2 Raw Headers](28-http2-part3.md#raw-headers)).

```js
stream.on('push', (headers, flags) => {
  console.log(headers);
});
```

#### Event: `'response'`

Added in: v8.4.0

- `headers` [`<HTTP/2 Headers Object>`](28-http2-part3.md#headers-object)
- `flags` [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type)
- `rawHeaders` [`<HTTP/2 Raw Headers>`](28-http2-part3.md#raw-headers)

The `'response'` event is emitted when a response `HEADERS` frame has been
received for this stream from the connected HTTP/2 server. The listener is
invoked with three arguments: an `Object` containing the received
[HTTP/2 Headers Object](28-http2-part3.md#headers-object), flags associated with the headers, and the headers
in raw format (see [HTTP/2 Raw Headers](28-http2-part3.md#raw-headers)).

*ES module version:*

```js
import { connect } from 'node:http2';
const client = connect('https://localhost');
const req = client.request({ ':path': '/' });
req.on('response', (headers, flags) => {
  console.log(headers[':status']);
});
```

*CommonJS version:*

```js
const http2 = require('node:http2');
const client = http2.connect('https://localhost');
const req = client.request({ ':path': '/' });
req.on('response', (headers, flags) => {
  console.log(headers[':status']);
});
```

### Class: `ServerHttp2Stream`

Added in: v8.4.0

- Extends: [`<Http2Stream>`](28-http2-part1.md#class-http2stream)

The `ServerHttp2Stream` class is an extension of [`Http2Stream`](#class-http2stream) that is
used exclusively on HTTP/2 Servers. `Http2Stream` instances on the server
provide additional methods such as `http2stream.pushStream()` and
`http2stream.respond()` that are only relevant on the server.

#### `http2stream.additionalHeaders(headers)`

Added in: v8.4.0

- `headers` [`<HTTP/2 Headers Object>`](28-http2-part3.md#headers-object)

Sends an additional informational `HEADERS` frame to the connected HTTP/2 peer.

#### `http2stream.headersSent`

Added in: v8.4.0

- Type: [`<boolean>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#boolean_type)

True if headers were sent, false otherwise (read-only).

#### `http2stream.pushAllowed`

Added in: v8.4.0

- Type: [`<boolean>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#boolean_type)

Read-only property mapped to the `SETTINGS_ENABLE_PUSH` flag of the remote
client's most recent `SETTINGS` frame. Will be `true` if the remote peer
accepts push streams, `false` otherwise. Settings are the same for every
`Http2Stream` in the same `Http2Session`.

[Contents](../README.md) | [Previous: HTTP, part 3 of 3](27-http-part3.md) | [Next: HTTP/2, part 2 of 4](28-http2-part2.md)
