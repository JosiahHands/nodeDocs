# Node.js API Docs Summary (1%)

This page condenses the complete Node.js v26.12.1 API documentation, all 68 modules in the official order, to about one percent of its length. The full page has 534,255 words; this summary has about 5,486 words, a ratio of 1.03 percent.

**How it was made.** Each section was written from the real documentation text on the full page. Every API name shown in code formatting was checked against the full documentation. About 45 factual claims were also checked individually against the source; the rest was written from the source but not individually verified. Each section gives the purpose, how to import the module, key concepts, main APIs and important notes. A module gets a stability label when its page has a module-level stability line; modules without one are left unlabeled. Core modules get more space; small, experimental or deprecated modules get a few lines.

**Listening time.** At roughly 150 words a minute, a rule of thumb not measured in Edge, this summary takes about 37 minutes to hear, versus about 60 hours for the full page.

**How to use it.** Read it straight through for an overview, or jump to a module from the contents. For details, options and examples, open the matching section of the full page at <https://nodedocs.pages.dev/>. Code samples are left out so the page reads well aloud.

## Contents

1. [About this documentation](#documentation)
2. [Usage and example](#synopsis)
3. [Assertion testing](#assert)
4. [Asynchronous context tracking](#async_context)
5. [Async hooks](#async_hooks)
6. [Benchmark runner](#bench)
7. [Buffer](#buffer)
8. [C++ addons](#addons)
9. [C/C++ addons with Node-API](#n-api)
10. [C++ embedder API](#embedding)
11. [Child processes](#child_process)
12. [Cluster](#cluster)
13. [Command-line options](#cli)
14. [Console](#console)
15. [Crypto](#crypto)
16. [Debugger](#debugger)
17. [Deprecated APIs](#deprecations)
18. [Diagnostics Channel](#diagnostics_channel)
19. [DNS](#dns)
20. [Domain](#domain)
21. [Environment Variables](#environment_variables)
22. [Errors](#errors)
23. [Events](#events)
24. [File system](#fs)
25. [FFI](#ffi)
26. [Globals](#globals)
27. [HTTP](#http)
28. [HTTP/2](#http2)
29. [HTTPS](#https)
30. [Inspector](#inspector)
31. [Internationalization](#intl)
32. [Iterable Streams API](#stream_iter)
33. [Modules: CommonJS modules](#modules)
34. [Modules: ECMAScript modules](#esm)
35. [Modules: node:module API](#module)
36. [Modules: Packages](#packages)
37. [Modules: TypeScript](#typescript)
38. [Net](#net)
39. [OS](#os)
40. [Path](#path)
41. [Performance hooks](#perf_hooks)
42. [Permissions](#permissions)
43. [Process](#process)
44. [Punycode](#punycode)
45. [Query strings](#querystring)
46. [Readline](#readline)
47. [REPL](#repl)
48. [Report](#report)
49. [Single executable applications](#single-executable-applications)
50. [SQLite](#sqlite)
51. [Stream](#stream)
52. [String decoder](#string_decoder)
53. [Test runner](#test)
54. [Timers](#timers)
55. [TLS/SSL](#tls)
56. [Trace events](#tracing)
57. [TTY](#tty)
58. [UDP/datagram](#dgram)
59. [URL](#url)
60. [Utilities](#util)
61. [V8](#v8)
62. [Virtual File System](#vfs)
63. [VM](#vm)
64. [WASI](#wasi)
65. [Web Crypto API](#webcrypto)
66. [Web Streams API](#webstreams)
67. [Worker threads](#worker_threads)
68. [Zlib](#zlib)

<a id="documentation"></a>

## About this documentation

**Purpose.** Explains how the reference is organized and how to read its stability labels.

**Key concepts.** Every section carries a stability index. Stability 0 is Deprecated: it may emit warnings and backward compatibility is not guaranteed. Stability 1 is Experimental: not covered by semantic versioning, not recommended in production, and split into 1.0 Early development, 1.1 Active development and 1.2 Release candidate. Stability 2 is Stable, with npm ecosystem compatibility as a high priority. Stability 3 is Legacy: still supported, but no longer actively maintained, and alternatives exist.

**Notes.** Experimental features may need a command-line flag and may emit a warning. The docs also offer JSON output and link system calls to their man pages.

[Full section](https://nodedocs.pages.dev/#documentation)

<a id="synopsis"></a>

## Usage and example

**Purpose.** Shows the basic command form and a first program.

**Key concepts.** You run `node` with options, then a script, an eval string or standard input, then arguments. The example builds a small web server with `createServer()` from `node:http` that answers every request with "Hello, World!".

**Notes.** The full option list is in Command-line options.

[Full section](https://nodedocs.pages.dev/#synopsis)

<a id="assert"></a>

## Assertion testing

**Purpose.** Assertion functions for checking invariants. Stability 2, Stable.

**Import.** `node:assert`, or `node:assert/strict` for strict mode.

**Key concepts.** In strict mode, non-strict methods behave like their strict versions and error messages show a diff. Failed assertions throw `AssertionError`.

**Main APIs.**

- `assert.ok()`, `assert.strictEqual()`, `assert.deepStrictEqual()`, `assert.partialDeepStrictEqual()`
- `assert.throws()`, `assert.rejects()`, `assert.doesNotThrow()`, `assert.match()`, `assert.fail()`

**Notes.** Legacy loose methods such as `assert.equal()` and `assert.deepEqual()` are Stability 3, Legacy; use the strict versions.

[Full section](https://nodedocs.pages.dev/#assert)

<a id="async_context"></a>

## Asynchronous context tracking

**Purpose.** Carries state through callbacks and promise chains, much like thread-local storage. Stability 2, Stable.

**Import.** `AsyncLocalStorage` and `AsyncResource` come from `node:async_hooks`.

**Key concepts.** `asyncLocalStorage.run()` sets a store for the duration of a callback and everything it starts asynchronously; `asyncLocalStorage.getStore()` reads it back anywhere in that chain.

**Main APIs.**

- `AsyncLocalStorage`: `run()`, `getStore()`, `enterWith()`, `exit()`, `AsyncLocalStorage.bind()`, `AsyncLocalStorage.snapshot()`
- `AsyncResource`: `runInAsyncScope()`, `bind()`, `emitDestroy()`

**Notes.** The docs include a troubleshooting section on context loss, which can happen with callback-based code that is not promisified.

[Full section](https://nodedocs.pages.dev/#async_context)

<a id="async_hooks"></a>

## Async hooks

**Purpose.** A low-level API to track the lifetime of asynchronous resources. Stability 1, Experimental.

**Main APIs.** `async_hooks.createHook()`, `asyncHook.enable()`, `async_hooks.executionAsyncId()`, `async_hooks.triggerAsyncId()`.

**Notes.** The docs strongly discourage this API because of usability issues, safety risks and performance costs. Use `AsyncLocalStorage` or Diagnostics Channel instead.

[Full section](https://nodedocs.pages.dev/#async_hooks)

<a id="bench"></a>

## Benchmark runner

**Purpose.** Defines and runs JavaScript benchmarks. Stability 1.0, Early development.

**Import.** `node:bench`, only with the `--experimental-bench` flag.

**Key concepts.** Each sample calls `context.start()` and `context.end()` once around the measured code. Ten warmup runs happen by default.

**Main APIs.** `bench()`, `suite()`, `run()`, `runFile()`, `createRunner()`.

**Notes.** There is no process isolation unless you use `runFile()`; keep raw samples when comparing results.

[Full section](https://nodedocs.pages.dev/#bench)

<a id="buffer"></a>

## Buffer

**Purpose.** Fixed-length sequences of bytes. Stability 2, Stable.

**Import.** `Buffer` is global, but the docs recommend importing it from `node:buffer`.

**Key concepts.** `Buffer` is a subclass of `Uint8Array`, and Node.js APIs accept plain `Uint8Array` values too. Converting to and from strings uses an encoding such as utf8, hex, base64, base64url or latin1; utf8 is the default.

**Main APIs.**

- Creating: `Buffer.alloc()`, `Buffer.allocUnsafe()`, `Buffer.from()`, `Buffer.concat()`
- Inspecting: `Buffer.byteLength()`, `Buffer.isBuffer()`, `Buffer.compare()`, `buf.length`
- Using: `buf.toString()`, `buf.write()`, `buf.subarray()`, `buf.copy()`, `buf.equals()`, `buf.indexOf()`, `buf.fill()`
- Numbers: the read and write families such as `buf.readUInt32LE()` and `buf.writeInt16BE()`
- Also `Blob`, `File`, `buffer.atob()` and `buffer.btoa()`

**Notes.** `Buffer.allocUnsafe()` memory is not initialized and may contain old data, so fill it before exposing it. The `Buffer()` constructor is deprecated (DEP0005). `buf.slice()` is deprecated in favour of `buf.subarray()`, which shares memory with the original.

[Full section](https://nodedocs.pages.dev/#buffer)

<a id="addons"></a>

## C++ addons

**Purpose.** Dynamically linked shared objects, written in C++, that load through `require()` like ordinary modules.

**Key concepts.** Three ways to build them: Node-API, which is recommended; nan, the Native Abstractions for Node.js; or direct use of V8, libuv and bundled libraries. Addons are usually built with node-gyp.

**Notes.** Direct V8 addons must be rebuilt for each Node.js version. Loading addons with `import` is Stability 1.0, Early development, behind `--experimental-addon-modules`.

[Full section](https://nodedocs.pages.dev/#addons)

<a id="n-api"></a>

## C/C++ addons with Node-API

**Purpose.** An ABI-stable C API for native addons, independent of the JavaScript engine. Stability 2, Stable.

**Key concepts.** Modules compiled for one major version run on later versions without recompiling. Every call returns a `napi_status`, and values are opaque `napi_value` handles managed with handle scopes and references.

**Main APIs.** `napi_create_function`, `napi_define_class`, `napi_wrap`, `napi_create_async_work`, `napi_create_threadsafe_function`, `napi_get_cb_info`.

**Notes.** A version matrix shows which Node-API version each Node.js release supports. Wrappers such as node-addon-api exist for C++ and other languages.

[Full section](https://nodedocs.pages.dev/#n-api)

<a id="embedding"></a>

## C++ embedder API

**Purpose.** C++ APIs for running JavaScript inside a Node.js environment from other software.

**Key concepts.** You set up per-process state with `node::InitializeOncePerProcess`, then per-instance state with `node::CreateEnvironment` and `node::LoadEnvironment`, and run the loop with `node::SpinEventLoop`.

**Notes.** Breaking changes here do not follow the normal deprecation policy and may come in any semver-major release.

[Full section](https://nodedocs.pages.dev/#embedding)

<a id="child_process"></a>

## Child processes

**Purpose.** Spawns subprocesses, similar to popen(3). Stability 2, Stable.

**Import.** `node:child_process`.

**Key concepts.** `child_process.spawn()` starts a process without blocking the event loop and gives you streams for stdin, stdout and stderr. `child_process.exec()` runs a command in a shell and buffers output. `child_process.execFile()` runs a file directly without a shell. `child_process.fork()` starts a new Node.js process with an IPC channel, so parent and child can exchange messages. Each async method has a sync twin that blocks until the child exits.

**Main APIs.**

- `child_process.spawn()`, `child_process.exec()`, `child_process.execFile()`, `child_process.fork()`
- `child_process.spawnSync()`, `child_process.execSync()`, `child_process.execFileSync()`
- `ChildProcess` events `'exit'`, `'close'`, `'error'`, `'message'`, `'spawn'`
- `subprocess.kill()`, `subprocess.send()`, `subprocess.pid`, `subprocess.stdout`, `subprocess.unref()`

**Notes.** Never pass unsanitized user input to a shell; it can run arbitrary commands. Output beyond `maxBuffer` terminates the child. On Windows, .bat and .cmd files need a shell. The `'close'` event fires after stdio streams close, which can be after `'exit'`.

[Full section](https://nodedocs.pages.dev/#child_process)

<a id="cluster"></a>

## Cluster

**Purpose.** Runs several Node.js processes that share server ports. Stability 2, Stable.

**Import.** `node:cluster`.

**Key concepts.** A primary process forks workers with `cluster.fork()`. Connections are distributed round-robin by default, except on Windows. When process isolation is not needed, the docs suggest `worker_threads` instead.

**Main APIs.** `cluster.isPrimary`, `cluster.fork()`, `cluster.workers`, `worker.send()`, `worker.kill()`, events `'exit'` and `'online'`.

**Notes.** `cluster.isMaster` and `cluster.setupMaster()` are deprecated aliases. Workers do not share state, so sessions must not rely on memory.

[Full section](https://nodedocs.pages.dev/#cluster)

<a id="cli"></a>

## Command-line options

**Purpose.** Documents every `node` flag and environment variable.

**Key concepts.** Highlights include `--eval`, `--print`, `--check`, `--watch`, `--test`, `--run` for package.json scripts, `--import` and `--require` for preloading, `--env-file`, `--inspect`, `--permission`, `--max-old-space-size`, `--enable-source-maps`, `--no-warnings`, plus flags that enable experimental features. `NODE_OPTIONS` passes options through the environment; only allowed flags work there.

**Notes.** `--config-file` can load options from a JSON file. Run `--help`, or `man node`, for the local list.

[Full section](https://nodedocs.pages.dev/#cli)

<a id="console"></a>

## Console

**Purpose.** A simple debugging console like the one in browsers. Stability 2, Stable.

**Import.** The global `console`, or the `Console` class from `node:console`.

**Main APIs.** `console.log()`, `console.error()`, `console.warn()`, `console.table()`, `console.dir()`, `console.time()`, `console.timeEnd()`, `console.trace()`, `console.group()`, `console.assert()`, `console.count()`.

**Notes.** `console.log()` and `console.info()` write to stdout; `console.error()` and `console.warn()` write to stderr. The global console is neither consistently synchronous nor consistently asynchronous; it depends on the backing stream.

[Full section](https://nodedocs.pages.dev/#console)

<a id="crypto"></a>

## Crypto

**Purpose.** Wraps OpenSSL hashes, HMAC, ciphers, signing, key exchange and key encapsulation. Stability 2, Stable.

**Import.** `node:crypto`. Node.js can be built without crypto, so the import may throw.

**Key concepts.** Most classes follow an update then digest, or update then final, pattern. Keys are best held as `KeyObject` instances. Expensive functions such as `crypto.pbkdf2()` and `crypto.scrypt()` have async versions that run on the libuv threadpool.

**Main APIs.**

- Hashing: `crypto.createHash()`, `crypto.hash()`, `crypto.createHmac()`
- Ciphers: `crypto.createCipheriv()`, `crypto.createDecipheriv()`, including authenticated modes such as GCM with `cipher.getAuthTag()`
- Keys: `crypto.generateKeyPair()`, `crypto.createPublicKey()`, `crypto.createPrivateKey()`, `crypto.createSecretKey()`
- Signing: `crypto.sign()`, `crypto.verify()`, `crypto.createSign()`, `crypto.createVerify()`
- Derivation: `crypto.pbkdf2()`, `crypto.scrypt()`, `crypto.hkdf()`, `crypto.diffieHellman()`
- Random: `crypto.randomBytes()`, `crypto.randomInt()`, `crypto.randomUUID()`
- Safety: `crypto.timingSafeEqual()`; also `crypto.webcrypto` and `crypto.subtle`

**Notes.** Initialization vector rules depend on the algorithm; some need an IV that is unique and unpredictable. Compare secrets with `crypto.timingSafeEqual()` to avoid timing attacks. Some newer algorithms are Stability 1.1. FIPS mode can be enabled with `--enable-fips` or `crypto.setFips()`.

[Full section](https://nodedocs.pages.dev/#crypto)

<a id="debugger"></a>

## Debugger

**Purpose.** A simple command-line debugger. Stability 2, Stable.

**Key concepts.** Start it with `node inspect` and a script. Commands include `cont`, `next`, `step`, `out`, `setBreakpoint`, `watch` and `repl`. A non-interactive probe mode, Stability 1, prints values at chosen locations as a report.

**Notes.** For full debugging, use `--inspect` with Chrome DevTools or another inspector client.

[Full section](https://nodedocs.pages.dev/#debugger)

<a id="deprecations"></a>

## Deprecated APIs

**Purpose.** Lists every deprecation by code, from DEP0001 upward.

**Key concepts.** Four kinds: documentation-only, application (non node_modules code only), runtime (warns on first use) and end-of-life (removed). `--pending-deprecation` surfaces some documentation-only ones; `--throw-deprecation` makes warnings throw; `--no-deprecation` silences them.

**Notes.** Well-known entries include DEP0005 for the `Buffer()` constructor and DEP0032 for the domain module. Deprecations can be revoked.

[Full section](https://nodedocs.pages.dev/#deprecations)

<a id="diagnostics_channel"></a>

## Diagnostics Channel

**Purpose.** Named channels for publishing diagnostic data. Stability 2, Stable.

**Import.** `node:diagnostics_channel`.

**Key concepts.** Publishers call `channel.publish()`; tools call `diagnostics_channel.subscribe()`. `TracingChannel` groups start, end, asyncStart, asyncEnd and error events around an operation. Many built-in modules publish on built-in channels.

**Main APIs.** `diagnostics_channel.channel()`, `diagnostics_channel.subscribe()`, `channel.hasSubscribers`, `diagnostics_channel.tracingChannel()`.

**Notes.** Check `channel.hasSubscribers` before preparing expensive messages. `BoundedChannel` is Experimental.

[Full section](https://nodedocs.pages.dev/#diagnostics_channel)

<a id="dns"></a>

## DNS

**Purpose.** Name resolution. Stability 2, Stable.

**Import.** `node:dns`, or `node:dns/promises`.

**Key concepts.** `dns.lookup()` uses the operating system resolver, like most programs, and runs on the libuv threadpool. The `dns.resolve()` family always queries DNS servers over the network and does not use the threadpool.

**Main APIs.** `dns.lookup()`, `dns.resolve4()`, `dns.resolveMx()`, `dns.resolveTxt()`, `dns.reverse()`, `dns.setServers()`, `dns.Resolver`, `dns.setDefaultResultOrder()`.

**Notes.** Many slow `dns.lookup()` calls can starve the threadpool and delay file system work.

[Full section](https://nodedocs.pages.dev/#dns)

<a id="domain"></a>

## Domain

**Purpose.** Grouped error handling for multiple I/O operations. Stability 0, Deprecated.

**Notes.** The module is pending deprecation and should not be used in new code. The docs warn that ignoring errors with domains leaves the process in an unknown state.

[Full section](https://nodedocs.pages.dev/#domain)

<a id="environment_variables"></a>

## Environment Variables

**Purpose.** How Node.js reads environment variables and .env files.

**Key concepts.** `process.env` holds the environment. Node.js supports .env files with its own parsing rules, loaded with `--env-file`, `--env-file-if-exists`, `process.loadEnvFile()` or parsed with `util.parseEnv()`. The DotEnv utilities are Stability 2, Stable.

**Notes.** All values are read as strings.

[Full section](https://nodedocs.pages.dev/#environment_variables)

<a id="errors"></a>

## Errors

**Purpose.** Explains the errors Node.js raises and how they propagate.

**Key concepts.** There are standard JavaScript errors, system errors from the operating system, user errors and assertion errors. All inherit from `Error`. Synchronous APIs throw; callback APIs pass the error as the first argument; promise APIs reject; event emitters emit `'error'`. Identify errors by `error.code`, because `error.message` can change between versions.

**Main APIs.**

- `Error.captureStackTrace()`, `Error.stackTraceLimit`, `error.cause`, `error.stack`
- `SystemError` properties `error.errno`, `error.syscall`, `error.path`, `error.address`, `error.port`

**Notes.** Common system codes include `EACCES`, `EADDRINUSE`, `ECONNREFUSED`, `ECONNRESET`, `EEXIST`, `EMFILE`, `ENOENT`, `ENOTFOUND`, `EPERM`, `EPIPE` and `ETIMEDOUT`. Node.js codes start with `ERR_`, such as `ERR_INVALID_ARG_TYPE`. Throwing inside an async callback cannot be caught by a surrounding try and catch.

[Full section](https://nodedocs.pages.dev/#errors)

<a id="events"></a>

## Events

**Purpose.** The event-driven core behind most Node.js APIs. Stability 2, Stable.

**Import.** `EventEmitter` from `node:events`.

**Key concepts.** Emitters emit named events and call attached listeners synchronously, in the order they were registered. An `'error'` event with no listener throws and usually crashes the process, so always listen for it. More than 10 listeners for one event prints a possible memory leak warning, which `emitter.setMaxListeners()` adjusts. `captureRejections` routes rejected async listeners to the error handler.

**Main APIs.**

- `emitter.on()`, `emitter.once()`, `emitter.off()`, `emitter.emit()`
- `emitter.prependListener()`, `emitter.removeAllListeners()`, `emitter.listenerCount()`, `emitter.eventNames()`
- `events.once()` returns a promise; `events.on()` returns an async iterator
- `events.errorMonitor`, `events.addAbortListener()`, `events.getEventListeners()`
- Web style `EventTarget`, `Event` and `CustomEvent`

**Notes.** Use `setImmediate()` or `process.nextTick()` in a listener to switch to asynchronous handling. `EventTarget` differs from the DOM version: there is no propagation hierarchy.

[Full section](https://nodedocs.pages.dev/#events)

<a id="fs"></a>

## File system

**Purpose.** File system access modeled on POSIX functions. Stability 2, Stable.

**Import.** `node:fs/promises` for promises, or `node:fs` for callback and synchronous forms.

**Key concepts.** Every operation comes in three styles: promise, error-first callback, and synchronous, which blocks the event loop until done. Asynchronous operations run on the libuv threadpool and are not synchronized, so concurrent changes to the same file can corrupt it. `FileHandle` objects wrap file descriptors in the promise API and should be closed explicitly. Paths can be strings, Buffers or file URLs.

**Main APIs.**

- Read and write: `fsPromises.readFile()`, `fsPromises.writeFile()`, `fsPromises.appendFile()`, `fsPromises.open()`
- Directories: `fsPromises.mkdir()`, `fsPromises.readdir()`, `fsPromises.opendir()`, `fsPromises.rm()`, `fsPromises.mkdtemp()`
- Metadata: `fsPromises.stat()`, `fsPromises.access()`, `fsPromises.chmod()`, `fsPromises.utimes()`
- Moving and copying: `fsPromises.rename()`, `fsPromises.copyFile()`, `fsPromises.cp()`, `fsPromises.symlink()`, `fsPromises.unlink()`
- Streams: `fs.createReadStream()`, `fs.createWriteStream()`
- Watching: `fs.watch()`, `fs.watchFile()`, `fsPromises.watch()`
- Matching: `fsPromises.glob()`
- Classes: `fs.Stats`, `fs.Dir`, `fs.Dirent`, and `fs.constants`

**Notes.** `fs.exists()` is deprecated; use `fs.stat()` or `fs.access()`. Checking access before opening invites race conditions, so open the file and handle the error. `fs.watch()` behaviour varies by platform. Callback-style functions require a callback.

[Full section](https://nodedocs.pages.dev/#fs)

<a id="ffi"></a>

## FFI

**Purpose.** Loads dynamic libraries and calls native symbols from JavaScript. Stability 1, Experimental.

**Import.** `node:ffi`, only in builds with FFI support.

**Main APIs.** `ffi.dlopen()`, `DynamicLibrary`, `library.getFunction()`, `library.registerCallback()`, `ffi.toBuffer()`.

**Notes.** The API is unsafe: wrong pointers or signatures can crash the process or corrupt memory. Under the permission model, it needs `--allow-ffi`.

[Full section](https://nodedocs.pages.dev/#ffi)

<a id="globals"></a>

## Globals

**Purpose.** Objects available everywhere without importing. Stability 2, Stable.

**Key concepts.** Node.js globals include `process`, `Buffer`, `console`, timers such as `setTimeout()`, `queueMicrotask()`, `structuredClone()`, `fetch()`, `AbortController`, `URL`, `TextEncoder`, `WebSocket`, `performance`, `navigator` and Web Streams classes. `__dirname`, `__filename`, `require()`, `module` and `exports` look global but exist only in CommonJS modules.

**Notes.** `AbortSignal.timeout()` and `AbortSignal.any()` help cancel work. `atob()` and `btoa()` are Legacy; use Buffer methods.

[Full section](https://nodedocs.pages.dev/#globals)

<a id="http"></a>

## HTTP

**Purpose.** An HTTP/1.1 client and server. Stability 2, Stable.

**Import.** `node:http`.

**Key concepts.** The module never buffers whole requests or responses, so you can stream data. Header names are lowercased. A server receives an `IncomingMessage` (a readable stream) and a `ServerResponse` (a writable stream). Clients create a `ClientRequest`, which emits `'response'`. An `Agent` manages connection reuse and keep-alive. Servers emit `'request'` for each request and `'upgrade'` when a client asks to switch protocols.

**Main APIs.**

- `http.createServer()`, `server.listen()`, `server.close()`, `server.closeAllConnections()`
- `http.request()`, `http.get()`, `http.Agent`, `http.globalAgent`
- `response.writeHead()`, `response.setHeader()`, `response.write()`, `response.end()`
- `message.headers`, `message.method`, `message.url`, `message.statusCode`
- Timeouts: `server.headersTimeout`, `server.requestTimeout`, `server.keepAliveTimeout`
- `http.METHODS`, `http.STATUS_CODES`, `http.validateHeaderName()`

**Notes.** If you add a `'response'` handler, you must consume the body; until you do, the `'end'` event does not fire and the data uses memory. Call `request.end()` to send a client request. The `'abort'` event and `request.abort()` are deprecated; use `'close'` and `request.destroy()`. Global `fetch()` is a higher-level alternative.

[Full section](https://nodedocs.pages.dev/#http)

<a id="http2"></a>

## HTTP/2

**Purpose.** An HTTP/2 implementation. Stability 2, Stable.

**Import.** `node:http2`.

**Key concepts.** The core API works with sessions and streams: `Http2Session` represents a connection and `Http2Stream` one request. Browsers require TLS, so servers use `http2.createSecureServer()`. A compatibility API mimics `node:http` request and response objects.

**Main APIs.** `http2.createSecureServer()`, `http2.connect()`, `clienthttp2session.request()`, `stream.respond()`, `'stream'` event, `http2.constants`.

**Notes.** Priority signaling is deprecated and no longer supported.

[Full section](https://nodedocs.pages.dev/#http2)

<a id="https"></a>

## HTTPS

**Purpose.** HTTP over TLS. Stability 2, Stable.

**Import.** `node:https`.

**Main APIs.** `https.createServer()` takes TLS options such as `key` and `cert`; `https.request()`, `https.get()`, `https.Agent`, `https.globalAgent`.

**Notes.** It mirrors `node:http` and fails to load in builds without crypto.

[Full section](https://nodedocs.pages.dev/#https)

<a id="inspector"></a>

## Inspector

**Purpose.** Programmatic access to the V8 inspector. Stability 2, Stable.

**Import.** `node:inspector` or `node:inspector/promises`.

**Main APIs.** `inspector.open()`, `inspector.url()`, `inspector.waitForDebugger()`, `inspector.Session` with `session.connect()` and `session.post()`.

**Notes.** The promises API is Stability 1, Experimental, and DevTools integration is Stability 1.1, Active development.

[Full section](https://nodedocs.pages.dev/#inspector)

<a id="intl"></a>

## Internationalization

**Purpose.** How Node.js supports Intl, Unicode and locale features through ICU.

**Key concepts.** Full ICU data ships by default. Build options are none, system-icu, small-icu and full-icu. Features include `Intl`, `String.prototype.localeCompare()`, `Date.prototype.toLocaleString()` and IDN support in the URL parser.

**Notes.** Check support with `process.versions.icu`; extra data can be loaded with `--icu-data-dir` or `NODE_ICU_DATA`.

[Full section](https://nodedocs.pages.dev/#intl)

<a id="stream_iter"></a>

## Iterable Streams API

**Purpose.** A streaming API built on iterables instead of stream classes. Stability 1, Experimental.

**Import.** `node:stream/iter`, with `--experimental-stream-iter`.

**Key concepts.** Streams are async or sync iterables that yield batches of `Uint8Array` chunks. Transforms are plain functions.

**Main APIs.** `from()`, `pull()`, `pipeTo()`, `push()`, `text()`, `merge()`, `fromReadable()`, `toReadable()`.

**Notes.** It implements a WinterTC draft; the classic stream interop functions are Node.js extensions.

[Full section](https://nodedocs.pages.dev/#stream_iter)

<a id="modules"></a>

## Modules: CommonJS modules

**Purpose.** The original Node.js module system, where each file is a module. Stability 2, Stable.

**Key concepts.** `require()` loads a module, and the module shares values by assigning `module.exports` or adding to `exports`. Code is wrapped in a function that provides `exports`, `require`, `module`, `__filename` and `__dirname`. Modules are cached after first load, so later calls return the same object. Resolution tries exact files, then .js, .json and .node extensions, then folders, then node_modules folders up the directory tree. Cycles return a partially finished export object.

**Main APIs.** `require()`, `require.resolve()`, `require.cache`, `require.main`, `module.exports`, `module.children`.

**Notes.** `require()` can load synchronous ES modules, which do not use top-level await. Files are CommonJS by default unless package.json sets "type" to "module" or the extension is .mjs. Folders as modules is Legacy; `require.extensions` and `module.parent` are deprecated. Some built-ins, such as `node:test`, need the node: prefix.

[Full section](https://nodedocs.pages.dev/#modules)

<a id="esm"></a>

## Modules: ECMAScript modules

**Purpose.** The standard JavaScript module format with `import` and `export`. Stability 2, Stable.

**Key concepts.** A file is an ES module if it ends in .mjs, if the nearest package.json has "type" set to "module", or if syntax detection finds ES module syntax. Relative imports need full file extensions. Specifiers can be relative, bare package names, or absolute URLs such as file: and data:. Top-level await is supported. JSON imports need an import attribute with type json.

**Main APIs.** `import()`, `import.meta.url`, `import.meta.dirname`, `import.meta.filename`, `import.meta.resolve()`, `import.meta.main`.

**Notes.** ES modules have no `require`, `__dirname` or `__filename`; use `import.meta` or `module.createRequire()`. Importing CommonJS gives `module.exports` as the default export. Wasm source phase imports are Stability 1.2, Release candidate. Loader hooks live in the node:module API.

[Full section](https://nodedocs.pages.dev/#esm)

<a id="module"></a>

## Modules: node:module API

**Purpose.** Utilities for the module system. Stability 2, Stable.

**Import.** `node:module`.

**Main APIs.**

- `module.createRequire()`, `module.builtinModules`, `module.isBuiltin()`, `module.findPackageJSON()`
- `module.registerHooks()` for synchronous resolve and load customization hooks
- `module.enableCompileCache()`, `module.flushCompileCache()`
- `module.stripTypeScriptTypes()`, `module.findSourceMap()`, `module.SourceMap`

**Notes.** `module.register()`, the asynchronous hooks entry point, is Deprecated; use `module.registerHooks()`.

[Full section](https://nodedocs.pages.dev/#module)

<a id="packages"></a>

## Modules: Packages

**Purpose.** How package.json controls module format and entry points.

**Key concepts.** The "type" field sets the default format for .js files: "commonjs" or "module". The "exports" field defines public entry points and subpaths and can use conditions such as "import", "require", "node" and "default". The "imports" field defines private mappings that start with a hash sign. A package can import itself by name.

**Notes.** Defining "exports" blocks access to unlisted files. Dual CommonJS and ES module packages risk two copies of state. Package maps are Stability 1, Experimental, behind `--experimental-package-map`.

[Full section](https://nodedocs.pages.dev/#packages)

<a id="typescript"></a>

## Modules: TypeScript

**Purpose.** Runs TypeScript files directly. Stability 2, Stable.

**Key concepts.** Type stripping is on by default: Node.js replaces erasable TypeScript syntax with whitespace and does no type checking. Disable it with `--no-strip-types`.

**Notes.** tsconfig.json is ignored, so paths aliases and syntax that needs code generation, such as enums, are not supported. Type imports need the type keyword. For full TypeScript, use a third-party tool such as tsx.

[Full section](https://nodedocs.pages.dev/#typescript)

<a id="net"></a>

## Net

**Purpose.** Stream-based TCP and IPC servers and clients. Stability 2, Stable.

**Import.** `node:net`.

**Key concepts.** `net.Server` accepts connections; each one is a `net.Socket`, a duplex stream. IPC uses Unix domain sockets or Windows named pipes. `server.listen()` accepts a port and host, a path for IPC, or an existing handle. If the port is omitted or is 0, the operating system assigns an unused port, which `server.address()` reports after the `'listening'` event.

**Main APIs.**

- `net.createServer()`, `server.listen()`, `server.close()`, `server.address()`
- `net.createConnection()`, `net.connect()`, `socket.write()`, `socket.end()`, `socket.destroy()`
- `socket.setTimeout()`, `socket.setNoDelay()`, `socket.setKeepAlive()`
- Socket events `'data'`, `'end'`, `'close'`, `'error'`, `'connect'`, `'timeout'`
- `net.isIP()`, `net.BlockList`, `net.SocketAddress`, `net.setDefaultAutoSelectFamily()`

**Notes.** A `'timeout'` event only notifies you; you must end or destroy the socket yourself. Unhandled `'error'` events crash the process.

[Full section](https://nodedocs.pages.dev/#net)

<a id="os"></a>

## OS

**Purpose.** Operating system information. Stability 2, Stable.

**Import.** `node:os`.

**Main APIs.** `os.cpus()`, `os.availableParallelism()`, `os.totalmem()`, `os.freemem()`, `os.homedir()`, `os.tmpdir()`, `os.platform()`, `os.networkInterfaces()`, `os.EOL`, `os.constants`.

**Notes.** `os.constants` lists signals, error codes and priority values.

[Full section](https://nodedocs.pages.dev/#os)

<a id="path"></a>

## Path

**Purpose.** Utilities for file and directory paths. Stability 2, Stable.

**Import.** `node:path`.

**Key concepts.** Behaviour follows the current operating system. Use `path.posix` or `path.win32` for consistent results on any platform.

**Main APIs.**

- `path.join()`, `path.resolve()`, `path.normalize()`, `path.relative()`
- `path.basename()`, `path.dirname()`, `path.extname()`
- `path.parse()`, `path.format()`, `path.isAbsolute()`, `path.matchesGlob()`
- `path.sep`, `path.delimiter`

**Notes.** `path.resolve()` builds an absolute path from right to left using the working directory; `path.join()` just joins and normalizes. On Windows, each drive has its own working directory. `path.parse()` returns an object with root, dir, base, ext and name fields, and `path.format()` reverses it.

[Full section](https://nodedocs.pages.dev/#path)

<a id="perf_hooks"></a>

## Performance hooks

**Purpose.** A subset of the W3C Web Performance APIs plus Node.js timing. Stability 2, Stable.

**Import.** `node:perf_hooks`, or the global `performance`.

**Main APIs.** `performance.now()`, `performance.mark()`, `performance.measure()`, `performance.timerify()`, `performance.eventLoopUtilization()`, `PerformanceObserver`, `perf_hooks.monitorEventLoopDelay()`, `perf_hooks.createHistogram()`.

**Notes.** `performance.nodeTiming` gives startup milestones.

[Full section](https://nodedocs.pages.dev/#perf_hooks)

<a id="permissions"></a>

## Permissions

**Purpose.** Limits what a process can access. The process-based Permission Model is Stability 2, Stable.

**Key concepts.** `--permission` restricts file system, network, child processes, worker threads, addons, WASI, FFI and the inspector. Grant access with `--allow-fs-read`, `--allow-fs-write`, `--allow-net`, `--allow-child-process`, `--allow-worker` and similar flags. Check at runtime with `process.permission.has()`.

**Notes.** It is a seat belt for trusted code, not a security boundary against malicious code. `--permission-audit` reports violations without blocking.

[Full section](https://nodedocs.pages.dev/#permissions)

<a id="process"></a>

## Process

**Purpose.** Information about, and control over, the current process.

**Import.** The global `process`, or `node:process`.

**Key concepts.** `process` is an `EventEmitter`. `'exit'` listeners run synchronous code only. `'beforeExit'` fires when the loop empties. An `'uncaughtException'` handler stops the default crash, but the docs say resuming normal operation afterwards is unsafe. `'unhandledRejection'` reports rejected promises with no handler.

**Main APIs.**

- `process.argv`, `process.env`, `process.cwd()`, `process.chdir()`, `process.platform`, `process.version`, `process.pid`
- `process.exit()`, `process.exitCode`, `process.kill()`, `process.on()` with signals such as `'SIGINT'`
- `process.nextTick()` runs a callback before other queued work
- `process.memoryUsage()`, `process.cpuUsage()`, `process.hrtime.bigint()`, `process.uptime()`
- `process.stdin`, `process.stdout`, `process.stderr`
- `process.emitWarning()`, `process.loadEnvFile()`, `process.getBuiltinModule()`

**Notes.** Prefer setting `process.exitCode` over calling `process.exit()`, which can cut off pending output. Writes to stdout and stderr may be synchronous or asynchronous depending on platform and target. Several `process.features` flags are deprecated.

[Full section](https://nodedocs.pages.dev/#process)

<a id="punycode"></a>

## Punycode

**Purpose.** A bundled copy of Punycode.js. Stability 0, Deprecated.

**Notes.** It will be removed; use the userland Punycode.js package, or `url.domainToASCII()` for URLs.

[Full section](https://nodedocs.pages.dev/#punycode)

<a id="querystring"></a>

## Query strings

**Purpose.** Parses and formats URL query strings. Stability 2, Stable.

**Import.** `node:querystring`.

**Main APIs.** `querystring.parse()`, `querystring.stringify()`, `querystring.escape()`, `querystring.unescape()`.

**Notes.** It is faster than `URLSearchParams` but is not a standardized API.

[Full section](https://nodedocs.pages.dev/#querystring)

<a id="readline"></a>

## Readline

**Purpose.** Reads a stream, such as `process.stdin`, one line at a time. Stability 2, Stable.

**Import.** `node:readline`, or `node:readline/promises`.

**Main APIs.** `readline.createInterface()`, `rl.question()`, `rl.on()` with `'line'` and `'close'`, `rl.prompt()`, `rl.close()`, and async iteration over lines.

**Notes.** Async iteration is a simple way to read a file line by line.

[Full section](https://nodedocs.pages.dev/#readline)

<a id="repl"></a>

## REPL

**Purpose.** The Read-Eval-Print-Loop, standalone or embedded. Stability 2, Stable.

**Import.** `node:repl`.

**Main APIs.** `repl.start()`, `replServer.defineCommand()`, `replServer.displayPrompt()`.

**Notes.** Built-in commands include .help, .editor, .load, .save and .exit. History persists in a file by default.

[Full section](https://nodedocs.pages.dev/#repl)

<a id="report"></a>

## Report

**Purpose.** Writes a JSON diagnostic report. Stability 2, Stable.

**Key concepts.** Reports hold stacks, heap statistics, platform data and resource usage. Trigger them with `--report-on-fatalerror`, `--report-uncaught-exception`, `--report-on-signal` or `process.report.writeReport()`.

**Notes.** `--report-exclude-env` and `--report-exclude-network` leave out environment variables and network details.

[Full section](https://nodedocs.pages.dev/#report)

<a id="single-executable-applications"></a>

## Single executable applications

**Purpose.** Bundles an app into the node binary so it runs where Node.js is not installed. Stability 1.1, Active development.

**Key concepts.** Write a JSON config naming the main script, then build with `--build-sea`. Assets can be embedded.

**Main APIs.** `sea.isSea()`, `sea.getAsset()`, `sea.getAssetAsBlob()`.

**Notes.** The binary is signed on macOS, and signing is optional on Windows. By default, `require()` and `import` in the main script can only load built-in modules, so bundle your app first.

[Full section](https://nodedocs.pages.dev/#single-executable-applications)

<a id="sqlite"></a>

## SQLite

**Purpose.** Works with SQLite databases. Stability 1.2, Release candidate.

**Import.** `node:sqlite`, only with the node: prefix.

**Main APIs.** `new Database()`, `database.exec()`, `database.prepare()`, `statement.run()`, `statement.get()`, `statement.all()`, `statement.iterate()`, `database.close()`, `sqlite.backup()`.

**Notes.** All `Database` and `Statement` APIs run synchronously. `DatabaseSync` is a deprecated alias for `Database`.

[Full section](https://nodedocs.pages.dev/#sqlite)

<a id="stream"></a>

## Stream

**Purpose.** The abstract interface for streaming data. Stability 2, Stable.

**Import.** `node:stream`, plus `node:stream/promises`.

**Key concepts.** There are four types: `Writable`, `Readable`, `Duplex` (both) and `Transform` (a duplex that changes data). All streams are event emitters. Data is buffered up to `highWaterMark`. When `writable.write()` returns false, wait for `'drain'` before writing more; that is backpressure. Readable streams start paused and switch to flowing when you attach a `'data'` listener, call `readable.pipe()` or `readable.resume()`. Object mode lets streams carry any JavaScript value.

**Main APIs.**

- `stream.pipeline()` connects streams, forwards errors and cleans up
- `stream.finished()`, `stream.compose()`, `stream.addAbortSignal()`
- `readable.read()`, `readable.pipe()`, `readable.on()` with `'data'`, `'end'` and `'error'`
- `writable.write()`, `writable.end()`, events `'finish'` and `'drain'`
- `stream.Readable.from()` makes a stream from an iterable; readable streams are async iterable
- `stream.Readable.toWeb()` and `stream.Readable.fromWeb()` convert to and from Web Streams
- Implementers override `_read()`, `_write()`, `_transform()` and `_flush()`

**Notes.** Prefer `stream.pipeline()` over `readable.pipe()`, which does not forward errors or destroy streams. Use `destroy()` to release a stream early.

[Full section](https://nodedocs.pages.dev/#stream)

<a id="string_decoder"></a>

## String decoder

**Purpose.** Decodes Buffers to strings without breaking multi-byte characters. Stability 2, Stable.

**Import.** `StringDecoder` from `node:string_decoder`.

**Main APIs.** `stringDecoder.write()`, `stringDecoder.end()`.

[Full section](https://nodedocs.pages.dev/#string_decoder)

<a id="test"></a>

## Test runner

**Purpose.** Built-in test framework. Stability 2, Stable.

**Import.** `node:test`, only with the node: prefix.

**Key concepts.** Define tests with `test()`, or `describe()` and `it()`. Tests can be sync, async or callback based, and nest as subtests. `node --test` finds and runs test files. Options include skip, todo, only and timeout.

**Main APIs.** `test()`, `describe()`, `it()`, `before()`, `after()`, `beforeEach()`, `afterEach()`, `mock.fn()`, `mock.method()`, `mock.timers`, `run()`.

**Notes.** Code coverage, `--watch` mode, snapshot testing and module mocking exist; some are Experimental. Reporters include spec, tap, dot, junit and lcov.

[Full section](https://nodedocs.pages.dev/#test)

<a id="timers"></a>

## Timers

**Purpose.** Schedules functions for later. Stability 2, Stable.

**Import.** Global; promise versions from `node:timers/promises`.

**Key concepts.** Timers are built around the Node.js event loop. A delay is a minimum, not a guarantee. `setImmediate()` runs after I/O callbacks in the current loop turn. Active timers keep the process alive unless you call `unref()`.

**Main APIs.**

- `setTimeout()`, `setInterval()`, `setImmediate()`
- `clearTimeout()`, `clearInterval()`, `clearImmediate()`
- `timeout.ref()`, `timeout.unref()`, `timeout.refresh()`, `timeout.hasRef()`
- `timersPromises.setTimeout()`, `timersPromises.setInterval()` as an async iterator, `timersPromises.setImmediate()`
- `timersPromises.scheduler.wait()` and `timersPromises.scheduler.yield()`, both Experimental

**Notes.** `timeout.close()` is Legacy; use `clearTimeout()`. Promise timers accept an `AbortSignal` for cancellation. `setInterval()` repeats until cleared, and `timeout.refresh()` restarts a timer without allocating a new one.

[Full section](https://nodedocs.pages.dev/#timers)

<a id="tls"></a>

## TLS/SSL

**Purpose.** TLS and SSL built on OpenSSL. Stability 2, Stable.

**Import.** `node:tls`.

**Key concepts.** Servers need a private key and certificate. ALPN and SNI choose protocols and hostnames. Session resumption, pre-shared keys and perfect forward secrecy are explained.

**Main APIs.** `tls.createServer()`, `tls.connect()`, `tls.createSecureContext()`, `tls.TLSSocket`, `tlsSocket.getPeerCertificate()`, `tls.checkServerIdentity()`, `tls.rootCertificates`.

**Notes.** `rejectUnauthorized` controls whether peer certificates are verified against the CA list. The default cipher suite can be changed with `--tls-cipher-list`.

[Full section](https://nodedocs.pages.dev/#tls)

<a id="tracing"></a>

## Trace events

**Purpose.** Centralizes tracing from V8, Node.js and user code. Stability 1, Experimental.

**Key concepts.** Enable with `--trace-event-categories` or `trace_events.createTracing()`. Output goes to log files that open in the chrome://tracing tab of Chrome.

[Full section](https://nodedocs.pages.dev/#tracing)

<a id="tty"></a>

## TTY

**Purpose.** Terminal streams. Stability 2, Stable.

**Import.** `node:tty`; usually used through `process.stdin` and `process.stdout`.

**Main APIs.** `tty.isatty()`, `readStream.setRawMode()`, `writeStream.columns`, `writeStream.rows`, `writeStream.getColorDepth()`, `'resize'` event.

[Full section](https://nodedocs.pages.dev/#tty)

<a id="dgram"></a>

## UDP/datagram

**Purpose.** UDP datagram sockets. Stability 2, Stable.

**Import.** `node:dgram`.

**Main APIs.** `dgram.createSocket()`, `socket.bind()`, `socket.send()`, `socket.close()`, `'message'` event, `socket.addMembership()` for multicast.

**Notes.** UDP has no delivery or ordering guarantee.

[Full section](https://nodedocs.pages.dev/#dgram)

<a id="url"></a>

## URL

**Purpose.** URL parsing and resolution. Stability 2, Stable.

**Import.** `URL` and `URLSearchParams` are global; helpers come from `node:url`.

**Key concepts.** The WHATWG `URL` class follows the browser standard. Parse with `new URL()` and read parts such as `url.hostname`, `url.pathname` and `url.searchParams`.

**Main APIs.**

- `URL`, `URL.canParse()`, `URL.parse()`, `URLSearchParams`
- `url.fileURLToPath()`, `url.pathToFileURL()`, `url.domainToASCII()`, `url.urlToHttpOptions()`
- `URLPattern`, Experimental

**Notes.** The legacy API is Stability 3, Legacy, and `url.parse()`, `url.resolve()` and `url.format()` with a string are Deprecated. Use `url.fileURLToPath()` rather than reading `pathname` for file paths. The docs warn that `url.parse()` uses a lenient, non-standard algorithm that is prone to security issues such as host name spoofing. Also, `url.fileURLToPath()` decodes encoded dot segments, so validate the resulting path before using it.

[Full section](https://nodedocs.pages.dev/#url)

<a id="util"></a>

## Utilities

**Purpose.** Helpers used by Node.js internals that are also useful in apps. Stability 2, Stable.

**Import.** `node:util`.

**Key concepts.** The helpers fall into a few groups: converting between callback and promise styles, turning values into readable strings, checking types reliably, and parsing input. `util.inspect()` returns a string representation of any value for debugging; options such as `depth`, `colors` and `showHidden` control the output, and objects can define `util.inspect.custom` to format themselves.

**Main APIs.**

- `util.promisify()`, `util.callbackify()`
- `util.inspect()`, `util.format()`, `util.inspect.custom`
- `util.types` checks, such as `util.types.isPromise()`
- `util.parseArgs()` for command-line arguments, `util.parseEnv()`
- `util.styleText()` for terminal colors, `util.stripVTControlCharacters()`
- `util.deprecate()`, `util.debuglog()`, `util.isDeepStrictEqual()`, `util.aborted()`
- `util.TextEncoder`, `util.TextDecoder`, `util.MIMEType`

**Notes.** `util.inherits()` is Legacy; use class and extends. `util.promisify()` expects a function that takes an error-first callback last.

[Full section](https://nodedocs.pages.dev/#util)

<a id="v8"></a>

## V8

**Purpose.** APIs specific to the bundled V8 engine.

**Import.** `node:v8`.

**Main APIs.** `v8.getHeapStatistics()`, `v8.writeHeapSnapshot()`, `v8.getHeapSnapshot()`, `v8.setFlagsFromString()`, `v8.serialize()`, `v8.deserialize()`, `v8.startupSnapshot`, `v8.GCProfiler`.

**Notes.** Changing V8 flags at runtime can cause unpredictable behaviour. Heap snapshots pause the process and need memory about twice the heap size.

[Full section](https://nodedocs.pages.dev/#v8)

<a id="vfs"></a>

## Virtual File System

**Purpose.** An in-memory file system with an fs-like API. Stability 1, Experimental.

**Import.** `node:vfs`, with `--experimental-vfs`.

**Main APIs.** `vfs.create()`, `vfs.mount()`, `vfs.unmount()`, `MemoryProvider`, `RealFSProvider`, `ZipProvider`.

**Notes.** It is not a sandbox or security boundary.

[Full section](https://nodedocs.pages.dev/#vfs)

<a id="vm"></a>

## VM

**Purpose.** Compiles and runs code in V8 contexts. Stability 2, Stable.

**Import.** `node:vm`.

**Main APIs.** `vm.Script`, `vm.createContext()`, `vm.runInContext()`, `vm.runInNewContext()`, `vm.runInThisContext()`, `vm.compileFunction()`; `vm.SourceTextModule`, Experimental.

**Notes.** The vm module is not a security mechanism. Do not use it to run untrusted code.

[Full section](https://nodedocs.pages.dev/#vm)

<a id="wasi"></a>

## WASI

**Purpose.** The WebAssembly System Interface, giving Wasm programs POSIX-like system access. Stability 1, Experimental.

**Import.** `WASI` from `node:wasi`.

**Main APIs.** `new WASI()`, `wasi.getImportObject()`, `wasi.start()`, `wasi.initialize()`.

**Notes.** It lacks full file system sandboxing; do not run untrusted code with it.

[Full section](https://nodedocs.pages.dev/#wasi)

<a id="webcrypto"></a>

## Web Crypto API

**Purpose.** The W3C Web Cryptography standard. Stability 2, Stable.

**Import.** `globalThis.crypto`, or `webcrypto` from `node:crypto`.

**Main APIs.** `crypto.subtle` methods `subtle.generateKey()`, `subtle.importKey()`, `subtle.exportKey()`, `subtle.encrypt()`, `subtle.decrypt()`, `subtle.sign()`, `subtle.verify()`, `subtle.digest()`, `subtle.deriveKey()`; also `crypto.getRandomValues()` and `crypto.randomUUID()`.

**Notes.** All `subtle` methods return promises. Some modern algorithms are Stability 1.1, Active development.

[Full section](https://nodedocs.pages.dev/#webcrypto)

<a id="webstreams"></a>

## Web Streams API

**Purpose.** The WHATWG Streams standard. Stability 2, Stable.

**Import.** Global, or from `node:stream/web`.

**Main APIs.** `ReadableStream`, `WritableStream`, `TransformStream`, `ReadableStream.from()`, `readableStream.getReader()`, `readableStream.pipeTo()`, `readableStream.pipeThrough()`, `TextDecoderStream`, `CompressionStream`.

**Notes.** Convert to Node.js streams with `stream.Readable.fromWeb()` and back with `stream.Readable.toWeb()`.

[Full section](https://nodedocs.pages.dev/#webstreams)

<a id="worker_threads"></a>

## Worker threads

**Purpose.** Runs JavaScript in parallel threads. Stability 2, Stable.

**Import.** `node:worker_threads`.

**Key concepts.** Workers suit CPU-heavy JavaScript, not I/O, which Node.js already handles efficiently. Each `Worker` is an independent JavaScript execution thread where most Node.js APIs are available. Threads talk by message passing with structured clone, can transfer an `ArrayBuffer` or `MessagePort` to move it without copying, and can share memory through `SharedArrayBuffer`.

**Main APIs.**

- `new Worker()` with a file path and options such as `workerData` and `resourceLimits`
- `worker.postMessage()`, `worker.terminate()`, `worker.on()` with `'message'`, `'error'` and `'exit'`
- `isMainThread`, `parentPort`, `workerData`, `threadId`
- `MessageChannel`, `MessagePort`, `BroadcastChannel`, `receiveMessageOnPort()`
- `worker_threads.setEnvironmentData()`, `worker_threads.markAsUntransferable()`

**Notes.** Creating workers is costly, so use a pool for repeated tasks. `process.exit()` in a worker ends only that thread. Some newer APIs, such as `worker_threads.locks`, carry experimental labels.

[Full section](https://nodedocs.pages.dev/#worker_threads)

<a id="zlib"></a>

## Zlib

**Purpose.** Compression with Gzip, Deflate, Brotli and Zstd. Stability 2, Stable.

**Import.** `node:zlib`.

**Key concepts.** Each format has a stream class, such as `zlib.createGzip()`, plus callback, promisified and synchronous convenience functions. All APIs except the explicitly synchronous ones use the internal threadpool.

**Main APIs.** `zlib.createGzip()`, `zlib.createGunzip()`, `zlib.gzip()`, `zlib.gunzipSync()`, `zlib.createBrotliCompress()`, `zlib.brotliDecompress()`, `zlib.createDeflate()`, `zlib.crc32()`.

**Notes.** Creating many zlib objects at once can cause memory fragmentation. Zstd support is Experimental. A new ZIP archive API is Stability 1.0, Early development.

[Full section](https://nodedocs.pages.dev/#zlib)

---

Summary of the official Node.js documentation by the OpenJS Foundation and Node.js contributors. Full page: <https://nodedocs.pages.dev/>.
