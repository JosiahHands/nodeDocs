[Contents](../README.md) | [Previous: Modules: TypeScript](37-typescript.md) | [Next: OS](39-os.md)

Source: [https://nodejs.org/docs/latest/api/net.html](https://nodejs.org/docs/latest/api/net.html)

# Net

[Stability: 2](01-documentation.md#stability-index) - Stable

The `node:net` module provides an asynchronous network API for creating stream-based
TCP or [IPC](#ipc-support) servers ([`net.createServer()`](#netcreateserveroptions-connectionlistener)) and clients
([`net.createConnection()`](#netcreateconnection)).

It can be accessed using:

*ES module version:*

```js
import net from 'node:net';
```

*CommonJS version:*

```js
const net = require('node:net');
```

## IPC support

**History**

| Version | Changes |
| --- | --- |
| v20.8.0 | Support binding to abstract Unix domain socket path like `\0abstract`. We can bind '\0' for Node.js `< v20.4.0`. |

The `node:net` module supports IPC with named pipes on Windows, and Unix domain
sockets on other operating systems.

### Identifying paths for IPC connections

[`net.connect()`](#netconnect), [`net.createConnection()`](#netcreateconnection), [`server.listen()`](#serverlisten), and
[`socket.connect()`](#socketconnect) take a `path` parameter to identify IPC endpoints.

On Unix, the local domain is also known as the Unix domain. The path is a
file system pathname. It will throw an error when the length of pathname is
greater than the length of `sizeof(sockaddr_un.sun_path)`. Typical values are
107 bytes on Linux and 103 bytes on macOS. If a Node.js API abstraction creates
the Unix domain socket, it will unlink the Unix domain socket as well. For
example, [`net.createServer()`](#netcreateserveroptions-connectionlistener) may create a Unix domain socket and
[`server.close()`](#serverclosecallback) will unlink it. But if a user creates the Unix domain
socket outside of these abstractions, the user will need to remove it. The same
applies when a Node.js API creates a Unix domain socket but the program then
crashes. In short, a Unix domain socket will be visible in the file system and
will persist until unlinked. On Linux, You can use Unix abstract socket by adding
`\0` to the beginning of the path, such as `\0abstract`. The path to the Unix
abstract socket is not visible in the file system and it will disappear automatically
when all open references to the socket are closed.

On Windows, the local domain is implemented using a named pipe. The path *must*
refer to an entry in `\\?\pipe\` or `\\.\pipe\`. Any characters are permitted,
but the latter may do some processing of pipe names, such as resolving `..`
sequences. Despite how it might look, the pipe namespace is flat. Pipes will
*not persist*. They are removed when the last reference to them is closed.
Unlike Unix domain sockets, Windows will close and remove the pipe when the
owning process exits.

JavaScript string escaping requires paths to be specified with extra backslash
escaping such as:

```js
net.createServer().listen(
  path.join('\\\\?\\pipe', process.cwd(), 'myctl'));
```

## Class: `net.BlockList`

Added in: v15.0.0, v14.18.0

The `BlockList` object can be used with some network APIs to specify rules for
disabling inbound or outbound access to specific IP addresses, IP ranges, or
IP subnets.

### `blockList.addAddress(address[, type])`

Added in: v15.0.0, v14.18.0

- `address` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) | [`<net.SocketAddress>`](38-net.md#class-netsocketaddress) An IPv4 or IPv6 address.
- `type` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) Either `'ipv4'` or `'ipv6'`. **Default:** `'ipv4'`.

Adds a rule to block the given IP address.

### `blockList.addAddresses(addresses[, type])`

Added in: v26.8.0

- `addresses` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type)[] | [`<net.SocketAddress>`](38-net.md#class-netsocketaddress)[] An array of IPv4 or IPv6
  addresses.
- `type` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) Either `'ipv4'` or `'ipv6'`. **Default:** `'ipv4'`.

Adds multiple address rules to the block list in a single operation.
This is more efficient than calling `blockList.addAddress()` repeatedly
when adding a large number of individual addresses, as the addresses
are inserted under a single internal lock acquisition.

### `blockList.addCIDR(cidr)`

Added in: v26.8.0

- `cidr` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) An IPv4 or IPv6 subnet in CIDR notation (e.g. `'10.0.0.0/8'` or `'2001:db8::/32'`).

Adds a subnet rule using CIDR notation. The address family is automatically
detected from the address (IPv6 if the address contains `':'`, IPv4
otherwise). This is equivalent to calling `blockList.addSubnet()` with
the parsed network address, prefix length, and family.

### `blockList.addCIDRs(cidrs)`

Added in: v26.8.0

- `cidrs` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type)[] An array of IPv4 or IPv6 subnets in CIDR notation.

Adds multiple subnet rules using CIDR notation in a single call. The address
family for each entry is automatically detected. This is equivalent to
calling `blockList.addCIDR()` for each element of the array.

### `blockList.addRange(start, end[, type])`

Added in: v15.0.0, v14.18.0

- `start` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) | [`<net.SocketAddress>`](38-net.md#class-netsocketaddress) The starting IPv4 or IPv6 address in the
  range.
- `end` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) | [`<net.SocketAddress>`](38-net.md#class-netsocketaddress) The ending IPv4 or IPv6 address in the range.
- `type` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) Either `'ipv4'` or `'ipv6'`. **Default:** `'ipv4'`.

Adds a rule to block a range of IP addresses from `start` (inclusive) to
`end` (inclusive).

### `blockList.addSubnet(net, prefix[, type])`

Added in: v15.0.0, v14.18.0

- `net` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) | [`<net.SocketAddress>`](38-net.md#class-netsocketaddress) The network IPv4 or IPv6 address.
- `prefix` [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) The number of CIDR prefix bits. For IPv4, this
  must be a value between `0` and `32`. For IPv6, this must be between
  `0` and `128`.
- `type` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) Either `'ipv4'` or `'ipv6'`. **Default:** `'ipv4'`.

Adds a rule to block a range of IP addresses specified as a subnet mask.

### `blockList.check(address[, type])`

Added in: v15.0.0, v14.18.0

- `address` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) | [`<net.SocketAddress>`](38-net.md#class-netsocketaddress) The IP address to check
- `type` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) Either `'ipv4'` or `'ipv6'`. **Default:** `'ipv4'`.
- Returns: [`<boolean>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#boolean_type)

Returns `true` if the given IP address matches any of the rules added to the
`BlockList`.

```js
const blockList = new net.BlockList();
blockList.addAddress('123.123.123.123');
blockList.addRange('10.0.0.1', '10.0.0.10');
blockList.addSubnet('8592:757c:efae:4e45::', 64, 'ipv6');

console.log(blockList.check('123.123.123.123'));  // Prints: true
console.log(blockList.check('10.0.0.3'));  // Prints: true
console.log(blockList.check('222.111.111.222'));  // Prints: false

// IPv6 notation for IPv4 addresses works:
console.log(blockList.check('::ffff:7b7b:7b7b', 'ipv6')); // Prints: true
console.log(blockList.check('::ffff:123.123.123.123', 'ipv6')); // Prints: true
```

### `blockList.clear()`

Clears all rules from the `BlockList`.

### `blockList.fromJSON(value)`

Stability: 1.2 - Release candidate

```js
const blockList = new net.BlockList();
const data = [
  'Subnet: IPv4 192.168.1.0/24',
  'Address: IPv4 10.0.0.5',
  'Range: IPv4 192.168.2.1-192.168.2.10',
  'Range: IPv4 10.0.0.1-10.0.0.10',
];
blockList.fromJSON(data);
blockList.fromJSON(JSON.stringify(data));
```

- `value` Blocklist.rules

### `BlockList.isBlockList(value)`

Added in: v23.4.0, v22.13.0

- `value` [`<any>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#Data_types) Any JS value
- Returns [`<boolean>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#boolean_type) `true` if the `value` is a `net.BlockList`.

### `BlockList.PRIVATE_RANGES`

Added in: v26.8.0

- Type: [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type)[]

A frozen array of CIDR strings representing private, loopback, and link-local
IP address ranges. This can be passed to `blockList.addCIDRs()` to quickly
populate a blocklist with all non-routable address ranges.

The included ranges are:

- `10.0.0.0/8` — RFC 1918 private IPv4
- `172.16.0.0/12` — RFC 1918 private IPv4
- `192.168.0.0/16` — RFC 1918 private IPv4
- `127.0.0.0/8` — IPv4 loopback
- `::1/128` — IPv6 loopback
- `169.254.0.0/16` — IPv4 link-local
- `fe80::/10` — IPv6 link-local
- `fc00::/7` — IPv6 unique local (ULA)

```js
const blockList = new net.BlockList();
blockList.addCIDRs(net.BlockList.PRIVATE_RANGES);

console.log(blockList.check('10.0.0.1'));      // Prints: true
console.log(blockList.check('127.0.0.1'));     // Prints: true
console.log(blockList.check('8.8.8.8'));       // Prints: false
```

### `blockList.removeAddress(address[, type])`

Added in: v26.8.0

- `address` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) | [`<net.SocketAddress>`](38-net.md#class-netsocketaddress) An IPv4 or IPv6 address.
- `type` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) Either `'ipv4'` or `'ipv6'`. **Default:** `'ipv4'`.

Removes a rule that was previously added with `blockList.addAddress()`. The
address must match exactly the value used when the rule was added. If the
specified address does not exist, this is a no-op.

### `blockList.removeCIDR(cidr)`

Added in: v26.8.0

- `cidr` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) An IPv4 or IPv6 subnet in CIDR notation (e.g. `'10.0.0.0/8'` or `'2001:db8::/32'`).

Removes a subnet rule using CIDR notation. The address family is automatically
detected from the address. This is equivalent to calling
`blockList.removeSubnet()` with the parsed network address, prefix length,
and family. If the specified subnet does not exist, this is a no-op.

### `blockList.removeRange(start, end[, type])`

Added in: v26.8.0

- `start` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) | [`<net.SocketAddress>`](38-net.md#class-netsocketaddress) The starting IPv4 or IPv6 address in the
  range.
- `end` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) | [`<net.SocketAddress>`](38-net.md#class-netsocketaddress) The ending IPv4 or IPv6 address in the range.
- `type` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) Either `'ipv4'` or `'ipv6'`. **Default:** `'ipv4'`.

Removes a rule that was previously added with `blockList.addRange()`. The `start`
and `end` addresses must match exactly the values used when the rule was added.
If the specified range does not exist, this is a no-op.

### `blockList.removeSubnet(net, prefix[, type])`

Added in: v26.8.0

- `net` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) | [`<net.SocketAddress>`](38-net.md#class-netsocketaddress) The network IPv4 or IPv6 address.
- `prefix` [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) The number of CIDR prefix bits. For IPv4, this
  must be a value between `0` and `32`. For IPv6, this must be between
  `0` and `128`.
- `type` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) Either `'ipv4'` or `'ipv6'`. **Default:** `'ipv4'`.

Removes a rule that was previously added with `blockList.addSubnet()`. The
network address and prefix must match exactly the values used when the rule was
added. If the specified subnet does not exist, this is a no-op.

### `blockList.rules`

Added in: v15.0.0, v14.18.0

- Type: [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type)[]

The list of rules added to the blocklist.

### `blockList.size`

Added in: v26.8.0

- Type: [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type)

The number of rules in the blocklist. This is equivalent to
`blockList.rules.length` but does not allocate the rules array.

### `blockList.toJSON()`

Stability: 1.2 - Release candidate

- Returns Blocklist.rules

## Class: `net.SocketAddress`

Added in: v15.14.0, v14.18.0

### `new net.SocketAddress([options])`

Added in: v15.14.0, v14.18.0

- `options` [`<Object>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Object)
  - `address` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) The network address as either an IPv4 or IPv6 string. **Default**: `'127.0.0.1'` if `family` is `'ipv4'`; `'::'` if `family` is
    `'ipv6'`.
  - `family` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) One of either `'ipv4'` or `'ipv6'`.
    **Default**: `'ipv4'`.
  - `flowlabel` [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) An IPv6 flow-label used only if `family` is `'ipv6'`.
  - `port` [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) An IP port.

### `socketaddress.address`

Added in: v15.14.0, v14.18.0

- Type: [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type)

### `socketaddress.family`

Added in: v15.14.0, v14.18.0

- Type: [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) Either `'ipv4'` or `'ipv6'`.

### `socketaddress.flowlabel`

Added in: v15.14.0, v14.18.0

- Type: [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type)

### `socketaddress.port`

Added in: v15.14.0, v14.18.0

- Type: [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type)

### `SocketAddress.parse(input)`

Added in: v23.4.0, v22.13.0

- `input` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) An input string containing an IP address and optional port,
  e.g. `123.1.2.3:1234` or `[1::1]:1234`.
- Returns: [`<net.SocketAddress>`](38-net.md#class-netsocketaddress) Returns a `SocketAddress` if parsing was successful.
  Otherwise returns `undefined`.

## Class: `net.Server`

Added in: v0.1.90

- Extends: [`<EventEmitter>`](23-events.md#class-eventemitter)

This class is used to create a TCP or [IPC](#ipc-support) server.

A listening TCP `net.Server` can be transferred to a worker thread by listing it
in the `transferList` of a [`worker_threads`](67-worker-threads.md) `postMessage()` call. This moves
the underlying listening socket to the receiving thread, where it resumes
accepting connections. See [Transferring TCP handles to other threads](#transferring-tcp-handles-to-other-threads).

### `new net.Server([options][, connectionListener])`

- `options` [`<Object>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Object) See [`net.createServer([options][, connectionListener])`](#netcreateserveroptions-connectionlistener).
- `connectionListener` [`<Function>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Function) Automatically set as a listener for the [`'connection'`](#event-connection) event.
- Returns: [`<net.Server>`](38-net.md#class-netserver)

`net.Server` is an [`EventEmitter`](23-events.md#class-eventemitter) with the following events:

### Event: `'close'`

Added in: v0.5.0

Emitted when the server closes. If connections exist, this
event is not emitted until all connections are ended.

### Event: `'connection'`

Added in: v0.1.90

- Type: [`<net.Socket>`](38-net.md#class-netsocket) The connection object

Emitted when a new connection is made. `socket` is an instance of
`net.Socket`.

### Event: `'error'`

Added in: v0.1.90

- Type: [`<Error>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Error)

Emitted when an error occurs. Unlike [`net.Socket`](#class-netsocket), the [`'close'`](#event-close)
event will **not** be emitted directly following this event unless
[`server.close()`](#serverclosecallback) is manually called. See the example in discussion of
[`server.listen()`](#serverlisten).

### Event: `'listening'`

Added in: v0.1.90

Emitted when the server has been bound after calling [`server.listen()`](#serverlisten).

### Event: `'drop'`

Added in: v18.6.0, v16.17.0

When the number of connections reaches the threshold of `server.maxConnections`,
the server will drop new connections and emit `'drop'` event instead. If it is a
TCP server, the argument is as follows, otherwise the argument is `undefined`.

- `data` [`<Object>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Object) The argument passed to event listener.
  - `localAddress` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) Local address.
  - `localPort` [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) Local port.
  - `localFamily` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) Local family.
  - `remoteAddress` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) Remote address.
  - `remotePort` [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) Remote port.
  - `remoteFamily` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) Remote IP family. `'IPv4'` or `'IPv6'`.

### `server.address()`

Added in: v0.1.90

**History**

| Version | Changes |
| --- | --- |
| v18.4.0 | The `family` property now returns a string instead of a number. |
| v18.0.0 | The `family` property now returns a number instead of a string. |

- Returns: [`<Object>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Object) | [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) | [`<null>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#null_type)

Returns the bound `address`, the address `family` name, and `port` of the server
as reported by the operating system if listening on an IP socket
(useful to find which port was assigned when getting an OS-assigned address):
`{ port: 12346, family: 'IPv4', address: '127.0.0.1' }`.

For a server listening on a pipe or Unix domain socket, the name is returned
as a string.

```js
const server = net.createServer((socket) => {
  socket.end('goodbye\n');
}).on('error', (err) => {
  // Handle errors here.
  throw err;
});

// Grab an arbitrary unused port.
server.listen(() => {
  console.log('opened server on', server.address());
});
```

`server.address()` returns `null` before the `'listening'` event has been
emitted or after calling `server.close()`.

### `server.close([callback])`

Added in: v0.1.90

- `callback` [`<Function>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Function) Called when the server is closed.
- Returns: [`<net.Server>`](38-net.md#class-netserver)

Stops the server from accepting new connections and keeps existing
connections. This function is asynchronous, the server is finally closed
when all connections are ended and the server emits a [`'close'`](#event-close) event.
The optional `callback` will be called once the `'close'` event occurs. Unlike
that event, it will be called with an `Error` as its only argument if the server
was not open when it was closed.

### `server[Symbol.asyncDispose]()`

Added in: v20.5.0, v18.18.0

**History**

| Version | Changes |
| --- | --- |
| v24.2.0 | No longer experimental. |

Calls [`server.close()`](#serverclosecallback) and returns a promise that fulfills when the
server has closed.

### `server.getConnections(callback)`

Added in: v0.9.7

- `callback` [`<Function>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Function)
- Returns: [`<net.Server>`](38-net.md#class-netserver)

Asynchronously get the number of concurrent connections on the server. Works
when sockets were sent to forks.

Callback should take two arguments `err` and `count`.

### `server.listen()`

Start a server listening for connections. A `net.Server` can be a TCP or
an [IPC](#ipc-support) server depending on what it listens to.

Possible signatures:

- [`server.listen(handle[, backlog][, callback])`](#serverlistenhandle-backlog-callback)
- [`server.listen(options[, callback])`](#serverlistenoptions-callback)
- [`server.listen(path[, backlog][, callback])`](#serverlistenpath-backlog-callback)
  for [IPC](#ipc-support) servers
- [`server.listen([port[, host[, backlog]]][, callback])`](#serverlistenport-host-backlog-callback)
  for TCP servers

This function is asynchronous. When the server starts listening, the
[`'listening'`](#event-listening) event will be emitted. The last parameter `callback`
will be added as a listener for the [`'listening'`](#event-listening) event.

All `listen()` methods can take a `backlog` parameter to specify the maximum
length of the queue of pending connections. The actual length will be determined
by the OS through sysctl settings such as `tcp_max_syn_backlog` and `somaxconn`
on Linux. The default value of this parameter is 511 (not 512).

All [`net.Socket`](#class-netsocket) are set to `SO_REUSEADDR` (see [`socket(7)`](https://man7.org/linux/man-pages/man7/socket.7.html) for
details).

The `server.listen()` method can be called again if and only if there was an
error during the first `server.listen()` call or `server.close()` has been
called. Otherwise, an `ERR_SERVER_ALREADY_LISTEN` error will be thrown.

One of the most common errors raised when listening is `EADDRINUSE`.
This happens when another server is already listening on the requested
`port`/`path`/`handle`. One way to handle this would be to retry
after a certain amount of time:

```js
server.on('error', (e) => {
  if (e.code === 'EADDRINUSE') {
    console.error('Address in use, retrying...');
    setTimeout(() => {
      server.close();
      server.listen(PORT, HOST);
    }, 1000);
  }
});
```

#### `server.listen(handle[, backlog][, callback])`

Added in: v0.5.10

- `handle` [`<Object>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Object)
- `backlog` [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) Common parameter of [`server.listen()`](#serverlisten) functions
- `callback` [`<Function>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Function)
- Returns: [`<net.Server>`](38-net.md#class-netserver)

Start a server listening for connections on a given `handle` that has
already been bound to a port, a Unix domain socket, or a Windows named pipe.

The `handle` object can be either a server, a socket (anything with an
underlying `_handle` member), a [`BoundSocket`](#class-netboundsocket), or an object with an `fd`
member that is a valid file descriptor.

When `handle` is a [`BoundSocket`](#class-netboundsocket), the server adopts the already-bound
socket and starts listening on it. Adoption consumes the bound socket (see
[ownership transfer](#class-netboundsocket)).

Listening on a file descriptor is not supported on Windows.

#### `server.listen(options[, callback])`

Added in: v0.11.14

**History**

| Version | Changes |
| --- | --- |
| v23.1.0, v22.12.0 | The `reusePort` option is supported. |
| v15.6.0 | AbortSignal support was added. |
| v11.4.0 | The `ipv6Only` option is supported. |

- `options` [`<Object>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Object) Required. Supports the following properties:
  - `backlog` [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) Common parameter of [`server.listen()`](#serverlisten)
    functions.
  - `exclusive` [`<boolean>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#boolean_type) **Default:** `false`
  - `handle` [`<net.BoundSocket>`](38-net.md#class-netboundsocket) A pre-bound [`BoundSocket`](#class-netboundsocket). The server adopts
    the already-bound socket and listens on it, ignoring `host`, `port`, and
    `path`. Adoption consumes the bound socket (see
    [ownership transfer](#class-netboundsocket)).
  - `host` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type)
  - `ipv6Only` [`<boolean>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#boolean_type) For TCP servers, setting `ipv6Only` to `true` will
    disable dual-stack support, i.e., binding to host `::` won't make
    `0.0.0.0` be bound. **Default:** `false`.
  - `reusePort` [`<boolean>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#boolean_type) For TCP servers, setting `reusePort` to `true` allows
    multiple sockets on the same host to bind to the same port. Incoming connections
    are distributed by the operating system to listening sockets. This option is
    available only on some platforms, such as Linux 3.9+, DragonFlyBSD 3.6+, FreeBSD 12.0+,
    Solaris 11.4, and AIX 7.2.5+. On unsupported platforms, this option raises
    an error. **Default:** `false`.
  - `path` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) Will be ignored if `port` is specified. See
    [Identifying paths for IPC connections](#identifying-paths-for-ipc-connections).
  - `port` [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type)
  - `readableAll` [`<boolean>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#boolean_type) For IPC servers makes the pipe readable
    for all users. **Default:** `false`.
  - `signal` [`<AbortSignal>`](26-globals.md#class-abortsignal) An AbortSignal that may be used to close a listening
    server.
  - `writableAll` [`<boolean>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#boolean_type) For IPC servers makes the pipe writable
    for all users. **Default:** `false`.
- `callback` [`<Function>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Function)
  functions.
- Returns: [`<net.Server>`](38-net.md#class-netserver)

If `handle` is specified, the server adopts that pre-bound socket. Otherwise, if
`port` is specified, it behaves the same as
[`server.listen([port[, host[, backlog]]][, callback])`](#serverlistenport-host-backlog-callback).
Otherwise, if `path` is specified, it behaves the same as
[`server.listen(path[, backlog][, callback])`](#serverlistenpath-backlog-callback).
If none of them is specified, an error will be thrown.

> Using the `signal` option to destroy a long-lived server as a resource cleanup
> mechanism is deprecated. The `signal` option remains appropriate for
> cancellation, externally propagated aborts, and timeouts. See
> [DEP0209](17-deprecations.md#dep0209-using-abortsignal-to-dispose-of-resources).

If `exclusive` is `false` (default), then cluster workers will use the same
underlying handle, allowing connection handling duties to be shared. When
`exclusive` is `true`, the handle is not shared, and attempted port sharing
results in an error. An example which listens on an exclusive port is
shown below.

```js
server.listen({
  host: 'localhost',
  port: 80,
  exclusive: true,
});
```

When `exclusive` is `true` and the underlying handle is shared, it is
possible that several workers query a handle with different backlogs.
In this case, the first `backlog` passed to the master process will be used.

Starting an IPC server as root may cause the server path to be inaccessible for
unprivileged users. Using `readableAll` and `writableAll` will make the server
accessible for all users.

If the `signal` option is enabled, calling `.abort()` on the corresponding
`AbortController` is similar to calling `.close()` on the server:

```js
const controller = new AbortController();
server.listen({
  host: 'localhost',
  port: 80,
  signal: controller.signal,
});
// Later, when you want to close the server.
controller.abort();
```

#### `server.listen(path[, backlog][, callback])`

Added in: v0.1.90

- `path` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) Path the server should listen to. See [Identifying paths for IPC connections](#identifying-paths-for-ipc-connections).
- `backlog` [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) Common parameter of [`server.listen()`](#serverlisten) functions.
- `callback` [`<Function>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Function).
- Returns: [`<net.Server>`](38-net.md#class-netserver)

Start an [IPC](#ipc-support) server listening for connections on the given `path`.

#### `server.listen([port[, host[, backlog]]][, callback])`

Added in: v0.1.90

- `port` [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type)
- `host` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type)
- `backlog` [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) Common parameter of [`server.listen()`](#serverlisten) functions.
- `callback` [`<Function>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Function).
- Returns: [`<net.Server>`](38-net.md#class-netserver)

Start a TCP server listening for connections on the given `port` and `host`.

If `port` is omitted or is 0, the operating system will assign an arbitrary
unused port, which can be retrieved by using `server.address().port`
after the [`'listening'`](#event-listening) event has been emitted.

If `host` is omitted, the server will accept connections on the
[unspecified IPv6 address](https://en.wikipedia.org/wiki/IPv6_address#Unspecified_address) (`::`) when IPv6 is available, or the
[unspecified IPv4 address](https://en.wikipedia.org/wiki/0.0.0.0) (`0.0.0.0`) otherwise.

In most operating systems, listening to the [unspecified IPv6 address](https://en.wikipedia.org/wiki/IPv6_address#Unspecified_address) (`::`)
may cause the `net.Server` to also listen on the [unspecified IPv4 address](https://en.wikipedia.org/wiki/0.0.0.0)
(`0.0.0.0`).

### `server.listening`

Added in: v5.7.0

- Type: [`<boolean>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#boolean_type) Indicates whether or not the server is listening for connections.

### `server.maxConnections`

Added in: v0.2.0

**History**

| Version | Changes |
| --- | --- |
| v21.0.0 | Setting `maxConnections` to `0` drops all the incoming connections. Previously, it was interpreted as `Infinity`. |

- Type: [`<integer>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type)

When the number of connections reaches the `server.maxConnections` threshold:

1. If the process is not running in cluster mode, Node.js will close the connection.
2. If the process is running in cluster mode, Node.js will, by default, route the connection to another worker process. To close the connection instead, set [`server.dropMaxConnection`](#serverdropmaxconnection) to `true`.

It is not recommended to use this option once a socket has been sent to a child
with [`child_process.fork()`](11-child-process.md#child_processforkmodulepath-args-options).

### `server.dropMaxConnection`

Added in: v23.1.0, v22.12.0

- Type: [`<boolean>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#boolean_type)

Set this property to `true` to begin closing connections once the number of connections reaches the [`server.maxConnections`](#servermaxconnections) threshold. This setting is only effective in cluster mode.

### `server.ref()`

Added in: v0.9.1

- Returns: [`<net.Server>`](38-net.md#class-netserver)

Opposite of `unref()`, calling `ref()` on a previously `unref`ed server will
*not* let the program exit if it's the only server left (the default behavior).
If the server is `ref`ed calling `ref()` again will have no effect.

### `server.unref()`

Added in: v0.9.1

- Returns: [`<net.Server>`](38-net.md#class-netserver)

Calling `unref()` on a server will allow the program to exit if this is the only
active server in the event system. If the server is already `unref`ed calling
`unref()` again will have no effect.

## Class: `net.Socket`

Added in: v0.3.4

- Extends: [`<stream.Duplex>`](51-stream.md#class-streamduplex)

This class is an abstraction of a TCP socket or a streaming [IPC](#ipc-support) endpoint
(uses named pipes on Windows, and Unix domain sockets otherwise). It is also
an [`EventEmitter`](23-events.md#class-eventemitter).

A `net.Socket` can be created by the user and used directly to interact with
a server. For example, it is returned by [`net.createConnection()`](#netcreateconnection),
so the user can use it to talk to the server.

It can also be created by Node.js and passed to the user when a connection
is received. For example, it is passed to the listeners of a
[`'connection'`](#event-connection) event emitted on a [`net.Server`](#class-netserver), so the user can use
it to interact with the client.

### Transferring TCP handles to other threads

A connected TCP `net.Socket` can be moved to another thread by listing it in the
`transferList` of a [`worker_threads`](67-worker-threads.md) `postMessage()` call. After the
transfer, the source socket is destroyed on the sending thread (further use
fails with `ERR_STREAM_DESTROYED` rather than silently dropping data), and the
socket continues to work on the receiving thread. This makes it possible to
accept connections on one thread and distribute them across a pool of worker
threads, for example to build a `node:cluster`-like model on top of worker
threads.

The socket must be a freshly accepted or created TCP connection: it must still
be attached to a live handle, must not be connecting or destroyed, and must not
have started reading or have buffered data. Otherwise `postMessage()` throws
`ERR_WORKER_HANDLE_NOT_TRANSFERABLE`. Only TCP sockets are supported.

```js
const net = require('node:net');
const { Worker } = require('node:worker_threads');

// worker.js receives `{ socket }` messages and handles each connection.
const worker = new Worker('./worker.js');

const server = net.createServer((socket) => {
  // Hand the freshly accepted connection off to the worker thread.
  worker.postMessage({ socket }, [socket]);
});
server.listen(8000);
```

A listening [`net.Server`](#class-netserver) can be transferred the same way, which moves the
listening socket itself (and its pending accept queue) to the receiving thread.

An un-adopted TCP [`BoundSocket`](#class-netboundsocket) can also be transferred, which moves the
bound (but not yet listening or connected) socket. This allows a port to be
reserved synchronously on one thread and adopted by a server or outgoing
connection on another. Pipe binds are not transferable. After the transfer, the
source `BoundSocket` behaves as if it had been adopted: `address()`, `fd()` and
`close()` throw [`ERR_SOCKET_HANDLE_ADOPTED`](22-errors.md#err_socket_handle_adopted).

### `new net.Socket([options])`

Added in: v0.3.4

**History**

| Version | Changes |
| --- | --- |
| v25.6.0 | Added `typeOfService` option. |
| v15.14.0 | AbortSignal support was added. |
| v12.10.0 | Added `onread` option. |

- `options` [`<Object>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Object) Available options are:
  - `allowHalfOpen` [`<boolean>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#boolean_type) If set to `false`, then the socket will
    automatically end the writable side when the readable side ends. See
    [`net.createServer()`](#netcreateserveroptions-connectionlistener) and the [`'end'`](#event-end) event for details. **Default:**
    `false`.
  - `blockList` [`<net.BlockList>`](38-net.md#class-netblocklist) `blockList` can be used for disabling outbound
    access to specific IP addresses, IP ranges, or IP subnets.
  - `fd` [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) If specified, wrap around an existing socket with
    the given file descriptor, otherwise a new socket will be created.
  - `handle` [`<net.BoundSocket>`](38-net.md#class-netboundsocket) If specified, wrap around the bound socket from a [`BoundSocket`](#class-netboundsocket). A subsequent
    [`socket.connect()`](#socketconnect) uses the bound socket as the
    connection's source binding (honoring the bound local address and port).
    Adoption consumes the bound socket (see
    [ownership transfer](#class-netboundsocket)).
  - `keepAlive` [`<boolean>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#boolean_type) If set to `true`, it enables keep-alive functionality on
    the socket immediately after the connection is established, similarly on what
    is done in [`socket.setKeepAlive()`](#socketsetkeepalive). **Default:** `false`.
  - `keepAliveInitialDelay` [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) If set to a positive number, it sets the
    initial delay before the first keepalive probe is sent on an idle socket. **Default:** `0`.
  - `noDelay` [`<boolean>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#boolean_type) If set to `true`, it disables the use of Nagle's algorithm
    immediately after the socket is established. **Default:** `false`.
  - `onread` [`<Object>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Object) If specified, incoming data is stored in a single `buffer`
    and passed to the supplied `callback` when data arrives on the socket.
    This will cause the streaming functionality to not provide any data.
    The socket will emit events like `'error'`, `'end'`, and `'close'`
    as usual. Methods like `pause()` and `resume()` will also behave as
    expected.
    - `buffer` [`<Buffer>`](07-buffer.md#class-buffer) | [`<Uint8Array>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Uint8Array) | [`<Function>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Function) Either a reusable chunk of memory to
      use for storing incoming data or a function that returns such.
    - `callback` [`<Function>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Function) This function is called for every chunk of incoming
      data. Two arguments are passed to it: the number of bytes written to `buffer` and a reference to `buffer`. Return `false` from this function to
      implicitly `pause()` the socket. This function will be executed in the
      global context.
  - `readable` [`<boolean>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#boolean_type) Allow reads on the socket when an `fd` is passed,
    otherwise ignored. **Default:** `false`.
  - `signal` [`<AbortSignal>`](26-globals.md#class-abortsignal) An Abort signal that may be used to destroy the
    socket.
  - `typeOfService` [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) The initial Type of Service (TOS) value.
  - `writable` [`<boolean>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#boolean_type) Allow writes on the socket when an `fd` is passed,
    otherwise ignored. **Default:** `false`.
- Returns: [`<net.Socket>`](38-net.md#class-netsocket)

Creates a new socket object.

The newly created socket can be either a TCP socket or a streaming [IPC](#ipc-support)
endpoint, depending on what it [`connect()`](#socketconnect) to.

### Event: `'close'`

Added in: v0.1.90

- `hadError` [`<boolean>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#boolean_type) `true` if the socket had a transmission error.

Emitted once the socket is fully closed. The argument `hadError` is a boolean
which says if the socket was closed due to a transmission error.

### Event: `'connect'`

Added in: v0.1.90

Emitted when a socket connection is successfully established.
See [`net.createConnection()`](#netcreateconnection).

### Event: `'connectionAttempt'`

Added in: v21.6.0, v20.12.0

- `ip` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) The IP which the socket is attempting to connect to.
- `port` [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) The port which the socket is attempting to connect to.
- `family` [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) The family of the IP. It can be `6` for IPv6 or `4` for IPv4.

Emitted when a new connection attempt is started. This may be emitted multiple times
if the family autoselection algorithm is enabled in [`socket.connect(options)`](#socketconnectoptions-connectlistener).

### Event: `'connectionAttemptFailed'`

Added in: v21.6.0, v20.12.0

- `ip` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) The IP which the socket attempted to connect to.
- `port` [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) The port which the socket attempted to connect to.
- `family` [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) The family of the IP. It can be `6` for IPv6 or `4` for IPv4.
- `error` [`<Error>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Error) The error associated with the failure.

Emitted when a connection attempt failed. This may be emitted multiple times
if the family autoselection algorithm is enabled in [`socket.connect(options)`](#socketconnectoptions-connectlistener).

### Event: `'connectionAttemptTimeout'`

Added in: v21.6.0, v20.12.0

- `ip` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) The IP which the socket attempted to connect to.
- `port` [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) The port which the socket attempted to connect to.
- `family` [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) The family of the IP. It can be `6` for IPv6 or `4` for IPv4.

Emitted when a connection attempt timed out. This is only emitted (and may be
emitted multiple times) if the family autoselection algorithm is enabled
in [`socket.connect(options)`](#socketconnectoptions-connectlistener).

### Event: `'data'`

Added in: v0.1.90

- Type: [`<Buffer>`](07-buffer.md#class-buffer) | [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type)

Emitted when data is received. The argument `data` will be a `Buffer` or
`String`. Encoding of data is set by [`socket.setEncoding()`](#socketsetencodingencoding).

The data will be lost if there is no listener when a `Socket`
emits a `'data'` event.

### Event: `'drain'`

Added in: v0.1.90

Emitted when the write buffer becomes empty. Can be used to throttle uploads.

See also: the return values of `socket.write()`.

### Event: `'end'`

Added in: v0.1.90

Emitted when the other end of the socket signals the end of transmission, thus
ending the readable side of the socket.

By default (`allowHalfOpen` is `false`) the socket will send an end of
transmission packet back and destroy its file descriptor once it has written out
its pending write queue. However, if `allowHalfOpen` is set to `true`, the
socket will not automatically [`end()`](#socketenddata-encoding-callback) its writable side,
allowing the user to write arbitrary amounts of data. The user must call
[`end()`](#socketenddata-encoding-callback) explicitly to close the connection (i.e. sending a
FIN packet back).

### Event: `'error'`

Added in: v0.1.90

- Type: [`<Error>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Error)

Emitted when an error occurs. The `'close'` event will be called directly
following this event.

### Event: `'lookup'`

Added in: v0.11.3

**History**

| Version | Changes |
| --- | --- |
| v5.10.0 | The `host` parameter is supported now. |

Emitted after resolving the host name but before connecting.
Not applicable to Unix sockets.

- `err` [`<Error>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Error) | [`<null>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#null_type) The error object. See [`dns.lookup()`](19-dns.md#dnslookuphostname-options-callback).
- `address` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) The IP address.
- `family` [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) | [`<null>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#null_type) The address type. See [`dns.lookup()`](19-dns.md#dnslookuphostname-options-callback).
- `host` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) The host name.

### Event: `'ready'`

Added in: v9.11.0

Emitted when a socket is ready to be used.

Triggered immediately after `'connect'`.

### Event: `'timeout'`

Added in: v0.1.90

Emitted if the socket times out from inactivity. This is only to notify that
the socket has been idle. The user must manually close the connection.

See also: [`socket.setTimeout()`](#socketsettimeouttimeout-callback).

### `socket.address()`

Added in: v0.1.90

**History**

| Version | Changes |
| --- | --- |
| v18.4.0 | The `family` property now returns a string instead of a number. |
| v18.0.0 | The `family` property now returns a number instead of a string. |

- Returns: [`<Object>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Object)

Returns the bound `address`, the address `family` name and `port` of the
socket as reported by the operating system:
`{ port: 12346, family: 'IPv4', address: '127.0.0.1' }`

### `socket.autoSelectFamilyAttemptedAddresses`

Added in: v19.4.0, v18.18.0

- Type: [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type)[]

This property is only present if the family autoselection algorithm is enabled in
[`socket.connect(options)`](#socketconnectoptions-connectlistener) and it is an array of the addresses that have been attempted.

Each address is a string in the form of `$IP:$PORT`. If the connection was successful,
then the last address is the one that the socket is currently connected to.

### `socket.bufferSize`

Added in: v0.3.8Deprecated in: v14.6.0

Stability: 0 - Deprecated: Use [`writable.writableLength`](51-stream.md#writablewritablelength) instead.

- Type: [`<integer>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type)

This property shows the number of characters buffered for writing. The buffer
may contain strings whose length after encoding is not yet known. So this number
is only an approximation of the number of bytes in the buffer.

`net.Socket` has the property that `socket.write()` always works. This is to
help users get up and running quickly. The computer cannot always keep up
with the amount of data that is written to a socket. The network connection
simply might be too slow. Node.js will internally queue up the data written to a
socket and send it out over the wire when it is possible.

The consequence of this internal buffering is that memory may grow.
Users who experience large or growing `bufferSize` should attempt to
"throttle" the data flows in their program with
[`socket.pause()`](#socketpause) and [`socket.resume()`](#socketresume).

### `socket.bytesRead`

Added in: v0.5.3

- Type: [`<integer>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type)

The amount of received bytes.

### `socket.bytesWritten`

Added in: v0.5.3

- Type: [`<integer>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type)

The amount of bytes sent.

### `socket.connect()`

Initiate a connection on a given socket.

Possible signatures:

- [`socket.connect(options[, connectListener])`](#socketconnectoptions-connectlistener)
- [`socket.connect(path[, connectListener])`](#socketconnectpath-connectlistener)
  for [IPC](#ipc-support) connections.
- [`socket.connect(port[, host][, connectListener])`](#socketconnectport-host-connectlistener)
  for TCP connections.
- Returns: [`<net.Socket>`](38-net.md#class-netsocket) The socket itself.

This function is asynchronous. When the connection is established, the
[`'connect'`](#event-connect) event will be emitted. If there is a problem connecting,
instead of a [`'connect'`](#event-connect) event, an [`'error'`](#event-error_1) event will be emitted with
the error passed to the [`'error'`](#event-error_1) listener.
The last parameter `connectListener`, if supplied, will be added as a listener
for the [`'connect'`](#event-connect) event **once**.

This function should only be used for reconnecting a socket after
`'close'` has been emitted or otherwise it may lead to undefined
behavior.

#### `socket.connect(options[, connectListener])`

Added in: v0.1.90

**History**

| Version | Changes |
| --- | --- |
| v20.0.0, v18.18.0 | The default value for the autoSelectFamily option is now true. The `--enable-network-family-autoselection` CLI flag has been renamed to `--network-family-autoselection`. The old name is now an alias but it is discouraged. |
| v19.4.0 | The default value for autoSelectFamily option can be changed at runtime using `setDefaultAutoSelectFamily` or via the command line option `--enable-network-family-autoselection`. |
| v19.3.0, v18.13.0 | Added the `autoSelectFamily` option. |
| v17.7.0, v16.15.0 | The `noDelay`, `keepAlive`, and `keepAliveInitialDelay` options are supported now. |
| v6.0.0 | The `hints` option defaults to `0` in all cases now. Previously, in the absence of the `family` option it would default to `dns.ADDRCONFIG \| dns.V4MAPPED`. |
| v5.11.0 | The `hints` option is supported now. |

- `options` [`<Object>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Object)
- `connectListener` [`<Function>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Function) Common parameter of [`socket.connect()`](#socketconnect)
  methods. Will be added as a listener for the [`'connect'`](#event-connect) event once.
- Returns: [`<net.Socket>`](38-net.md#class-netsocket) The socket itself.

Initiate a connection on a given socket. Normally this method is not needed,
the socket should be created and opened with [`net.createConnection()`](#netcreateconnection). Use
this only when implementing a custom Socket.

For TCP connections, available `options` are:

- `autoSelectFamily` [`<boolean>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#boolean_type): If set to `true`, it enables a family
  autodetection algorithm that loosely implements section 5 of [RFC 8305](https://www.rfc-editor.org/rfc/rfc8305.txt). The
  `all` option passed to lookup is set to `true` and the sockets attempts to
  connect to all obtained IPv6 and IPv4 addresses, in sequence, until a
  connection is established. The first returned AAAA address is tried first,
  then the first returned A address, then the second returned AAAA address and
  so on. Each connection attempt (but the last one) is given the amount of time
  specified by the `autoSelectFamilyAttemptTimeout` option before timing out and
  trying the next address. Ignored if the `family` option is not `0` or if
  `localAddress` is set. Connection errors are not emitted if at least one
  connection succeeds. If all connections attempts fails, a single
  `AggregateError` with all failed attempts is emitted. **Default:**
  [`net.getDefaultAutoSelectFamily()`](#netgetdefaultautoselectfamily).
- `autoSelectFamilyAttemptTimeout` [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type): The amount of time in milliseconds
  to wait for a connection attempt to finish before trying the next address when
  using the `autoSelectFamily` option. If set to a positive integer less than
  `10`, then the value `10` will be used instead. **Default:**
  [`net.getDefaultAutoSelectFamilyAttemptTimeout()`](#netgetdefaultautoselectfamilyattempttimeout).
- `family` [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type): Version of IP stack. Must be `4`, `6`, or `0`. The value
  `0` indicates that both IPv4 and IPv6 addresses are allowed. **Default:** `0`.
- `hints` [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) Optional [`dns.lookup()` hints](19-dns.md#supported-getaddrinfo-flags).
- `host` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) Host the socket should connect to. **Default:** `'localhost'`.
- `localAddress` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) Local address the socket should connect from.
- `localPort` [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) Local port the socket should connect from.
- `lookup` [`<Function>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Function) Custom lookup function. **Default:** [`dns.lookup()`](19-dns.md#dnslookuphostname-options-callback).
- `port` [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) Required. Port the socket should connect to.

For [IPC](#ipc-support) connections, available `options` are:

- `path` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) Required. Path the client should connect to.
  See [Identifying paths for IPC connections](#identifying-paths-for-ipc-connections). If provided, the TCP-specific
  options above are ignored.

#### `socket.connect(path[, connectListener])`

- `path` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) Path the client should connect to. See [Identifying paths for IPC connections](#identifying-paths-for-ipc-connections).
- `connectListener` [`<Function>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Function) Common parameter of [`socket.connect()`](#socketconnect)
  methods. Will be added as a listener for the [`'connect'`](#event-connect) event once.
- Returns: [`<net.Socket>`](38-net.md#class-netsocket) The socket itself.

Initiate an [IPC](#ipc-support) connection on the given socket.

Alias to
[`socket.connect(options[, connectListener])`](#socketconnectoptions-connectlistener)
called with `{ path: path }` as `options`.

#### `socket.connect(port[, host][, connectListener])`

Added in: v0.1.90

- `port` [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) Port the client should connect to.
- `host` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) Host the client should connect to.
- `connectListener` [`<Function>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Function) Common parameter of [`socket.connect()`](#socketconnect)
  methods. Will be added as a listener for the [`'connect'`](#event-connect) event once.
- Returns: [`<net.Socket>`](38-net.md#class-netsocket) The socket itself.

Initiate a TCP connection on the given socket.

Alias to
[`socket.connect(options[, connectListener])`](#socketconnectoptions-connectlistener)
called with `{port: port, host: host}` as `options`.

### `socket.connecting`

Added in: v6.1.0

- Type: [`<boolean>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#boolean_type)

If `true`,
[`socket.connect(options[, connectListener])`](#socketconnectoptions-connectlistener) was
called and has not yet finished. It will stay `true` until the socket becomes
connected, then it is set to `false` and the `'connect'` event is emitted. Note
that the
[`socket.connect(options[, connectListener])`](#socketconnectoptions-connectlistener)
callback is a listener for the `'connect'` event.

### `socket.destroy([error])`

Added in: v0.1.90

- `error` [`<Object>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Object)
- Returns: [`<net.Socket>`](38-net.md#class-netsocket)

Ensures that no more I/O activity happens on the current connection.
Destroys the stream and closes the connection.

See [`writable.destroy()`](51-stream.md#writabledestroyerror) for further details.

### `socket.destroyed`

- Type: [`<boolean>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#boolean_type) Indicates if the connection is destroyed or not. No further
  data can be transferred using a destroyed connection.

See [`writable.destroyed`](51-stream.md#writabledestroyed) for further details.

### `socket.destroySoon()`

Added in: v0.3.4

Destroys the socket after all data is written. If the `'finish'` event was
already emitted the socket is destroyed immediately. If the socket is still
writable it implicitly calls `socket.end()`.

### `socket.end([data[, encoding]][, callback])`

Added in: v0.1.90

- `data` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) | [`<Buffer>`](07-buffer.md#class-buffer) | [`<Uint8Array>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Uint8Array)
- `encoding` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) Only used when data is `string`. **Default:** `'utf8'`.
- `callback` [`<Function>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Function) Optional callback for when the socket is finished.
- Returns: [`<net.Socket>`](38-net.md#class-netsocket) The socket itself.

Half-closes the socket. i.e., it sends a FIN packet. It is possible the
server will still send some data.

See [`writable.end()`](51-stream.md#writableendchunk-encoding-callback) for further details.

### `socket.localAddress`

Added in: v0.9.6

- Type: [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type)

The string representation of the local IP address the remote client is
connecting on. For example, in a server listening on `'0.0.0.0'`, if a client
connects on `'192.168.1.1'`, the value of `socket.localAddress` would be
`'192.168.1.1'`.

### `socket.localPort`

Added in: v0.9.6

- Type: [`<integer>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type)

The numeric representation of the local port. For example, `80` or `21`.

### `socket.localFamily`

Added in: v18.8.0, v16.18.0

- Type: [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type)

The string representation of the local IP family. `'IPv4'` or `'IPv6'`.

### `socket.pause()`

- Returns: [`<net.Socket>`](38-net.md#class-netsocket) The socket itself.

Pauses the reading of data. That is, [`'data'`](#event-data) events will not be emitted.
Useful to throttle back an upload.

### `socket.pending`

Added in: v11.2.0, v10.16.0

- Type: [`<boolean>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#boolean_type)

This is `true` if the socket is not connected yet, either because `.connect()`
has not yet been called or because it is still in the process of connecting
(see [`socket.connecting`](#socketconnecting)).

### `socket.ref()`

Added in: v0.9.1

- Returns: [`<net.Socket>`](38-net.md#class-netsocket) The socket itself.

Opposite of `unref()`, calling `ref()` on a previously `unref`ed socket will
*not* let the program exit if it's the only socket left (the default behavior).
If the socket is `ref`ed calling `ref` again will have no effect.

### `socket.remoteAddress`

Added in: v0.5.10

- Type: [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type)

The string representation of the remote IP address. For example,
`'74.125.127.100'` or `'2001:4860:a005::68'`. Value may be `undefined` if
the socket is destroyed (for example, if the client disconnected).

### `socket.remoteFamily`

Added in: v0.11.14

- Type: [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type)

The string representation of the remote IP family. `'IPv4'` or `'IPv6'`. Value may be `undefined` if
the socket is destroyed (for example, if the client disconnected).

### `socket.remotePort`

Added in: v0.5.10

- Type: [`<integer>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type)

The numeric representation of the remote port. For example, `80` or `21`. Value may be `undefined` if
the socket is destroyed (for example, if the client disconnected).

### `socket.server`

Added in: v0.3.4

- Type: [`<net.Server>`](38-net.md#class-netserver) | [`<null>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#null_type)

Reference to the server that accepted the socket. This is `null` for sockets
that were not accepted by a server.

### `socket.resetAndDestroy()`

Added in: v18.3.0, v16.17.0

- Returns: [`<net.Socket>`](38-net.md#class-netsocket)

Close the TCP connection by sending an RST packet and destroy the stream.
If this TCP socket is in connecting status, it will send an RST packet and destroy this TCP socket once it is connected.
Otherwise, it will call `socket.destroy` with an `ERR_SOCKET_CLOSED` Error.
If this is not a TCP socket (for example, a pipe), calling this method will immediately throw an `ERR_INVALID_HANDLE_TYPE` Error.

### `socket.resume()`

- Returns: [`<net.Socket>`](38-net.md#class-netsocket) The socket itself.

Resumes reading after a call to [`socket.pause()`](#socketpause).

### `socket.setEncoding([encoding])`

Added in: v0.1.90

- `encoding` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type)
- Returns: [`<net.Socket>`](38-net.md#class-netsocket) The socket itself.

Set the encoding for the socket as a [Readable Stream](51-stream.md#class-streamreadable). See
[`readable.setEncoding()`](51-stream.md#readablesetencodingencoding) for more information.

### `socket.setKeepAlive()`

Enable/disable keep-alive functionality, and optionally configure the
keepalive probe timing. Returns the socket itself.

Possible signatures:

- [`socket.setKeepAlive([options])`](#socketsetkeepaliveoptions)
- [`socket.setKeepAlive([enable][, initialDelay][, interval][, count])`](#socketsetkeepaliveenable-initialdelay-interval-count)

Enabling keep-alive sets the initial delay before the first keepalive probe is
sent on an idle socket.

Set `initialDelay` (in milliseconds) to set the delay between the last
data packet received and the first keepalive probe. Setting `0` for
`initialDelay` will leave the value unchanged from the default
(or previous) setting.

Set `interval` (in milliseconds) to set the delay between successive
keepalive probes once they begin (`TCP_KEEPINTVL`). Set `count` to the
number of unacknowledged probes sent before the connection is dropped
(`TCP_KEEPCNT`). Both are only applied when keep-alive is enabled.
Omitting `interval` or `count` uses the defaults of `1000` ms and `10`.
As with `initialDelay`, a non-positive `interval` or `count` leaves the
corresponding system default unchanged.

`initialDelay` and `interval` are specified in milliseconds but the
underlying socket options are configured in whole seconds; the values are
divided by `1000` and rounded down before being applied. For example,
setting `initialDelay` to `400` will result in a `TCP_KEEPIDLE` of `0`
seconds (since `400 / 1000` rounds down to `0`).

Enabling the keep-alive functionality will set the following socket options:

- `SO_KEEPALIVE=1`
- `TCP_KEEPIDLE=initialDelay / 1000`
- `TCP_KEEPCNT=count`
- `TCP_KEEPINTVL=interval / 1000`

On Windows versions older than build 1709, keep-alive is configured through
`SIO_KEEPALIVE_VALS`, which has no probe-count field, so `count` is ignored on
those platforms.

#### `socket.setKeepAlive([options])`

Added in: v26.4.0

- `options` [`<Object>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Object)
  - `enable` [`<boolean>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#boolean_type) **Default:** `false`
  - `initialDelay` [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) **Default:** `0`
  - `interval` [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) **Default:** `1000`
  - `count` [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) **Default:** `10`
- Returns: [`<net.Socket>`](38-net.md#class-netsocket) The socket itself.

Configure keep-alive using an options object. See [`socket.setKeepAlive()`](#socketsetkeepalive)
for a description of each property.

```js
socket.setKeepAlive({ enable: true, initialDelay: 1000, interval: 1000, count: 10 });
```

#### `socket.setKeepAlive([enable][, initialDelay][, interval][, count])`

Added in: v0.1.92

**History**

| Version | Changes |
| --- | --- |
| v26.4.0 | Added the `interval` and `count` arguments to configure `TCP_KEEPINTVL` and `TCP_KEEPCNT`. |
| v13.12.0, v12.17.0 | New defaults for `TCP_KEEPCNT` and `TCP_KEEPINTVL` socket options were added. |

- `enable` [`<boolean>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#boolean_type) **Default:** `false`
- `initialDelay` [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) **Default:** `0`
- `interval` [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) **Default:** `1000`
- `count` [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) **Default:** `10`
- Returns: [`<net.Socket>`](38-net.md#class-netsocket) The socket itself.

Configure keep-alive using positional arguments. See
[`socket.setKeepAlive()`](#socketsetkeepalive) for a description of each argument.

### `socket.setNoDelay([noDelay])`

Added in: v0.1.90

- `noDelay` [`<boolean>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#boolean_type) **Default:** `true`
- Returns: [`<net.Socket>`](38-net.md#class-netsocket) The socket itself.

Enable/disable the use of Nagle's algorithm.

When a TCP connection is created, it will have Nagle's algorithm enabled.

Nagle's algorithm delays data before it is sent via the network. It attempts
to optimize throughput at the expense of latency.

Passing `true` for `noDelay` or not passing an argument will disable Nagle's
algorithm for the socket. Passing `false` for `noDelay` will enable Nagle's
algorithm.

### `socket.setTimeout(timeout[, callback])`

Added in: v0.1.90

**History**

| Version | Changes |
| --- | --- |
| v18.0.0 | Passing an invalid callback to the `callback` argument now throws `ERR_INVALID_ARG_TYPE` instead of `ERR_INVALID_CALLBACK`. |

- `timeout` [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type)
- `callback` [`<Function>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Function)
- Returns: [`<net.Socket>`](38-net.md#class-netsocket) The socket itself.

Sets the socket to timeout after `timeout` milliseconds of inactivity on
the socket. By default `net.Socket` do not have a timeout.

When an idle timeout is triggered the socket will receive a [`'timeout'`](#event-timeout)
event but the connection will not be severed. The user must manually call
[`socket.end()`](#socketenddata-encoding-callback) or [`socket.destroy()`](#socketdestroyerror) to end the connection.

```js
socket.setTimeout(3000);
socket.on('timeout', () => {
  console.log('socket timeout');
  socket.end();
});
```

If `timeout` is 0, then the existing idle timeout is disabled.

The optional `callback` parameter will be added as a one-time listener for the
[`'timeout'`](#event-timeout) event.

### `socket.getTypeOfService()`

Added in: v25.6.0

- Returns: [`<integer>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) The current TOS value.

Returns the current Type of Service (TOS) field for IPv4 packets or Traffic
Class for IPv6 packets for this socket.

`setTypeOfService()` may be called before the socket is connected; the value
will be cached and applied when the socket establishes a connection.
`getTypeOfService()` will return the currently set value even before connection.

On some platforms (e.g., Linux), certain TOS/ECN bits may be masked or ignored,
and behavior can differ between IPv4 and IPv6 or dual-stack sockets. Callers
should verify platform-specific semantics.

### `socket.setTypeOfService(tos)`

Added in: v25.6.0

- `tos` [`<integer>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) The TOS value to set (0-255).
- Returns: [`<net.Socket>`](38-net.md#class-netsocket) The socket itself.

Sets the Type of Service (TOS) field for IPv4 packets or Traffic Class for IPv6
Packets sent from this socket. This can be used to prioritize network traffic.

`setTypeOfService()` may be called before the socket is connected; the value
will be cached and applied when the socket establishes a connection.
`getTypeOfService()` will return the currently set value even before connection.

On some platforms (e.g., Linux), certain TOS/ECN bits may be masked or ignored,
and behavior can differ between IPv4 and IPv6 or dual-stack sockets. Callers
should verify platform-specific semantics.

### `socket.timeout`

Added in: v10.7.0

- Type: [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) | [`<undefined>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#undefined_type)

The socket timeout in milliseconds as set by [`socket.setTimeout()`](#socketsettimeouttimeout-callback).
It is `undefined` if a timeout has not been set.

### `socket.unref()`

Added in: v0.9.1

- Returns: [`<net.Socket>`](38-net.md#class-netsocket) The socket itself.

Calling `unref()` on a socket will allow the program to exit if this is the only
active socket in the event system. If the socket is already `unref`ed calling
`unref()` again will have no effect.

### `socket.write(data[, encoding][, callback])`

Added in: v0.1.90

- `data` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) | [`<Buffer>`](07-buffer.md#class-buffer) | [`<Uint8Array>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Uint8Array)
- `encoding` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) Only used when data is `string`. **Default:** `utf8`.
- `callback` [`<Function>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Function)
- Returns: [`<boolean>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#boolean_type)

Sends data on the socket. The second parameter specifies the encoding in the
case of a string. It defaults to UTF8 encoding.

Returns `true` if the entire data was flushed successfully to the kernel
buffer. Returns `false` if all or part of the data was queued in user memory.
[`'drain'`](#event-drain) will be emitted when the buffer is again free.

The optional `callback` parameter will be executed when the data is finally
written out, which may not be immediately.

See `Writable` stream [`write()`](51-stream.md#writablewritechunk-encoding-callback) method for more
information.

### `socket.readyState`

Added in: v0.5.0

- Type: [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type)

This property represents the state of the connection as a string.

- If the socket is connecting, `socket.readyState` is `opening`.
- If the socket is readable and writable, it is `open`.
- If the socket is readable and not writable, it is `readOnly`.
- If the socket is not readable and writable, it is `writeOnly`.
- Otherwise, it is `closed`.

## Class: `net.BoundSocket`

Added in: v26.4.0

Allows for the synchronous creation of a pre-bound socket, that can be passed
to `listen()` or `new net.Socket()` later on. For `listen()` this enables
synchronous port reservation, while for `new net.Socket()`, it allows control
over the local egress port/IP, via `bind(2)` semantics.

A `BoundSocket` binds either a TCP endpoint (`host` or `port`) or a
Unix domain/named-pipe endpoint (`path`); the two are mutually exclusive. For a
`path`, the file system entry is reserved in the constructor, so conflicts such
as `EADDRINUSE` throw synchronously exactly as a TCP bind does. On Linux a
leading `'\0'` in `path` selects the abstract namespace (no file system entry);
an abstract path on any other platform throws [`ERR_INVALID_ARG_VALUE`](22-errors.md#err_invalid_arg_value).

Adoption transfers ownership of the socket; afterwards `address()` and `close()`
throw [`ERR_SOCKET_HANDLE_ADOPTED`](22-errors.md#err_socket_handle_adopted). A handle that is never adopted must be
closed to avoid leaking the socket. Closing a pipe `BoundSocket` removes its
file system entry; abstract and TCP binds have none to remove.

When a pipe `BoundSocket` bound to a source `path` is adopted as a client, that
path is reported as the socket's `localAddress` once it connects.

An un-adopted TCP `BoundSocket` can be moved to another thread by listing it in
the `transferList` of a [`worker_threads`](67-worker-threads.md) `postMessage()` call, see
[Transferring TCP handles to other threads](#transferring-tcp-handles-to-other-threads). It can likewise be sent to a
child process as the `sendHandle` argument of [`subprocess.send()`](11-child-process.md#subprocesssendmessage-sendhandle-options-callback). In both
cases the source is left in the adopted state. Pipe binds cannot be moved
either way.

When an adopted `BoundSocket` connects to a numeric IP literal, `connect(2)` is
issued synchronously, so [`socket.localAddress`](#socketlocaladdress) is resolved once
[`socket.connect()`](#socketconnect) returns. Connection failures are still reported via a
deferred `'error'` event.

```js
import net from 'node:net';

const bound = new net.BoundSocket();
const { port } = bound.address();
console.log(`Reserved port ${port} for server`);

const server = net.createServer();
server.listen(bound); // Adopt as a server, or pass to new net.Socket() instead.
```

### `new net.BoundSocket([options])`

Added in: v26.4.0

**History**

| Version | Changes |
| --- | --- |
| v26.7.0 | The `path` option is supported. |

- `options` [`<Object>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Object)
  - `host` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) Local address to bind. Must be a numeric IP literal; no DNS
    resolution is performed. **Default:** `'0.0.0.0'`, or `'::'` when
    `ipv6Only` is `true`.
  - `port` [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) Local port. `0` requests an OS-assigned ephemeral port.
    **Default:** `0`.
  - `ipv6Only` [`<boolean>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#boolean_type) Sets `IPV6_V6ONLY`, disabling dual-stack support so the
    socket binds IPv6 only. Only meaningful for IPv6 binds. **Default:**
    `false`.
  - `reusePort` [`<boolean>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#boolean_type) Sets `SO_REUSEPORT`, allowing multiple sockets to bind
    the same address and port for kernel-level load balancing. Support is
    platform-dependent. **Default:** `false`.
  - `path` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) Binds a Unix domain socket (or Windows named pipe) at the
    given path instead of a TCP endpoint. A leading `'\0'` selects the Linux
    abstract namespace. Mutually exclusive with `host`, `port`, `ipv6Only`, and
    `reusePort`; combining them throws [`ERR_INVALID_ARG_VALUE`](22-errors.md#err_invalid_arg_value).

### `boundSocket.address()`

Added in: v26.4.0

**History**

| Version | Changes |
| --- | --- |
| v26.7.0 | The bound path is returned for a pipe bind. |

- Returns: [`<Object>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Object) | [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) For a TCP bind, an object with `address`, `family`,
  and `port` properties, as [`server.address()`](#serveraddress) returns. For a pipe bind, the
  bound path string, as [`server.address()`](#serveraddress) returns for a pipe server.

Returns the bound local address. When bound with `port: 0`, `port` is the
OS-assigned ephemeral port.

### `boundSocket.isPipe`

Added in: v26.7.0

- [`<boolean>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#boolean_type)

`true` when the socket was bound with a `path` (a Unix domain socket or Windows
named pipe), `false` for a TCP bind. The getter's presence on
`net.BoundSocket.prototype` also serves as a capability probe for `path`
support.

### `boundSocket.fd()`

Added in: v26.4.0

- Returns: [`<integer>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) The underlying OS file descriptor, or `-1` on platforms
  that do not expose one for sockets (such as Windows).

Returns the file descriptor of the bound socket. Ownership remains with the
`BoundSocket`, so the descriptor must not be closed by the caller. The
descriptor is only available before the handle is adopted; afterwards it belongs
to the adopting [`net.Server`](#class-netserver) or [`net.Socket`](#class-netsocket) and `fd()` throws
[`ERR_SOCKET_HANDLE_ADOPTED`](22-errors.md#err_socket_handle_adopted).

### `boundSocket.close()`

Added in: v26.4.0

Releases the bound socket. Only needed when the handle is never adopted.

### `boundSocket[Symbol.dispose]()`

Added in: v26.4.0

Closes the handle if it has not been adopted or closed; otherwise a no-op.

## `net.connect()`

Aliases to
[`net.createConnection()`](#netcreateconnection).

Possible signatures:

- [`net.connect(options[, connectListener])`](#netconnectoptions-connectlistener)
- [`net.connect(path[, connectListener])`](#netconnectpath-connectlistener) for [IPC](#ipc-support)
  connections.
- [`net.connect(port[, host][, connectListener])`](#netconnectport-host-connectlistener)
  for TCP connections.

### `net.connect(options[, connectListener])`

Added in: v0.7.0

- `options` [`<Object>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Object)
- `connectListener` [`<Function>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Function)
- Returns: [`<net.Socket>`](38-net.md#class-netsocket)

Alias to
[`net.createConnection(options[, connectListener])`](#netcreateconnectionoptions-connectlistener).

### `net.connect(path[, connectListener])`

Added in: v0.1.90

- `path` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type)
- `connectListener` [`<Function>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Function)
- Returns: [`<net.Socket>`](38-net.md#class-netsocket)

Alias to
[`net.createConnection(path[, connectListener])`](#netcreateconnectionpath-connectlistener).

### `net.connect(port[, host][, connectListener])`

Added in: v0.1.90

- `port` [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type)
- `host` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type)
- `connectListener` [`<Function>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Function)
- Returns: [`<net.Socket>`](38-net.md#class-netsocket)

Alias to
[`net.createConnection(port[, host][, connectListener])`](#netcreateconnectionport-host-connectlistener).

## `net.createConnection()`

A factory function, which creates a new [`net.Socket`](#class-netsocket),
immediately initiates connection with [`socket.connect()`](#socketconnect),
then returns the `net.Socket` that starts the connection.

When the connection is established, a [`'connect'`](#event-connect) event will be emitted
on the returned socket. The last parameter `connectListener`, if supplied,
will be added as a listener for the [`'connect'`](#event-connect) event **once**.

Possible signatures:

- [`net.createConnection(options[, connectListener])`](#netcreateconnectionoptions-connectlistener)
- [`net.createConnection(path[, connectListener])`](#netcreateconnectionpath-connectlistener)
  for [IPC](#ipc-support) connections.
- [`net.createConnection(port[, host][, connectListener])`](#netcreateconnectionport-host-connectlistener)
  for TCP connections.

The [`net.connect()`](#netconnect) function is an alias to this function.

### `net.createConnection(options[, connectListener])`

Added in: v0.1.90

- `options` [`<Object>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Object) Required. Will be passed to both the [`new net.Socket([options])`](#new-netsocketoptions) call and the
  [`socket.connect(options[, connectListener])`](#socketconnectoptions-connectlistener)
  method.
- `connectListener` [`<Function>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Function) Common parameter of the [`net.createConnection()`](#netcreateconnection) functions. If supplied, will be added as
  a listener for the [`'connect'`](#event-connect) event on the returned socket once.
- Returns: [`<net.Socket>`](38-net.md#class-netsocket) The newly created socket used to start the connection.

For available options, see
[`new net.Socket([options])`](#new-netsocketoptions)
and [`socket.connect(options[, connectListener])`](#socketconnectoptions-connectlistener).

Additional options:

- `handle` [`<net.BoundSocket>`](38-net.md#class-netboundsocket) A pre-bound [`BoundSocket`](#class-netboundsocket) used as the
  connection's source binding, honoring its local address and port. Adoption
  consumes the bound socket (see [ownership transfer](#class-netboundsocket)).
- `timeout` [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) If set, will be used to call [`socket.setTimeout(timeout)`](#socketsettimeouttimeout-callback) after the socket is created, but before
  it starts the connection.

Following is an example of a client of the echo server described
in the [`net.createServer()`](#netcreateserveroptions-connectionlistener) section:

*ES module version:*

```js
import net from 'node:net';
const client = net.createConnection({ port: 8124 }, () => {
  // 'connect' listener.
  console.log('connected to server!');
  client.write('world!\r\n');
});
client.on('data', (data) => {
  console.log(data.toString());
  client.end();
});
client.on('end', () => {
  console.log('disconnected from server');
});
```

*CommonJS version:*

```js
const net = require('node:net');
const client = net.createConnection({ port: 8124 }, () => {
  // 'connect' listener.
  console.log('connected to server!');
  client.write('world!\r\n');
});
client.on('data', (data) => {
  console.log(data.toString());
  client.end();
});
client.on('end', () => {
  console.log('disconnected from server');
});
```

To connect on the socket `/tmp/echo.sock`:

```js
const client = net.createConnection({ path: '/tmp/echo.sock' });
```

Following is an example of a client using the `port` and `onread`
option. In this case, the `onread` option will be only used to call
`new net.Socket([options])` and the `port` option will be used to
call `socket.connect(options[, connectListener])`.

*ES module version:*

```js
import net from 'node:net';
import { Buffer } from 'node:buffer';
net.createConnection({
  port: 8124,
  onread: {
    // Reuses a 4KiB Buffer for every read from the socket.
    buffer: Buffer.alloc(4 * 1024),
    callback: function(nread, buf) {
      // Received data is available in `buf` from 0 to `nread`.
      console.log(buf.toString('utf8', 0, nread));
    },
  },
});
```

*CommonJS version:*

```js
const net = require('node:net');
net.createConnection({
  port: 8124,
  onread: {
    // Reuses a 4KiB Buffer for every read from the socket.
    buffer: Buffer.alloc(4 * 1024),
    callback: function(nread, buf) {
      // Received data is available in `buf` from 0 to `nread`.
      console.log(buf.toString('utf8', 0, nread));
    },
  },
});
```

### `net.createConnection(path[, connectListener])`

Added in: v0.1.90

- `path` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) Path the socket should connect to. Will be passed to [`socket.connect(path[, connectListener])`](#socketconnectpath-connectlistener).
  See [Identifying paths for IPC connections](#identifying-paths-for-ipc-connections).
- `connectListener` [`<Function>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Function) Common parameter of the [`net.createConnection()`](#netcreateconnection) functions, an "once" listener for the
  `'connect'` event on the initiating socket. Will be passed to
  [`socket.connect(path[, connectListener])`](#socketconnectpath-connectlistener).
- Returns: [`<net.Socket>`](38-net.md#class-netsocket) The newly created socket used to start the connection.

Initiates an [IPC](#ipc-support) connection.

This function creates a new [`net.Socket`](#class-netsocket) with all options set to default,
immediately initiates connection with
[`socket.connect(path[, connectListener])`](#socketconnectpath-connectlistener),
then returns the `net.Socket` that starts the connection.

### `net.createConnection(port[, host][, connectListener])`

Added in: v0.1.90

- `port` [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) Port the socket should connect to. Will be passed to [`socket.connect(port[, host][, connectListener])`](#socketconnectport-host-connectlistener).
- `host` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) Host the socket should connect to. Will be passed to [`socket.connect(port[, host][, connectListener])`](#socketconnectport-host-connectlistener).
  **Default:** `'localhost'`.
- `connectListener` [`<Function>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Function) Common parameter of the [`net.createConnection()`](#netcreateconnection) functions, an "once" listener for the
  `'connect'` event on the initiating socket. Will be passed to
  [`socket.connect(port[, host][, connectListener])`](#socketconnectport-host-connectlistener).
- Returns: [`<net.Socket>`](38-net.md#class-netsocket) The newly created socket used to start the connection.

Initiates a TCP connection.

This function creates a new [`net.Socket`](#class-netsocket) with all options set to default,
immediately initiates connection with
[`socket.connect(port[, host][, connectListener])`](#socketconnectport-host-connectlistener),
then returns the `net.Socket` that starts the connection.

## `net.createServer([options][, connectionListener])`

Added in: v0.5.0

**History**

| Version | Changes |
| --- | --- |
| v20.1.0, v18.17.0 | The `highWaterMark` option is supported now. |
| v17.7.0, v16.15.0 | The `noDelay`, `keepAlive`, and `keepAliveInitialDelay` options are supported now. |

- `options` [`<Object>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Object)

  - `allowHalfOpen` [`<boolean>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#boolean_type) If set to `false`, then the socket will
    automatically end the writable side when the readable side ends.
    **Default:** `false`.
  - `highWaterMark` [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) Optionally overrides all [`net.Socket`](#class-netsocket)s'
    `readableHighWaterMark` and `writableHighWaterMark`.
    **Default:** See [`stream.getDefaultHighWaterMark()`](51-stream.md#streamgetdefaulthighwatermarkobjectmode).
  - `keepAlive` [`<boolean>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#boolean_type) If set to `true`, it enables keep-alive functionality
    on the socket immediately after a new incoming connection is received,
    similarly on what is done in [`socket.setKeepAlive()`](#socketsetkeepalive). **Default:**
    `false`.
  - `keepAliveInitialDelay` [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) If set to a positive number, it sets the
    initial delay before the first keepalive probe is sent on an idle socket. **Default:** `0`.
  - `noDelay` [`<boolean>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#boolean_type) If set to `true`, it disables the use of Nagle's
    algorithm immediately after a new incoming connection is received.
    **Default:** `false`.
  - `pauseOnConnect` [`<boolean>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#boolean_type) Indicates whether the socket should be
    paused on incoming connections. **Default:** `false`.
  - `blockList` [`<net.BlockList>`](38-net.md#class-netblocklist) `blockList` can be used for disabling inbound
    access to specific IP addresses, IP ranges, or IP subnets. This does not
    work if the server is behind a reverse proxy, NAT, etc. because the address
    checked against the block list is the address of the proxy, or the one
    specified by the NAT.
- `connectionListener` [`<Function>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Function) Automatically set as a listener for the [`'connection'`](#event-connection) event.
- Returns: [`<net.Server>`](38-net.md#class-netserver)

Creates a new TCP or [IPC](#ipc-support) server.

If `allowHalfOpen` is set to `true`, when the other end of the socket
signals the end of transmission, the server will only send back the end of
transmission when [`socket.end()`](#socketenddata-encoding-callback) is explicitly called. For example, in the
context of TCP, when a FIN packet is received, a FIN packet is sent
back only when [`socket.end()`](#socketenddata-encoding-callback) is explicitly called. Until then the
connection is half-closed (non-readable but still writable). See [`'end'`](#event-end)
event and [RFC 1122](https://tools.ietf.org/html/rfc1122) (section 4.2.2.13) for more information.

If `pauseOnConnect` is set to `true`, then the socket associated with each
incoming connection will be paused, and no data will be read from its handle.
This allows connections to be passed between processes without any data being
read by the original process. To begin reading data from a paused socket, call
[`socket.resume()`](#socketresume).

The server can be a TCP server or an [IPC](#ipc-support) server, depending on what it
[`listen()`](#serverlisten) to.

Here is an example of a TCP echo server which listens for connections
on port 8124:

*ES module version:*

```js
import net from 'node:net';
const server = net.createServer((c) => {
  // 'connection' listener.
  console.log('client connected');
  c.on('end', () => {
    console.log('client disconnected');
  });
  c.write('hello\r\n');
  c.pipe(c);
});
server.on('error', (err) => {
  throw err;
});
server.listen(8124, () => {
  console.log('server bound');
});
```

*CommonJS version:*

```js
const net = require('node:net');
const server = net.createServer((c) => {
  // 'connection' listener.
  console.log('client connected');
  c.on('end', () => {
    console.log('client disconnected');
  });
  c.write('hello\r\n');
  c.pipe(c);
});
server.on('error', (err) => {
  throw err;
});
server.listen(8124, () => {
  console.log('server bound');
});
```

Test this by using `telnet`:

```bash
telnet localhost 8124
```

To listen on the socket `/tmp/echo.sock`:

```js
server.listen('/tmp/echo.sock', () => {
  console.log('server bound');
});
```

Use `nc` to connect to a Unix domain socket server:

```bash
nc -U /tmp/echo.sock
```

## `net.getDefaultAutoSelectFamily()`

Added in: v19.4.0

Gets the current default value of the `autoSelectFamily` option of [`socket.connect(options)`](#socketconnectoptions-connectlistener).
The initial default value is `true`, unless the command line option
`--no-network-family-autoselection` is provided.

- Returns: [`<boolean>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#boolean_type) The current default value of the `autoSelectFamily` option.

## `net.setDefaultAutoSelectFamily(value)`

Added in: v19.4.0

Sets the default value of the `autoSelectFamily` option of [`socket.connect(options)`](#socketconnectoptions-connectlistener).

- `value` [`<boolean>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#boolean_type) The new default value.
  The initial default value is `true`, unless the command line option
  `--no-network-family-autoselection` is provided.

## `net.getDefaultAutoSelectFamilyAttemptTimeout()`

Added in: v19.8.0, v18.18.0

Gets the current default value of the `autoSelectFamilyAttemptTimeout` option of [`socket.connect(options)`](#socketconnectoptions-connectlistener).
The initial default value is `500` or the value specified via the command line
option `--network-family-autoselection-attempt-timeout`.

- Returns: [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) The current default value of the `autoSelectFamilyAttemptTimeout` option.

## `net.setDefaultAutoSelectFamilyAttemptTimeout(value)`

Added in: v19.8.0, v18.18.0

Sets the default value of the `autoSelectFamilyAttemptTimeout` option of [`socket.connect(options)`](#socketconnectoptions-connectlistener).

- `value` [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) The new default value, which must be a positive number. If the number is less than `10`,
  the value `10` is used instead. The initial default value is `250` or the value specified via the command line
  option `--network-family-autoselection-attempt-timeout`.

## `net.isIP(input)`

Added in: v0.3.0

- `input` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type)
- Returns: [`<integer>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type)

Returns `6` if `input` is an IPv6 address, including an IPv4-mapped IPv6 address.
Returns `4` if `input` is an IPv4 address in [dot-decimal notation](https://en.wikipedia.org/wiki/Dot-decimal_notation) with no
leading zeroes. Otherwise, returns `0`.

```js
net.isIP('::1'); // returns 6
net.isIP('::ffff:127.0.0.1'); // returns 6
net.isIP('127.0.0.1'); // returns 4
net.isIP('127.000.000.001'); // returns 0
net.isIP('127.0.0.1/24'); // returns 0
net.isIP('fhqwhgads'); // returns 0
```

## `net.isIPv4(input)`

Added in: v0.3.0

- `input` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type)
- Returns: [`<boolean>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#boolean_type)

Returns `true` if `input` is an IPv4 address in [dot-decimal notation](https://en.wikipedia.org/wiki/Dot-decimal_notation) with no
leading zeroes. Otherwise, returns `false`.

```js
net.isIPv4('127.0.0.1'); // returns true
net.isIPv4('127.000.000.001'); // returns false
net.isIPv4('127.0.0.1/24'); // returns false
net.isIPv4('fhqwhgads'); // returns false
```

## `net.isIPv6(input)`

Added in: v0.3.0

- `input` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type)
- Returns: [`<boolean>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#boolean_type)

Returns `true` if `input` is an IPv6 address, including an IPv4-mapped IPv6 address.
Otherwise, returns `false`.

```js
net.isIPv6('::1'); // returns true
net.isIPv6('::ffff:127.0.0.1'); // returns true
net.isIPv6('fhqwhgads'); // returns false
```

[Contents](../README.md) | [Previous: Modules: TypeScript](37-typescript.md) | [Next: OS](39-os.md)
