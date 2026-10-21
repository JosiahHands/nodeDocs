[Contents](../README.md) | [Next: Usage and example](02-synopsis.md)

Source: [https://nodejs.org/docs/latest/api/documentation.html](https://nodejs.org/docs/latest/api/documentation.html)

# About this documentation

Welcome to the official API reference documentation for Node.js!

Node.js is a JavaScript runtime built on the [V8 JavaScript engine](https://v8.dev/).

## Contributing

Report errors in this documentation in [the issue tracker](https://github.com/nodejs/node/issues/new). See
[the contributing guide](https://github.com/nodejs/node/blob/HEAD/CONTRIBUTING.md) for directions on how to submit pull requests.

## Stability index

Throughout the documentation are indications of a section's stability. Some APIs
are so proven and so relied upon that they are unlikely to ever change at all.
Others are brand new and experimental, or known to be hazardous.

The stability indexes are as follows:

[Stability: 0](01-documentation.md#stability-index) - Deprecated. The feature may emit warnings. Backward
compatibility is not guaranteed.

Stability: 1 - Experimental. The feature is not subject to
[semantic versioning](https://semver.org/) rules. Non-backward compatible changes or removal may
occur in any future release. Use of the feature is not recommended in
production environments.

Experimental features are subdivided into stages:

- 1.0 - Early development. Experimental features at this stage are unfinished
  and subject to substantial change.
- 1.1 - Active development. Experimental features at this stage are nearing
  minimum viability.
- 1.2 - Release candidate. Experimental features at this stage are hopefully
  ready to become stable. No further breaking changes are anticipated but may
  still occur in response to user feedback or the features' underlying
  specification development. We encourage user testing and feedback so that
  we can know that this feature is ready to be marked as stable.

Experimental features leave the experimental status typically either by
graduating to stable, or are removed without a deprecation cycle.

Stability: 2 - Stable. Compatibility with the npm ecosystem is a high
priority.

Stability: 3 - Legacy. Although this feature is unlikely to be removed and is
still covered by semantic versioning guarantees, it is no longer actively
maintained, and other alternatives are available.

Features are marked as legacy rather than being deprecated if their use does no
harm, and they are widely relied upon within the npm ecosystem. Bugs found in
legacy features are unlikely to be fixed.

Use caution when making use of Experimental features, particularly when
authoring libraries. Users may not be aware that experimental features are being
used. Bugs or behavior changes may surprise users when Experimental API
modifications occur. To avoid surprises, use of an Experimental feature may need
a command-line flag. Experimental features may also emit a [warning](43-process.md#event-warning).

## Stability overview

| API | Stability |
| --- | --- |
| [Assert](03-assert.md) | (2) Stable |
| [Async hooks](05-async-hooks.md) | (1) Experimental |
| [Asynchronous context tracking](04-async-context.md) | (2) Stable |
| [Benchmark runner](06-bench.md) | (1.0) Early Development |
| [Buffer](07-buffer.md) | (2) Stable |
| [Child process](11-child-process.md) | (2) Stable |
| [Cluster](12-cluster.md) | (2) Stable |
| [Console](14-console.md) | (2) Stable |
| [Crypto](15-crypto.md) | (2) Stable |
| [Debugger](16-debugger.md) | (2) Stable |
| [Diagnostic report](48-report.md) | (2) Stable |
| [Diagnostics Channel](18-diagnostics-channel.md) | (2) Stable |
| [DNS](19-dns.md) | (2) Stable |
| [Domain](20-domain.md) | (0) Deprecated |
| [DTLS](https://nodejs.org/docs/latest/api/dtls.html) | (1.1) Active Development |
| [Events](23-events.md) | (2) Stable |
| [FFI](25-ffi.md) | (1) Experimental |
| [File system](24-fs.md) | (2) Stable |
| [Global objects](26-globals.md) | (2) Stable |
| [HTTP](27-http.md) | (2) Stable |
| [HTTP/2](28-http2.md) | (2) Stable |
| [HTTPS](29-https.md) | (2) Stable |
| [Inspector](30-inspector.md) | (2) Stable |
| [Iterable Streams](32-stream-iter.md) | (1) Experimental – Enable this API with the `--experimental-stream-iter` CLI flag. |
| [Modules: `node:module` API](35-module.md) | (2) Stable |
| [Modules: CommonJS modules](33-modules.md) | (2) Stable |
| [Modules: ECMAScript modules](34-esm.md) | (2) Stable |
| [Modules: TypeScript](37-typescript.md) | (2) Stable |
| [Net](38-net.md) | (2) Stable |
| [Node-API](09-n-api.md) | (2) Stable |
| [OS](39-os.md) | (2) Stable |
| [Path](40-path.md) | (2) Stable |
| [Performance measurement APIs](41-perf-hooks.md) | (2) Stable |
| [Punycode](44-punycode.md) | (0) Deprecated |
| [Query string](45-querystring.md) | (2) Stable |
| [Readline](46-readline.md) | (2) Stable |
| [REPL](47-repl.md) | (2) Stable |
| [Single executable applications](49-single-executable-applications.md) | (1.1) Active development |
| [SQLite](50-sqlite.md) | (1.2) Release candidate. |
| [Stream](51-stream.md) | (2) Stable |
| [String decoder](52-string-decoder.md) | (2) Stable |
| [Test runner](53-test.md) | (2) Stable |
| [Timers](54-timers.md) | (2) Stable |
| [TLS (SSL)](55-tls.md) | (2) Stable |
| [Trace events](56-tracing.md) | (1) Experimental |
| [TTY](57-tty.md) | (2) Stable |
| [UDP/datagram sockets](58-dgram.md) | (2) Stable |
| [URL](59-url.md) | (2) Stable |
| [Util](60-util.md) | (2) Stable |
| [Virtual File System](62-vfs.md) | (1) Experimental |
| [VM (executing JavaScript)](63-vm.md) | (2) Stable |
| [Web Crypto API](65-webcrypto.md) | (2) Stable |
| [Web Streams API](66-webstreams.md) | (2) Stable |
| [WebAssembly System Interface (WASI)](64-wasi.md) | (1) Experimental |
| [Worker threads](67-worker-threads.md) | (2) Stable |
| [Zlib](68-zlib.md) | (2) Stable |

## JSON output

Added in: v0.6.12

Every `.html` document has a corresponding `.json` document. This is for IDEs
and other utilities that consume the documentation.

## System calls and man pages

Node.js functions which wrap a system call will document that. The docs link
to the corresponding man pages which describe how the system call works.

Most Unix system calls have Windows analogues. Still, behavior differences may
be unavoidable.

[Contents](../README.md) | [Next: Usage and example](02-synopsis.md)
