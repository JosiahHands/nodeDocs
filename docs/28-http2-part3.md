[Contents](../README.md) | [Previous: HTTP/2, part 2 of 4](28-http2-part2.md) | [Next: HTTP/2, part 4 of 4](28-http2-part4.md)

Source: [https://nodejs.org/docs/latest/api/http2.html](https://nodejs.org/docs/latest/api/http2.html)

Part 3 of 4 of this section.

# HTTP/2 (part 3 of 4)

Continues: Core API.

### `http2.constants`

Added in: v8.4.0

#### Header name constants

The `HTTP2_HEADER_*` constants provide names for HTTP/2 pseudo-headers and
known HTTP header names. Using these string constants is optional. For example,
`http2.constants.HTTP2_HEADER_CONTENT_TYPE` is equal to `'content-type'`.
For APIs that accept regular header names,
`http2.constants.HTTP2_HEADER_CONTENT_TYPE`, `'content-type'`, and
`'Content-Type'` have the same effect; Node.js serializes the name in
lower-case.

Regular header constants can be used with the compatibility API wherever the
corresponding literal header name is accepted. In compatibility API request
handlers, prefer `request.method`, `request.authority`, `request.scheme`, and
`request.url` for the corresponding pseudo-headers. Other incoming
pseudo-headers remain available through `request.headers`. Set response status
through `response.statusCode` or the `statusCode` argument to
`response.writeHead()`. Passing `HTTP2_HEADER_STATUS` (`':status'`) to
`response.setHeader()` or in `response.writeHead()`'s headers object throws
`ERR_HTTP2_PSEUDOHEADER_NOT_ALLOWED`. `HTTP2_HEADER_PROTOCOL` is a request
pseudo-header and cannot be sent in a response.

Incoming header object keys are lower-case, so use a constant or a lower-case
literal when accessing them as object properties. Using a constant does not
change header validation, and the availability of a constant does not imply
that the header is valid in every HTTP/2 context. See [HTTP/2 Headers Object](#headers-object)
and [Invalid character handling in header names and values](#invalid-character-handling-in-header-names-and-values) for details about
header casing and validation.

##### Pseudo-header constants

`HTTP2_HEADER_METHOD`, `HTTP2_HEADER_AUTHORITY`, `HTTP2_HEADER_SCHEME`, and
`HTTP2_HEADER_PATH` identify request pseudo-headers. `HTTP2_HEADER_STATUS`
identifies the response pseudo-header. `HTTP2_HEADER_PROTOCOL` identifies the
extended `CONNECT` request pseudo-header. Pseudo-headers are not permitted in
trailers.

| Constant | Value |
| --- | --- |
| `http2.constants.HTTP2_HEADER_STATUS` | `':status'` |
| `http2.constants.HTTP2_HEADER_METHOD` | `':method'` |
| `http2.constants.HTTP2_HEADER_AUTHORITY` | `':authority'` |
| `http2.constants.HTTP2_HEADER_SCHEME` | `':scheme'` |
| `http2.constants.HTTP2_HEADER_PATH` | `':path'` |
| `http2.constants.HTTP2_HEADER_PROTOCOL` | `':protocol'` |

##### Regular header constants

The `HTTP2_HEADER_CONNECTION`, `HTTP2_HEADER_UPGRADE`,
`HTTP2_HEADER_HTTP2_SETTINGS`, `HTTP2_HEADER_KEEP_ALIVE`,
`HTTP2_HEADER_PROXY_CONNECTION`, and `HTTP2_HEADER_TRANSFER_ENCODING`
constants identify connection-specific headers that HTTP/2 does not permit.
`HTTP2_HEADER_TE` is permitted only when its value is `'trailers'`.

| Constant | Value |
| --- | --- |
| `http2.constants.HTTP2_HEADER_ACCEPT_ENCODING` | `'accept-encoding'` |
| `http2.constants.HTTP2_HEADER_ACCEPT_LANGUAGE` | `'accept-language'` |
| `http2.constants.HTTP2_HEADER_ACCEPT_RANGES` | `'accept-ranges'` |
| `http2.constants.HTTP2_HEADER_ACCEPT` | `'accept'` |
| `http2.constants.HTTP2_HEADER_ACCESS_CONTROL_ALLOW_CREDENTIALS` | `'access-control-allow-credentials'` |
| `http2.constants.HTTP2_HEADER_ACCESS_CONTROL_ALLOW_HEADERS` | `'access-control-allow-headers'` |
| `http2.constants.HTTP2_HEADER_ACCESS_CONTROL_ALLOW_METHODS` | `'access-control-allow-methods'` |
| `http2.constants.HTTP2_HEADER_ACCESS_CONTROL_ALLOW_ORIGIN` | `'access-control-allow-origin'` |
| `http2.constants.HTTP2_HEADER_ACCESS_CONTROL_EXPOSE_HEADERS` | `'access-control-expose-headers'` |
| `http2.constants.HTTP2_HEADER_ACCESS_CONTROL_REQUEST_HEADERS` | `'access-control-request-headers'` |
| `http2.constants.HTTP2_HEADER_ACCESS_CONTROL_REQUEST_METHOD` | `'access-control-request-method'` |
| `http2.constants.HTTP2_HEADER_AGE` | `'age'` |
| `http2.constants.HTTP2_HEADER_AUTHORIZATION` | `'authorization'` |
| `http2.constants.HTTP2_HEADER_CACHE_CONTROL` | `'cache-control'` |
| `http2.constants.HTTP2_HEADER_CONNECTION` | `'connection'` |
| `http2.constants.HTTP2_HEADER_CONTENT_DISPOSITION` | `'content-disposition'` |
| `http2.constants.HTTP2_HEADER_CONTENT_ENCODING` | `'content-encoding'` |
| `http2.constants.HTTP2_HEADER_CONTENT_LENGTH` | `'content-length'` |
| `http2.constants.HTTP2_HEADER_CONTENT_TYPE` | `'content-type'` |
| `http2.constants.HTTP2_HEADER_COOKIE` | `'cookie'` |
| `http2.constants.HTTP2_HEADER_DATE` | `'date'` |
| `http2.constants.HTTP2_HEADER_ETAG` | `'etag'` |
| `http2.constants.HTTP2_HEADER_FORWARDED` | `'forwarded'` |
| `http2.constants.HTTP2_HEADER_HOST` | `'host'` |
| `http2.constants.HTTP2_HEADER_IF_MODIFIED_SINCE` | `'if-modified-since'` |
| `http2.constants.HTTP2_HEADER_IF_NONE_MATCH` | `'if-none-match'` |
| `http2.constants.HTTP2_HEADER_IF_RANGE` | `'if-range'` |
| `http2.constants.HTTP2_HEADER_LAST_MODIFIED` | `'last-modified'` |
| `http2.constants.HTTP2_HEADER_LINK` | `'link'` |
| `http2.constants.HTTP2_HEADER_LOCATION` | `'location'` |
| `http2.constants.HTTP2_HEADER_RANGE` | `'range'` |
| `http2.constants.HTTP2_HEADER_REFERER` | `'referer'` |
| `http2.constants.HTTP2_HEADER_SERVER` | `'server'` |
| `http2.constants.HTTP2_HEADER_SET_COOKIE` | `'set-cookie'` |
| `http2.constants.HTTP2_HEADER_STRICT_TRANSPORT_SECURITY` | `'strict-transport-security'` |
| `http2.constants.HTTP2_HEADER_TRANSFER_ENCODING` | `'transfer-encoding'` |
| `http2.constants.HTTP2_HEADER_TE` | `'te'` |
| `http2.constants.HTTP2_HEADER_UPGRADE_INSECURE_REQUESTS` | `'upgrade-insecure-requests'` |
| `http2.constants.HTTP2_HEADER_UPGRADE` | `'upgrade'` |
| `http2.constants.HTTP2_HEADER_USER_AGENT` | `'user-agent'` |
| `http2.constants.HTTP2_HEADER_VARY` | `'vary'` |
| `http2.constants.HTTP2_HEADER_X_CONTENT_TYPE_OPTIONS` | `'x-content-type-options'` |
| `http2.constants.HTTP2_HEADER_X_FRAME_OPTIONS` | `'x-frame-options'` |
| `http2.constants.HTTP2_HEADER_KEEP_ALIVE` | `'keep-alive'` |
| `http2.constants.HTTP2_HEADER_PROXY_CONNECTION` | `'proxy-connection'` |
| `http2.constants.HTTP2_HEADER_X_XSS_PROTECTION` | `'x-xss-protection'` |
| `http2.constants.HTTP2_HEADER_ALT_SVC` | `'alt-svc'` |
| `http2.constants.HTTP2_HEADER_CONTENT_SECURITY_POLICY` | `'content-security-policy'` |
| `http2.constants.HTTP2_HEADER_EARLY_DATA` | `'early-data'` |
| `http2.constants.HTTP2_HEADER_EXPECT_CT` | `'expect-ct'` |
| `http2.constants.HTTP2_HEADER_ORIGIN` | `'origin'` |
| `http2.constants.HTTP2_HEADER_PURPOSE` | `'purpose'` |
| `http2.constants.HTTP2_HEADER_TIMING_ALLOW_ORIGIN` | `'timing-allow-origin'` |
| `http2.constants.HTTP2_HEADER_X_FORWARDED_FOR` | `'x-forwarded-for'` |
| `http2.constants.HTTP2_HEADER_PRIORITY` | `'priority'` |
| `http2.constants.HTTP2_HEADER_ACCEPT_CHARSET` | `'accept-charset'` |
| `http2.constants.HTTP2_HEADER_ACCESS_CONTROL_MAX_AGE` | `'access-control-max-age'` |
| `http2.constants.HTTP2_HEADER_ALLOW` | `'allow'` |
| `http2.constants.HTTP2_HEADER_CONTENT_LANGUAGE` | `'content-language'` |
| `http2.constants.HTTP2_HEADER_CONTENT_LOCATION` | `'content-location'` |
| `http2.constants.HTTP2_HEADER_CONTENT_MD5` | `'content-md5'` |
| `http2.constants.HTTP2_HEADER_CONTENT_RANGE` | `'content-range'` |
| `http2.constants.HTTP2_HEADER_DNT` | `'dnt'` |
| `http2.constants.HTTP2_HEADER_EXPECT` | `'expect'` |
| `http2.constants.HTTP2_HEADER_EXPIRES` | `'expires'` |
| `http2.constants.HTTP2_HEADER_FROM` | `'from'` |
| `http2.constants.HTTP2_HEADER_IF_MATCH` | `'if-match'` |
| `http2.constants.HTTP2_HEADER_IF_UNMODIFIED_SINCE` | `'if-unmodified-since'` |
| `http2.constants.HTTP2_HEADER_MAX_FORWARDS` | `'max-forwards'` |
| `http2.constants.HTTP2_HEADER_PREFER` | `'prefer'` |
| `http2.constants.HTTP2_HEADER_PROXY_AUTHENTICATE` | `'proxy-authenticate'` |
| `http2.constants.HTTP2_HEADER_PROXY_AUTHORIZATION` | `'proxy-authorization'` |
| `http2.constants.HTTP2_HEADER_REFRESH` | `'refresh'` |
| `http2.constants.HTTP2_HEADER_RETRY_AFTER` | `'retry-after'` |
| `http2.constants.HTTP2_HEADER_TRAILER` | `'trailer'` |
| `http2.constants.HTTP2_HEADER_TK` | `'tk'` |
| `http2.constants.HTTP2_HEADER_VIA` | `'via'` |
| `http2.constants.HTTP2_HEADER_WARNING` | `'warning'` |
| `http2.constants.HTTP2_HEADER_WWW_AUTHENTICATE` | `'www-authenticate'` |
| `http2.constants.HTTP2_HEADER_HTTP2_SETTINGS` | `'http2-settings'` |

#### Error codes for `RST_STREAM` and `GOAWAY`

| Value | Name | Constant |
| --- | --- | --- |
| `0x00` | No Error | `http2.constants.NGHTTP2_NO_ERROR` |
| `0x01` | Protocol Error | `http2.constants.NGHTTP2_PROTOCOL_ERROR` |
| `0x02` | Internal Error | `http2.constants.NGHTTP2_INTERNAL_ERROR` |
| `0x03` | Flow Control Error | `http2.constants.NGHTTP2_FLOW_CONTROL_ERROR` |
| `0x04` | Settings Timeout | `http2.constants.NGHTTP2_SETTINGS_TIMEOUT` |
| `0x05` | Stream Closed | `http2.constants.NGHTTP2_STREAM_CLOSED` |
| `0x06` | Frame Size Error | `http2.constants.NGHTTP2_FRAME_SIZE_ERROR` |
| `0x07` | Refused Stream | `http2.constants.NGHTTP2_REFUSED_STREAM` |
| `0x08` | Cancel | `http2.constants.NGHTTP2_CANCEL` |
| `0x09` | Compression Error | `http2.constants.NGHTTP2_COMPRESSION_ERROR` |
| `0x0a` | Connect Error | `http2.constants.NGHTTP2_CONNECT_ERROR` |
| `0x0b` | Enhance Your Calm | `http2.constants.NGHTTP2_ENHANCE_YOUR_CALM` |
| `0x0c` | Inadequate Security | `http2.constants.NGHTTP2_INADEQUATE_SECURITY` |
| `0x0d` | HTTP/1.1 Required | `http2.constants.NGHTTP2_HTTP_1_1_REQUIRED` |

The `'timeout'` event is emitted when there is no activity on the Server for
a given number of milliseconds set using `http2server.setTimeout()`.

### `http2.getDefaultSettings()`

Added in: v8.4.0

- Returns: [`<HTTP/2 Settings Object>`](28-http2-part3.md#settings-object)

Returns an object containing the default settings for an `Http2Session`
instance. This method returns a new object instance every time it is called
so instances returned may be safely modified for use.

### `http2.getPackedSettings([settings])`

Added in: v8.4.0

- `settings` [`<HTTP/2 Settings Object>`](28-http2-part3.md#settings-object)
- Returns: [`<Buffer>`](07-buffer-part1.md#class-buffer)

Returns a `Buffer` instance containing serialized representation of the given
HTTP/2 settings as specified in the [HTTP/2](https://tools.ietf.org/html/rfc7540) specification. This is intended
for use with the `HTTP2-Settings` header field.

*ES module version:*

```js
import { getPackedSettings } from 'node:http2';

const packed = getPackedSettings({ enablePush: false });

console.log(packed.toString('base64'));
// Prints: AAIAAAAA
```

*CommonJS version:*

```js
const http2 = require('node:http2');

const packed = http2.getPackedSettings({ enablePush: false });

console.log(packed.toString('base64'));
// Prints: AAIAAAAA
```

### `http2.getUnpackedSettings(buf)`

Added in: v8.4.0

- `buf` [`<Buffer>`](07-buffer-part1.md#class-buffer) | [`<TypedArray>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/TypedArray) The packed settings.
- Returns: [`<HTTP/2 Settings Object>`](28-http2-part3.md#settings-object)

Returns a [HTTP/2 Settings Object](#settings-object) containing the deserialized settings from
the given `Buffer` as generated by `http2.getPackedSettings()`.

### `http2.performServerHandshake(socket[, options])`

Added in: v21.7.0, v20.12.0

- `socket` [`<stream.Duplex>`](51-stream-part2.md#class-streamduplex)
- `options` [`<Object>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Object) Any [`http2.createServer()`](28-http2-part2.md#http2createserveroptions-onrequesthandler) option can be provided.
- Returns: [`<ServerHttp2Session>`](28-http2-part1.md#class-serverhttp2session)

Create an HTTP/2 server session from an existing socket.

### `http2.sensitiveHeaders`

Added in: v15.0.0, v14.18.0

- Type: [`<symbol>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#symbol_type)

This symbol can be set as a property on the HTTP/2 headers object with an array
value in order to provide a list of headers considered sensitive.
See [Sensitive headers](#sensitive-headers) for more details.

### Headers object

Headers are represented as own-properties on JavaScript objects. The property
keys will be serialized to lower-case. Property values should be strings (if
they are not they will be coerced to strings) or an `Array` of strings (in order
to send more than one value per header field).

```js
const headers = {
  ':status': '200',
  'content-type': 'text-plain',
  'ABC': ['has', 'more', 'than', 'one', 'value'],
};

stream.respond(headers);
```

Header objects passed to callback functions will have a `null` prototype. This
means that normal JavaScript object methods such as
`Object.prototype.toString()` and `Object.prototype.hasOwnProperty()` will
not work.

For incoming headers:

- The `:status` header is converted to `number`.
- Duplicates of `:status`, `:method`, `:authority`, `:scheme`, `:path`,
  `:protocol`, `age`, `authorization`, `access-control-allow-credentials`,
  `access-control-max-age`, `access-control-request-method`, `content-encoding`,
  `content-language`, `content-length`, `content-location`, `content-md5`,
  `content-range`, `content-type`, `date`, `dnt`, `etag`, `expires`, `from`,
  `host`, `if-match`, `if-modified-since`, `if-none-match`, `if-range`,
  `if-unmodified-since`, `last-modified`, `location`, `max-forwards`,
  `proxy-authorization`, `range`, `referer`,`retry-after`, `tk`,
  `upgrade-insecure-requests`, `user-agent` or `x-content-type-options` are
  discarded.
- `set-cookie` is always an array. Duplicates are added to the array.
- For duplicate `cookie` headers, the values are joined together with '; '.
- For all other headers, the values are joined together with ', '.

*ES module version:*

```js
import { createServer } from 'node:http2';
const server = createServer();
server.on('stream', (stream, headers) => {
  console.log(headers[':path']);
  console.log(headers.ABC);
});
```

*CommonJS version:*

```js
const http2 = require('node:http2');
const server = http2.createServer();
server.on('stream', (stream, headers) => {
  console.log(headers[':path']);
  console.log(headers.ABC);
});
```

#### Raw headers

In some APIs, in addition to object format, headers can also be passed or
accessed as a raw flat array, preserving details of ordering and
duplicate keys to match the raw transmission format.

In this format the keys and values are in the same list. It is *not* a
list of tuples. So, the even-numbered offsets are key values, and the
odd-numbered offsets are the associated values. Duplicate headers are
not merged and so each key-value pair will appear separately.

This can be useful for cases such as proxies, where existing headers
should be exactly forwarded as received, or as a performance
optimization when the headers are already available in raw format.

```js
const rawHeaders = [
  ':status',
  '404',
  'content-type',
  'text/plain',
];

stream.respond(rawHeaders);
```

#### Sensitive headers

HTTP2 headers can be marked as sensitive, which means that the HTTP/2
header compression algorithm will never index them. This can make sense for
header values with low entropy and that may be considered valuable to an
attacker, for example `Cookie` or `Authorization`. To achieve this, add
the header name to the `[http2.sensitiveHeaders]` property as an array:

```js
const headers = {
  ':status': '200',
  'content-type': 'text-plain',
  'cookie': 'some-cookie',
  'other-sensitive-header': 'very secret data',
  [http2.sensitiveHeaders]: ['cookie', 'other-sensitive-header'],
};

stream.respond(headers);
```

For some headers, such as `Authorization` and short `Cookie` headers,
this flag is set automatically.

This property is also set for received headers. It will contain the names of
all headers marked as sensitive, including ones marked that way automatically.

For raw headers, this should still be set as a property on the array, like
`rawHeadersArray[http2.sensitiveHeaders] = ['cookie']`, not as a separate key
and value pair within the array itself.

### Settings object

Added in: v8.4.0

**History**

| Version | Changes |
| --- | --- |
| v12.12.0 | The `maxConcurrentStreams` setting is stricter. |
| v8.9.3 | The `maxHeaderListSize` setting is now strictly enforced. |

The `http2.getDefaultSettings()`, `http2.getPackedSettings()`,
`http2.createServer()`, `http2.createSecureServer()`,
`http2session.settings()`, `http2session.localSettings`, and
`http2session.remoteSettings` APIs either return or receive as input an
object that defines configuration settings for an `Http2Session` object.
These objects are ordinary JavaScript objects containing the following
properties.

- `headerTableSize` [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) Specifies the maximum number of bytes used for
  header compression. The minimum allowed value is 0. The maximum allowed value
  is 232-1. **Default:** `4096`.
- `enablePush` [`<boolean>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#boolean_type) Specifies `true` if HTTP/2 Push Streams are to be
  permitted on the `Http2Session` instances. **Default:** `true`.
- `initialWindowSize` [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) Specifies the *sender's* initial window size in
  bytes for stream-level flow control. The minimum allowed value is 0. The
  maximum allowed value is 232-1. **Default:** `65535`.
  This is a per-stream limit; the window for the connection as a whole is
  configured separately with the `connectionWindowSize` option of
  [`http2.createServer()`](28-http2-part2.md#http2createserveroptions-onrequesthandler) or [`http2.connect()`](28-http2-part2.md#http2connectauthority-options-listener).
- `maxFrameSize` [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) Specifies the size in bytes of the largest frame
  payload. The minimum allowed value is 16,384. The maximum allowed value is
  224-1. **Default:** `16384`.
- `maxConcurrentStreams` [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) Specifies the maximum number of concurrent
  streams permitted on an `Http2Session`. There is no default value which
  implies, at least theoretically, 232-1 streams may be open
  concurrently at any given time in an `Http2Session`. The minimum value
  is 0. The maximum allowed value is 232-1. **Default:**
  `4294967295`.
- `maxHeaderListSize` [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) Specifies the maximum size (uncompressed octets)
  of header list that will be accepted. The minimum allowed value is 0. The
  maximum allowed value is 232-1. **Default:** `65535`.
- `maxHeaderSize` [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) Alias for `maxHeaderListSize`.
- `enableConnectProtocol`[`<boolean>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#boolean_type) Specifies `true` if the "Extended Connect
  Protocol" defined by [RFC 8441](https://tools.ietf.org/html/rfc8441) is to be enabled. This setting is only
  meaningful if sent by the server. Once the `enableConnectProtocol` setting
  has been enabled for a given `Http2Session`, it cannot be disabled.
  **Default:** `false`.
- `customSettings` [`<Object>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Object) Specifies additional settings, yet not implemented
  in node and the underlying libraries. The key of the object defines the
  numeric value of the settings type (as defined in the "HTTP/2 SETTINGS"
  registry established by [RFC 7540]) and the values the actual numeric value
  of the settings.
  The settings type has to be an integer in the range from 1 to 2^16-1.
  It should not be a settings type already handled by node, i.e. currently
  it should be greater than 6, although it is not an error.
  The values need to be unsigned integers in the range from 0 to 2^32-1.
  Currently, a maximum of up 10 custom settings is supported.
  It is only supported for sending SETTINGS, or for receiving settings values
  specified in the `remoteCustomSettings` options of the server or client
  object. Do not mix the `customSettings`-mechanism for a settings id with
  interfaces for the natively handled settings, in case a setting becomes
  natively supported in a future node version.

All additional properties on the settings object are ignored.

### Error handling

There are several types of error conditions that may arise when using the
`node:http2` module:

Validation errors occur when an incorrect argument, option, or setting value is
passed in. These will always be reported by a synchronous `throw`.

State errors occur when an action is attempted at an incorrect time (for
instance, attempting to send data on a stream after it has closed). These will
be reported using either a synchronous `throw` or via an `'error'` event on
the `Http2Stream`, `Http2Session` or HTTP/2 Server objects, depending on where
and when the error occurs.

Internal errors occur when an HTTP/2 session fails unexpectedly. These will be
reported via an `'error'` event on the `Http2Session` or HTTP/2 Server objects.

Protocol errors occur when various HTTP/2 protocol constraints are violated.
These will be reported using either a synchronous `throw` or via an `'error'`
event on the `Http2Stream`, `Http2Session` or HTTP/2 Server objects, depending
on where and when the error occurs.

### Invalid character handling in header names and values

The HTTP/2 implementation applies stricter handling of invalid characters in
HTTP header names and values than the HTTP/1 implementation.

Header field names are *case-insensitive* and are transmitted over the wire
strictly as lower-case strings. The API provided by Node.js allows header
names to be set as mixed-case strings (e.g. `Content-Type`) but will convert
those to lower-case (e.g. `content-type`) upon transmission.

Header field-names *must only* contain one or more of the following ASCII
characters: `a`-`z`, `A`-`Z`, `0`-`9`, `!`, `#`, `$`, `%`, `&`, `'`, `*`, `+`,
`-`, `.`, `^`, `_`, `` ` `` (backtick), `|`, and `~`.

Using invalid characters within an HTTP header field name will cause the
stream to be closed with a protocol error being reported.

Header field values are handled with more leniency but *should* not contain
new-line or carriage return characters and *should* be limited to US-ASCII
characters, per the requirements of the HTTP specification.

### Push streams on the client

To receive pushed streams on the client, set a listener for the `'stream'`
event on the `ClientHttp2Session`:

*ES module version:*

```js
import { connect } from 'node:http2';

const client = connect('http://localhost');

client.on('stream', (pushedStream, requestHeaders) => {
  pushedStream.on('push', (responseHeaders) => {
    // Process response headers
  });
  pushedStream.on('data', (chunk) => { /* handle pushed data */ });
});

const req = client.request({ ':path': '/' });
```

*CommonJS version:*

```js
const http2 = require('node:http2');

const client = http2.connect('http://localhost');

client.on('stream', (pushedStream, requestHeaders) => {
  pushedStream.on('push', (responseHeaders) => {
    // Process response headers
  });
  pushedStream.on('data', (chunk) => { /* handle pushed data */ });
});

const req = client.request({ ':path': '/' });
```

### Supporting the `CONNECT` method

The `CONNECT` method is used to allow an HTTP/2 server to be used as a proxy
for TCP/IP connections.

A simple TCP Server:

*ES module version:*

```js
import { createServer } from 'node:net';

const server = createServer((socket) => {
  let name = '';
  socket.setEncoding('utf8');
  socket.on('data', (chunk) => name += chunk);
  socket.on('end', () => socket.end(`hello ${name}`));
});

server.listen(8000);
```

*CommonJS version:*

```js
const net = require('node:net');

const server = net.createServer((socket) => {
  let name = '';
  socket.setEncoding('utf8');
  socket.on('data', (chunk) => name += chunk);
  socket.on('end', () => socket.end(`hello ${name}`));
});

server.listen(8000);
```

An HTTP/2 CONNECT proxy:

*ES module version:*

```js
import { createServer, constants } from 'node:http2';
const { NGHTTP2_REFUSED_STREAM, NGHTTP2_CONNECT_ERROR } = constants;
import { connect } from 'node:net';

const proxy = createServer();
proxy.on('stream', (stream, headers) => {
  if (headers[':method'] !== 'CONNECT') {
    // Only accept CONNECT requests
    stream.close(NGHTTP2_REFUSED_STREAM);
    return;
  }
  const auth = new URL(`tcp://${headers[':authority']}`);
  // It's a very good idea to verify that hostname and port are
  // things this proxy should be connecting to.
  const socket = connect(auth.port, auth.hostname, () => {
    stream.respond();
    socket.pipe(stream);
    stream.pipe(socket);
  });
  socket.on('error', (error) => {
    stream.close(NGHTTP2_CONNECT_ERROR);
  });
});

proxy.listen(8001);
```

*CommonJS version:*

```js
const http2 = require('node:http2');
const { NGHTTP2_REFUSED_STREAM } = http2.constants;
const net = require('node:net');

const proxy = http2.createServer();
proxy.on('stream', (stream, headers) => {
  if (headers[':method'] !== 'CONNECT') {
    // Only accept CONNECT requests
    stream.close(NGHTTP2_REFUSED_STREAM);
    return;
  }
  const auth = new URL(`tcp://${headers[':authority']}`);
  // It's a very good idea to verify that hostname and port are
  // things this proxy should be connecting to.
  const socket = net.connect(auth.port, auth.hostname, () => {
    stream.respond();
    socket.pipe(stream);
    stream.pipe(socket);
  });
  socket.on('error', (error) => {
    stream.close(http2.constants.NGHTTP2_CONNECT_ERROR);
  });
});

proxy.listen(8001);
```

An HTTP/2 CONNECT client:

*ES module version:*

```js
import { connect, constants } from 'node:http2';

const client = connect('http://localhost:8001');

// Must not specify the ':path' and ':scheme' headers
// for CONNECT requests or an error will be thrown.
const req = client.request({
  ':method': 'CONNECT',
  ':authority': 'localhost:8000',
});

req.on('response', (headers) => {
  console.log(headers[constants.HTTP2_HEADER_STATUS]);
});
let data = '';
req.setEncoding('utf8');
req.on('data', (chunk) => data += chunk);
req.on('end', () => {
  console.log(`The server says: ${data}`);
  client.close();
});
req.end('Jane');
```

*CommonJS version:*

```js
const http2 = require('node:http2');

const client = http2.connect('http://localhost:8001');

// Must not specify the ':path' and ':scheme' headers
// for CONNECT requests or an error will be thrown.
const req = client.request({
  ':method': 'CONNECT',
  ':authority': 'localhost:8000',
});

req.on('response', (headers) => {
  console.log(headers[http2.constants.HTTP2_HEADER_STATUS]);
});
let data = '';
req.setEncoding('utf8');
req.on('data', (chunk) => data += chunk);
req.on('end', () => {
  console.log(`The server says: ${data}`);
  client.close();
});
req.end('Jane');
```

### The extended `CONNECT` protocol

[RFC 8441](https://tools.ietf.org/html/rfc8441) defines an "Extended CONNECT Protocol" extension to HTTP/2 that
may be used to bootstrap the use of an `Http2Stream` using the `CONNECT`
method as a tunnel for other communication protocols (such as WebSockets).

The use of the Extended CONNECT Protocol is enabled by HTTP/2 servers by using
the `enableConnectProtocol` setting:

*ES module version:*

```js
import { createServer } from 'node:http2';
const settings = { enableConnectProtocol: true };
const server = createServer({ settings });
```

*CommonJS version:*

```js
const http2 = require('node:http2');
const settings = { enableConnectProtocol: true };
const server = http2.createServer({ settings });
```

Once the client receives the `SETTINGS` frame from the server indicating that
the extended CONNECT may be used, it may send `CONNECT` requests that use the
`':protocol'` HTTP/2 pseudo-header:

*ES module version:*

```js
import { connect } from 'node:http2';
const client = connect('http://localhost:8080');
client.on('remoteSettings', (settings) => {
  if (settings.enableConnectProtocol) {
    const req = client.request({ ':method': 'CONNECT', ':protocol': 'foo' });
    // ...
  }
});
```

*CommonJS version:*

```js
const http2 = require('node:http2');
const client = http2.connect('http://localhost:8080');
client.on('remoteSettings', (settings) => {
  if (settings.enableConnectProtocol) {
    const req = client.request({ ':method': 'CONNECT', ':protocol': 'foo' });
    // ...
  }
});
```

## Compatibility API

The Compatibility API has the goal of providing a similar developer experience
of HTTP/1 when using HTTP/2, making it possible to develop applications
that support both [HTTP/1](27-http-part1.md) and HTTP/2. This API targets only the
**public API** of the [HTTP/1](27-http-part1.md). However many modules use internal
methods or state, and those *are not supported* as it is a completely
different implementation.

The following example creates an HTTP/2 server using the compatibility
API:

*ES module version:*

```js
import { createServer } from 'node:http2';
const server = createServer((req, res) => {
  res.writeHead(200, {
    'Content-Type': 'text/plain; charset=utf-8',
    'X-Foo': 'bar',
  });
  res.end('ok');
});
```

*CommonJS version:*

```js
const http2 = require('node:http2');
const server = http2.createServer((req, res) => {
  res.writeHead(200, {
    'Content-Type': 'text/plain; charset=utf-8',
    'X-Foo': 'bar',
  });
  res.end('ok');
});
```

In order to create a mixed [HTTPS](29-https.md) and HTTP/2 server, refer to the
[ALPN negotiation](#alpn-negotiation) section.
Upgrading from non-tls HTTP/1 servers is not supported.

The HTTP/2 compatibility API is composed of [`Http2ServerRequest`](#class-http2http2serverrequest) and
[`Http2ServerResponse`](#class-http2http2serverresponse). They aim at API compatibility with HTTP/1, but
they do not hide the differences between the protocols. As an example,
the status message for HTTP codes is ignored.

### ALPN negotiation

ALPN negotiation allows supporting both [HTTPS](29-https.md) and HTTP/2 over
the same socket. The `req` and `res` objects can be either HTTP/1 or
HTTP/2, and an application **must** restrict itself to the public API of
[HTTP/1](27-http-part1.md), and detect if it is possible to use the more advanced
features of HTTP/2.

The following example creates a server that supports both protocols:

*ES module version:*

```js
import { createSecureServer } from 'node:http2';
import { readFileSync } from 'node:fs';

const cert = readFileSync('./cert.pem');
const key = readFileSync('./key.pem');

const server = createSecureServer(
  { cert, key, allowHTTP1: true },
  onRequest,
).listen(8000);

function onRequest(req, res) {
  // Detects if it is an HTTPS request or HTTP/2
  const { socket: { alpnProtocol } } = req.httpVersion === '2.0' ?
    req.stream.session : req;
  res.writeHead(200, { 'content-type': 'application/json' });
  res.end(JSON.stringify({
    alpnProtocol,
    httpVersion: req.httpVersion,
  }));
}
```

*CommonJS version:*

```js
const { createSecureServer } = require('node:http2');
const { readFileSync } = require('node:fs');

const cert = readFileSync('./cert.pem');
const key = readFileSync('./key.pem');

const server = createSecureServer(
  { cert, key, allowHTTP1: true },
  onRequest,
).listen(4443);

function onRequest(req, res) {
  // Detects if it is an HTTPS request or HTTP/2
  const { socket: { alpnProtocol } } = req.httpVersion === '2.0' ?
    req.stream.session : req;
  res.writeHead(200, { 'content-type': 'application/json' });
  res.end(JSON.stringify({
    alpnProtocol,
    httpVersion: req.httpVersion,
  }));
}
```

The `'request'` event works identically on both [HTTPS](29-https.md) and
HTTP/2.

### Class: `http2.Http2ServerRequest`

Added in: v8.4.0

- Extends: [`<stream.Readable>`](51-stream-part1.md#class-streamreadable)

A `Http2ServerRequest` object is created by [`http2.Server`](28-http2-part2.md#class-http2server) or
[`http2.SecureServer`](28-http2-part2.md#class-http2secureserver) and passed as the first argument to the
[`'request'`](28-http2-part2.md#event-request) event. It may be used to access a request status, headers, and
data.

#### Event: `'aborted'`

Added in: v8.4.0

The `'aborted'` event is emitted whenever a `Http2ServerRequest` instance is
abnormally aborted in mid-communication.

The `'aborted'` event will only be emitted if the `Http2ServerRequest` writable
side has not been ended.

#### Event: `'close'`

Added in: v8.4.0

Indicates that the underlying [`Http2Stream`](28-http2-part1.md#class-http2stream) was closed.
Just like `'end'`, this event occurs only once per response.

#### `request.aborted`

Added in: v10.1.0

- Type: [`<boolean>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#boolean_type)

The `request.aborted` property will be `true` if the request has
been aborted.

#### `request.authority`

Added in: v8.4.0

- Type: [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type)

The request authority pseudo header field. Because HTTP/2 allows requests
to set either `:authority` or `host`, this value is derived from
`req.headers[':authority']` if present. Otherwise, it is derived from
`req.headers['host']`.

#### `request.complete`

Added in: v12.10.0

- Type: [`<boolean>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#boolean_type)

The `request.complete` property will be `true` if the request has
been completed, aborted, or destroyed.

#### `request.connection`

Added in: v8.4.0Deprecated in: v13.0.0

Stability: 0 - Deprecated. Use [`request.socket`](#requestsocket).

- Type: [`<net.Socket>`](38-net.md#class-netsocket) | [`<tls.TLSSocket>`](55-tls.md#tlstlssocket)

See [`request.socket`](#requestsocket).

#### `request.destroy([error])`

Added in: v8.4.0

- `error` [`<Error>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Error)

Calls `destroy()` on the [`Http2Stream`](28-http2-part1.md#class-http2stream) that received
the [`Http2ServerRequest`](#class-http2http2serverrequest). If `error` is provided, an `'error'` event
is emitted and `error` is passed as an argument to any listeners on the event.

It does nothing if the stream was already destroyed.

#### `request.headers`

Added in: v8.4.0

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

See [HTTP/2 Headers Object](#headers-object).

In HTTP/2, the request path, host name, protocol, and method are represented as
special headers prefixed with the `:` character (e.g. `':path'`). These special
headers will be included in the `request.headers` object. Care must be taken not
to inadvertently modify these special headers or errors may occur. For instance,
removing all headers from the request will cause errors to occur:

```js
removeAllHeaders(request.headers);
assert(request.url);   // Fails because the :path header has been removed
```

#### `request.httpVersion`

Added in: v8.4.0

- Type: [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type)

In case of server request, the HTTP version sent by the client. In the case of
client response, the HTTP version of the connected-to server. Returns
`'2.0'`.

Also `message.httpVersionMajor` is the first integer and
`message.httpVersionMinor` is the second.

#### `request.method`

Added in: v8.4.0

- Type: [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type)

The request method as a string. Read-only. Examples: `'GET'`, `'DELETE'`.

#### `request.rawHeaders`

Added in: v8.4.0

- Type: [`<HTTP/2 Raw Headers>`](28-http2-part3.md#raw-headers)

The raw request/response headers list exactly as they were received.

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

#### `request.rawTrailers`

Added in: v8.4.0

- Type: [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type)[]

The raw request/response trailer keys and values exactly as they were
received. Only populated at the `'end'` event.

#### `request.scheme`

Added in: v8.4.0

- Type: [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type)

The request scheme pseudo header field indicating the scheme
portion of the target URL.

#### `request.setTimeout(msecs, callback)`

Added in: v8.4.0

- `msecs` [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type)
- `callback` [`<Function>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Function)
- Returns: [`<http2.Http2ServerRequest>`](28-http2-part3.md#class-http2http2serverrequest)

Sets the [`Http2Stream`](28-http2-part1.md#class-http2stream)'s timeout value to `msecs`. If a callback is
provided, then it is added as a listener on the `'timeout'` event on
the response object.

If no `'timeout'` listener is added to the request, the response, or
the server, then [`Http2Stream`](28-http2-part1.md#class-http2stream)s are destroyed when they time out. If a
handler is assigned to the request, the response, or the server's `'timeout'`
events, timed out sockets must be handled explicitly.

#### `request.socket`

Added in: v8.4.0

- Type: [`<net.Socket>`](38-net.md#class-netsocket) | [`<tls.TLSSocket>`](55-tls.md#tlstlssocket)

Returns a `Proxy` object that acts as a `net.Socket` (or `tls.TLSSocket`) but
applies getters, setters, and methods based on HTTP/2 logic.

`destroyed`, `readable`, and `writable` properties will be retrieved from and
set on `request.stream`.

`destroy`, `emit`, `end`, `on` and `once` methods will be called on
`request.stream`.

`setTimeout` method will be called on `request.stream.session`.

`pause`, `read`, `resume`, and `write` will throw an error with code
`ERR_HTTP2_NO_SOCKET_MANIPULATION`. See [`Http2Session` and Sockets](28-http2-part1.md#http2session-and-sockets) for
more information.

All other interactions will be routed directly to the socket. With TLS support,
use [`request.socket.getPeerCertificate()`](55-tls.md#tlssocketgetpeercertificatedetailed) to obtain the client's
authentication details.

#### `request.stream`

Added in: v8.4.0

- Type: [`<Http2Stream>`](28-http2-part1.md#class-http2stream)

The [`Http2Stream`](28-http2-part1.md#class-http2stream) object backing the request.

#### `request.trailers`

Added in: v8.4.0

- Type: [`<Object>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Object)

The request/response trailers object. Only populated at the `'end'` event.

#### `request.url`

Added in: v8.4.0

- Type: [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type)

Request URL string. This contains only the URL that is present in the actual
HTTP request. If the request is:

```http
GET /status?name=ryan HTTP/1.1
Accept: text/plain
```

Then `request.url` will be:

```json
"/status?name=ryan"
```

To parse the url into its parts, `new URL()` can be used:

```console
$ node
> new URL('/status?name=ryan', 'http://example.com')
URL {
  href: 'http://example.com/status?name=ryan',
  origin: 'http://example.com',
  protocol: 'http:',
  username: '',
  password: '',
  host: 'example.com',
  hostname: 'example.com',
  port: '',
  pathname: '/status',
  search: '?name=ryan',
  searchParams: URLSearchParams { 'name' => 'ryan' },
  hash: ''
}
```

### Class: `http2.Http2ServerResponse`

Added in: v8.4.0

- Extends: [`<Stream>`](51-stream-part1.md#stream)

This object is created internally by an HTTP server, not by the user. It is
passed as the second parameter to the [`'request'`](28-http2-part2.md#event-request) event.

#### Event: `'close'`

Added in: v8.4.0

Indicates that the underlying [`Http2Stream`](28-http2-part1.md#class-http2stream) was terminated before
[`response.end()`](#responseenddata-encoding-callback) was called or able to flush.

#### Event: `'finish'`

Added in: v8.4.0

Emitted when the response has been sent. More specifically, this event is
emitted when the last segment of the response headers and body have been
handed off to the HTTP/2 multiplexing for transmission over the network. It
does not imply that the client has received anything yet.

After this event, no more events will be emitted on the response object.

#### `response.addTrailers(headers)`

Added in: v8.4.0

- `headers` [`<Object>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Object)

This method adds HTTP trailing headers (a header but at the end of the
message) to the response.

Attempting to set a header field name or value that contains invalid characters
will result in a [`TypeError`](22-errors-part1.md#class-typeerror) being thrown.

#### `response.appendHeader(name, value)`

Added in: v21.7.0, v20.12.0

- `name` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type)
- `value` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) | [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type)[]

Append a single header value to the header object.

If the value is an array, this is equivalent to calling this method multiple
times.

If there were no previous values for the header, this is equivalent to calling
[`response.setHeader()`](#responsesetheadername-value).

Attempting to set a header field name or value that contains invalid characters
will result in a [`TypeError`](22-errors-part1.md#class-typeerror) being thrown.

```js
// Returns headers including "set-cookie: a" and "set-cookie: b"
const server = http2.createServer((req, res) => {
  res.setHeader('set-cookie', 'a');
  res.appendHeader('set-cookie', 'b');
  res.writeHead(200);
  res.end('ok');
});
```

#### `response.connection`

Added in: v8.4.0Deprecated in: v13.0.0

Stability: 0 - Deprecated. Use [`response.socket`](#responsesocket).

- Type: [`<net.Socket>`](38-net.md#class-netsocket) | [`<tls.TLSSocket>`](55-tls.md#tlstlssocket)

See [`response.socket`](#responsesocket).

#### `response.createPushResponse(headers, callback)`

Added in: v8.4.0

**History**

| Version | Changes |
| --- | --- |
| v18.0.0 | Passing an invalid callback to the `callback` argument now throws `ERR_INVALID_ARG_TYPE` instead of `ERR_INVALID_CALLBACK`. |

- `headers` [`<HTTP/2 Headers Object>`](28-http2-part3.md#headers-object) An object describing the headers
- `callback` [`<Function>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Function) Called once `http2stream.pushStream()` is finished,
  or either when the attempt to create the pushed `Http2Stream` has failed or
  has been rejected, or the state of `Http2ServerRequest` is closed prior to
  calling the `http2stream.pushStream()` method
  - `err` [`<Error>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Error)
  - `res` [`<http2.Http2ServerResponse>`](28-http2-part3.md#class-http2http2serverresponse) The newly-created `Http2ServerResponse`
    object

Call [`http2stream.pushStream()`](28-http2-part2.md#http2streampushstreamheaders-options-callback) with the given headers, and wrap the
given [`Http2Stream`](28-http2-part1.md#class-http2stream) on a newly created `Http2ServerResponse` as the callback
parameter if successful. When `Http2ServerRequest` is closed, the callback is
called with an error `ERR_HTTP2_INVALID_STREAM`.

#### `response.end([data[, encoding]][, callback])`

Added in: v8.4.0

**History**

| Version | Changes |
| --- | --- |
| v10.0.0 | This method now returns a reference to `ServerResponse`. |

- `data` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) | [`<Buffer>`](07-buffer-part1.md#class-buffer) | [`<Uint8Array>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Uint8Array)
- `encoding` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type)
- `callback` [`<Function>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Function)
- Returns: [`<this>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Operators/this)

This method signals to the server that all of the response headers and body
have been sent; that server should consider this message complete.
The method, `response.end()`, MUST be called on each response.

If `data` is specified, it is equivalent to calling
[`response.write(data, encoding)`](27-http-part2.md#responsewritechunk-encoding-callback) followed by `response.end(callback)`.

If `callback` is specified, it will be called when the response stream
is finished.

#### `response.finished`

Added in: v8.4.0Deprecated in: v13.4.0, v12.16.0

Stability: 0 - Deprecated. Use [`response.writableEnded`](#responsewritableended).

- Type: [`<boolean>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#boolean_type)

Boolean value that indicates whether the response has completed. Starts
as `false`. After [`response.end()`](#responseenddata-encoding-callback) executes, the value will be `true`.

#### `response.getHeader(name)`

Added in: v8.4.0

- `name` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type)
- Returns: [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type)

Reads out a header that has already been queued but not sent to the client.
The name is case-insensitive.

```js
const contentType = response.getHeader('content-type');
```

#### `response.getHeaderNames()`

Added in: v8.4.0

- Returns: [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type)[]

Returns an array containing the unique names of the current outgoing headers.
All header names are lowercase.

```js
response.setHeader('Foo', 'bar');
response.setHeader('Set-Cookie', ['foo=bar', 'bar=baz']);

const headerNames = response.getHeaderNames();
// headerNames === ['foo', 'set-cookie']
```

#### `response.getHeaders()`

Added in: v8.4.0

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

#### `response.hasHeader(name)`

Added in: v8.4.0

- `name` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type)
- Returns: [`<boolean>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#boolean_type)

Returns `true` if the header identified by `name` is currently set in the
outgoing headers. The header name matching is case-insensitive.

```js
const hasContentType = response.hasHeader('content-type');
```

#### `response.headersSent`

Added in: v8.4.0

- Type: [`<boolean>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#boolean_type)

True if headers were sent, false otherwise (read-only).

#### `response.removeHeader(name)`

Added in: v8.4.0

- `name` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type)

Removes a header that has been queued for implicit sending.

```js
response.removeHeader('Content-Encoding');
```

#### `response.req`

Added in: v15.7.0

- Type: [`<http2.Http2ServerRequest>`](28-http2-part3.md#class-http2http2serverrequest)

A reference to the original HTTP2 `request` object.

#### `response.sendDate`

Added in: v8.4.0

- Type: [`<boolean>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#boolean_type)

When true, the Date header will be automatically generated and sent in
the response if it is not already present in the headers. Defaults to true.

This should only be disabled for testing; HTTP requires the Date header
in responses.

#### `response.setHeader(name, value)`

Added in: v8.4.0

- `name` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type)
- `value` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) | [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type)[]

Sets a single header value for implicit headers. If this header already exists
in the to-be-sent headers, its value will be replaced. Use an array of strings
here to send multiple headers with the same name.

```js
response.setHeader('Content-Type', 'text/html; charset=utf-8');
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
const server = http2.createServer((req, res) => {
  res.setHeader('Content-Type', 'text/html; charset=utf-8');
  res.setHeader('X-Foo', 'bar');
  res.writeHead(200, { 'Content-Type': 'text/plain; charset=utf-8' });
  res.end('ok');
});
```

#### `response.setTimeout(msecs[, callback])`

Added in: v8.4.0

- `msecs` [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type)
- `callback` [`<Function>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Function)
- Returns: [`<http2.Http2ServerResponse>`](28-http2-part3.md#class-http2http2serverresponse)

Sets the [`Http2Stream`](28-http2-part1.md#class-http2stream)'s timeout value to `msecs`. If a callback is
provided, then it is added as a listener on the `'timeout'` event on
the response object.

If no `'timeout'` listener is added to the request, the response, or
the server, then [`Http2Stream`](28-http2-part1.md#class-http2stream)s are destroyed when they time out. If a
handler is assigned to the request, the response, or the server's `'timeout'`
events, timed out sockets must be handled explicitly.

#### `response.socket`

Added in: v8.4.0

- Type: [`<net.Socket>`](38-net.md#class-netsocket) | [`<tls.TLSSocket>`](55-tls.md#tlstlssocket)

Returns a `Proxy` object that acts as a `net.Socket` (or `tls.TLSSocket`) but
applies getters, setters, and methods based on HTTP/2 logic.

`destroyed`, `readable`, and `writable` properties will be retrieved from and
set on `response.stream`.

`destroy`, `emit`, `end`, `on` and `once` methods will be called on
`response.stream`.

`setTimeout` method will be called on `response.stream.session`.

`pause`, `read`, `resume`, and `write` will throw an error with code
`ERR_HTTP2_NO_SOCKET_MANIPULATION`. See [`Http2Session` and Sockets](28-http2-part1.md#http2session-and-sockets) for
more information.

All other interactions will be routed directly to the socket.

*ES module version:*

```js
import { createServer } from 'node:http2';
const server = createServer((req, res) => {
  const ip = req.socket.remoteAddress;
  const port = req.socket.remotePort;
  res.end(`Your IP address is ${ip} and your source port is ${port}.`);
}).listen(3000);
```

*CommonJS version:*

```js
const http2 = require('node:http2');
const server = http2.createServer((req, res) => {
  const ip = req.socket.remoteAddress;
  const port = req.socket.remotePort;
  res.end(`Your IP address is ${ip} and your source port is ${port}.`);
}).listen(3000);
```

#### `response.statusCode`

Added in: v8.4.0

- Type: [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type)

When using implicit headers (not calling [`response.writeHead()`](#responsewriteheadstatuscode-statusmessage-headers) explicitly),
this property controls the status code that will be sent to the client when
the headers get flushed.

```js
response.statusCode = 404;
```

After response header was sent to the client, this property indicates the
status code which was sent out.

#### `response.statusMessage`

Added in: v8.4.0

- Type: [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type)

Status message is not supported by HTTP/2 (RFC 7540 8.1.2.4). It returns
an empty string.

#### `response.stream`

Added in: v8.4.0

- Type: [`<Http2Stream>`](28-http2-part1.md#class-http2stream)

The [`Http2Stream`](28-http2-part1.md#class-http2stream) object backing the response.

#### `response.writableEnded`

Added in: v12.9.0

- Type: [`<boolean>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#boolean_type)

Is `true` after [`response.end()`](#responseenddata-encoding-callback) has been called. This property
does not indicate whether the data has been flushed, for this use
[`writable.writableFinished`](51-stream-part1.md#writablewritablefinished) instead.

#### `response.write(chunk[, encoding][, callback])`

Added in: v8.4.0

- `chunk` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) | [`<Buffer>`](07-buffer-part1.md#class-buffer) | [`<Uint8Array>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Uint8Array)
- `encoding` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type)
- `callback` [`<Function>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Function)
- Returns: [`<boolean>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#boolean_type)

If this method is called and [`response.writeHead()`](#responsewriteheadstatuscode-statusmessage-headers) has not been called,
it will switch to implicit header mode and flush the implicit headers.

This sends a chunk of the response body. This method may
be called multiple times to provide successive parts of the body.

In the `node:http` module, the response body is omitted when the
request is a HEAD request. Similarly, the `204` and `304` responses
*must not* include a message body.

`chunk` can be a string or a buffer. If `chunk` is a string,
the second parameter specifies how to encode it into a byte stream.
By default the `encoding` is `'utf8'`. `callback` will be called when this chunk
of data is flushed.

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

#### `response.writeContinue()`

Added in: v8.4.0

Sends a status `100 Continue` to the client, indicating that the request body
should be sent. See the [`'checkContinue'`](28-http2-part2.md#event-checkcontinue) event on `Http2Server` and
`Http2SecureServer`.

#### `response.writeEarlyHints(hints)`

Added in: v18.11.0

- `hints` [`<Object>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Object)

Sends a status `103 Early Hints` to the client with a Link header,
indicating that the user agent can preload/preconnect the linked resources.
The `hints` is an object containing the values of headers to be sent with
early hints message.

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
});
```

#### `response.writeInformation(statusCode[, headers])`

Added in: v26.2.0

- `statusCode` [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) An HTTP 1xx informational status code, between `100`
  and `199` inclusive, excluding `101` (Switching Protocols) which is not
  allowed in HTTP/2.
- `headers` [`<Object>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Object) An optional object of headers to send with the
  informational response.

Sends an arbitrary HTTP 1xx informational response, equivalent in HTTP/2 to a
`HEADERS` frame whose `:status` pseudo-header is a 1xx code. May be called
multiple times before the final response. After the final response headers
have been sent, this method is a no-op and returns `false`.

This is the generic equivalent of [`response.writeContinue()`](#responsewritecontinue) and
[`response.writeEarlyHints()`](#responsewriteearlyhintshints).

```js
response.writeInformation(110, { 'X-Progress': '50%' });
```

#### `response.writeHead(statusCode[, statusMessage][, headers])`

Added in: v8.4.0

**History**

| Version | Changes |
| --- | --- |
| v11.10.0, v10.17.0 | Return `this` from `writeHead()` to allow chaining with `end()`. |

- `statusCode` [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type)
- `statusMessage` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type)
- `headers` [`<HTTP/2 Headers Object>`](28-http2-part3.md#headers-object) | [`<HTTP/2 Raw Headers>`](28-http2-part3.md#raw-headers)
- Returns: [`<http2.Http2ServerResponse>`](28-http2-part3.md#class-http2http2serverresponse)

Sends a response header to the request. The status code is a 3-digit HTTP
status code, like `404`. The last argument, `headers`, are the response headers.

Returns a reference to the `Http2ServerResponse`, so that calls can be chained.

For compatibility with [HTTP/1](27-http-part1.md), a human-readable `statusMessage` may be
passed as the second argument. However, because the `statusMessage` has no
meaning within HTTP/2, the argument will have no effect and a process warning
will be emitted.

```js
const body = 'hello world';
response.writeHead(200, {
  'Content-Length': Buffer.byteLength(body),
  'Content-Type': 'text/plain; charset=utf-8',
});
```

`Content-Length` is given in bytes not characters. The
`Buffer.byteLength()` API may be used to determine the number of bytes in a
given encoding. On outbound messages, Node.js does not check if Content-Length
and the length of the body being transmitted are equal or not. However, when
receiving messages, Node.js will automatically reject messages when the
`Content-Length` does not match the actual payload size.

This method may be called at most one time on a message before
[`response.end()`](#responseenddata-encoding-callback) is called.

If [`response.write()`](#responsewritechunk-encoding-callback) or [`response.end()`](#responseenddata-encoding-callback) are called before calling
this, the implicit/mutable headers will be calculated and call this function.

When headers have been set with [`response.setHeader()`](#responsesetheadername-value), they will be merged
with any headers passed to [`response.writeHead()`](#responsewriteheadstatuscode-statusmessage-headers), with the headers passed
to [`response.writeHead()`](#responsewriteheadstatuscode-statusmessage-headers) given precedence.

```js
// Returns content-type = text/plain
const server = http2.createServer((req, res) => {
  res.setHeader('Content-Type', 'text/html; charset=utf-8');
  res.setHeader('X-Foo', 'bar');
  res.writeHead(200, { 'Content-Type': 'text/plain; charset=utf-8' });
  res.end('ok');
});
```

Attempting to set a header field name or value that contains invalid characters
will result in a [`TypeError`](22-errors-part1.md#class-typeerror) being thrown.

[Contents](../README.md) | [Previous: HTTP/2, part 2 of 4](28-http2-part2.md) | [Next: HTTP/2, part 4 of 4](28-http2-part4.md)
