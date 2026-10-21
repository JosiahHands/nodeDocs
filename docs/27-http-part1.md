[Contents](../README.md) | [Previous: Globals](26-globals.md) | [Next: HTTP, part 2 of 3](27-http-part2.md)

Source: [https://nodejs.org/docs/latest/api/http.html](https://nodejs.org/docs/latest/api/http.html)

Part 1 of 3 of this section.

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

See [`message.headers`](27-http-part2.md#messageheaders) for details on how duplicate headers are handled.

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
    See [Built-in Proxy Support](27-http-part3.md#built-in-proxy-support) for details. **Default:** `undefined`
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
  - `socket` [`<stream.Duplex>`](51-stream-part2.md#class-streamduplex) The created socket.
- Returns: [`<stream.Duplex>`](51-stream-part2.md#class-streamduplex) The created socket. This is returned by the default
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

- `socket` [`<stream.Duplex>`](51-stream-part2.md#class-streamduplex)
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
[`<stream.Duplex>`](51-stream-part2.md#class-streamduplex).

### `agent.reuseSocket(socket, request)`

Added in: v8.1.0

- `socket` [`<stream.Duplex>`](51-stream-part2.md#class-streamduplex)
- `request` [`<http.ClientRequest>`](27-http-part1.md#class-httpclientrequest)

Called when `socket` is attached to `request` after being persisted because of
the keep-alive options. Default behavior is to:

```js
socket.ref();
```

This method can be overridden by a particular `Agent` subclass.

The `socket` argument can be an instance of [`<net.Socket>`](38-net.md#class-netsocket), a subclass of
[`<stream.Duplex>`](51-stream-part2.md#class-streamduplex).

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

- Extends: [`<http.OutgoingMessage>`](27-http-part2.md#class-httpoutgoingmessage)

This object is created internally and returned from [`http.request()`](27-http-part3.md#httprequestoptions-callback). It
represents an *in-progress* request whose header has already been queued. The
header is still mutable using the [`setHeader(name, value)`](#requestsetheadername-value),
[`getHeader(name)`](#requestgetheadername), [`removeHeader(name)`](#requestremoveheadername) API. The actual header will
be sent along with the first data chunk or when calling [`request.end()`](#requestenddata-encoding-callback).

To get the response, add a listener for [`'response'`](#event-response) to the request object.
[`'response'`](#event-response) will be emitted from the request object when the response
headers have been received. The [`'response'`](#event-response) event is executed with one
argument which is an instance of [`http.IncomingMessage`](27-http-part2.md#class-httpincomingmessage).

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
If [`response.strictContentLength`](27-http-part2.md#responsestrictcontentlength) is set to `true`, mismatching the
`Content-Length` header value will result in an `Error` being thrown,
identified by `code:` [`'ERR_HTTP_CONTENT_LENGTH_MISMATCH'`](22-errors-part2.md#err_http_content_length_mismatch).

`Content-Length` value should be in bytes, not characters. Use
[`Buffer.byteLength()`](07-buffer-part1.md#static-method-bufferbytelengthstring-encoding) to determine the length of the body in bytes.

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

- `response` [`<http.IncomingMessage>`](27-http-part2.md#class-httpincomingmessage)
- `socket` [`<stream.Duplex>`](51-stream-part2.md#class-streamduplex)
- `head` [`<Buffer>`](07-buffer-part1.md#class-buffer)

Emitted each time a server responds to a request with a `CONNECT` method. If
this event is not being listened for, clients receiving a `CONNECT` method will
have their connections closed.

This event is guaranteed to be passed an instance of the [`<net.Socket>`](38-net.md#class-netsocket) class,
a subclass of [`<stream.Duplex>`](51-stream-part2.md#class-streamduplex), unless the user specifies a socket
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

- `response` [`<http.IncomingMessage>`](27-http-part2.md#class-httpincomingmessage)

Emitted when a response is received to this request. This event is emitted only
once.

### Event: `'socket'`

Added in: v0.5.3

- `socket` [`<stream.Duplex>`](51-stream-part2.md#class-streamduplex)

This event is guaranteed to be passed an instance of the [`<net.Socket>`](38-net.md#class-netsocket) class,
a subclass of [`<stream.Duplex>`](51-stream-part2.md#class-streamduplex), unless the user specifies a socket
type other than [`<net.Socket>`](38-net.md#class-netsocket).

### Event: `'timeout'`

Added in: v0.7.8

Emitted when the underlying socket times out from inactivity. This only notifies
that the socket has been idle. The request must be destroyed manually.

See also: [`request.setTimeout()`](#requestsettimeouttimeout-callback).

### Event: `'upgrade'`

Added in: v0.1.94

- `response` [`<http.IncomingMessage>`](27-http-part2.md#class-httpincomingmessage)
- `stream` [`<stream.Duplex>`](51-stream-part2.md#class-streamduplex)
- `head` [`<Buffer>`](07-buffer-part1.md#class-buffer)

Emitted each time a server responds to a request with an upgrade. If this
event is not being listened for and the response status code is 101 Switching
Protocols, clients receiving an upgrade header will have their connections
closed.

This event is guaranteed to be passed an instance of the [`<net.Socket>`](38-net.md#class-netsocket) class,
a subclass of [`<stream.Duplex>`](51-stream-part2.md#class-streamduplex), unless the user specifies a socket
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

- Type: [`<stream.Duplex>`](51-stream-part2.md#class-streamduplex)

See [`request.socket`](#requestsocket).

### `request.cork()`

Added in: v13.2.0, v12.16.0

See [`writable.cork()`](51-stream-part1.md#writablecork).

### `request.end([data[, encoding]][, callback])`

Added in: v0.1.90

**History**

| Version | Changes |
| --- | --- |
| v15.0.0 | The `data` parameter can now be a `Uint8Array`. |
| v10.0.0 | This method now returns a reference to `ClientRequest`. |

- `data` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) | [`<Buffer>`](07-buffer-part1.md#class-buffer) | [`<Uint8Array>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Uint8Array)
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

See [`writable.destroy()`](51-stream-part1.md#writabledestroyerror) for further details.

#### `request.destroyed`

Added in: v14.1.0, v13.14.0

- Type: [`<boolean>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#boolean_type)

Is `true` after [`request.destroy()`](#requestdestroyerror) has been called.

See [`writable.destroyed`](51-stream-part1.md#writabledestroyed) for further details.

### `request.finished`

Added in: v0.0.1Deprecated in: v13.4.0, v12.16.0

Stability: 0 - Deprecated. Use [`request.writableEnded`](#requestwritableended).

- Type: [`<boolean>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#boolean_type)

The `request.finished` property will be `true` if [`request.end()`](#requestenddata-encoding-callback)
has been called. `request.end()` will automatically be called if the
request was initiated via [`http.get()`](27-http-part3.md#httpgetoptions-callback).

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
[`server.keepAliveTimeoutBuffer`](27-http-part2.md#serverkeepalivetimeoutbuffer) to `0` so the internal socket timeout
matches [`server.keepAliveTimeout`](27-http-part2.md#serverkeepalivetimeout).

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
- Returns: [`<http.ClientRequest>`](27-http-part1.md#class-httpclientrequest)

Once a socket is assigned to this request and is connected
[`socket.setTimeout()`](38-net.md#socketsettimeouttimeout-callback) will be called.

### `request.socket`

Added in: v0.3.0

- Type: [`<stream.Duplex>`](51-stream-part2.md#class-streamduplex)

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
a subclass of [`<stream.Duplex>`](51-stream-part2.md#class-streamduplex), unless the user specified a socket
type other than [`<net.Socket>`](38-net.md#class-netsocket).

### `request.uncork()`

Added in: v13.2.0, v12.16.0

See [`writable.uncork()`](51-stream-part1.md#writableuncork).

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

- `chunk` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) | [`<Buffer>`](07-buffer-part1.md#class-buffer) | [`<Uint8Array>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Uint8Array)
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

- `request` [`<http.IncomingMessage>`](27-http-part2.md#class-httpincomingmessage)
- `response` [`<http.ServerResponse>`](27-http-part2.md#class-httpserverresponse)

Emitted each time a request with an HTTP `Expect: 100-continue` is received.
If this event is not listened for, the server will automatically respond
with a `100 Continue` as appropriate.

Handling this event involves calling [`response.writeContinue()`](27-http-part2.md#responsewritecontinue) if the
client should continue to send the request body, or generating an appropriate
HTTP response (e.g. 400 Bad Request) if the client should not continue to send
the request body.

When this event is emitted and handled, the [`'request'`](#event-request) event will
not be emitted.

### Event: `'checkExpectation'`

Added in: v5.5.0

- `request` [`<http.IncomingMessage>`](27-http-part2.md#class-httpincomingmessage)
- `response` [`<http.ServerResponse>`](27-http-part2.md#class-httpserverresponse)

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
- `socket` [`<stream.Duplex>`](51-stream-part2.md#class-streamduplex)

If a client connection emits an `'error'` event, it will be forwarded here.
Listener of this event is responsible for closing/destroying the underlying
socket. For example, one may wish to more gracefully close the socket with a
custom HTTP response instead of abruptly severing the connection. The socket
**must be closed or destroyed** before the listener ends.

This event is guaranteed to be passed an instance of the [`<net.Socket>`](38-net.md#class-netsocket) class,
a subclass of [`<stream.Duplex>`](51-stream-part2.md#class-streamduplex), unless the user specifies a socket
type other than [`<net.Socket>`](38-net.md#class-netsocket).

Default behavior is to try close the socket with an HTTP '400 Bad Request',
or an HTTP '431 Request Header Fields Too Large' in the case of an
[`HPE_HEADER_OVERFLOW`](22-errors-part4.md#hpe_header_overflow) error. If the socket is not writable or headers
of the current attached [`http.ServerResponse`](27-http-part2.md#class-httpserverresponse) has been sent, it is
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

- `request` [`<http.IncomingMessage>`](27-http-part2.md#class-httpincomingmessage) Arguments for the HTTP request, as it is in
  the [`'request'`](#event-request) event
- `socket` [`<stream.Duplex>`](51-stream-part2.md#class-streamduplex) Network socket between the server and client
- `head` [`<Buffer>`](07-buffer-part1.md#class-buffer) The first packet of the tunneling stream (may be empty)

Emitted each time a client requests an HTTP `CONNECT` method. If this event is
not listened for, then clients requesting a `CONNECT` method will have their
connections closed.

This event is guaranteed to be passed an instance of the [`<net.Socket>`](38-net.md#class-netsocket) class,
a subclass of [`<stream.Duplex>`](51-stream-part2.md#class-streamduplex), unless the user specifies a socket
type other than [`<net.Socket>`](38-net.md#class-netsocket).

After this event is emitted, the request's socket will not have a `'data'`
event listener, meaning it will need to be bound in order to handle data
sent to the server on that socket.

### Event: `'connection'`

Added in: v0.1.0

- `socket` [`<stream.Duplex>`](51-stream-part2.md#class-streamduplex)

This event is emitted when a new TCP stream is established. `socket` is
typically an object of type [`net.Socket`](38-net.md#class-netsocket). Usually users will not want to
access this event. In particular, the socket will not emit `'readable'` events
because of how the protocol parser attaches to the socket. The `socket` can
also be accessed at `request.socket`.

This event can also be explicitly emitted by users to inject connections
into the HTTP server. In that case, any [`Duplex`](51-stream-part2.md#class-streamduplex) stream can be passed.

If `socket.setTimeout()` is called here, the timeout will be replaced with
`server.keepAliveTimeout` when the socket has served a request (if
`server.keepAliveTimeout` is non-zero).

This event is guaranteed to be passed an instance of the [`<net.Socket>`](38-net.md#class-netsocket) class,
a subclass of [`<stream.Duplex>`](51-stream-part2.md#class-streamduplex), unless the user specifies a socket
type other than [`<net.Socket>`](38-net.md#class-netsocket).

### Event: `'dropRequest'`

Added in: v18.7.0, v16.17.0

- `request` [`<http.IncomingMessage>`](27-http-part2.md#class-httpincomingmessage) Arguments for the HTTP request, as it is in
  the [`'request'`](#event-request) event
- `socket` [`<stream.Duplex>`](51-stream-part2.md#class-streamduplex) Network socket between the server and client

When the number of requests on a socket reaches the threshold of
`server.maxRequestsPerSocket`, the server will drop new requests
and emit `'dropRequest'` event instead, then send `503` to client.

### Event: `'request'`

Added in: v0.1.0

- `request` [`<http.IncomingMessage>`](27-http-part2.md#class-httpincomingmessage)
- `response` [`<http.ServerResponse>`](27-http-part2.md#class-httpserverresponse)

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

- `request` [`<http.IncomingMessage>`](27-http-part2.md#class-httpincomingmessage) Arguments for the HTTP request, as it is in
  the [`'request'`](#event-request) event
- `stream` [`<stream.Duplex>`](51-stream-part2.md#class-streamduplex) The upgraded stream between the server and client
- `head` [`<Buffer>`](07-buffer-part1.md#class-buffer) The first packet of the upgraded stream (may be empty)

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

[Contents](../README.md) | [Previous: Globals](26-globals.md) | [Next: HTTP, part 2 of 3](27-http-part2.md)
