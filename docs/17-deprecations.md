[Contents](../README.md) | [Previous: Debugger](16-debugger.md) | [Next: Diagnostics Channel](18-diagnostics-channel.md)

Source: [https://nodejs.org/docs/latest/api/deprecations.html](https://nodejs.org/docs/latest/api/deprecations.html)

# Deprecated APIs

Node.js APIs might be deprecated for any of the following reasons:

- Use of the API is unsafe.
- An improved alternative API is available.
- Breaking changes to the API are expected in a future major release.

Node.js uses four kinds of deprecations:

- Documentation-only
- Application (non-`node_modules` code only)
- Runtime (all code)
- End-of-Life

A Documentation-only deprecation is one that is expressed only within the
Node.js API docs. These generate no side-effects while running Node.js.
Some Documentation-only deprecations trigger a runtime warning when launched
with [`--pending-deprecation`](13-cli.md#--pending-deprecation) flag (or its alternative,
`NODE_PENDING_DEPRECATION=1` environment variable), similarly to Runtime
deprecations below. Documentation-only deprecations that support that flag
are explicitly labeled as such in the
[list of Deprecated APIs](#list-of-deprecated-apis).

An Application deprecation for only non-`node_modules` code will, by default,
generate a process warning that will be printed to `stderr` the first time
the deprecated API is used in code that's not loaded from `node_modules`.
When the [`--throw-deprecation`](13-cli.md#--throw-deprecation) command-line flag is used, a Runtime
deprecation will cause an error to be thrown. When
[`--pending-deprecation`](13-cli.md#--pending-deprecation) is used, warnings will also be emitted for
code loaded from `node_modules`.

A runtime deprecation for all code is similar to the runtime deprecation
for non-`node_modules` code, except that it also emits a warning for
code loaded from `node_modules`.

An End-of-Life deprecation is used when functionality is or will soon be removed
from Node.js.

## Revoking deprecations

Occasionally, the deprecation of an API might be reversed. In such situations,
this document will be updated with information relevant to the decision.
However, the deprecation identifier will not be modified.

## List of deprecated APIs

### DEP0001: `http.OutgoingMessage.prototype.flush`

**History**

| Version | Changes |
| --- | --- |
| v14.0.0 | End-of-Life. |
| v6.12.0, v4.8.6 | A deprecation code has been assigned. |
| v1.6.0 | Runtime deprecation. |

Type: End-of-Life

`OutgoingMessage.prototype.flush()` has been removed. Use
`OutgoingMessage.prototype.flushHeaders()` instead.

### DEP0002: `require('_linklist')`

**History**

| Version | Changes |
| --- | --- |
| v8.0.0 | End-of-Life. |
| v6.12.0 | A deprecation code has been assigned. |
| v5.0.0 | Runtime deprecation. |

Type: End-of-Life

The `_linklist` module is deprecated. Please use a userland alternative.

### DEP0003: `_writableState.buffer`

**History**

| Version | Changes |
| --- | --- |
| v14.0.0 | End-of-Life. |
| v6.12.0, v4.8.6 | A deprecation code has been assigned. |
| v0.11.15 | Runtime deprecation. |

Type: End-of-Life

The `_writableState.buffer` has been removed. Use `_writableState.getBuffer()`
instead.

### DEP0004: `CryptoStream.prototype.readyState`

**History**

| Version | Changes |
| --- | --- |
| v10.0.0 | End-of-Life. |
| v6.12.0, v4.8.6 | A deprecation code has been assigned. |
| v0.4.0 | Documentation-only deprecation. |

Type: End-of-Life

The `CryptoStream.prototype.readyState` property was removed.

### DEP0005: `Buffer()` constructor

**History**

| Version | Changes |
| --- | --- |
| v10.0.0 | Runtime deprecation. |
| v6.12.0 | A deprecation code has been assigned. |
| v6.0.0 | Documentation-only deprecation. |

Type: Application (non-`node_modules` code only)

The `Buffer()` function and `new Buffer()` constructor are deprecated due to
API usability issues that can lead to accidental security issues.

As an alternative, use one of the following methods of constructing `Buffer`
objects:

- [`Buffer.alloc(size[, fill[, encoding]])`](07-buffer.md#static-method-bufferallocsize-fill-encoding): Create a `Buffer` with
  *initialized* memory.
- [`Buffer.allocUnsafe(size)`](07-buffer.md#static-method-bufferallocunsafesize-alignment): Create a `Buffer` with
  *uninitialized* memory.
- [`Buffer.allocUnsafeSlow(size)`](07-buffer.md#static-method-bufferallocunsafeslowsize-alignment): Create a `Buffer` with *uninitialized*
  memory.
- [`Buffer.from(array)`](07-buffer.md#static-method-bufferfromarray): Create a `Buffer` with a copy of `array`
- [`Buffer.from(arrayBuffer[, byteOffset[, length]])`](07-buffer.md#static-method-bufferfromarraybuffer-byteoffset-length) -
  Create a `Buffer` that wraps the given `arrayBuffer`.
- [`Buffer.from(buffer)`](07-buffer.md#static-method-bufferfrombuffer): Create a `Buffer` that copies `buffer`.
- [`Buffer.from(string[, encoding])`](07-buffer.md#static-method-bufferfromstring-encoding): Create a `Buffer`
  that copies `string`.

Without `--pending-deprecation`, runtime warnings occur only for code not in
`node_modules`. This means there will not be deprecation warnings for
`Buffer()` usage in dependencies. With `--pending-deprecation`, a runtime
warning results no matter where the `Buffer()` usage occurs.

### DEP0006: `child_process` `options.customFds`

**History**

| Version | Changes |
| --- | --- |
| v12.0.0 | End-of-Life. |
| v6.12.0, v4.8.6 | A deprecation code has been assigned. |
| v0.11.14 | Runtime deprecation. |
| v0.5.10 | Documentation-only deprecation. |

Type: End-of-Life

Within the [`child_process`](11-child-process.md) module's `spawn()`, `fork()`, and `exec()`
methods, the `options.customFds` option is deprecated. The `options.stdio`
option should be used instead.

### DEP0007: Replace `cluster` `worker.suicide` with `worker.exitedAfterDisconnect`

**History**

| Version | Changes |
| --- | --- |
| v9.0.0 | End-of-Life. |
| v7.0.0 | Runtime deprecation. |
| v6.12.0 | A deprecation code has been assigned. |
| v6.0.0 | Documentation-only deprecation. |

Type: End-of-Life

In an earlier version of the Node.js `cluster`, a boolean property with the name
`suicide` was added to the `Worker` object. The intent of this property was to
provide an indication of how and why the `Worker` instance exited. In Node.js
6.0.0, the old property was deprecated and replaced with a new
[`worker.exitedAfterDisconnect`](12-cluster.md#workerexitedafterdisconnect) property. The old property name did not
precisely describe the actual semantics and was unnecessarily emotion-laden.

### DEP0008: `require('node:constants')`

**History**

| Version | Changes |
| --- | --- |
| v6.12.0 | A deprecation code has been assigned. |
| v6.3.0 | Documentation-only deprecation. |

Type: Documentation-only

The `node:constants` module is deprecated. When requiring access to constants
relevant to specific Node.js builtin modules, developers should instead refer
to the `constants` property exposed by the relevant module. For instance,
`require('node:fs').constants` and `require('node:os').constants`.

### DEP0009: `crypto.pbkdf2` without digest

**History**

| Version | Changes |
| --- | --- |
| v14.0.0 | End-of-Life (for `digest === null`). |
| v11.0.0 | Runtime deprecation (for `digest === null`). |
| v8.0.0 | End-of-Life (for `digest === undefined`). |
| v6.12.0 | A deprecation code has been assigned. |
| v6.0.0 | Runtime deprecation (for `digest === undefined`). |

Type: End-of-Life

Use of the [`crypto.pbkdf2()`](15-crypto.md#cryptopbkdf2password-salt-iterations-keylen-digest-callback) API without specifying a digest was deprecated
in Node.js 6.0 because the method defaulted to using the non-recommended
`'SHA1'` digest. Previously, a deprecation warning was printed. Starting in
Node.js 8.0.0, calling `crypto.pbkdf2()` or `crypto.pbkdf2Sync()` with
`digest` set to `undefined` will throw a `TypeError`.

Beginning in Node.js 11.0.0, calling these functions with `digest` set to
`null` would print a deprecation warning to align with the behavior when `digest`
is `undefined`.

Now, however, passing either `undefined` or `null` will throw a `TypeError`.

### DEP0010: `crypto.createCredentials`

**History**

| Version | Changes |
| --- | --- |
| v11.0.0 | End-of-Life. |
| v6.12.0, v4.8.6 | A deprecation code has been assigned. |
| v0.11.13 | Runtime deprecation. |

Type: End-of-Life

The `crypto.createCredentials()` API was removed. Please use
[`tls.createSecureContext()`](55-tls.md#tlscreatesecurecontextoptions) instead.

### DEP0011: `crypto.Credentials`

**History**

| Version | Changes |
| --- | --- |
| v11.0.0 | End-of-Life. |
| v6.12.0, v4.8.6 | A deprecation code has been assigned. |
| v0.11.13 | Runtime deprecation. |

Type: End-of-Life

The `crypto.Credentials` class was removed. Please use [`tls.SecureContext`](55-tls.md#tlscreatesecurecontextoptions)
instead.

### DEP0012: `Domain.dispose`

**History**

| Version | Changes |
| --- | --- |
| v9.0.0 | End-of-Life. |
| v6.12.0, v4.8.6 | A deprecation code has been assigned. |
| v0.11.7 | Runtime deprecation. |

Type: End-of-Life

`Domain.dispose()` has been removed. Recover from failed I/O actions
explicitly via error event handlers set on the domain instead.

### DEP0013: `fs` asynchronous function without callback

**History**

| Version | Changes |
| --- | --- |
| v10.0.0 | End-of-Life. |
| v7.0.0 | Runtime deprecation. |

Type: End-of-Life

Calling an asynchronous function without a callback throws a `TypeError`
in Node.js 10.0.0 onwards. See <https://github.com/nodejs/node/pull/12562>.

### DEP0014: `fs.read` legacy String interface

**History**

| Version | Changes |
| --- | --- |
| v8.0.0 | End-of-Life. |
| v6.12.0, v4.8.6 | A deprecation code has been assigned. |
| v6.0.0 | Runtime deprecation. |
| v0.1.96 | Documentation-only deprecation. |

Type: End-of-Life

The [`fs.read()`](24-fs.md#fsreadfd-buffer-offset-length-position-callback) legacy `String` interface is deprecated. Use the `Buffer`
API as mentioned in the documentation instead.

### DEP0015: `fs.readSync` legacy String interface

**History**

| Version | Changes |
| --- | --- |
| v8.0.0 | End-of-Life. |
| v6.12.0, v4.8.6 | A deprecation code has been assigned. |
| v6.0.0 | Runtime deprecation. |
| v0.1.96 | Documentation-only deprecation. |

Type: End-of-Life

The [`fs.readSync()`](24-fs.md#fsreadsyncfd-buffer-offset-length-position) legacy `String` interface is deprecated. Use the
`Buffer` API as mentioned in the documentation instead.

### DEP0016: `GLOBAL`/`root`

**History**

| Version | Changes |
| --- | --- |
| v14.0.0 | End-of-Life. |
| v6.12.0 | A deprecation code has been assigned. |
| v6.0.0 | Runtime deprecation. |

Type: End-of-Life

The `GLOBAL` and `root` aliases for the `global` property were deprecated
in Node.js 6.0.0 and have since been removed.

### DEP0017: `Intl.v8BreakIterator`

**History**

| Version | Changes |
| --- | --- |
| v9.0.0 | End-of-Life. |
| v7.0.0 | Runtime deprecation. |

Type: End-of-Life

`Intl.v8BreakIterator` was a non-standard extension and has been removed.
See [`Intl.Segmenter`](https://github.com/tc39/proposal-intl-segmenter).

### DEP0018: Unhandled promise rejections

**History**

| Version | Changes |
| --- | --- |
| v15.0.0 | End-of-Life. |
| v7.0.0 | Runtime deprecation. |

Type: End-of-Life

Unhandled promise rejections are deprecated. By default, promise rejections
that are not handled terminate the Node.js process with a non-zero exit
code. To change the way Node.js treats unhandled rejections, use the
[`--unhandled-rejections`](13-cli.md#--unhandled-rejectionsmode) command-line option.

### DEP0019: `require('.')` resolved outside directory

**History**

| Version | Changes |
| --- | --- |
| v12.0.0 | Removed functionality. |
| v6.12.0, v4.8.6 | A deprecation code has been assigned. |
| v1.8.1 | Runtime deprecation. |

Type: End-of-Life

In certain cases, `require('.')` could resolve outside the package directory.
This behavior has been removed.

### DEP0020: `Server.connections`

**History**

| Version | Changes |
| --- | --- |
| v15.0.0 | Server.connections has been removed. |
| v6.12.0, v4.8.6 | A deprecation code has been assigned. |
| v0.9.7 | Runtime deprecation. |

Type: End-of-Life

The `Server.connections` property was deprecated in Node.js 0.9.7 and has
been removed. Please use the [`Server.getConnections()`](38-net.md#servergetconnectionscallback) method instead.

### DEP0021: `Server.listenFD`

**History**

| Version | Changes |
| --- | --- |
| v12.0.0 | End-of-Life. |
| v6.12.0, v4.8.6 | A deprecation code has been assigned. |
| v0.7.12 | Runtime deprecation. |

Type: End-of-Life

The `Server.listenFD()` method was deprecated and removed. Please use
[`Server.listen({fd: <number>})`](38-net.md#serverlistenhandle-backlog-callback) instead.

### DEP0022: `os.tmpDir()`

**History**

| Version | Changes |
| --- | --- |
| v14.0.0 | End-of-Life. |
| v7.0.0 | Runtime deprecation. |

Type: End-of-Life

The `os.tmpDir()` API was deprecated in Node.js 7.0.0 and has since been
removed. Please use [`os.tmpdir()`](39-os.md#ostmpdir) instead.

An automated migration is available ([source](https://github.com/nodejs/userland-migrations/tree/main/recipes/tmpdir-to-tmpdir)):

```bash
npx codemod@latest @nodejs/tmpDir-to-tmpdir
```

### DEP0023: `os.getNetworkInterfaces()`

**History**

| Version | Changes |
| --- | --- |
| v12.0.0 | End-of-Life. |
| v6.12.0, v4.8.6 | A deprecation code has been assigned. |
| v0.6.0 | Runtime deprecation. |

Type: End-of-Life

The `os.getNetworkInterfaces()` method is deprecated. Please use the
[`os.networkInterfaces()`](39-os.md#osnetworkinterfaces) method instead.

### DEP0024: `REPLServer.prototype.convertToContext()`

**History**

| Version | Changes |
| --- | --- |
| v9.0.0 | End-of-Life. |
| v7.0.0 | Runtime deprecation. |

Type: End-of-Life

The `REPLServer.prototype.convertToContext()` API has been removed.

### DEP0025: `require('node:sys')`

**History**

| Version | Changes |
| --- | --- |
| v6.12.0, v4.8.6 | A deprecation code has been assigned. |
| v1.0.0 | Runtime deprecation. |

Type: Runtime

The `node:sys` module is deprecated. Please use the [`util`](60-util.md) module instead.

### DEP0026: `util.print()`

**History**

| Version | Changes |
| --- | --- |
| v12.0.0 | End-of-Life. |
| v6.12.0, v4.8.6 | A deprecation code has been assigned. |
| v0.11.3 | Runtime deprecation. |

Type: End-of-Life

`util.print()` has been removed. Please use [`console.log()`](14-console.md#consolelogdata-args) instead.

An automated migration is available ([source](https://github.com/nodejs/userland-migrations/tree/main/recipes/util-print-to-console-log)):

```bash
npx codemod@latest @nodejs/util-print-to-console-log
```

### DEP0027: `util.puts()`

**History**

| Version | Changes |
| --- | --- |
| v12.0.0 | End-of-Life. |
| v6.12.0, v4.8.6 | A deprecation code has been assigned. |
| v0.11.3 | Runtime deprecation. |

Type: End-of-Life

`util.puts()` has been removed. Please use [`console.log()`](14-console.md#consolelogdata-args) instead.

An automated migration is available ([source](https://github.com/nodejs/userland-migrations/tree/main/recipes/util-print-to-console-log)):

```bash
npx codemod@latest @nodejs/util-print-to-console-log
```

### DEP0028: `util.debug()`

**History**

| Version | Changes |
| --- | --- |
| v12.0.0 | End-of-Life. |
| v6.12.0, v4.8.6 | A deprecation code has been assigned. |
| v0.11.3 | Runtime deprecation. |

Type: End-of-Life

`util.debug()` has been removed. Please use [`console.error()`](14-console.md#consoleerrordata-args) instead.

An automated migration is available ([source](https://github.com/nodejs/userland-migrations/tree/main/recipes/util-print-to-console-log)):

```bash
npx codemod@latest @nodejs/util-print-to-console-log
```

### DEP0029: `util.error()`

**History**

| Version | Changes |
| --- | --- |
| v12.0.0 | End-of-Life. |
| v6.12.0, v4.8.6 | A deprecation code has been assigned. |
| v0.11.3 | Runtime deprecation. |

Type: End-of-Life

`util.error()` has been removed. Please use [`console.error()`](14-console.md#consoleerrordata-args) instead.

An automated migration is available ([source](https://github.com/nodejs/userland-migrations/tree/main/recipes/util-print-to-console-log)):

```bash
npx codemod@latest @nodejs/util-print-to-console-log
```

### DEP0030: `SlowBuffer`

**History**

| Version | Changes |
| --- | --- |
| v25.0.0 | End-of-Life. |
| v24.0.0 | Runtime deprecation. |
| v6.12.0 | A deprecation code has been assigned. |
| v6.0.0 | Documentation-only deprecation. |

Type: End-of-Life

The `SlowBuffer` class has been removed. Please use
[`Buffer.allocUnsafeSlow(size)`](07-buffer.md#static-method-bufferallocunsafeslowsize-alignment) instead.

An automated migration is available ([source](https://github.com/nodejs/userland-migrations/tree/main/recipes/slow-buffer-to-buffer-alloc-unsafe-slow)).

```bash
npx codemod@latest @nodejs/slow-buffer-to-buffer-alloc-unsafe-slow
```

### DEP0031: `ecdh.setPublicKey()`

**History**

| Version | Changes |
| --- | --- |
| v25.0.0 | Runtime deprecation. |
| v6.12.0 | A deprecation code has been assigned. |
| v5.2.0 | Documentation-only deprecation. |

Type: Runtime

The [`ecdh.setPublicKey()`](15-crypto.md#ecdhsetpublickeypublickey-encoding) method is now deprecated as its inclusion in
the API is not useful.

### DEP0032: `node:domain` module

**History**

| Version | Changes |
| --- | --- |
| v6.12.0, v4.8.6 | A deprecation code has been assigned. |
| v1.4.2 | Documentation-only deprecation. |

Type: Documentation-only

The [`domain`](20-domain.md) module is deprecated and should not be used.

### DEP0033: `EventEmitter.listenerCount()`

**History**

| Version | Changes |
| --- | --- |
| v25.4.0, v24.14.0 | Deprecation revoked. |
| v6.12.0, v4.8.6 | A deprecation code has been assigned. |
| v3.2.0 | Documentation-only deprecation. |

Type: Revoked

The [`events.listenerCount(emitter, eventName)`](23-events.md#eventslistenercountemitterortarget-eventname) API was deprecated, as it
provided identical functionality to [`emitter.listenerCount(eventName)`](23-events.md#emitterlistenercounteventname-listener). The
deprecation was revoked because this function has been repurposed to also
accept [`<EventTarget>`](23-events.md#class-eventtarget) arguments.

### DEP0034: `fs.exists(path, callback)`

**History**

| Version | Changes |
| --- | --- |
| v6.12.0, v4.8.6 | A deprecation code has been assigned. |
| v1.0.0 | Documentation-only deprecation. |

Type: Documentation-only

The [`fs.exists(path, callback)`](24-fs.md#fsexistspath-callback) API is deprecated. Please use
[`fs.stat()`](24-fs.md#fsstatpath-options-callback) or [`fs.access()`](24-fs.md#fsaccesspath-mode-callback) instead.

### DEP0035: `fs.lchmod(path, mode, callback)`

**History**

| Version | Changes |
| --- | --- |
| v6.12.0, v4.8.6 | A deprecation code has been assigned. |
| v0.4.7 | Documentation-only deprecation. |

Type: Documentation-only

The [`fs.lchmod(path, mode, callback)`](24-fs.md#fslchmodpath-mode-callback) API is deprecated.

### DEP0036: `fs.lchmodSync(path, mode)`

**History**

| Version | Changes |
| --- | --- |
| v6.12.0, v4.8.6 | A deprecation code has been assigned. |
| v0.4.7 | Documentation-only deprecation. |

Type: Documentation-only

The [`fs.lchmodSync(path, mode)`](24-fs.md#fslchmodsyncpath-mode) API is deprecated.

### DEP0037: `fs.lchown(path, uid, gid, callback)`

**History**

| Version | Changes |
| --- | --- |
| v10.6.0 | Deprecation revoked. |
| v6.12.0, v4.8.6 | A deprecation code has been assigned. |
| v0.4.7 | Documentation-only deprecation. |

Type: Deprecation revoked

The [`fs.lchown(path, uid, gid, callback)`](24-fs.md#fslchownpath-uid-gid-callback) API was deprecated. The
deprecation was revoked because the requisite supporting APIs were added in
libuv.

### DEP0038: `fs.lchownSync(path, uid, gid)`

**History**

| Version | Changes |
| --- | --- |
| v10.6.0 | Deprecation revoked. |
| v6.12.0, v4.8.6 | A deprecation code has been assigned. |
| v0.4.7 | Documentation-only deprecation. |

Type: Deprecation revoked

The [`fs.lchownSync(path, uid, gid)`](24-fs.md#fslchownsyncpath-uid-gid) API was deprecated. The deprecation was
revoked because the requisite supporting APIs were added in libuv.

### DEP0039: `require.extensions`

**History**

| Version | Changes |
| --- | --- |
| v6.12.0, v4.8.6 | A deprecation code has been assigned. |
| v0.10.6 | Documentation-only deprecation. |

Type: Documentation-only

The [`require.extensions`](33-modules.md#requireextensions) property is deprecated.

### DEP0040: `node:punycode` module

**History**

| Version | Changes |
| --- | --- |
| v23.7.0, v22.14.0 | Application deprecation. |
| v21.0.0 | Runtime deprecation. |
| v16.6.0 | Added support for `--pending-deprecation`. |
| v7.0.0 | Documentation-only deprecation. |

Type: Application (non-`node_modules` code only)

The [`punycode`](44-punycode.md) module is deprecated. Please use a userland alternative
instead.

### DEP0041: `NODE_REPL_HISTORY_FILE` environment variable

**History**

| Version | Changes |
| --- | --- |
| v10.0.0 | End-of-Life. |
| v6.12.0, v4.8.6 | A deprecation code has been assigned. |
| v3.0.0 | Documentation-only deprecation. |

Type: End-of-Life

The `NODE_REPL_HISTORY_FILE` environment variable was removed. Please use
`NODE_REPL_HISTORY` instead.

### DEP0042: `tls.CryptoStream`

**History**

| Version | Changes |
| --- | --- |
| v10.0.0 | End-of-Life. |
| v6.12.0, v4.8.6 | A deprecation code has been assigned. |
| v0.11.3 | Documentation-only deprecation. |

Type: End-of-Life

The `tls.CryptoStream` class was removed. Please use
[`tls.TLSSocket`](55-tls.md#class-tlstlssocket) instead.

### DEP0043: `tls.SecurePair`

**History**

| Version | Changes |
| --- | --- |
| v24.0.0 | End-of-Life. |
| v8.0.0 | Runtime deprecation. |
| v6.12.0 | A deprecation code has been assigned. |
| v6.0.0 | Documentation-only deprecation. |
| v0.11.15 | Deprecation revoked. |
| v0.11.3 | Runtime deprecation. |

Type: End-of-Life

The `tls.SecurePair` class is deprecated. Please use
[`tls.TLSSocket`](55-tls.md#class-tlstlssocket) instead.

### DEP0044: `util.isArray()`

**History**

| Version | Changes |
| --- | --- |
| v22.0.0 | Runtime deprecation. |
| v6.12.0, v4.8.6 | A deprecation code has been assigned. |
| v4.0.0, v3.3.1 | Documentation-only deprecation. |

Type: Runtime

The [`util.isArray()`](60-util.md#utilisarrayobject) API is deprecated. Please use `Array.isArray()`
instead.

An automated migration is available ([source](https://github.com/nodejs/userland-migrations/tree/main/recipes/util-is)):

```bash
npx codemod@latest @nodejs/util-is
```

### DEP0045: `util.isBoolean()`

**History**

| Version | Changes |
| --- | --- |
| v23.0.0 | End-of-Life deprecation. |
| v22.0.0 | Runtime deprecation. |
| v6.12.0, v4.8.6 | A deprecation code has been assigned. |
| v4.0.0, v3.3.1 | Documentation-only deprecation. |

Type: End-of-Life

The `util.isBoolean()` API has been removed. Please use
`typeof arg === 'boolean'` instead.

An automated migration is available ([source](https://github.com/nodejs/userland-migrations/tree/main/recipes/util-is)):

```bash
npx codemod@latest @nodejs/util-is
```

### DEP0046: `util.isBuffer()`

**History**

| Version | Changes |
| --- | --- |
| v23.0.0 | End-of-Life deprecation. |
| v22.0.0 | Runtime deprecation. |
| v6.12.0, v4.8.6 | A deprecation code has been assigned. |
| v4.0.0, v3.3.1 | Documentation-only deprecation. |

Type: End-of-Life

The `util.isBuffer()` API has been removed. Please use
[`Buffer.isBuffer()`](07-buffer.md#static-method-bufferisbufferobj) instead.

An automated migration is available ([source](https://github.com/nodejs/userland-migrations/tree/main/recipes/util-is)):

```bash
npx codemod@latest @nodejs/util-is
```

### DEP0047: `util.isDate()`

**History**

| Version | Changes |
| --- | --- |
| v23.0.0 | End-of-Life deprecation. |
| v22.0.0 | Runtime deprecation. |
| v6.12.0, v4.8.6 | A deprecation code has been assigned. |
| v4.0.0, v3.3.1 | Documentation-only deprecation. |

Type: End-of-Life

The `util.isDate()` API has been removed. Please use
`arg instanceof Date` instead.

Also for stronger approaches, consider using:
`Date.prototype.toString.call(arg) === '[object Date]' && !isNaN(arg)`.
This can also be used in a `try/catch` block to handle invalid date objects.

An automated migration is available ([source](https://github.com/nodejs/userland-migrations/tree/main/recipes/util-is)):

```bash
npx codemod@latest @nodejs/util-is
```

### DEP0048: `util.isError()`

**History**

| Version | Changes |
| --- | --- |
| v23.0.0 | End-of-Life deprecation. |
| v22.0.0 | Runtime deprecation. |
| v6.12.0, v4.8.6 | A deprecation code has been assigned. |
| v4.0.0, v3.3.1 | Documentation-only deprecation. |

Type: End-of-Life

The `util.isError()` API has been removed. Please use `Error.isError(arg)`.

An automated migration is available ([source](https://github.com/nodejs/userland-migrations/tree/main/recipes/util-is)):

```bash
npx codemod@latest @nodejs/util-is
```

### DEP0049: `util.isFunction()`

**History**

| Version | Changes |
| --- | --- |
| v23.0.0 | End-of-Life deprecation. |
| v22.0.0 | Runtime deprecation. |
| v6.12.0, v4.8.6 | A deprecation code has been assigned. |
| v4.0.0, v3.3.1 | Documentation-only deprecation. |

Type: End-of-Life

The `util.isFunction()` API has been removed. Please use
`typeof arg === 'function'` instead.

An automated migration is available ([source](https://github.com/nodejs/userland-migrations/tree/main/recipes/util-is)):

```bash
npx codemod@latest @nodejs/util-is
```

### DEP0050: `util.isNull()`

**History**

| Version | Changes |
| --- | --- |
| v23.0.0 | End-of-Life deprecation. |
| v22.0.0 | Runtime deprecation. |
| v6.12.0, v4.8.6 | A deprecation code has been assigned. |
| v4.0.0, v3.3.1 | Documentation-only deprecation. |

Type: End-of-Life

The `util.isNull()` API has been removed. Please use
`arg === null` instead.

An automated migration is available ([source](https://github.com/nodejs/userland-migrations/tree/main/recipes/util-is)):

```bash
npx codemod@latest @nodejs/util-is
```

### DEP0051: `util.isNullOrUndefined()`

**History**

| Version | Changes |
| --- | --- |
| v23.0.0 | End-of-Life deprecation. |
| v22.0.0 | Runtime deprecation. |
| v6.12.0, v4.8.6 | A deprecation code has been assigned. |
| v4.0.0, v3.3.1 | Documentation-only deprecation. |

Type: End-of-Life

The `util.isNullOrUndefined()` API has been removed. Please use
`arg === null || arg === undefined` instead.

An automated migration is available ([source](https://github.com/nodejs/userland-migrations/tree/main/recipes/util-is)):

```bash
npx codemod@latest @nodejs/util-is
```

### DEP0052: `util.isNumber()`

**History**

| Version | Changes |
| --- | --- |
| v23.0.0 | End-of-Life deprecation. |
| v22.0.0 | Runtime deprecation. |
| v6.12.0, v4.8.6 | A deprecation code has been assigned. |
| v4.0.0, v3.3.1 | Documentation-only deprecation. |

Type: End-of-Life

The `util.isNumber()` API has been removed. Please use
`typeof arg === 'number'` instead.

An automated migration is available ([source](https://github.com/nodejs/userland-migrations/tree/main/recipes/util-is)):

```bash
npx codemod@latest @nodejs/util-is
```

### DEP0053: `util.isObject()`

**History**

| Version | Changes |
| --- | --- |
| v23.0.0 | End-of-Life deprecation. |
| v22.0.0 | Runtime deprecation. |
| v6.12.0, v4.8.6 | A deprecation code has been assigned. |
| v4.0.0, v3.3.1 | Documentation-only deprecation. |

Type: End-of-Life

The `util.isObject()` API has been removed. Please use
`arg && typeof arg === 'object'` instead.

An automated migration is available ([source](https://github.com/nodejs/userland-migrations/tree/main/recipes/util-is)):

```bash
npx codemod@latest @nodejs/util-is
```

### DEP0054: `util.isPrimitive()`

**History**

| Version | Changes |
| --- | --- |
| v23.0.0 | End-of-Life deprecation. |
| v22.0.0 | Runtime deprecation. |
| v6.12.0, v4.8.6 | A deprecation code has been assigned. |
| v4.0.0, v3.3.1 | Documentation-only deprecation. |

Type: End-of-Life

The `util.isPrimitive()` API has been removed. Please use `Object(arg) !== arg` instead.

An automated migration is available ([source](https://github.com/nodejs/userland-migrations/tree/main/recipes/util-is)):

```bash
npx codemod@latest @nodejs/util-is
```

### DEP0055: `util.isRegExp()`

**History**

| Version | Changes |
| --- | --- |
| v23.0.0 | End-of-Life deprecation. |
| v22.0.0 | Runtime deprecation. |
| v6.12.0, v4.8.6 | A deprecation code has been assigned. |
| v4.0.0, v3.3.1 | Documentation-only deprecation. |

Type: End-of-Life

The `util.isRegExp()` API has been removed. Please use
`arg instanceof RegExp` instead.

An automated migration is available ([source](https://github.com/nodejs/userland-migrations/tree/main/recipes/util-is)):

```bash
npx codemod@latest @nodejs/util-is
```

### DEP0056: `util.isString()`

**History**

| Version | Changes |
| --- | --- |
| v23.0.0 | End-of-Life deprecation. |
| v22.0.0 | Runtime deprecation. |
| v6.12.0, v4.8.6 | A deprecation code has been assigned. |
| v4.0.0, v3.3.1 | Documentation-only deprecation. |

Type: End-of-Life

The `util.isString()` API has been removed. Please use
`typeof arg === 'string'` instead.

An automated migration is available ([source](https://github.com/nodejs/userland-migrations/tree/main/recipes/util-is)):

```bash
npx codemod@latest @nodejs/util-is
```

### DEP0057: `util.isSymbol()`

**History**

| Version | Changes |
| --- | --- |
| v23.0.0 | End-of-Life deprecation. |
| v22.0.0 | Runtime deprecation. |
| v6.12.0, v4.8.6 | A deprecation code has been assigned. |
| v4.0.0, v3.3.1 | Documentation-only deprecation. |

Type: End-of-Life

The `util.isSymbol()` API has been removed. Please use
`typeof arg === 'symbol'` instead.

An automated migration is available ([source](https://github.com/nodejs/userland-migrations/tree/main/recipes/util-is)):

```bash
npx codemod@latest @nodejs/util-is
```

### DEP0058: `util.isUndefined()`

**History**

| Version | Changes |
| --- | --- |
| v23.0.0 | End-of-Life deprecation. |
| v22.0.0 | Runtime deprecation. |
| v6.12.0, v4.8.6 | A deprecation code has been assigned. |
| v4.0.0, v3.3.1 | Documentation-only deprecation. |

Type: End-of-Life

The `util.isUndefined()` API has been removed. Please use
`arg === undefined` instead.

An automated migration is available ([source](https://github.com/nodejs/userland-migrations/tree/main/recipes/util-is)):

```bash
npx codemod@latest @nodejs/util-is
```

### DEP0059: `util.log()`

**History**

| Version | Changes |
| --- | --- |
| v23.0.0 | End-of-Life deprecation. |
| v22.0.0 | Runtime deprecation. |
| v6.12.0 | A deprecation code has been assigned. |
| v6.0.0 | Documentation-only deprecation. |

Type: End-of-Life

The `util.log()` API has been removed because it's an unmaintained
legacy API that was exposed to user land by accident. Instead,
consider the following alternatives based on your specific needs:

- **Third-Party Logging Libraries**
- **Use `console.log(new Date().toLocaleString(), message)`**

By adopting one of these alternatives, you can transition away from `util.log()`
and choose a logging strategy that aligns with the specific
requirements and complexity of your application.

An automated migration is available ([source](https://github.com/nodejs/userland-migrations/tree/main/recipes/util-log-to-console-log)):

```bash
npx codemod@latest @nodejs/util-log-to-console-log
```

### DEP0060: `util._extend()`

**History**

| Version | Changes |
| --- | --- |
| v22.0.0 | Runtime deprecation. |
| v6.12.0 | A deprecation code has been assigned. |
| v6.0.0 | Documentation-only deprecation. |

Type: Runtime

The [`util._extend()`](60-util.md#util_extendtarget-source) API is deprecated because it's an unmaintained
legacy API that was exposed to user land by accident.
Please use `target = Object.assign(target, source)` instead.

An automated migration is available ([source](https://github.com/nodejs/userland-migrations/tree/main/recipes/util-extend-to-object-assign)):

```bash
npx codemod@latest @nodejs/util-extend-to-object-assign
```

### DEP0061: `fs.SyncWriteStream`

**History**

| Version | Changes |
| --- | --- |
| v11.0.0 | End-of-Life. |
| v8.0.0 | Runtime deprecation. |
| v7.0.0 | Documentation-only deprecation. |

Type: End-of-Life

The `fs.SyncWriteStream` class was never intended to be a publicly accessible
API and has been removed. No alternative API is available. Please use a userland
alternative.

### DEP0062: `node --debug`

**History**

| Version | Changes |
| --- | --- |
| v12.0.0 | End-of-Life. |
| v8.0.0 | Runtime deprecation. |

Type: End-of-Life

`--debug` activates the legacy V8 debugger interface, which was removed as
of V8 5.8. It is replaced by Inspector which is activated with `--inspect`
instead.

### DEP0063: `ServerResponse.prototype.writeHeader()`

**History**

| Version | Changes |
| --- | --- |
| v26.0.0 | End-of-Life. |
| v25.0.0 | Runtime deprecation. |
| v8.0.0 | Documentation-only deprecation. |

Type: End-of-Life

The `node:http` module `ServerResponse.prototype.writeHeader()` API is
deprecated. Please use `ServerResponse.prototype.writeHead()` instead.

The `ServerResponse.prototype.writeHeader()` method was never documented as an
officially supported API.

### DEP0064: `tls.createSecurePair()`

**History**

| Version | Changes |
| --- | --- |
| v24.0.0 | End-of-Life. |
| v8.0.0 | Runtime deprecation. |
| v6.12.0 | A deprecation code has been assigned. |
| v6.0.0 | Documentation-only deprecation. |
| v0.11.15 | Deprecation revoked. |
| v0.11.3 | Runtime deprecation. |

Type: End-of-Life

The `tls.createSecurePair()` API was deprecated in documentation in Node.js
0.11.3. Users should use `tls.Socket` instead.

An automated migration is available ([source](https://github.com/nodejs/userland-migrations/tree/main/recipes/tls-create-secure-pair-to-tls-socket)):

```bash
npx codemod @nodejs/tls-create-secure-pair-to-tls-socket
```

### DEP0065: `repl.REPL_MODE_MAGIC` and `NODE_REPL_MODE=magic`

**History**

| Version | Changes |
| --- | --- |
| v10.0.0 | End-of-Life. |
| v8.0.0 | Documentation-only deprecation. |

Type: End-of-Life

The `node:repl` module's `REPL_MODE_MAGIC` constant, used for `replMode` option,
has been removed. Its behavior has been functionally identical to that of
`REPL_MODE_SLOPPY` since Node.js 6.0.0, when V8 5.0 was imported. Please use
`REPL_MODE_SLOPPY` instead.

The `NODE_REPL_MODE` environment variable is used to set the underlying
`replMode` of an interactive `node` session. Its value, `magic`, is also
removed. Please use `sloppy` instead.

### DEP0066: `OutgoingMessage.prototype._headers, OutgoingMessage.prototype._headerNames`

**History**

| Version | Changes |
| --- | --- |
| v24.0.0 | End-of-Life. |
| v12.0.0 | Runtime deprecation. |
| v8.0.0 | Documentation-only deprecation. |

Type: End-of-Life

The `node:http` module `OutgoingMessage.prototype._headers` and
`OutgoingMessage.prototype._headerNames` properties are deprecated. Use one of
the public methods (e.g. `OutgoingMessage.prototype.getHeader()`,
`OutgoingMessage.prototype.getHeaders()`,
`OutgoingMessage.prototype.getHeaderNames()`,
`OutgoingMessage.prototype.getRawHeaderNames()`,
`OutgoingMessage.prototype.hasHeader()`,
`OutgoingMessage.prototype.removeHeader()`,
`OutgoingMessage.prototype.setHeader()`) for working with outgoing headers.

The `OutgoingMessage.prototype._headers` and
`OutgoingMessage.prototype._headerNames` properties were never documented as
officially supported properties.

An automated migration is available ([source](https://github.com/nodejs/userland-migrations/tree/main/recipes/http-outgoingmessage-headers)):

```bash
npx codemod@latest @nodejs/http-outgoingmessage-headers
```

### DEP0067: `OutgoingMessage.prototype._renderHeaders`

**History**

| Version | Changes |
| --- | --- |
| v8.0.0 | Documentation-only deprecation. |

Type: Documentation-only

The `node:http` module `OutgoingMessage.prototype._renderHeaders()` API is
deprecated.

The `OutgoingMessage.prototype._renderHeaders` property was never documented as
an officially supported API.

### DEP0068: `node debug`

**History**

| Version | Changes |
| --- | --- |
| v15.0.0 | The legacy `node debug` command was removed. |
| v8.0.0 | Runtime deprecation. |

Type: End-of-Life

`node debug` corresponds to the legacy CLI debugger which has been replaced with
a V8-inspector based CLI debugger available through `node inspect`.

### DEP0069: `vm.runInDebugContext(string)`

**History**

| Version | Changes |
| --- | --- |
| v10.0.0 | End-of-Life. |
| v9.0.0 | Runtime deprecation. |
| v8.0.0 | Documentation-only deprecation. |

Type: End-of-Life

DebugContext has been removed in V8 and is not available in Node.js 10+.

DebugContext was an experimental API.

### DEP0070: `async_hooks.currentId()`

**History**

| Version | Changes |
| --- | --- |
| v9.0.0 | End-of-Life. |
| v8.2.0 | Runtime deprecation. |

Type: End-of-Life

`async_hooks.currentId()` was renamed to `async_hooks.executionAsyncId()` for
clarity.

This change was made while `async_hooks` was an experimental API.

### DEP0071: `async_hooks.triggerId()`

**History**

| Version | Changes |
| --- | --- |
| v9.0.0 | End-of-Life. |
| v8.2.0 | Runtime deprecation. |

Type: End-of-Life

`async_hooks.triggerId()` was renamed to `async_hooks.triggerAsyncId()` for
clarity.

This change was made while `async_hooks` was an experimental API.

### DEP0072: `async_hooks.AsyncResource.triggerId()`

**History**

| Version | Changes |
| --- | --- |
| v9.0.0 | End-of-Life. |
| v8.2.0 | Runtime deprecation. |

Type: End-of-Life

`async_hooks.AsyncResource.triggerId()` was renamed to
`async_hooks.AsyncResource.triggerAsyncId()` for clarity.

This change was made while `async_hooks` was an experimental API.

### DEP0073: Several internal properties of `net.Server`

**History**

| Version | Changes |
| --- | --- |
| v10.0.0 | End-of-Life. |
| v9.0.0 | Runtime deprecation. |

Type: End-of-Life

Accessing several internal, undocumented properties of `net.Server` instances
with inappropriate names is deprecated.

As the original API was undocumented and not generally useful for non-internal
code, no replacement API is provided.

### DEP0074: `REPLServer.bufferedCommand`

**History**

| Version | Changes |
| --- | --- |
| v15.0.0 | End-of-Life. |
| v9.0.0 | Runtime deprecation. |

Type: End-of-Life

The `REPLServer.bufferedCommand` property was deprecated in favor of
[`REPLServer.clearBufferedCommand()`](47-repl.md#replserverclearbufferedcommand).

### DEP0075: `REPLServer.parseREPLKeyword()`

**History**

| Version | Changes |
| --- | --- |
| v15.0.0 | End-of-Life. |
| v9.0.0 | Runtime deprecation. |

Type: End-of-Life

`REPLServer.parseREPLKeyword()` was removed from userland visibility.

### DEP0076: `tls.parseCertString()`

**History**

| Version | Changes |
| --- | --- |
| v18.0.0 | End-of-Life. |
| v9.0.0 | Runtime deprecation. |
| v8.6.0 | Documentation-only deprecation. |

Type: End-of-Life

`tls.parseCertString()` was a trivial parsing helper that was made public by
mistake. While it was supposed to parse certificate subject and issuer strings,
it never handled multi-value Relative Distinguished Names correctly.

Earlier versions of this document suggested using `querystring.parse()` as an
alternative to `tls.parseCertString()`. However, `querystring.parse()` also does
not handle all certificate subjects correctly and should not be used.

### DEP0077: `Module._debug()`

**History**

| Version | Changes |
| --- | --- |
| v25.0.0 | End-of-Life. |
| v9.0.0 | Runtime deprecation. |

Type: End-of-Life

`Module._debug()` has been removed.

The `Module._debug()` function was never documented as an officially
supported API.

### DEP0078: `REPLServer.turnOffEditorMode()`

**History**

| Version | Changes |
| --- | --- |
| v15.0.0 | End-of-Life. |
| v9.0.0 | Runtime deprecation. |

Type: End-of-Life

`REPLServer.turnOffEditorMode()` was removed from userland visibility.

### DEP0079: Custom inspection function on objects via `.inspect()`

**History**

| Version | Changes |
| --- | --- |
| v11.0.0 | End-of-Life. |
| v10.0.0 | Runtime deprecation. |
| v8.7.0 | Documentation-only deprecation. |

Type: End-of-Life

Using a property named `inspect` on an object to specify a custom inspection
function for [`util.inspect()`](60-util.md#utilinspectobject-options) is deprecated. Use [`util.inspect.custom`](60-util.md#utilinspectcustom)
instead. For backward compatibility with Node.js prior to version 6.4.0, both
can be specified.

### DEP0080: `path._makeLong()`

**History**

| Version | Changes |
| --- | --- |
| v9.0.0 | Documentation-only deprecation. |

Type: Documentation-only

The internal `path._makeLong()` was not intended for public use. However,
userland modules have found it useful. The internal API is deprecated
and replaced with an identical, public `path.toNamespacedPath()` method.

### DEP0081: `fs.truncate()` using a file descriptor

**History**

| Version | Changes |
| --- | --- |
| v24.0.0 | End-of-Life. |
| v9.0.0 | Runtime deprecation. |

Type: End-of-Life

`fs.truncate()` `fs.truncateSync()` usage with a file descriptor is
deprecated. Please use `fs.ftruncate()` or `fs.ftruncateSync()` to work with
file descriptors.

An automated migration is available ([source](https://github.com/nodejs/userland-migrations/tree/main/recipes/fs-truncate-fd-deprecation)):

```bash
npx codemod@latest @nodejs/fs-truncate-fd-deprecation
```

### DEP0082: `REPLServer.prototype.memory()`

**History**

| Version | Changes |
| --- | --- |
| v15.0.0 | End-of-Life. |
| v9.0.0 | Runtime deprecation. |

Type: End-of-Life

`REPLServer.prototype.memory()` is only necessary for the internal mechanics of
the `REPLServer` itself. Do not use this function.

### DEP0083: Disabling ECDH by setting `ecdhCurve` to `false`

**History**

| Version | Changes |
| --- | --- |
| v10.0.0 | End-of-Life. |
| v9.2.0 | Runtime deprecation. |

Type: End-of-Life

The `ecdhCurve` option to `tls.createSecureContext()` and `tls.TLSSocket` could
be set to `false` to disable ECDH entirely on the server only. This mode was
deprecated in preparation for migrating to OpenSSL 1.1.0 and consistency with
the client and is now unsupported. Use the `ciphers` parameter instead.

### DEP0084: requiring bundled internal dependencies

**History**

| Version | Changes |
| --- | --- |
| v12.0.0 | This functionality has been removed. |
| v10.0.0 | Runtime deprecation. |

Type: End-of-Life

Since Node.js versions 4.4.0 and 5.2.0, several modules only intended for
internal usage were mistakenly exposed to user code through `require()`. These
modules were:

- `v8/tools/codemap`
- `v8/tools/consarray`
- `v8/tools/csvparser`
- `v8/tools/logreader`
- `v8/tools/profile_view`
- `v8/tools/profile`
- `v8/tools/SourceMap`
- `v8/tools/splaytree`
- `v8/tools/tickprocessor-driver`
- `v8/tools/tickprocessor`
- `node-inspect/lib/_inspect` (from 7.6.0)
- `node-inspect/lib/internal/inspect_client` (from 7.6.0)
- `node-inspect/lib/internal/inspect_repl` (from 7.6.0)

The `v8/*` modules do not have any exports, and if not imported in a specific
order would in fact throw errors. As such there are virtually no legitimate use
cases for importing them through `require()`.

On the other hand, `node-inspect` can be installed locally through a package
manager, as it is published on the npm registry under the same name. No source
code modification is necessary if that is done.

### DEP0085: AsyncHooks sensitive API

**History**

| Version | Changes |
| --- | --- |
| v10.0.0 | End-of-Life. |
| v9.4.0, v8.10.0 | Runtime deprecation. |

Type: End-of-Life

The AsyncHooks sensitive API was never documented and had various minor issues.
Use the `AsyncResource` API instead. See
<https://github.com/nodejs/node/issues/15572>.

### DEP0086: Remove `runInAsyncIdScope`

**History**

| Version | Changes |
| --- | --- |
| v10.0.0 | End-of-Life. |
| v9.4.0, v8.10.0 | Runtime deprecation. |

Type: End-of-Life

`runInAsyncIdScope` doesn't emit the `'before'` or `'after'` event and can thus
cause a lot of issues. See <https://github.com/nodejs/node/issues/14328>.

### DEP0089: `require('node:assert')`

**History**

| Version | Changes |
| --- | --- |
| v12.8.0 | Deprecation revoked. |
| v9.9.0, v8.13.0 | Documentation-only deprecation. |

Type: Deprecation revoked

Importing assert directly was not recommended as the exposed functions use
loose equality checks. The deprecation was revoked because use of the
`node:assert` module is not discouraged, and the deprecation caused developer
confusion.

### DEP0090: Invalid GCM authentication tag lengths

**History**

| Version | Changes |
| --- | --- |
| v11.0.0 | End-of-Life. |
| v10.0.0 | Runtime deprecation. |

Type: End-of-Life

Node.js used to support all GCM authentication tag lengths which are accepted by
OpenSSL when calling [`decipher.setAuthTag()`](15-crypto.md#deciphersetauthtagbuffer-encoding). Beginning with Node.js
v11.0.0, only authentication tag lengths of 128, 120, 112, 104, 96, 64, and 32
bits are allowed. Authentication tags of other lengths are invalid per
[NIST SP 800-38D](https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-38d.pdf).

### DEP0091: `crypto.DEFAULT_ENCODING`

**History**

| Version | Changes |
| --- | --- |
| v20.0.0 | End-of-Life. |
| v10.0.0 | Runtime deprecation. |

Type: End-of-Life

The `crypto.DEFAULT_ENCODING` property only existed for compatibility with
Node.js releases prior to versions 0.9.3 and has been removed.

### DEP0092: Top-level `this` bound to `module.exports`

**History**

| Version | Changes |
| --- | --- |
| v10.0.0 | Documentation-only deprecation. |

Type: Documentation-only

Assigning properties to the top-level `this` as an alternative
to `module.exports` is deprecated. Developers should use `exports`
or `module.exports` instead.

### DEP0093: `crypto.fips` is deprecated and replaced

**History**

| Version | Changes |
| --- | --- |
| v23.0.0 | Runtime deprecation. |
| v10.0.0 | Documentation-only deprecation. |

Type: Runtime

The [`crypto.fips`](15-crypto.md#cryptofips) property is deprecated. Please use `crypto.setFips()`
and `crypto.getFips()` instead.

An automated migration is available ([source](https://github.com/nodejs/userland-migrations/tree/main/recipes/crypto-fips-to-getFips)).

```bash
npx codemod@latest @nodejs/crypto-fips-to-getFips
```

### DEP0094: Using `assert.fail()` with more than one argument

**History**

| Version | Changes |
| --- | --- |
| v25.0.0 | End-of-Life. |
| v10.0.0 | Runtime deprecation. |

Type: End-of-Life

Using `assert.fail()` with more than one argument is deprecated. Use
`assert.fail()` with only one argument or use a different `node:assert` module
method.

### DEP0095: `timers.enroll()`

**History**

| Version | Changes |
| --- | --- |
| v24.0.0 | End-of-Life. |
| v10.0.0 | Runtime deprecation. |

Type: End-of-Life

`timers.enroll()` has been removed. Please use the publicly documented
[`setTimeout()`](54-timers.md#settimeoutcallback-delay-args) or [`setInterval()`](54-timers.md#setintervalcallback-delay-args) instead.

An automated migration is available ([source](https://github.com/nodejs/userland-migrations/tree/main/recipes/timers-deprecations)).

```bash
npx codemod @nodejs/timers-deprecations
```

### DEP0096: `timers.unenroll()`

**History**

| Version | Changes |
| --- | --- |
| v24.0.0 | End-of-Life. |
| v10.0.0 | Runtime deprecation. |

Type: End-of-Life

`timers.unenroll()` has been removed. Please use the publicly documented
[`clearTimeout()`](54-timers.md#cleartimeouttimeout) or [`clearInterval()`](54-timers.md#clearintervaltimeout) instead.

An automated migration is available ([source](https://github.com/nodejs/userland-migrations/tree/main/recipes/timers-deprecations)).

```bash
npx codemod @nodejs/timers-deprecations
```

### DEP0097: `MakeCallback` with `domain` property

**History**

| Version | Changes |
| --- | --- |
| v10.0.0 | Runtime deprecation. |

Type: Runtime

Users of `MakeCallback` that add the `domain` property to carry context,
should start using the `async_context` variant of `MakeCallback` or
`CallbackScope`, or the high-level `AsyncResource` class.

### DEP0098: AsyncHooks embedder `AsyncResource.emitBefore` and `AsyncResource.emitAfter` APIs

**History**

| Version | Changes |
| --- | --- |
| v12.0.0 | End-of-Life. |
| v10.0.0, v9.6.0, v8.12.0 | Runtime deprecation. |

Type: End-of-Life

The embedded API provided by AsyncHooks exposes `.emitBefore()` and
`.emitAfter()` methods which are very easy to use incorrectly which can lead
to unrecoverable errors.

Use [`asyncResource.runInAsyncScope()`](04-async-context.md#asyncresourceruninasyncscopefn-thisarg-args) API instead which provides a much
safer, and more convenient, alternative. See
<https://github.com/nodejs/node/pull/18513>.

### DEP0099: Async context-unaware `node::MakeCallback` C++ APIs

**History**

| Version | Changes |
| --- | --- |
| v10.0.0 | Compile-time deprecation. |

Type: Compile-time

Certain versions of `node::MakeCallback` APIs available to native addons are
deprecated. Please use the versions of the API that accept an `async_context`
parameter.

### DEP0100: `process.assert()`

**History**

| Version | Changes |
| --- | --- |
| v23.0.0 | End-of-Life. |
| v10.0.0 | Runtime deprecation. |
| v0.3.7 | Documentation-only deprecation. |

Type: End-of-Life

`process.assert()` is deprecated. Please use the [`assert`](03-assert.md) module instead.

This was never a documented feature.

An automated migration is available ([source](https://github.com/nodejs/userland-migrations/tree/main/recipes/process-assert-to-node-assert)).

```bash
npx codemod@latest @nodejs/process-assert-to-node-assert
```

### DEP0101: `--with-lttng`

**History**

| Version | Changes |
| --- | --- |
| v10.0.0 | End-of-Life. |

Type: End-of-Life

The `--with-lttng` compile-time option has been removed.

### DEP0102: Using `noAssert` in `Buffer#(read|write)` operations

**History**

| Version | Changes |
| --- | --- |
| v10.0.0 | End-of-Life. |

Type: End-of-Life

Using the `noAssert` argument has no functionality anymore. All input is
verified regardless of the value of `noAssert`. Skipping the verification
could lead to hard-to-find errors and crashes.

### DEP0103: `process.binding('util').is[...]` typechecks

**History**

| Version | Changes |
| --- | --- |
| v10.9.0 | Superseded by [DEP0111](#DEP0111). |
| v10.0.0 | Documentation-only deprecation. |

Type: Documentation-only (supports [`--pending-deprecation`](13-cli.md#--pending-deprecation))

Using `process.binding()` in general should be avoided. The type checking
methods in particular can be replaced by using [`util.types`](60-util.md#utiltypes).

This deprecation has been superseded by the deprecation of the
`process.binding()` API ([DEP0111](#DEP0111)).

### DEP0104: `process.env` string coercion

**History**

| Version | Changes |
| --- | --- |
| v10.0.0 | Documentation-only deprecation with `--pending-deprecation` support. |

Type: Documentation-only (supports [`--pending-deprecation`](13-cli.md#--pending-deprecation))

When assigning a non-string property to [`process.env`](43-process.md#processenv), the assigned value is
implicitly converted to a string. This behavior is deprecated if the assigned
value is not a string, boolean, or number. In the future, such assignment might
result in a thrown error. Please convert the property to a string before
assigning it to `process.env`.

### DEP0105: `decipher.finaltol`

**History**

| Version | Changes |
| --- | --- |
| v11.0.0 | End-of-Life. |
| v10.0.0 | Runtime deprecation. |

Type: End-of-Life

`decipher.finaltol()` has never been documented and was an alias for
[`decipher.final()`](15-crypto.md#decipherfinaloutputencoding). This API has been removed, and it is recommended to use
[`decipher.final()`](15-crypto.md#decipherfinaloutputencoding) instead.

### DEP0106: `crypto.createCipher` and `crypto.createDecipher`

**History**

| Version | Changes |
| --- | --- |
| v22.0.0 | End-of-Life. |
| v11.0.0 | Runtime deprecation. |
| v10.0.0 | Documentation-only deprecation. |

Type: End-of-Life

`crypto.createCipher()` and `crypto.createDecipher()` have been removed
as they use a weak key derivation function (MD5 with no salt) and static
initialization vectors.
It is recommended to derive a key using
[`crypto.pbkdf2()`](15-crypto.md#cryptopbkdf2password-salt-iterations-keylen-digest-callback) or [`crypto.scrypt()`](15-crypto.md#cryptoscryptpassword-salt-keylen-options-callback) with random salts and to use
[`crypto.createCipheriv()`](15-crypto.md#cryptocreatecipherivalgorithm-key-iv-options) and [`crypto.createDecipheriv()`](15-crypto.md#cryptocreatedecipherivalgorithm-key-iv-options) to obtain the
[`Cipheriv`](15-crypto.md#class-cipheriv) and [`Decipheriv`](15-crypto.md#class-decipheriv) objects respectively.

An automated migration is available ([source](https://github.com/nodejs/userland-migrations/tree/main/recipes/crypto-createcipheriv-migration)):

```bash
npx codemod @nodejs/crypto-createcipheriv-migration
```

### DEP0107: `tls.convertNPNProtocols()`

**History**

| Version | Changes |
| --- | --- |
| v11.0.0 | End-of-Life. |
| v10.0.0 | Runtime deprecation. |

Type: End-of-Life

This was an undocumented helper function not intended for use outside Node.js
core and obsoleted by the removal of NPN (Next Protocol Negotiation) support.

### DEP0108: `zlib.bytesRead`

**History**

| Version | Changes |
| --- | --- |
| v23.0.0 | End-of-Life. |
| v11.0.0 | Runtime deprecation. |
| v10.0.0 | Documentation-only deprecation. |

Type: End-of-Life

Deprecated alias for [`zlib.bytesWritten`](68-zlib.md#zlibbyteswritten). This original name was chosen
because it also made sense to interpret the value as the number of bytes
read by the engine, but is inconsistent with other streams in Node.js that
expose values under these names.

An automated migration is available ([source](https://github.com/nodejs/userland-migrations/tree/main/recipes/zlib-bytesread-to-byteswritten)):

```bash
npx codemod@latest @nodejs/zlib-bytesread-to-byteswritten
```

### DEP0109: `http`, `https`, and `tls` support for invalid URLs

**History**

| Version | Changes |
| --- | --- |
| v16.0.0 | End-of-Life. |
| v11.0.0 | Runtime deprecation. |

Type: End-of-Life

Some previously supported (but strictly invalid) URLs were accepted through the
[`http.request()`](27-http.md#httprequestoptions-callback), [`http.get()`](27-http.md#httpgetoptions-callback), [`https.request()`](29-https.md#httpsrequestoptions-callback),
[`https.get()`](29-https.md#httpsgetoptions-callback), and [`tls.checkServerIdentity()`](55-tls.md#tlscheckserveridentityhostname-cert) APIs because those were
accepted by the legacy `url.parse()` API. The mentioned APIs now use the WHATWG
URL parser that requires strictly valid URLs. Passing an invalid URL is
deprecated and support will be removed in the future.

### DEP0110: `vm.Script` cached data

**History**

| Version | Changes |
| --- | --- |
| v10.6.0 | Documentation-only deprecation. |

Type: Documentation-only

The `produceCachedData` option is deprecated. Use
[`script.createCachedData()`](63-vm.md#scriptcreatecacheddata) instead.

### DEP0111: `process.binding()`

**History**

| Version | Changes |
| --- | --- |
| v11.12.0 | Added support for `--pending-deprecation`. |
| v10.9.0 | Documentation-only deprecation. |

Type: Documentation-only (supports [`--pending-deprecation`](13-cli.md#--pending-deprecation))

`process.binding()` is for use by Node.js internal code only.

While `process.binding()` has not reached End-of-Life status in general, it is
unavailable when the [permission model](42-permissions.md#permission-model) is enabled.

### DEP0112: `dgram` private APIs

**History**

| Version | Changes |
| --- | --- |
| v25.0.0 | End-of-Life. |
| v11.0.0 | Runtime deprecation. |

Type: End-of-Life

The `node:dgram` module previously contained several APIs that were never meant
to accessed outside of Node.js core: `Socket.prototype._handle`,
`Socket.prototype._receiving`, `Socket.prototype._bindState`,
`Socket.prototype._queue`, `Socket.prototype._reuseAddr`,
`Socket.prototype._healthCheck()`, `Socket.prototype._stopReceiving()`, and
`dgram._createSocketHandle()`. These have been removed.

### DEP0113: `Cipher.setAuthTag()`, `Decipher.getAuthTag()`

**History**

| Version | Changes |
| --- | --- |
| v12.0.0 | End-of-Life. |
| v11.0.0 | Runtime deprecation. |

Type: End-of-Life

`Cipher.setAuthTag()` and `Decipher.getAuthTag()` are no longer available. They
were never documented and would throw when called.

### DEP0114: `crypto._toBuf()`

**History**

| Version | Changes |
| --- | --- |
| v12.0.0 | End-of-Life. |
| v11.0.0 | Runtime deprecation. |

Type: End-of-Life

The `crypto._toBuf()` function was not designed to be used by modules outside
of Node.js core and was removed.

### DEP0115: `crypto.prng()`, `crypto.pseudoRandomBytes()`, `crypto.rng()`

**History**

| Version | Changes |
| --- | --- |
| v11.0.0 | Documentation-only deprecation with `--pending-deprecation` support. |

Type: Documentation-only (supports [`--pending-deprecation`](13-cli.md#--pending-deprecation))

In recent versions of Node.js, there is no difference between
[`crypto.randomBytes()`](15-crypto.md#cryptorandombytessize-callback) and `crypto.pseudoRandomBytes()`. The latter is
deprecated along with the undocumented aliases `crypto.prng()` and
`crypto.rng()` in favor of [`crypto.randomBytes()`](15-crypto.md#cryptorandombytessize-callback) and might be removed in a
future release.

### DEP0116: Legacy URL API

**History**

| Version | Changes |
| --- | --- |
| v24.0.0 | DEP0169 covers also `url.format()` and `url.resolve()`. |
| v19.0.0, v18.13.0 | DEP0169 deprecates `url.parse()` again. |
| v15.13.0, v14.17.0 | Deprecation revoked. Status changed to "Legacy". |
| v11.0.0 | Documentation-only deprecation. |

Type: Deprecation revoked

The [legacy URL API](59-url.md#legacy-url-api) is deprecated. This includes [`url.format()`](59-url.md#urlformaturlobject),
[`url.parse()`](59-url.md#urlparseurlstring-parsequerystring-slashesdenotehost), [`url.resolve()`](59-url.md#urlresolvefrom-to), and the [legacy `urlObject`](59-url.md#legacy-urlobject). Please
use the [WHATWG URL API](59-url.md#the-whatwg-url-api) instead.

An automated migration is available ([source](https://github.com/nodejs/userland-migrations/tree/main/recipes/node-url-to-whatwg-url)).

```bash
npx codemod@latest @nodejs/node-url-to-whatwg-url
```

### DEP0117: Native crypto handles

**History**

| Version | Changes |
| --- | --- |
| v12.0.0 | End-of-Life. |
| v11.0.0 | Runtime deprecation. |

Type: End-of-Life

Previous versions of Node.js exposed handles to internal native objects through
the `_handle` property of the `Cipher`, `Decipher`, `DiffieHellman`,
`DiffieHellmanGroup`, `ECDH`, `Hash`, `Hmac`, `Sign`, and `Verify` classes.
The `_handle` property has been removed because improper use of the native
object can lead to crashing the application.

### DEP0118: `dns.lookup()` support for a falsy host name

**History**

| Version | Changes |
| --- | --- |
| v25.0.0 | End-of-Life. |
| v11.0.0 | Runtime deprecation. |

Type: End-of-Life

Previous versions of Node.js supported `dns.lookup()` with a falsy host name
like `dns.lookup(false)` due to backward compatibility. This has been removed.

### DEP0119: `process.binding('uv').errname()` private API

**History**

| Version | Changes |
| --- | --- |
| v11.0.0 | Documentation-only deprecation with `--pending-deprecation` support. |

Type: Documentation-only (supports [`--pending-deprecation`](13-cli.md#--pending-deprecation))

`process.binding('uv').errname()` is deprecated. Please use
[`util.getSystemErrorName()`](60-util.md#utilgetsystemerrornameerr) instead.

### DEP0120: Windows Performance Counter support

**History**

| Version | Changes |
| --- | --- |
| v12.0.0 | End-of-Life. |
| v11.0.0 | Runtime deprecation. |

Type: End-of-Life

Windows Performance Counter support has been removed from Node.js. The
undocumented `COUNTER_NET_SERVER_CONNECTION()`,
`COUNTER_NET_SERVER_CONNECTION_CLOSE()`, `COUNTER_HTTP_SERVER_REQUEST()`,
`COUNTER_HTTP_SERVER_RESPONSE()`, `COUNTER_HTTP_CLIENT_REQUEST()`, and
`COUNTER_HTTP_CLIENT_RESPONSE()` functions have been deprecated.

### DEP0121: `net._setSimultaneousAccepts()`

**History**

| Version | Changes |
| --- | --- |
| v24.0.0 | End-of-Life. |
| v12.0.0 | Runtime deprecation. |

Type: End-of-Life

The undocumented `net._setSimultaneousAccepts()` function was originally
intended for debugging and performance tuning when using the
`node:child_process` and `node:cluster` modules on Windows. The function is not
generally useful and is being removed. See discussion here:
<https://github.com/nodejs/node/issues/18391>

### DEP0122: `tls` `Server.prototype.setOptions()`

**History**

| Version | Changes |
| --- | --- |
| v24.0.0 | End-of-Life. |
| v12.0.0 | Runtime deprecation. |

Type: End-of-Life

Please use `Server.prototype.setSecureContext()` instead.

### DEP0123: setting the TLS ServerName to an IP address

**History**

| Version | Changes |
| --- | --- |
| v25.0.0 | End-of-Life. |
| v12.0.0 | Runtime deprecation. |

Type: End-of-Life

Setting the TLS ServerName to an IP address is not permitted by
[RFC 6066](https://tools.ietf.org/html/rfc6066#section-3).

### DEP0124: using `REPLServer.rli`

**History**

| Version | Changes |
| --- | --- |
| v15.0.0 | End-of-Life. |
| v12.0.0 | Runtime deprecation. |

Type: End-of-Life

This property is a reference to the instance itself.

### DEP0125: `require('node:_stream_wrap')`

**History**

| Version | Changes |
| --- | --- |
| v26.0.0 | End-of-Life. |
| v12.0.0 | Runtime deprecation. |

Type: End-of-Life

The `node:_stream_wrap` module is deprecated.

### DEP0126: `timers.active()`

**History**

| Version | Changes |
| --- | --- |
| v24.0.0 | End-of-Life. |
| v11.14.0 | Runtime deprecation. |

Type: End-of-Life

The previously undocumented `timers.active()` has been removed.
Please use the publicly documented [`timeout.refresh()`](54-timers.md#timeoutrefresh) instead.
If re-referencing the timeout is necessary, [`timeout.ref()`](54-timers.md#timeoutref) can be used
with no performance impact since Node.js 10.

An automated migration is available ([source](https://github.com/nodejs/userland-migrations/tree/main/recipes/timers-deprecations)).

```bash
npx codemod @nodejs/timers-deprecations
```

### DEP0127: `timers._unrefActive()`

**History**

| Version | Changes |
| --- | --- |
| v24.0.0 | End-of-Life. |
| v11.14.0 | Runtime deprecation. |

Type: End-of-Life

The previously undocumented and "private" `timers._unrefActive()` has been removed.
Please use the publicly documented [`timeout.refresh()`](54-timers.md#timeoutrefresh) instead.
If unreferencing the timeout is necessary, [`timeout.unref()`](54-timers.md#timeoutunref) can be used
with no performance impact since Node.js 10.

An automated migration is available ([source](https://github.com/nodejs/userland-migrations/tree/main/recipes/timers-deprecations)).

```bash
npx codemod @nodejs/timers-deprecations
```

### DEP0128: modules with an invalid `main` entry and an `index.js` file

**History**

| Version | Changes |
| --- | --- |
| v16.0.0 | Runtime deprecation. |
| v12.0.0 | Documentation-only. |

Type: Runtime

Modules that have an invalid `main` entry (e.g., `./does-not-exist.js`) and
also have an `index.js` file in the top level directory will resolve the
`index.js` file. That is deprecated and is going to throw an error in future
Node.js versions.

### DEP0129: `ChildProcess._channel`

**History**

| Version | Changes |
| --- | --- |
| v25.0.0 | End-of-Life. |
| v13.0.0 | Runtime deprecation. |
| v11.14.0 | Documentation-only. |

Type: End-of-Life

The `_channel` property of child process objects returned by `spawn()` and
similar functions is not intended for public use. Use `ChildProcess.channel`
instead.

### DEP0130: `Module.createRequireFromPath()`

**History**

| Version | Changes |
| --- | --- |
| v16.0.0 | End-of-life. |
| v13.0.0 | Runtime deprecation. |
| v12.2.0 | Documentation-only. |

Type: End-of-Life

Use [`module.createRequire()`](35-module.md#modulecreaterequirefilename) instead.

An automated migration is available ([source](https://github.com/nodejs/userland-migrations/tree/main/recipes/create-require-from-path)):

```bash
npx codemod@latest @nodejs/create-require-from-path
```

### DEP0131: Legacy HTTP parser

**History**

| Version | Changes |
| --- | --- |
| v13.0.0 | This feature has been removed. |
| v12.22.0 | Runtime deprecation. |
| v12.3.0 | Documentation-only. |

Type: End-of-Life

The legacy HTTP parser, used by default in versions of Node.js prior to 12.0.0,
is deprecated and has been removed in v13.0.0. Prior to v13.0.0, the
`--http-parser=legacy` command-line flag could be used to revert to using the
legacy parser.

### DEP0132: `worker.terminate()` with callback

**History**

| Version | Changes |
| --- | --- |
| v25.0.0 | End-of-Life. |
| v12.5.0 | Runtime deprecation. |

Type: End-of-Life

Passing a callback to [`worker.terminate()`](67-worker-threads.md#workerterminate) is deprecated. Use the returned
`Promise` instead, or a listener to the worker's `'exit'` event.

### DEP0133: `http` `connection`

**History**

| Version | Changes |
| --- | --- |
| v12.12.0 | Documentation-only deprecation. |

Type: Documentation-only

Prefer [`response.socket`](27-http.md#responsesocket) over [`response.connection`](27-http.md#responseconnection) and
[`request.socket`](27-http.md#requestsocket) over [`request.connection`](27-http.md#requestconnection).

### DEP0134: `process._tickCallback`

**History**

| Version | Changes |
| --- | --- |
| v12.12.0 | Documentation-only deprecation with `--pending-deprecation` support. |

Type: Documentation-only (supports [`--pending-deprecation`](13-cli.md#--pending-deprecation))

The `process._tickCallback` property was never documented as
an officially supported API.

### DEP0135: `WriteStream.open()` and `ReadStream.open()` are internal

**History**

| Version | Changes |
| --- | --- |
| v25.0.0 | End-of-Life. |
| v13.0.0 | Runtime deprecation. |

Type: End-of-Life

[`WriteStream.open()`](24-fs.md#class-fswritestream) and [`ReadStream.open()`](24-fs.md#class-fsreadstream) are undocumented internal
APIs that do not make sense to use in userland. File streams should always be
opened through their corresponding factory methods [`fs.createWriteStream()`](24-fs.md#fscreatewritestreampath-options)
and [`fs.createReadStream()`](24-fs.md#fscreatereadstreampath-options)) or by passing a file descriptor in options.

### DEP0136: `http` `finished`

**History**

| Version | Changes |
| --- | --- |
| v13.4.0, v12.16.0 | Documentation-only deprecation. |

Type: Documentation-only

[`response.finished`](27-http.md#responsefinished) indicates whether [`response.end()`](27-http.md#responseenddata-encoding-callback) has been
called, not whether `'finish'` has been emitted and the underlying data
is flushed.

Use [`response.writableFinished`](27-http.md#responsewritablefinished) or [`response.writableEnded`](27-http.md#responsewritableended)
accordingly instead to avoid the ambiguity.

To maintain existing behavior `response.finished` should be replaced with
`response.writableEnded`.

### DEP0137: Closing fs.FileHandle on garbage collection

**History**

| Version | Changes |
| --- | --- |
| v25.0.0 | End-of-Life. |
| v14.0.0 | Runtime deprecation. |

Type: End-of-Life

Allowing a [`fs.FileHandle`](24-fs.md#class-filehandle) object to be closed on garbage collection used
to be allowed, but now throws an error.

Please ensure that all `fs.FileHandle` objects are explicitly closed using
`FileHandle.prototype.close()` when the `fs.FileHandle` is no longer needed:

```js
const fsPromises = require('node:fs').promises;
async function openAndClose() {
  let filehandle;
  try {
    filehandle = await fsPromises.open('thefile.txt', 'r');
  } finally {
    if (filehandle !== undefined)
      await filehandle.close();
  }
}
```

### DEP0138: `process.mainModule`

**History**

| Version | Changes |
| --- | --- |
| v14.0.0 | Documentation-only deprecation. |

Type: Documentation-only

[`process.mainModule`](43-process.md#processmainmodule) is a CommonJS-only feature while `process` global
object is shared with non-CommonJS environment. Its use within ECMAScript
modules is unsupported.

It is deprecated in favor of [`require.main`](33-modules.md#accessing-the-main-module), because it serves the same
purpose and is only available on CommonJS environment.

An automated migration is available ([source](https://github.com/nodejs/userland-migrations/tree/main/recipes/process-main-module)):

```bash
npx codemod@latest @nodejs/process-main-module
```

### DEP0139: `process.umask()` with no arguments

**History**

| Version | Changes |
| --- | --- |
| v14.0.0, v12.19.0 | Documentation-only deprecation. |

Type: Documentation-only

Calling `process.umask()` with no argument causes the process-wide umask to be
written twice. This introduces a race condition between threads, and is a
potential security vulnerability. There is no safe, cross-platform alternative
API.

### DEP0140: Use `request.destroy()` instead of `request.abort()`

**History**

| Version | Changes |
| --- | --- |
| v14.1.0, v13.14.0 | Documentation-only deprecation. |

Type: Documentation-only

Use [`request.destroy()`](27-http.md#requestdestroyerror) instead of [`request.abort()`](27-http.md#requestabort).

### DEP0141: `repl.inputStream` and `repl.outputStream`

**History**

| Version | Changes |
| --- | --- |
| v14.3.0 | Documentation-only deprecation with `--pending-deprecation` support. |

Type: Documentation-only (supports [`--pending-deprecation`](13-cli.md#--pending-deprecation))

The `node:repl` module exported the input and output stream twice. Use `.input`
instead of `.inputStream` and `.output` instead of `.outputStream`.

### DEP0142: `repl._builtinLibs`

**History**

| Version | Changes |
| --- | --- |
| v14.3.0 | Documentation-only deprecation with `--pending-deprecation` support. |

Type: Documentation-only (supports [`--pending-deprecation`](13-cli.md#--pending-deprecation))

The `node:repl` module exports a `_builtinLibs` property that contains an array
of built-in modules. It was incomplete so far and instead it's better to rely
upon `require('node:module').builtinModules`.

An automated migration is available ([source](https://github.com/nodejs/userland-migrations/tree/main/recipes/repl-builtin-modules)):

```bash
npx codemod@latest @nodejs/repl-builtin-modules
```

### DEP0143: `Transform._transformState`

**History**

| Version | Changes |
| --- | --- |
| v15.0.0 | End-of-Life. |
| v14.5.0 | Runtime deprecation. |

Type: End-of-Life

`Transform._transformState` will be removed in future versions where it is
no longer required due to simplification of the implementation.

### DEP0144: `module.parent`

**History**

| Version | Changes |
| --- | --- |
| v14.6.0, v12.19.0 | Documentation-only deprecation. |

Type: Documentation-only (supports [`--pending-deprecation`](13-cli.md#--pending-deprecation))

A CommonJS module can access the first module that required it using
`module.parent`. This feature is deprecated because it does not work
consistently in the presence of ECMAScript modules and because it gives an
inaccurate representation of the CommonJS module graph.

Some modules use it to check if they are the entry point of the current process.
Instead, it is recommended to compare `require.main` and `module`:

```js
if (require.main === module) {
  // Code section that will run only if current file is the entry point.
}
```

When looking for the CommonJS modules that have required the current one,
`require.cache` and `module.children` can be used:

```js
const moduleParents = Object.values(require.cache)
  .filter((m) => m.children.includes(module));
```

### DEP0145: `socket.bufferSize`

**History**

| Version | Changes |
| --- | --- |
| v14.6.0 | Documentation-only deprecation. |

Type: Documentation-only

[`socket.bufferSize`](38-net.md#socketbuffersize) is just an alias for [`writable.writableLength`](51-stream.md#writablewritablelength).

### DEP0146: `new crypto.Certificate()`

**History**

| Version | Changes |
| --- | --- |
| v14.9.0 | Documentation-only deprecation. |

Type: Documentation-only

The [`crypto.Certificate()` constructor](15-crypto.md#legacy-api) is deprecated. Use
[static methods of `crypto.Certificate()`](15-crypto.md#class-certificate) instead.

### DEP0147: `fs.rmdir(path, { recursive: true })`

**History**

| Version | Changes |
| --- | --- |
| v25.0.0 | End-of-Life. |
| v16.0.0 | Runtime deprecation. |
| v15.0.0 | Runtime deprecation for permissive behavior. |
| v14.14.0 | Documentation-only deprecation. |

Type: End-of-Life

The `fs.rmdir`, `fs.rmdirSync`, and `fs.promises.rmdir` methods used
to support a `recursive` option. That option has been removed.

Use `fs.rm(path, { recursive: true, force: true })`,
`fs.rmSync(path, { recursive: true, force: true })` or
`fs.promises.rm(path, { recursive: true, force: true })` instead.

An automated migration is available ([source](https://github.com/nodejs/userland-migrations/tree/main/recipes/rmdir)):

```bash
npx codemod@latest @nodejs/rmdir
```

### DEP0148: Folder mappings in `"exports"` (trailing `"/"`)

**History**

| Version | Changes |
| --- | --- |
| v17.0.0 | End-of-Life. |
| v16.0.0 | Runtime deprecation. |
| v15.1.0 | Runtime deprecation for self-referencing imports. |
| v14.13.0 | Documentation-only deprecation. |

Type: End-of-Life

Using a trailing `"/"` to define subpath folder mappings in the
[subpath exports](36-packages.md#subpath-exports) or [subpath imports](36-packages.md#subpath-imports) fields is no longer supported.
Use [subpath patterns](36-packages.md#subpath-patterns) instead.

### DEP0149: `http.IncomingMessage#connection`

**History**

| Version | Changes |
| --- | --- |
| v16.0.0 | Documentation-only deprecation. |

Type: Documentation-only

Prefer [`message.socket`](27-http.md#messagesocket) over [`message.connection`](27-http.md#messageconnection).

### DEP0150: Changing the value of `process.config`

**History**

| Version | Changes |
| --- | --- |
| v19.0.0 | End-of-Life. |
| v16.0.0 | Runtime deprecation. |

Type: End-of-Life

The `process.config` property provides access to Node.js compile-time settings.
However, the property is mutable and therefore subject to tampering. The ability
to change the value will be removed in a future version of Node.js.

### DEP0151: Main index lookup and extension searching

**History**

| Version | Changes |
| --- | --- |
| v16.0.0 | Runtime deprecation. |
| v15.8.0, v14.18.0 | Documentation-only deprecation with `--pending-deprecation` support. |

Type: Runtime

Previously, `index.js` and extension searching lookups would apply to
`import 'pkg'` main entry point resolution, even when resolving ES modules.

With this deprecation, all ES module main entry point resolutions require
an explicit [`"exports"` or `"main"` entry](36-packages.md#main-entry-point-export) with the exact file extension.

### DEP0152: Extension PerformanceEntry properties

**History**

| Version | Changes |
| --- | --- |
| v25.0.0 | End-of-Life. |
| v16.0.0 | Runtime deprecation. |

Type: End-of-Life

The `'gc'`, `'http2'`, and `'http'` [`<PerformanceEntry>`](41-perf-hooks.md#class-performanceentry) object types used to have
additional properties assigned to them that provide additional information.
These properties are now available within the standard `detail` property
of the `PerformanceEntry` object. The deprecated accessors have been
removed.

### DEP0153: `dns.lookup` and `dnsPromises.lookup` options type coercion

**History**

| Version | Changes |
| --- | --- |
| v18.0.0 | End-of-Life. |
| v17.0.0 | Runtime deprecation. |
| v16.8.0 | Documentation-only deprecation. |

Type: End-of-Life

Using a non-nullish non-integer value for `family` option, a non-nullish
non-number value for `hints` option, a non-nullish non-boolean value for `all`
option, or a non-nullish non-boolean value for `verbatim` option in
[`dns.lookup()`](19-dns.md#dnslookuphostname-options-callback) and [`dnsPromises.lookup()`](19-dns.md#dnspromiseslookuphostname-options) throws an
`ERR_INVALID_ARG_TYPE` error.

### DEP0154: RSA-PSS generate key pair options

**History**

| Version | Changes |
| --- | --- |
| v25.0.0 | End-of-Life. |
| v20.0.0 | Runtime deprecation. |
| v16.10.0 | Documentation-only deprecation. |

Type: End-of-Life

Use `'hashAlgorithm'` instead of `'hash'`, and `'mgf1HashAlgorithm'` instead of `'mgf1Hash'`.

An automated migration is available ([source](https://github.com/nodejs/userland-migrations/tree/main/recipes/crypto-rsa-pss-update)):

```bash
npx codemod@latest @nodejs/crypto-rsa-pss-update
```

### DEP0155: Trailing slashes in pattern specifier resolutions

**History**

| Version | Changes |
| --- | --- |
| v17.0.0 | Runtime deprecation. |
| v16.10.0 | Documentation-only deprecation with `--pending-deprecation` support. |

Type: Runtime

The remapping of specifiers ending in `"/"` like `import 'pkg/x/'` is deprecated
for package `"exports"` and `"imports"` pattern resolutions.

### DEP0156: `.aborted` property and `'abort'`, `'aborted'` event in `http`

**History**

| Version | Changes |
| --- | --- |
| v17.0.0, v16.12.0 | Documentation-only deprecation. |

Type: Documentation-only

Move to [`<Stream>`](51-stream.md#stream) API instead, as the [`http.ClientRequest`](27-http.md#class-httpclientrequest),
[`http.ServerResponse`](27-http.md#class-httpserverresponse), and [`http.IncomingMessage`](27-http.md#class-httpincomingmessage) are all stream-based.
Check `stream.destroyed` instead of the `.aborted` property, and listen for
`'close'` instead of `'abort'`, `'aborted'` event.

The `.aborted` property and `'abort'` event are only useful for detecting
`.abort()` calls. For closing a request early, use the Stream
`.destroy([error])` then check the `.destroyed` property and `'close'` event
should have the same effect. The receiving end should also check the
[`readable.readableEnded`](51-stream.md#readablereadableended) value on [`http.IncomingMessage`](27-http.md#class-httpincomingmessage) to get whether
it was an aborted or graceful destroy.

### DEP0157: Thenable support in streams

**History**

| Version | Changes |
| --- | --- |
| v18.0.0 | End-of-life. |
| v17.2.0, v16.14.0 | Documentation-only deprecation. |

Type: End-of-Life

An undocumented feature of Node.js streams was to support thenables in
implementation methods. This is now deprecated, use callbacks instead and avoid
use of async function for streams implementation methods.

This feature caused users to encounter unexpected problems where the user
implements the function in callback style but uses e.g. an async method which
would cause an error since mixing promise and callback semantics is not valid.

```js
const w = new Writable({
  async final(callback) {
    await someOp();
    callback();
  },
});
```

### DEP0158: `buffer.slice(start, end)`

**History**

| Version | Changes |
| --- | --- |
| v17.5.0, v16.15.0 | Documentation-only deprecation. |

Type: Documentation-only

This method was deprecated because it is not compatible with
`Uint8Array.prototype.slice()`, which is a superclass of `Buffer`.

Use [`buffer.subarray`](07-buffer.md#bufsubarraystart-end) which does the same thing instead.

### DEP0159: `ERR_INVALID_CALLBACK`

**History**

| Version | Changes |
| --- | --- |
| v18.0.0 | End-of-Life. |

Type: End-of-Life

This error code was removed due to adding more confusion to
the errors used for value type validation.

An automated migration is available ([source](https://github.com/nodejs/userland-migrations/tree/main/recipes/err-invalid-callback)):

```bash
npx codemod @nodejs/err-invalid-callback
```

### DEP0160: `process.on('multipleResolves', handler)`

**History**

| Version | Changes |
| --- | --- |
| v25.0.0 | End-of-Life. |
| v18.0.0 | Runtime deprecation. |
| v17.6.0, v16.15.0 | Documentation-only deprecation. |

Type: End-of-Life

This event was deprecated and removed because it did not work with V8 promise
combinators which diminished its usefulness.

### DEP0161: `process._getActiveRequests()` and `process._getActiveHandles()`

**History**

| Version | Changes |
| --- | --- |
| v17.6.0, v16.15.0 | Documentation-only deprecation. |

Type: Documentation-only

The `process._getActiveHandles()` and `process._getActiveRequests()`
functions are not intended for public use and can be removed in future
releases.

Use [`process.getActiveResourcesInfo()`](43-process.md#processgetactiveresourcesinfo) to get a list of types of active
resources and not the actual references.

### DEP0162: `fs.write()`, `fs.writeFileSync()` coercion to string

**History**

| Version | Changes |
| --- | --- |
| v19.0.0 | End-of-Life. |
| v18.0.0 | Runtime deprecation. |
| v17.8.0, v16.15.0 | Documentation-only deprecation. |

Type: End-of-Life

Implicit coercion of objects with own `toString` property, passed as second
parameter in [`fs.write()`](24-fs.md#fswritefd-buffer-offset-length-position-callback), [`fs.writeFile()`](24-fs.md#fswritefilefile-data-options-callback), [`fs.appendFile()`](24-fs.md#fsappendfilepath-data-options-callback),
[`fs.writeFileSync()`](24-fs.md#fswritefilesyncfile-data-options), and [`fs.appendFileSync()`](24-fs.md#fsappendfilesyncpath-data-options) is deprecated.
Convert them to primitive strings.

### DEP0163: `channel.subscribe(onMessage)`, `channel.unsubscribe(onMessage)`

**History**

| Version | Changes |
| --- | --- |
| v24.8.0, v22.20.0 | Deprecation revoked. |
| v18.7.0, v16.17.0 | Documentation-only deprecation. |

Type: Deprecation revoked

These methods were deprecated because their use could leave the channel object
vulnerable to being garbage-collected if not strongly referenced by the user.
The deprecation was revoked because channel objects are now resistant to
garbage collection when the channel has active subscribers.

### DEP0164: `process.exit(code)`, `process.exitCode` coercion to integer

**History**

| Version | Changes |
| --- | --- |
| v20.0.0 | End-of-Life. |
| v19.0.0 | Runtime deprecation. |
| v18.10.0, v16.18.0 | Documentation-only deprecation of `process.exitCode` integer coercion. |
| v18.7.0, v16.17.0 | Documentation-only deprecation of `process.exit(code)` integer coercion. |

Type: End-of-Life

Values other than `undefined`, `null`, integer numbers, and integer strings
(e.g., `'1'`) are deprecated as value for the `code` parameter in
[`process.exit()`](43-process.md#processexitcode) and as value to assign to [`process.exitCode`](43-process.md#processexitcode_1).

### DEP0165: `--trace-atomics-wait`

**History**

| Version | Changes |
| --- | --- |
| v23.0.0 | End-of-Life. |
| v22.0.0 | Runtime deprecation. |
| v18.8.0, v16.18.0 | Documentation-only deprecation. |

Type: End-of-Life

The `--trace-atomics-wait` flag has been removed because
it uses the V8 hook `SetAtomicsWaitCallback`,
that will be removed in a future V8 release.

### DEP0166: Double slashes in imports and exports targets

**History**

| Version | Changes |
| --- | --- |
| v19.0.0 | Runtime deprecation. |
| v18.10.0 | Documentation-only deprecation with `--pending-deprecation` support. |

Type: Runtime

Package imports and exports targets mapping into paths including a double slash
(of *"/"* or *"\"*) are deprecated and will fail with a resolution validation
error in a future release. This same deprecation also applies to pattern matches
starting or ending in a slash.

### DEP0167: Weak `DiffieHellmanGroup` instances (`modp1`, `modp2`, `modp5`)

**History**

| Version | Changes |
| --- | --- |
| v18.10.0, v16.18.0 | Documentation-only deprecation. |

Type: Documentation-only

The well-known MODP groups `modp1`, `modp2`, and `modp5` are deprecated because
they are not secure against practical attacks. See [RFC 8247 Section 2.4](https://www.rfc-editor.org/rfc/rfc8247#section-2.4) for
details.

These groups might be removed in future versions of Node.js. Applications that
rely on these groups should evaluate using stronger MODP groups instead.

### DEP0168: Unhandled exception in Node-API callbacks

**History**

| Version | Changes |
| --- | --- |
| v18.3.0, v16.17.0 | Runtime deprecation. |

Type: Runtime

The implicit suppression of uncaught exceptions in Node-API callbacks is now
deprecated.

Set the flag [`--force-node-api-uncaught-exceptions-policy`](13-cli.md#--force-node-api-uncaught-exceptions-policy) to force Node.js
to emit an [`'uncaughtException'`](43-process.md#event-uncaughtexception) event if the exception is not handled in
Node-API callbacks.

### DEP0169: Insecure url.parse()

**History**

| Version | Changes |
| --- | --- |
| v24.0.0 | Application deprecation. |
| v19.9.0, v18.17.0 | Added support for `--pending-deprecation`. |
| v19.0.0, v18.13.0 | Documentation-only deprecation. |

Type: Application (non-`node_modules` code only)

[`url.parse()`](59-url.md#urlparseurlstring-parsequerystring-slashesdenotehost) behavior is not standardized and prone to errors that
have security implications. Use the [WHATWG URL API](59-url.md#the-whatwg-url-api) instead. CVEs are not
issued for `url.parse()` vulnerabilities.

Calling [`url.format(urlString)`](59-url.md#urlformaturlstring) or [`url.resolve()`](59-url.md#urlresolvefrom-to) invokes `url.parse()`
internally, and is therefore also covered by this deprecation.

### DEP0170: Invalid port when using `url.parse()`

**History**

| Version | Changes |
| --- | --- |
| v25.0.0 | End-of-Life. |
| v20.0.0 | Runtime deprecation. |
| v19.2.0, v18.13.0 | Documentation-only deprecation. |

Type: End-of-Life

[`url.parse()`](59-url.md#urlparseurlstring-parsequerystring-slashesdenotehost) used to accept URLs with ports that are not numbers. This
behavior might result in host name spoofing with unexpected input. These URLs
will throw an error (which the [WHATWG URL API](59-url.md#the-whatwg-url-api) also does).

### DEP0171: Setters for `http.IncomingMessage` headers and trailers

**History**

| Version | Changes |
| --- | --- |
| v19.3.0, v18.13.0 | Documentation-only deprecation. |

Type: Documentation-only

In a future version of Node.js, [`message.headers`](27-http.md#messageheaders),
[`message.headersDistinct`](27-http.md#messageheadersdistinct), [`message.trailers`](27-http.md#messagetrailers), and
[`message.trailersDistinct`](27-http.md#messagetrailersdistinct) will be read-only.

### DEP0172: The `asyncResource` property of `AsyncResource` bound functions

**History**

| Version | Changes |
| --- | --- |
| v25.0.0 | End-of-Life. |
| v20.0.0 | Runtime-deprecation. |

Type: End-of-Life

Older versions of Node.js would add the `asyncResource` when a function is
bound to an `AsyncResource`. It no longer does.

### DEP0173: the `assert.CallTracker` class

**History**

| Version | Changes |
| --- | --- |
| v25.0.0 | End-of-Life. |
| v20.1.0 | Runtime deprecation. |

Type: End-of-Life

The `assert.CallTracker` API has been removed.

### DEP0174: calling `promisify` on a function that returns a `Promise`

**History**

| Version | Changes |
| --- | --- |
| v21.0.0 | Runtime deprecation. |
| v20.8.0 | Documentation-only deprecation. |

Type: Runtime

Calling [`util.promisify`](60-util.md#utilpromisifyoriginal) on a function that returns a `Promise` will ignore
the result of said promise, which can lead to unhandled promise rejections.

### DEP0175: `util.toUSVString`

**History**

| Version | Changes |
| --- | --- |
| v20.8.0 | Documentation-only deprecation. |

Type: Documentation-only

The [`util.toUSVString()`](60-util.md#utiltousvstringstring) API is deprecated. Please use
[`String.prototype.toWellFormed`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/String/toWellFormed) instead.

### DEP0176: `fs.F_OK`, `fs.R_OK`, `fs.W_OK`, `fs.X_OK`

**History**

| Version | Changes |
| --- | --- |
| v25.0.0 | End-of-Life. |
| v24.0.0 | Runtime deprecation. |
| v20.8.0 | Documentation-only deprecation. |

Type: End-of-Life

`F_OK`, `R_OK`, `W_OK` and `X_OK` getters exposed directly on `node:fs` were
removed. Get them from `fs.constants` or `fs.promises.constants` instead.

An automated migration is available ([source](https://github.com/nodejs/userland-migrations/tree/main/recipes/fs-access-mode-constants)):

```bash
npx codemod@latest @nodejs/fs-access-mode-constants
```

### DEP0177: `util.types.isWebAssemblyCompiledModule`

**History**

| Version | Changes |
| --- | --- |
| v21.7.0, v20.12.0 | End-of-Life. |
| v21.3.0, v20.11.0 | A deprecation code has been assigned. |
| v14.0.0 | Documentation-only deprecation. |

Type: End-of-Life

The `util.types.isWebAssemblyCompiledModule` API has been removed.
Please use `value instanceof WebAssembly.Module` instead.

### DEP0178: `dirent.path`

**History**

| Version | Changes |
| --- | --- |
| v24.0.0 | End-of-Life. |
| v23.0.0 | Runtime deprecation. |
| v21.5.0, v20.12.0, v18.20.0 | Documentation-only deprecation. |

Type: End-of-Life

The `dirent.path` property has been removed due to its lack of consistency across
release lines. Please use [`dirent.parentPath`](24-fs.md#direntparentpath) instead.

An automated migration is available ([source](https://github.com/nodejs/userland-migrations/tree/main/recipes/dirent-path-to-parent-path)):

```bash
npx codemod@latest @nodejs/dirent-path-to-parent-path
```

### DEP0179: `Hash` constructor

**History**

| Version | Changes |
| --- | --- |
| v22.0.0 | Runtime deprecation. |
| v21.5.0, v20.12.0 | Documentation-only deprecation. |

Type: Runtime

Calling `Hash` class directly with `Hash()` or `new Hash()` is
deprecated due to being internals, not intended for public use.
Please use the [`crypto.createHash()`](15-crypto.md#cryptocreatehashalgorithm-options) method to create Hash instances.

### DEP0180: `fs.Stats` constructor

**History**

| Version | Changes |
| --- | --- |
| v22.0.0 | Runtime deprecation. |
| v20.13.0 | Documentation-only deprecation. |

Type: Runtime

Calling `fs.Stats` class directly with `Stats()` or `new Stats()` is
deprecated due to being internals, not intended for public use.

### DEP0181: `Hmac` constructor

**History**

| Version | Changes |
| --- | --- |
| v22.0.0 | Runtime deprecation. |
| v20.13.0 | Documentation-only deprecation. |

Type: Runtime

Calling `Hmac` class directly with `Hmac()` or `new Hmac()` is
deprecated due to being internals, not intended for public use.
Please use the [`crypto.createHmac()`](15-crypto.md#cryptocreatehmacalgorithm-key-options) method to create Hmac instances.

### DEP0182: Short GCM authentication tags without explicit `authTagLength`

**History**

| Version | Changes |
| --- | --- |
| v26.0.0 | End-of-Life. |
| v23.0.0 | Runtime deprecation. |
| v20.13.0 | Documentation-only deprecation. |

Type: End-of-Life

For ciphers in GCM mode, the [`decipher.setAuthTag()`](15-crypto.md#deciphersetauthtagbuffer-encoding) function used to accept
authentication tags of any valid length (see also [DEP0090](#DEP0090)). This
exception has been removed to better align with recommendations per
[NIST SP 800-38D](https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-38d.pdf), and applications that intend to use authentication tags
that are shorter than the default authentication tag length (i.e., shorter than
16 bytes for AES-GCM) must explicitly set the `authTagLength` option of the
[`crypto.createDecipheriv()`](15-crypto.md#cryptocreatedecipherivalgorithm-key-iv-options) function to the appropriate length.

### DEP0183: OpenSSL engine-based APIs

**History**

| Version | Changes |
| --- | --- |
| v22.4.0, v20.16.0 | Documentation-only deprecation. |

Type: Documentation-only

OpenSSL 3 has deprecated support for custom engines with a recommendation to
switch to its new provider model. The `clientCertEngine` option for
`https.request()`, [`tls.createSecureContext()`](55-tls.md#tlscreatesecurecontextoptions), and [`tls.createServer()`](55-tls.md#tlscreateserveroptions-secureconnectionlistener);
the `privateKeyEngine` and `privateKeyIdentifier` for [`tls.createSecureContext()`](55-tls.md#tlscreatesecurecontextoptions);
and [`crypto.setEngine()`](15-crypto.md#cryptosetengineengine-flags) all depend on this functionality from OpenSSL.

### DEP0184: Instantiating `node:zlib` classes without `new`

**History**

| Version | Changes |
| --- | --- |
| v24.0.0 | Runtime deprecation. |
| v22.9.0, v20.18.0 | Documentation-only deprecation. |

Type: Runtime

Instantiating classes without the `new` qualifier exported by the `node:zlib` module is deprecated.
It is recommended to use the `new` qualifier instead. This applies to all Zlib classes, such as `Deflate`,
`DeflateRaw`, `Gunzip`, `Inflate`, `InflateRaw`, `Unzip`, and `Zlib`.

### DEP0185: Instantiating `node:repl` classes without `new`

**History**

| Version | Changes |
| --- | --- |
| v25.0.0 | End-of-Life. |
| v24.0.0 | Runtime deprecation. |
| v22.9.0, v20.18.0 | Documentation-only deprecation. |

Type: End-of-Life

Instantiating classes without the `new` qualifier exported by the `node:repl` module is deprecated.
The `new` qualifier must be used instead. This applies to all REPL classes, including
`REPLServer` and `Recoverable`.

An automated migration is available ([source](https://github.com/nodejs/userland-migrations/tree/main/recipes/repl-classes-with-new)):

```bash
npx codemod@latest @nodejs/repl-classes-with-new
```

### DEP0187: Passing invalid argument types to `fs.existsSync`

**History**

| Version | Changes |
| --- | --- |
| v24.0.0 | Runtime deprecation. |
| v23.4.0, v22.13.0, v20.19.3 | Documentation-only. |

Type: Runtime

Passing non-supported argument types is deprecated and, instead of returning `false`,
will throw an error in a future version.

### DEP0188: `process.features.ipv6` and `process.features.uv`

**History**

| Version | Changes |
| --- | --- |
| v23.4.0, v22.13.0 | Documentation-only deprecation. |

Type: Documentation-only

These properties are unconditionally `true`. Any checks based on these properties are redundant.

### DEP0189: `process.features.tls_*`

**History**

| Version | Changes |
| --- | --- |
| v23.4.0, v22.13.0 | Documentation-only deprecation. |

Type: Documentation-only

`process.features.tls_alpn`, `process.features.tls_ocsp`, and `process.features.tls_sni` are
deprecated, as their values are guaranteed to be identical to that of `process.features.tls`.

### DEP0190: Passing `args` to `node:child_process` `execFile`/`spawn` with `shell` option

**History**

| Version | Changes |
| --- | --- |
| v24.0.0 | Runtime deprecation. |
| v23.11.0, v22.15.0 | Documentation-only deprecation. |

Type: Runtime

When an `args` array is passed to [`child_process.execFile`](11-child-process.md#child_processexecfilefile-args-options-callback) or [`child_process.spawn`](11-child-process.md#child_processspawncommand-args-options) with the option
`{ shell: true }` or `{ shell: '/path/to/shell' }`, the values are not escaped, only space-separated,
which can lead to shell injection.

### DEP0191: `repl.builtinModules`

**History**

| Version | Changes |
| --- | --- |
| v24.0.0, v22.16.0 | Documentation-only deprecation with `--pending-deprecation` support. |

Type: Documentation-only (supports [`--pending-deprecation`](13-cli.md#--pending-deprecation))

The `node:repl` module exports a `builtinModules` property that contains an array
of built-in modules. This was incomplete and matched the already deprecated
`repl._builtinLibs` ([DEP0142](#dep0142-repl_builtinlibs)) instead it's better to rely
upon `require('node:module').builtinModules`.

An automated migration is available ([source](https://github.com/nodejs/userland-migrations/tree/main/recipes/repl-builtin-modules)):

```bash
npx codemod@latest @nodejs/repl-builtin-modules
```

### DEP0192: `require('node:_tls_common')` and `require('node:_tls_wrap')`

**History**

| Version | Changes |
| --- | --- |
| v24.2.0, v22.17.0, v20.19.6 | Runtime deprecation. |

Type: Runtime

The `node:_tls_common` and `node:_tls_wrap` modules are deprecated as they should be considered
an internal nodejs implementation rather than a public facing API, use `node:tls` instead.

### DEP0193: `require('node:_stream_*')`

**History**

| Version | Changes |
| --- | --- |
| v26.0.0 | End-of-Life. |
| v24.2.0, v22.17.0, v20.19.6 | Runtime deprecation. |

Type: End-of-Life

The `node:_stream_duplex`, `node:_stream_passthrough`, `node:_stream_readable`, `node:_stream_transform`,
`node:_stream_wrap` and `node:_stream_writable` modules are deprecated as they should be considered
an internal nodejs implementation rather than a public facing API, use `node:stream` instead.

### DEP0194: HTTP/2 priority signaling

**History**

| Version | Changes |
| --- | --- |
| v24.2.0 | End-of-Life. |
| v24.2.0, v22.17.0, v20.19.6 | Documentation-only deprecation. |

Type: End-of-Life

The support for priority signaling has been removed following its deprecation in the [RFC 9113](https://datatracker.ietf.org/doc/html/rfc9113#section-5.3.1).

An automated migration is available ([source](https://github.com/nodejs/userland-migrations/tree/main/recipes/http2-priority-signaling)):

```bash
npx codemod@latest @nodejs/http2-priority-signaling
```

### DEP0195: Instantiating `node:http` classes without `new`

**History**

| Version | Changes |
| --- | --- |
| v24.2.0, v22.17.0 | Documentation-only deprecation. |

Type: Documentation-only

Instantiating classes without the `new` qualifier exported by the `node:http` module is deprecated.
It is recommended to use the `new` qualifier instead. This applies to all http classes, such as
`OutgoingMessage`, `IncomingMessage`, `ServerResponse` and `ClientRequest`.

An automated migration is available ([source](https://github.com/nodejs/userland-migrations/tree/main/recipes/http-classes-with-new)):

```bash
npx codemod@latest @nodejs/http-classes-with-new
```

### DEP0196: Calling `node:child_process` functions with `options.shell` as an empty string

**History**

| Version | Changes |
| --- | --- |
| v24.2.0, v22.17.0 | Documentation-only deprecation. |

Type: Documentation-only

Calling the process-spawning functions with `{ shell: '' }` is almost certainly
unintentional, and can cause aberrant behavior.

To make [`child_process.execFile`](11-child-process.md#child_processexecfilefile-args-options-callback) or [`child_process.spawn`](11-child-process.md#child_processspawncommand-args-options) invoke the
default shell, use `{ shell: true }`. If the intention is not to invoke a shell
(default behavior), either omit the `shell` option, or set it to `false` or a
nullish value.

To make [`child_process.exec`](11-child-process.md#child_processexeccommand-options-callback) invoke the default shell, either omit the
`shell` option, or set it to a nullish value. If the intention is not to invoke
a shell, use [`child_process.execFile`](11-child-process.md#child_processexecfilefile-args-options-callback) instead.

### DEP0197: `util.types.isNativeError()`

**History**

| Version | Changes |
| --- | --- |
| v24.2.0 | Documentation-only deprecation. |

Type: Documentation-only

The [`util.types.isNativeError`](60-util.md#utiltypesisnativeerrorvalue) API is deprecated. Please use [`Error.isError`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Error/isError) instead.

An automated migration is available ([source](https://github.com/nodejs/userland-migrations/tree/main/recipes/types-is-native-error)):

```bash
npx codemod@latest @nodejs/types-is-native-error
```

### DEP0198: Creating SHAKE-128 and SHAKE-256 digests without an explicit `options.outputLength`

**History**

| Version | Changes |
| --- | --- |
| v25.0.0 | Runtime deprecation. |
| v24.4.0, v22.18.0, v20.19.5 | Documentation-only deprecation with support for `--pending-deprecation`. |

Type: Runtime

Creating SHAKE-128 and SHAKE-256 digests without an explicit `options.outputLength` is deprecated.

### DEP0199: `require('node:_http_*')`

**History**

| Version | Changes |
| --- | --- |
| v24.6.0, v22.19.0 | Documentation-only deprecation. |

Type: Documentation-only

The `node:_http_agent`, `node:_http_client`, `node:_http_common`, `node:_http_incoming`,
`node:_http_outgoing` and `node:_http_server` modules are deprecated as they should be considered
an internal nodejs implementation rather than a public facing API, use `node:http` instead.

### DEP0200: Closing fs.Dir on garbage collection

**History**

| Version | Changes |
| --- | --- |
| v24.9.0 | Documentation-only deprecation. |

Type: Documentation-only

Allowing a [`fs.Dir`](24-fs.md#class-fsdir) object to be closed on garbage collection is
deprecated. In the future, doing so might result in a thrown error that will
terminate the process.

Please ensure that all `fs.Dir` objects are explicitly closed using
`Dir.prototype.close()` or `using` keyword:

```js
import { opendir } from 'node:fs/promises';

{
  await using dir = await opendir('/async/disposable/directory');
} // Closed by dir[Symbol.asyncDispose]()

{
  using dir = await opendir('/sync/disposable/directory');
} // Closed by dir[Symbol.dispose]()

{
  const dir = await opendir('/unconditionally/iterated/directory');
  for await (const entry of dir) {
    // process an entry
  } // Closed by iterator
}

{
  let dir;
  try {
    dir = await opendir('/legacy/closeable/directory');
  } finally {
    await dir?.close();
  }
}
```

### DEP0201: Passing `options.type` to `Duplex.toWeb()`

**History**

| Version | Changes |
| --- | --- |
| v26.0.0 | Runtime deprecation. |
| v25.7.0 | Documentation-only deprecation. |

Type: Runtime

Passing the `type` option to [`Duplex.toWeb()`](51-stream.md#streamduplextowebstreamduplex-options) is deprecated. To specify the
type of the readable half of the constructed readable-writable pair, use the
`readableType` option instead.

### DEP0202: `Http1IncomingMessage` and `Http1ServerResponse` options of HTTP/2 servers

**History**

| Version | Changes |
| --- | --- |
| v25.7.0 | Documentation-only deprecation. |

Type: Documentation-only

The `Http1IncomingMessage` and `Http1ServerResponse` options of
[`http2.createServer()`](28-http2.md#http2createserveroptions-onrequesthandler) and [`http2.createSecureServer()`](28-http2.md#http2createsecureserveroptions-onrequesthandler) are
deprecated. Use `http1Options.IncomingMessage` and
`http1Options.ServerResponse` instead.

```js
// Deprecated
const server = http2.createSecureServer({
  allowHTTP1: true,
  Http1IncomingMessage: MyIncomingMessage,
  Http1ServerResponse: MyServerResponse,
});
```

```js
// Use this instead
const server = http2.createSecureServer({
  allowHTTP1: true,
  http1Options: {
    IncomingMessage: MyIncomingMessage,
    ServerResponse: MyServerResponse,
  },
});
```

### DEP0203: Passing `CryptoKey` to `node:crypto` APIs

**History**

| Version | Changes |
| --- | --- |
| v26.0.0 | Runtime deprecation. |
| v25.9.0 | Documentation-only deprecation. |

Type: Runtime

Passing a [`CryptoKey`](65-webcrypto.md#class-cryptokey) to `node:crypto` functions is deprecated and
will throw an error in a future version. This includes
[`crypto.createPublicKey()`](15-crypto.md#cryptocreatepublickeykey), [`crypto.createPrivateKey()`](15-crypto.md#cryptocreateprivatekeykey),
[`crypto.sign()`](15-crypto.md#cryptosignalgorithm-data-key-callback), [`crypto.verify()`](15-crypto.md#cryptoverifyalgorithm-data-key-signature-callback),
[`crypto.publicEncrypt()`](15-crypto.md#cryptopublicencryptkey-buffer), [`crypto.publicDecrypt()`](15-crypto.md#cryptopublicdecryptkey-buffer),
[`crypto.privateEncrypt()`](15-crypto.md#cryptoprivateencryptprivatekey-buffer), [`crypto.privateDecrypt()`](15-crypto.md#cryptoprivatedecryptprivatekey-buffer),
[`Sign.prototype.sign()`](15-crypto.md#signsignprivatekey-outputencoding), [`Verify.prototype.verify()`](15-crypto.md#verifyverifyobject-signature-signatureencoding),
[`crypto.createHmac()`](15-crypto.md#cryptocreatehmacalgorithm-key-options), [`crypto.createCipheriv()`](15-crypto.md#cryptocreatecipherivalgorithm-key-iv-options),
[`crypto.createDecipheriv()`](15-crypto.md#cryptocreatedecipherivalgorithm-key-iv-options), [`crypto.encapsulate()`](15-crypto.md#cryptoencapsulatekey-callback), and
[`crypto.decapsulate()`](15-crypto.md#cryptodecapsulatekey-ciphertext-callback).

### DEP0204: `KeyObject.from()` with non-extractable `CryptoKey`

**History**

| Version | Changes |
| --- | --- |
| v26.0.0 | Runtime deprecation. |
| v25.9.0 | Documentation-only deprecation. |

Type: Runtime

Passing a non-extractable [`CryptoKey`](65-webcrypto.md#class-cryptokey) to [`KeyObject.from()`](15-crypto.md#static-method-keyobjectfromkey) is
deprecated and will throw an error in a future version.

### DEP0205: `module.register()`

**History**

| Version | Changes |
| --- | --- |
| v26.0.0 | Runtime deprecation. |
| v25.9.0 | Documentation-only deprecation. |

Type: Runtime

[`module.register()`](35-module.md#moduleregisterspecifier-parenturl-options) is deprecated. Use [`module.registerHooks()`](35-module.md#moduleregisterhooksoptions)
instead.

The `module.register()` API provides off-thread async hooks for customizing ES modules;
the `module.registerHooks()` API provides similar hooks that are synchronous, in-thread, and
work for all types of modules.
Supporting async hooks has proven to be complex, involving worker threads orchestration, and there are issues
that have proven unresolvable. See [caveats of asynchronous customization hooks](35-module.md#caveats-of-asynchronous-customization-hooks). Please migrate to
`module.registerHooks()` as soon as possible as `module.register()` will be
removed in a future version of Node.js.

### DEP0206: Calling `digest()` on an already-finalized `Hmac` instance

**History**

| Version | Changes |
| --- | --- |
| v26.2.0 | Documentation-only deprecation. |

Type: Documentation-only

Calling `hmac.digest()` more than once returns an empty buffer instead of
throwing an error. This behavior is inconsistent with `hash.digest()` and
may lead to subtle bugs. Calling `hmac.digest()` on a finalized `Hmac` instance
will throw an error in a future version.

### DEP0208: `Server.prototype._listen2`

**History**

| Version | Changes |
| --- | --- |
| v26.8.2 | Documentation-only. |

Type: Documentation-only

`net.Server.prototype._listen2` is an undocumented alias for an internal
function that sets up the listening handle. It is kept only so that code
replacing it keeps being called by [`server.listen()`](38-net.md#serverlisten), and it will be
removed in a future version of Node.js. Use [`server.listen()`](38-net.md#serverlisten) instead of
calling or overriding `_listen2`.

### DEP0209: Using `AbortSignal` to dispose of resources

**History**

| Version | Changes |
| --- | --- |
| v26.8.2 | Documentation-only deprecation. |

Type: Documentation-only

Using `AbortSignal` to destroy long-lived resources is deprecated. Prefer
`using` for resource cleanup.

`AbortSignal` is still a good fit for canceling actions, propagating
cancellation from the outside, and timeouts.

```js
// Deprecated
async function example() {
  const ac = new AbortController();
  const server = http.createServer(handler);
  server.listen({ port: 3000, signal: ac.signal });

  await doWork();
  ac.abort();
}
```

```js
// Use this instead
async function example() {
  await using server = http.createServer(handler);
  server.listen(3000);

  await doWork();
}
```

```js
// Deprecated
async function example() {
  const ac = new AbortController();
  const stream = addAbortSignal(ac.signal, fs.createReadStream(file));

  await consume(stream);
  ac.abort();
}
```

```js
// Use this instead
async function example() {
  await using stream = fs.createReadStream(file);

  await consume(stream);
}
```

```js
// Deprecated
async function example() {
  const ac = new AbortController();
  const child = spawn(command, args, { signal: ac.signal });

  await doWork();
  ac.abort();
}
```

```js
// Use this instead
async function example() {
  using child = spawn(command, args);

  await doWork();
}
```

### DEP0210: `sqlite.DatabaseSync`

**History**

| Version | Changes |
| --- | --- |
| v26.11.0 | Documentation-only deprecation. |

Type: Documentation-only

`node:sqlite`'s `DatabaseSync` class was renamed to `Database`. `DatabaseSync`
is kept as a deprecated alias. Use `Database` instead.

### DEP0211: `sqlite.StatementSync`

**History**

| Version | Changes |
| --- | --- |
| v26.11.0 | Documentation-only deprecation. |

Type: Documentation-only

`node:sqlite`'s `StatementSync` class was renamed to `Statement`.
`StatementSync` is kept as a deprecated alias. Use `Statement` instead.

[Contents](../README.md) | [Previous: Debugger](16-debugger.md) | [Next: Diagnostics Channel](18-diagnostics-channel.md)
