[Contents](../README.md) | [Previous: Globals](26-globals.md) | [Next: HTTP/2](28-http2.md)

Source: [https://nodejs.org/docs/latest/api/http.html](https://nodejs.org/docs/latest/api/http.html)

# HTTP

[Stability: 2](01-documentation.md#stability-index) - Stable

This module, containing both a client and server, can be imported via
`require('node:http')` (CommonJS) or `import * as http from 'node:http'` (ES module).

The HTTP interfaces in Node.js are designed to support many features
of the protocol which have been traditionally difficult to use.
In particular, large, possibly chunk-encoded, messages. The interface is
careful to never buffer entire requests or responses, so the
user is able to stream data.

HTTP message headers are represented by an object like this:

```json
{ "content-length": "123",
  "content-type": "text/plain",
  "connection": "keep-alive",
  "host": "example.com",
  "accept": "*/*" }
```

Keys are lowercased. Values are not modified.

In order to support the full spectrum of possible HTTP applications, the Node.js
HTTP API is very low-level. It deals with stream handling and message
parsing only. It parses a message into headers and body but it does not
parse the actual headers or the body.

See [`message.headers`](#messageheaders) for details on how duplicate headers are handled.

The raw headers as they were received are retained in the `rawHeaders`
property, which is an array of `[key, value, key2, value2, ...]`. For
example, the previous message header object might have a `rawHeaders`
list like the following:

```json
[ "ConTent-Length", "123456",
  "content-LENGTH", "123",
  "content-type", "text/plain",
  "CONNECTION", "keep-alive",
  "Host", "example.com",
  "accepT", "*/*" ]
```

## Class: `http.Agent`

Added in: v0.3.4

An `Agent` is responsible for managing connection persistence
and reuse for HTTP clients. It maintains a queue of pending requests
for a given host and port, reusing a single socket connection for each
until the queue is empty, at which time the socket is either destroyed
or put into a pool where it is kept to be used again for requests to the
same host and port. Whether it is destroyed or pooled depends on the
`keepAlive` [option](#new-agentoptions).

Pooled connections have TCP Keep-Alive enabled for them, but servers may
still close idle connections, in which case they will be removed from the
pool and a new connection will be made when a new HTTP request is made for
that host and port. Servers may also refuse to allow multiple requests
over the same connection, in which case the connection will have to be
remade for every request and cannot be pooled. The `Agent` will still make
the requests to that server, but each one will occur over a new connection.

### Response ordering with connection reuse

On a reused HTTP/1.1 keep-alive connection, responses are associated with
requests by their order on that connection. HTTP/1.1 keep-alive does not provide
per-request response attribution beyond that ordering. Applications that require
per-request connection isolation can use a separate `Agent`, disable keep-alive,
or pass `agent: false`.

When a connection is closed by the client or the server, it is removed
from the pool. Any unused sockets in the pool will be unrefed so as not
to keep the Node.js process running when there are no outstanding requests.
(see [`socket.unref()`](38-net.md#socketunref)).

It is good practice, to [`destroy()`](#agentdestroy) an `Agent` instance when it is no
longer in use, because unused sockets consume OS resources.

Sockets are removed from an agent when the socket emits either
a `'close'` event or an `'agentRemove'` event. When intending to keep one
HTTP request open for a long time without keeping it in the agent, something
like the following may be done:

```js
http.get(options, (res) => {
  // Do stuff
}).on('socket', (socket) => {
  socket.emit('agentRemove');
});
```

An agent may also be used for an individual request. By providing
`{agent: false}` as an option to the `http.get()` or `http.request()`
functions, a one-time use `Agent` with default options will be used
for the client connection.

`agent:false`:

```js
http.get({
  hostname: 'localhost',
  port: 80,
  path: '/',
  agent: false,  // Create a new agent just for this one request
}, (res) => {
  // Do stuff with response
});
```

Use `agent: false` to avoid connection reuse for a request.

### `new Agent([options])`

Added in: v0.3.4

**History**

| Version | Changes |
| --- | --- |
| v24.7.0, v22.20.0 | Add support for `agentKeepAliveTimeoutBuffer`. |
| v24.5.0, v22.21.0 | Add support for `proxyEnv`. |
| v24.5.0, v22.21.0 | Add support for `defaultPort` and `protocol`. |
| v15.6.0, v14.17.0 | Change the default scheduling from 'fifo' to 'lifo'. |
| v14.5.0, v12.19.0 | Add `maxTotalSockets` option to agent constructor. |
| v14.5.0, v12.20.0 | Add `scheduling` option to specify the free socket scheduling strategy. |

- `options` [`<Object>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Object) Set of configurable options to set on the agent.
  Can have the following fields:
  - `keepAlive` [`<boolean>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#boolean_type) Keep sockets around even when there are no
    outstanding requests, so they can be used for future requests without
    having to reestablish a TCP connection. Not to be confused with the `keep-alive` value of the `Connection` header. The `Connection: keep-alive`
    header is always sent when using an agent except when the `Connection`
    header is explicitly specified or when the `keepAlive` and `maxSockets`
    options are respectively set to `false` and `Infinity`, in which case
    `Connection: close` will be used. **Default:** `false`.
  - `keepAliveMsecs` [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) When using the `keepAlive` option, specifies
    the [initial delay](38-net.md#socketsetkeepaliveenable-initialdelay-interval-count)
    for TCP Keep-Alive packets. Ignored when the
    `keepAlive` option is `false` or `undefined`. **Default:** `1000`.
  - `agentKeepAliveTimeoutBuffer` [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) Milliseconds to subtract from
    the server-provided `keep-alive: timeout=...` hint when determining socket
    expiration time. This buffer helps ensure the agent closes the socket
    slightly before the server does, reducing the chance of sending a request
    on a socket that’s about to be closed by the server.
    **Default:** `1000`.
  - `maxSockets` [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) Maximum number of sockets to allow per host.
    If the same host opens multiple concurrent connections, each request
    will use new socket until the `maxSockets` value is reached.
    If the host attempts to open more connections than `maxSockets`,
    the additional requests will enter into a pending request queue, and
    will enter active connection state when an existing connection terminates.
    This makes sure there are at most `maxSockets` active connections at
    any point in time, from a given host.
    **Default:** `Infinity`.
  - `maxTotalSockets` [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) Maximum number of sockets allowed for
    all hosts in total. Each request will use a new socket
    until the maximum is reached. **Default:** `Infinity`.
  - `maxFreeSockets` [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) Maximum number of sockets per host to leave open
    in a free state. Only relevant if `keepAlive` is set to `true`.
    **Default:** `256`.
  - `scheduling` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) Scheduling strategy to apply when picking
    the next free socket to use. It can be `'fifo'` or `'lifo'`.
    The main difference between the two scheduling strategies is that `'lifo'`
    selects the most recently used socket, while `'fifo'` selects
    the least recently used socket.
    In case of a low rate of request per second, the `'lifo'` scheduling
    will lower the risk of picking a socket that might have been closed
    by the server due to inactivity.
    In case of a high rate of request per second,
    the `'fifo'` scheduling will maximize the number of open sockets,
    while the `'lifo'` scheduling will keep it as low as possible.
    **Default:** `'lifo'`.
  - `timeout` [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) Socket timeout in milliseconds.
    This will set the timeout when the socket is created.
  - `proxyEnv` [`<Object>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Object) | [`<undefined>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#undefined_type) Environment variables for proxy configuration.
    See [Built-in Proxy Support](#built-in-proxy-support) for details. **Default:** `undefined`
    - `HTTP_PROXY` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) | [`<undefined>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#undefined_type) URL for the proxy server that HTTP requests should use.
      If undefined, no proxy is used for HTTP requests.
    - `HTTPS_PROXY` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) | [`<undefined>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#undefined_type) URL for the proxy server that HTTPS requests should use.
      If undefined, no proxy is used for HTTPS requests.
    - `NO_PROXY` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) | [`<undefined>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#undefined_type) Patterns specifying the endpoints
      that should not be routed through a proxy.
    - `http_proxy` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) | [`<undefined>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#undefined_type) Same as `HTTP_PROXY`. If both are set, `http_proxy` takes precedence.
    - `https_proxy` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) | [`<undefined>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#undefined_type) Same as `HTTPS_PROXY`. If both are set, `https_proxy` takes precedence.
    - `no_proxy` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) | [`<undefined>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#undefined_type) Same as `NO_PROXY`. If both are set, `no_proxy` takes precedence.
  - `defaultPort` [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) Default port to use when the port is not specified
    in requests. **Default:** `80`.
  - `protocol` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) The protocol to use for the agent. **Default:** `'http:'`.

`options` in [`socket.connect()`](38-net.md#socketconnectoptions-connectlistener) are also supported.

To configure any of them, a custom [`http.Agent`](#class-httpagent) instance must be created.

*ES module version:*

```js
import { Agent, request } from 'node:http';
const keepAliveAgent = new Agent({ keepAlive: true });
options.agent = keepAliveAgent;
request(options, onResponseCallback);
```

*CommonJS version:*

```js
const http = require('node:http');
const keepAliveAgent = new http.Agent({ keepAlive: true });
options.agent = keepAliveAgent;
http.request(options, onResponseCallback);
```

### `agent.createConnection(options[, callback])`

Added in: v0.11.4

- `options` [`<Object>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Object) Options containing connection details. Check [`net.createConnection()`](38-net.md#netcreateconnectionoptions-connectlistener) for the format of the options. For custom agents,
  this object is passed to the custom `createConnection` function.
- `callback` [`<Function>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Function) (Optional, primarily for custom agents) A function to be
  called by a custom `createConnection` implementation when the socket is
  created, especially for asynchronous operations.
  - `err` [`<Error>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Error) | [`<null>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#null_type) An error object if socket creation failed.
  - `socket` [`<stream.Duplex>`](51-stream.md#class-streamduplex) The created socket.
- Returns: [`<stream.Duplex>`](51-stream.md#class-streamduplex) The created socket. This is returned by the default
  implementation or by a custom synchronous `createConnection` implementation.
  If a custom `createConnection` uses the `callback` for asynchronous
  operation, this return value might not be the primary way to obtain the socket.

Produces a socket/stream to be used for HTTP requests.

By default, this function behaves identically to [`net.createConnection()`](38-net.md#netcreateconnectionoptions-connectlistener),
synchronously returning the created socket. The optional `callback` parameter in the
signature is **not** used by this default implementation.

However, custom agents may override this method to provide greater flexibility,
for example, to create sockets asynchronously. When overriding `createConnection`:

1. **Synchronous socket creation**: The overriding method can return the
   socket/stream directly.
2. **Asynchronous socket creation**: The overriding method can accept the `callback`
   and pass the created socket/stream to it (e.g., `callback(null, newSocket)`).
   If an error occurs during socket creation, it should be passed as the first
   argument to the `callback` (e.g., `callback(err)`).

The agent will call the provided `createConnection` function with `options` and
this internal `callback`. The `callback` provided by the agent has a signature
of `(err, stream)`.

### `agent.keepSocketAlive(socket)`

Added in: v8.1.0

- `socket` [`<stream.Duplex>`](51-stream.md#class-streamduplex)
- Returns: [`<boolean>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#boolean_type)

Called when `socket` is detached from a request and could be persisted by the
`Agent`. The `Agent` calls this method only when `keepAlive` is enabled.

By default, TCP keep-alive is enabled and the socket is unrefed. A server
keep-alive timeout hint may adjust the socket timeout or cause this method to
return `false`; otherwise, it returns `true`.

This method can be overridden by a particular `Agent` subclass. If this
method returns a falsy value, the socket will be destroyed instead of persisting
it for use with the next request.

The `socket` argument can be an instance of [`<net.Socket>`](38-net.md#class-netsocket), a subclass of
[`<stream.Duplex>`](51-stream.md#class-streamduplex).

### `agent.reuseSocket(socket, request)`

Added in: v8.1.0

- `socket` [`<stream.Duplex>`](51-stream.md#class-streamduplex)
- `request` [`<http.ClientRequest>`](27-http.md#class-httpclientrequest)

Called when `socket` is attached to `request` after being persisted because of
the keep-alive options. Default behavior is to:

```js
socket.ref();
```

This method can be overridden by a particular `Agent` subclass.

The `socket` argument can be an instance of [`<net.Socket>`](38-net.md#class-netsocket), a subclass of
[`<stream.Duplex>`](51-stream.md#class-streamduplex).

### `agent.destroy()`

Added in: v0.11.4

Destroy any sockets that are currently in use by the agent.

It is usually not necessary to do this. However, if using an
agent with `keepAlive` enabled, then it is best to explicitly shut down
the agent when it is no longer needed. Otherwise,
sockets might stay open for quite a long time before the server
terminates them.

### `agent.freeSockets`

Added in: v0.11.4

**History**

| Version | Changes |
| --- | --- |
| v16.0.0 | The property now has a `null` prototype. |

- Type: [`<Object>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Object)

An object which contains arrays of sockets currently awaiting use by
the agent when `keepAlive` is enabled. Do not modify.

Sockets in the `freeSockets` list will be automatically destroyed and
removed from the array on `'timeout'`.

### `agent.getName([options])`

Added in: v0.11.4

**History**

| Version | Changes |
| --- | --- |
| v17.7.0, v16.15.0 | The `options` parameter is now optional. |

- `options` [`<Object>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Object) A set of options providing information for name generation
  - `host` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) A domain name or IP address of the server to issue the
    request to
  - `port` [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) Port of remote server
  - `localAddress` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) Local interface to bind for network connections
    when issuing the request
  - `family` [`<integer>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) Must be 4 or 6 if this doesn't equal `undefined`.
- Returns: [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type)

Get a unique name for a set of request options, to determine whether a
connection can be reused. For an HTTP agent, this returns
`host:port:localAddress` or `host:port:localAddress:family`. For an HTTPS agent,
the name includes the CA, cert, ciphers, and other HTTPS/TLS-specific options
that determine socket reusability.

### `agent.maxFreeSockets`

Added in: v0.11.7

- Type: [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type)

By default set to 256. For agents with `keepAlive` enabled, this
sets the maximum number of sockets that will be left open in the free
state.

### `agent.maxSockets`

Added in: v0.3.6

- Type: [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type)

By default set to `Infinity`. Determines how many concurrent sockets the agent
can have open per origin. Origin is the returned value of [`agent.getName()`](#agentgetnameoptions).

### `agent.maxTotalSockets`

Added in: v14.5.0, v12.19.0

- Type: [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type)

By default set to `Infinity`. Determines how many concurrent sockets the agent
can have open. Unlike `maxSockets`, this parameter applies across all origins.

### `agent.requests`

Added in: v0.5.9

**History**

| Version | Changes |
| --- | --- |
| v16.0.0 | The property now has a `null` prototype. |

- Type: [`<Object>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Object)

An object which contains queues of requests that have not yet been assigned to
sockets. Do not modify.

### `agent.sockets`

Added in: v0.3.6

**History**

| Version | Changes |
| --- | --- |
| v16.0.0 | The property now has a `null` prototype. |

- Type: [`<Object>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Object)

An object which contains arrays of sockets currently in use by the
agent. Do not modify.

## Class: `http.ClientRequest`

Added in: v0.1.17

- Extends: [`<http.OutgoingMessage>`](27-http.md#class-httpoutgoingmessage)

This object is created internally and returned from [`http.request()`](#httprequestoptions-callback). It
represents an *in-progress* request whose header has already been queued. The
header is still mutable using the [`setHeader(name, value)`](#requestsetheadername-value),
[`getHeader(name)`](#requestgetheadername), [`removeHeader(name)`](#requestremoveheadername) API. The actual header will
be sent along with the first data chunk or when calling [`request.end()`](#requestenddata-encoding-callback).

To get the response, add a listener for [`'response'`](#event-response) to the request object.
[`'response'`](#event-response) will be emitted from the request object when the response
headers have been received. The [`'response'`](#event-response) event is executed with one
argument which is an instance of [`http.IncomingMessage`](#class-httpincomingmessage).

During the [`'response'`](#event-response) event, one can add listeners to the
response object; particularly to listen for the `'data'` event.

If no [`'response'`](#event-response) handler is added, then the response will be
entirely discarded. However, if a [`'response'`](#event-response) event handler is added,
then the data from the response object **must** be consumed, either by
calling `response.read()` whenever there is a `'readable'` event, or
by adding a `'data'` handler, or by calling the `.resume()` method.
Until the data is consumed, the `'end'` event will not fire. Also, until
the data is read it will consume memory that can eventually lead to a
'process out of memory' error.

For backward compatibility, `res` will only emit `'error'` if there is an
`'error'` listener registered.

Set `Content-Length` header to limit the response body size.
If [`response.strictContentLength`](#responsestrictcontentlength) is set to `true`, mismatching the
`Content-Length` header value will result in an `Error` being thrown,
identified by `code:` [`'ERR_HTTP_CONTENT_LENGTH_MISMATCH'`](22-errors.md#err_http_content_length_mismatch).

`Content-Length` value should be in bytes, not characters. Use
[`Buffer.byteLength()`](07-buffer.md#static-method-bufferbytelengthstring-encoding) to determine the length of the body in bytes.

### Event: `'abort'`

Added in: v1.4.1Deprecated in: v17.0.0, v16.12.0

Stability: 0 - Deprecated. Listen for the `'close'` event instead.

Emitted when the request has been aborted by the client. This event is only
emitted on the first call to `abort()`.

### Event: `'close'`

Added in: v0.5.4

Indicates that the request is completed, or its underlying connection was
terminated prematurely (before the response completion).

### Event: `'connect'`

Added in: v0.7.0

- `response` [`<http.IncomingMessage>`](27-http.md#class-httpincomingmessage)
- `socket` [`<stream.Duplex>`](51-stream.md#class-streamduplex)
- `head` [`<Buffer>`](07-buffer.md#class-buffer)

Emitted each time a server responds to a request with a `CONNECT` method. If
this event is not being listened for, clients receiving a `CONNECT` method will
have their connections closed.

This event is guaranteed to be passed an instance of the [`<net.Socket>`](38-net.md#class-netsocket) class,
a subclass of [`<stream.Duplex>`](51-stream.md#class-streamduplex), unless the user specifies a socket
type other than [`<net.Socket>`](38-net.md#class-netsocket).

A client and server pair demonstrating how to listen for the `'connect'` event:

*ES module version:*

```js
import { createServer, request } from 'node:http';
import { connect } from 'node:net';
import { URL } from 'node:url';

// Create an HTTP tunneling proxy
const proxy = createServer((req, res) => {
  res.writeHead(200, { 'Content-Type': 'text/plain' });
  res.end('okay');
});
proxy.on('connect', (req, clientSocket, head) => {
  // Connect to an origin server
  const { port, hostname } = new URL(`http://${req.url}`);
  const serverSocket = connect(port || 80, hostname, () => {
    clientSocket.write('HTTP/1.1 200 Connection Established\r\n' +
                    'Proxy-agent: Node.js-Proxy\r\n' +
                    '\r\n');
    serverSocket.write(head);
    serverSocket.pipe(clientSocket);
    clientSocket.pipe(serverSocket);
  });
});

// Now that proxy is running
proxy.listen(1337, '127.0.0.1', () => {

  // Make a request to a tunneling proxy
  const options = {
    port: 1337,
    host: '127.0.0.1',
    method: 'CONNECT',
    path: 'www.google.com:80',
  };

  const req = request(options);
  req.end();

  req.on('connect', (res, socket, head) => {
    console.log('got connected!');

    // Make a request over an HTTP tunnel
    socket.write('GET / HTTP/1.1\r\n' +
                 'Host: www.google.com:80\r\n' +
                 'Connection: close\r\n' +
                 '\r\n');
    socket.on('data', (chunk) => {
      console.log(chunk.toString());
    });
    socket.on('end', () => {
      proxy.close();
    });
  });
});
```

*CommonJS version:*

```js
const http = require('node:http');
const net = require('node:net');
const { URL } = require('node:url');

// Create an HTTP tunneling proxy
const proxy = http.createServer((req, res) => {
  res.writeHead(200, { 'Content-Type': 'text/plain' });
  res.end('okay');
});
proxy.on('connect', (req, clientSocket, head) => {
  // Connect to an origin server
  const { port, hostname } = new URL(`http://${req.url}`);
  const serverSocket = net.connect(port || 80, hostname, () => {
    clientSocket.write('HTTP/1.1 200 Connection Established\r\n' +
                    'Proxy-agent: Node.js-Proxy\r\n' +
                    '\r\n');
    serverSocket.write(head);
    serverSocket.pipe(clientSocket);
    clientSocket.pipe(serverSocket);
  });
});

// Now that proxy is running
proxy.listen(1337, '127.0.0.1', () => {

  // Make a request to a tunneling proxy
  const options = {
    port: 1337,
    host: '127.0.0.1',
    method: 'CONNECT',
    path: 'www.google.com:80',
  };

  const req = http.request(options);
  req.end();

  req.on('connect', (res, socket, head) => {
    console.log('got connected!');

    // Make a request over an HTTP tunnel
    socket.write('GET / HTTP/1.1\r\n' +
                 'Host: www.google.com:80\r\n' +
                 'Connection: close\r\n' +
                 '\r\n');
    socket.on('data', (chunk) => {
      console.log(chunk.toString());
    });
    socket.on('end', () => {
      proxy.close();
    });
  });
});
```

### Event: `'continue'`

Added in: v0.3.2

Emitted when the server sends a '100 Continue' HTTP response, usually because
the request contained 'Expect: 100-continue'. This is an instruction that
the client should send the request body.

### Event: `'finish'`

Added in: v0.3.6

Emitted when the request has been sent. More specifically, this event is emitted
when the last segment of the request headers and body have been handed off to
the operating system for transmission over the network. It does not imply that
the server has received anything yet.

### Event: `'information'`

Added in: v10.0.0

- `info` [`<Object>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Object)
  - `httpVersion` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type)
  - `httpVersionMajor` [`<integer>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type)
  - `httpVersionMinor` [`<integer>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type)
  - `statusCode` [`<integer>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type)
  - `statusMessage` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type)
  - `headers` [`<Object>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Object)
  - `rawHeaders` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type)[]

Emitted when the server sends a 1xx intermediate response (excluding 101
Upgrade). The listeners of this event will receive an object containing the
HTTP version, status code, status message, key-value headers object,
and array with the raw header names followed by their respective values.

*ES module version:*

```js
import { request } from 'node:http';

const options = {
  host: '127.0.0.1',
  port: 8080,
  path: '/length_request',
};

// Make a request
const req = request(options);
req.end();

req.on('information', (info) => {
  console.log(`Got information prior to main response: ${info.statusCode}`);
});
```

*CommonJS version:*

```js
const http = require('node:http');

const options = {
  host: '127.0.0.1',
  port: 8080,
  path: '/length_request',
};

// Make a request
const req = http.request(options);
req.end();

req.on('information', (info) => {
  console.log(`Got information prior to main response: ${info.statusCode}`);
});
```

101 Upgrade statuses do not fire this event due to their break from the
traditional HTTP request/response chain, such as web sockets, in-place TLS
upgrades, or HTTP 2.0. To be notified of 101 Upgrade notices, listen for the
[`'upgrade'`](#event-upgrade) event instead.

### Event: `'response'`

Added in: v0.1.0

- `response` [`<http.IncomingMessage>`](27-http.md#class-httpincomingmessage)

Emitted when a response is received to this request. This event is emitted only
once.

### Event: `'socket'`

Added in: v0.5.3

- `socket` [`<stream.Duplex>`](51-stream.md#class-streamduplex)

This event is guaranteed to be passed an instance of the [`<net.Socket>`](38-net.md#class-netsocket) class,
a subclass of [`<stream.Duplex>`](51-stream.md#class-streamduplex), unless the user specifies a socket
type other than [`<net.Socket>`](38-net.md#class-netsocket).

### Event: `'timeout'`

Added in: v0.7.8

Emitted when the underlying socket times out from inactivity. This only notifies
that the socket has been idle. The request must be destroyed manually.

See also: [`request.setTimeout()`](#requestsettimeouttimeout-callback).

### Event: `'upgrade'`

Added in: v0.1.94

- `response` [`<http.IncomingMessage>`](27-http.md#class-httpincomingmessage)
- `stream` [`<stream.Duplex>`](51-stream.md#class-streamduplex)
- `head` [`<Buffer>`](07-buffer.md#class-buffer)

Emitted each time a server responds to a request with an upgrade. If this
event is not being listened for and the response status code is 101 Switching
Protocols, clients receiving an upgrade header will have their connections
closed.

This event is guaranteed to be passed an instance of the [`<net.Socket>`](38-net.md#class-netsocket) class,
a subclass of [`<stream.Duplex>`](51-stream.md#class-streamduplex), unless the user specifies a socket
type other than [`<net.Socket>`](38-net.md#class-netsocket).

A client server pair demonstrating how to listen for the `'upgrade'` event.

*ES module version:*

```js
import http from 'node:http';
import process from 'node:process';

// Create an HTTP server
const server = http.createServer((req, res) => {
  res.writeHead(200, { 'Content-Type': 'text/plain' });
  res.end('okay');
});
server.on('upgrade', (req, stream, head) => {
  stream.write('HTTP/1.1 101 Web Socket Protocol Handshake\r\n' +
               'Upgrade: WebSocket\r\n' +
               'Connection: Upgrade\r\n' +
               '\r\n');

  stream.pipe(stream); // echo back
});

// Now that server is running
server.listen(1337, '127.0.0.1', () => {

  // make a request
  const options = {
    port: 1337,
    host: '127.0.0.1',
    headers: {
      'Connection': 'Upgrade',
      'Upgrade': 'websocket',
    },
  };

  const req = http.request(options);
  req.end();

  req.on('upgrade', (res, stream, upgradeHead) => {
    console.log('got upgraded!');
    stream.end();
    process.exit(0);
  });
});
```

*CommonJS version:*

```js
const http = require('node:http');

// Create an HTTP server
const server = http.createServer((req, res) => {
  res.writeHead(200, { 'Content-Type': 'text/plain' });
  res.end('okay');
});
server.on('upgrade', (req, stream, head) => {
  stream.write('HTTP/1.1 101 Web Socket Protocol Handshake\r\n' +
               'Upgrade: WebSocket\r\n' +
               'Connection: Upgrade\r\n' +
               '\r\n');

  stream.pipe(stream); // echo back
});

// Now that server is running
server.listen(1337, '127.0.0.1', () => {

  // make a request
  const options = {
    port: 1337,
    host: '127.0.0.1',
    headers: {
      'Connection': 'Upgrade',
      'Upgrade': 'websocket',
    },
  };

  const req = http.request(options);
  req.end();

  req.on('upgrade', (res, stream, upgradeHead) => {
    console.log('got upgraded!');
    stream.end();
    process.exit(0);
  });
});
```

### `request.abort()`

Added in: v0.3.8Deprecated in: v14.1.0, v13.14.0

Stability: 0 - Deprecated: Use [`request.destroy()`](#requestdestroyerror) instead.

Marks the request as aborting. Calling this will cause remaining data
in the response to be dropped and the socket to be destroyed.

### `request.aborted`

Added in: v0.11.14Deprecated in: v17.0.0, v16.12.0

**History**

| Version | Changes |
| --- | --- |
| v11.0.0 | The `aborted` property is no longer a timestamp number. |

Stability: 0 - Deprecated. Check [`request.destroyed`](#requestdestroyed) instead.

- Type: [`<boolean>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#boolean_type)

The `request.aborted` property will be `true` if the request has
been aborted.

### `request.connection`

Added in: v0.3.0Deprecated in: v13.0.0

Stability: 0 - Deprecated. Use [`request.socket`](#requestsocket).

- Type: [`<stream.Duplex>`](51-stream.md#class-streamduplex)

See [`request.socket`](#requestsocket).

### `request.cork()`

Added in: v13.2.0, v12.16.0

See [`writable.cork()`](51-stream.md#writablecork).

### `request.end([data[, encoding]][, callback])`

Added in: v0.1.90

**History**

| Version | Changes |
| --- | --- |
| v15.0.0 | The `data` parameter can now be a `Uint8Array`. |
| v10.0.0 | This method now returns a reference to `ClientRequest`. |

- `data` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) | [`<Buffer>`](07-buffer.md#class-buffer) | [`<Uint8Array>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Uint8Array)
- `encoding` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type)
- `callback` [`<Function>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Function)
- Returns: [`<this>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Operators/this)

Finishes sending the request. If any parts of the body are
unsent, it will flush them to the stream. If the request is
chunked, this will send the terminating `'0\r\n\r\n'`.

If `data` is specified, it is equivalent to calling
[`request.write(data, encoding)`](#requestwritechunk-encoding-callback) followed by `request.end(callback)`.

If `callback` is specified, it will be called when the request stream
is finished.

### `request.destroy([error])`

Added in: v0.3.0

**History**

| Version | Changes |
| --- | --- |
| v14.5.0 | The function returns `this` for consistency with other Readable streams. |

- `error` [`<Error>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Error) Optional, an error to emit with `'error'` event.
- Returns: [`<this>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Operators/this)

Destroy the request. Optionally emit an `'error'` event,
and emit a `'close'` event. Calling this will cause remaining data
in the response to be dropped, and the socket to be destroyed if used,
or returned to the corresponding Agent pool otherwise if possible.

See [`writable.destroy()`](51-stream.md#writabledestroyerror) for further details.

#### `request.destroyed`

Added in: v14.1.0, v13.14.0

- Type: [`<boolean>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#boolean_type)

Is `true` after [`request.destroy()`](#requestdestroyerror) has been called.

See [`writable.destroyed`](51-stream.md#writabledestroyed) for further details.

### `request.finished`

Added in: v0.0.1Deprecated in: v13.4.0, v12.16.0

Stability: 0 - Deprecated. Use [`request.writableEnded`](#requestwritableended).

- Type: [`<boolean>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#boolean_type)

The `request.finished` property will be `true` if [`request.end()`](#requestenddata-encoding-callback)
has been called. `request.end()` will automatically be called if the
request was initiated via [`http.get()`](#httpgetoptions-callback).

### `request.flushHeaders()`

Added in: v1.6.0

Flushes the request headers.

For efficiency reasons, Node.js normally buffers the request headers until
`request.end()` is called or the first chunk of request data is written. It
then tries to pack the request headers and data into a single TCP packet.

That's usually desired (it saves a TCP round-trip), but not when the first
data is not sent until possibly much later. `request.flushHeaders()` bypasses
the optimization and kickstarts the request.

### `request.getHeader(name)`

Added in: v1.6.0

- `name` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type)
- Returns: [`<any>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#Data_types)

Reads out a header on the request. The name is case-insensitive.
The type of the return value depends on the arguments provided to
[`request.setHeader()`](#requestsetheadername-value).

```js
request.setHeader('content-type', 'text/html');
request.setHeader('Content-Length', Buffer.byteLength(body));
request.setHeader('Cookie', ['type=ninja', 'language=javascript']);
const contentType = request.getHeader('Content-Type');
// 'contentType' is 'text/html'
const contentLength = request.getHeader('Content-Length');
// 'contentLength' is of type number
const cookie = request.getHeader('Cookie');
// 'cookie' is of type string[]
```

### `request.getHeaderNames()`

Added in: v7.7.0

- Returns: [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type)[]

Returns an array containing the unique names of the current outgoing headers.
All header names are lowercase.

```js
request.setHeader('Foo', 'bar');
request.setHeader('Cookie', ['foo=bar', 'bar=baz']);

const headerNames = request.getHeaderNames();
// headerNames === ['foo', 'cookie']
```

### `request.getHeaders()`

Added in: v7.7.0

- Returns: [`<Object>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Object)

Returns a shallow copy of the current outgoing headers. Since a shallow copy
is used, array values may be mutated without additional calls to various
header-related http module methods. The keys of the returned object are the
header names and the values are the respective header values. All header names
are lowercase.

The object returned by the `request.getHeaders()` method *does not*
prototypically inherit from the JavaScript `Object`. This means that typical
`Object` methods such as `obj.toString()`, `obj.hasOwnProperty()`, and others
are not defined and *will not work*.

```js
request.setHeader('Foo', 'bar');
request.setHeader('Cookie', ['foo=bar', 'bar=baz']);

const headers = request.getHeaders();
// headers === { foo: 'bar', 'cookie': ['foo=bar', 'bar=baz'] }
```

### `request.getRawHeaderNames()`

Added in: v15.13.0, v14.17.0

- Returns: [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type)[]

Returns an array containing the unique names of the current outgoing raw
headers. Header names are returned with their exact casing being set.

```js
request.setHeader('Foo', 'bar');
request.setHeader('Set-Cookie', ['foo=bar', 'bar=baz']);

const headerNames = request.getRawHeaderNames();
// headerNames === ['Foo', 'Set-Cookie']
```

### `request.hasHeader(name)`

Added in: v7.7.0

- `name` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type)
- Returns: [`<boolean>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#boolean_type)

Returns `true` if the header identified by `name` is currently set in the
outgoing headers. The header name matching is case-insensitive.

```js
const hasContentType = request.hasHeader('content-type');
```

### `request.maxHeadersCount`

- Type: [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) **Default:** `1000`

Limits maximum response headers count. If set to 0, no limit will be applied.

### `request.path`

Added in: v0.4.0

- Type: [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) The request path.

### `request.method`

Added in: v0.1.97

- Type: [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) The request method.

### `request.host`

Added in: v14.5.0, v12.19.0

- Type: [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) The request host.

### `request.protocol`

Added in: v14.5.0, v12.19.0

- Type: [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) The request protocol.

### `request.removeHeader(name)`

Added in: v1.6.0

- `name` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type)

Removes a header that's already defined into headers object.

```js
request.removeHeader('Content-Type');
```

### `request.reusedSocket`

Added in: v13.0.0, v12.16.0

- Type: [`<boolean>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#boolean_type) Whether the request is sent through a reused socket.

When sending a request through a keep-alive-enabled agent, the underlying socket
might be reused. If the server closes the connection at the same time that the
agent reuses it, the request may encounter an `ECONNRESET` error.

The following example sends requests near the server's keep-alive timeout to
make this race easier to reproduce. It sets
[`server.keepAliveTimeoutBuffer`](#serverkeepalivetimeoutbuffer) to `0` so the internal socket timeout
matches [`server.keepAliveTimeout`](#serverkeepalivetimeout).

*ES module version:*

```js
import http from 'node:http';
const agent = new http.Agent({ keepAlive: true });
const server = http.createServer(
  { keepAliveTimeout: 3000, keepAliveTimeoutBuffer: 0 },
  (req, res) => {
    res.write('hello\n');
    res.end();
  },
);
server.listen(3000);

setInterval(() => {
  http.get('http://localhost:3000', { agent }, (res) => {
    res.resume();
  });
}, 3000);
```

*CommonJS version:*

```js
const http = require('node:http');
const agent = new http.Agent({ keepAlive: true });
const server = http.createServer(
  { keepAliveTimeout: 3000, keepAliveTimeoutBuffer: 0 },
  (req, res) => {
    res.write('hello\n');
    res.end();
  },
);
server.listen(3000);

setInterval(() => {
  http.get('http://localhost:3000', { agent }, (res) => {
    res.resume();
  });
}, 3000);
```

By checking `request.reusedSocket`, an application can retry idempotent requests
that fail because a reused connection was reset.

*ES module version:*

```js
import http from 'node:http';
const agent = new http.Agent({ keepAlive: true });

function retriableRequest() {
  const req = http
    .get('http://localhost:3000', { agent }, (res) => {
      // ...
    })
    .on('error', (err) => {
      // Check if retry is needed
      if (req.reusedSocket && err.code === 'ECONNRESET') {
        retriableRequest();
      }
    });
}

retriableRequest();
```

*CommonJS version:*

```js
const http = require('node:http');
const agent = new http.Agent({ keepAlive: true });

function retriableRequest() {
  const req = http
    .get('http://localhost:3000', { agent }, (res) => {
      // ...
    })
    .on('error', (err) => {
      // Check if retry is needed
      if (req.reusedSocket && err.code === 'ECONNRESET') {
        retriableRequest();
      }
    });
}

retriableRequest();
```

### `request.setHeader(name, value)`

Added in: v1.6.0

- `name` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type)
- `value` [`<any>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#Data_types)

Sets a single header value for headers object. If this header already exists in
the to-be-sent headers, its value will be replaced. Use an array of strings
here to send multiple headers with the same name. Non-string values will be
stored without modification. Therefore, [`request.getHeader()`](#requestgetheadername) may return
non-string values. However, the non-string values will be converted to strings
for network transmission.

```js
request.setHeader('Content-Type', 'application/json');
```

or

```js
request.setHeader('Cookie', ['type=ninja', 'language=javascript']);
```

When the value is a string an exception will be thrown if it contains
characters outside the `latin1` encoding.

If you need to pass UTF-8 characters in the value please encode the value
using the [RFC 8187](https://www.rfc-editor.org/rfc/rfc8187.txt) standard.

```js
const filename = 'Rock 🎵.txt';
request.setHeader('Content-Disposition', `attachment; filename*=utf-8''${encodeURIComponent(filename)}`);
```

### `request.setNoDelay([noDelay])`

Added in: v0.5.9

- `noDelay` [`<boolean>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#boolean_type)

Once a socket is assigned to this request and is connected
[`socket.setNoDelay()`](38-net.md#socketsetnodelaynodelay) will be called.

### `request.setSocketKeepAlive([enable][, initialDelay])`

Added in: v0.5.9

- `enable` [`<boolean>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#boolean_type)
- `initialDelay` [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type)

Once a socket is assigned to this request and is connected
[`socket.setKeepAlive()`](38-net.md#socketsetkeepalive) will be called.

### `request.setTimeout(timeout[, callback])`

Added in: v0.5.9

**History**

| Version | Changes |
| --- | --- |
| v9.0.0 | Consistently set socket timeout only when the socket connects. |

- `timeout` [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) Milliseconds before a request times out.
- `callback` [`<Function>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Function) Optional function to be called when a timeout occurs.
  Same as binding to the `'timeout'` event.
- Returns: [`<http.ClientRequest>`](27-http.md#class-httpclientrequest)

Once a socket is assigned to this request and is connected
[`socket.setTimeout()`](38-net.md#socketsettimeouttimeout-callback) will be called.

### `request.socket`

Added in: v0.3.0

- Type: [`<stream.Duplex>`](51-stream.md#class-streamduplex)

Reference to the underlying socket. Usually users will not want to access
this property. In particular, the socket will not emit `'readable'` events
because of how the protocol parser attaches to the socket.

*ES module version:*

```js
import http from 'node:http';
const options = {
  host: 'www.google.com',
};
const req = http.get(options);
req.end();
req.once('response', (res) => {
  const ip = req.socket.localAddress;
  const port = req.socket.localPort;
  console.log(`Your IP address is ${ip} and your source port is ${port}.`);
  // Consume response object
});
```

*CommonJS version:*

```js
const http = require('node:http');
const options = {
  host: 'www.google.com',
};
const req = http.get(options);
req.end();
req.once('response', (res) => {
  const ip = req.socket.localAddress;
  const port = req.socket.localPort;
  console.log(`Your IP address is ${ip} and your source port is ${port}.`);
  // Consume response object
});
```

This property is guaranteed to be an instance of the [`<net.Socket>`](38-net.md#class-netsocket) class,
a subclass of [`<stream.Duplex>`](51-stream.md#class-streamduplex), unless the user specified a socket
type other than [`<net.Socket>`](38-net.md#class-netsocket).

### `request.uncork()`

Added in: v13.2.0, v12.16.0

See [`writable.uncork()`](51-stream.md#writableuncork).

### `request.writableEnded`

Added in: v12.9.0

- Type: [`<boolean>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#boolean_type)

Is `true` after [`request.end()`](#requestenddata-encoding-callback) has been called. This property
does not indicate whether the data has been flushed, for this use
[`request.writableFinished`](#requestwritablefinished) instead.

### `request.writableFinished`

Added in: v12.7.0

- Type: [`<boolean>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#boolean_type)

Is `true` if all data has been flushed to the underlying system, immediately
before the [`'finish'`](#event-finish) event is emitted.

### `request.write(chunk[, encoding][, callback])`

Added in: v0.1.29

**History**

| Version | Changes |
| --- | --- |
| v15.0.0 | The `chunk` parameter can now be a `Uint8Array`. |

- `chunk` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) | [`<Buffer>`](07-buffer.md#class-buffer) | [`<Uint8Array>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Uint8Array)
- `encoding` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type)
- `callback` [`<Function>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Function)
- Returns: [`<boolean>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#boolean_type)

Sends a chunk of the body. This method can be called multiple times. If no
`Content-Length` is set, data will automatically be encoded in HTTP Chunked
transfer encoding, so that server knows when the data ends. The
`Transfer-Encoding: chunked` header is added. Calling [`request.end()`](#requestenddata-encoding-callback)
is necessary to finish sending the request.

The `encoding` argument is optional and only applies when `chunk` is a string.
Defaults to `'utf8'`.

The `callback` argument is optional and will be called when this chunk of data
is flushed, but only if the chunk is non-empty.

Returns `true` if the entire data was flushed successfully to the kernel
buffer. Returns `false` if all or part of the data was queued in user memory.
`'drain'` will be emitted when the buffer is free again.

When `write` function is called with empty string or buffer, it does
nothing and waits for more input.

## Class: `http.Server`

Added in: v0.1.17

- Extends: [`<net.Server>`](38-net.md#class-netserver)

### Event: `'checkContinue'`

Added in: v0.3.0

- `request` [`<http.IncomingMessage>`](27-http.md#class-httpincomingmessage)
- `response` [`<http.ServerResponse>`](27-http.md#class-httpserverresponse)

Emitted each time a request with an HTTP `Expect: 100-continue` is received.
If this event is not listened for, the server will automatically respond
with a `100 Continue` as appropriate.

Handling this event involves calling [`response.writeContinue()`](#responsewritecontinue) if the
client should continue to send the request body, or generating an appropriate
HTTP response (e.g. 400 Bad Request) if the client should not continue to send
the request body.

When this event is emitted and handled, the [`'request'`](#event-request) event will
not be emitted.

### Event: `'checkExpectation'`

Added in: v5.5.0

- `request` [`<http.IncomingMessage>`](27-http.md#class-httpincomingmessage)
- `response` [`<http.ServerResponse>`](27-http.md#class-httpserverresponse)

Emitted each time a request with an HTTP `Expect` header is received, where the
value is not `100-continue`. If this event is not listened for, the server will
automatically respond with a `417 Expectation Failed` as appropriate.

When this event is emitted and handled, the [`'request'`](#event-request) event will
not be emitted.

### Event: `'clientError'`

Added in: v0.1.94

**History**

| Version | Changes |
| --- | --- |
| v12.0.0 | The default behavior will return a 431 Request Header Fields Too Large if an HPE\_HEADER\_OVERFLOW error occurs. |
| v9.4.0 | The `rawPacket` is the current buffer that just parsed. Adding this buffer to the error object of `'clientError'` event is to make it possible that developers can log the broken packet. |
| v6.0.0 | The default action of calling `.destroy()` on the `socket` will no longer take place if there are listeners attached for `'clientError'`. |

- `exception` [`<Error>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Error)
- `socket` [`<stream.Duplex>`](51-stream.md#class-streamduplex)

If a client connection emits an `'error'` event, it will be forwarded here.
Listener of this event is responsible for closing/destroying the underlying
socket. For example, one may wish to more gracefully close the socket with a
custom HTTP response instead of abruptly severing the connection. The socket
**must be closed or destroyed** before the listener ends.

This event is guaranteed to be passed an instance of the [`<net.Socket>`](38-net.md#class-netsocket) class,
a subclass of [`<stream.Duplex>`](51-stream.md#class-streamduplex), unless the user specifies a socket
type other than [`<net.Socket>`](38-net.md#class-netsocket).

Default behavior is to try close the socket with an HTTP '400 Bad Request',
or an HTTP '431 Request Header Fields Too Large' in the case of an
[`HPE_HEADER_OVERFLOW`](22-errors.md#hpe_header_overflow) error. If the socket is not writable or headers
of the current attached [`http.ServerResponse`](#class-httpserverresponse) has been sent, it is
immediately destroyed.

`socket` is the [`net.Socket`](38-net.md#class-netsocket) object that the error originated from.

*ES module version:*

```js
import http from 'node:http';

const server = http.createServer((req, res) => {
  res.end();
});
server.on('clientError', (err, socket) => {
  socket.end('HTTP/1.1 400 Bad Request\r\n\r\n');
});
server.listen(8000);
```

*CommonJS version:*

```js
const http = require('node:http');

const server = http.createServer((req, res) => {
  res.end();
});
server.on('clientError', (err, socket) => {
  socket.end('HTTP/1.1 400 Bad Request\r\n\r\n');
});
server.listen(8000);
```

When the `'clientError'` event occurs, there is no `request` or `response`
object, so any HTTP response sent, including response headers and payload,
*must* be written directly to the `socket` object. Care must be taken to
ensure the response is a properly formatted HTTP response message.

`err` is an instance of `Error` with two extra columns:

- `bytesParsed`: the bytes count of request packet that Node.js may have parsed
  correctly;
- `rawPacket`: the raw packet of current request.

In some cases, the client has already received the response and/or the socket
has already been destroyed, like in case of `ECONNRESET` errors. Before
trying to send data to the socket, it is better to check that it is still
writable.

```js
server.on('clientError', (err, socket) => {
  if (err.code === 'ECONNRESET' || !socket.writable) {
    return;
  }

  socket.end('HTTP/1.1 400 Bad Request\r\n\r\n');
});
```

### Event: `'close'`

Added in: v0.1.4

Emitted when the server closes.

### Event: `'connect'`

Added in: v0.7.0

- `request` [`<http.IncomingMessage>`](27-http.md#class-httpincomingmessage) Arguments for the HTTP request, as it is in
  the [`'request'`](#event-request) event
- `socket` [`<stream.Duplex>`](51-stream.md#class-streamduplex) Network socket between the server and client
- `head` [`<Buffer>`](07-buffer.md#class-buffer) The first packet of the tunneling stream (may be empty)

Emitted each time a client requests an HTTP `CONNECT` method. If this event is
not listened for, then clients requesting a `CONNECT` method will have their
connections closed.

This event is guaranteed to be passed an instance of the [`<net.Socket>`](38-net.md#class-netsocket) class,
a subclass of [`<stream.Duplex>`](51-stream.md#class-streamduplex), unless the user specifies a socket
type other than [`<net.Socket>`](38-net.md#class-netsocket).

After this event is emitted, the request's socket will not have a `'data'`
event listener, meaning it will need to be bound in order to handle data
sent to the server on that socket.

### Event: `'connection'`

Added in: v0.1.0

- `socket` [`<stream.Duplex>`](51-stream.md#class-streamduplex)

This event is emitted when a new TCP stream is established. `socket` is
typically an object of type [`net.Socket`](38-net.md#class-netsocket). Usually users will not want to
access this event. In particular, the socket will not emit `'readable'` events
because of how the protocol parser attaches to the socket. The `socket` can
also be accessed at `request.socket`.

This event can also be explicitly emitted by users to inject connections
into the HTTP server. In that case, any [`Duplex`](51-stream.md#class-streamduplex) stream can be passed.

If `socket.setTimeout()` is called here, the timeout will be replaced with
`server.keepAliveTimeout` when the socket has served a request (if
`server.keepAliveTimeout` is non-zero).

This event is guaranteed to be passed an instance of the [`<net.Socket>`](38-net.md#class-netsocket) class,
a subclass of [`<stream.Duplex>`](51-stream.md#class-streamduplex), unless the user specifies a socket
type other than [`<net.Socket>`](38-net.md#class-netsocket).

### Event: `'dropRequest'`

Added in: v18.7.0, v16.17.0

- `request` [`<http.IncomingMessage>`](27-http.md#class-httpincomingmessage) Arguments for the HTTP request, as it is in
  the [`'request'`](#event-request) event
- `socket` [`<stream.Duplex>`](51-stream.md#class-streamduplex) Network socket between the server and client

When the number of requests on a socket reaches the threshold of
`server.maxRequestsPerSocket`, the server will drop new requests
and emit `'dropRequest'` event instead, then send `503` to client.

### Event: `'request'`

Added in: v0.1.0

- `request` [`<http.IncomingMessage>`](27-http.md#class-httpincomingmessage)
- `response` [`<http.ServerResponse>`](27-http.md#class-httpserverresponse)

Emitted each time there is a request. There may be multiple requests
per connection (in the case of HTTP Keep-Alive connections).

### Event: `'upgrade'`

Added in: v0.1.94

**History**

| Version | Changes |
| --- | --- |
| v26.0.0 | Request bodies are no longer exposed raw (unparsed) on the socket argument. Instead, if a body is received, the stream argument will be a duplex that emits socket content only after the request body, while the parsed request body data will be emitted from the request, just as in normal server `'request'` events. |
| v24.9.0, v22.21.0 | Whether this event is fired can now be controlled by the `shouldUpgradeCallback` and sockets will be destroyed if upgraded while no event handler is listening. |
| v10.0.0 | Not listening to this event no longer causes the socket to be destroyed if a client sends an Upgrade header. |

- `request` [`<http.IncomingMessage>`](27-http.md#class-httpincomingmessage) Arguments for the HTTP request, as it is in
  the [`'request'`](#event-request) event
- `stream` [`<stream.Duplex>`](51-stream.md#class-streamduplex) The upgraded stream between the server and client
- `head` [`<Buffer>`](07-buffer.md#class-buffer) The first packet of the upgraded stream (may be empty)

Emitted each time a client's HTTP upgrade request is accepted. By default
all HTTP upgrade requests are ignored (i.e. only regular `'request'` events
are emitted, sticking with the normal HTTP request/response flow) unless you
listen to this event, in which case they are all accepted (i.e. the `'upgrade'`
event is emitted instead, and future communication must handled directly
through the raw stream). You can control this more precisely by using the
server `shouldUpgradeCallback` option.

Listening to this event is optional and clients cannot insist on a protocol
change.

If an upgrade is accepted by `shouldUpgradeCallback` but no event handler
is registered then the socket will be destroyed, resulting in an immediate
connection closure for the client.

In the uncommon case that the incoming request has a body, this body will be
parsed as normal, separate to the upgrade stream, and the raw stream data will
only begin after it has completed. To ensure that reading from the stream isn't
blocked by waiting for the request body to be read, any reads on the stream
will start the request body flowing automatically. If you want to read the
request body, ensure that you do so (i.e. you attach `'data'` listeners)
before starting to read from the upgraded stream.

The stream argument will typically be the [`<net.Socket>`](38-net.md#class-netsocket) instance used by the
request, but in some cases (such as with a request body) it may be a duplex
stream. If required, you can access the raw connection underlying the request
via [`request.socket`](#requestsocket), which is guaranteed to be an instance of [`<net.Socket>`](38-net.md#class-netsocket)
unless the user specified another socket type.

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
- Returns: [`<http.Server>`](27-http.md#class-httpserver)

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

- Extends: [`<http.OutgoingMessage>`](27-http.md#class-httpoutgoingmessage)

This object is created internally by an HTTP server, not by the user. It is
passed as the second parameter to the [`'request'`](#event-request) event.

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
will result in a [`TypeError`](22-errors.md#class-typeerror) being thrown.

### `response.connection`

Added in: v0.3.0Deprecated in: v13.0.0

Stability: 0 - Deprecated. Use [`response.socket`](#responsesocket).

- Type: [`<stream.Duplex>`](51-stream.md#class-streamduplex)

See [`response.socket`](#responsesocket).

### `response.cork()`

Added in: v13.2.0, v12.16.0

See [`writable.cork()`](51-stream.md#writablecork).

### `response.end([data[, encoding]][, callback])`

Added in: v0.1.90

**History**

| Version | Changes |
| --- | --- |
| v15.0.0 | The `data` parameter can now be a `Uint8Array`. |
| v10.0.0 | This method now returns a reference to `ServerResponse`. |

- `data` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) | [`<Buffer>`](07-buffer.md#class-buffer) | [`<Uint8Array>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Uint8Array)
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

Flushes the response headers. See also: [`request.flushHeaders()`](#requestflushheaders).

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

- Type: [`<http.IncomingMessage>`](27-http.md#class-httpincomingmessage)

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
- Returns: [`<http.ServerResponse>`](27-http.md#class-httpserverresponse)

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
will result in a [`TypeError`](22-errors.md#class-typeerror) being thrown.

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
- Returns: [`<http.ServerResponse>`](27-http.md#class-httpserverresponse)

Sets the Socket's timeout value to `msecs`. If a callback is
provided, then it is added as a listener on the `'timeout'` event on
the response object.

If no `'timeout'` listener is added to the request, the response, or
the server, then sockets are destroyed when they time out. If a handler is
assigned to the request, the response, or the server's `'timeout'` events,
timed out sockets must be handled explicitly.

### `response.socket`

Added in: v0.3.0

- Type: [`<stream.Duplex>`](51-stream.md#class-streamduplex)

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
a subclass of [`<stream.Duplex>`](51-stream.md#class-streamduplex), unless the user specified a socket
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
in an `Error` being thrown, identified by `code:` [`'ERR_HTTP_CONTENT_LENGTH_MISMATCH'`](22-errors.md#err_http_content_length_mismatch).

### `response.uncork()`

Added in: v13.2.0, v12.16.0

See [`writable.uncork()`](51-stream.md#writableuncork).

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
before the [`'finish'`](#event-finish) event is emitted.

### `response.write(chunk[, encoding][, callback])`

Added in: v0.1.29

**History**

| Version | Changes |
| --- | --- |
| v15.0.0 | The `chunk` parameter can now be a `Uint8Array`. |

- `chunk` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) | [`<Buffer>`](07-buffer.md#class-buffer) | [`<Uint8Array>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Uint8Array)
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
the request body should be sent. See the [`'checkContinue'`](#event-checkcontinue) event on
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
- Returns: [`<http.ServerResponse>`](27-http.md#class-httpserverresponse)

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
[`Buffer.byteLength()`](07-buffer.md#static-method-bufferbytelengthstring-encoding) to determine the length of the body in bytes. Node.js
will check whether `Content-Length` and the length of the body which has
been transmitted are equal or not.

Attempting to set a header field name or value that contains invalid characters
will result in a [`TypeError`](22-errors.md#class-typeerror) being thrown.

### `response.writeInformation(statusCode[, headers][, callback])`

Added in: v26.2.0

- `statusCode` [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) An HTTP 1xx informational status code, between `100`
  and `199` inclusive, excluding `101` (Switching Protocols) which is only
  available through the [`'upgrade'`](#event-upgrade) event.
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

Clients receive these responses via the [`'information'`](#event-information)
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

- Extends: [`<stream.Readable>`](51-stream.md#class-streamreadable)

An `IncomingMessage` object is created by [`http.Server`](#class-httpserver) or
[`http.ClientRequest`](#class-httpclientrequest) and passed as the first argument to the [`'request'`](#event-request)
and [`'response'`](#event-response) event respectively. It may be used to access response
status, headers, and data.

Different from its `socket` value which is a subclass of [`<stream.Duplex>`](51-stream.md#class-streamduplex), the `IncomingMessage` itself extends [`<stream.Readable>`](51-stream.md#class-streamreadable) and is created separately to
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

Stability: 0 - Deprecated. Check `message.destroyed` from [`<stream.Readable>`](51-stream.md#class-streamreadable).

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
  use the option `joinDuplicateHeaders` in [`http.request()`](#httprequestoptions-callback)
  and [`http.createServer()`](#httpcreateserveroptions-requestlistener). See RFC 9110 Section 5.3 for more
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

**Only valid for request obtained from [`http.Server`](#class-httpserver).**

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
- Returns: [`<http.IncomingMessage>`](27-http.md#class-httpincomingmessage)

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

- Type: [`<stream.Duplex>`](51-stream.md#class-streamduplex)

The [`net.Socket`](38-net.md#class-netsocket) object associated with the connection.

With HTTPS support, use [`request.socket.getPeerCertificate()`](55-tls.md#tlssocketgetpeercertificatedetailed) to obtain the
client's authentication details.

This property is guaranteed to be an instance of the [`<net.Socket>`](38-net.md#class-netsocket) class,
a subclass of [`<stream.Duplex>`](51-stream.md#class-streamduplex), unless the user specified a socket
type other than [`<net.Socket>`](38-net.md#class-netsocket) or internally nulled.

### `message.statusCode`

Added in: v0.1.1

- Type: [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type)

**Only valid for response obtained from [`http.ClientRequest`](#class-httpclientrequest).**

The 3-digit HTTP response status code. E.G. `404`.

### `message.statusMessage`

Added in: v0.11.10

- Type: [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type)

**Only valid for response obtained from [`http.ClientRequest`](#class-httpclientrequest).**

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

**Only valid for request obtained from [`http.Server`](#class-httpserver).**

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

- Extends: [`<Stream>`](51-stream.md#stream)

This class serves as the parent class of [`http.ClientRequest`](#class-httpclientrequest)
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

See [`writable.cork()`](51-stream.md#writablecork).

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

- `chunk` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) | [`<Buffer>`](07-buffer.md#class-buffer) | [`<Uint8Array>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Uint8Array)
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

- Type: [`<stream.Duplex>`](51-stream.md#class-streamduplex)

Reference to the underlying socket. Usually, users will not want to access
this property.

After calling `outgoingMessage.end()`, this property will be nulled.

### `outgoingMessage.uncork()`

Added in: v13.2.0, v12.16.0

See [`writable.uncork()`](51-stream.md#writableuncork)

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
buffer level when [`writable.write()`](51-stream.md#writablewritechunk-encoding-callback) starts returning false (`16384`).

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

- `chunk` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) | [`<Buffer>`](07-buffer.md#class-buffer) | [`<Uint8Array>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Uint8Array)
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

## `http.METHODS`

Added in: v0.11.8

- Type: [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type)[]

A list of the HTTP methods that are supported by the parser.

## `http.STATUS_CODES`

Added in: v0.1.22

- Type: [`<Object>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Object)

A collection of all the standard HTTP response status codes, and the
short description of each. For example, `http.STATUS_CODES[404] === 'Not Found'`.

## `http.createServer([options][, requestListener])`

Added in: v0.1.13

**History**

| Version | Changes |
| --- | --- |
| v26.3.0 | The `httpValidation` option is supported now. |
| v25.1.0, v24.12.0 | Add optimizeEmptyRequests option. |
| v24.9.0, v22.21.0 | The `shouldUpgradeCallback` option is now supported. |
| v24.6.0, v22.19.0 | The `keepAliveTimeoutBuffer` option is supported now. |
| v20.1.0, v18.17.0 | The `highWaterMark` option is supported now. |
| v18.0.0 | The `requestTimeout`, `headersTimeout`, `keepAliveTimeout`, and `connectionsCheckingInterval` options are supported now. |
| v18.0.0 | The `noDelay` option now defaults to `true`. |
| v17.7.0, v16.15.0 | The `noDelay`, `keepAlive` and `keepAliveInitialDelay` options are supported now. |
| v13.8.0, v12.15.0, v10.19.0 | The `insecureHTTPParser` option is supported now. |
| v13.3.0 | The `maxHeaderSize` option is supported now. |
| v9.6.0, v8.12.0 | The `options` argument is supported now. |

- `options` [`<Object>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Object)

  - `connectionsCheckingInterval`: Sets the interval value in milliseconds to
    check for request and headers timeout in incomplete requests.
    **Default:** `30000`.
  - `headersTimeout`: Sets the timeout value in milliseconds for receiving
    the complete HTTP headers from the client.
    See [`server.headersTimeout`](#serverheaderstimeout) for more information.
    **Default:** `60000`.
  - `highWaterMark` [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) Optionally overrides all `socket`s'
    `readableHighWaterMark` and `writableHighWaterMark`. This affects
    `highWaterMark` property of both `IncomingMessage` and `ServerResponse`.
    **Default:** See [`stream.getDefaultHighWaterMark()`](51-stream.md#streamgetdefaulthighwatermarkobjectmode).
  - `httpValidation` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) Controls HTTP header value validation strictness
    for incoming requests. Accepted values are:
    - `'strict'`: Strictest validation; rejects any non-ASCII or control
      characters in header values.
    - `'relaxed'`: Allows a limited set of non-ASCII characters in header
      values, aligning with the
      [Fetch specification](https://fetch.spec.whatwg.org/).
    - `'insecure'`: Disables all header value validation (equivalent to
      `insecureHTTPParser: true`).
      Cannot be used together with `insecureHTTPParser`. **Default:** `'strict'`.
  - `insecureHTTPParser` [`<boolean>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#boolean_type) If set to `true`, it will use an HTTP parser
    with leniency flags enabled. Using the insecure parser should be avoided.
    See [`--insecure-http-parser`](13-cli.md#--insecure-http-parser) for more information.
    **Default:** `false`.
  - `IncomingMessage` [`<http.IncomingMessage>`](27-http.md#class-httpincomingmessage) Specifies the `IncomingMessage`
    class to be used. Useful for extending the original `IncomingMessage`.
    **Default:** `IncomingMessage`.
  - `joinDuplicateHeaders` [`<boolean>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#boolean_type) If set to `true`, this option allows
    joining the field line values of multiple headers in a request with
    a comma (`,` ) instead of discarding the duplicates.
    For more information, refer to [`message.headers`](#messageheaders).
    **Default:** `false`.
  - `keepAlive` [`<boolean>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#boolean_type) If set to `true`, it enables keep-alive functionality
    on the socket immediately after a new incoming connection is received,
    similarly on what is done in [`socket.setKeepAlive()`](38-net.md#socketsetkeepalive).
    **Default:** `false`.
  - `keepAliveInitialDelay` [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) If set to a positive number, it sets the
    initial delay before the first keepalive probe is sent on an idle socket. **Default:** `0`.
  - `keepAliveTimeout`: The number of milliseconds of inactivity a server
    needs to wait for additional incoming data, after it has finished writing
    the last response, before a socket will be destroyed.
    See [`server.keepAliveTimeout`](#serverkeepalivetimeout) for more information.
    **Default:** `65000`.
  - `keepAliveTimeoutBuffer`: An additional buffer time in milliseconds added
    to `keepAliveTimeout` to extend the internal socket timeout.
    See [`server.keepAliveTimeoutBuffer`](#serverkeepalivetimeoutbuffer) for more information.
    **Default:** `1000`.
  - `maxHeaderSize` [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) Optionally overrides the value of [`--max-http-header-size`](13-cli.md#--max-http-header-sizesize) for requests received by this server, i.e.
    the maximum length of request headers in bytes.
    **Default:** 16384 (16 KiB).
  - `noDelay` [`<boolean>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#boolean_type) If set to `true`, it disables the use of Nagle's
    algorithm immediately after a new incoming connection is received.
    **Default:** `true`.
  - `requestTimeout`: Sets the timeout value in milliseconds for receiving
    the entire request from the client.
    See [`server.requestTimeout`](#serverrequesttimeout) for more information.
    **Default:** `300000`.
  - `requireHostHeader` [`<boolean>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#boolean_type) If set to `true`, it forces the server to
    respond with a 400 (Bad Request) status code to any HTTP/1.1
    request message that lacks a Host header
    (as mandated by the specification).
    **Default:** `true`.
  - `ServerResponse` [`<http.ServerResponse>`](27-http.md#class-httpserverresponse) Specifies the `ServerResponse` class
    to be used. Useful for extending the original `ServerResponse`. **Default:**
    `ServerResponse`.
  - `shouldUpgradeCallback(request)` [`<Function>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Function) A callback which receives an
    incoming request and returns a boolean, to control which upgrade attempts
    should be accepted. Accepted upgrades will fire an `'upgrade'` event (or
    their sockets will be destroyed, if no listener is registered) while
    rejected upgrades will fire a `'request'` event like any non-upgrade
    request. This options defaults to
    `() => server.listenerCount('upgrade') > 0`.
  - `uniqueHeaders` [`<Array>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Array) A list of response headers that should be sent only
    once. If the header's value is an array, the items will be joined
    using `;` .
  - `rejectNonStandardBodyWrites` [`<boolean>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#boolean_type) If set to `true`, an error is thrown
    when writing to an HTTP response which does not have a body.
    **Default:** `false`.
  - `optimizeEmptyRequests` [`<boolean>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#boolean_type) If set to `true`, requests without `Content-Length`
    or `Transfer-Encoding` headers (indicating no body) will be initialized with an
    already-ended body stream, so they will never emit any stream events
    (like `'data'` or `'end'`). You can use `req.readableEnded` to detect this case.
    **Default:** `false`.
- `requestListener` [`<Function>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Function)
- Returns: [`<http.Server>`](27-http.md#class-httpserver)

Returns a new instance of [`http.Server`](#class-httpserver).

The `requestListener` is a function which is automatically
added to the [`'request'`](#event-request) event.

*ES module version:*

```js
import http from 'node:http';

// Create a local server to receive data from
const server = http.createServer((req, res) => {
  res.writeHead(200, { 'Content-Type': 'application/json' });
  res.end(JSON.stringify({
    data: 'Hello World!',
  }));
});

server.listen(8000);
```

*CommonJS version:*

```js
const http = require('node:http');

// Create a local server to receive data from
const server = http.createServer((req, res) => {
  res.writeHead(200, { 'Content-Type': 'application/json' });
  res.end(JSON.stringify({
    data: 'Hello World!',
  }));
});

server.listen(8000);
```

*ES module version:*

```js
import http from 'node:http';

// Create a local server to receive data from
const server = http.createServer();

// Listen to the request event
server.on('request', (request, res) => {
  res.writeHead(200, { 'Content-Type': 'application/json' });
  res.end(JSON.stringify({
    data: 'Hello World!',
  }));
});

server.listen(8000);
```

*CommonJS version:*

```js
const http = require('node:http');

// Create a local server to receive data from
const server = http.createServer();

// Listen to the request event
server.on('request', (request, res) => {
  res.writeHead(200, { 'Content-Type': 'application/json' });
  res.end(JSON.stringify({
    data: 'Hello World!',
  }));
});

server.listen(8000);
```

## `http.get(options[, callback])`

## `http.get(url[, options][, callback])`

Added in: v0.3.6

**History**

| Version | Changes |
| --- | --- |
| v10.9.0 | The `url` parameter can now be passed along with a separate `options` object. |
| v7.5.0 | The `options` parameter can be a WHATWG `URL` object. |

- `url` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) | [`<URL>`](59-url.md#the-whatwg-url-api)
- `options` [`<Object>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Object) Accepts the same `options` as
  [`http.request()`](#httprequestoptions-callback), with the method set to GET by default.
- `callback` [`<Function>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Function)
- Returns: [`<http.ClientRequest>`](27-http.md#class-httpclientrequest)

Since most requests are GET requests without bodies, Node.js provides this
convenience method. The only difference between this method and
[`http.request()`](#httprequestoptions-callback) is that it sets the method to GET by default and calls `req.end()`
automatically. The callback must take care to consume the response
data for reasons stated in [`http.ClientRequest`](#class-httpclientrequest) section.

The `callback` is invoked with a single argument that is an instance of
[`http.IncomingMessage`](#class-httpincomingmessage).

JSON fetching example:

```js
http.get('http://localhost:8000/', (res) => {
  const { statusCode } = res;
  const contentType = res.headers['content-type'];

  let error;
  // Any 2xx status code signals a successful response but
  // here we're only checking for 200.
  if (statusCode !== 200) {
    error = new Error('Request Failed.\n' +
                      `Status Code: ${statusCode}`);
  } else if (!/^application\/json/.test(contentType)) {
    error = new Error('Invalid content-type.\n' +
                      `Expected application/json but received ${contentType}`);
  }
  if (error) {
    console.error(error.message);
    // Consume response data to free up memory
    res.resume();
    return;
  }

  res.setEncoding('utf8');
  let rawData = '';
  res.on('data', (chunk) => { rawData += chunk; });
  res.on('end', () => {
    try {
      const parsedData = JSON.parse(rawData);
      console.log(parsedData);
    } catch (e) {
      console.error(e.message);
    }
  });
}).on('error', (e) => {
  console.error(`Got error: ${e.message}`);
});

// Create a local server to receive data from
const server = http.createServer((req, res) => {
  res.writeHead(200, { 'Content-Type': 'application/json' });
  res.end(JSON.stringify({
    data: 'Hello World!',
  }));
});

server.listen(8000);
```

## `http.globalAgent`

Added in: v0.5.9

**History**

| Version | Changes |
| --- | --- |
| v19.0.0 | The agent now uses HTTP Keep-Alive and a 5 second timeout by default. |

- Type: [`<http.Agent>`](27-http.md#class-httpagent)

Global instance of `Agent` which is used as the default for all HTTP client
requests. Diverges from a default `Agent` configuration by having `keepAlive`
enabled and a `timeout` of 5 seconds.

## `http.maxHeaderSize`

Added in: v11.6.0, v10.15.0

- Type: [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type)

Read-only property specifying the maximum allowed size of HTTP headers in bytes.
Defaults to 16 KiB. Configurable using the [`--max-http-header-size`](13-cli.md#--max-http-header-sizesize) CLI
option.

This can be overridden for servers and client requests by passing the
`maxHeaderSize` option.

## `http.request(options[, callback])`

## `http.request(url[, options][, callback])`

Added in: v0.3.6

**History**

| Version | Changes |
| --- | --- |
| v26.3.0 | The `httpValidation` option is supported now. |
| v16.7.0, v14.18.0 | When using a `URL` object parsed username and password will now be properly URI decoded. |
| v15.3.0, v14.17.0 | It is possible to abort a request with an AbortSignal. |
| v13.8.0, v12.15.0, v10.19.0 | The `insecureHTTPParser` option is supported now. |
| v13.3.0 | The `maxHeaderSize` option is supported now. |
| v10.9.0 | The `url` parameter can now be passed along with a separate `options` object. |
| v7.5.0 | The `options` parameter can be a WHATWG `URL` object. |

- `url` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) | [`<URL>`](59-url.md#the-whatwg-url-api)
- `options` [`<Object>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Object)
  - `agent` [`<http.Agent>`](27-http.md#class-httpagent) | [`<boolean>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#boolean_type) Controls [`Agent`](#class-httpagent) behavior. Possible
    values:
    - `undefined` (default): use [`http.globalAgent`](#httpglobalagent) for this host and port.
    - `Agent` object: explicitly use the passed in `Agent`.
    - `false`: causes a new `Agent` with default values to be used.
  - `auth` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) Basic authentication (`'user:password'`) to compute an
    Authorization header.
  - `createConnection` [`<Function>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Function) A function that produces a socket/stream to
    use for the request when the `agent` option is not used. This can be used to
    avoid creating a custom `Agent` class just to override the default
    `createConnection` function. See [`agent.createConnection()`](#agentcreateconnectionoptions-callback) for more
    details. Any [`Duplex`](51-stream.md#class-streamduplex) stream is a valid return value.
  - `defaultPort` [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) Default port for the protocol. **Default:**
    `agent.defaultPort` if an `Agent` is used, else `undefined`.
  - `family` [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) IP address family to use when resolving `host` or
    `hostname`. Valid values are `4` or `6`. When unspecified, both IP v4 and
    v6 will be used.
  - `headers` [`<Object>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Object) | [`<Array>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Array) An object or an array of strings containing request
    headers. The array is in the same format as [`message.rawHeaders`](#messagerawheaders).
  - `hints` [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) Optional [`dns.lookup()` hints](19-dns.md#supported-getaddrinfo-flags).
  - `host` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) A domain name or IP address of the server to issue the
    request to. **Default:** `'localhost'`.
  - `hostname` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) Alias for `host`. To support [`url.parse()`](59-url.md#urlparseurlstring-parsequerystring-slashesdenotehost),
    `hostname` will be used if both `host` and `hostname` are specified.
  - `httpValidation` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) Controls HTTP header value validation strictness
    for outgoing requests. Accepted values are:
    - `'strict'`: Strictest validation; rejects any non-ASCII or control
      characters in header values.
    - `'relaxed'`: Allows a limited set of non-ASCII characters in header
      values, aligning with the
      [Fetch specification](https://fetch.spec.whatwg.org/).
    - `'insecure'`: Disables all header value validation (equivalent to
      `insecureHTTPParser: true`).
      Cannot be used together with `insecureHTTPParser`. **Default:** `'strict'`.
  - `insecureHTTPParser` [`<boolean>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#boolean_type) If set to `true`, it will use an HTTP parser
    with leniency flags enabled. Using the insecure parser should be avoided.
    See [`--insecure-http-parser`](13-cli.md#--insecure-http-parser) for more information.
    **Default:** `false`
  - `joinDuplicateHeaders` [`<boolean>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#boolean_type) It joins the field line values of
    multiple headers in a request with `,`  instead of discarding
    the duplicates. See [`message.headers`](#messageheaders) for more information.
    **Default:** `false`.
  - `localAddress` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) Local interface to bind for network connections.
  - `localPort` [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) Local port to connect from.
  - `lookup` [`<Function>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Function) Custom lookup function. **Default:** [`dns.lookup()`](19-dns.md#dnslookuphostname-options-callback).
  - `maxHeaderSize` [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) Optionally overrides the value of [`--max-http-header-size`](13-cli.md#--max-http-header-sizesize) (the maximum length of response headers in
    bytes) for responses received from the server.
    **Default:** 16384 (16 KiB).
  - `method` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) A string specifying the HTTP request method. **Default:**
    `'GET'`.
  - `path` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) Request path. Should include query string if any.
    E.G. `'/index.html?page=12'`. An exception is thrown when the request path
    contains illegal characters. Currently, only spaces are rejected but that
    may change in the future. **Default:** `'/'`.
    The content in `path` is sent as the [request target](https://datatracker.ietf.org/doc/html/rfc9112#section-3.2) in the HTTP 1.1 message.
    When `path` is an absolute URL, this means the request target in the message in [absolute form](https://datatracker.ietf.org/doc/html/rfc9112#section-3.2.2).
    If the receiving server is a proxy, the server typically forwards the request to the
    destination specified in the request target, and ignores the `Host` header.
    The user needs to make sure that `path`, `host` and the Host headers conform to the
    requirement of the [request target](https://datatracker.ietf.org/doc/html/rfc9112#section-3.2) in the HTTP specification.
    When the receiving server is known to be a proxy because the request is routed through
    [Built-in Proxy Support](#built-in-proxy-support), `http.request` will additionally perform a best-effort
    check to see that the `host` option or `Host` in `headers` agrees with the authority
    in `path` during the initial construction of the request. It gives up rewriting the
    request target for proxying and throws an error if they don't match at request
    construction time, though there won't be checks for later header mutations done by the user.
  - `port` [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) Port of remote server. **Default:** `defaultPort` if set,
    else `80`.
  - `protocol` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) Protocol to use. **Default:** `'http:'`.
  - `setDefaultHeaders` [`<boolean>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#boolean_type): Specifies whether or not to automatically add
    default headers such as `Connection`, `Content-Length`, `Transfer-Encoding`,
    and `Host`. If set to `false` then all necessary headers must be added
    manually. Defaults to `true`.
  - `setHost` [`<boolean>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#boolean_type): Specifies whether or not to automatically add the `Host` header. If provided, this overrides `setDefaultHeaders`. Defaults to
    `true`.
  - `signal` [`<AbortSignal>`](26-globals.md#class-abortsignal): An AbortSignal that may be used to abort an ongoing
    request.
  - `socketPath` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) Unix domain socket. Cannot be used if one of `host`
    or `port` is specified, as those specify a TCP Socket.
  - `timeout` [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type): A number specifying the socket timeout in milliseconds.
    This will set the timeout before the socket is connected.
  - `uniqueHeaders` [`<Array>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Array) A list of request headers that should be sent
    only once. If the header's value is an array, the items will be joined
    using `;` .
- `callback` [`<Function>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Function)
- Returns: [`<http.ClientRequest>`](27-http.md#class-httpclientrequest)

`options` in [`socket.connect()`](38-net.md#socketconnectoptions-connectlistener) are also supported.

Node.js maintains several connections per server to make HTTP requests.
This function allows one to transparently issue requests.

`url` can be a string or a [`URL`](59-url.md#the-whatwg-url-api) object. If `url` is a
string, it is automatically parsed with [`new URL()`](59-url.md#new-urlinput-base). If it is a [`URL`](59-url.md#the-whatwg-url-api)
object, it will be automatically converted to an ordinary `options` object.

If both `url` and `options` are specified, the objects are merged, with the
`options` properties taking precedence.

The optional `callback` parameter will be added as a one-time listener for
the [`'response'`](#event-response) event.

`http.request()` returns an instance of the [`http.ClientRequest`](#class-httpclientrequest)
class. The `ClientRequest` instance is a writable stream. If one needs to
upload a file with a POST request, then write to the `ClientRequest` object.

*ES module version:*

```js
import http from 'node:http';
import { Buffer } from 'node:buffer';

const postData = JSON.stringify({
  'msg': 'Hello World!',
});

const options = {
  hostname: 'www.google.com',
  port: 80,
  path: '/upload',
  method: 'POST',
  headers: {
    'Content-Type': 'application/json',
    'Content-Length': Buffer.byteLength(postData),
  },
};

const req = http.request(options, (res) => {
  console.log(`STATUS: ${res.statusCode}`);
  console.log(`HEADERS: ${JSON.stringify(res.headers)}`);
  res.setEncoding('utf8');
  res.on('data', (chunk) => {
    console.log(`BODY: ${chunk}`);
  });
  res.on('end', () => {
    console.log('No more data in response.');
  });
});

req.on('error', (e) => {
  console.error(`problem with request: ${e.message}`);
});

// Write data to request body
req.write(postData);
req.end();
```

*CommonJS version:*

```js
const http = require('node:http');

const postData = JSON.stringify({
  'msg': 'Hello World!',
});

const options = {
  hostname: 'www.google.com',
  port: 80,
  path: '/upload',
  method: 'POST',
  headers: {
    'Content-Type': 'application/json',
    'Content-Length': Buffer.byteLength(postData),
  },
};

const req = http.request(options, (res) => {
  console.log(`STATUS: ${res.statusCode}`);
  console.log(`HEADERS: ${JSON.stringify(res.headers)}`);
  res.setEncoding('utf8');
  res.on('data', (chunk) => {
    console.log(`BODY: ${chunk}`);
  });
  res.on('end', () => {
    console.log('No more data in response.');
  });
});

req.on('error', (e) => {
  console.error(`problem with request: ${e.message}`);
});

// Write data to request body
req.write(postData);
req.end();
```

In the example `req.end()` was called. With `http.request()` one
must always call `req.end()` to signify the end of the request -
even if there is no data being written to the request body.

If any error is encountered during the request (be that with DNS resolution,
TCP level errors, or actual HTTP parse errors) an `'error'` event is emitted
on the returned request object. As with all `'error'` events, if no listeners
are registered the error will be thrown.

There are a few special headers that should be noted.

- Sending a 'Connection: keep-alive' will notify Node.js that the connection to
  the server should be persisted until the next request.
- Sending a 'Content-Length' header will disable the default chunked encoding.
- Sending an 'Expect' header will immediately send the request headers.
  Usually, when sending 'Expect: 100-continue', both a timeout and a listener
  for the `'continue'` event should be set. See RFC 2616 Section 8.2.3 for more
  information.
- Sending an Authorization header will override using the `auth` option
  to compute basic authentication.

Example using a [`URL`](59-url.md#the-whatwg-url-api) as `options`:

```js
const options = new URL('http://abc:xyz@example.com');

const req = http.request(options, (res) => {
  // ...
});
```

In a successful request, the following events will be emitted in the following
order:

- `'socket'`
- `'response'`
  - `'data'` any number of times, on the `res` object
    (`'data'` will not be emitted at all if the response body is empty, for
    instance, in most redirects)
  - `'end'` on the `res` object
- `'close'`

In the case of a connection error, the following events will be emitted:

- `'socket'`
- `'error'`
- `'close'`

In the case of a premature connection close before the response is received,
the following events will be emitted in the following order:

- `'socket'`
- `'error'` with an error with message `'Error: socket hang up'` and code
  `'ECONNRESET'`
- `'close'`

In the case of a premature connection close after the response is received,
the following events will be emitted in the following order:

- `'socket'`
- `'response'`
  - `'data'` any number of times, on the `res` object
- (connection closed here)
- `'aborted'` on the `res` object
- `'close'`
- `'error'` on the `res` object with an error with message
  `'Error: aborted'` and code `'ECONNRESET'`
- `'close'` on the `res` object

If a socket error (such as a TLS error) causes the premature close, that error
is emitted on the request before the close. The error emitted on the incomplete
response retains the message `'aborted'` and code `'ECONNRESET'`, with the original
socket error available as its `cause`. This also applies when the original socket
error has code `'ECONNRESET'`. If no underlying error is available, the response
error has no `cause` property.

If `req.destroy()` is called before a socket is assigned, the following
events will be emitted in the following order:

- (`req.destroy()` called here)
- `'error'` with an error with message `'Error: socket hang up'` and code
  `'ECONNRESET'`, or the error with which `req.destroy()` was called
- `'close'`

If `req.destroy()` is called before the connection succeeds, the following
events will be emitted in the following order:

- `'socket'`
- (`req.destroy()` called here)
- `'error'` with an error with message `'Error: socket hang up'` and code
  `'ECONNRESET'`, or the error with which `req.destroy()` was called
- `'close'`

If `req.destroy()` is called after the response is received, the following
events will be emitted in the following order:

- `'socket'`
- `'response'`
  - `'data'` any number of times, on the `res` object
- (`req.destroy()` called here)
- `'aborted'` on the `res` object
- `'close'`
- `'error'` on the `res` object with an error with message `'Error: aborted'`
  and code `'ECONNRESET'`. If an error was passed to `req.destroy()`, it is
  available as the response error's `cause`.
- `'close'` on the `res` object

If `req.abort()` is called before a socket is assigned, the following
events will be emitted in the following order:

- (`req.abort()` called here)
- `'abort'`
- `'close'`

If `req.abort()` is called before the connection succeeds, the following
events will be emitted in the following order:

- `'socket'`
- (`req.abort()` called here)
- `'abort'`
- `'error'` with an error with message `'Error: socket hang up'` and code
  `'ECONNRESET'`
- `'close'`

If `req.abort()` is called after the response is received, the following
events will be emitted in the following order:

- `'socket'`
- `'response'`
  - `'data'` any number of times, on the `res` object
- (`req.abort()` called here)
- `'abort'`
- `'aborted'` on the `res` object
- `'error'` on the `res` object with an error with message
  `'Error: aborted'` and code `'ECONNRESET'`.
- `'close'`
- `'close'` on the `res` object

Setting the `timeout` option or using the `setTimeout()` function will
not abort the request or do anything besides add a `'timeout'` event.

Passing an `AbortSignal` and then calling `abort()` on the corresponding
`AbortController` will behave the same way as calling `.destroy()` on the
request. Specifically, the `'error'` event will be emitted with an error with
the message `'AbortError: The operation was aborted'`, the code `'ABORT_ERR'`
and the `cause`, if one was provided.

## `http.isValidHeaderName(name)`

Added in: v26.11.0

- `name` [`<any>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#Data_types)
- Returns: [`<boolean>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#boolean_type)

Returns `true` if `name` is a valid HTTP header name (a non-empty string that
is an HTTP [token](https://datatracker.ietf.org/doc/html/rfc9110#section-5.6.2)), and `false` otherwise. This is the same check that
[`http.validateHeaderName()`](#httpvalidateheadernamename-label) performs, but the result is returned instead of
an error being thrown, so it is suitable for use in hot paths where invalid
input is expected.

HTTP methods are also tokens, so this function can validate them as well.

*ES module version:*

```js
import { isValidHeaderName } from 'node:http';

console.log(isValidHeaderName('content-type')); // true
console.log(isValidHeaderName('X-Request-Id')); // true
console.log(isValidHeaderName('')); // false
console.log(isValidHeaderName('bad header')); // false
console.log(isValidHeaderName(42)); // false
```

*CommonJS version:*

```js
const { isValidHeaderName } = require('node:http');

console.log(isValidHeaderName('content-type')); // true
console.log(isValidHeaderName('X-Request-Id')); // true
console.log(isValidHeaderName('')); // false
console.log(isValidHeaderName('bad header')); // false
console.log(isValidHeaderName(42)); // false
```

## `http.isValidHeaderValue(value[, options])`

Added in: v26.11.0

- `value` [`<any>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#Data_types)
- `options` [`<Object>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Object)
  - `httpValidation` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) Validation strictness, one of `'strict'` or
    `'relaxed'`. These have the same meaning as the `httpValidation` option of
    [`http.createServer()`](#httpcreateserveroptions-requestlistener) and [`http.request()`](#httprequestoptions-callback). **Default:** `'strict'`.
- Returns: [`<boolean>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#boolean_type)

Returns `true` if `value` is a valid HTTP header value, and `false` otherwise.
With the default options this is the same check that
[`http.validateHeaderValue()`](#httpvalidateheadervaluename-value) performs, but the result is returned instead
of an error being thrown.

`undefined` and symbols are never valid header values. Other non-string
values are converted to strings before being checked, as they are when passed
to [`outgoingMessage.setHeader(name, value)`](#outgoingmessagesetheadername-value).

Passing an invalid `options` argument throws.

*ES module version:*

```js
import { isValidHeaderValue } from 'node:http';

console.log(isValidHeaderValue('text/html')); // true
console.log(isValidHeaderValue(123)); // true
console.log(isValidHeaderValue(undefined)); // false
console.log(isValidHeaderValue('a\r\nb')); // false
console.log(isValidHeaderValue('a\x01b')); // false
console.log(isValidHeaderValue('a\x01b', { httpValidation: 'relaxed' })); // true
```

*CommonJS version:*

```js
const { isValidHeaderValue } = require('node:http');

console.log(isValidHeaderValue('text/html')); // true
console.log(isValidHeaderValue(123)); // true
console.log(isValidHeaderValue(undefined)); // false
console.log(isValidHeaderValue('a\r\nb')); // false
console.log(isValidHeaderValue('a\x01b')); // false
console.log(isValidHeaderValue('a\x01b', { httpValidation: 'relaxed' })); // true
```

## `http.validateHeaderName(name[, label])`

Added in: v14.3.0

**History**

| Version | Changes |
| --- | --- |
| v19.5.0, v18.14.0 | The `label` parameter is added. |

- `name` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type)
- `label` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) Label for error message. **Default:** `'Header name'`.

Performs the low-level validations on the provided `name` that are done when
`res.setHeader(name, value)` is called.

Passing illegal value as `name` will result in a [`TypeError`](22-errors.md#class-typeerror) being thrown,
identified by `code: 'ERR_INVALID_HTTP_TOKEN'`.

It is not necessary to use this method before passing headers to an HTTP request
or response. The HTTP module will automatically validate such headers.

Example:

*ES module version:*

```js
import { validateHeaderName } from 'node:http';

try {
  validateHeaderName('');
} catch (err) {
  console.error(err instanceof TypeError); // --> true
  console.error(err.code); // --> 'ERR_INVALID_HTTP_TOKEN'
  console.error(err.message); // --> 'Header name must be a valid HTTP token [""]'
}
```

*CommonJS version:*

```js
const { validateHeaderName } = require('node:http');

try {
  validateHeaderName('');
} catch (err) {
  console.error(err instanceof TypeError); // --> true
  console.error(err.code); // --> 'ERR_INVALID_HTTP_TOKEN'
  console.error(err.message); // --> 'Header name must be a valid HTTP token [""]'
}
```

## `http.validateHeaderValue(name, value)`

Added in: v14.3.0

- `name` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type)
- `value` [`<any>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#Data_types)

Performs the low-level validations on the provided `value` that are done when
`res.setHeader(name, value)` is called.

Passing illegal value as `value` will result in a [`TypeError`](22-errors.md#class-typeerror) being thrown.

- Undefined value error is identified by `code: 'ERR_HTTP_INVALID_HEADER_VALUE'`.
- Invalid value character error is identified by `code: 'ERR_INVALID_CHAR'`.

It is not necessary to use this method before passing headers to an HTTP request
or response. The HTTP module will automatically validate such headers.

Examples:

*ES module version:*

```js
import { validateHeaderValue } from 'node:http';

try {
  validateHeaderValue('x-my-header', undefined);
} catch (err) {
  console.error(err instanceof TypeError); // --> true
  console.error(err.code === 'ERR_HTTP_INVALID_HEADER_VALUE'); // --> true
  console.error(err.message); // --> 'Invalid value "undefined" for header "x-my-header"'
}

try {
  validateHeaderValue('x-my-header', 'oʊmɪɡə');
} catch (err) {
  console.error(err instanceof TypeError); // --> true
  console.error(err.code === 'ERR_INVALID_CHAR'); // --> true
  console.error(err.message); // --> 'Invalid character in header content ["x-my-header"]'
}
```

*CommonJS version:*

```js
const { validateHeaderValue } = require('node:http');

try {
  validateHeaderValue('x-my-header', undefined);
} catch (err) {
  console.error(err instanceof TypeError); // --> true
  console.error(err.code === 'ERR_HTTP_INVALID_HEADER_VALUE'); // --> true
  console.error(err.message); // --> 'Invalid value "undefined" for header "x-my-header"'
}

try {
  validateHeaderValue('x-my-header', 'oʊmɪɡə');
} catch (err) {
  console.error(err instanceof TypeError); // --> true
  console.error(err.code === 'ERR_INVALID_CHAR'); // --> true
  console.error(err.message); // --> 'Invalid character in header content ["x-my-header"]'
}
```

## `http.setMaxIdleHTTPParsers(max)`

Added in: v18.8.0, v16.18.0

- `max` [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) **Default:** `1000`.

Set the maximum number of idle HTTP parsers.

## `http.setGlobalProxyFromEnv([proxyEnv])`

Added in: v25.4.0, v24.14.0

- `proxyEnv` [`<Object>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Object) An object containing proxy configuration. This accepts the
  same options as the `proxyEnv` option accepted by [`Agent`](#class-httpagent). **Default:**
  `process.env`.
- Returns: [`<Function>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Function) A function that restores the original agent and dispatcher
  settings to the state before this `http.setGlobalProxyFromEnv()` is invoked.

Dynamically resets the global configurations to enable built-in proxy support for
`fetch()` and `http.request()`/`https.request()` at runtime, as an alternative
to using the `--use-env-proxy` flag or `NODE_USE_ENV_PROXY` environment variable.
It can also be used to override settings configured from the environment variables.

As this function resets the global configurations, any previously configured
`http.globalAgent`, `https.globalAgent` or undici global dispatcher would be
overridden after this function is invoked. It's recommended to invoke it before any
requests are made and avoid invoking it in the middle of any requests.

See [Built-in Proxy Support](#built-in-proxy-support) for details on proxy URL formats and `NO_PROXY`
syntax.

## `http.websocketMask(source, mask, output, offset, length)`

Added in: v26.12.0

- `source` [`<Buffer>`](07-buffer.md#class-buffer) | [`<TypedArray>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/TypedArray) | [`<DataView>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/DataView) The data to mask.
- `mask` [`<Buffer>`](07-buffer.md#class-buffer) | [`<TypedArray>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/TypedArray) | [`<DataView>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/DataView) The 4-byte masking key.
- `output` [`<Buffer>`](07-buffer.md#class-buffer) | [`<TypedArray>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/TypedArray) | [`<DataView>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/DataView) Where to write the masked data.
- `offset` [`<integer>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) Byte offset in `output` at which to start writing.
- `length` [`<integer>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) Number of bytes of `source` to mask.

XORs the first `length` bytes of `source` with `mask`, repeated, and writes the
result to `output` starting at `offset`: byte `i` of `source` is XORed with
byte `i % 4` of `mask`. This is the masking operation that WebSocket clients
apply to every frame payload they send ([RFC 6455, Section 5.3](https://datatracker.ietf.org/doc/html/rfc6455#section-5.3)), and that
servers undo on every frame they receive. Applying it twice with the same
`mask` restores the original data. `source` is not modified, unless it shares
memory with `output`.

All arguments are treated as raw bytes, whatever the view type. `source` and
`output` may be the same view or overlapping views over the same memory; the
result is the same as if `source` had been copied first.

An error is thrown if `mask` is not exactly 4 bytes long, if `length` is
greater than `source.byteLength`, if `offset + length` is greater than
`output.byteLength`, or if `output` is backed by an immutable `ArrayBuffer`.
Nothing is written in that case. `source` and `mask` may be backed by an
immutable `ArrayBuffer`.

*ES module version:*

```js
import { Buffer } from 'node:buffer';
import { websocketMask, websocketUnmask } from 'node:http';

const key = Buffer.from([0x37, 0xfa, 0x21, 0x3d]);
const payload = Buffer.from('Hello');

// Write a masked copy of the payload after a 6-byte frame header.
const frame = Buffer.alloc(6 + payload.length);
websocketMask(payload, key, frame, 6, payload.length);
console.log(frame.subarray(6));
// Prints: <Buffer 7f 9f 4d 51 58>

const received = frame.subarray(6);
websocketUnmask(received, key);
console.log(received.toString());
// Prints: Hello
```

*CommonJS version:*

```js
const { Buffer } = require('node:buffer');
const { websocketMask, websocketUnmask } = require('node:http');

const key = Buffer.from([0x37, 0xfa, 0x21, 0x3d]);
const payload = Buffer.from('Hello');

// Write a masked copy of the payload after a 6-byte frame header.
const frame = Buffer.alloc(6 + payload.length);
websocketMask(payload, key, frame, 6, payload.length);
console.log(frame.subarray(6));
// Prints: <Buffer 7f 9f 4d 51 58>

const received = frame.subarray(6);
websocketUnmask(received, key);
console.log(received.toString());
// Prints: Hello
```

## `http.websocketUnmask(buffer, mask)`

Added in: v26.12.0

- `buffer` [`<Buffer>`](07-buffer.md#class-buffer) | [`<TypedArray>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/TypedArray) | [`<DataView>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/DataView) The data to unmask, in place.
- `mask` [`<Buffer>`](07-buffer.md#class-buffer) | [`<TypedArray>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/TypedArray) | [`<DataView>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/DataView) The 4-byte masking key.

XORs every byte of `buffer` with `mask`, repeated, in place: byte `i` is XORed
with byte `i % 4` of `mask`. This is equivalent to
`http.websocketMask(buffer, mask, buffer, 0, buffer.byteLength)`, and is
typically used to unmask a received WebSocket frame payload
([RFC 6455, Section 5.3](https://datatracker.ietf.org/doc/html/rfc6455#section-5.3)). See [`http.websocketMask()`](#httpwebsocketmasksource-mask-output-offset-length) for an example.

An error is thrown, and nothing is written, if `mask` is not exactly 4 bytes
long or if `buffer` is backed by an immutable `ArrayBuffer`.

## Class: `WebSocket`

Added in: v22.5.0

A browser-compatible implementation of [`<WebSocket>`](https://developer.mozilla.org/docs/Web/API/WebSocket).

## Built-in Proxy Support

Added in: v24.5.0, v22.21.0

Stability: 1.1 - Active development

When Node.js creates the global agent, if the `NODE_USE_ENV_PROXY` environment variable is
set to `1` or `--use-env-proxy` is enabled, the global agent will be constructed
with `proxyEnv: process.env`, enabling proxy support based on the environment variables.

To enable proxy support dynamically and globally, use [`http.setGlobalProxyFromEnv()`](#httpsetglobalproxyfromenvproxyenv).

Custom agents can also be created with proxy support by passing a
`proxyEnv` option when constructing the agent. The value can be `process.env`
if they just want to inherit the configuration from the environment variables,
or an object with specific setting overriding the environment.

The following properties of the `proxyEnv` are checked to configure proxy
support.

- `HTTP_PROXY` or `http_proxy`: Proxy server URL for HTTP requests. If both are set,
  `http_proxy` takes precedence.
- `HTTPS_PROXY` or `https_proxy`: Proxy server URL for HTTPS requests. If both are set,
  `https_proxy` takes precedence.
- `NO_PROXY` or `no_proxy`: Comma-separated list of hosts to bypass the proxy. If both are set,
  `no_proxy` takes precedence.

If the request is made to a Unix domain socket, the proxy settings will be ignored.

### Proxy security considerations

Built-in proxy support routes outbound requests through an HTTP(S) proxy, often
because a firewall requires one to access external networks. It is not an
anonymity or traffic-hiding feature and does not attempt to hide traffic from
the proxy, the local network, network operators, or authorities that govern the
deployment.

Configure only proxies that are trusted and authorized for the deployment. A
proxy can observe connection metadata; for plain HTTP requests, or when TLS is
terminated or intercepted by the proxy, it can also observe request and response
contents. Node.js does not support treating an untrusted proxy as a privacy
boundary. Deployment operators are responsible for controlling proxy
configuration and for meeting deployment-specific network policy and legal
requirements.

### Proxy URL Format

Proxy URLs can use either HTTP or HTTPS protocols:

- HTTP proxy: `http://proxy.example.com:8080`
- HTTPS proxy: `https://proxy.example.com:8080`
- Proxy with authentication: `http://username:password@proxy.example.com:8080`

### `NO_PROXY` Format

The `NO_PROXY` environment variable supports several formats:

- `*` - Bypass proxy for all hosts
- `example.com` - Exact host name match
- `.example.com` - Domain suffix match (matches `sub.example.com`)
- `*.example.com` - Wildcard domain match
- `192.168.1.100` - Exact IP address match
- `192.168.1.1-192.168.1.100` - IP address range
- `example.com:8080` - Hostname with specific port

Multiple entries should be separated by commas.

### Example

To start a Node.js process with proxy support enabled for all requests sent
through the default global agent, either use the `NODE_USE_ENV_PROXY` environment
variable:

```console
NODE_USE_ENV_PROXY=1 HTTP_PROXY=http://proxy.example.com:8080 NO_PROXY=localhost,127.0.0.1 node client.js
```

Or the `--use-env-proxy` flag.

```console
HTTP_PROXY=http://proxy.example.com:8080 NO_PROXY=localhost,127.0.0.1 node --use-env-proxy client.js
```

To enable proxy support dynamically and globally with `process.env` (the default option of `http.setGlobalProxyFromEnv()`):

*CommonJS version:*

```js
const http = require('node:http');

// Reads proxy-related environment variables from process.env
const restore = http.setGlobalProxyFromEnv();

// Subsequent requests will use the configured proxies from environment variables
http.get('http://www.example.com', (res) => {
  // This request will be proxied if HTTP_PROXY or http_proxy is set
});

fetch('https://www.example.com', (res) => {
  // This request will be proxied if HTTPS_PROXY or https_proxy is set
});

// To restore the original global agent and dispatcher settings, call the returned function.
// restore();
```

*ES module version:*

```js
import http from 'node:http';

// Reads proxy-related environment variables from process.env
http.setGlobalProxyFromEnv();

// Subsequent requests will use the configured proxies from environment variables
http.get('http://www.example.com', (res) => {
  // This request will be proxied if HTTP_PROXY or http_proxy is set
});

fetch('https://www.example.com', (res) => {
  // This request will be proxied if HTTPS_PROXY or https_proxy is set
});

// To restore the original global agent and dispatcher settings, call the returned function.
// restore();
```

To enable proxy support dynamically and globally with custom settings:

*CommonJS version:*

```js
const http = require('node:http');

const restore = http.setGlobalProxyFromEnv({
  http_proxy: 'http://proxy.example.com:8080',
  https_proxy: 'https://proxy.example.com:8443',
  no_proxy: 'localhost,127.0.0.1,.internal.example.com',
});

// Subsequent requests will use the configured proxies
http.get('http://www.example.com', (res) => {
  // This request will be proxied through proxy.example.com:8080
});

fetch('https://www.example.com', (res) => {
  // This request will be proxied through proxy.example.com:8443
});
```

*ES module version:*

```js
import http from 'node:http';

http.setGlobalProxyFromEnv({
  http_proxy: 'http://proxy.example.com:8080',
  https_proxy: 'https://proxy.example.com:8443',
  no_proxy: 'localhost,127.0.0.1,.internal.example.com',
});

// Subsequent requests will use the configured proxies
http.get('http://www.example.com', (res) => {
  // This request will be proxied through proxy.example.com:8080
});

fetch('https://www.example.com', (res) => {
  // This request will be proxied through proxy.example.com:8443
});
```

To create a custom agent with built-in proxy support:

```js
const http = require('node:http');

// Creating a custom agent with custom proxy support.
const agent = new http.Agent({ proxyEnv: { HTTP_PROXY: 'http://proxy.example.com:8080' } });

http.request({
  hostname: 'www.example.com',
  port: 80,
  path: '/',
  agent,
}, (res) => {
  // This request will be proxied through proxy.example.com:8080 using the HTTP protocol.
  console.log(`STATUS: ${res.statusCode}`);
});
```

Alternatively, the following also works:

```js
const http = require('node:http');
// Use lower-cased option name.
const agent1 = new http.Agent({ proxyEnv: { http_proxy: 'http://proxy.example.com:8080' } });
// Use values inherited from the environment variables, if the process is started with
// HTTP_PROXY=http://proxy.example.com:8080 this will use the proxy server specified
// in process.env.HTTP_PROXY.
const agent2 = new http.Agent({ proxyEnv: process.env });
```

[Contents](../README.md) | [Previous: Globals](26-globals.md) | [Next: HTTP/2](28-http2.md)
