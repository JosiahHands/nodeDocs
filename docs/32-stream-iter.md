[Contents](../README.md) | [Previous: Internationalization](31-intl.md) | [Next: Modules: CommonJS modules](33-modules.md)

Source: [https://nodejs.org/docs/latest/api/stream_iter.html](https://nodejs.org/docs/latest/api/stream_iter.html)

# Iterable Streams

[Stability: 1](01-documentation.md#stability-index) - Experimental – Enable this API with the [`--experimental-stream-iter`](13-cli.md#--experimental-stream-iter) CLI flag.

The `node:stream/iter` module provides a streaming API built on iterables
rather than the event-driven `Readable`/`Writable`/`Transform` class hierarchy,
or the Web Streams `ReadableStream`/`WritableStream`/`TransformStream` interfaces.

Streams are represented as [`<AsyncIterable>`](https://tc39.github.io/ecma262/#sec-asynciterable-interface) (async) or [`<Iterable>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Iteration_protocols#the_iterable_protocol) (sync). There
are no base classes to extend -- any
object implementing the iterable protocol can participate. Transforms are plain
functions or objects with a `transform` method.

Data flows in **batches** ([`<Uint8Array>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Uint8Array)[] per iteration) to amortize the cost
of async operations.

The module implements the WinterTC [Iterable Streams API](https://iter-streams.proposal.wintertc.org/) draft. The
classic stream interop functions ([`fromReadable()`](#fromreadablereadable), [`fromWritable()`](#fromwritablewritable-options),
[`toReadable()`](#toreadablesource-options), [`toReadableSync()`](#toreadablesyncsource-options) and [`toWritable()`](#towritablewriter)),
[`Broadcast.from()`](#broadcastfrominput-options), [`Share.from()`](#static-method-sharefrominput-options), [`SyncShare.fromSync()`](#static-method-syncsharefromsyncinput-options) and the
protocol symbols exported by `Stream` are Node.js extensions that are not part
of the draft.

*ES module version:*

```js
import { from, pull, text } from 'node:stream/iter';
import { compressGzip, decompressGzip } from 'node:zlib/iter';

// Compress and decompress a string
const compressed = pull(from('Hello, world!'), compressGzip());
const result = await text(pull(compressed, decompressGzip()));
console.log(result); // 'Hello, world!'
```

*CommonJS version:*

```js
const { from, pull, text } = require('node:stream/iter');
const { compressGzip, decompressGzip } = require('node:zlib/iter');

async function run() {
  // Compress and decompress a string
  const compressed = pull(from('Hello, world!'), compressGzip());
  const result = await text(pull(compressed, decompressGzip()));
  console.log(result); // 'Hello, world!'
}

run().catch(console.error);
```

*ES module version:*

```js
import { open } from 'node:fs/promises';
import { text, pipeTo } from 'node:stream/iter';
import { compressGzip, decompressGzip } from 'node:zlib/iter';

// Read a file, compress, write to another file
const src = await open('input.txt', 'r');
const dst = await open('output.gz', 'w');
await pipeTo(src.pull(), compressGzip(), dst.writer({ autoClose: true }));
await src.close();

// Read it back
const gz = await open('output.gz', 'r');
console.log(await text(gz.pull(decompressGzip(), { autoClose: true })));
```

*CommonJS version:*

```js
const { open } = require('node:fs/promises');
const { text, pipeTo } = require('node:stream/iter');
const { compressGzip, decompressGzip } = require('node:zlib/iter');

async function run() {
  // Read a file, compress, write to another file
  const src = await open('input.txt', 'r');
  const dst = await open('output.gz', 'w');
  await pipeTo(src.pull(), compressGzip(), dst.writer({ autoClose: true }));
  await src.close();

  // Read it back
  const gz = await open('output.gz', 'r');
  console.log(await text(gz.pull(decompressGzip(), { autoClose: true })));
}

run().catch(console.error);
```

## Concepts

### Byte streams

All data in this API is represented as [`<Uint8Array>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Uint8Array) bytes. Strings
are automatically UTF-8 encoded when passed to `from()`, `push()`, or
`pipeTo()`. This removes ambiguity around encodings and enables zero-copy
transfers between streams and native code.

### Batching

Each iteration yields a **batch** -- an [`<Array>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Array) of [`<Uint8Array>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Uint8Array) chunks
([`<Uint8Array>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Uint8Array)[]). Batching amortizes the cost of `await` and [`<Promise>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Promise) creation
across multiple chunks. A consumer that processes one chunk at a time can
simply iterate the inner array:

*ES module version:*

```js
for await (const batch of source) {
  for (const chunk of batch) {
    handle(chunk);
  }
}
```

*CommonJS version:*

```js
async function run() {
  for await (const batch of source) {
    for (const chunk of batch) {
      handle(chunk);
    }
  }
}
```

### Transforms

Transforms come in two forms:

- **Stateless** -- a function `(chunks, options) => result` called once per
  batch. Receives `Uint8Array[]` (or `null` as the flush signal) and an
  `options` object. Returns [`<Uint8Array>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Uint8Array)[] | [`<null>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#null_type) | [`<Iterable>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Iteration_protocols#the_iterable_protocol).
- **Stateful** -- an object `{ transform(source, options) }` where `transform`
  is a generator (sync or async) that receives the entire upstream iterable
  and an `options` object, and yields output. This form is used for
  compression, encryption, and any transform that needs to buffer across
  batches.

Both forms receive an `options` parameter with the following property:

- `options.signal` [`<AbortSignal>`](26-globals.md#class-abortsignal) An AbortSignal that fires when the pipeline
  is cancelled, encounters an error, or the consumer stops reading. Transforms
  can check `signal.aborted` or listen for the `'abort'` event to perform
  early cleanup.

The flush signal (`null`) is sent after the source ends, giving transforms
a chance to emit trailing data (e.g., compression footers).

```js
// Stateless: uppercase transform
const upper = (chunks) => {
  if (chunks === null) return null; // flush
  return chunks.map((c) => new TextEncoder().encode(
    new TextDecoder().decode(c).toUpperCase(),
  ));
};

// Stateful: line splitter
const lines = {
  transform: async function*(source) {
    let partial = '';
    for await (const chunks of source) {
      if (chunks === null) {
        if (partial) yield [new TextEncoder().encode(partial)];
        continue;
      }
      for (const chunk of chunks) {
        const str = partial + new TextDecoder().decode(chunk);
        const parts = str.split('\n');
        partial = parts.pop();
        for (const line of parts) {
          yield [new TextEncoder().encode(`${line}\n`)];
        }
      }
    }
  },
};
```

### Pull vs. push

The API supports two models:

- **Pull** -- data flows on demand. `pull()` and `pullSync()` create lazy
  pipelines that only read from the source when the consumer iterates.
- **Push** -- data is written explicitly. `push()` creates a writer/readable
  pair with backpressure. The writer pushes data in; the readable is consumed
  as an async iterable.

### Backpressure

Pull streams have natural backpressure -- the consumer drives the pace, so
the source is never read faster than the consumer can process. Push streams
need explicit backpressure because the producer and consumer run
independently. The `budget` and `backpressure` options on `push()`,
`broadcast()`, and `share()` control how this works.

#### The two-buffer model

Push streams use a two-part buffering system. Think of it like a bucket
(buffer) being filled through a hose (pending writes), with a float valve
that closes when the bucket is full:

```text
                          budget (e.g., 16384)
                                 |
    Producer                     v
       |                    +---------+
       v                    |         |
  [ write() ] ----+    +--->| buffer  |---> Consumer pulls
  [ write() ]     |    |    | (bucket)|     for await (...)
  [ write() ]     v    |    +---------+
              +--------+         ^
              | pending|         |
              | writes |    float valve
              | (hose) |    (backpressure)
              +--------+
                   ^
                   |
          'strict' mode limits this too!
```

- **Buffer (the bucket)** -- data ready for the consumer, capped at
  `budget` bytes. When the consumer pulls, it drains all buffered data
  at once into a single batch.
- **Pending writes (the hose)** -- writes waiting for buffer space. After
  the consumer drains, pending writes are promoted into the now-empty
  buffer and their promises settle.

How each policy uses these buffers:

| Policy | Buffer limit | Pending writes limit |
| --- | --- | --- |
| `'strict'` | `budget` | 1 |
| `'unbounded'` | `budget` | Unbounded |
| `'drop-oldest'` | `budget` | N/A (never waits) |
| `'drop-newest'` | `budget` | N/A (never waits) |

#### Strict (default)

Strict mode catches "fire-and-forget" patterns where the producer calls
`write()` without awaiting, which would cause unbounded memory growth.
It limits the buffer to `budget` bytes and the pending writes queue
to a single entry.

If you properly await each write, you can only ever have one pending
write at a time (yours), so you never hit the pending writes limit.
Unawaited writes accumulate in the pending queue and throw once it
overflows:

*ES module version:*

```js
import { push, text } from 'node:stream/iter';

const { writer, readable } = push({ budget: 16384 });

// Consumer must run concurrently -- without it, the first write
// that fills the buffer blocks the producer forever.
const consuming = text(readable);

// GOOD: awaited writes. The producer waits for the consumer to
// make room when the buffer is full.
for (const item of dataset) {
  await writer.write(item);
}
await writer.end();
console.log(await consuming);
```

*CommonJS version:*

```js
const { push, text } = require('node:stream/iter');

async function run() {
  const { writer, readable } = push({ budget: 16384 });

  // Consumer must run concurrently -- without it, the first write
  // that fills the buffer blocks the producer forever.
  const consuming = text(readable);

  // GOOD: awaited writes. The producer waits for the consumer to
  // make room when the buffer is full.
  for (const item of dataset) {
    await writer.write(item);
  }
  await writer.end();
  console.log(await consuming);
}

run().catch(console.error);
```

Forgetting to `await` will eventually throw:

```js
// BAD: fire-and-forget. Strict mode throws once both buffers fill.
for (const item of dataset) {
  writer.write(item); // Not awaited -- queues without bound
}
// --> throws "Backpressure violation: too many pending writes"
```

#### Unbounded

Unbounded mode caps buffered bytes at `budget` but places no limit on the
pending writes queue. Awaited writes block until the consumer makes room,
just like strict mode. The difference is that unawaited writes silently
queue forever instead of throwing -- a potential memory leak if the
producer forgets to `await`.

This is the mode that existing Node.js classic streams and Web Streams
default to. Use it when you control the producer and know it awaits
properly, or when migrating code from those APIs.

*ES module version:*

```js
import { push, text } from 'node:stream/iter';

const { writer, readable } = push({
  budget: 16384,
  backpressure: 'unbounded',
});

const consuming = text(readable);

// Safe -- awaited writes block until the consumer reads.
for (const item of dataset) {
  await writer.write(item);
}
await writer.end();
console.log(await consuming);
```

*CommonJS version:*

```js
const { push, text } = require('node:stream/iter');

async function run() {
  const { writer, readable } = push({
    budget: 16384,
    backpressure: 'unbounded',
  });

  const consuming = text(readable);

  // Safe -- awaited writes block until the consumer reads.
  for (const item of dataset) {
    await writer.write(item);
  }
  await writer.end();
  console.log(await consuming);
}

run().catch(console.error);
```

#### Drop-oldest

Writes never wait. When the slots buffer is full, the oldest buffered
chunk is evicted to make room for the incoming write. The consumer
always sees the most recent data. Useful for live feeds, telemetry, or
any scenario where stale data is less valuable than current data.

*ES module version:*

```js
import { push } from 'node:stream/iter';

// Keep only the most recent ~16 KB of readings
const { writer, readable } = push({
  budget: 16384,
  backpressure: 'drop-oldest',
});
```

*CommonJS version:*

```js
const { push } = require('node:stream/iter');

// Keep only the most recent ~16 KB of readings
const { writer, readable } = push({
  budget: 16384,
  backpressure: 'drop-oldest',
});
```

#### Drop-newest

Writes never wait. When the slots buffer is full, the incoming write is
silently discarded. The consumer processes what is already buffered
without being overwhelmed by new data. Useful for rate-limiting or
shedding load under pressure.

*ES module version:*

```js
import { push } from 'node:stream/iter';

// Accept up to 16 KB of buffered data; discard anything beyond that
const { writer, readable } = push({
  budget: 16384,
  backpressure: 'drop-newest',
});
```

*CommonJS version:*

```js
const { push } = require('node:stream/iter');

// Accept up to 16 KB of buffered data; discard anything beyond that
const { writer, readable } = push({
  budget: 16384,
  backpressure: 'drop-newest',
});
```

### Writer interface

A writer is any object conforming to the Writer interface. Only `write()` is
required; all other methods are optional.

Writer arguments use Web IDL conversion semantics. A non-`Uint8Array` chunk is
converted to a `USVString` and then UTF-8 encoded. `writev()` and
`writevSync()` accept any iterable object whose values can be converted to
chunks. Writer option dictionaries treat `null` as an empty dictionary and
ignore unknown members.

Arguments are converted before the write itself starts. If the conversion runs
user code (for example a `toString()` method, or the iterator of a `writev()`
argument) that writes to the same writer, those writes are ordered before the
write whose argument is being converted, and they count against the same
backpressure limits.

After `end()` or `endSync()` has been called, and until all buffered data has
been consumed, the writer is *closing*. While closing, `canWrite` is `null`,
`write()` and `writev()` reject with a `TypeError`, `writeSync()` and
`writevSync()` return `false`, `endSync()` returns `-1`, and calling `end()`
again returns the same promise as the first call.

Each async method has a synchronous `*Sync` counterpart designed for a
try-fallback pattern: attempt the fast synchronous path first, and fall back
to the async version only when the synchronous call indicates it could not
complete:

```js
if (!writer.writeSync(chunk)) await writer.write(chunk);
if (!writer.writevSync(chunks)) await writer.writev(chunks);
if (writer.endSync() < 0) await writer.end();
writer.fail(err);  // Always synchronous, no fallback needed
```

#### `writer.canWrite`

- [`<boolean>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#boolean_type) | [`<null>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#null_type)

Returns `true` if the slots buffer has physical capacity (buffered data is
below the configured byte budget), `false` if the budget is exhausted, or
`null` if the writer is closed or the consumer has disconnected.

This reports physical capacity independently of the backpressure policy. With
`'drop-oldest'` or `'drop-newest'`, writes still complete when this is `false`
by evicting buffered data or discarding the incoming data, respectively.

This is a hint, not a guarantee: the state can change between the check and
the write. Use [`ondrain()`](#ondraindrainable) to wait for capacity rather than polling.

#### `writer.end([options])`

- `options` [`<Object>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Object)
  - `signal` [`<AbortSignal>`](26-globals.md#class-abortsignal) Cancel just this operation. The signal cancels only
    the pending `end()` call; it does not fail the writer itself.
- Returns: [`<Promise>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Promise) Fulfills with the total number of bytes written.

Signals that no more data will be written. Writes already waiting for buffer
space remain ordered before the end of the stream, while later writes fail. If
data is outstanding, the returned promise fulfills after the consumer pulls
`done: true` beyond the final batch. If no data is buffered or pending, the
writer closes immediately.

#### `writer.endSync()`

- Returns: [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) Total bytes written, or `-1` if ending cannot complete
  synchronously.

Synchronous variant of `writer.end()`. A return value of `-1` only indicates
that the operation could not complete synchronously; no assumption can be made
about whether closing has started or why it could not complete. Use the
try-fallback pattern to await completion:

```js
const result = writer.endSync();
if (result < 0) {
  writer.end();
}
```

#### `writer.fail([reason])`

- `reason` [`<any>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#Data_types)

Put the writer into a terminal error state. If the writer is already closed
or errored, this is a no-op. Unlike `write()` and `end()`, `fail()` is
unconditionally synchronous because failing a writer is a pure state
transition with no async work to perform. The reason is stored and propagated
without modification. If omitted, the reason is `undefined`.

#### `writer[Symbol.asyncDispose]()`

If the writer is open, calls `writer.fail()`. If the writer is closing after
`end()` or `endSync()`, waits for buffered data to drain. If the writer is
already closed or errored, resolves immediately.

#### `writer.write(chunk[, options])`

- `chunk` [`<Uint8Array>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Uint8Array) | [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type)
- `options` [`<Object>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Object)
  - `signal` [`<AbortSignal>`](26-globals.md#class-abortsignal) Cancel just this write operation. The signal cancels
    only the pending `write()` call; it does not fail the writer itself.
- Returns: [`<Promise>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Promise) Fulfills with `undefined` when buffer space is available.

Write a chunk.

#### `writer.writeSync(chunk)`

- `chunk` [`<Uint8Array>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Uint8Array) | [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type)
- Returns: [`<boolean>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#boolean_type) `true` if the write was accepted, `false` if the
  buffer is full.

Synchronous write. Does not block; returns `false` if backpressure is active.

#### `writer.writev(chunks[, options])`

- `chunks` [`<Iterable>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Iteration_protocols#the_iterable_protocol) of [`<Uint8Array>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Uint8Array) | [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) values
- `options` [`<Object>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Object)
  - `signal` [`<AbortSignal>`](26-globals.md#class-abortsignal) Cancel just this write operation. The signal cancels
    only the pending `writev()` call; it does not fail the writer itself.
- Returns: [`<Promise>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Promise)

Write multiple chunks as a single batch.

#### `writer.writevSync(chunks)`

- `chunks` [`<Iterable>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Iteration_protocols#the_iterable_protocol) of [`<Uint8Array>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Uint8Array) | [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) values
- Returns: [`<boolean>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#boolean_type) `true` if the write was accepted, `false` if the
  buffer is full.

Synchronous batch write.

## The `stream/iter` module

Most functions are available both as named exports and as properties of the
`Stream` namespace object. The classic stream adapters (`fromReadable()`,
`fromWritable()`, `toReadable()`, `toReadableSync()`, and `toWritable()`) and
the static helper objects (`Broadcast`, `Share`, and `SyncShare`) are named
exports only.

```js
// Named exports
import { from, pull, bytes, Stream } from 'node:stream/iter';

// Namespace access
Stream.from('hello');
```

Options dictionaries defined by the Iterable Streams API use Web IDL
conversion semantics. `null` is treated as an empty dictionary, unknown
members are ignored, and known members are converted to their declared types
before the operation runs. Conversion failures use Node.js error codes such as
`ERR_INVALID_ARG_TYPE`, `ERR_INVALID_ARG_VALUE`, and `ERR_OUT_OF_RANGE`.

```js
// Named exports
const { from, pull, bytes, Stream } = require('node:stream/iter');

// Namespace access
Stream.from('hello');
```

Including the `node:` prefix on the module specifier is optional.

## Sources

### `from(input)`

Added in: v25.9.0

- `input` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) | [`<ArrayBuffer>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/ArrayBuffer) | [`<ArrayBufferView>`](https://developer.mozilla.org/docs/Web/API/ArrayBufferView) | [`<Iterable>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Iteration_protocols#the_iterable_protocol) | [`<AsyncIterable>`](https://tc39.github.io/ecma262/#sec-asynciterable-interface) | [`<Object>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Object)
  Must not be `null` or `undefined`.
- Returns: [`<AsyncIterable>`](https://tc39.github.io/ecma262/#sec-asynciterable-interface) whose chunks fulfill with [`<Uint8Array>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Uint8Array)[].

Create an async byte stream from the given input. Strings are UTF-8 encoded.
`ArrayBuffer` and `ArrayBufferView` values are wrapped as `Uint8Array`. Arrays
and iterables in `input` are recursively flattened and normalized. Flattened
values may be split across implementation-defined bounded batches.

Objects implementing `Symbol.for('Stream.toAsyncStreamable')` or
`Symbol.for('Stream.toStreamable')` are converted via those protocols. The
`toAsyncStreamable` protocol takes precedence over `toStreamable`, which takes
precedence over the iteration protocols (`Symbol.asyncIterator`,
`Symbol.iterator`).

*ES module version:*

```js
import { Buffer } from 'node:buffer';
import { from, text } from 'node:stream/iter';

console.log(await text(from('hello')));       // 'hello'
console.log(await text(from(Buffer.from('hello')))); // 'hello'
```

*CommonJS version:*

```js
const { Buffer } = require('node:buffer');
const { from, text } = require('node:stream/iter');

async function run() {
  console.log(await text(from('hello')));       // 'hello'
  console.log(await text(from(Buffer.from('hello')))); // 'hello'
}

run().catch(console.error);
```

### `fromSync(input)`

Added in: v25.9.0

- `input` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) | [`<ArrayBuffer>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/ArrayBuffer) | [`<ArrayBufferView>`](https://developer.mozilla.org/docs/Web/API/ArrayBufferView) | [`<Iterable>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Iteration_protocols#the_iterable_protocol) | [`<Object>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Object)
  Must not be `null` or `undefined`.
- Returns: [`<Iterable>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Iteration_protocols#the_iterable_protocol) whose chunks return [`<Uint8Array>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Uint8Array)[]

Synchronous version of [`from()`](#frominput). Returns a sync iterable. Cannot accept
async iterables or promises. Objects implementing
`Symbol.for('Stream.toStreamable')` are converted via that protocol (takes
precedence over `Symbol.iterator`). The `toAsyncStreamable` protocol is
ignored entirely.

*ES module version:*

```js
import { fromSync, textSync } from 'node:stream/iter';

console.log(textSync(fromSync('hello'))); // 'hello'
```

*CommonJS version:*

```js
const { fromSync, textSync } = require('node:stream/iter');

console.log(textSync(fromSync('hello'))); // 'hello'
```

## Pipelines

### `pipeTo(source[, ...transforms], writer[, options])`

Added in: v25.9.0

- `source` [`<AsyncIterable>`](https://tc39.github.io/ecma262/#sec-asynciterable-interface) | [`<Iterable>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Iteration_protocols#the_iterable_protocol) The data source.
- `...transforms` [`<Function>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Function) | [`<Object>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Object) Zero or more transforms to apply.
- `writer` [`<Object>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Object) Destination with `write(chunk)` method.
- `options` [`<Object>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Object)
  - `signal` [`<AbortSignal>`](26-globals.md#class-abortsignal) Abort the pipeline. Aborting fails the destination
    writer unless `preventFail` is `true`.
  - `preventClose` [`<boolean>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#boolean_type) If `true`, do not call `writer.end()` when
    the source ends. **Default:** `false`.
  - `preventFail` [`<boolean>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#boolean_type) If `true`, do not call `writer.fail()` on
    error. **Default:** `false`.
- Returns: [`<Promise>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Promise) Fulfills with the total number of bytes written.

Pipe a source through transforms into a writer. If the writer has a
`writev(chunks)` method, entire batches are passed in a single call (enabling
scatter/gather I/O).

If the writer implements the optional `*Sync` methods (`writeSync`, `writevSync`,
`endSync`), `pipeTo()` will attempt to use the synchronous methods
first as a fast path, and fall back to the async versions only when the sync
methods indicate they cannot complete (e.g., backpressure or waiting for the
next tick). `fail()` is always called synchronously.

*ES module version:*

```js
import { from, pipeTo } from 'node:stream/iter';
import { compressGzip } from 'node:zlib/iter';
import { open } from 'node:fs/promises';

const fh = await open('output.gz', 'w');
const totalBytes = await pipeTo(
  from('Hello, world!'),
  compressGzip(),
  fh.writer({ autoClose: true }),
);
```

*CommonJS version:*

```js
const { from, pipeTo } = require('node:stream/iter');
const { compressGzip } = require('node:zlib/iter');
const { open } = require('node:fs/promises');

async function run() {
  const fh = await open('output.gz', 'w');
  const totalBytes = await pipeTo(
    from('Hello, world!'),
    compressGzip(),
    fh.writer({ autoClose: true }),
  );
}

run().catch(console.error);
```

### `pipeToSync(source[, ...transforms], writer[, options])`

Added in: v25.9.0

- `source` [`<Iterable>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Iteration_protocols#the_iterable_protocol) The sync data source.
- `...transforms` [`<Function>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Function) | [`<Object>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Object) Zero or more sync transforms.
- `writer` [`<Object>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Object) Destination with `write(chunk)` method.
- `options` [`<Object>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Object)
  - `failOnIncompleteClose` [`<boolean>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#boolean_type) If `true`, call `writer.fail()` when
    `writer.endSync()` cannot close the writer synchronously. Ignored when
    `preventFail` is `true`. This option is a Node.js extension.
    **Default:** `false`.
  - `preventClose` [`<boolean>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#boolean_type) **Default:** `false`.
  - `preventFail` [`<boolean>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#boolean_type) **Default:** `false`.
- Returns: [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) Total bytes written.

Synchronous version of [`pipeTo()`](#pipetosource-transforms-writer-options). The `source`, all transforms, and the
`writer` must be synchronous. Cannot accept async iterables or promises.

The `writer` must have the `*Sync` methods (`writeSync`, `writevSync`,
`endSync`) and `fail()` for this to work.

`pipeToSync()` never falls back to the asynchronous writer methods. If
`writer.endSync()` returns `-1` because the writer cannot close synchronously
(for example, a `push()` writer whose consumer has not read all of the data
yet), `pipeToSync()` throws `ERR_INVALID_STATE`. All of the data was accepted
by then, so by default the writer is not failed: it can still be closed, for
example with `await writer.end()`. If the writer cannot be closed any other
way (for example, it has no `end()` method), or the caller will not close it,
set `failOnIncompleteClose` to fail it with the thrown error instead.

### `pull(source[, ...transforms][, options])`

Added in: v25.9.0

- `source` [`<AsyncIterable>`](https://tc39.github.io/ecma262/#sec-asynciterable-interface) | [`<Iterable>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Iteration_protocols#the_iterable_protocol) The data source.
- `...transforms` [`<Function>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Function) | [`<Object>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Object) Zero or more transforms to apply.
- `options` [`<Object>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Object)
  - `signal` [`<AbortSignal>`](26-globals.md#class-abortsignal) Abort the pipeline.
- Returns: [`<AsyncIterable>`](https://tc39.github.io/ecma262/#sec-asynciterable-interface) whose chunks fulfill with [`<Uint8Array>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Uint8Array)[]

Create a lazy async pipeline. Source conversion and streamable protocol
dispatch occur when `pull()` is called, but data is not read from `source`
until the returned iterable is consumed. Transforms are applied in order.

When `signal` aborts, the pending read (or the next one) rejects with
`signal.reason`, and so does every later read. If `signal` is already aborted,
`pull()` still returns an iterable; reading from it rejects with
`signal.reason` without reading from `source`.

*ES module version:*

```js
import { from, pull, text } from 'node:stream/iter';

const asciiUpper = (chunks) => {
  if (chunks === null) return null;
  return chunks.map((c) => {
    for (let i = 0; i < c.length; i++) {
      c[i] -= (c[i] >= 97 && c[i] <= 122) * 32;
    }
    return c;
  });
};

const result = pull(from('hello'), asciiUpper);
console.log(await text(result)); // 'HELLO'
```

*CommonJS version:*

```js
const { from, pull, text } = require('node:stream/iter');

const asciiUpper = (chunks) => {
  if (chunks === null) return null;
  return chunks.map((c) => {
    for (let i = 0; i < c.length; i++) {
      c[i] -= (c[i] >= 97 && c[i] <= 122) * 32;
    }
    return c;
  });
};

async function run() {
  const result = pull(from('hello'), asciiUpper);
  console.log(await text(result)); // 'HELLO'
}

run().catch(console.error);
```

Using an `AbortSignal`:

*ES module version:*

```js
import { pull } from 'node:stream/iter';

const ac = new AbortController();
const result = pull(source, transform, { signal: ac.signal });
ac.abort(); // Pipeline throws AbortError on next iteration
```

*CommonJS version:*

```js
const { pull } = require('node:stream/iter');

const ac = new AbortController();
const result = pull(source, transform, { signal: ac.signal });
ac.abort(); // Pipeline throws AbortError on next iteration
```

### `pullSync(source[, ...transforms])`

Added in: v25.9.0

- `source` [`<Iterable>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Iteration_protocols#the_iterable_protocol) The sync data source.
- `...transforms` [`<Function>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Function) | [`<Object>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Object) Zero or more sync transforms.
- Returns: [`<Iterable>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Iteration_protocols#the_iterable_protocol) whose chunks return [`<Uint8Array>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Uint8Array)[]

Synchronous version of [`pull()`](#pullsource-transforms-options). Source conversion and streamable protocol
dispatch occur when `pullSync()` is called. All transforms must be synchronous.

## Push streams

### `push([...transforms][, options])`

Added in: v25.9.0

- `...transforms` [`<Function>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Function) | [`<Object>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Object) Optional transforms applied to the
  readable side.
- `options` [`<Object>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Object)
  - `budget` [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) Maximum number of buffered bytes before
    backpressure is applied. Must be a positive integer. **Default:** `16384`.
  - `backpressure` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) Backpressure policy: `'strict'`, `'unbounded'`,
    `'drop-oldest'`, or `'drop-newest'`. **Default:** `'strict'`.
  - `signal` [`<AbortSignal>`](26-globals.md#class-abortsignal) Abort the stream. The signal remains active while
    buffered data drains after `writer.end()`; aborting during that time fails
    the writer and rejects the pending `end()` promise.
- Returns: [`<Object>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Object)
  - `writer` [`<Writable>`](51-stream-part1.md#class-streamwritable) The writer side.
  - `readable` [`<AsyncIterable>`](https://tc39.github.io/ecma262/#sec-asynciterable-interface) whose chunks fulfill with [`<Uint8Array>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Uint8Array)[]

Create a push stream with backpressure. The writer pushes data in; the
readable side is consumed as an async iterable.

*ES module version:*

```js
import { push, text } from 'node:stream/iter';

const { writer, readable } = push();

// Producer and consumer must run concurrently. With strict backpressure
// (the default), awaited writes block until the consumer reads.
const producing = (async () => {
  await writer.write('hello');
  await writer.write(' world');
  await writer.end();
})();

console.log(await text(readable)); // 'hello world'
await producing;
```

*CommonJS version:*

```js
const { push, text } = require('node:stream/iter');

async function run() {
  const { writer, readable } = push();

  // Producer and consumer must run concurrently. With strict backpressure
  // (the default), awaited writes block until the consumer reads.
  const producing = (async () => {
    await writer.write('hello');
    await writer.write(' world');
    await writer.end();
  })();

  console.log(await text(readable)); // 'hello world'
  await producing;
}

run().catch(console.error);
```

The writer returned by `push()` conforms to the [Writer interface][].

Zero-length chunks are accepted without being buffered: they are not delivered
to the consumer, and `writeSync()` and `write()` report success for them even
when backpressure is active.

## Duplex channels

### `duplex([options])`

Added in: v25.9.0

- `options` [`<Object>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Object)
  - `budget` [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) Buffer size in bytes for both directions.
    Must be a positive integer. **Default:** `16384`.
  - `backpressure` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) Policy for both directions. **Default:** `'strict'`.
  - `signal` [`<AbortSignal>`](26-globals.md#class-abortsignal) Cancellation signal for both channels.
  - `a` [`<Object>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Object) Options specific to the A-to-B direction. Overrides
    shared options.
    - `budget` [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type)
    - `backpressure` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type)
  - `b` [`<Object>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Object) Options specific to the B-to-A direction. Overrides
    shared options.
    - `budget` [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type)
    - `backpressure` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type)
- Returns: [`<Array>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Array) A pair `[channelA, channelB]` of duplex channels.

Create a pair of connected duplex channels for bidirectional communication,
similar to `socketpair()`. Data written to one channel's writer appears in
the other channel's readable.

Each channel has:

- `writer` — a [Writer interface][] object for sending data to the peer.
- `readable` — an [`<AsyncIterable>`](https://tc39.github.io/ecma262/#sec-asynciterable-interface) for reading data from the peer.
- `close()` — close this end of the channel (idempotent).
- `[Symbol.asyncDispose]()` — async dispose support for `await using`.

*ES module version:*

```js
import { duplex, text } from 'node:stream/iter';

const [client, server] = duplex();

// Server echoes back
const serving = (async () => {
  for await (const chunks of server.readable) {
    await server.writer.writev(chunks);
  }
  await server.writer.end();
})();

await client.writer.write('hello');
await client.writer.end();

console.log(await text(client.readable)); // 'hello'
await serving;
```

*CommonJS version:*

```js
const { duplex, text } = require('node:stream/iter');

async function run() {
  const [client, server] = duplex();

  // Server echoes back
  const serving = (async () => {
    for await (const chunks of server.readable) {
      await server.writer.writev(chunks);
    }
    await server.writer.end();
  })();

  await client.writer.write('hello');
  await client.writer.end();

  console.log(await text(client.readable)); // 'hello'
  await serving;
}

run().catch(console.error);
```

## Consumers

### `array(source[, options])`

Added in: v25.9.0

- `source` [`<AsyncIterable>`](https://tc39.github.io/ecma262/#sec-asynciterable-interface) | [`<Iterable>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Iteration_protocols#the_iterable_protocol) whose chunks must be [`<Uint8Array>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Uint8Array)[]
- `options` [`<Object>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Object)
  - `signal` [`<AbortSignal>`](26-globals.md#class-abortsignal)
  - `limit` [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) Maximum number of bytes to consume. If the total bytes
    collected exceeds limit, an `ERR_OUT_OF_RANGE` error is thrown
- Returns: [`<Promise>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Promise) Fulfills with an array of `Uint8Array` objects.

Collect all chunks as an array of `Uint8Array` values (without concatenating).

### `arrayBuffer(source[, options])`

Added in: v25.9.0

- `source` [`<AsyncIterable>`](https://tc39.github.io/ecma262/#sec-asynciterable-interface) | [`<Iterable>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Iteration_protocols#the_iterable_protocol) whose chunks must be [`<Uint8Array>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Uint8Array)[]
- `options` [`<Object>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Object)
  - `signal` [`<AbortSignal>`](26-globals.md#class-abortsignal)
  - `limit` [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) Maximum number of bytes to consume. If the total bytes
    collected exceeds limit, an `ERR_OUT_OF_RANGE` error is thrown
- Returns: [`<Promise>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Promise) Fulfills with an `ArrayBuffer` object.

Collect all bytes into an `ArrayBuffer`.

### `arrayBufferSync(source[, options])`

Added in: v25.9.0

- `source` [`<Iterable>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Iteration_protocols#the_iterable_protocol) whose chunks must be [`<Uint8Array>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Uint8Array)[]
- `options` [`<Object>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Object)
  - `limit` [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) Maximum number of bytes to consume. If the total bytes
    collected exceeds limit, an `ERR_OUT_OF_RANGE` error is thrown
- Returns: [`<ArrayBuffer>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/ArrayBuffer)

Synchronous version of [`arrayBuffer()`](#arraybuffersource-options).

### `arraySync(source[, options])`

Added in: v25.9.0

- `source` [`<Iterable>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Iteration_protocols#the_iterable_protocol) whose chunks must be [`<Uint8Array>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Uint8Array)[]
- `options` [`<Object>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Object)
  - `limit` [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) Maximum number of bytes to consume. If the total bytes
    collected exceeds limit, an `ERR_OUT_OF_RANGE` error is thrown
- Returns: [`<Uint8Array>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Uint8Array)[]

Synchronous version of [`array()`](#arraysource-options).

### `bytes(source[, options])`

Added in: v25.9.0

- `source` [`<AsyncIterable>`](https://tc39.github.io/ecma262/#sec-asynciterable-interface) | [`<Iterable>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Iteration_protocols#the_iterable_protocol) whose chunks must be [`<Uint8Array>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Uint8Array)[]
- `options` [`<Object>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Object)
  - `signal` [`<AbortSignal>`](26-globals.md#class-abortsignal)
  - `limit` [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) Maximum number of bytes to consume. If the total bytes
    collected exceeds limit, an `ERR_OUT_OF_RANGE` error is thrown
- Returns: [`<Promise>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Promise) Fulfills with an `Uint8Array` object.

Collect all bytes from a stream into a single `Uint8Array`.

*ES module version:*

```js
import { from, bytes } from 'node:stream/iter';

const data = await bytes(from('hello'));
console.log(data); // Uint8Array(5) [ 104, 101, 108, 108, 111 ]
```

*CommonJS version:*

```js
const { from, bytes } = require('node:stream/iter');

async function run() {
  const data = await bytes(from('hello'));
  console.log(data); // Uint8Array(5) [ 104, 101, 108, 108, 111 ]
}

run().catch(console.error);
```

### `bytesSync(source[, options])`

Added in: v25.9.0

- `source` [`<Iterable>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Iteration_protocols#the_iterable_protocol) whose chunks must be [`<Uint8Array>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Uint8Array)[]
- `options` [`<Object>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Object)
  - `limit` [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) Maximum number of bytes to consume. If the total bytes
    collected exceeds limit, an `ERR_OUT_OF_RANGE` error is thrown
- Returns: [`<Uint8Array>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Uint8Array)

Synchronous version of [`bytes()`](#bytessource-options).

### `dump(source[, options])`

Added in: v26.12.0

- `source` [`<AsyncIterable>`](https://tc39.github.io/ecma262/#sec-asynciterable-interface) | [`<Iterable>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Iteration_protocols#the_iterable_protocol) whose chunks must be [`<Uint8Array>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Uint8Array)[]
- `options` [`<Object>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Object)
  - `signal` [`<AbortSignal>`](26-globals.md#class-abortsignal)
  - `limit` [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) Maximum number of bytes to consume. If the total bytes
    read exceeds limit, an `ERR_OUT_OF_RANGE` error is thrown
- Returns: [`<Promise>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Promise) Fulfills with `undefined`.

Read a source to completion, discarding every chunk. Unlike the other
consumers, `dump()` retains nothing. Memory tops-out at a single batch no
matter how much data the source produces.

Use this to consume a stream when the content doesn't matter. For example, A
QUIC stream only returns flow-control credit to the peer as its data is
consumed, so a receiver that does not want the payload must still read it to
completion.

If the source errors part-way through, the returned promise rejects with that
error.

There is no default `limit`. `dump()` reads until the source is exhausted
unless a limit is specified. When a limit is configured and the source exceeds
it, the promise rejects and the source is cancelled. A partial dump is never
reported as success.

*ES module version:*

```js
import { dump, from, pull, tap } from 'node:stream/iter';

// Count the bytes flowing through a stream without retaining any of them.
let bytesSeen = 0;
const counter = tap((chunks) => {
  for (const chunk of chunks) bytesSeen += chunk.byteLength;
});

await dump(pull(from('hello world'), counter));
console.log(bytesSeen); // 11
```

*CommonJS version:*

```js
const { dump, from, pull, tap } = require('node:stream/iter');

async function run() {
  // Count the bytes flowing through a stream without retaining any of them.
  let bytesSeen = 0;
  const counter = tap((chunks) => {
    for (const chunk of chunks) bytesSeen += chunk.byteLength;
  });

  await dump(pull(from('hello world'), counter));
  console.log(bytesSeen); // 11
}

run().catch(console.error);
```

### `dumpSync(source[, options])`

Added in: v26.12.0

- `source` [`<Iterable>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Iteration_protocols#the_iterable_protocol) whose chunks must be [`<Uint8Array>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Uint8Array)[]
- `options` [`<Object>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Object)
  - `limit` [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) Maximum number of bytes to consume. If the total bytes
    read exceeds limit, an `ERR_OUT_OF_RANGE` error is thrown
- Returns: [`<undefined>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#undefined_type)

Synchronous version of [`dump()`](#drainsource-options). Throws `ERR_INVALID_ARG_TYPE` if `source`
is not synchronously iterable.

### `text(source[, options])`

Added in: v25.9.0

- `source` [`<AsyncIterable>`](https://tc39.github.io/ecma262/#sec-asynciterable-interface) | [`<Iterable>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Iteration_protocols#the_iterable_protocol) whose chunks must be [`<Uint8Array>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Uint8Array)[]
- `options` [`<Object>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Object)
  - `encoding` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) Text encoding. **Default:** `'utf-8'`.
  - `signal` [`<AbortSignal>`](26-globals.md#class-abortsignal)
  - `limit` [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) Maximum number of bytes to consume. If the total bytes
    collected exceeds limit, an `ERR_OUT_OF_RANGE` error is thrown
- Returns: [`<Promise>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Promise) Fulfills with a `string`.

Collect all bytes and decode as text.

*ES module version:*

```js
import { from, text } from 'node:stream/iter';

console.log(await text(from('hello'))); // 'hello'
```

*CommonJS version:*

```js
const { from, text } = require('node:stream/iter');

async function run() {
  console.log(await text(from('hello'))); // 'hello'
}

run().catch(console.error);
```

### `textSync(source[, options])`

Added in: v25.9.0

- `source` [`<Iterable>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Iteration_protocols#the_iterable_protocol) whose chunks must be [`<Uint8Array>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Uint8Array)[]
- `options` [`<Object>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Object)
  - `encoding` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) **Default:** `'utf-8'`.
  - `limit` [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) Maximum number of bytes to consume. If the total bytes
    collected exceeds limit, an `ERR_OUT_OF_RANGE` error is thrown
- Returns: [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type)

Synchronous version of [`text()`](#textsource-options).

## Utilities

### `ondrain(drainable)`

Added in: v25.9.0

- `drainable` [`<Object>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Object) An object implementing the drainable protocol.
- Returns: [`<Promise>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Promise) | [`<null>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#null_type)

Wait for a drainable writer to regain physical buffer capacity. Returns `null`
if the object does not implement the drainable protocol, or a promise that
fulfills with `true` when buffered data falls below the byte budget.

For writers using `'drop-oldest'` or `'drop-newest'`, this waits for physical
capacity even though writes do not block. This allows producers to avoid data
loss by waiting before writing.

*ES module version:*

```js
import { push, ondrain, text } from 'node:stream/iter';

const { writer, readable } = push({ budget: 16384 });
const chunk = new Uint8Array(8192);  // 8 KB
writer.writeSync(chunk);
writer.writeSync(chunk);  // 16 KB total -- buffer full

// Start consuming so the buffer can actually drain
const consuming = text(readable);

// Buffer is full -- wait for drain
const canWrite = await ondrain(writer);
if (canWrite) {
  await writer.write('c');
}
await writer.end();
await consuming;
```

*CommonJS version:*

```js
const { push, ondrain, text } = require('node:stream/iter');

async function run() {
  const { writer, readable } = push({ budget: 16384 });
  const chunk = new Uint8Array(8192);  // 8 KB
  writer.writeSync(chunk);
  writer.writeSync(chunk);  // 16 KB total -- buffer full

  // Start consuming so the buffer can actually drain
  const consuming = text(readable);

  // Buffer is full -- wait for drain
  const canWrite = await ondrain(writer);
  if (canWrite) {
    await writer.write('c');
  }
  await writer.end();
  await consuming;
}

run().catch(console.error);
```

### `merge(...sources[, options])`

Added in: v25.9.0

- `...sources` [`<AsyncIterable>`](https://tc39.github.io/ecma262/#sec-asynciterable-interface) | [`<Iterable>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Iteration_protocols#the_iterable_protocol) whose chunks must be [`<Uint8Array>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Uint8Array)[]
- `options` [`<Object>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Object)
  - `signal` [`<AbortSignal>`](26-globals.md#class-abortsignal)
- Returns: [`<AsyncIterable>`](https://tc39.github.io/ecma262/#sec-asynciterable-interface) whose chunks fulfill with [`<Uint8Array>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Uint8Array)[]

Merge multiple async iterables by yielding batches in temporal order
(whichever source produces data first). All sources are consumed
concurrently, with at most one pending `next()` call per source.

If a source fails, the returned iterable rejects with its error. `merge()`
calls `return()` on the other sources but does not wait for it to settle: an
async generator source that is suspended in an `await` only runs its cleanup
once that `await` completes.

*ES module version:*

```js
import { from, merge, text } from 'node:stream/iter';

const merged = merge(from('hello '), from('world'));
console.log(await text(merged)); // Order depends on timing
```

*CommonJS version:*

```js
const { from, merge, text } = require('node:stream/iter');

async function run() {
  const merged = merge(from('hello '), from('world'));
  console.log(await text(merged)); // Order depends on timing
}

run().catch(console.error);
```

### `tap(callback)`

Added in: v25.9.0

- `callback` [`<Function>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Function) `(chunks, options) => void` Called with each batch and
  with `null` when the source ends. `options.signal` is the pipeline's
  [`<AbortSignal>`](26-globals.md#class-abortsignal).
- Returns: [`<Function>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Function) A stateless transform.

Create a pass-through transform that observes batches without modifying them.
Useful for logging, metrics, or debugging.

*ES module version:*

```js
import { from, pull, text, tap } from 'node:stream/iter';

const result = pull(
  from('hello'),
  tap((chunks) => {
    if (chunks !== null) console.log('Batch size:', chunks.length);
  }),
);
console.log(await text(result));
```

*CommonJS version:*

```js
const { from, pull, text, tap } = require('node:stream/iter');

async function run() {
  const result = pull(
    from('hello'),
    tap((chunks) => {
      if (chunks !== null) console.log('Batch size:', chunks.length);
    }),
  );
  console.log(await text(result));
}

run().catch(console.error);
```

`tap()` intentionally does not prevent in-place modification of the
chunks by the tapping callback; but return values are ignored.

### `tapSync(callback)`

Added in: v25.9.0

- `callback` [`<Function>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Function)
- Returns: [`<Function>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Function)

Synchronous version of [`tap()`](#tapcallback).

## Multi-consumer

### `broadcast([options])`

Added in: v25.9.0

- `options` [`<Object>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Object)
  - `budget` [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) Buffer size in bytes. Must be a positive integer. **Default:** `65536`.
  - `backpressure` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) `'strict'`, `'unbounded'`, `'drop-oldest'`, or
    `'drop-newest'`. **Default:** `'strict'`.
  - `signal` [`<AbortSignal>`](26-globals.md#class-abortsignal)
- Returns: [`<Object>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Object)
  - `writer` [`<Writable>`](51-stream-part1.md#class-streamwritable)
  - `broadcast` [`<BroadcastChannel>`](67-worker-threads.md#class-broadcastchannel-extends-eventtarget)

Create a push-model multi-consumer broadcast channel. A single writer pushes
data to multiple consumers. Each consumer has an independent cursor into a
shared buffer.

*ES module version:*

```js
import { broadcast, text } from 'node:stream/iter';

const { writer, broadcast: bc } = broadcast();

// Create consumers before writing
const c1 = bc.push();  // Consumer 1
const c2 = bc.push();  // Consumer 2

// Producer and consumers must run concurrently. Awaited writes
// block when the buffer fills until consumers read.
const producing = (async () => {
  await writer.write('hello');
  await writer.end();
})();

const [r1, r2] = await Promise.all([text(c1), text(c2)]);
console.log(r1); // 'hello'
console.log(r2); // 'hello'
await producing;
```

*CommonJS version:*

```js
const { broadcast, text } = require('node:stream/iter');

async function run() {
  const { writer, broadcast: bc } = broadcast();

  // Create consumers before writing
  const c1 = bc.push();  // Consumer 1
  const c2 = bc.push();  // Consumer 2

  // Producer and consumers must run concurrently. Awaited writes
  // block when the buffer fills until consumers read.
  const producing = (async () => {
    await writer.write('hello');
    await writer.end();
  })();

  const [r1, r2] = await Promise.all([text(c1), text(c2)]);
  console.log(r1); // 'hello'
  console.log(r2); // 'hello'
  await producing;
}

run().catch(console.error);
```

#### `broadcast.cancel([reason])`

- `reason` [`<any>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#Data_types)

Cancel the broadcast. If `reason` is provided, all consumers reject with that
exact reason. If it is omitted, consumers complete normally.

Cancelling also closes the paired writer: afterwards its `canWrite` is `null`
and `write()` rejects with a `TypeError`. This lets a [`Broadcast.from()`](#broadcastfrominput-options)
pump stop pulling from its source.

#### `broadcast.consumerCount`

- [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type)

The number of active consumers.

#### `broadcast.push([...transforms][, options])`

- `...transforms` [`<Function>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Function) | [`<Object>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Object)
- `options` [`<Object>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Object)
  - `signal` [`<AbortSignal>`](26-globals.md#class-abortsignal)
- Returns: [`<AsyncIterable>`](https://tc39.github.io/ecma262/#sec-asynciterable-interface) whose chunks fulfill with [`<Uint8Array>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Uint8Array)[]

Create a new consumer. Optional transforms are applied to this consumer's view
of the data.

#### `broadcast[Symbol.dispose]()`

Alias for `broadcast.cancel()`.

### `Broadcast.from(input[, options])`

Added in: v25.9.0

- `input` [`<AsyncIterable>`](https://tc39.github.io/ecma262/#sec-asynciterable-interface) | [`<Iterable>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Iteration_protocols#the_iterable_protocol) | [`<BroadcastChannel>`](67-worker-threads.md#class-broadcastchannel-extends-eventtarget)
- `options` [`<Object>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Object) Same as `broadcast()`.
- Returns: [`<BroadcastChannel>`](67-worker-threads.md#class-broadcastchannel-extends-eventtarget) | [`<Object>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Object) A `broadcastProtocol` input returns its
  [`<BroadcastChannel>`](67-worker-threads.md#class-broadcastchannel-extends-eventtarget) directly. Other inputs return `{ writer, broadcast }`.

Create a [`<BroadcastChannel>`](67-worker-threads.md#class-broadcastchannel-extends-eventtarget) from an existing source. The source is consumed
automatically and pushed to all subscribers.

### `share(source[, options])`

Added in: v25.9.0

- `source` [`<AsyncIterable>`](https://tc39.github.io/ecma262/#sec-asynciterable-interface) The source to share.
- `options` [`<Object>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Object)
  - `budget` [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) Buffer size in bytes. Must be a positive integer. **Default:** `65536`.
  - `backpressure` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) `'strict'`, `'unbounded'`, `'drop-oldest'`, or
    `'drop-newest'`. **Default:** `'strict'`.
  - `signal` [`<AbortSignal>`](26-globals.md#class-abortsignal)
- Returns: [`<Share>`](32-stream-iter.md#class-share)

Create a pull-model multi-consumer shared stream. Unlike `broadcast()`, the
source is only read when a consumer pulls. Multiple consumers share a single
buffer.

A consumer created with `share.pull()` starts reading at the oldest entry still
in the buffer. Entries are released once every consumer has read them. When
every consumer has detached, the buffered data is kept for consumers that
attach later, and the source is not closed. Call `share.cancel()` (or dispose
the share) to release the source once it is no longer needed.

With `'strict'` backpressure, a consumer that needs to pull from the source
while the buffer is at or above `budget` is rejected with `ERR_OUT_OF_RANGE`
and detached; further reads from that consumer complete with `{ done: true }`.
Detaching keeps a consumer that is not retried (for example, one read with
`for await...of`, which does not call `return()` when a read rejects) from
holding buffered data and blocking the other consumers.

With `'unbounded'`, such a consumer waits until the slowest consumer releases
budget. With `'drop-newest'`, the entry pulled from the source is discarded
and the consumer then waits in the same way, so in both cases a stalled
consumer also stalls the consumers that are ahead of it. Only `'drop-oldest'`
lets consumers that are ahead continue, by discarding the oldest buffered
entries that the slowest consumer has not read yet. A batch pulled from the
source that is larger than `budget` is split into smaller entries first, so
eviction keeps the newest chunks that fit within the budget.

*ES module version:*

```js
import { from, share, text } from 'node:stream/iter';

const shared = share(from('hello'));

const c1 = shared.pull();
const c2 = shared.pull();

// Consume concurrently to avoid deadlock with small buffers.
const [r1, r2] = await Promise.all([text(c1), text(c2)]);
console.log(r1); // 'hello'
console.log(r2); // 'hello'
```

*CommonJS version:*

```js
const { from, share, text } = require('node:stream/iter');

async function run() {
  const shared = share(from('hello'));

  const c1 = shared.pull();
  const c2 = shared.pull();

  // Consume concurrently to avoid deadlock with small buffers.
  const [r1, r2] = await Promise.all([text(c1), text(c2)]);
  console.log(r1); // 'hello'
  console.log(r2); // 'hello'
}

run().catch(console.error);
```

### Class: `Share`

#### Static method: `Share.from(input[, options])`

Added in: v25.9.0

- `input` [`<AsyncIterable>`](https://tc39.github.io/ecma262/#sec-asynciterable-interface) | [`<Shareable>`](32-stream-iter.md#interface-shareable)
- `options` [`<Object>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Object) Same as `share()`.
- Returns: [`<Share>`](32-stream-iter.md#class-share)

Create a [`<Share>`](32-stream-iter.md#class-share) from an existing source.

#### `share.cancel([reason])`

- `reason` [`<any>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#Data_types)

Cancel the share. If `reason` is provided, all consumers reject with that exact
reason. If it is omitted, consumers complete normally.

#### `share.consumerCount`

- [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type)

The number of active consumers.

#### `share.pull([...transforms][, options])`

- `...transforms` [`<Function>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Function) | [`<Object>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Object)
- `options` [`<Object>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Object)
  - `signal` [`<AbortSignal>`](26-globals.md#class-abortsignal)
- Returns: [`<AsyncIterable>`](https://tc39.github.io/ecma262/#sec-asynciterable-interface) whose chunks fulfill with [`<Uint8Array>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Uint8Array)[]

Create a new consumer of the shared source.

#### `share[Symbol.dispose]()`

Alias for `share.cancel()`.

### Interface: `Shareable`

#### `sharable[Symbol.for('Stream.shareProtocol')]`

- [`<Function>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Function) that returns a [`<Share>`](32-stream-iter.md#class-share).

### Interface: `SyncShareable`

#### `sharable[Symbol.for('Stream.shareSyncProtocol')]`

- [`<Function>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Function) that returns a [`<SyncShare>`](32-stream-iter.md#class-syncshare).

### `shareSync(source[, options])`

Added in: v25.9.0

- `source` [`<Iterable>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Iteration_protocols#the_iterable_protocol) The sync source to share.
- `options` [`<Object>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Object)
  - `budget` [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) Must be a positive integer. **Default:** `65536`.
  - `backpressure` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) `'strict'` or `'drop-oldest'`.
    **Default:** `'strict'`.
- Returns: [`<SyncShare>`](32-stream-iter.md#class-syncshare)

Synchronous version of [`share()`](#sharesource-options).

A synchronous consumer cannot wait for the slowest consumer to release budget,
and the slowest consumer cannot advance while another consumer's read is
running. `'unbounded'` and `'drop-newest'` are therefore not supported and
throw `ERR_INVALID_ARG_VALUE`.

### Class: `SyncShare`

#### Static method: `SyncShare.fromSync(input[, options])`

Added in: v25.9.0

- `input` [`<Iterable>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Iteration_protocols#the_iterable_protocol) | [`<SyncShareable>`](32-stream-iter.md#interface-syncshareable)
- `options` [`<Object>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Object)
- Returns: [`<SyncShare>`](32-stream-iter.md#class-syncshare)

#### `share.cancel([reason])`

- `reason` [`<any>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#Data_types)

Cancel the share. If `reason` is provided, all consumers throw that exact
reason. If it is omitted, consumers complete normally.

#### `share.consumerCount`

- [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type)

The number of active consumers.

#### `share.pull([...transforms])`

- `...transforms` [`<Function>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Function) | [`<Object>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Object)
- Returns: [`<Iterable>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Iteration_protocols#the_iterable_protocol) whose chunks return [`<Uint8Array>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Uint8Array)[]

Create a new consumer of the shared source.

#### `share[Symbol.dispose]()`

Alias for `share.cancel()`.

## Compression and decompression transforms

Compression and decompression transforms for use with `pull()`, `pullSync()`,
`pipeTo()`, and `pipeToSync()` are available via the [`node:zlib/iter`](68-zlib.md#iterable-compression)
module. See the [`node:zlib/iter` documentation](68-zlib.md#iterable-compression) for details.

## Classic stream interop

These utility functions bridge between classic
[`stream.Readable`](51-stream-part1.md#class-streamreadable)/[`stream.Writable`](51-stream-part1.md#class-streamwritable) streams and the `stream/iter`
API.

Both `fromReadable()` and `fromWritable()` accept duck-typed objects -- they
do not require the input to extend `stream.Readable` or `stream.Writable`
directly. The minimum contract is described below for each function.

### `fromReadable(readable)`

Added in: v26.1.0

Stability: 1 - Experimental

- `readable` [`<stream.Readable>`](51-stream-part1.md#class-streamreadable) | [`<Object>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Object) A classic Readable stream or a compatible
  object with `read()`, `pipe()`, `destroy()`, `on()`, and `removeListener()`
  methods.
- Returns: [`<AsyncIterable>`](https://tc39.github.io/ecma262/#sec-asynciterable-interface) whose chunks fulfill with [`<Uint8Array>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Uint8Array)[]

Converts a classic Readable stream (or duck-typed equivalent) into a
stream/iter async iterable source that can be passed to [`from()`](#frominput),
[`pull()`](#pullsource-transforms-options), [`text()`](#textsource-options), etc.

If the object implements the [`toAsyncStreamable`](#streamtoasyncstreamable) protocol (as
`stream.Readable` does), that protocol is used. Otherwise, the function
duck-types on `read()`, `pipe()`, `destroy()`, `on()`, and `removeListener()`
(EventEmitter) and wraps the stream with a batched async iterator.

The result is cached per instance -- calling `fromReadable()` twice with the
same stream returns the same iterable.

For object-mode or encoded Readable streams, chunks are automatically
normalized to `Uint8Array`.

*ES module version:*

```js
import { Readable } from 'node:stream';
import { fromReadable, text } from 'node:stream/iter';

const readable = new Readable({
  read() { this.push('hello world'); this.push(null); },
});

const result = await text(fromReadable(readable));
console.log(result); // 'hello world'
```

*CommonJS version:*

```js
const { Readable } = require('node:stream');
const { fromReadable, text } = require('node:stream/iter');

const readable = new Readable({
  read() { this.push('hello world'); this.push(null); },
});

async function run() {
  const result = await text(fromReadable(readable));
  console.log(result); // 'hello world'
}
run();
```

### `fromWritable(writable[, options])`

Added in: v26.1.0

Stability: 1 - Experimental

- `writable` [`<stream.Writable>`](51-stream-part1.md#class-streamwritable) | [`<Object>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Object) A classic Writable stream or a compatible
  object with `write()`, `end()`, `destroy()`, `on()`, and `removeListener()`
  methods.
- `options` [`<Object>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Object)
  - `backpressure` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) Backpressure policy. **Default:** `'strict'`.
    - `'strict'` -- one write may wait while the buffer is full. Further writes
      are rejected until it is accepted or canceled.
    - `'unbounded'` -- writes are queued while the buffer is full. Recommended
      for use with [`pipeTo()`](#pipetosource-transforms-writer-options).
    - `'drop-newest'` -- writes are silently discarded when the buffer is full.
    - `'drop-oldest'` -- **not supported**. Throws `ERR_INVALID_ARG_VALUE`.
- Returns: [`<Object>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Object) A stream/iter Writer adapter.

Creates a stream/iter Writer adapter from a classic Writable stream (or
duck-typed equivalent). The adapter can be passed to [`pipeTo()`](#pipetosource-transforms-writer-options) as a
destination.

Since all writes on a classic Writable are fundamentally asynchronous,
the synchronous Writer methods (`writeSync`, `writevSync`, `endSync`) always
return `false` or `-1`, deferring to the async path. A queued `write()` or
`writev()` can be canceled with its `options.signal` before it reaches the
classic Writable.

If `writer.fail(reason)` receives a non-Error reason, the classic Writable is
destroyed with an `ERR_FALSY_VALUE_REJECTION` or `ERR_OPERATION_FAILED` error.
Its `reason` property contains the original value, which remains the Writer's
stored failure reason.

The result is cached per instance and backpressure policy -- calling
`fromWritable()` twice with the same stream and `backpressure` option returns
the same Writer.

For duck-typed streams that do not expose `writableHighWaterMark`,
`writableLength`, or similar properties, sensible defaults are used.
Object-mode writables (if detectable) are rejected since the Writer
interface is bytes-only.

*ES module version:*

```js
import { Writable } from 'node:stream';
import { from, fromWritable, pipeTo } from 'node:stream/iter';

const writable = new Writable({
  write(chunk, encoding, cb) { console.log(chunk.toString()); cb(); },
});

await pipeTo(from('hello world'),
             fromWritable(writable, { backpressure: 'unbounded' }));
```

*CommonJS version:*

```js
const { Writable } = require('node:stream');
const { from, fromWritable, pipeTo } = require('node:stream/iter');

async function run() {
  const writable = new Writable({
    write(chunk, encoding, cb) { console.log(chunk.toString()); cb(); },
  });

  await pipeTo(from('hello world'),
               fromWritable(writable, { backpressure: 'unbounded' }));
}
run();
```

### `toReadable(source[, options])`

Added in: v26.1.0

Stability: 1 - Experimental

- `source` [`<AsyncIterable>`](https://tc39.github.io/ecma262/#sec-asynciterable-interface) whose chunks must fulfill with [`<Uint8Array>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Uint8Array)[]
  the return value of [`pull()`](#pullsource-transforms-options) or [`from()`](#frominput).
- `options` [`<Object>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Object)
  - `highWaterMark` [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) The internal buffer size in bytes before
    backpressure is applied. **Default:** `65536` (64 KB).
  - `signal` [`<AbortSignal>`](26-globals.md#class-abortsignal) An optional signal to abort the readable.
- Returns: [`<stream.Readable>`](51-stream-part1.md#class-streamreadable)

Creates a byte-mode [`stream.Readable`](51-stream-part1.md#class-streamreadable) from the `source`
(the native batch format used by the stream/iter API). Each `Uint8Array` in a
yielded batch is pushed as a separate chunk into the Readable.

Classic streams cannot represent arbitrary values as emitted errors. A
non-Error reason is wrapped in an `ERR_FALSY_VALUE_REJECTION` or
`ERR_OPERATION_FAILED` error whose `reason` property contains the original
value.

*ES module version:*

```js
import { createWriteStream } from 'node:fs';
import { from, pull, toReadable } from 'node:stream/iter';
import { compressGzip } from 'node:zlib/iter';

const source = pull(from('hello world'), compressGzip());
const readable = toReadable(source);

readable.pipe(createWriteStream('output.gz'));
```

*CommonJS version:*

```js
const { createWriteStream } = require('node:fs');
const { from, pull, toReadable } = require('node:stream/iter');
const { compressGzip } = require('node:zlib/iter');

const source = pull(from('hello world'), compressGzip());
const readable = toReadable(source);

readable.pipe(createWriteStream('output.gz'));
```

### `toReadableSync(source[, options])`

Added in: v26.1.0

Stability: 1 - Experimental

- `source` [`<Iterable>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Iteration_protocols#the_iterable_protocol) whose chunks must return [`<Uint8Array>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Uint8Array)[], such as the
  return value of [`pullSync()`](#pullsyncsource-transforms) or [`fromSync()`](#fromsyncinput).
- `options` [`<Object>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Object)
  - `highWaterMark` [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) The internal buffer size in bytes before
    backpressure is applied. **Default:** `65536` (64 KB).
- Returns: [`<stream.Readable>`](51-stream-part1.md#class-streamreadable)

Creates a byte-mode [`stream.Readable`](51-stream-part1.md#class-streamreadable) from the `source`.
The `_read()` method pulls from the iterator
synchronously, so data is available immediately via `readable.read()`.

*ES module version:*

```js
import { fromSync, toReadableSync } from 'node:stream/iter';

const source = fromSync('hello world');
const readable = toReadableSync(source);

console.log(readable.read().toString()); // 'hello world'
```

*CommonJS version:*

```js
const { fromSync, toReadableSync } = require('node:stream/iter');

const source = fromSync('hello world');
const readable = toReadableSync(source);

console.log(readable.read().toString()); // 'hello world'
```

### `toWritable(writer)`

Added in: v26.1.0

Stability: 1 - Experimental

- `writer` [`<Object>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Object) A stream/iter Writer. Only the `write()` method is
  required; `end()`, `fail()`, `writeSync()`, `writevSync()`, `endSync()`,
  and `writev()` are optional.
- Returns: [`<stream.Writable>`](51-stream-part1.md#class-streamwritable)

Creates a classic [`stream.Writable`](51-stream-part1.md#class-streamwritable) backed by a stream/iter Writer.

Each `_write()` / `_writev()` call attempts the Writer's synchronous method
first (`writeSync` / `writevSync`), falling back to the async method if the
sync path returns `false`. Similarly, `_final()` tries `endSync()`
before `end()`. When the sync path succeeds, the callback is deferred via
`queueMicrotask` to preserve the async resolution contract.

Classic stream callbacks cannot represent arbitrary values as errors. A
non-Error reason is wrapped in an `ERR_FALSY_VALUE_REJECTION` or
`ERR_OPERATION_FAILED` error before it is passed to the callback. The error's
`reason` property contains the original value.

Destroying the Writable before successful completion calls `writer.fail()`.
If `fail()` is unavailable, `Symbol.dispose` or `Symbol.asyncDispose` is used
when implemented by the Writer.

The Writable uses the default classic stream `highWaterMark`. Classic stream
backpressure bounds writes waiting to reach the underlying Writer, while the
Writer controls completion of the active `_write()` or `_writev()` operation.

*ES module version:*

```js
import { push, toWritable } from 'node:stream/iter';

const { writer, readable } = push();
const writable = toWritable(writer);

writable.write('hello');
writable.end();
```

*CommonJS version:*

```js
const { push, toWritable } = require('node:stream/iter');

const { writer, readable } = push();
const writable = toWritable(writer);

writable.write('hello');
writable.end();
```

## Protocol symbols

These well-known symbols allow third-party objects to participate in the
streaming protocol without importing from `node:stream/iter` directly.

### `Stream.broadcastProtocol`

- Value: `Symbol.for('Stream.broadcastProtocol')`

The value must be a function. When called by `Broadcast.from()`, it receives
the options passed to `Broadcast.from()` and must return an object conforming
to the [`<BroadcastChannel>`](67-worker-threads.md#class-broadcastchannel-extends-eventtarget) interface. The implementation is fully custom -- it can
manage consumers, buffering, and backpressure however it wants.

*ES module version:*

```js
import {
  broadcast as createBroadcast,
  Broadcast,
  text,
} from 'node:stream/iter';

// This example defers to the built-in Broadcast, but a custom
// implementation could use any mechanism.
class MessageBus {
  #broadcast;
  #writer;

  constructor() {
    const { writer, broadcast } = createBroadcast();
    this.#writer = writer;
    this.#broadcast = broadcast;
  }

  [Symbol.for('Stream.broadcastProtocol')](options) {
    return this.#broadcast;
  }

  send(data) {
    this.#writer.write(new TextEncoder().encode(data));
  }

  close() {
    this.#writer.end();
  }
}

const bus = new MessageBus();
const broadcast = Broadcast.from(bus);
const consumer = broadcast.push();
bus.send('hello');
bus.close();
console.log(await text(consumer)); // 'hello'
```

*CommonJS version:*

```js
const {
  broadcast: createBroadcast,
  Broadcast,
  text,
} = require('node:stream/iter');

// This example defers to the built-in Broadcast, but a custom
// implementation could use any mechanism.
class MessageBus {
  #broadcast;
  #writer;

  constructor() {
    const { writer, broadcast } = createBroadcast();
    this.#writer = writer;
    this.#broadcast = broadcast;
  }

  [Symbol.for('Stream.broadcastProtocol')](options) {
    return this.#broadcast;
  }

  send(data) {
    this.#writer.write(new TextEncoder().encode(data));
  }

  close() {
    this.#writer.end();
  }
}

const bus = new MessageBus();
const broadcast = Broadcast.from(bus);
const consumer = broadcast.push();
bus.send('hello');
bus.close();
text(consumer).then(console.log); // 'hello'
```

### `Stream.drainableProtocol`

- Value: `Symbol.for('Stream.drainableProtocol')`

Implement to make a writer compatible with `ondrain()`. The method should
return `null` if no backpressure, or a promise that fulfills with a truthy value
when backpressure clears.

*ES module version:*

```js
import { ondrain } from 'node:stream/iter';

class CustomWriter {
  #queue = [];
  #drain = null;
  #closed = false;
  [Symbol.for('Stream.drainableProtocol')]() {
    if (this.#closed) return null;
    if (this.#queue.length < 3) return Promise.resolve(true);
    this.#drain ??= Promise.withResolvers();
    return this.#drain.promise;
  }
  write(chunk) {
    this.#queue.push(chunk);
  }
  flush() {
    this.#queue.length = 0;
    this.#drain?.resolve(true);
    this.#drain = null;
  }
  close() {
    this.#closed = true;
  }
}
const writer = new CustomWriter();
const ready = ondrain(writer);
console.log(ready); // Promise { true } -- no backpressure
```

*CommonJS version:*

```js
const { ondrain } = require('node:stream/iter');

class CustomWriter {
  #queue = [];
  #drain = null;
  #closed = false;

  [Symbol.for('Stream.drainableProtocol')]() {
    if (this.#closed) return null;
    if (this.#queue.length < 3) return Promise.resolve(true);
    this.#drain ??= Promise.withResolvers();
    return this.#drain.promise;
  }

  write(chunk) {
    this.#queue.push(chunk);
  }

  flush() {
    this.#queue.length = 0;
    this.#drain?.resolve(true);
    this.#drain = null;
  }

  close() {
    this.#closed = true;
  }
}

const writer = new CustomWriter();
const ready = ondrain(writer);
console.log(ready); // Promise { true } -- no backpressure
```

### `Stream.shareProtocol`

- Value: `Symbol.for('Stream.shareProtocol')`

The value must be a function. When called by `Share.from()`, it receives the
options passed to `Share.from()` and must return an object conforming to the
[`<Share>`](32-stream-iter.md#class-share) interface. The implementation is fully custom -- it can manage the shared
source, consumers, buffering, and backpressure however it wants.

*ES module version:*

```js
import { share, Share, text } from 'node:stream/iter';

// This example defers to the built-in share(), but a custom
// implementation could use any mechanism.
class DataPool {
  #share;

  constructor(source) {
    this.#share = share(source);
  }

  [Symbol.for('Stream.shareProtocol')](options) {
    return this.#share;
  }
}

const pool = new DataPool(
  (async function* () {
    yield 'hello';
  })(),
);

const shared = Share.from(pool);
const consumer = shared.pull();
console.log(await text(consumer)); // 'hello'
```

*CommonJS version:*

```js
const { share, Share, text } = require('node:stream/iter');

// This example defers to the built-in share(), but a custom
// implementation could use any mechanism.
class DataPool {
  #share;

  constructor(source) {
    this.#share = share(source);
  }

  [Symbol.for('Stream.shareProtocol')](options) {
    return this.#share;
  }
}

const pool = new DataPool(
  (async function* () {
    yield 'hello';
  })(),
);

const shared = Share.from(pool);
const consumer = shared.pull();
text(consumer).then(console.log); // 'hello'
```

### `Stream.shareSyncProtocol`

- Value: `Symbol.for('Stream.shareSyncProtocol')`

The value must be a function. When called by `SyncShare.fromSync()`, it receives
the options passed to `SyncShare.fromSync()` and must return an object conforming
to the [`<SyncShare>`](32-stream-iter.md#class-syncshare) interface. The implementation is fully custom -- it can manage
the shared source, consumers, and buffering however it wants.

*ES module version:*

```js
import { shareSync, SyncShare, textSync } from 'node:stream/iter';

// This example defers to the built-in shareSync(), but a custom
// implementation could use any mechanism.
class SyncDataPool {
  #share;

  constructor(source) {
    this.#share = shareSync(source);
  }

  [Symbol.for('Stream.shareSyncProtocol')](options) {
    return this.#share;
  }
}

const encoder = new TextEncoder();
const pool = new SyncDataPool(
  function* () {
    yield [encoder.encode('hello')];
  }(),
);

const shared = SyncShare.fromSync(pool);
const consumer = shared.pull();
console.log(textSync(consumer)); // 'hello'
```

*CommonJS version:*

```js
const { shareSync, SyncShare, textSync } = require('node:stream/iter');

// This example defers to the built-in shareSync(), but a custom
// implementation could use any mechanism.
class SyncDataPool {
  #share;

  constructor(source) {
    this.#share = shareSync(source);
  }

  [Symbol.for('Stream.shareSyncProtocol')](options) {
    return this.#share;
  }
}

const encoder = new TextEncoder();
const pool = new SyncDataPool(
  function* () {
    yield [encoder.encode('hello')];
  }(),
);

const shared = SyncShare.fromSync(pool);
const consumer = shared.pull();
console.log(textSync(consumer)); // 'hello'
```

### `Stream.toAsyncStreamable`

- Value: `Symbol.for('Stream.toAsyncStreamable')`

The value must be a function that converts the object into a streamable value.
When the object is passed to `from()`, this method is called to produce the
actual data. It may return any value that resolves to a string, `Uint8Array`,
`AsyncIterable`, `Iterable`, or another streamable object.

*ES module version:*

```js
import { from, text } from 'node:stream/iter';

class Greeting {
  #name;

  constructor(name) {
    this.#name = name;
  }

  [Symbol.for('Stream.toAsyncStreamable')]() {
    return `hello ${this.#name}`;
  }
}

const stream = from(new Greeting('world'));
console.log(await text(stream)); // 'hello world'
```

*CommonJS version:*

```js
const { from, text } = require('node:stream/iter');

class Greeting {
  #name;

  constructor(name) {
    this.#name = name;
  }

  [Symbol.for('Stream.toAsyncStreamable')]() {
    return `hello ${this.#name}`;
  }
}

const stream = from(new Greeting('world'));
text(stream).then(console.log); // 'hello world'
```

### `Stream.toStreamable`

- Value: `Symbol.for('Stream.toStreamable')`

The value must be a function that synchronously converts the object into a
streamable value. When the object is passed to `fromSync()`, this method is
called to produce the actual data. It must synchronously return a streamable
value: a string, `Uint8Array`, or `Iterable`.

*ES module version:*

```js
import { fromSync, textSync } from 'node:stream/iter';

class Greeting {
  #name;

  constructor(name) {
    this.#name = name;
  }

  [Symbol.for('Stream.toStreamable')]() {
    return `hello ${this.#name}`;
  }
}

const stream = fromSync(new Greeting('world'));
console.log(textSync(stream)); // 'hello world'
```

*CommonJS version:*

```js
const { fromSync, textSync } = require('node:stream/iter');

class Greeting {
  #name;

  constructor(name) {
    this.#name = name;
  }

  [Symbol.for('Stream.toStreamable')]() {
    return `hello ${this.#name}`;
  }
}

const stream = fromSync(new Greeting('world'));
console.log(textSync(stream)); // 'hello world'
```

[Contents](../README.md) | [Previous: Internationalization](31-intl.md) | [Next: Modules: CommonJS modules](33-modules.md)
