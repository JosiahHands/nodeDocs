[Contents](../README.md) | [Previous: HTTP, part 2 of 3](27-http-part2.md) | [Next: HTTP/2, part 1 of 4](28-http2-part1.md)

Source: [https://nodejs.org/docs/latest/api/http.html](https://nodejs.org/docs/latest/api/http.html)

Part 3 of 3 of this section.

# HTTP (part 3 of 3)

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
    See [`server.headersTimeout`](27-http-part2.md#serverheaderstimeout) for more information.
    **Default:** `60000`.
  - `highWaterMark` [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) Optionally overrides all `socket`s'
    `readableHighWaterMark` and `writableHighWaterMark`. This affects
    `highWaterMark` property of both `IncomingMessage` and `ServerResponse`.
    **Default:** See [`stream.getDefaultHighWaterMark()`](51-stream-part3.md#streamgetdefaulthighwatermarkobjectmode).
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
  - `IncomingMessage` [`<http.IncomingMessage>`](27-http-part2.md#class-httpincomingmessage) Specifies the `IncomingMessage`
    class to be used. Useful for extending the original `IncomingMessage`.
    **Default:** `IncomingMessage`.
  - `joinDuplicateHeaders` [`<boolean>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#boolean_type) If set to `true`, this option allows
    joining the field line values of multiple headers in a request with
    a comma (`,` ) instead of discarding the duplicates.
    For more information, refer to [`message.headers`](27-http-part2.md#messageheaders).
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
    See [`server.keepAliveTimeout`](27-http-part2.md#serverkeepalivetimeout) for more information.
    **Default:** `65000`.
  - `keepAliveTimeoutBuffer`: An additional buffer time in milliseconds added
    to `keepAliveTimeout` to extend the internal socket timeout.
    See [`server.keepAliveTimeoutBuffer`](27-http-part2.md#serverkeepalivetimeoutbuffer) for more information.
    **Default:** `1000`.
  - `maxHeaderSize` [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) Optionally overrides the value of [`--max-http-header-size`](13-cli.md#--max-http-header-sizesize) for requests received by this server, i.e.
    the maximum length of request headers in bytes.
    **Default:** 16384 (16 KiB).
  - `noDelay` [`<boolean>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#boolean_type) If set to `true`, it disables the use of Nagle's
    algorithm immediately after a new incoming connection is received.
    **Default:** `true`.
  - `requestTimeout`: Sets the timeout value in milliseconds for receiving
    the entire request from the client.
    See [`server.requestTimeout`](27-http-part2.md#serverrequesttimeout) for more information.
    **Default:** `300000`.
  - `requireHostHeader` [`<boolean>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#boolean_type) If set to `true`, it forces the server to
    respond with a 400 (Bad Request) status code to any HTTP/1.1
    request message that lacks a Host header
    (as mandated by the specification).
    **Default:** `true`.
  - `ServerResponse` [`<http.ServerResponse>`](27-http-part2.md#class-httpserverresponse) Specifies the `ServerResponse` class
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
- Returns: [`<http.Server>`](27-http-part1.md#class-httpserver)

Returns a new instance of [`http.Server`](27-http-part1.md#class-httpserver).

The `requestListener` is a function which is automatically
added to the [`'request'`](27-http-part1.md#event-request) event.

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
- Returns: [`<http.ClientRequest>`](27-http-part1.md#class-httpclientrequest)

Since most requests are GET requests without bodies, Node.js provides this
convenience method. The only difference between this method and
[`http.request()`](#httprequestoptions-callback) is that it sets the method to GET by default and calls `req.end()`
automatically. The callback must take care to consume the response
data for reasons stated in [`http.ClientRequest`](27-http-part1.md#class-httpclientrequest) section.

The `callback` is invoked with a single argument that is an instance of
[`http.IncomingMessage`](27-http-part2.md#class-httpincomingmessage).

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

- Type: [`<http.Agent>`](27-http-part1.md#class-httpagent)

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
  - `agent` [`<http.Agent>`](27-http-part1.md#class-httpagent) | [`<boolean>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#boolean_type) Controls [`Agent`](27-http-part1.md#class-httpagent) behavior. Possible
    values:
    - `undefined` (default): use [`http.globalAgent`](#httpglobalagent) for this host and port.
    - `Agent` object: explicitly use the passed in `Agent`.
    - `false`: causes a new `Agent` with default values to be used.
  - `auth` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) Basic authentication (`'user:password'`) to compute an
    Authorization header.
  - `createConnection` [`<Function>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Function) A function that produces a socket/stream to
    use for the request when the `agent` option is not used. This can be used to
    avoid creating a custom `Agent` class just to override the default
    `createConnection` function. See [`agent.createConnection()`](27-http-part1.md#agentcreateconnectionoptions-callback) for more
    details. Any [`Duplex`](51-stream-part2.md#class-streamduplex) stream is a valid return value.
  - `defaultPort` [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) Default port for the protocol. **Default:**
    `agent.defaultPort` if an `Agent` is used, else `undefined`.
  - `family` [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) IP address family to use when resolving `host` or
    `hostname`. Valid values are `4` or `6`. When unspecified, both IP v4 and
    v6 will be used.
  - `headers` [`<Object>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Object) | [`<Array>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Array) An object or an array of strings containing request
    headers. The array is in the same format as [`message.rawHeaders`](27-http-part2.md#messagerawheaders).
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
    the duplicates. See [`message.headers`](27-http-part2.md#messageheaders) for more information.
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
- Returns: [`<http.ClientRequest>`](27-http-part1.md#class-httpclientrequest)

`options` in [`socket.connect()`](38-net.md#socketconnectoptions-connectlistener) are also supported.

Node.js maintains several connections per server to make HTTP requests.
This function allows one to transparently issue requests.

`url` can be a string or a [`URL`](59-url.md#the-whatwg-url-api) object. If `url` is a
string, it is automatically parsed with [`new URL()`](59-url.md#new-urlinput-base). If it is a [`URL`](59-url.md#the-whatwg-url-api)
object, it will be automatically converted to an ordinary `options` object.

If both `url` and `options` are specified, the objects are merged, with the
`options` properties taking precedence.

The optional `callback` parameter will be added as a one-time listener for
the [`'response'`](27-http-part1.md#event-response) event.

`http.request()` returns an instance of the [`http.ClientRequest`](27-http-part1.md#class-httpclientrequest)
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
to [`outgoingMessage.setHeader(name, value)`](27-http-part2.md#outgoingmessagesetheadername-value).

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

Passing illegal value as `name` will result in a [`TypeError`](22-errors-part1.md#class-typeerror) being thrown,
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

Passing illegal value as `value` will result in a [`TypeError`](22-errors-part1.md#class-typeerror) being thrown.

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
  same options as the `proxyEnv` option accepted by [`Agent`](27-http-part1.md#class-httpagent). **Default:**
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

- `source` [`<Buffer>`](07-buffer-part1.md#class-buffer) | [`<TypedArray>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/TypedArray) | [`<DataView>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/DataView) The data to mask.
- `mask` [`<Buffer>`](07-buffer-part1.md#class-buffer) | [`<TypedArray>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/TypedArray) | [`<DataView>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/DataView) The 4-byte masking key.
- `output` [`<Buffer>`](07-buffer-part1.md#class-buffer) | [`<TypedArray>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/TypedArray) | [`<DataView>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/DataView) Where to write the masked data.
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

- `buffer` [`<Buffer>`](07-buffer-part1.md#class-buffer) | [`<TypedArray>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/TypedArray) | [`<DataView>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/DataView) The data to unmask, in place.
- `mask` [`<Buffer>`](07-buffer-part1.md#class-buffer) | [`<TypedArray>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/TypedArray) | [`<DataView>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/DataView) The 4-byte masking key.

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

[Contents](../README.md) | [Previous: HTTP, part 2 of 3](27-http-part2.md) | [Next: HTTP/2, part 1 of 4](28-http2-part1.md)
