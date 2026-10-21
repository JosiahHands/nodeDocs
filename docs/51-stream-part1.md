[Contents](../README.md) | [Previous: SQLite](50-sqlite.md) | [Next: Stream, part 2 of 4](51-stream-part2.md)

Source: [https://nodejs.org/docs/latest/api/stream.html](https://nodejs.org/docs/latest/api/stream.html)

Part 1 of 4 of this section.

# Stream

[Stability: 2](01-documentation.md#stability-index) - Stable

A stream is an abstract interface for working with streaming data in Node.js.
The `node:stream` module provides an API for implementing the stream interface.

There are many stream objects provided by Node.js. For instance, a
[request to an HTTP server](27-http-part2.md#class-httpincomingmessage) and [`process.stdout`](43-process-part3.md#processstdout)
are both stream instances.

Streams can be readable, writable, or both. All streams are instances of
[`EventEmitter`](23-events.md#class-eventemitter).

To access the `node:stream` module:

```js
const stream = require('node:stream');
```

The `node:stream` module is useful for creating new types of stream instances.
It is usually not necessary to use the `node:stream` module to consume streams.

## Organization of this document

This document contains two primary sections and a third section for notes. The
first section explains how to use existing streams within an application. The
second section explains how to create new types of streams.

## Types of streams

There are four fundamental stream types within Node.js:

- [`Writable`](#class-streamwritable): streams to which data can be written (for example,
  [`fs.createWriteStream()`](24-fs-part3.md#fscreatewritestreampath-options)).
- [`Readable`](#class-streamreadable): streams from which data can be read (for example,
  [`fs.createReadStream()`](24-fs-part3.md#fscreatereadstreampath-options)).
- [`Duplex`](51-stream-part2.md#class-streamduplex): streams that are both `Readable` and `Writable` (for example,
  [`net.Socket`](38-net.md#class-netsocket)).
- [`Transform`](51-stream-part2.md#class-streamtransform): `Duplex` streams that can modify or transform the data as it
  is written and read (for example, [`zlib.createDeflate()`](68-zlib.md#zlibcreatedeflateoptions)).

Additionally, this module includes the utility functions
[`stream.duplexPair()`](51-stream-part2.md#streamduplexpairoptions),
[`stream.pipeline()`](51-stream-part2.md#streampipelinesource-transforms-destination-callback),
[`stream.finished()`](51-stream-part2.md#streamfinishedstream-options-callback)
[`stream.Readable.from()`](51-stream-part2.md#streamreadablefromiterable-options), and
[`stream.addAbortSignal()`](51-stream-part3.md#streamaddabortsignalsignal-stream).

### Streams Promises API

Added in: v15.0.0

The `stream/promises` API provides an alternative set of asynchronous utility
functions for streams that return `Promise` objects rather than using
callbacks. The API is accessible via `require('node:stream/promises')`
or `require('node:stream').promises`.

### `stream.pipeline(streams[, options])`

### `stream.pipeline(source[, ...transforms], destination[, options])`

Added in: v15.0.0

**History**

| Version | Changes |
| --- | --- |
| v19.7.0, v18.16.0 | Added support for webstreams. |
| v18.0.0, v17.2.0, v16.14.0 | Add the `end` option, which can be set to `false` to prevent automatically closing the destination stream when the source ends. |

- `streams` {Stream[]|Iterable[]|AsyncIterable[]|Function[]|
  ReadableStream[]|WritableStream[]|TransformStream[]}
- `source` [`<Stream>`](51-stream-part1.md#stream) | [`<Iterable>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Iteration_protocols#the_iterable_protocol) | [`<AsyncIterable>`](https://tc39.github.io/ecma262/#sec-asynciterable-interface) | [`<Function>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Function) | [`<ReadableStream>`](66-webstreams.md#class-readablestream)
  - Returns: [`<Promise>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Promise) | [`<AsyncIterable>`](https://tc39.github.io/ecma262/#sec-asynciterable-interface)
- `...transforms` [`<Stream>`](51-stream-part1.md#stream) | [`<Function>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Function) | [`<TransformStream>`](66-webstreams.md#class-transformstream)
  - `source` [`<AsyncIterable>`](https://tc39.github.io/ecma262/#sec-asynciterable-interface)
  - Returns: [`<Promise>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Promise) | [`<AsyncIterable>`](https://tc39.github.io/ecma262/#sec-asynciterable-interface)
- `destination` [`<Stream>`](51-stream-part1.md#stream) | [`<Function>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Function) | [`<WritableStream>`](66-webstreams.md#class-writablestream)
  - `source` [`<AsyncIterable>`](https://tc39.github.io/ecma262/#sec-asynciterable-interface)
  - Returns: [`<Promise>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Promise) | [`<AsyncIterable>`](https://tc39.github.io/ecma262/#sec-asynciterable-interface)
- `options` [`<Object>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Object) Pipeline options
  - `signal` [`<AbortSignal>`](26-globals.md#class-abortsignal)
  - `end` [`<boolean>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#boolean_type) End the destination stream when the source stream ends.
    Transform streams are always ended, even if this value is `false`.
    **Default:** `true`.
- Returns: [`<Promise>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Promise) Fulfills when the pipeline is complete.

*CommonJS version:*

```js
const { pipeline } = require('node:stream/promises');
const fs = require('node:fs');
const zlib = require('node:zlib');

async function run() {
  await pipeline(
    fs.createReadStream('archive.tar'),
    zlib.createGzip(),
    fs.createWriteStream('archive.tar.gz'),
  );
  console.log('Pipeline succeeded.');
}

run().catch(console.error);
```

*ES module version:*

```js
import { pipeline } from 'node:stream/promises';
import { createReadStream, createWriteStream } from 'node:fs';
import { createGzip } from 'node:zlib';

await pipeline(
  createReadStream('archive.tar'),
  createGzip(),
  createWriteStream('archive.tar.gz'),
);
console.log('Pipeline succeeded.');
```

To use an `AbortSignal`, pass it inside an options object, as the last argument.
When the signal is aborted, `destroy` will be called on the underlying pipeline,
with an `AbortError`.

*CommonJS version:*

```js
const { pipeline } = require('node:stream/promises');
const fs = require('node:fs');
const zlib = require('node:zlib');

async function run() {
  const ac = new AbortController();
  const signal = ac.signal;

  setImmediate(() => ac.abort());
  await pipeline(
    fs.createReadStream('archive.tar'),
    zlib.createGzip(),
    fs.createWriteStream('archive.tar.gz'),
    { signal },
  );
}

run().catch(console.error); // AbortError
```

*ES module version:*

```js
import { pipeline } from 'node:stream/promises';
import { createReadStream, createWriteStream } from 'node:fs';
import { createGzip } from 'node:zlib';

const ac = new AbortController();
const { signal } = ac;
setImmediate(() => ac.abort());
try {
  await pipeline(
    createReadStream('archive.tar'),
    createGzip(),
    createWriteStream('archive.tar.gz'),
    { signal },
  );
} catch (err) {
  console.error(err); // AbortError
}
```

The `pipeline` API also supports async generators:

*CommonJS version:*

```js
const { pipeline } = require('node:stream/promises');
const fs = require('node:fs');

async function run() {
  await pipeline(
    fs.createReadStream('lowercase.txt'),
    async function* (source, { signal }) {
      source.setEncoding('utf8');  // Work with strings rather than `Buffer`s.
      for await (const chunk of source) {
        yield await processChunk(chunk, { signal });
      }
    },
    fs.createWriteStream('uppercase.txt'),
  );
  console.log('Pipeline succeeded.');
}

run().catch(console.error);
```

*ES module version:*

```js
import { pipeline } from 'node:stream/promises';
import { createReadStream, createWriteStream } from 'node:fs';

await pipeline(
  createReadStream('lowercase.txt'),
  async function* (source, { signal }) {
    source.setEncoding('utf8');  // Work with strings rather than `Buffer`s.
    for await (const chunk of source) {
      yield await processChunk(chunk, { signal });
    }
  },
  createWriteStream('uppercase.txt'),
);
console.log('Pipeline succeeded.');
```

Remember to handle the `signal` argument passed into the async generator.
Especially in the case where the async generator is the source for the
pipeline (i.e. first argument) or the pipeline will never complete.

*CommonJS version:*

```js
const { pipeline } = require('node:stream/promises');
const fs = require('node:fs');

async function run() {
  await pipeline(
    async function* ({ signal }) {
      await someLongRunningfn({ signal });
      yield 'asd';
    },
    fs.createWriteStream('uppercase.txt'),
  );
  console.log('Pipeline succeeded.');
}

run().catch(console.error);
```

*ES module version:*

```js
import { pipeline } from 'node:stream/promises';
import fs from 'node:fs';
await pipeline(
  async function* ({ signal }) {
    await someLongRunningfn({ signal });
    yield 'asd';
  },
  fs.createWriteStream('uppercase.txt'),
);
console.log('Pipeline succeeded.');
```

The `pipeline` API provides [callback version](51-stream-part2.md#streampipelinesource-transforms-destination-callback):

### `stream.finished(stream[, options])`

Added in: v15.0.0

**History**

| Version | Changes |
| --- | --- |
| v19.5.0, v18.14.0 | Added support for `ReadableStream` and `WritableStream`. |
| v19.1.0, v18.13.0 | The `cleanup` option was added. |

- `stream` [`<Stream>`](51-stream-part1.md#stream) | [`<ReadableStream>`](66-webstreams.md#class-readablestream) | [`<WritableStream>`](66-webstreams.md#class-writablestream) A readable and/or writable
  stream/webstream.
- `options` [`<Object>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Object)
  - `error` [`<boolean>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#boolean_type) | [`<undefined>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#undefined_type)
  - `readable` [`<boolean>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#boolean_type) | [`<undefined>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#undefined_type)
  - `writable` [`<boolean>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#boolean_type) | [`<undefined>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#undefined_type)
  - `signal` [`<AbortSignal>`](26-globals.md#class-abortsignal) | [`<undefined>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#undefined_type)
  - `cleanup` [`<boolean>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#boolean_type) | [`<undefined>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#undefined_type) If `true`, removes the listeners registered by
    this function before the promise is fulfilled. **Default:** `false`.
- Returns: [`<Promise>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Promise) Fulfills when the stream is no
  longer readable or writable.

*CommonJS version:*

```js
const { finished } = require('node:stream/promises');
const fs = require('node:fs');

const rs = fs.createReadStream('archive.tar');

async function run() {
  await finished(rs);
  console.log('Stream is done reading.');
}

run().catch(console.error);
rs.resume(); // Drain the stream.
```

*ES module version:*

```js
import { finished } from 'node:stream/promises';
import { createReadStream } from 'node:fs';

const rs = createReadStream('archive.tar');

async function run() {
  await finished(rs);
  console.log('Stream is done reading.');
}

run().catch(console.error);
rs.resume(); // Drain the stream.
```

The `finished` API also provides a [callback version](51-stream-part2.md#streamfinishedstream-options-callback).

`stream.finished()` leaves dangling event listeners (in particular
`'error'`, `'end'`, `'finish'` and `'close'`) after the returned promise is
resolved or rejected. The reason for this is so that unexpected `'error'`
events (due to incorrect stream implementations) do not cause unexpected
crashes. If this is unwanted behavior then `options.cleanup` should be set to
`true`:

```js
await finished(rs, { cleanup: true });
```

### Object mode

All streams created by Node.js APIs operate exclusively on strings, [`<Buffer>`](07-buffer-part1.md#class-buffer),
[`<TypedArray>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/TypedArray) and [`<DataView>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/DataView) objects:

- `Strings` and `Buffers` are the most common types used with streams.
- `TypedArray` and `DataView` lets you handle binary data with types like
  `Int32Array` or `Uint8Array`. When you write a TypedArray or DataView to a
  stream, Node.js processes
  the raw bytes.

It is possible, however, for stream
implementations to work with other types of JavaScript values (with the
exception of `null`, which serves a special purpose within streams).
Such streams are considered to operate in "object mode".

Stream instances are switched into object mode using the `objectMode` option
when the stream is created. Attempting to switch an existing stream into
object mode is not safe.

### Buffering

Both [`Writable`](#class-streamwritable) and [`Readable`](#class-streamreadable) streams will store data in an internal
buffer.

The amount of data potentially buffered depends on the `highWaterMark` option
passed into the stream's constructor. For normal streams, the `highWaterMark`
option specifies a [total number of bytes](51-stream-part4.md#highwatermark-discrepancy-after-calling-readablesetencoding). For streams operating
in object mode, the `highWaterMark` specifies a total number of objects. For
streams operating on (but not decoding) strings, the `highWaterMark` specifies
a total number of UTF-16 code units.

Data is buffered in `Readable` streams when the implementation calls
[`stream.push(chunk)`](51-stream-part3.md#readablepushchunk-encoding). If the consumer of the Stream does not
call [`stream.read()`](#readablereadsize), the data will sit in the internal
queue until it is consumed.

Once the total size of the internal read buffer reaches the threshold specified
by `highWaterMark`, the stream will temporarily stop reading data from the
underlying resource until the data currently buffered can be consumed (that is,
the stream will stop calling the internal [`readable._read()`](51-stream-part3.md#readable_readsize) method that is
used to fill the read buffer).

Data is buffered in `Writable` streams when the
[`writable.write(chunk)`](#writablewritechunk-encoding-callback) method is called repeatedly. While the
total size of the internal write buffer is below the threshold set by
`highWaterMark`, calls to `writable.write()` will return `true`. Once
the size of the internal buffer reaches or exceeds the `highWaterMark`, `false`
will be returned.

A key goal of the `stream` API, particularly the [`stream.pipe()`](#readablepipedestination-options) method,
is to limit the buffering of data to acceptable levels such that sources and
destinations of differing speeds will not overwhelm the available memory.

The `highWaterMark` option is a threshold, not a limit: it dictates the amount
of data that a stream buffers before it stops asking for more data. It does not
enforce a strict memory limitation in general. Specific stream implementations
may choose to enforce stricter limits but doing so is optional.

Because [`Duplex`](51-stream-part2.md#class-streamduplex) and [`Transform`](51-stream-part2.md#class-streamtransform) streams are both `Readable` and
`Writable`, each maintains *two* separate internal buffers used for reading and
writing, allowing each side to operate independently of the other while
maintaining an appropriate and efficient flow of data. For example,
[`net.Socket`](38-net.md#class-netsocket) instances are [`Duplex`](51-stream-part2.md#class-streamduplex) streams whose `Readable` side allows
consumption of data received *from* the socket and whose `Writable` side allows
writing data *to* the socket. Because data may be written to the socket at a
faster or slower rate than data is received, each side should
operate (and buffer) independently of the other.

The mechanics of the internal buffering are an internal implementation detail
and may be changed at any time. However, for certain advanced implementations,
the internal buffers can be retrieved using `writable.writableBuffer` or
`readable.readableBuffer`. Use of these undocumented properties is discouraged.

## API for stream consumers

Almost all Node.js applications, no matter how simple, use streams in some
manner. The following is an example of using streams in a Node.js application
that implements an HTTP server:

```js
const http = require('node:http');

const server = http.createServer((req, res) => {
  // `req` is an http.IncomingMessage, which is a readable stream.
  // `res` is an http.ServerResponse, which is a writable stream.

  let body = '';
  // Get the data as utf8 strings.
  // If an encoding is not set, Buffer objects will be received.
  req.setEncoding('utf8');

  // Readable streams emit 'data' events once a listener is added.
  req.on('data', (chunk) => {
    body += chunk;
  });

  // The 'end' event indicates that the entire body has been received.
  req.on('end', () => {
    try {
      const data = JSON.parse(body);
      // Write back something interesting to the user:
      res.write(typeof data);
      res.end();
    } catch (er) {
      // uh oh! bad json!
      res.statusCode = 400;
      return res.end(`error: ${er.message}`);
    }
  });
});

server.listen(1337);

// $ curl localhost:1337 -d "{}"
// object
// $ curl localhost:1337 -d "\"foo\""
// string
// $ curl localhost:1337 -d "not json"
// error: Unexpected token 'o', "not json" is not valid JSON
```

[`Writable`](#class-streamwritable) streams (such as `res` in the example) expose methods such as
`write()` and `end()` that are used to write data onto the stream.

[`Readable`](#class-streamreadable) streams use the [`EventEmitter`](23-events.md#class-eventemitter) API for notifying application
code when data is available to be read off the stream. That available data can
be read from the stream in multiple ways.

Both [`Writable`](#class-streamwritable) and [`Readable`](#class-streamreadable) streams use the [`EventEmitter`](23-events.md#class-eventemitter) API in
various ways to communicate the current state of the stream.

[`Duplex`](51-stream-part2.md#class-streamduplex) and [`Transform`](51-stream-part2.md#class-streamtransform) streams are both [`Writable`](#class-streamwritable) and
[`Readable`](#class-streamreadable).

Applications that are either writing data to or consuming data from a stream
are not required to implement the stream interfaces directly and will generally
have no reason to call `require('node:stream')`.

Developers wishing to implement new types of streams should refer to the
section [API for stream implementers](51-stream-part3.md#api-for-stream-implementers).

### Writable streams

Writable streams are an abstraction for a *destination* to which data is
written.

Examples of [`Writable`](#class-streamwritable) streams include:

- [HTTP requests, on the client](27-http-part1.md#class-httpclientrequest)
- [HTTP responses, on the server](27-http-part2.md#class-httpserverresponse)
- [fs write streams](24-fs-part7.md#class-fswritestream)
- [zlib streams](68-zlib.md)
- [crypto streams](15-crypto-part1.md)
- [TCP sockets](38-net.md#class-netsocket)
- [child process stdin](11-child-process.md#subprocessstdin)
- [`process.stdout`](43-process-part3.md#processstdout), [`process.stderr`](43-process-part3.md#processstderr)

Some of these examples are actually [`Duplex`](51-stream-part2.md#class-streamduplex) streams that implement the
[`Writable`](#class-streamwritable) interface.

All [`Writable`](#class-streamwritable) streams implement the interface defined by the
`stream.Writable` class.

While specific instances of [`Writable`](#class-streamwritable) streams may differ in various ways,
all `Writable` streams follow the same fundamental usage pattern as illustrated
in the example below:

```js
const myStream = getWritableStreamSomehow();
myStream.write('some data');
myStream.write('some more data');
myStream.end('done writing data');
```

#### Class: `stream.Writable`

Added in: v0.9.4

##### Event: `'close'`

Added in: v0.9.4

**History**

| Version | Changes |
| --- | --- |
| v10.0.0 | Add `emitClose` option to specify if `'close'` is emitted on destroy. |

The `'close'` event is emitted when the stream and any of its underlying
resources (a file descriptor, for example) have been closed. The event indicates
that no more events will be emitted, and no further computation will occur.

A [`Writable`](#class-streamwritable) stream will always emit the `'close'` event if it is
created with the `emitClose` option.

##### Event: `'drain'`

Added in: v0.9.4

If a call to [`stream.write(chunk)`](#writablewritechunk-encoding-callback) returns `false`, the
`'drain'` event will be emitted when it is appropriate to resume writing data
to the stream.

```js
// Write the data to the supplied writable stream one million times.
// Be attentive to back-pressure.
function writeOneMillionTimes(writer, data, encoding, callback) {
  let i = 1000000;
  write();
  function write() {
    let ok = true;
    do {
      i--;
      if (i === 0) {
        // Last time!
        writer.write(data, encoding, callback);
      } else {
        // See if we should continue, or wait.
        // Don't pass the callback, because we're not done yet.
        ok = writer.write(data, encoding);
      }
    } while (i > 0 && ok);
    if (i > 0) {
      // Had to stop early!
      // Write some more once it drains.
      writer.once('drain', write);
    }
  }
}
```

##### Event: `'error'`

Added in: v0.9.4

- Type: [`<Error>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Error)

The `'error'` event is emitted if an error occurred while writing or piping
data. The listener callback is passed a single `Error` argument when called.

The stream is closed when the `'error'` event is emitted unless the
[`autoDestroy`](51-stream-part3.md#new-streamwritableoptions) option was set to `false` when creating the
stream.

After `'error'`, no further events other than `'close'` *should* be emitted
(including `'error'` events).

##### Event: `'finish'`

Added in: v0.9.4

The `'finish'` event is emitted after the [`stream.end()`](#writableendchunk-encoding-callback) method
has been called, and all data has been flushed to the underlying system.

```js
const writer = getWritableStreamSomehow();
for (let i = 0; i < 100; i++) {
  writer.write(`hello, #${i}!\n`);
}
writer.on('finish', () => {
  console.log('All writes are now complete.');
});
writer.end('This is the end\n');
```

##### Event: `'pipe'`

Added in: v0.9.4

- `src` [`<stream.Readable>`](51-stream-part1.md#class-streamreadable) source stream that is piping to this writable

The `'pipe'` event is emitted when the [`stream.pipe()`](#readablepipedestination-options) method is called on
a readable stream, adding this writable to its set of destinations.

```js
const writer = getWritableStreamSomehow();
const reader = getReadableStreamSomehow();
writer.on('pipe', (src) => {
  console.log('Something is piping into the writer.');
  assert.equal(src, reader);
});
reader.pipe(writer);
```

##### Event: `'unpipe'`

Added in: v0.9.4

- `src` [`<stream.Readable>`](51-stream-part1.md#class-streamreadable) The source stream that [unpiped](51-stream-part2.md#readableunpipedestination) this writable

The `'unpipe'` event is emitted when the [`stream.unpipe()`](51-stream-part2.md#readableunpipedestination) method is called
on a [`Readable`](#class-streamreadable) stream, removing this [`Writable`](#class-streamwritable) from its set of
destinations.

This is also emitted in case this [`Writable`](#class-streamwritable) stream emits an error when a
[`Readable`](#class-streamreadable) stream pipes into it.

```js
const writer = getWritableStreamSomehow();
const reader = getReadableStreamSomehow();
writer.on('unpipe', (src) => {
  console.log('Something has stopped piping into the writer.');
  assert.equal(src, reader);
});
reader.pipe(writer);
reader.unpipe(writer);
```

##### `writable.cork()`

Added in: v0.11.2

The `writable.cork()` method forces all written data to be buffered in memory.
The buffered data will be flushed when either the [`stream.uncork()`](#writableuncork) or
[`stream.end()`](#writableendchunk-encoding-callback) methods are called.

The primary intent of `writable.cork()` is to accommodate a situation in which
several small chunks are written to the stream in rapid succession. Instead of
immediately forwarding them to the underlying destination, `writable.cork()`
buffers all the chunks until `writable.uncork()` is called, which will pass them
all to `writable._writev()`, if present. This prevents a head-of-line blocking
situation where data is being buffered while waiting for the first small chunk
to be processed. However, use of `writable.cork()` without implementing
`writable._writev()` may have an adverse effect on throughput.

See also: [`writable.uncork()`](#writableuncork), [`writable._writev()`](51-stream-part3.md#writable_writevchunks-callback).

##### `writable.destroy([error])`

Added in: v8.0.0

**History**

| Version | Changes |
| --- | --- |
| v14.0.0 | Work as a no-op on a stream that has already been destroyed. |

- `error` [`<Error>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Error) Optional, an error to emit with `'error'` event.
- Returns: [`<this>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Operators/this)

Destroy the stream. Optionally emit an `'error'` event, and emit a `'close'`
event (unless `emitClose` is set to `false`). After this call, the writable
stream has ended and subsequent calls to `write()` or `end()` will result in
an `ERR_STREAM_DESTROYED` error.
This is a destructive and immediate way to destroy a stream. Previous calls to
`write()` may not have drained, and may trigger an `ERR_STREAM_DESTROYED` error.
Use `end()` instead of destroy if data should flush before close, or wait for
the `'drain'` event before destroying the stream.

```js
const { Writable } = require('node:stream');

const myStream = new Writable();

const fooErr = new Error('foo error');
myStream.destroy(fooErr);
myStream.on('error', (fooErr) => console.error(fooErr.message)); // foo error
```

```js
const { Writable } = require('node:stream');

const myStream = new Writable();

myStream.destroy();
myStream.on('error', function wontHappen() {});
```

```js
const { Writable } = require('node:stream');

const myStream = new Writable();
myStream.destroy();

myStream.write('foo', (error) => console.error(error.code));
// ERR_STREAM_DESTROYED
```

Once `destroy()` has been called any further calls will be a no-op and no
further errors except from `_destroy()` may be emitted as `'error'`.

Implementors should not override this method,
but instead implement [`writable._destroy()`](51-stream-part3.md#writable_destroyerr-callback).

##### `writable.closed`

Added in: v18.0.0

- Type: [`<boolean>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#boolean_type)

Is `true` after `'close'` has been emitted.

##### `writable.destroyed`

Added in: v8.0.0

- Type: [`<boolean>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#boolean_type)

Is `true` after [`writable.destroy()`](#writabledestroyerror) has been called.

```js
const { Writable } = require('node:stream');

const myStream = new Writable();

console.log(myStream.destroyed); // false
myStream.destroy();
console.log(myStream.destroyed); // true
```

##### `writable.end([chunk[, encoding]][, callback])`

Added in: v0.9.4

**History**

| Version | Changes |
| --- | --- |
| v22.0.0, v20.13.0 | The `chunk` argument can now be a `TypedArray` or `DataView` instance. |
| v15.0.0 | The `callback` is invoked before 'finish' or on error. |
| v14.0.0 | The `callback` is invoked if 'finish' or 'error' is emitted. |
| v10.0.0 | This method now returns a reference to `writable`. |
| v8.0.0 | The `chunk` argument can now be a `Uint8Array` instance. |

- `chunk` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) | [`<Buffer>`](07-buffer-part1.md#class-buffer) | [`<TypedArray>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/TypedArray) | [`<DataView>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/DataView) | [`<any>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#Data_types) Optional data to write. For
  streams not operating in object mode, `chunk` must be a [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type), [`<Buffer>`](07-buffer-part1.md#class-buffer),
  [`<TypedArray>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/TypedArray) or [`<DataView>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/DataView). For object mode streams, `chunk` may be any
  JavaScript value other than `null`.
- `encoding` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) The encoding if `chunk` is a string
- `callback` [`<Function>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Function) Callback for when the stream is finished.
- Returns: [`<this>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Operators/this)

Calling the `writable.end()` method signals that no more data will be written
to the [`Writable`](#class-streamwritable). The optional `chunk` and `encoding` arguments allow one
final additional chunk of data to be written immediately before closing the
stream.

Calling the [`stream.write()`](#writablewritechunk-encoding-callback) method after calling
[`stream.end()`](#writableendchunk-encoding-callback) will raise an error.

```js
// Write 'hello, ' and then end with 'world!'.
const fs = require('node:fs');
const file = fs.createWriteStream('example.txt');
file.write('hello, ');
file.end('world!');
// Writing more now is not allowed!
```

##### `writable.setDefaultEncoding(encoding)`

Added in: v0.11.15

**History**

| Version | Changes |
| --- | --- |
| v6.1.0 | This method now returns a reference to `writable`. |

- `encoding` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) The new default encoding
- Returns: [`<this>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Operators/this)

The `writable.setDefaultEncoding()` method sets the default `encoding` for a
[`Writable`](#class-streamwritable) stream.

##### `writable.uncork()`

Added in: v0.11.2

The `writable.uncork()` method flushes all data buffered since
[`stream.cork()`](#writablecork) was called.

When using [`writable.cork()`](#writablecork) and `writable.uncork()` to manage the buffering
of writes to a stream, defer calls to `writable.uncork()` using
`process.nextTick()`. Doing so allows batching of all
`writable.write()` calls that occur within a given Node.js event loop phase.

```js
stream.cork();
stream.write('some ');
stream.write('data ');
process.nextTick(() => stream.uncork());
```

If the [`writable.cork()`](#writablecork) method is called multiple times on a stream, the
same number of calls to `writable.uncork()` must be called to flush the buffered
data.

```js
stream.cork();
stream.write('some ');
stream.cork();
stream.write('data ');
process.nextTick(() => {
  stream.uncork();
  // The data will not be flushed until uncork() is called a second time.
  stream.uncork();
});
```

See also: [`writable.cork()`](#writablecork).

##### `writable.writable`

Added in: v11.4.0

- Type: [`<boolean>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#boolean_type)

Is `true` if it is safe to call [`writable.write()`](#writablewritechunk-encoding-callback), which means
the stream has not been destroyed, errored, or ended.

##### `writable.writableAborted`

Added in: v18.0.0, v16.17.0

**History**

| Version | Changes |
| --- | --- |
| v24.0.0, v22.17.0 | Marking the API stable. |

- Type: [`<boolean>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#boolean_type)

Returns whether the stream was destroyed or errored before emitting `'finish'`.

##### `writable.writableEnded`

Added in: v12.9.0

- Type: [`<boolean>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#boolean_type)

Is `true` after [`writable.end()`](#writableendchunk-encoding-callback) has been called. This property
does not indicate whether the data has been flushed, for this use
[`writable.writableFinished`](#writablewritablefinished) instead.

##### `writable.writableCorked`

Added in: v13.2.0, v12.16.0

- Type: [`<integer>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type)

Number of times [`writable.uncork()`](#writableuncork) needs to be
called in order to fully uncork the stream.

##### `writable.errored`

Added in: v18.0.0

- Type: [`<Error>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Error)

Returns error if the stream has been destroyed with an error.

##### `writable.writableFinished`

Added in: v12.6.0

- Type: [`<boolean>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#boolean_type)

Is set to `true` immediately before the [`'finish'`](#event-finish) event is emitted.

##### `writable.writableHighWaterMark`

Added in: v9.3.0

- Type: [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type)

Return the value of `highWaterMark` passed when creating this `Writable`.

##### `writable.writableLength`

Added in: v9.4.0

- Type: [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type)

This property contains the number of bytes (or objects) in the queue
ready to be written. The value provides introspection data regarding
the status of the `highWaterMark`.

##### `writable.writableNeedDrain`

Added in: v15.2.0, v14.17.0

- Type: [`<boolean>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#boolean_type)

Is `true` if the stream's buffer has been full and stream will emit `'drain'`.

##### `writable.writableObjectMode`

Added in: v12.3.0

- Type: [`<boolean>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#boolean_type)

Getter for the property `objectMode` of a given `Writable` stream.

##### `writable[Symbol.asyncDispose]()`

Added in: v22.4.0, v20.16.0

**History**

| Version | Changes |
| --- | --- |
| v24.2.0 | No longer experimental. |

Calls [`writable.destroy()`](#writabledestroyerror) with an `AbortError` and returns
a promise that fulfills when the stream is finished.

##### `writable.write(chunk[, encoding][, callback])`

Added in: v0.9.4

**History**

| Version | Changes |
| --- | --- |
| v22.0.0, v20.13.0 | The `chunk` argument can now be a `TypedArray` or `DataView` instance. |
| v8.0.0 | The `chunk` argument can now be a `Uint8Array` instance. |
| v6.0.0 | Passing `null` as the `chunk` parameter will always be considered invalid now, even in object mode. |

- `chunk` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) | [`<Buffer>`](07-buffer-part1.md#class-buffer) | [`<TypedArray>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/TypedArray) | [`<DataView>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/DataView) | [`<any>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#Data_types) Optional data to write. For
  streams not operating in object mode, `chunk` must be a [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type), [`<Buffer>`](07-buffer-part1.md#class-buffer),
  [`<TypedArray>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/TypedArray) or [`<DataView>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/DataView). For object mode streams, `chunk` may be any
  JavaScript value other than `null`.
- `encoding` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) | [`<null>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#null_type) The encoding, if `chunk` is a string. **Default:** `'utf8'`
- `callback` [`<Function>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Function) Callback for when this chunk of data is flushed.
- Returns: [`<boolean>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#boolean_type) `false` if the stream wishes for the calling code to
  wait for the `'drain'` event to be emitted before continuing to write
  additional data; otherwise `true`.

The `writable.write()` method writes some data to the stream, and calls the
supplied `callback` once the data has been fully handled. If an error
occurs, the `callback` will be called with the error as its
first argument. The `callback` is called asynchronously and before `'error'` is
emitted.

The return value is `true` if the internal buffer is less than the
`highWaterMark` configured when the stream was created after admitting `chunk`.
If `false` is returned, further attempts to write data to the stream should
stop until the [`'drain'`](#event-drain) event is emitted.

While a stream is not draining, calls to `write()` will buffer `chunk`, and
return false. Once all currently buffered chunks are drained (accepted for
delivery by the operating system), the `'drain'` event will be emitted.
Once `write()` returns false, do not write more chunks
until the `'drain'` event is emitted. While calling `write()` on a stream that
is not draining is allowed, Node.js will buffer all written chunks until
maximum memory usage occurs, at which point it will abort unconditionally.
Even before it aborts, high memory usage will cause poor garbage collector
performance and high RSS (which is not typically released back to the system,
even after the memory is no longer required). Since TCP sockets may never
drain if the remote peer does not read the data, writing a socket that is
not draining may lead to a remotely exploitable vulnerability.

Writing data while the stream is not draining is particularly
problematic for a [`Transform`](51-stream-part2.md#class-streamtransform), because the `Transform` streams are paused
by default until they are piped or a `'data'` or `'readable'` event handler
is added.

If the data to be written can be generated or fetched on demand, it is
recommended to encapsulate the logic into a [`Readable`](#class-streamreadable) and use
[`stream.pipe()`](#readablepipedestination-options). However, if calling `write()` is preferred, it is
possible to respect backpressure and avoid memory issues using the
[`'drain'`](#event-drain) event:

```js
function write(data, cb) {
  if (!stream.write(data)) {
    stream.once('drain', cb);
  } else {
    process.nextTick(cb);
  }
}

// Wait for cb to be called before doing any other write.
write('hello', () => {
  console.log('Write completed, do more writes now.');
});
```

A `Writable` stream in object mode will always ignore the `encoding` argument.

### Readable streams

Readable streams are an abstraction for a *source* from which data is
consumed.

Examples of `Readable` streams include:

- [HTTP responses, on the client](27-http-part2.md#class-httpincomingmessage)
- [HTTP requests, on the server](27-http-part2.md#class-httpincomingmessage)
- [fs read streams](24-fs-part6.md#class-fsreadstream)
- [zlib streams](68-zlib.md)
- [crypto streams](15-crypto-part1.md)
- [TCP sockets](38-net.md#class-netsocket)
- [child process stdout and stderr](11-child-process.md#subprocessstdout)
- [`process.stdin`](43-process-part3.md#processstdin)

All [`Readable`](#class-streamreadable) streams implement the interface defined by the
`stream.Readable` class.

#### Two reading modes

`Readable` streams effectively operate in one of two modes: flowing and
paused. These modes are separate from [object mode](#object-mode).
A [`Readable`](#class-streamreadable) stream can be in object mode or not, regardless of whether
it is in flowing mode or paused mode.

- In flowing mode, data is read from the underlying system automatically
  and provided to an application as quickly as possible using events via the
  [`EventEmitter`](23-events.md#class-eventemitter) interface.
- In paused mode, the [`stream.read()`](#readablereadsize) method must be called
  explicitly to read chunks of data from the stream.

All [`Readable`](#class-streamreadable) streams begin in paused mode but can be switched to flowing
mode in one of the following ways:

- Adding a [`'data'`](#event-data) event handler.
- Calling the [`stream.resume()`](51-stream-part2.md#readableresume) method.
- Calling the [`stream.pipe()`](#readablepipedestination-options) method to send the data to a [`Writable`](#class-streamwritable).

The `Readable` can switch back to paused mode using one of the following:

- If there are no pipe destinations, by calling the
  [`stream.pause()`](#readablepause) method.
- If there are pipe destinations, by removing all pipe destinations.
  Multiple pipe destinations may be removed by calling the
  [`stream.unpipe()`](51-stream-part2.md#readableunpipedestination) method.

The important concept to remember is that a `Readable` will not generate data
until a mechanism for either consuming or ignoring that data is provided. If
the consuming mechanism is disabled or taken away, the `Readable` will *attempt*
to stop generating the data.

For backward compatibility reasons, removing [`'data'`](#event-data) event handlers will
**not** automatically pause the stream. Also, if there are piped destinations,
then calling [`stream.pause()`](#readablepause) will not guarantee that the
stream will *remain* paused once those destinations drain and ask for more data.

If a [`Readable`](#class-streamreadable) is switched into flowing mode and there are no consumers
available to handle the data, that data will be lost. This can occur, for
instance, when the `readable.resume()` method is called without a listener
attached to the `'data'` event, or when a `'data'` event handler is removed
from the stream.

Adding a [`'readable'`](#event-readable) event handler automatically makes the stream
stop flowing, and the data has to be consumed via
[`readable.read()`](#readablereadsize). If the [`'readable'`](#event-readable) event handler is
removed, then the stream will start flowing again if there is a
[`'data'`](#event-data) event handler.

#### Three states

The "two modes" of operation for a `Readable` stream are a simplified
abstraction for the more complicated internal state management that is happening
within the `Readable` stream implementation.

Specifically, at any given point in time, every `Readable` is in one of three
possible states:

- `readable.readableFlowing === null`
- `readable.readableFlowing === false`
- `readable.readableFlowing === true`

When `readable.readableFlowing` is `null`, no mechanism for consuming the
stream's data is provided. Therefore, the stream will not generate data.
While in this state, attaching a listener for the `'data'` event, calling the
`readable.pipe()` method, or calling the `readable.resume()` method will switch
`readable.readableFlowing` to `true`, causing the `Readable` to begin actively
emitting events as data is generated.

Calling `readable.pause()`, `readable.unpipe()`, or receiving backpressure
will cause the `readable.readableFlowing` to be set as `false`,
temporarily halting the flowing of events but *not* halting the generation of
data. While in this state, attaching a listener for the `'data'` event
will not switch `readable.readableFlowing` to `true`.

```js
const { PassThrough, Writable } = require('node:stream');
const pass = new PassThrough();
const writable = new Writable();

pass.pipe(writable);
pass.unpipe(writable);
// readableFlowing is now false.

pass.on('data', (chunk) => { console.log(chunk.toString()); });
// readableFlowing is still false.
pass.write('ok');  // Will not emit 'data'.
pass.resume();     // Must be called to make stream emit 'data'.
// readableFlowing is now true.
```

While `readable.readableFlowing` is `false`, data may be accumulating
within the stream's internal buffer.

#### Choose one API style

The `Readable` stream API evolved across multiple Node.js versions and provides
multiple methods of consuming stream data. In general, developers should choose
*one* of the methods of consuming data and *should never* use multiple methods
to consume data from a single stream. Specifically, using a combination
of `on('data')`, `on('readable')`, `pipe()`, or async iterators could
lead to unintuitive behavior.

#### Class: `stream.Readable`

Added in: v0.9.4

##### Event: `'close'`

Added in: v0.9.4

**History**

| Version | Changes |
| --- | --- |
| v10.0.0 | Add `emitClose` option to specify if `'close'` is emitted on destroy. |

The `'close'` event is emitted when the stream and any of its underlying
resources (a file descriptor, for example) have been closed. The event indicates
that no more events will be emitted, and no further computation will occur.

A [`Readable`](#class-streamreadable) stream will always emit the `'close'` event if it is
created with the `emitClose` option.

##### Event: `'data'`

Added in: v0.9.4

- `chunk` [`<Buffer>`](07-buffer-part1.md#class-buffer) | [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) | [`<any>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#Data_types) The chunk of data. For streams that are not
  operating in object mode, the chunk will be either a string or `Buffer`.
  For streams that are in object mode, the chunk can be any JavaScript value
  other than `null`.

The `'data'` event is emitted whenever the stream is relinquishing ownership of
a chunk of data to a consumer. This may occur whenever the stream is switched
in flowing mode by calling `readable.pipe()`, `readable.resume()`, or by
attaching a listener callback to the `'data'` event. The `'data'` event will
also be emitted whenever the `readable.read()` method is called and a chunk of
data is available to be returned.

Attaching a `'data'` event listener to a stream that has not been explicitly
paused will switch the stream into flowing mode. Data will then be passed as
soon as it is available.

The listener callback will be passed the chunk of data as a string if a default
encoding has been specified for the stream using the
`readable.setEncoding()` method; otherwise the data will be passed as a
`Buffer`.

```js
const readable = getReadableStreamSomehow();
readable.on('data', (chunk) => {
  console.log(`Received ${chunk.length} bytes of data.`);
});
```

##### Event: `'end'`

Added in: v0.9.4

The `'end'` event is emitted when there is no more data to be consumed from
the stream.

The `'end'` event **will not be emitted** unless the data is completely
consumed. This can be accomplished by switching the stream into flowing mode,
or by calling [`stream.read()`](#readablereadsize) repeatedly until all data has been
consumed.

```js
const readable = getReadableStreamSomehow();
readable.on('data', (chunk) => {
  console.log(`Received ${chunk.length} bytes of data.`);
});
readable.on('end', () => {
  console.log('There will be no more data.');
});
```

##### Event: `'error'`

Added in: v0.9.4

- Type: [`<Error>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Error)

The `'error'` event may be emitted by a `Readable` implementation at any time.
Typically, this may occur if the underlying stream is unable to generate data
due to an underlying internal failure, or when a stream implementation attempts
to push an invalid chunk of data.

The listener callback will be passed a single `Error` object.

##### Event: `'pause'`

Added in: v0.9.4

The `'pause'` event is emitted when [`stream.pause()`](#readablepause) is called
and `readableFlowing` is not `false`.

##### Event: `'readable'`

Added in: v0.9.4

**History**

| Version | Changes |
| --- | --- |
| v10.0.0 | The `'readable'` is always emitted in the next tick after `.push()` is called. |
| v10.0.0 | Using `'readable'` requires calling `.read()`. |

The `'readable'` event is emitted when there is data available to be read from
the stream, up to the configured high water mark (`state.highWaterMark`). Effectively,
it indicates that the stream has new information within the buffer. If data is available
within this buffer, [`stream.read()`](#readablereadsize) can be called to retrieve that data.
Additionally, the `'readable'` event may also be emitted when the end of the stream has been
reached.

```js
const readable = getReadableStreamSomehow();
readable.on('readable', function() {
  // There is some data to read now.
  let data;

  while ((data = this.read()) !== null) {
    console.log(data);
  }
});
```

If the end of the stream has been reached, calling
[`stream.read()`](#readablereadsize) will return `null` and trigger the `'end'`
event. This is also true if there never was any data to be read. For instance,
in the following example, `foo.txt` is an empty file:

```js
const fs = require('node:fs');
const rr = fs.createReadStream('foo.txt');
rr.on('readable', () => {
  console.log(`readable: ${rr.read()}`);
});
rr.on('end', () => {
  console.log('end');
});
```

The output of running this script is:

```console
$ node test.js
readable: null
end
```

In some cases, attaching a listener for the `'readable'` event will cause some
amount of data to be read into an internal buffer.

In general, the `readable.pipe()` and `'data'` event mechanisms are easier to
understand than the `'readable'` event. However, handling `'readable'` might
result in increased throughput.

If both `'readable'` and [`'data'`](#event-data) are used at the same time, `'readable'`
takes precedence in controlling the flow, i.e. `'data'` will be emitted
only when [`stream.read()`](#readablereadsize) is called. The
`readableFlowing` property would become `false`.
If there are `'data'` listeners when `'readable'` is removed, the stream
will start flowing, i.e. `'data'` events will be emitted without calling
`.resume()`.

##### Event: `'resume'`

Added in: v0.9.4

The `'resume'` event is emitted when [`stream.resume()`](51-stream-part2.md#readableresume) is
called and `readableFlowing` is not `true`.

##### `readable.destroy([error])`

Added in: v8.0.0

**History**

| Version | Changes |
| --- | --- |
| v14.0.0 | Work as a no-op on a stream that has already been destroyed. |

- `error` [`<Error>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Error) Error which will be passed as payload in `'error'` event
- Returns: [`<this>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Operators/this)

Destroy the stream. Optionally emit an `'error'` event, and emit a `'close'`
event (unless `emitClose` is set to `false`). After this call, the readable
stream will release any internal resources and subsequent calls to `push()`
will be ignored.

Once `destroy()` has been called any further calls will be a no-op and no
further errors except from `_destroy()` may be emitted as `'error'`.

Implementors should not override this method, but instead implement
[`readable._destroy()`](51-stream-part3.md#readable_destroyerr-callback).

##### `readable.closed`

Added in: v18.0.0

- Type: [`<boolean>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#boolean_type)

Is `true` after `'close'` has been emitted.

##### `readable.destroyed`

Added in: v8.0.0

- Type: [`<boolean>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#boolean_type)

Is `true` after [`readable.destroy()`](#readabledestroyerror) has been called.

##### `readable.isPaused()`

Added in: v0.11.14

- Returns: [`<boolean>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#boolean_type)

The `readable.isPaused()` method returns the current operating state of the
`Readable`. This is used primarily by the mechanism that underlies the
`readable.pipe()` method. In most typical cases, there will be no reason to
use this method directly.

```js
const readable = new stream.Readable();

readable.isPaused(); // === false
readable.pause();
readable.isPaused(); // === true
readable.resume();
readable.isPaused(); // === false
```

##### `readable.pause()`

Added in: v0.9.4

- Returns: [`<this>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Operators/this)

The `readable.pause()` method will cause a stream in flowing mode to stop
emitting [`'data'`](#event-data) events, switching out of flowing mode. Any data that
becomes available will remain in the internal buffer.

```js
const readable = getReadableStreamSomehow();
readable.on('data', (chunk) => {
  console.log(`Received ${chunk.length} bytes of data.`);
  readable.pause();
  console.log('There will be no additional data for 1 second.');
  setTimeout(() => {
    console.log('Now data will start flowing again.');
    readable.resume();
  }, 1000);
});
```

The `readable.pause()` method has no effect if there is a `'readable'`
event listener.

##### `readable.pipe(destination[, options])`

Added in: v0.9.4

- `destination` [`<stream.Writable>`](51-stream-part1.md#class-streamwritable) The destination for writing data
- `options` [`<Object>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Object) Pipe options
  - `end` [`<boolean>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#boolean_type) End the writer when the reader ends. **Default:** `true`.
- Returns: [`<stream.Writable>`](51-stream-part1.md#class-streamwritable) The *destination*, allowing for a chain of pipes if
  it is a [`Duplex`](51-stream-part2.md#class-streamduplex) or a [`Transform`](51-stream-part2.md#class-streamtransform) stream

The `readable.pipe()` method attaches a [`Writable`](#class-streamwritable) stream to the `readable`,
causing it to switch automatically into flowing mode and push all of its data
to the attached [`Writable`](#class-streamwritable). The flow of data will be automatically managed
so that the destination `Writable` stream is not overwhelmed by a faster
`Readable` stream.

The following example pipes all of the data from the `readable` into a file
named `file.txt`:

```js
const fs = require('node:fs');
const readable = getReadableStreamSomehow();
const writable = fs.createWriteStream('file.txt');
// All the data from readable goes into 'file.txt'.
readable.pipe(writable);
```

It is possible to attach multiple `Writable` streams to a single `Readable`
stream.

The `readable.pipe()` method returns a reference to the *destination* stream
making it possible to set up chains of piped streams:

```js
const fs = require('node:fs');
const zlib = require('node:zlib');
const r = fs.createReadStream('file.txt');
const z = zlib.createGzip();
const w = fs.createWriteStream('file.txt.gz');
r.pipe(z).pipe(w);
```

By default, [`stream.end()`](#writableendchunk-encoding-callback) is called on the destination `Writable`
stream when the source `Readable` stream emits [`'end'`](#event-end), so that the
destination is no longer writable. To disable this default behavior, the `end`
option can be passed as `false`, causing the destination stream to remain open:

```js
reader.pipe(writer, { end: false });
reader.on('end', () => {
  writer.end('Goodbye\n');
});
```

One important caveat is that if the `Readable` stream emits an error during
processing, the `Writable` destination *is not closed* automatically. If an
error occurs, it will be necessary to *manually* close each stream in order
to prevent memory leaks.

The [`process.stderr`](43-process-part3.md#processstderr) and [`process.stdout`](43-process-part3.md#processstdout) `Writable` streams are never
closed until the Node.js process exits, regardless of the specified options.

##### `readable.read([size])`

Added in: v0.9.4

- `size` [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) Optional argument to specify how much data to read.
- Returns: [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) | [`<Buffer>`](07-buffer-part1.md#class-buffer) | [`<null>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#null_type) | [`<any>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#Data_types)

The `readable.read()` method reads data out of the internal buffer and
returns it. If no data is available to be read, `null` is returned. By default,
the data is returned as a `Buffer` object unless an encoding has been
specified using the `readable.setEncoding()` method or the stream is operating
in object mode.

The optional `size` argument specifies a specific number of bytes to read. If
`size` bytes are not available to be read, `null` will be returned *unless*
the stream has ended, in which case all of the data remaining in the internal
buffer will be returned.

If the `size` argument is not specified, all of the data contained in the
internal buffer will be returned.

The `size` argument must be less than or equal to 1 GiB.

The `readable.read()` method should only be called on `Readable` streams
operating in paused mode. In flowing mode, `readable.read()` is called
automatically until the internal buffer is fully drained.

```js
const readable = getReadableStreamSomehow();

// 'readable' may be triggered multiple times as data is buffered in
readable.on('readable', () => {
  let chunk;
  console.log('Stream is readable (new data received in buffer)');
  // Use a loop to make sure we read all currently available data
  while (null !== (chunk = readable.read())) {
    console.log(`Read ${chunk.length} bytes of data...`);
  }
});

// 'end' will be triggered once when there is no more data available
readable.on('end', () => {
  console.log('Reached end of stream.');
});
```

Each call to `readable.read()` returns a chunk of data or `null`, signifying
that there's no more data to read at that moment. These chunks aren't automatically
concatenated. Because a single `read()` call does not return all the data, using
a while loop may be necessary to continuously read chunks until all data is retrieved.
When reading a large file, `.read()` might return `null` temporarily, indicating
that it has consumed all buffered content but there may be more data yet to be
buffered. In such cases, a new `'readable'` event is emitted once there's more
data in the buffer, and the `'end'` event signifies the end of data transmission.

Therefore to read a file's whole contents from a `readable`, it is necessary
to collect chunks across multiple `'readable'` events:

```js
const chunks = [];

readable.on('readable', () => {
  let chunk;
  while (null !== (chunk = readable.read())) {
    chunks.push(chunk);
  }
});

readable.on('end', () => {
  const content = chunks.join('');
});
```

A `Readable` stream in object mode will always return a single item from
a call to [`readable.read(size)`](#readablereadsize), regardless of the value of the
`size` argument.

If the `readable.read()` method returns a chunk of data, a `'data'` event will
also be emitted.

Calling [`stream.read([size])`](#readablereadsize) after the [`'end'`](#event-end) event has
been emitted will return `null`. No runtime error will be raised.

##### `readable.readable`

Added in: v11.4.0

- Type: [`<boolean>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#boolean_type)

Is `true` if it is safe to call [`readable.read()`](#readablereadsize), which means
the stream has not been destroyed or emitted `'error'` or `'end'`.

##### `readable.readableAborted`

Added in: v16.8.0

**History**

| Version | Changes |
| --- | --- |
| v24.0.0, v22.17.0 | Marking the API stable. |

- Type: [`<boolean>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#boolean_type)

Returns whether the stream was destroyed or errored before emitting `'end'`.

##### `readable.readableDidRead`

Added in: v16.7.0, v14.18.0

**History**

| Version | Changes |
| --- | --- |
| v24.0.0, v22.17.0 | Marking the API stable. |

- Type: [`<boolean>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#boolean_type)

Returns whether `'data'` has been emitted.

[Contents](../README.md) | [Previous: SQLite](50-sqlite.md) | [Next: Stream, part 2 of 4](51-stream-part2.md)
