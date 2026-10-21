[Contents](../README.md) | [Previous: TTY](57-tty.md) | [Next: URL](59-url.md)

Source: [https://nodejs.org/docs/latest/api/dgram.html](https://nodejs.org/docs/latest/api/dgram.html)

# UDP/datagram sockets

[Stability: 2](01-documentation.md#stability-index) - Stable

The `node:dgram` module provides an implementation of UDP datagram sockets.

*ES module version:*

```js
import dgram from 'node:dgram';

const server = dgram.createSocket('udp4');

server.on('error', (err) => {
  console.error(`server error:\n${err.stack}`);
  server.close();
});

server.on('message', (msg, rinfo) => {
  console.log(`server got: ${msg} from ${rinfo.address}:${rinfo.port}`);
});

server.on('listening', () => {
  const address = server.address();
  console.log(`server listening ${address.address}:${address.port}`);
});

server.bind(41234);
// Prints: server listening 0.0.0.0:41234
```

*CommonJS version:*

```js
const dgram = require('node:dgram');
const server = dgram.createSocket('udp4');

server.on('error', (err) => {
  console.error(`server error:\n${err.stack}`);
  server.close();
});

server.on('message', (msg, rinfo) => {
  console.log(`server got: ${msg} from ${rinfo.address}:${rinfo.port}`);
});

server.on('listening', () => {
  const address = server.address();
  console.log(`server listening ${address.address}:${address.port}`);
});

server.bind(41234);
// Prints: server listening 0.0.0.0:41234
```

## Class: `dgram.Socket`

Added in: v0.1.99

- Extends: [`<EventEmitter>`](23-events.md#class-eventemitter)

Encapsulates the datagram functionality.

New instances of `dgram.Socket` are created using [`dgram.createSocket()`](#dgramcreatesocketoptions-callback).
The `new` keyword is not to be used to create `dgram.Socket` instances.

### Event: `'close'`

Added in: v0.1.99

The `'close'` event is emitted after a socket is closed with [`close()`](#socketclosecallback).
Once triggered, no new `'message'` events will be emitted on this socket.

### Event: `'connect'`

Added in: v12.0.0

The `'connect'` event is emitted after a socket is associated to a remote
address as a result of a successful [`connect()`](#socketconnectport-address-callback) call.

### Event: `'error'`

Added in: v0.1.99

- `exception` [`<Error>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Error)

The `'error'` event is emitted whenever any error occurs. The event handler
function is passed a single `Error` object.

### Event: `'listening'`

Added in: v0.1.99

The `'listening'` event is emitted once the `dgram.Socket` is addressable and
can receive data. This happens either explicitly with `socket.bind()` or
implicitly the first time data is sent using `socket.send()`.
Until the `dgram.Socket` is listening, the underlying system resources do not
exist and calls such as `socket.address()` and `socket.setTTL()` will fail.

### Event: `'message'`

Added in: v0.1.99

**History**

| Version | Changes |
| --- | --- |
| v18.4.0 | The `family` property now returns a string instead of a number. |
| v18.0.0 | The `family` property now returns a number instead of a string. |

The `'message'` event is emitted when a new datagram is available on a socket.
The event handler function is passed two arguments: `msg` and `rinfo`.

- `msg` [`<Buffer>`](07-buffer-part1.md#class-buffer) The message.
- `rinfo` [`<Object>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Object) Remote address information.
  - `address` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) The sender address.
  - `family` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) The address family (`'IPv4'` or `'IPv6'`).
  - `port` [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) The sender port.
  - `size` [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) The message size.

If the source address of the incoming packet is an IPv6 link-local
address, the interface name is added to the `address`. For
example, a packet received on the `en0` interface might have the
address field set to `'fe80::2618:1234:ab11:3b9c%en0'`, where `'%en0'`
is the interface name as a zone ID suffix.

### `socket.addMembership(multicastAddress[, multicastInterface])`

Added in: v0.6.9

- `multicastAddress` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type)
- `multicastInterface` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type)

Tells the kernel to join a multicast group at the given `multicastAddress` and
`multicastInterface` using the `IP_ADD_MEMBERSHIP` socket option. If the
`multicastInterface` argument is not specified, the operating system will choose
one interface and will add membership to it. To add membership to every
available interface, call `addMembership` multiple times, once per interface.

When called on an unbound socket, this method will implicitly bind to a random
port, listening on all interfaces.

When sharing a UDP socket across multiple `cluster` workers, the
`socket.addMembership()` function must be called only once or an
`EADDRINUSE` error will occur:

*ES module version:*

```js
import cluster from 'node:cluster';
import dgram from 'node:dgram';

if (cluster.isPrimary) {
  cluster.fork(); // Works ok.
  cluster.fork(); // Fails with EADDRINUSE.
} else {
  const s = dgram.createSocket('udp4');
  s.bind(1234, () => {
    s.addMembership('224.0.0.114');
  });
}
```

*CommonJS version:*

```js
const cluster = require('node:cluster');
const dgram = require('node:dgram');

if (cluster.isPrimary) {
  cluster.fork(); // Works ok.
  cluster.fork(); // Fails with EADDRINUSE.
} else {
  const s = dgram.createSocket('udp4');
  s.bind(1234, () => {
    s.addMembership('224.0.0.114');
  });
}
```

### `socket.addSourceSpecificMembership(sourceAddress, groupAddress[, multicastInterface])`

Added in: v13.1.0, v12.16.0

- `sourceAddress` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type)
- `groupAddress` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type)
- `multicastInterface` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type)

Tells the kernel to join a source-specific multicast channel at the given
`sourceAddress` and `groupAddress`, using the `multicastInterface` with the
`IP_ADD_SOURCE_MEMBERSHIP` socket option. If the `multicastInterface` argument
is not specified, the operating system will choose one interface and will add
membership to it. To add membership to every available interface, call
`socket.addSourceSpecificMembership()` multiple times, once per interface.

When called on an unbound socket, this method will implicitly bind to a random
port, listening on all interfaces.

### `socket.address()`

Added in: v0.1.99

- Returns: [`<Object>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Object)

Returns an object containing the address information for a socket.
For UDP sockets, this object will contain `address`, `family`, and `port`
properties.

This method throws `EBADF` if called on an unbound socket.

### `socket.bind([port][, address][, callback])`

Added in: v0.1.99

**History**

| Version | Changes |
| --- | --- |
| v0.9.1 | The method was changed to an asynchronous execution model. Legacy code would need to be changed to pass a callback function to the method call. |

- `port` [`<integer>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type)
- `address` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type)
- `callback` [`<Function>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Function) with no parameters. Called when binding is complete.

For UDP sockets, causes the `dgram.Socket` to listen for datagram
messages on a named `port` and optional `address`. If `port` is not
specified or is `0`, the operating system will attempt to bind to a
random port. If `address` is not specified, the operating system will
attempt to listen on all addresses. Once binding is complete, a
`'listening'` event is emitted and the optional `callback` function is
called.

Specifying both a `'listening'` event listener and passing a
`callback` to the `socket.bind()` method is not harmful but not very
useful.

A bound datagram socket keeps the Node.js process running to receive
datagram messages.

If binding fails, an `'error'` event is generated. In rare cases (e.g.,
attempting to bind with a closed socket), an [`Error`](22-errors-part1.md#class-error) may be thrown.

Example of a UDP server listening on port 41234:

*ES module version:*

```js
import dgram from 'node:dgram';

const server = dgram.createSocket('udp4');

server.on('error', (err) => {
  console.error(`server error:\n${err.stack}`);
  server.close();
});

server.on('message', (msg, rinfo) => {
  console.log(`server got: ${msg} from ${rinfo.address}:${rinfo.port}`);
});

server.on('listening', () => {
  const address = server.address();
  console.log(`server listening ${address.address}:${address.port}`);
});

server.bind(41234);
// Prints: server listening 0.0.0.0:41234
```

*CommonJS version:*

```js
const dgram = require('node:dgram');
const server = dgram.createSocket('udp4');

server.on('error', (err) => {
  console.error(`server error:\n${err.stack}`);
  server.close();
});

server.on('message', (msg, rinfo) => {
  console.log(`server got: ${msg} from ${rinfo.address}:${rinfo.port}`);
});

server.on('listening', () => {
  const address = server.address();
  console.log(`server listening ${address.address}:${address.port}`);
});

server.bind(41234);
// Prints: server listening 0.0.0.0:41234
```

### `socket.bind(options[, callback])`

Added in: v0.11.14

- `options` [`<Object>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Object) Required. Supports the following properties:
  - `port` [`<integer>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type)
  - `address` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type)
  - `exclusive` [`<boolean>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#boolean_type)
  - `fd` [`<integer>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type)
- `callback` [`<Function>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Function)

For UDP sockets, causes the `dgram.Socket` to listen for datagram
messages on a named `port` and optional `address` that are passed as
properties of an `options` object passed as the first argument. If
`port` is not specified or is `0`, the operating system will attempt
to bind to a random port. If `address` is not specified, the operating
system will attempt to listen on all addresses. Once binding is
complete, a `'listening'` event is emitted and the optional `callback`
function is called.

The `options` object may contain a `fd` property. When a `fd` greater
than `0` is set, it will wrap around an existing socket with the given
file descriptor. In this case, the properties of `port` and `address`
will be ignored.

Specifying both a `'listening'` event listener and passing a
`callback` to the `socket.bind()` method is not harmful but not very
useful.

The `options` object may contain an additional `exclusive` property that is
used when using `dgram.Socket` objects with the [`cluster`](12-cluster.md) module. When
`exclusive` is set to `false` (the default), cluster workers will use the same
underlying socket handle allowing connection handling duties to be shared.
When `exclusive` is `true`, however, the handle is not shared and attempted
port sharing results in an error. Creating a `dgram.Socket` with the `reusePort`
option set to `true` causes `exclusive` to always be `true` when `socket.bind()`
is called.

A bound datagram socket keeps the Node.js process running to receive
datagram messages.

If binding fails, an `'error'` event is generated. In rare cases (e.g.,
attempting to bind with a closed socket), an [`Error`](22-errors-part1.md#class-error) may be thrown.

An example socket listening on an exclusive port is shown below.

```js
socket.bind({
  address: 'localhost',
  port: 8000,
  exclusive: true,
});
```

### `socket.bindSync([options])`

Added in: v26.4.0

- `options` [`<Object>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Object)
  - `port` [`<integer>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) If omitted or `0`, the operating system will assign an
    arbitrary unused port. **Default:** `0`.
  - `address` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) A numeric IP address to bind to. Unlike [`socket.bind()`](#socketbindport-address-callback), no DNS resolution is performed, so a host name is not
    accepted. If omitted, the operating system binds to all addresses
    (`'0.0.0.0'` for `udp4` sockets, `'::'` for `udp6`).
- Returns: [`<Object>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Object) The bound address as returned by [`socket.address()`](#socketaddress).

The synchronous counterpart of [`socket.bind()`](#socketbindport-address-callback). `bind(2)` is a local,
non-blocking system call, so the bind is performed inline and the resolved
address is returned immediately, including the operating-system-assigned
ephemeral port when `port` is `0`:

```js
const dgram = require('node:dgram');

const socket = dgram.createSocket('udp4');
const address = socket.bindSync({ address: '0.0.0.0', port: 0 });
console.log(address); // e.g. { address: '0.0.0.0', family: 'IPv4', port: 53124 }
```

A bind failure such as `EADDRINUSE` is thrown synchronously rather than emitted
as an `'error'` event. After `bindSync()` returns, [`socket.address()`](#socketaddress) is
valid synchronously and the `'listening'` event is emitted on the next tick.

`address` must be a numeric IP literal; `bindSync()` never performs DNS
resolution (asynchronous name resolution being the only genuinely blocking part
of binding). Incoming datagrams continue to be delivered asynchronously via the
[`'message'`](#event-message) event. `bindSync()` always binds the socket's own handle and
does not participate in [`cluster`](12-cluster.md) handle sharing.

### `socket.close([callback])`

Added in: v0.1.99

- `callback` [`<Function>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Function) Called when the socket has been closed.

Close the underlying socket and stop listening for data on it. If a callback is
provided, it is added as a listener for the [`'close'`](#event-close) event.

### `socket[Symbol.asyncDispose]()`

Added in: v20.5.0, v18.18.0

**History**

| Version | Changes |
| --- | --- |
| v24.2.0 | No longer experimental. |

Calls [`socket.close()`](#socketclosecallback) and returns a promise that fulfills when the
socket has closed.

### `socket.connect(port[, address][, callback])`

Added in: v12.0.0

- `port` [`<integer>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type)
- `address` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type)
- `callback` [`<Function>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Function) Called when the connection is completed or on error.

Associates the `dgram.Socket` to a remote address and port. Every
message sent by this handle is automatically sent to that destination. Also,
the socket will only receive messages from that remote peer.
Trying to call `connect()` on an already connected socket will result
in an [`ERR_SOCKET_DGRAM_IS_CONNECTED`](22-errors-part3.md#err_socket_dgram_is_connected) exception. If `address` is not
provided, `'127.0.0.1'` (for `udp4` sockets) or `'::1'` (for `udp6` sockets)
will be used by default. Once the connection is complete, a `'connect'` event
is emitted and the optional `callback` function is called. In case of failure,
the `callback` is called or, failing this, an `'error'` event is emitted.

### `socket.connectSync(port[, address])`

Added in: v26.4.0

- `port` [`<integer>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type)
- `address` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) A numeric IP address to connect to. Unlike [`socket.connect()`](#socketconnectport-address-callback), no DNS resolution is performed, so a host name is not
  accepted. If omitted, `'127.0.0.1'` (for `udp4` sockets) or `'::1'` (for
  `udp6` sockets) is used.

The synchronous counterpart of [`socket.connect()`](#socketconnectport-address-callback). For a UDP socket
`connect(2)` only records the default peer address and is a local, non-blocking
system call, so the association is performed inline. Any error raised by the
call itself (for example `EAFNOSUPPORT` for a mismatched address family) is
thrown synchronously rather than reported via the `'error'` event. Because
`connect(2)` does not probe reachability, errors such as `ECONNREFUSED` are
still surfaced asynchronously on a later send or receive, exactly as for
[`socket.connect()`](#socketconnectport-address-callback):

```js
const dgram = require('node:dgram');

const socket = dgram.createSocket('udp4');
socket.connectSync(41234, '127.0.0.1');
console.log(socket.remoteAddress()); // { address: '127.0.0.1', family: 'IPv4', port: 41234 }
```

If the socket is still unbound it is bound synchronously first. After
`connectSync()` returns, [`socket.remoteAddress()`](#socketremoteaddress) is valid synchronously
and the `'connect'` event is emitted on the next tick. Trying to call
`connectSync()` on an already connected socket throws an
[`ERR_SOCKET_DGRAM_IS_CONNECTED`](22-errors-part3.md#err_socket_dgram_is_connected) exception, and calling it while an
asynchronous [`socket.bind()`](#socketbindport-address-callback) is still in progress throws an
[`ERR_SOCKET_ALREADY_BOUND`](22-errors-part3.md#err_socket_already_bound) exception.

`address` must be a numeric IP literal; `connectSync()` never performs DNS
resolution (asynchronous name resolution being the only genuinely blocking part
of connecting).

### `socket.disconnect()`

Added in: v12.0.0

A synchronous function that disassociates a connected `dgram.Socket` from
its remote address. Trying to call `disconnect()` on an unbound or already
disconnected socket will result in an [`ERR_SOCKET_DGRAM_NOT_CONNECTED`](22-errors-part3.md#err_socket_dgram_not_connected)
exception.

### `socket.dropMembership(multicastAddress[, multicastInterface])`

Added in: v0.6.9

- `multicastAddress` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type)
- `multicastInterface` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type)

Instructs the kernel to leave a multicast group at `multicastAddress` using the
`IP_DROP_MEMBERSHIP` socket option. This method is automatically called by the
kernel when the socket is closed or the process terminates, so most apps will
never have reason to call this.

If `multicastInterface` is not specified, the operating system will attempt to
drop membership on all valid interfaces.

### `socket.dropSourceSpecificMembership(sourceAddress, groupAddress[, multicastInterface])`

Added in: v13.1.0, v12.16.0

- `sourceAddress` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type)
- `groupAddress` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type)
- `multicastInterface` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type)

Instructs the kernel to leave a source-specific multicast channel at the given
`sourceAddress` and `groupAddress` using the `IP_DROP_SOURCE_MEMBERSHIP`
socket option. This method is automatically called by the kernel when the
socket is closed or the process terminates, so most apps will never have
reason to call this.

If `multicastInterface` is not specified, the operating system will attempt to
drop membership on all valid interfaces.

### `socket.getRecvBufferSize()`

Added in: v8.7.0

- Returns: [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) the `SO_RCVBUF` socket receive buffer size in bytes.

This method throws [`ERR_SOCKET_BUFFER_SIZE`](22-errors-part3.md#err_socket_buffer_size) if called on an unbound socket.

### `socket.getSendBufferSize()`

Added in: v8.7.0

- Returns: [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) the `SO_SNDBUF` socket send buffer size in bytes.

This method throws [`ERR_SOCKET_BUFFER_SIZE`](22-errors-part3.md#err_socket_buffer_size) if called on an unbound socket.

### `socket.getSendQueueSize()`

Added in: v18.8.0, v16.19.0

- Returns: [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) Number of bytes queued for sending.

### `socket.getSendQueueCount()`

Added in: v18.8.0, v16.19.0

- Returns: [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) Number of send requests currently in the queue awaiting
  to be processed.

### `socket.ref()`

Added in: v0.9.1

- Returns: [`<dgram.Socket>`](58-dgram.md#class-dgramsocket)

By default, binding a socket will cause it to block the Node.js process from
exiting as long as the socket is open. The `socket.unref()` method can be used
to exclude the socket from the reference counting that keeps the Node.js
process active. The `socket.ref()` method adds the socket back to the reference
counting and restores the default behavior.

Calling `socket.ref()` multiple times will have no additional effect.

The `socket.ref()` method returns a reference to the socket so calls can be
chained.

### `socket.remoteAddress()`

Added in: v12.0.0

- Returns: [`<Object>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Object)

Returns an object containing the `address`, `family`, and `port` of the remote
endpoint. This method throws an [`ERR_SOCKET_DGRAM_NOT_CONNECTED`](22-errors-part3.md#err_socket_dgram_not_connected) exception
if the socket is not connected.

### `socket.send(msg[, offset, length][, port][, address][, callback])`

Added in: v0.1.99

**History**

| Version | Changes |
| --- | --- |
| v17.0.0 | The `address` parameter now only accepts a `string`, `null` or `undefined`. |
| v14.5.0, v12.19.0 | The `msg` parameter can now be any `TypedArray` or `DataView`. |
| v12.0.0 | Added support for sending data on connected sockets. |
| v8.0.0 | The `msg` parameter can be an `Uint8Array` now. |
| v8.0.0 | The `address` parameter is always optional now. |
| v6.0.0 | On success, `callback` will now be called with an `error` argument of `null` rather than `0`. |
| v5.7.0 | The `msg` parameter can be an array now. Also, the `offset` and `length` parameters are optional now. |

- `msg` [`<Buffer>`](07-buffer-part1.md#class-buffer) | [`<TypedArray>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/TypedArray) | [`<DataView>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/DataView) | [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) | [`<Array>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Array) Message to be sent.
- `offset` [`<integer>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) Offset in the buffer where the message starts.
- `length` [`<integer>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) Number of bytes in the message.
- `port` [`<integer>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) Destination port.
- `address` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) Destination host name or IP address.
- `callback` [`<Function>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Function) Called when the message has been sent.

Broadcasts a datagram on the socket.
For connectionless sockets, the destination `port` and `address` must be
specified. Connected sockets, on the other hand, will use their associated
remote endpoint, so the `port` and `address` arguments must not be set.

The `msg` argument contains the message to be sent.
Depending on its type, different behavior can apply. If `msg` is a `Buffer`,
any `TypedArray` or a `DataView`,
the `offset` and `length` specify the offset within the `Buffer` where the
message begins and the number of bytes in the message, respectively.
If `msg` is a `String`, then it is automatically converted to a `Buffer`
with `'utf8'` encoding. With messages that
contain multi-byte characters, `offset` and `length` will be calculated with
respect to [byte length](07-buffer-part1.md#static-method-bufferbytelengthstring-encoding) and not the character position.
If `msg` is an array, `offset` and `length` must not be specified.

The `address` argument is a string. If the value of `address` is a host name,
DNS will be used to resolve the address of the host. If `address` is not
provided or otherwise nullish, `'127.0.0.1'` (for `udp4` sockets) or `'::1'`
(for `udp6` sockets) will be used by default.

If the socket has not been previously bound with a call to `bind`, the socket
is assigned a random port number and is bound to the "all interfaces" address
(`'0.0.0.0'` for `udp4` sockets, `'::0'` for `udp6` sockets.)

An optional `callback` function may be specified to as a way of reporting
DNS errors or for determining when it is safe to reuse the `buf` object.
DNS lookups delay the time to send for at least one tick of the
Node.js event loop.

The only way to know for sure that the datagram has been sent is by using a
`callback`. If an error occurs and a `callback` is given, the error will be
passed as the first argument to the `callback`. If a `callback` is not given,
the error is emitted as an `'error'` event on the `socket` object.

Offset and length are optional but both *must* be set if either are used.
They are supported only when the first argument is a `Buffer`, a `TypedArray`,
or a `DataView`.

This method throws [`ERR_SOCKET_BAD_PORT`](22-errors-part3.md#err_socket_bad_port) if called on an unbound socket.

Example of sending a UDP packet to a port on `localhost`;

*ES module version:*

```js
import dgram from 'node:dgram';
import { Buffer } from 'node:buffer';

const message = Buffer.from('Some bytes');
const client = dgram.createSocket('udp4');
client.send(message, 41234, 'localhost', (err) => {
  client.close();
});
```

*CommonJS version:*

```js
const dgram = require('node:dgram');
const { Buffer } = require('node:buffer');

const message = Buffer.from('Some bytes');
const client = dgram.createSocket('udp4');
client.send(message, 41234, 'localhost', (err) => {
  client.close();
});
```

Example of sending a UDP packet composed of multiple buffers to a port on
`127.0.0.1`;

*ES module version:*

```js
import dgram from 'node:dgram';
import { Buffer } from 'node:buffer';

const buf1 = Buffer.from('Some ');
const buf2 = Buffer.from('bytes');
const client = dgram.createSocket('udp4');
client.send([buf1, buf2], 41234, (err) => {
  client.close();
});
```

*CommonJS version:*

```js
const dgram = require('node:dgram');
const { Buffer } = require('node:buffer');

const buf1 = Buffer.from('Some ');
const buf2 = Buffer.from('bytes');
const client = dgram.createSocket('udp4');
client.send([buf1, buf2], 41234, (err) => {
  client.close();
});
```

Sending multiple buffers might be faster or slower, depending on the
application and operating system. Run benchmarks to
determine the optimal strategy on a case-by-case basis. Generally speaking,
however, sending multiple buffers is faster.

Example of sending a UDP packet using a socket connected to a port on
`localhost`:

*ES module version:*

```js
import dgram from 'node:dgram';
import { Buffer } from 'node:buffer';

const message = Buffer.from('Some bytes');
const client = dgram.createSocket('udp4');
client.connect(41234, 'localhost', (err) => {
  client.send(message, (err) => {
    client.close();
  });
});
```

*CommonJS version:*

```js
const dgram = require('node:dgram');
const { Buffer } = require('node:buffer');

const message = Buffer.from('Some bytes');
const client = dgram.createSocket('udp4');
client.connect(41234, 'localhost', (err) => {
  client.send(message, (err) => {
    client.close();
  });
});
```

#### Note about UDP datagram size

The maximum size of an IPv4/v6 datagram depends on the `MTU`
(Maximum Transmission Unit) and on the `Payload Length` field size.

- The `Payload Length` field is 16 bits wide, which means that a normal
  payload cannot exceed 64K octets including the internet header and data
  (65,507 bytes = 65,535 − 8 bytes UDP header − 20 bytes IP header);
  this is generally true for loopback interfaces, but such long datagram
  messages are impractical for most hosts and networks.
- The `MTU` is the largest size a given link layer technology can support for
  datagram messages. For any link, IPv4 mandates a minimum `MTU` of 68
  octets, while the recommended `MTU` for IPv4 is 576 (typically recommended
  as the `MTU` for dial-up type applications), whether they arrive whole or in
  fragments.

  For IPv6, the minimum `MTU` is 1280 octets. However, the mandatory minimum
  fragment reassembly buffer size is 1500 octets. The value of 68 octets is
  very small, since most current link layer technologies, like Ethernet, have a
  minimum `MTU` of 1500.

It is impossible to know in advance the MTU of each link through which
a packet might travel. Sending a datagram greater than the receiver `MTU` will
not work because the packet will get silently dropped without informing the
source that the data did not reach its intended recipient.

### `socket.setBroadcast(flag)`

Added in: v0.6.9

- `flag` [`<boolean>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#boolean_type)

Sets or clears the `SO_BROADCAST` socket option. When set to `true`, UDP
packets may be sent to a local interface's broadcast address.

This method throws `EBADF` if called on an unbound socket.

### `socket.setMulticastInterface(multicastInterface)`

Added in: v8.6.0

- `multicastInterface` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type)

*All references to scope in this section are referring to
[IPv6 Zone Indexes](https://en.wikipedia.org/wiki/IPv6_address#Scoped_literal_IPv6_addresses), which are defined by [RFC 4007](https://tools.ietf.org/html/rfc4007). In string form, an IP
with a scope index is written as `'IP%scope'` where scope is an interface name
or interface number.*

Sets the default outgoing multicast interface of the socket to a chosen
interface or back to system interface selection. The `multicastInterface` must
be a valid string representation of an IP from the socket's family.

For IPv4 sockets, this should be the IP configured for the desired physical
interface. All packets sent to multicast on the socket will be sent on the
interface determined by the most recent successful use of this call.

For IPv6 sockets, `multicastInterface` should include a scope to indicate the
interface as in the examples that follow. In IPv6, individual `send` calls can
also use explicit scope in addresses, so only packets sent to a multicast
address without specifying an explicit scope are affected by the most recent
successful use of this call.

This method throws `EBADF` if called on an unbound socket.

#### Example: IPv6 outgoing multicast interface

On most systems, where scope format uses the interface name:

```js
const socket = dgram.createSocket('udp6');

socket.bind(1234, () => {
  socket.setMulticastInterface('::%eth1');
});
```

On Windows, where scope format uses an interface number:

```js
const socket = dgram.createSocket('udp6');

socket.bind(1234, () => {
  socket.setMulticastInterface('::%2');
});
```

#### Example: IPv4 outgoing multicast interface

All systems use an IP of the host on the desired physical interface:

```js
const socket = dgram.createSocket('udp4');

socket.bind(1234, () => {
  socket.setMulticastInterface('10.0.0.2');
});
```

#### Call results

A call on a socket that is not ready to send or no longer open may throw a *Not
running* [`Error`](22-errors-part1.md#class-error).

If `multicastInterface` cannot be parsed into an IP then an *EINVAL*
[`System Error`](22-errors-part1.md#class-systemerror) is thrown.

On IPv4, if `multicastInterface` is a valid address but does not match any
interface, or if the address does not match the family then
a [`System Error`](22-errors-part1.md#class-systemerror) such as `EADDRNOTAVAIL` or `EPROTONOSUP` is thrown.

On IPv6, most errors with specifying or omitting scope will result in the socket
continuing to use (or returning to) the system's default interface selection.

A socket's address family's ANY address (IPv4 `'0.0.0.0'` or IPv6 `'::'`) can be
used to return control of the sockets default outgoing interface to the system
for future multicast packets.

### `socket.setMulticastLoopback(flag)`

Added in: v0.3.8

- `flag` [`<boolean>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#boolean_type)

Sets or clears the `IP_MULTICAST_LOOP` socket option. When set to `true`,
multicast packets will also be received on the local interface.

This method throws `EBADF` if called on an unbound socket.

### `socket.setMulticastTTL(ttl)`

Added in: v0.3.8

- `ttl` [`<integer>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type)

Sets the `IP_MULTICAST_TTL` socket option. While TTL generally stands for
"Time to Live", in this context it specifies the number of IP hops that a
packet is allowed to travel through, specifically for multicast traffic. Each
router or gateway that forwards a packet decrements the TTL. If the TTL is
decremented to 0 by a router, it will not be forwarded.

The `ttl` argument may be between 0 and 255. The default on most systems is `1`.

This method throws `EBADF` if called on an unbound socket.

### `socket.setRecvBufferSize(size)`

Added in: v8.7.0

- `size` [`<integer>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type)

Sets the `SO_RCVBUF` socket option. Sets the maximum socket receive buffer
in bytes.

This method throws [`ERR_SOCKET_BUFFER_SIZE`](22-errors-part3.md#err_socket_buffer_size) if called on an unbound socket.

### `socket.setSendBufferSize(size)`

Added in: v8.7.0

- `size` [`<integer>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type)

Sets the `SO_SNDBUF` socket option. Sets the maximum socket send buffer
in bytes.

This method throws [`ERR_SOCKET_BUFFER_SIZE`](22-errors-part3.md#err_socket_buffer_size) if called on an unbound socket.

### `socket.setTTL(ttl)`

Added in: v0.1.101

- `ttl` [`<integer>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type)

Sets the `IP_TTL` socket option. While TTL generally stands for "Time to Live",
in this context it specifies the number of IP hops that a packet is allowed to
travel through. Each router or gateway that forwards a packet decrements the
TTL. If the TTL is decremented to 0 by a router, it will not be forwarded.
Changing TTL values is typically done for network probes or when multicasting.

The `ttl` argument may be between 1 and 255. The default on most systems
is 64.

This method throws `EBADF` if called on an unbound socket.

### `socket.unref()`

Added in: v0.9.1

- Returns: [`<dgram.Socket>`](58-dgram.md#class-dgramsocket)

By default, binding a socket will cause it to block the Node.js process from
exiting as long as the socket is open. The `socket.unref()` method can be used
to exclude the socket from the reference counting that keeps the Node.js
process active, allowing the process to exit even if the socket is still
listening.

Calling `socket.unref()` multiple times will have no additional effect.

The `socket.unref()` method returns a reference to the socket so calls can be
chained.

## `node:dgram` module functions

### `dgram.createSocket(options[, callback])`

Added in: v0.11.13

**History**

| Version | Changes |
| --- | --- |
| v23.1.0, v22.12.0 | The `reusePort` option is supported. |
| v15.8.0 | AbortSignal support was added. |
| v11.4.0 | The `ipv6Only` option is supported. |
| v8.7.0 | The `recvBufferSize` and `sendBufferSize` options are supported now. |
| v8.6.0 | The `lookup` option is supported. |

- `options` [`<Object>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Object) Available options are:
  - `type` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) The family of socket. Must be either `'udp4'` or `'udp6'`.
    Required.
  - `reuseAddr` [`<boolean>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#boolean_type) When `true` [`socket.bind()`](#socketbindport-address-callback) will reuse the
    address, even if another process has already bound a socket on it, but
    only one socket can receive the data.
    **Default:** `false`.
  - `reusePort` [`<boolean>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#boolean_type) When `true` [`socket.bind()`](#socketbindport-address-callback) will reuse the
    port, even if another process has already bound a socket on it. Incoming
    datagrams are distributed to listening sockets. The option is available
    only on some platforms, such as Linux 3.9+, DragonFlyBSD 3.6+, FreeBSD 12.0+,
    Solaris 11.4, and AIX 7.2.5+. On unsupported platforms, this option raises
    an error when the socket is bound.
    **Default:** `false`.
  - `ipv6Only` [`<boolean>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#boolean_type) Setting `ipv6Only` to `true` will
    disable dual-stack support, i.e., binding to address `::` won't make
    `0.0.0.0` be bound. **Default:** `false`.
  - `recvBufferSize` [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) Sets the `SO_RCVBUF` socket value.
  - `sendBufferSize` [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) Sets the `SO_SNDBUF` socket value.
  - `lookup` [`<Function>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Function) Custom lookup function. **Default:** [`dns.lookup()`](19-dns.md#dnslookuphostname-options-callback).
    When the default is used, a literal IP address of the socket's family
    resolves to itself without calling [`dns.lookup()`](19-dns.md#dnslookuphostname-options-callback).
  - `signal` [`<AbortSignal>`](26-globals.md#class-abortsignal) An AbortSignal that may be used to close a socket.
  - `receiveBlockList` [`<net.BlockList>`](38-net.md#class-netblocklist) `receiveBlockList` can be used for discarding
    inbound datagram to specific IP addresses, IP ranges, or IP subnets. This does not
    work if the server is behind a reverse proxy, NAT, etc. because the address
    checked against the blocklist is the address of the proxy, or the one
    specified by the NAT.
  - `sendBlockList` [`<net.BlockList>`](38-net.md#class-netblocklist) `sendBlockList` can be used for disabling outbound
    access to specific IP addresses, IP ranges, or IP subnets.
- `callback` [`<Function>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Function) Attached as a listener for `'message'` events. Optional.
- Returns: [`<dgram.Socket>`](58-dgram.md#class-dgramsocket)

Creates a `dgram.Socket` object. Once the socket is created, calling
[`socket.bind()`](#socketbindport-address-callback) will instruct the socket to begin listening for datagram
messages. When `address` and `port` are not passed to [`socket.bind()`](#socketbindport-address-callback) the
method will bind the socket to the "all interfaces" address on a random port
(it does the right thing for both `udp4` and `udp6` sockets). The bound address
and port can be retrieved using [`socket.address().address`](#socketaddress) and
[`socket.address().port`](#socketaddress).

If the `signal` option is enabled, calling `.abort()` on the corresponding
`AbortController` is similar to calling `.close()` on the socket:

```js
const controller = new AbortController();
const { signal } = controller;
const server = dgram.createSocket({ type: 'udp4', signal });
server.on('message', (msg, rinfo) => {
  console.log(`server got: ${msg} from ${rinfo.address}:${rinfo.port}`);
});
// Later, when you want to close the server.
controller.abort();
```

### `dgram.createSocket(type[, callback])`

Added in: v0.1.99

- `type` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) Either `'udp4'` or `'udp6'`.
- `callback` [`<Function>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Function) Attached as a listener to `'message'` events.
- Returns: [`<dgram.Socket>`](58-dgram.md#class-dgramsocket)

Creates a `dgram.Socket` object of the specified `type`.

Once the socket is created, calling [`socket.bind()`](#socketbindport-address-callback) will instruct the
socket to begin listening for datagram messages. When `address` and `port` are
not passed to [`socket.bind()`](#socketbindport-address-callback) the method will bind the socket to the "all
interfaces" address on a random port (it does the right thing for both `udp4`
and `udp6` sockets). The bound address and port can be retrieved using
[`socket.address().address`](#socketaddress) and [`socket.address().port`](#socketaddress).

[Contents](../README.md) | [Previous: TTY](57-tty.md) | [Next: URL](59-url.md)
