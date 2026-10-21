[Contents](../README.md) | [Previous: HTTP, part 1 of 3](27-http-part1.md) | [Next: HTTP, part 3 of 3](27-http-part3.md)

Source: [https://nodejs.org/docs/latest/api/http.html](https://nodejs.org/docs/latest/api/http.html)

Part 2 of 3 of this section.

# HTTP (part 2 of 3)

### `server.close([callback])`

Added in: v0.1.90

**History**

| Version | Changes |
| --- | --- |
| v19.0.0 | The method closes idle connections before returning. |

- `callback` [`<Function>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Function)

Stops the server from accepting new connections and closes all connections
connected to this server which are not sending a request or waiting for
a response.
See [`net.Server.close()`](38-net.md#serverclosecallback).

```js
const http = require('node:http');

const server = http.createServer({ keepAliveTimeout: 60000 }, (req, res) => {
  res.writeHead(200, { 'Content-Type': 'application/json' });
  res.end(JSON.stringify({
    data: 'Hello World!',
  }));
});

server.listen(8000);
// Close the server after 10 seconds
setTimeout(() => {
  server.close(() => {
    console.log('server on port 8000 closed successfully');
  });
}, 10000);
```

### `server.closeAllConnections()`

Added in: v18.2.0

Closes all established HTTP(S) connections connected to this server, including
active connections connected to this server which are sending a request or
waiting for a response. This does *not* destroy sockets upgraded to a different
protocol, such as WebSocket or HTTP/2.

> This is a forceful way of closing all connections and should be used with
> caution. Whenever using this in conjunction with `server.close`, calling this
> *after* `server.close` is recommended as to avoid race conditions where new
> connections are created between a call to this and a call to `server.close`.

```js
const http = require('node:http');

const server = http.createServer({ keepAliveTimeout: 60000 }, (req, res) => {
  res.writeHead(200, { 'Content-Type': 'application/json' });
  res.end(JSON.stringify({
    data: 'Hello World!',
  }));
});

server.listen(8000);
// Close the server after 10 seconds
setTimeout(() => {
  server.close(() => {
    console.log('server on port 8000 closed successfully');
  });
  // Closes all connections, ensuring the server closes successfully
  server.closeAllConnections();
}, 10000);
```

### `server.closeIdleConnections()`

Added in: v18.2.0

Closes all connections connected to this server which are not sending a request
or waiting for a response.

> Starting with Node.js 19.0.0, there's no need for calling this method in
> conjunction with `server.close` to reap `keep-alive` connections. Using it
> won't cause any harm though, and it can be useful to ensure backwards
> compatibility for libraries and applications that need to support versions
> older than 19.0.0. Whenever using this in conjunction with `server.close`,
> calling this *after* `server.close` is recommended as to avoid race
> conditions where new connections are created between a call to this and a
> call to `server.close`.

```js
const http = require('node:http');

const server = http.createServer({ keepAliveTimeout: 60000 }, (req, res) => {
  res.writeHead(200, { 'Content-Type': 'application/json' });
  res.end(JSON.stringify({
    data: 'Hello World!',
  }));
});

server.listen(8000);
// Close the server after 10 seconds
setTimeout(() => {
  server.close(() => {
    console.log('server on port 8000 closed successfully');
  });
  // Closes idle connections, such as keep-alive connections. Server will close
  // once remaining active connections are terminated
  server.closeIdleConnections();
}, 10000);
```

### `server.headersTimeout`

Added in: v11.3.0, v10.14.0

**History**

| Version | Changes |
| --- | --- |
| v19.4.0, v18.14.0 | The default is now set to the minimum between 60000 (60 seconds) or `requestTimeout`. |

- Type: [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) **Default:** The minimum between [`server.requestTimeout`](#serverrequesttimeout) or `60000`.

Limit the amount of time the parser will wait to receive the complete HTTP
headers.

If the timeout expires, the server responds with status 408 without
forwarding the request to the request listener and then closes the connection.

It must be set to a non-zero value (e.g. 120 seconds) to protect against
potential Denial-of-Service attacks in case the server is deployed without a
reverse proxy in front.

### `server.listen()`

Starts the HTTP server listening for connections.
This method is identical to [`server.listen()`](38-net.md#serverlisten) from [`net.Server`](38-net.md#class-netserver).

### `server.listening`

Added in: v5.7.0

- Type: [`<boolean>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#boolean_type) Indicates whether or not the server is listening for connections.

### `server.maxHeadersCount`

Added in: v0.7.0

- Type: [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) **Default:** `1000`

Limits maximum incoming headers count. If set to 0, no limit will be applied.

### `server.requestTimeout`

Added in: v14.11.0

**History**

| Version | Changes |
| --- | --- |
| v18.0.0 | The default request timeout changed from no timeout to 300s (5 minutes). |

- Type: [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) **Default:** `300000`

Sets the timeout value in milliseconds for receiving the entire request from
the client.

If the timeout expires, the server responds with status 408 without
forwarding the request to the request listener and then closes the connection.

It must be set to a non-zero value (e.g. 120 seconds) to protect against
potential Denial-of-Service attacks in case the server is deployed without a
reverse proxy in front.

### `server.setTimeout([msecs][, callback])`

Added in: v0.9.12

**History**

| Version | Changes |
| --- | --- |
| v13.0.0 | The default timeout changed from 120s to 0 (no timeout). |

- `msecs` [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) **Default:** 0 (no timeout)
- `callback` [`<Function>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Function)
- Returns: [`<http.Server>`](27-http-part1.md#class-httpserver)

Sets the timeout value for sockets, and emits a `'timeout'` event on
the Server object, passing the socket as an argument, if a timeout
occurs.

If there is a `'timeout'` event listener on the Server object, then it
will be called with the timed-out socket as an argument.

By default, the Server does not timeout sockets. However, if a callback
is assigned to the Server's `'timeout'` event, timeouts must be handled
explicitly.

### `server.maxRequestsPerSocket`

Added in: v16.10.0

- Type: [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) Requests per socket. **Default:** 0 (no limit)

The maximum number of requests socket can handle
before closing keep alive connection.

A value of `0` will disable the limit.

When the limit is reached it will set the `Connection` header value to `close`,
but will not actually close the connection, subsequent requests sent
after the limit is reached will get `503 Service Unavailable` as a response.

### `server.timeout`

Added in: v0.9.12

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

### `server.keepAliveTimeout`

Added in: v8.0.0

- Type: [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) Timeout in milliseconds. **Default:** `5000` (5 seconds).

The number of milliseconds of inactivity a server needs to wait for additional
incoming data, after it has finished writing the last response, before a socket
will be destroyed.

This timeout value is combined with the
[`server.keepAliveTimeoutBuffer`](#serverkeepalivetimeoutbuffer) option to determine the actual socket
timeout, calculated as:
socketTimeout = keepAliveTimeout + keepAliveTimeoutBuffer
If the server receives new data before the keep-alive timeout has fired, it
will reset the regular inactivity timeout, i.e., [`server.timeout`](#servertimeout).

A value of `0` will disable the keep-alive timeout behavior on incoming
connections.
A value of `0` makes the HTTP server behave similarly to Node.js versions prior
to 8.0.0, which did not have a keep-alive timeout.

The socket timeout logic is set up on connection, so changing this value only
affects new connections to the server, not any existing connections.

### `server.keepAliveTimeoutBuffer`

Added in: v24.6.0, v22.19.0

- Type: [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) Timeout in milliseconds. **Default:** `1000` (1 second).

An additional buffer time added to the
[`server.keepAliveTimeout`](#serverkeepalivetimeout) to extend the internal socket timeout.

This buffer helps reduce connection reset (`ECONNRESET`) errors by increasing
the socket timeout slightly beyond the advertised keep-alive timeout.

This option applies only to new incoming connections.

### `server[Symbol.asyncDispose]()`

Added in: v20.4.0

**History**

| Version | Changes |
| --- | --- |
| v24.2.0 | No longer experimental. |

Calls [`server.close()`](#serverclosecallback) and returns a promise that fulfills when the
server has closed.

## Class: `http.ServerResponse`

Added in: v0.1.17

- Extends: [`<http.OutgoingMessage>`](27-http-part2.md#class-httpoutgoingmessage)

This object is created internally by an HTTP server, not by the user. It is
passed as the second parameter to the [`'request'`](27-http-part1.md#event-request) event.

### Event: `'close'`

Added in: v0.6.7

Indicates that the response is completed, or its underlying connection was
terminated prematurely (before the response completion).

### Event: `'finish'`

Added in: v0.3.6

Emitted when the response has been sent. More specifically, this event is
emitted when the last segment of the response headers and body have been
handed off to the operating system for transmission over the network. It
does not imply that the client has received anything yet.

### `response.addTrailers(headers)`

Added in: v0.3.0

- `headers` [`<Object>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Object)

This method adds HTTP trailing headers (a header but at the end of the
message) to the response.

Trailers will **only** be emitted if chunked encoding is used for the
response; if it is not (e.g. if the request was HTTP/1.0), they will
be silently discarded.

HTTP requires the `Trailer` header to be sent in order to
emit trailers, with a list of the header fields in its value. E.g.,

```js
response.writeHead(200, { 'Content-Type': 'text/plain',
                          'Trailer': 'Content-MD5' });
response.write(fileData);
response.addTrailers({ 'Content-MD5': '7895bf4b8828b55ceaf47747b4bca667' });
response.end();
```

Attempting to set a header field name or value that contains invalid characters
will result in a [`TypeError`](22-errors-part1.md#class-typeerror) being thrown.

### `response.connection`

Added in: v0.3.0Deprecated in: v13.0.0

Stability: 0 - Deprecated. Use [`response.socket`](#responsesocket).

- Type: [`<stream.Duplex>`](51-stream-part2.md#class-streamduplex)

See [`response.socket`](#responsesocket).

### `response.cork()`

Added in: v13.2.0, v12.16.0

See [`writable.cork()`](51-stream-part1.md#writablecork).

### `response.end([data[, encoding]][, callback])`

Added in: v0.1.90

**History**

| Version | Changes |
| --- | --- |
| v15.0.0 | The `data` parameter can now be a `Uint8Array`. |
| v10.0.0 | This method now returns a reference to `ServerResponse`. |

- `data` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) | [`<Buffer>`](07-buffer-part1.md#class-buffer) | [`<Uint8Array>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Uint8Array)
- `encoding` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type)
- `callback` [`<Function>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Function)
- Returns: [`<this>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Operators/this)

This method signals to the server that all of the response headers and body
have been sent; that server should consider this message complete.
The method, `response.end()`, MUST be called on each response.

If `data` is specified, it is similar in effect to calling
[`response.write(data, encoding)`](#responsewritechunk-encoding-callback) followed by `response.end(callback)`.

If `callback` is specified, it will be called when the response stream
is finished.

### `response.finished`

Added in: v0.0.2Deprecated in: v13.4.0, v12.16.0

Stability: 0 - Deprecated. Use [`response.writableEnded`](#responsewritableended).

- Type: [`<boolean>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#boolean_type)

The `response.finished` property will be `true` if [`response.end()`](#responseenddata-encoding-callback)
has been called.

### `response.flushHeaders()`

Added in: v1.6.0

Flushes the response headers. See also: [`request.flushHeaders()`](27-http-part1.md#requestflushheaders).

### `response.getHeader(name)`

Added in: v0.4.0

- `name` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type)
- Returns: [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) | [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) | [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type)[] | [`<undefined>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#undefined_type)

Reads out a header that's already been queued but not sent to the client.
The name is case-insensitive. The type of the return value depends
on the arguments provided to [`response.setHeader()`](#responsesetheadername-value).

```js
response.setHeader('Content-Type', 'text/html');
response.setHeader('Content-Length', Buffer.byteLength(body));
response.setHeader('Set-Cookie', ['type=ninja', 'language=javascript']);
const contentType = response.getHeader('content-type');
// contentType is 'text/html'
const contentLength = response.getHeader('Content-Length');
// contentLength is of type number
const setCookie = response.getHeader('set-cookie');
// setCookie is of type string[]
```

### `response.getHeaderNames()`

Added in: v7.7.0

- Returns: [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type)[]

Returns an array containing the unique names of the current outgoing headers.
All header names are lowercase.

```js
response.setHeader('Foo', 'bar');
response.setHeader('Set-Cookie', ['foo=bar', 'bar=baz']);

const headerNames = response.getHeaderNames();
// headerNames === ['foo', 'set-cookie']
```

### `response.getHeaders()`

Added in: v7.7.0

- Returns: [`<Object>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Object)

Returns a shallow copy of the current outgoing headers. Since a shallow copy
is used, array values may be mutated without additional calls to various
header-related http module methods. The keys of the returned object are the
header names and the values are the respective header values. All header names
are lowercase.

The object returned by the `response.getHeaders()` method *does not*
prototypically inherit from the JavaScript `Object`. This means that typical
`Object` methods such as `obj.toString()`, `obj.hasOwnProperty()`, and others
are not defined and *will not work*.

```js
response.setHeader('Foo', 'bar');
response.setHeader('Set-Cookie', ['foo=bar', 'bar=baz']);

const headers = response.getHeaders();
// headers === { foo: 'bar', 'set-cookie': ['foo=bar', 'bar=baz'] }
```

### `response.hasHeader(name)`

Added in: v7.7.0

- `name` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type)
- Returns: [`<boolean>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#boolean_type)

Returns `true` if the header identified by `name` is currently set in the
outgoing headers. The header name matching is case-insensitive.

```js
const hasContentType = response.hasHeader('content-type');
```

### `response.headersSent`

Added in: v0.9.3

- Type: [`<boolean>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#boolean_type)

Boolean (read-only). True if headers were sent, false otherwise.

### `response.removeHeader(name)`

Added in: v0.4.0

- `name` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type)

Removes a header that's queued for implicit sending.

```js
response.removeHeader('Content-Encoding');
```

### `response.req`

Added in: v15.7.0

- Type: [`<http.IncomingMessage>`](27-http-part2.md#class-httpincomingmessage)

A reference to the original HTTP `request` object.

### `response.sendDate`

Added in: v0.7.5

- Type: [`<boolean>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#boolean_type)

When true, the Date header will be automatically generated and sent in
the response if it is not already present in the headers. Defaults to true.

This should only be disabled for testing; the Date header is required in
most HTTP responses (see [RFC 9110 Section 6.6.1](https://www.rfc-editor.org/rfc/rfc9110#section-6.6.1) for details).

### `response.setHeader(name, value)`

Added in: v0.4.0

- `name` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type)
- `value` [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) | [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) | [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type)[]
- Returns: [`<http.ServerResponse>`](27-http-part2.md#class-httpserverresponse)

Returns the response object.

Sets a single header value for implicit headers. If this header already exists
in the to-be-sent headers, its value will be replaced. Use an array of strings
here to send multiple headers with the same name. Non-string values will be
stored without modification. Therefore, [`response.getHeader()`](#responsegetheadername) may return
non-string values. However, the non-string values will be converted to strings
for network transmission. The same response object is returned to the caller,
to enable call chaining.

```js
response.setHeader('Content-Type', 'text/html');
```

or

```js
response.setHeader('Set-Cookie', ['type=ninja', 'language=javascript']);
```

Attempting to set a header field name or value that contains invalid characters
will result in a [`TypeError`](22-errors-part1.md#class-typeerror) being thrown.

When headers have been set with [`response.setHeader()`](#responsesetheadername-value), they will be merged
with any headers passed to [`response.writeHead()`](#responsewriteheadstatuscode-statusmessage-headers), with the headers passed
to [`response.writeHead()`](#responsewriteheadstatuscode-statusmessage-headers) given precedence.

```js
// Returns content-type = text/plain
const server = http.createServer((req, res) => {
  res.setHeader('Content-Type', 'text/html');
  res.setHeader('X-Foo', 'bar');
  res.writeHead(200, { 'Content-Type': 'text/plain' });
  res.end('ok');
});
```

If [`response.writeHead()`](#responsewriteheadstatuscode-statusmessage-headers) method is called and this method has not been
called, it will directly write the supplied header values onto the network
channel without caching internally, and the [`response.getHeader()`](#responsegetheadername) on the
header will not yield the expected result. If progressive population of headers
is desired with potential future retrieval and modification, use
[`response.setHeader()`](#responsesetheadername-value) instead of [`response.writeHead()`](#responsewriteheadstatuscode-statusmessage-headers).

### `response.setTimeout(msecs[, callback])`

Added in: v0.9.12

- `msecs` [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type)
- `callback` [`<Function>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Function)
- Returns: [`<http.ServerResponse>`](27-http-part2.md#class-httpserverresponse)

Sets the Socket's timeout value to `msecs`. If a callback is
provided, then it is added as a listener on the `'timeout'` event on
the response object.

If no `'timeout'` listener is added to the request, the response, or
the server, then sockets are destroyed when they time out. If a handler is
assigned to the request, the response, or the server's `'timeout'` events,
timed out sockets must be handled explicitly.

### `response.socket`

Added in: v0.3.0

- Type: [`<stream.Duplex>`](51-stream-part2.md#class-streamduplex)

Reference to the underlying socket. Usually users will not want to access
this property. In particular, the socket will not emit `'readable'` events
because of how the protocol parser attaches to the socket. After
`response.end()`, the property is nulled.

*ES module version:*

```js
import http from 'node:http';
const server = http.createServer((req, res) => {
  const ip = res.socket.remoteAddress;
  const port = res.socket.remotePort;
  res.end(`Your IP address is ${ip} and your source port is ${port}.`);
}).listen(3000);
```

*CommonJS version:*

```js
const http = require('node:http');
const server = http.createServer((req, res) => {
  const ip = res.socket.remoteAddress;
  const port = res.socket.remotePort;
  res.end(`Your IP address is ${ip} and your source port is ${port}.`);
}).listen(3000);
```

This property is guaranteed to be an instance of the [`<net.Socket>`](38-net.md#class-netsocket) class,
a subclass of [`<stream.Duplex>`](51-stream-part2.md#class-streamduplex), unless the user specified a socket
type other than [`<net.Socket>`](38-net.md#class-netsocket).

### `response.statusCode`

Added in: v0.4.0

- Type: [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) **Default:** `200`

When using implicit headers (not calling [`response.writeHead()`](#responsewriteheadstatuscode-statusmessage-headers) explicitly),
this property controls the status code that will be sent to the client when
the headers get flushed.

```js
response.statusCode = 404;
```

After response header was sent to the client, this property indicates the
status code which was sent out.

### `response.statusMessage`

Added in: v0.11.8

- Type: [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type)

When using implicit headers (not calling [`response.writeHead()`](#responsewriteheadstatuscode-statusmessage-headers) explicitly),
this property controls the status message that will be sent to the client when
the headers get flushed. If this is left as `undefined` then the standard
message for the status code will be used.

```js
response.statusMessage = 'Not found';
```

After response header was sent to the client, this property indicates the
status message which was sent out.

### `response.strictContentLength`

Added in: v18.10.0, v16.18.0

- Type: [`<boolean>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#boolean_type) **Default:** `false`

If set to `true`, Node.js will check whether the `Content-Length`
header value and the size of the body, in bytes, are equal.
Mismatching the `Content-Length` header value will result
in an `Error` being thrown, identified by `code:` [`'ERR_HTTP_CONTENT_LENGTH_MISMATCH'`](22-errors-part2.md#err_http_content_length_mismatch).

### `response.uncork()`

Added in: v13.2.0, v12.16.0

See [`writable.uncork()`](51-stream-part1.md#writableuncork).

### `response.writableEnded`

Added in: v12.9.0

- Type: [`<boolean>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#boolean_type)

Is `true` after [`response.end()`](#responseenddata-encoding-callback) has been called. This property
does not indicate whether the data has been flushed, for this use
[`response.writableFinished`](#responsewritablefinished) instead.

### `response.writableFinished`

Added in: v12.7.0

- Type: [`<boolean>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#boolean_type)

Is `true` if all data has been flushed to the underlying system, immediately
before the [`'finish'`](27-http-part1.md#event-finish) event is emitted.

### `response.write(chunk[, encoding][, callback])`

Added in: v0.1.29

**History**

| Version | Changes |
| --- | --- |
| v15.0.0 | The `chunk` parameter can now be a `Uint8Array`. |

- `chunk` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) | [`<Buffer>`](07-buffer-part1.md#class-buffer) | [`<Uint8Array>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Uint8Array)
- `encoding` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) **Default:** `'utf8'`
- `callback` [`<Function>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Function)
- Returns: [`<boolean>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#boolean_type)

If this method is called and [`response.writeHead()`](#responsewriteheadstatuscode-statusmessage-headers) has not been called,
it will switch to implicit header mode and flush the implicit headers.

This sends a chunk of the response body. This method may
be called multiple times to provide successive parts of the body.

If `rejectNonStandardBodyWrites` is set to true in `createServer`
then writing to the body is not allowed when the request method or response
status do not support content. If an attempt is made to write to the body for a
HEAD request or as part of a `204` or `304`response, a synchronous `Error`
with the code `ERR_HTTP_BODY_NOT_ALLOWED` is thrown.

`chunk` can be a string or a buffer. If `chunk` is a string,
the second parameter specifies how to encode it into a byte stream.
`callback` will be called when this chunk of data is flushed.

This is the raw HTTP body and has nothing to do with higher-level multi-part
body encodings that may be used.

The first time [`response.write()`](#responsewritechunk-encoding-callback) is called, it will send the buffered
header information and the first chunk of the body to the client. The second
time [`response.write()`](#responsewritechunk-encoding-callback) is called, Node.js assumes data will be streamed,
and sends the new data separately. That is, the response is buffered up to the
first chunk of the body.

Returns `true` if the entire data was flushed successfully to the kernel
buffer. Returns `false` if all or part of the data was queued in user memory.
`'drain'` will be emitted when the buffer is free again.

### `response.writeContinue()`

Added in: v0.3.0

Sends an HTTP/1.1 100 Continue message to the client, indicating that
the request body should be sent. See the [`'checkContinue'`](27-http-part1.md#event-checkcontinue) event on
`Server`.

### `response.writeEarlyHints(hints[, callback])`

Added in: v18.11.0

**History**

| Version | Changes |
| --- | --- |
| v18.11.0 | Allow passing hints as an object. |

- `hints` [`<Object>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Object)
- `callback` [`<Function>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Function)

Sends an HTTP/1.1 103 Early Hints message to the client with a Link header,
indicating that the user agent can preload/preconnect the linked resources.
The `hints` is an object containing the values of headers to be sent with
early hints message. The optional `callback` argument will be called when
the response message has been written.

**Example**

```js
const earlyHintsLink = '</styles.css>; rel=preload; as=style';
response.writeEarlyHints({
  'link': earlyHintsLink,
});

const earlyHintsLinks = [
  '</styles.css>; rel=preload; as=style',
  '</scripts.js>; rel=preload; as=script',
];
response.writeEarlyHints({
  'link': earlyHintsLinks,
  'x-trace-id': 'id for diagnostics',
});

const earlyHintsCallback = () => console.log('early hints message sent');
response.writeEarlyHints({
  'link': earlyHintsLinks,
}, earlyHintsCallback);
```

### `response.writeHead(statusCode[, statusMessage][, headers])`

Added in: v0.1.30

**History**

| Version | Changes |
| --- | --- |
| v14.14.0 | Allow passing headers as an array. |
| v11.10.0, v10.17.0 | Return `this` from `writeHead()` to allow chaining with `end()`. |
| v5.11.0, v4.4.5 | A `RangeError` is thrown if `statusCode` is not a number in the range `[100, 999]`. |

- `statusCode` [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type)
- `statusMessage` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type)
- `headers` [`<Object>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Object) | [`<Array>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Array)
- Returns: [`<http.ServerResponse>`](27-http-part2.md#class-httpserverresponse)

Sends a response header to the request. The status code is a 3-digit HTTP
status code, like `404`. The last argument, `headers`, are the response headers.
Optionally one can give a human-readable `statusMessage` as the second
argument.

`headers` may be an `Array` where the keys and values are in the same list.
It is *not* a list of tuples. So, the even-numbered offsets are key values,
and the odd-numbered offsets are the associated values. The array is in the same
format as `request.rawHeaders`.

Returns a reference to the `ServerResponse`, so that calls can be chained.

```js
const body = 'hello world';
response
  .writeHead(200, {
    'Content-Length': Buffer.byteLength(body),
    'Content-Type': 'text/plain',
  })
  .end(body);
```

This method must only be called once on a message and it must
be called before [`response.end()`](#responseenddata-encoding-callback) is called.

If [`response.write()`](#responsewritechunk-encoding-callback) or [`response.end()`](#responseenddata-encoding-callback) are called before calling
this, the implicit/mutable headers will be calculated and call this function.

When headers have been set with [`response.setHeader()`](#responsesetheadername-value), they will be merged
with any headers passed to [`response.writeHead()`](#responsewriteheadstatuscode-statusmessage-headers), with the headers passed
to [`response.writeHead()`](#responsewriteheadstatuscode-statusmessage-headers) given precedence.

If this method is called and [`response.setHeader()`](#responsesetheadername-value) has not been called,
it will directly write the supplied header values onto the network channel
without caching internally, and the [`response.getHeader()`](#responsegetheadername) on the header
will not yield the expected result. If progressive population of headers is
desired with potential future retrieval and modification, use
[`response.setHeader()`](#responsesetheadername-value) instead.

```js
// Returns content-type = text/plain
const server = http.createServer((req, res) => {
  res.setHeader('Content-Type', 'text/html');
  res.setHeader('X-Foo', 'bar');
  res.writeHead(200, { 'Content-Type': 'text/plain' });
  res.end('ok');
});
```

`Content-Length` is read in bytes, not characters. Use
[`Buffer.byteLength()`](07-buffer-part1.md#static-method-bufferbytelengthstring-encoding) to determine the length of the body in bytes. Node.js
will check whether `Content-Length` and the length of the body which has
been transmitted are equal or not.

Attempting to set a header field name or value that contains invalid characters
will result in a [`TypeError`](22-errors-part1.md#class-typeerror) being thrown.

### `response.writeInformation(statusCode[, headers][, callback])`

Added in: v26.2.0

- `statusCode` [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) An HTTP 1xx informational status code, between `100`
  and `199` inclusive, excluding `101` (Switching Protocols) which is only
  available through the [`'upgrade'`](27-http-part1.md#event-upgrade) event.
- `headers` [`<Object>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Object) | [`<Array>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Array) An optional set of headers to send with the
  informational response. Accepts the same shapes as [`response.writeHead()`](#responsewriteheadstatuscode-statusmessage-headers).
- `callback` [`<Function>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Function) Optional, called once the message has been written
  to the socket.

Sends an arbitrary HTTP/1.1 1xx informational response to the client. This
is a generic equivalent of [`response.writeContinue()`](#responsewritecontinue),
[`response.writeProcessing()`](#responsewriteprocessing) and [`response.writeEarlyHints()`](#responsewriteearlyhintshints-callback), and
can be called multiple times before the final response. After the final
response headers have been sent (via [`response.writeHead()`](#responsewriteheadstatuscode-statusmessage-headers) or an
implicit header), calling this method throws `ERR_HTTP_HEADERS_SENT`.

Clients receive these responses via the [`'information'`](27-http-part1.md#event-information)
event on `http.ClientRequest`.

```js
response.writeInformation(110, { 'X-Progress': '50%' });
```

### `response.writeProcessing()`

Added in: v10.0.0

Sends an HTTP/1.1 102 Processing message to the client, indicating that
the request body should be sent.

## Class: `http.IncomingMessage`

Added in: v0.1.17

**History**

| Version | Changes |
| --- | --- |
| v15.5.0 | The `destroyed` value returns `true` after the incoming data is consumed. |
| v13.1.0, v12.16.0 | The `readableHighWaterMark` value mirrors that of the socket. |

- Extends: [`<stream.Readable>`](51-stream-part1.md#class-streamreadable)

An `IncomingMessage` object is created by [`http.Server`](27-http-part1.md#class-httpserver) or
[`http.ClientRequest`](27-http-part1.md#class-httpclientrequest) and passed as the first argument to the [`'request'`](27-http-part1.md#event-request)
and [`'response'`](27-http-part1.md#event-response) event respectively. It may be used to access response
status, headers, and data.

Different from its `socket` value which is a subclass of [`<stream.Duplex>`](51-stream-part2.md#class-streamduplex), the `IncomingMessage` itself extends [`<stream.Readable>`](51-stream-part1.md#class-streamreadable) and is created separately to
parse and emit the incoming HTTP headers and payload, as the underlying socket
may be reused multiple times in case of keep-alive.

### Event: `'aborted'`

Added in: v0.3.8Deprecated in: v17.0.0, v16.12.0

Stability: 0 - Deprecated. Listen for `'close'` event instead.

Emitted when the request has been aborted.

### Event: `'close'`

Added in: v0.4.2

**History**

| Version | Changes |
| --- | --- |
| v16.0.0 | The close event is now emitted when the request has been completed and not when the underlying socket is closed. |

Emitted when the request has been completed.

### `message.aborted`

Added in: v10.1.0Deprecated in: v17.0.0, v16.12.0

Stability: 0 - Deprecated. Check `message.destroyed` from [`<stream.Readable>`](51-stream-part1.md#class-streamreadable).

- Type: [`<boolean>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#boolean_type)

The `message.aborted` property will be `true` if the request has
been aborted.

### `message.complete`

Added in: v0.3.0

- Type: [`<boolean>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#boolean_type)

The `message.complete` property will be `true` if a complete HTTP message has
been received and successfully parsed.

This property is particularly useful as a means of determining if a client or
server fully transmitted a message before a connection was terminated:

```js
const req = http.request({
  host: '127.0.0.1',
  port: 8080,
  method: 'POST',
}, (res) => {
  res.resume();
  res.on('end', () => {
    if (!res.complete)
      console.error(
        'The connection was terminated while the message was still being sent');
  });
});
```

### `message.connection`

Added in: v0.1.90Deprecated in: v16.0.0

Stability: 0 - Deprecated. Use [`message.socket`](#messagesocket).

Alias for [`message.socket`](#messagesocket).

### `message.destroy([error])`

Added in: v0.3.0

**History**

| Version | Changes |
| --- | --- |
| v14.5.0, v12.19.0 | The function returns `this` for consistency with other Readable streams. |

- `error` [`<Error>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Error)
- Returns: [`<this>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Operators/this)

Calls `destroy()` on the socket that received the `IncomingMessage`. If `error`
is provided, an `'error'` event is emitted on the socket and `error` is passed
as an argument to any listeners on the event.

### `message.headers`

Added in: v0.1.5

**History**

| Version | Changes |
| --- | --- |
| v19.5.0, v18.14.0 | The `joinDuplicateHeaders` option in the `http.request()` and `http.createServer()` functions ensures that duplicate headers are not discarded, but rather combined using a comma separator, in accordance with RFC 9110 Section 5.3. |
| v15.1.0 | `message.headers` is now lazily computed using an accessor property on the prototype and is no longer enumerable. |

- Type: [`<Object>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Object)

The request/response headers object.

Key-value pairs of header names and values. Header names are lower-cased.

```js
// Prints something like:
//
// { 'user-agent': 'curl/7.22.0',
//   host: '127.0.0.1:8000',
//   accept: '*/*' }
console.log(request.headers);
```

Duplicates in raw headers are handled in the following ways, depending on the
header name:

- Duplicates of `age`, `authorization`, `content-length`, `content-type`,
  `etag`, `expires`, `from`, `host`, `if-modified-since`, `if-unmodified-since`,
  `last-modified`, `location`, `max-forwards`, `proxy-authorization`, `referer`,
  `retry-after`, `server`, or `user-agent` are discarded.
  To allow duplicate values of the headers listed above to be joined,
  use the option `joinDuplicateHeaders` in [`http.request()`](27-http-part3.md#httprequestoptions-callback)
  and [`http.createServer()`](27-http-part3.md#httpcreateserveroptions-requestlistener). See RFC 9110 Section 5.3 for more
  information.
- `set-cookie` is always an array. Duplicates are added to the array.
- For duplicate `cookie` headers, the values are joined together with `;` .
- For all other headers, the values are joined together with `,` .

### `message.headersDistinct`

Added in: v18.3.0, v16.17.0

- Type: [`<Object>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Object)

Similar to [`message.headers`](#messageheaders), but there is no join logic and the values are
always arrays of strings, even for headers received just once.

```js
// Prints something like:
//
// { 'user-agent': ['curl/7.22.0'],
//   host: ['127.0.0.1:8000'],
//   accept: ['*/*'] }
console.log(request.headersDistinct);
```

### `message.httpVersion`

Added in: v0.1.1

- Type: [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type)

In case of server request, the HTTP version sent by the client. In the case of
client response, the HTTP version of the connected-to server.
Probably either `'1.1'` or `'1.0'`.

Also `message.httpVersionMajor` is the first integer and
`message.httpVersionMinor` is the second.

### `message.method`

Added in: v0.1.1

- Type: [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type)

**Only valid for request obtained from [`http.Server`](27-http-part1.md#class-httpserver).**

The request method as a string. Read only. Examples: `'GET'`, `'DELETE'`.

### `message.rawHeaders`

Added in: v0.11.6

- Type: [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type)[]

The raw request/response headers list exactly as they were received.

The keys and values are in the same list. It is *not* a
list of tuples. So, the even-numbered offsets are key values, and the
odd-numbered offsets are the associated values.

Header names are not lowercased, and duplicates are not merged.

```js
// Prints something like:
//
// [ 'user-agent',
//   'this is invalid because there can be only one',
//   'User-Agent',
//   'curl/7.22.0',
//   'Host',
//   '127.0.0.1:8000',
//   'ACCEPT',
//   '*/*' ]
console.log(request.rawHeaders);
```

### `message.rawTrailers`

Added in: v0.11.6

- Type: [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type)[]

The raw request/response trailer keys and values exactly as they were
received. Only populated at the `'end'` event.

### `message.setTimeout(msecs[, callback])`

Added in: v0.5.9

- `msecs` [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type)
- `callback` [`<Function>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Function)
- Returns: [`<http.IncomingMessage>`](27-http-part2.md#class-httpincomingmessage)

Calls `message.socket.setTimeout(msecs, callback)`.

### `message.signal`

Added in: v26.1.0

**History**

| Version | Changes |
| --- | --- |
| v26.7.0 | The signal is no longer aborted after the message completes normally. |

- Type: [`<AbortSignal>`](26-globals.md#class-abortsignal)

An [`<AbortSignal>`](26-globals.md#class-abortsignal) that is aborted when the message is destroyed before
completion or when its underlying socket closes before request handling or
response reading completes.
The signal is created lazily on first access — no [`<AbortController>`](26-globals.md#class-abortcontroller) is allocated
for requests that never use this property.

This is useful for cancelling downstream asynchronous work such as database
queries or `fetch` calls when a client disconnects mid-request.

*ES module version:*

```js
import http from 'node:http';

http.createServer(async (req, res) => {
  try {
    const data = await fetch('https://example.com/api', { signal: req.signal });
    res.end(JSON.stringify(await data.json()));
  } catch (err) {
    if (err.name === 'AbortError') return;
    res.statusCode = 500;
    res.end('Internal Server Error');
  }
}).listen(3000);
```

*CommonJS version:*

```js
const http = require('node:http');

http.createServer(async (req, res) => {
  try {
    const data = await fetch('https://example.com/api', { signal: req.signal });
    res.end(JSON.stringify(await data.json()));
  } catch (err) {
    if (err.name === 'AbortError') return;
    res.statusCode = 500;
    res.end('Internal Server Error');
  }
}).listen(3000);
```

### `message.socket`

Added in: v0.3.0

- Type: [`<stream.Duplex>`](51-stream-part2.md#class-streamduplex)

The [`net.Socket`](38-net.md#class-netsocket) object associated with the connection.

With HTTPS support, use [`request.socket.getPeerCertificate()`](55-tls.md#tlssocketgetpeercertificatedetailed) to obtain the
client's authentication details.

This property is guaranteed to be an instance of the [`<net.Socket>`](38-net.md#class-netsocket) class,
a subclass of [`<stream.Duplex>`](51-stream-part2.md#class-streamduplex), unless the user specified a socket
type other than [`<net.Socket>`](38-net.md#class-netsocket) or internally nulled.

### `message.statusCode`

Added in: v0.1.1

- Type: [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type)

**Only valid for response obtained from [`http.ClientRequest`](27-http-part1.md#class-httpclientrequest).**

The 3-digit HTTP response status code. E.G. `404`.

### `message.statusMessage`

Added in: v0.11.10

- Type: [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type)

**Only valid for response obtained from [`http.ClientRequest`](27-http-part1.md#class-httpclientrequest).**

The HTTP response status message (reason phrase). E.G. `OK` or `Internal Server Error`.

### `message.trailers`

Added in: v0.3.0

- Type: [`<Object>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Object)

The request/response trailers object. Only populated at the `'end'` event.

### `message.trailersDistinct`

Added in: v18.3.0, v16.17.0

- Type: [`<Object>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Object)

Similar to [`message.trailers`](#messagetrailers), but there is no join logic and the values are
always arrays of strings, even for headers received just once.
Only populated at the `'end'` event.

### `message.url`

Added in: v0.1.90

- Type: [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type)

**Only valid for request obtained from [`http.Server`](27-http-part1.md#class-httpserver).**

Request URL string. This contains only the URL that is present in the actual
HTTP request. Take the following request:

```http
GET /status?name=ryan HTTP/1.1
Accept: text/plain
```

To parse the URL into its parts:

```js
new URL(`http://${process.env.HOST ?? 'localhost'}${request.url}`);
```

When `request.url` is `'/status?name=ryan'` and `process.env.HOST` is undefined:

```console
$ node
> new URL(`http://${process.env.HOST ?? 'localhost'}${request.url}`);
URL {
  href: 'http://localhost/status?name=ryan',
  origin: 'http://localhost',
  protocol: 'http:',
  username: '',
  password: '',
  host: 'localhost',
  hostname: 'localhost',
  port: '',
  pathname: '/status',
  search: '?name=ryan',
  searchParams: URLSearchParams { 'name' => 'ryan' },
  hash: ''
}
```

Ensure that you set `process.env.HOST` to the server's host name, or consider
replacing this part entirely. If using `req.headers.host`, ensure proper
validation is used, as clients may specify a custom `Host` header.

## Class: `http.OutgoingMessage`

Added in: v0.1.17

- Extends: [`<Stream>`](51-stream-part1.md#stream)

This class serves as the parent class of [`http.ClientRequest`](27-http-part1.md#class-httpclientrequest)
and [`http.ServerResponse`](#class-httpserverresponse). It is an abstract outgoing message from
the perspective of the participants of an HTTP transaction.

### Event: `'drain'`

Added in: v0.3.6

Emitted when the buffer of the message is free again.

### Event: `'finish'`

Added in: v0.1.17

Emitted when the transmission is finished successfully.

### Event: `'prefinish'`

Added in: v0.11.6

Emitted after `outgoingMessage.end()` is called.
When the event is emitted, all data has been processed but not necessarily
completely flushed.

### `outgoingMessage.addTrailers(headers)`

Added in: v0.3.0

- `headers` [`<Object>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Object)

Adds HTTP trailers (headers but at the end of the message) to the message.

Trailers will **only** be emitted if the message is chunked encoded. If not,
the trailers will be silently discarded.

HTTP requires the `Trailer` header to be sent to emit trailers,
with a list of header field names in its value, e.g.

```js
message.writeHead(200, { 'Content-Type': 'text/plain',
                         'Trailer': 'Content-MD5' });
message.write(fileData);
message.addTrailers({ 'Content-MD5': '7895bf4b8828b55ceaf47747b4bca667' });
message.end();
```

Attempting to set a header field name or value that contains invalid characters
will result in a `TypeError` being thrown.

### `outgoingMessage.appendHeader(name, value)`

Added in: v18.3.0, v16.17.0

- `name` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) Header name
- `value` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) | [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type)[] Header value
- Returns: [`<this>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Operators/this)

Append a single header value to the header object.

If the value is an array, this is equivalent to calling this method multiple
times.

If there were no previous values for the header, this is equivalent to calling
[`outgoingMessage.setHeader(name, value)`](#outgoingmessagesetheadername-value).

Depending of the value of `options.uniqueHeaders` when the client request or the
server were created, this will end up in the header being sent multiple times or
a single time with values joined using `;` .

### `outgoingMessage.connection`

Added in: v0.3.0Deprecated in: v15.12.0, v14.17.1

Stability: 0 - Deprecated: Use [`outgoingMessage.socket`](#outgoingmessagesocket) instead.

Alias of [`outgoingMessage.socket`](#outgoingmessagesocket).

### `outgoingMessage.cork()`

Added in: v13.2.0, v12.16.0

See [`writable.cork()`](51-stream-part1.md#writablecork).

### `outgoingMessage.destroy([error])`

Added in: v0.3.0

- `error` [`<Error>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Error) Optional, an error to emit with `error` event
- Returns: [`<this>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Operators/this)

Destroys the message. Once a socket is associated with the message
and is connected, that socket will be destroyed as well.

### `outgoingMessage.end(chunk[, encoding][, callback])`

Added in: v0.1.90

**History**

| Version | Changes |
| --- | --- |
| v15.0.0 | The `chunk` parameter can now be a `Uint8Array`. |
| v0.11.6 | add `callback` argument. |

- `chunk` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) | [`<Buffer>`](07-buffer-part1.md#class-buffer) | [`<Uint8Array>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Uint8Array)
- `encoding` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) Optional, **Default**: `utf8`
- `callback` [`<Function>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Function) Optional
- Returns: [`<this>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Operators/this)

Finishes the outgoing message. If any parts of the body are unsent, it will
flush them to the underlying system. If the message is chunked, it will
send the terminating chunk `0\r\n\r\n`, and send the trailers (if any).

If `chunk` is specified, it is equivalent to calling
`outgoingMessage.write(chunk, encoding)`, followed by
`outgoingMessage.end(callback)`.

If `callback` is provided, it will be called when the message is finished
(equivalent to a listener of the `'finish'` event).

### `outgoingMessage.flushHeaders()`

Added in: v1.6.0

Flushes the message headers.

For efficiency reason, Node.js normally buffers the message headers
until `outgoingMessage.end()` is called or the first chunk of message data
is written. It then tries to pack the headers and data into a single TCP
packet.

It is usually desired (it saves a TCP round-trip), but not when the first
data is not sent until possibly much later. `outgoingMessage.flushHeaders()`
bypasses the optimization and kickstarts the message.

### `outgoingMessage.getHeader(name)`

Added in: v0.4.0

- `name` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) Name of header
- Returns: [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) | [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) | [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type)[] | [`<undefined>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#undefined_type)

Gets the value of the HTTP header with the given name. If that header is not
set, the returned value will be `undefined`.

### `outgoingMessage.getHeaderNames()`

Added in: v7.7.0

- Returns: [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type)[]

Returns an array containing the unique names of the current outgoing headers.
All names are lowercase.

### `outgoingMessage.getHeaders()`

Added in: v7.7.0

- Returns: [`<Object>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Object)

Returns a shallow copy of the current outgoing headers. Since a shallow
copy is used, array values may be mutated without additional calls to
various header-related HTTP module methods. The keys of the returned
object are the header names and the values are the respective header
values. All header names are lowercase.

The object returned by the `outgoingMessage.getHeaders()` method does
not prototypically inherit from the JavaScript `Object`. This means that
typical `Object` methods such as `obj.toString()`, `obj.hasOwnProperty()`,
and others are not defined and will not work.

```js
outgoingMessage.setHeader('Foo', 'bar');
outgoingMessage.setHeader('Set-Cookie', ['foo=bar', 'bar=baz']);

const headers = outgoingMessage.getHeaders();
// headers === { foo: 'bar', 'set-cookie': ['foo=bar', 'bar=baz'] }
```

### `outgoingMessage.hasHeader(name)`

Added in: v7.7.0

- `name` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type)
- Returns: [`<boolean>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#boolean_type)

Returns `true` if the header identified by `name` is currently set in the
outgoing headers. The header name is case-insensitive.

```js
const hasContentType = outgoingMessage.hasHeader('content-type');
```

### `outgoingMessage.headersSent`

Added in: v0.9.3

- Type: [`<boolean>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#boolean_type)

Read-only. `true` if the headers were sent, otherwise `false`.

### `outgoingMessage.pipe()`

Added in: v9.0.0

Overrides the `stream.pipe()` method inherited from the legacy `Stream` class
which is the parent class of `http.OutgoingMessage`.

Calling this method will throw an `Error` because `outgoingMessage` is a
write-only stream.

### `outgoingMessage.removeHeader(name)`

Added in: v0.4.0

- `name` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) Header name

Removes a header that is queued for implicit sending.

```js
outgoingMessage.removeHeader('Content-Encoding');
```

### `outgoingMessage.setHeader(name, value)`

Added in: v0.4.0

- `name` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) Header name
- `value` [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) | [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) | [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type)[] Header value
- Returns: [`<this>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Operators/this)

Sets a single header value. If the header already exists in the to-be-sent
headers, its value will be replaced. Use an array of strings to send multiple
headers with the same name.

### `outgoingMessage.setHeaders(headers)`

Added in: v19.6.0, v18.15.0

- `headers` [`<Headers>`](https://developer.mozilla.org/docs/Web/API/Headers) | [`<Map>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Map)
- Returns: [`<this>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Operators/this)

Sets multiple header values for implicit headers.
`headers` must be an instance of [`Headers`](26-globals.md#class-headers) or `Map`,
if a header already exists in the to-be-sent headers,
its value will be replaced.

```js
const headers = new Headers({ foo: 'bar' });
outgoingMessage.setHeaders(headers);
```

or

```js
const headers = new Map([['foo', 'bar']]);
outgoingMessage.setHeaders(headers);
```

When headers have been set with [`outgoingMessage.setHeaders()`](#outgoingmessagesetheadersheaders),
they will be merged with any headers passed to [`response.writeHead()`](#responsewriteheadstatuscode-statusmessage-headers),
with the headers passed to [`response.writeHead()`](#responsewriteheadstatuscode-statusmessage-headers) given precedence.

```js
// Returns content-type = text/plain
const server = http.createServer((req, res) => {
  const headers = new Headers({ 'Content-Type': 'text/html' });
  res.setHeaders(headers);
  res.writeHead(200, { 'Content-Type': 'text/plain' });
  res.end('ok');
});
```

### `outgoingMessage.setTimeout(msecs[, callback])`

Added in: v0.9.12

- `msecs` [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type)
- `callback` [`<Function>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Function) Optional function to be called when a timeout
  occurs. Same as binding to the `timeout` event.
- Returns: [`<this>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Operators/this)

Once a socket is associated with the message and is connected,
[`socket.setTimeout()`](38-net.md#socketsettimeouttimeout-callback) will be called with `msecs` as the first parameter.

### `outgoingMessage.socket`

Added in: v0.3.0

- Type: [`<stream.Duplex>`](51-stream-part2.md#class-streamduplex)

Reference to the underlying socket. Usually, users will not want to access
this property.

After calling `outgoingMessage.end()`, this property will be nulled.

### `outgoingMessage.uncork()`

Added in: v13.2.0, v12.16.0

See [`writable.uncork()`](51-stream-part1.md#writableuncork)

### `outgoingMessage.writableCorked`

Added in: v13.2.0, v12.16.0

- Type: [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type)

The number of times `outgoingMessage.cork()` has been called.

### `outgoingMessage.writableEnded`

Added in: v12.9.0

- Type: [`<boolean>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#boolean_type)

Is `true` if `outgoingMessage.end()` has been called. This property does
not indicate whether the data has been flushed. For that purpose, use
`message.writableFinished` instead.

### `outgoingMessage.writableFinished`

Added in: v12.7.0

- Type: [`<boolean>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#boolean_type)

Is `true` if all data has been flushed to the underlying system.

### `outgoingMessage.writableHighWaterMark`

Added in: v12.9.0

- Type: [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type)

The `highWaterMark` of the underlying socket if assigned. Otherwise, the default
buffer level when [`writable.write()`](51-stream-part1.md#writablewritechunk-encoding-callback) starts returning false (`16384`).

### `outgoingMessage.writableLength`

Added in: v12.9.0

- Type: [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type)

The number of buffered bytes.

### `outgoingMessage.writableObjectMode`

Added in: v12.9.0

- Type: [`<boolean>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#boolean_type)

Always `false`.

### `outgoingMessage.write(chunk[, encoding][, callback])`

Added in: v0.1.29

**History**

| Version | Changes |
| --- | --- |
| v15.0.0 | The `chunk` parameter can now be a `Uint8Array`. |
| v0.11.6 | The `callback` argument was added. |

- `chunk` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) | [`<Buffer>`](07-buffer-part1.md#class-buffer) | [`<Uint8Array>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Uint8Array)
- `encoding` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) **Default**: `utf8`
- `callback` [`<Function>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Function)
- Returns: [`<boolean>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#boolean_type)

Sends a chunk of the body. This method can be called multiple times.

The `encoding` argument is only relevant when `chunk` is a string. Defaults to
`'utf8'`.

The `callback` argument is optional and will be called when this chunk of data
is flushed.

Returns `true` if the entire data was flushed successfully to the kernel
buffer. Returns `false` if all or part of the data was queued in the user
memory. The `'drain'` event will be emitted when the buffer is free again.

[Contents](../README.md) | [Previous: HTTP, part 1 of 3](27-http-part1.md) | [Next: HTTP, part 3 of 3](27-http-part3.md)
