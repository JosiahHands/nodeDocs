[Contents](../README.md) | [Previous: Web Crypto API](65-webcrypto.md) | [Next: Worker threads](67-worker-threads.md)

Source: [https://nodejs.org/docs/latest/api/webstreams.html](https://nodejs.org/docs/latest/api/webstreams.html)

# Web Streams API

Added in: v16.5.0

**History**

| Version | Changes |
| --- | --- |
| v21.0.0 | No longer experimental. |
| v18.0.0 | Use of this API no longer emit a runtime warning. |

[Stability: 2](01-documentation.md#stability-index) - Stable

An implementation of the [WHATWG Streams Standard](https://streams.spec.whatwg.org/).

## Overview

The [WHATWG Streams Standard](https://streams.spec.whatwg.org/) (or "web streams") defines an API for handling
streaming data. It is similar to the Node.js [Streams](51-stream.md) API but emerged later
and has become the "standard" API for streaming data across many JavaScript
environments.

There are three primary types of objects:

- `ReadableStream` - Represents a source of streaming data.
- `WritableStream` - Represents a destination for streaming data.
- `TransformStream` - Represents an algorithm for transforming streaming data.

### Example `ReadableStream`

This example creates a simple `ReadableStream` that pushes the current
`performance.now()` timestamp once every second forever. An async iterable
is used to read the data from the stream.

*ES module version:*

```js
import {
  ReadableStream,
} from 'node:stream/web';

import {
  setInterval as every,
} from 'node:timers/promises';

import {
  performance,
} from 'node:perf_hooks';

const SECOND = 1000;

const stream = new ReadableStream({
  async start(controller) {
    for await (const _ of every(SECOND))
      controller.enqueue(performance.now());
  },
});

for await (const value of stream)
  console.log(value);
```

*CommonJS version:*

```js
const {
  ReadableStream,
} = require('node:stream/web');

const {
  setInterval: every,
} = require('node:timers/promises');

const {
  performance,
} = require('node:perf_hooks');

const SECOND = 1000;

const stream = new ReadableStream({
  async start(controller) {
    for await (const _ of every(SECOND))
      controller.enqueue(performance.now());
  },
});

(async () => {
  for await (const value of stream)
    console.log(value);
})();
```

### Node.js streams interoperability

Node.js streams can be converted to web streams and vice versa via the `toWeb` and `fromWeb` methods present on [`stream.Readable`](51-stream.md#class-streamreadable), [`stream.Writable`](51-stream.md#class-streamwritable) and [`stream.Duplex`](51-stream.md#class-streamduplex) objects.

For more details refer to the relevant documentation:

- [`stream.Readable.toWeb`](51-stream.md#streamreadabletowebstreamreadable-options)
- [`stream.Readable.fromWeb`](51-stream.md#streamreadablefromwebreadablestream-options)
- [`stream.Writable.toWeb`](51-stream.md#streamwritabletowebstreamwritable)
- [`stream.Writable.fromWeb`](51-stream.md#streamwritablefromwebwritablestream-options)
- [`stream.Duplex.toWeb`](51-stream.md#streamduplextowebstreamduplex-options)
- [`stream.Duplex.fromWeb`](51-stream.md#streamduplexfromwebpair-options)

## API

### `ReadableStreamTee(stream[, cloneForBranch2])`

Added in: v26.5.0

Stability: 1 - Experimental

- `stream` [`<ReadableStream>`](66-webstreams.md#class-readablestream)
- `cloneForBranch2` [`<boolean>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#boolean_type) When `true`, chunks enqueued into the second
  branch are cloned from chunks enqueued into the first branch. **Default:**
  `false`.
- Returns: [`<ReadableStream>`](66-webstreams.md#class-readablestream)[] Two [`<ReadableStream>`](66-webstreams.md#class-readablestream) branches.

Runs the WHATWG `ReadableStreamTee` abstract operation on `stream`.

This differs from `readableStream.tee()` only when `cloneForBranch2` is
`true`. The `tee()` method always passes `false`, while other web platform
specifications, such as Fetch body cloning, pass `true` so that the second
branch receives cloned chunks and consumption of one branch cannot mutate chunks
seen by the other.

### Class: `ReadableStream`

Added in: v16.5.0

**History**

| Version | Changes |
| --- | --- |
| v18.0.0 | This class is now exposed on the global object. |

#### `new ReadableStream([underlyingSource [, strategy]])`

Added in: v16.5.0

- `underlyingSource` [`<Object>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Object)
  - `start` [`<Function>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Function) A user-defined function that is invoked immediately when
    the `ReadableStream` is created.
    - `controller` [`<ReadableStreamDefaultController>`](66-webstreams.md#class-readablestreamdefaultcontroller) | [`<ReadableByteStreamController>`](66-webstreams.md#class-readablebytestreamcontroller)
    - Returns: `undefined` or a promise fulfilled with `undefined`.
  - `pull` [`<Function>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Function) A user-defined function that is called repeatedly when the `ReadableStream` internal queue is not full. The operation may be sync or
    async. If async, the function will not be called again until the previously
    returned promise is fulfilled.
    - `controller` [`<ReadableStreamDefaultController>`](66-webstreams.md#class-readablestreamdefaultcontroller) | [`<ReadableByteStreamController>`](66-webstreams.md#class-readablebytestreamcontroller)
    - Returns: A promise fulfilled with `undefined`.
  - `cancel` [`<Function>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Function) A user-defined function that is called when the `ReadableStream` is canceled.
    - `reason` [`<any>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#Data_types)
    - Returns: A promise fulfilled with `undefined`.
  - `type` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) Must be `'bytes'` or `undefined`.
  - `autoAllocateChunkSize` [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) Used only when `type` is equal to
    `'bytes'`. When set to a non-zero value a view buffer is automatically
    allocated to `ReadableByteStreamController.byobRequest`. When not set
    one must use stream's internal queues to transfer data via the default
    reader `ReadableStreamDefaultReader`.
- `strategy` [`<Object>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Object)
  - `highWaterMark` [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) The maximum internal queue size before backpressure
    is applied.
  - `size` [`<Function>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Function) A user-defined function used to identify the size of each
    chunk of data.
    - `chunk` [`<any>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#Data_types)
    - Returns: [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type)

#### `readableStream.locked`

Added in: v16.5.0

- Type: [`<boolean>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#boolean_type) Set to `true` if there is an active reader for this
  [`<ReadableStream>`](66-webstreams.md#class-readablestream).

The `readableStream.locked` property is `false` by default, and is
switched to `true` while there is an active reader consuming the
stream's data.

#### `readableStream.cancel([reason])`

Added in: v16.5.0

- `reason` [`<any>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#Data_types)
- Returns: A promise fulfilled with `undefined` once cancelation has
  been completed.

#### `readableStream.getReader([options])`

Added in: v16.5.0

- `options` [`<Object>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Object)
  - `mode` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) `'byob'` or `undefined`
- Returns: [`<ReadableStreamDefaultReader>`](66-webstreams.md#class-readablestreamdefaultreader) | [`<ReadableStreamBYOBReader>`](66-webstreams.md#class-readablestreambyobreader)

*ES module version:*

```js
import { ReadableStream } from 'node:stream/web';

const stream = new ReadableStream();

const reader = stream.getReader();

console.log(await reader.read());
```

*CommonJS version:*

```js
const { ReadableStream } = require('node:stream/web');

const stream = new ReadableStream();

const reader = stream.getReader();

reader.read().then(console.log);
```

Causes the `readableStream.locked` to be `true`.

#### `readableStream.pipeThrough(transform[, options])`

Added in: v16.5.0

- `transform` [`<Object>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Object)
  - `readable` [`<ReadableStream>`](66-webstreams.md#class-readablestream) The `ReadableStream` to which
    `transform.writable` will push the potentially modified data
    it receives from this `ReadableStream`.
  - `writable` [`<WritableStream>`](66-webstreams.md#class-writablestream) The `WritableStream` to which this
    `ReadableStream`'s data will be written.
- `options` [`<Object>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Object)
  - `preventAbort` [`<boolean>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#boolean_type) When `true`, errors in this `ReadableStream`
    will not cause `transform.writable` to be aborted.
  - `preventCancel` [`<boolean>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#boolean_type) When `true`, errors in the destination
    `transform.writable` do not cause this `ReadableStream` to be
    canceled.
  - `preventClose` [`<boolean>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#boolean_type) When `true`, closing this `ReadableStream`
    does not cause `transform.writable` to be closed.
  - `signal` [`<AbortSignal>`](26-globals.md#class-abortsignal) Allows the transfer of data to be canceled
    using an [`<AbortController>`](26-globals.md#class-abortcontroller).
- Returns: [`<ReadableStream>`](66-webstreams.md#class-readablestream) From `transform.readable`.

Connects this [`<ReadableStream>`](66-webstreams.md#class-readablestream) to the pair of [`<ReadableStream>`](66-webstreams.md#class-readablestream) and
[`<WritableStream>`](66-webstreams.md#class-writablestream) provided in the `transform` argument such that the
data from this [`<ReadableStream>`](66-webstreams.md#class-readablestream) is written in to `transform.writable`,
possibly transformed, then pushed to `transform.readable`. Once the
pipeline is configured, `transform.readable` is returned.

Causes the `readableStream.locked` to be `true` while the pipe operation
is active.

*ES module version:*

```js
import {
  ReadableStream,
  TransformStream,
} from 'node:stream/web';

const stream = new ReadableStream({
  start(controller) {
    controller.enqueue('a');
  },
});

const transform = new TransformStream({
  transform(chunk, controller) {
    controller.enqueue(chunk.toUpperCase());
  },
});

const transformedStream = stream.pipeThrough(transform);

for await (const chunk of transformedStream)
  console.log(chunk);
  // Prints: A
```

*CommonJS version:*

```js
const {
  ReadableStream,
  TransformStream,
} = require('node:stream/web');

const stream = new ReadableStream({
  start(controller) {
    controller.enqueue('a');
  },
});

const transform = new TransformStream({
  transform(chunk, controller) {
    controller.enqueue(chunk.toUpperCase());
  },
});

const transformedStream = stream.pipeThrough(transform);

(async () => {
  for await (const chunk of transformedStream)
    console.log(chunk);
    // Prints: A
})();
```

#### `readableStream.pipeTo(destination[, options])`

Added in: v16.5.0

- `destination` [`<WritableStream>`](66-webstreams.md#class-writablestream) A [`<WritableStream>`](66-webstreams.md#class-writablestream) to which this `ReadableStream`'s data will be written.
- `options` [`<Object>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Object)
  - `preventAbort` [`<boolean>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#boolean_type) When `true`, errors in this `ReadableStream`
    will not cause `destination` to be aborted.
  - `preventCancel` [`<boolean>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#boolean_type) When `true`, errors in the `destination`
    will not cause this `ReadableStream` to be canceled.
  - `preventClose` [`<boolean>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#boolean_type) When `true`, closing this `ReadableStream`
    does not cause `destination` to be closed.
  - `signal` [`<AbortSignal>`](26-globals.md#class-abortsignal) Allows the transfer of data to be canceled
    using an [`<AbortController>`](26-globals.md#class-abortcontroller).
- Returns: A promise fulfilled with `undefined`

Causes the `readableStream.locked` to be `true` while the pipe operation
is active.

#### `readableStream.tee()`

Added in: v16.5.0

**History**

| Version | Changes |
| --- | --- |
| v18.10.0, v16.18.0 | Support teeing a readable byte stream. |

- Returns: [`<ReadableStream>`](66-webstreams.md#class-readablestream)[]

Returns a pair of new [`<ReadableStream>`](66-webstreams.md#class-readablestream) instances to which this `ReadableStream`'s data will be forwarded. Each will receive the
same data.

Causes the `readableStream.locked` to be `true`.

#### `readableStream.values([options])`

Added in: v16.5.0

- `options` [`<Object>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Object)
  - `preventCancel` [`<boolean>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#boolean_type) When `true`, prevents the [`<ReadableStream>`](66-webstreams.md#class-readablestream)
    from being closed when the async iterator abruptly terminates. **Default**: `false`.

Creates and returns an async iterator usable for consuming this
`ReadableStream`'s data.

Causes the `readableStream.locked` to be `true` while the async iterator
is active.

```js
import { Buffer } from 'node:buffer';

const stream = new ReadableStream(getSomeSource());

for await (const chunk of stream.values({ preventCancel: true }))
  console.log(Buffer.from(chunk).toString());
```

#### Async Iteration

The [`<ReadableStream>`](66-webstreams.md#class-readablestream) object supports the async iterator protocol using `for await` syntax.

```js
import { Buffer } from 'node:buffer';

const stream = new ReadableStream(getSomeSource());

for await (const chunk of stream)
  console.log(Buffer.from(chunk).toString());
```

The async iterator will consume the [`<ReadableStream>`](66-webstreams.md#class-readablestream) until it terminates.

By default, if the async iterator exits early (via either a `break`,
`return`, or a `throw`), the [`<ReadableStream>`](66-webstreams.md#class-readablestream) will be closed. To prevent
automatic closing of the [`<ReadableStream>`](66-webstreams.md#class-readablestream), use the `readableStream.values()`
method to acquire the async iterator and set the `preventCancel` option to
`true`.

The [`<ReadableStream>`](66-webstreams.md#class-readablestream) must not be locked (that is, it must not have an existing
active reader). During the async iteration, the [`<ReadableStream>`](66-webstreams.md#class-readablestream) will be locked.

#### Transferring with `postMessage()`

A [`<ReadableStream>`](66-webstreams.md#class-readablestream) instance can be transferred using a [`<MessagePort>`](67-worker-threads.md#class-messageport).

```js
const stream = new ReadableStream(getReadableSourceSomehow());

const { port1, port2 } = new MessageChannel();

port1.onmessage = ({ data }) => {
  data.getReader().read().then((chunk) => {
    console.log(chunk);
  });
};

port2.postMessage(stream, [stream]);
```

### `ReadableStream.from(iterable)`

Added in: v20.6.0

- `iterable` [`<Iterable>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Iteration_protocols#the_iterable_protocol) Object implementing the `Symbol.asyncIterator` or
  `Symbol.iterator` iterable protocol.

A utility method that creates a new [`<ReadableStream>`](66-webstreams.md#class-readablestream) from an iterable.

*ES module version:*

```js
import { ReadableStream } from 'node:stream/web';

async function* asyncIterableGenerator() {
  yield 'a';
  yield 'b';
  yield 'c';
}

const stream = ReadableStream.from(asyncIterableGenerator());

for await (const chunk of stream)
  console.log(chunk); // Prints: 'a', 'b', 'c'
```

*CommonJS version:*

```js
const { ReadableStream } = require('node:stream/web');

async function* asyncIterableGenerator() {
  yield 'a';
  yield 'b';
  yield 'c';
}

(async () => {
  const stream = ReadableStream.from(asyncIterableGenerator());

  for await (const chunk of stream)
    console.log(chunk); // Prints: 'a', 'b', 'c'
})();
```

To pipe the resulting [`<ReadableStream>`](66-webstreams.md#class-readablestream) into a [`<WritableStream>`](66-webstreams.md#class-writablestream) the [`<Iterable>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Iteration_protocols#the_iterable_protocol)
should yield a sequence of [`<Buffer>`](07-buffer.md#class-buffer), [`<TypedArray>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/TypedArray), or [`<DataView>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/DataView) objects.

*ES module version:*

```js
import { ReadableStream } from 'node:stream/web';
import { Buffer } from 'node:buffer';

async function* asyncIterableGenerator() {
  yield Buffer.from('a');
  yield Buffer.from('b');
  yield Buffer.from('c');
}

const stream = ReadableStream.from(asyncIterableGenerator());

await stream.pipeTo(createWritableStreamSomehow());
```

*CommonJS version:*

```js
const { ReadableStream } = require('node:stream/web');
const { Buffer } = require('node:buffer');

async function* asyncIterableGenerator() {
  yield Buffer.from('a');
  yield Buffer.from('b');
  yield Buffer.from('c');
}

const stream = ReadableStream.from(asyncIterableGenerator());

(async () => {
  await stream.pipeTo(createWritableStreamSomehow());
})();
```

### Class: `ReadableStreamDefaultReader`

Added in: v16.5.0

**History**

| Version | Changes |
| --- | --- |
| v18.0.0 | This class is now exposed on the global object. |

By default, calling `readableStream.getReader()` with no arguments
will return an instance of `ReadableStreamDefaultReader`. The default
reader treats the chunks of data passed through the stream as opaque
values, which allows the [`<ReadableStream>`](66-webstreams.md#class-readablestream) to work with generally any
JavaScript value.

#### `new ReadableStreamDefaultReader(stream)`

Added in: v16.5.0

- `stream` [`<ReadableStream>`](66-webstreams.md#class-readablestream)

Creates a new [`<ReadableStreamDefaultReader>`](66-webstreams.md#class-readablestreamdefaultreader) that is locked to the
given [`<ReadableStream>`](66-webstreams.md#class-readablestream).

#### `readableStreamDefaultReader.cancel([reason])`

Added in: v16.5.0

- `reason` [`<any>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#Data_types)
- Returns: A promise fulfilled with `undefined`.

Cancels the [`<ReadableStream>`](66-webstreams.md#class-readablestream) and returns a promise that is fulfilled
when the underlying stream has been canceled.

#### `readableStreamDefaultReader.closed`

Added in: v16.5.0

- Type: [`<Promise>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Promise) Fulfilled with `undefined` when the associated
  [`<ReadableStream>`](66-webstreams.md#class-readablestream) is closed or rejected if the stream errors or the reader's
  lock is released before the stream finishes closing.

#### `readableStreamDefaultReader.read()`

Added in: v16.5.0

- Returns: A promise fulfilled with an object:
  - `value` [`<any>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#Data_types)
  - `done` [`<boolean>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#boolean_type)

Requests the next chunk of data from the underlying [`<ReadableStream>`](66-webstreams.md#class-readablestream)
and returns a promise that is fulfilled with the data once it is
available.

#### `readableStreamDefaultReader.releaseLock()`

Added in: v16.5.0

Releases this reader's lock on the underlying [`<ReadableStream>`](66-webstreams.md#class-readablestream).

### Class: `ReadableStreamBYOBReader`

Added in: v16.5.0

**History**

| Version | Changes |
| --- | --- |
| v18.0.0 | This class is now exposed on the global object. |

The `ReadableStreamBYOBReader` is an alternative consumer for
byte-oriented [`<ReadableStream>`](66-webstreams.md#class-readablestream)s (those that are created with `underlyingSource.type` set equal to `'bytes'` when the
`ReadableStream` was created).

The `BYOB` is short for "bring your own buffer". This is a
pattern that allows for more efficient reading of byte-oriented
data that avoids extraneous copying.

```js
import {
  open,
} from 'node:fs/promises';

import {
  ReadableStream,
} from 'node:stream/web';

import { Buffer } from 'node:buffer';

class Source {
  type = 'bytes';
  autoAllocateChunkSize = 1024;

  async start(controller) {
    this.file = await open(new URL(import.meta.url));
    this.controller = controller;
  }

  async pull(controller) {
    const view = controller.byobRequest?.view;
    const {
      bytesRead,
    } = await this.file.read({
      buffer: view,
      offset: view.byteOffset,
      length: view.byteLength,
    });

    if (bytesRead === 0) {
      await this.file.close();
      this.controller.close();
    }
    controller.byobRequest.respond(bytesRead);
  }
}

const stream = new ReadableStream(new Source());

async function read(stream) {
  const reader = stream.getReader({ mode: 'byob' });

  const chunks = [];
  let result;
  do {
    result = await reader.read(Buffer.alloc(100));
    if (result.value !== undefined)
      chunks.push(Buffer.from(result.value));
  } while (!result.done);

  return Buffer.concat(chunks);
}

const data = await read(stream);
console.log(Buffer.from(data).toString());
```

#### `new ReadableStreamBYOBReader(stream)`

Added in: v16.5.0

- `stream` [`<ReadableStream>`](66-webstreams.md#class-readablestream)

Creates a new `ReadableStreamBYOBReader` that is locked to the
given [`<ReadableStream>`](66-webstreams.md#class-readablestream).

#### `readableStreamBYOBReader.cancel([reason])`

Added in: v16.5.0

- `reason` [`<any>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#Data_types)
- Returns: A promise fulfilled with `undefined`.

Cancels the [`<ReadableStream>`](66-webstreams.md#class-readablestream) and returns a promise that is fulfilled
when the underlying stream has been canceled.

#### `readableStreamBYOBReader.closed`

Added in: v16.5.0

- Type: [`<Promise>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Promise) Fulfilled with `undefined` when the associated
  [`<ReadableStream>`](66-webstreams.md#class-readablestream) is closed or rejected if the stream errors or the reader's
  lock is released before the stream finishes closing.

#### `readableStreamBYOBReader.read(view[, options])`

Added in: v16.5.0

**History**

| Version | Changes |
| --- | --- |
| v21.7.0, v20.17.0 | Added `min` option. |

- `view` [`<Buffer>`](07-buffer.md#class-buffer) | [`<TypedArray>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/TypedArray) | [`<DataView>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/DataView)
- `options` [`<Object>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Object)
  - `min` [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) When set, the returned promise will only be
    fulfilled as soon as `min` number of elements are available.
    When not set, the promise fulfills when at least one element
    is available.
- Returns: A promise fulfilled with an object:
  - `value` [`<TypedArray>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/TypedArray) | [`<DataView>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/DataView)
  - `done` [`<boolean>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#boolean_type)

Requests the next chunk of data from the underlying [`<ReadableStream>`](66-webstreams.md#class-readablestream)
and returns a promise that is fulfilled with the data once it is
available.

Do not pass a pooled [`<Buffer>`](07-buffer.md#class-buffer) object instance in to this method.
Pooled `Buffer` objects are created using `Buffer.allocUnsafe()`,
or `Buffer.from()`, or are often returned by various `node:fs` module
callbacks. These types of `Buffer`s use a shared underlying
[`<ArrayBuffer>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/ArrayBuffer) object that contains all of the data from all of
the pooled `Buffer` instances. When a `Buffer`, [`<TypedArray>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/TypedArray),
or [`<DataView>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/DataView) is passed in to `readableStreamBYOBReader.read()`,
the view's underlying `ArrayBuffer` is *detached*, invalidating
all existing views that may exist on that `ArrayBuffer`. This
can have disastrous consequences for your application.

#### `readableStreamBYOBReader.releaseLock()`

Added in: v16.5.0

Releases this reader's lock on the underlying [`<ReadableStream>`](66-webstreams.md#class-readablestream).

### Class: `ReadableStreamDefaultController`

Added in: v16.5.0

Every [`<ReadableStream>`](66-webstreams.md#class-readablestream) has a controller that is responsible for
the internal state and management of the stream's queue. The `ReadableStreamDefaultController` is the default controller
implementation for `ReadableStream`s that are not byte-oriented.

#### `readableStreamDefaultController.close()`

Added in: v16.5.0

Closes the [`<ReadableStream>`](66-webstreams.md#class-readablestream) to which this controller is associated.

#### `readableStreamDefaultController.desiredSize`

Added in: v16.5.0

- Type: [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type)

Returns the amount of data remaining to fill the [`<ReadableStream>`](66-webstreams.md#class-readablestream)'s
queue.

#### `readableStreamDefaultController.enqueue([chunk])`

Added in: v16.5.0

- `chunk` [`<any>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#Data_types)

Appends a new chunk of data to the [`<ReadableStream>`](66-webstreams.md#class-readablestream)'s queue.

#### `readableStreamDefaultController.error([error])`

Added in: v16.5.0

- `error` [`<any>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#Data_types)

Signals an error that causes the [`<ReadableStream>`](66-webstreams.md#class-readablestream) to error and close.

### Class: `ReadableByteStreamController`

Added in: v16.5.0

**History**

| Version | Changes |
| --- | --- |
| v18.10.0 | Support handling a BYOB pull request from a released reader. |

Every [`<ReadableStream>`](66-webstreams.md#class-readablestream) has a controller that is responsible for
the internal state and management of the stream's queue. The `ReadableByteStreamController` is for byte-oriented `ReadableStream`s.

#### `readableByteStreamController.byobRequest`

Added in: v16.5.0

- Type: [`<ReadableStreamBYOBRequest>`](66-webstreams.md#class-readablestreambyobrequest)

#### `readableByteStreamController.close()`

Added in: v16.5.0

Closes the [`<ReadableStream>`](66-webstreams.md#class-readablestream) to which this controller is associated.

#### `readableByteStreamController.desiredSize`

Added in: v16.5.0

- Type: [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type)

Returns the amount of data remaining to fill the [`<ReadableStream>`](66-webstreams.md#class-readablestream)'s
queue.

#### `readableByteStreamController.enqueue(chunk)`

Added in: v16.5.0

- `chunk` [`<Buffer>`](07-buffer.md#class-buffer) | [`<TypedArray>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/TypedArray) | [`<DataView>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/DataView)

Appends a new chunk of data to the [`<ReadableStream>`](66-webstreams.md#class-readablestream)'s queue.

#### `readableByteStreamController.error([error])`

Added in: v16.5.0

- `error` [`<any>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#Data_types)

Signals an error that causes the [`<ReadableStream>`](66-webstreams.md#class-readablestream) to error and close.

### Class: `ReadableStreamBYOBRequest`

Added in: v16.5.0

**History**

| Version | Changes |
| --- | --- |
| v18.0.0 | This class is now exposed on the global object. |

When using `ReadableByteStreamController` in byte-oriented
streams, and when using the `ReadableStreamBYOBReader`,
the `readableByteStreamController.byobRequest` property
provides access to a `ReadableStreamBYOBRequest` instance
that represents the current read request. The object
is used to gain access to the `ArrayBuffer`/`TypedArray`
that has been provided for the read request to fill,
and provides methods for signaling that the data has
been provided.

#### `readableStreamBYOBRequest.respond(bytesWritten)`

Added in: v16.5.0

- `bytesWritten` [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type)

Signals that a `bytesWritten` number of bytes have been written
to `readableStreamBYOBRequest.view`.

#### `readableStreamBYOBRequest.respondWithNewView(view)`

Added in: v16.5.0

- `view` [`<Buffer>`](07-buffer.md#class-buffer) | [`<TypedArray>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/TypedArray) | [`<DataView>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/DataView)

Signals that the request has been fulfilled with bytes written
to a new `Buffer`, `TypedArray`, or `DataView`.

#### `readableStreamBYOBRequest.view`

Added in: v16.5.0

- Type: [`<Buffer>`](07-buffer.md#class-buffer) | [`<TypedArray>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/TypedArray) | [`<DataView>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/DataView)

### Class: `WritableStream`

Added in: v16.5.0

**History**

| Version | Changes |
| --- | --- |
| v18.0.0 | This class is now exposed on the global object. |

The `WritableStream` is a destination to which stream data is sent.

```js
import {
  WritableStream,
} from 'node:stream/web';

const stream = new WritableStream({
  write(chunk) {
    console.log(chunk);
  },
});

await stream.getWriter().write('Hello World');
```

#### `new WritableStream([underlyingSink[, strategy]])`

Added in: v16.5.0

- `underlyingSink` [`<Object>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Object)
  - `start` [`<Function>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Function) A user-defined function that is invoked immediately when
    the `WritableStream` is created.
    - `controller` [`<WritableStreamDefaultController>`](66-webstreams.md#class-writablestreamdefaultcontroller)
    - Returns: `undefined` or a promise fulfilled with `undefined`.
  - `write` [`<Function>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Function) A user-defined function that is invoked when a chunk of
    data has been written to the `WritableStream`.
    - `chunk` [`<any>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#Data_types)
    - `controller` [`<WritableStreamDefaultController>`](66-webstreams.md#class-writablestreamdefaultcontroller)
    - Returns: A promise fulfilled with `undefined`.
  - `close` [`<Function>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Function) A user-defined function that is called when the `WritableStream` is closed.
    - Returns: A promise fulfilled with `undefined`.
  - `abort` [`<Function>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Function) A user-defined function that is called to abruptly close
    the `WritableStream`.
    - `reason` [`<any>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#Data_types)
    - Returns: A promise fulfilled with `undefined`.
  - `type` [`<any>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#Data_types) The `type` option is reserved for future use and *must* be
    undefined.
- `strategy` [`<Object>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Object)
  - `highWaterMark` [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) The maximum internal queue size before backpressure
    is applied.
  - `size` [`<Function>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Function) A user-defined function used to identify the size of each
    chunk of data.
    - `chunk` [`<any>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#Data_types)
    - Returns: [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type)

#### `writableStream.abort([reason])`

Added in: v16.5.0

- `reason` [`<any>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#Data_types)
- Returns: A promise fulfilled with `undefined`.

Abruptly terminates the `WritableStream`. All queued writes will be
canceled with their associated promises rejected.

#### `writableStream.close()`

Added in: v16.5.0

- Returns: A promise fulfilled with `undefined`.

Closes the `WritableStream` when no additional writes are expected.

#### `writableStream.getWriter()`

Added in: v16.5.0

- Returns: [`<WritableStreamDefaultWriter>`](66-webstreams.md#class-writablestreamdefaultwriter)

Creates and returns a new writer instance that can be used to write
data into the `WritableStream`.

#### `writableStream.locked`

Added in: v16.5.0

- Type: [`<boolean>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#boolean_type)

The `writableStream.locked` property is `false` by default, and is
switched to `true` while there is an active writer attached to this
`WritableStream`.

#### Transferring with postMessage()

A [`<WritableStream>`](66-webstreams.md#class-writablestream) instance can be transferred using a [`<MessagePort>`](67-worker-threads.md#class-messageport).

```js
const stream = new WritableStream(getWritableSinkSomehow());

const { port1, port2 } = new MessageChannel();

port1.onmessage = ({ data }) => {
  data.getWriter().write('hello');
};

port2.postMessage(stream, [stream]);
```

### Class: `WritableStreamDefaultWriter`

Added in: v16.5.0

**History**

| Version | Changes |
| --- | --- |
| v18.0.0 | This class is now exposed on the global object. |

#### `new WritableStreamDefaultWriter(stream)`

Added in: v16.5.0

- `stream` [`<WritableStream>`](66-webstreams.md#class-writablestream)

Creates a new `WritableStreamDefaultWriter` that is locked to the given
`WritableStream`.

#### `writableStreamDefaultWriter.abort([reason])`

Added in: v16.5.0

- `reason` [`<any>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#Data_types)
- Returns: A promise fulfilled with `undefined`.

Abruptly terminates the `WritableStream`. All queued writes will be
canceled with their associated promises rejected.

#### `writableStreamDefaultWriter.close()`

Added in: v16.5.0

- Returns: A promise fulfilled with `undefined`.

Closes the `WritableStream` when no additional writes are expected.

#### `writableStreamDefaultWriter.closed`

Added in: v16.5.0

- Type: [`<Promise>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Promise) Fulfilled with `undefined` when the associated
  [`<WritableStream>`](66-webstreams.md#class-writablestream) is closed or rejected if the stream errors or the writer's
  lock is released before the stream finishes closing.

#### `writableStreamDefaultWriter.desiredSize`

Added in: v16.5.0

- Type: [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type)

The amount of data required to fill the [`<WritableStream>`](66-webstreams.md#class-writablestream)'s queue.

#### `writableStreamDefaultWriter.ready`

Added in: v16.5.0

- Type: [`<Promise>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Promise) Fulfilled with `undefined` when the writer is ready
  to be used.

#### `writableStreamDefaultWriter.releaseLock()`

Added in: v16.5.0

Releases this writer's lock on the underlying [`<ReadableStream>`](66-webstreams.md#class-readablestream).

#### `writableStreamDefaultWriter.write([chunk])`

Added in: v16.5.0

- `chunk` [`<any>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#Data_types)
- Returns: A promise fulfilled with `undefined`.

Appends a new chunk of data to the [`<WritableStream>`](66-webstreams.md#class-writablestream)'s queue.

### Class: `WritableStreamDefaultController`

Added in: v16.5.0

**History**

| Version | Changes |
| --- | --- |
| v18.0.0 | This class is now exposed on the global object. |

The `WritableStreamDefaultController` manages the [`<WritableStream>`](66-webstreams.md#class-writablestream)'s
internal state.

#### `writableStreamDefaultController.error([error])`

Added in: v16.5.0

- `error` [`<any>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#Data_types)

Called by user-code to signal that an error has occurred while processing
the `WritableStream` data. When called, the [`<WritableStream>`](66-webstreams.md#class-writablestream) will be aborted,
with currently pending writes canceled.

#### `writableStreamDefaultController.signal`

- Type: [`<AbortSignal>`](26-globals.md#class-abortsignal) An `AbortSignal` that can be used to cancel pending
  write or close operations when a [`<WritableStream>`](66-webstreams.md#class-writablestream) is aborted.

### Class: `TransformStream`

Added in: v16.5.0

**History**

| Version | Changes |
| --- | --- |
| v18.0.0 | This class is now exposed on the global object. |

A `TransformStream` consists of a [`<ReadableStream>`](66-webstreams.md#class-readablestream) and a [`<WritableStream>`](66-webstreams.md#class-writablestream) that
are connected such that the data written to the `WritableStream` is received,
and potentially transformed, before being pushed into the `ReadableStream`'s
queue.

```js
import {
  TransformStream,
} from 'node:stream/web';

const transform = new TransformStream({
  transform(chunk, controller) {
    controller.enqueue(chunk.toUpperCase());
  },
});

await Promise.all([
  transform.writable.getWriter().write('A'),
  transform.readable.getReader().read(),
]);
```

#### `new TransformStream([transformer[, writableStrategy[, readableStrategy]]])`

Added in: v16.5.0

**History**

| Version | Changes |
| --- | --- |
| v21.5.0, v20.14.0 | Supports the `cancel` transformer callback. |

- `transformer` [`<Object>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Object)
  - `start` [`<Function>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Function) A user-defined function that is invoked immediately when
    the `TransformStream` is created.
    - `controller` [`<TransformStreamDefaultController>`](66-webstreams.md#class-transformstreamdefaultcontroller)
    - Returns: `undefined` or a promise fulfilled with `undefined`
  - `transform` [`<Function>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Function) A user-defined function that receives, and
    potentially modifies, a chunk of data written to `transformStream.writable`,
    before forwarding that on to `transformStream.readable`.
    - `chunk` [`<any>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#Data_types)
    - `controller` [`<TransformStreamDefaultController>`](66-webstreams.md#class-transformstreamdefaultcontroller)
    - Returns: A promise fulfilled with `undefined`.
  - `flush` [`<Function>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Function) A user-defined function that is called immediately before
    the writable side of the `TransformStream` is closed, signaling the end of
    the transformation process.
    - `controller` [`<TransformStreamDefaultController>`](66-webstreams.md#class-transformstreamdefaultcontroller)
    - Returns: A promise fulfilled with `undefined`.
  - `cancel` [`<Function>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Function) A user-defined function that is called when either the
    readable side of the `TransformStream` is canceled or the writable side is
    aborted.
    - `reason` [`<any>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#Data_types)
    - Returns: A promise fulfilled with `undefined`.
  - `readableType` [`<any>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#Data_types) the `readableType` option is reserved for future use
    and *must* be `undefined`.
  - `writableType` [`<any>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#Data_types) the `writableType` option is reserved for future use
    and *must* be `undefined`.
- `writableStrategy` [`<Object>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Object)
  - `highWaterMark` [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) The maximum internal queue size before backpressure
    is applied.
  - `size` [`<Function>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Function) A user-defined function used to identify the size of each
    chunk of data.
    - `chunk` [`<any>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#Data_types)
    - Returns: [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type)
- `readableStrategy` [`<Object>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Object)
  - `highWaterMark` [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) The maximum internal queue size before backpressure
    is applied.
  - `size` [`<Function>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Function) A user-defined function used to identify the size of each
    chunk of data.
    - `chunk` [`<any>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#Data_types)
    - Returns: [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type)

#### `transformStream.readable`

Added in: v16.5.0

- Type: [`<ReadableStream>`](66-webstreams.md#class-readablestream)

#### `transformStream.writable`

Added in: v16.5.0

- Type: [`<WritableStream>`](66-webstreams.md#class-writablestream)

#### Transferring with postMessage()

A [`<TransformStream>`](66-webstreams.md#class-transformstream) instance can be transferred using a [`<MessagePort>`](67-worker-threads.md#class-messageport).

```js
const stream = new TransformStream();

const { port1, port2 } = new MessageChannel();

port1.onmessage = ({ data }) => {
  const { writable, readable } = data;
  // ...
};

port2.postMessage(stream, [stream]);
```

### Class: `TransformStreamDefaultController`

Added in: v16.5.0

**History**

| Version | Changes |
| --- | --- |
| v18.0.0 | This class is now exposed on the global object. |

The `TransformStreamDefaultController` manages the internal state
of the `TransformStream`.

#### `transformStreamDefaultController.desiredSize`

Added in: v16.5.0

- Type: [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type)

The amount of data required to fill the readable side's queue.

#### `transformStreamDefaultController.enqueue([chunk])`

Added in: v16.5.0

- `chunk` [`<any>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#Data_types)

Appends a chunk of data to the readable side's queue.

#### `transformStreamDefaultController.error([reason])`

Added in: v16.5.0

- `reason` [`<any>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#Data_types)

Signals to both the readable and writable side that an error has occurred
while processing the transform data, causing both sides to be abruptly
closed.

#### `transformStreamDefaultController.terminate()`

Added in: v16.5.0

Closes the readable side of the transport and causes the writable side
to be abruptly closed with an error.

### Class: `ByteLengthQueuingStrategy`

Added in: v16.5.0

**History**

| Version | Changes |
| --- | --- |
| v18.0.0 | This class is now exposed on the global object. |

#### `new ByteLengthQueuingStrategy(init)`

Added in: v16.5.0

- `init` [`<Object>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Object)
  - `highWaterMark` [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type)

#### `byteLengthQueuingStrategy.highWaterMark`

Added in: v16.5.0

- Type: [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type)

#### `byteLengthQueuingStrategy.size`

Added in: v16.5.0

- Type: [`<Function>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Function)
  - `chunk` [`<any>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#Data_types)
  - Returns: [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type)

### Class: `CountQueuingStrategy`

Added in: v16.5.0

**History**

| Version | Changes |
| --- | --- |
| v18.0.0 | This class is now exposed on the global object. |

#### `new CountQueuingStrategy(init)`

Added in: v16.5.0

- `init` [`<Object>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Object)
  - `highWaterMark` [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type)

#### `countQueuingStrategy.highWaterMark`

Added in: v16.5.0

- Type: [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type)

#### `countQueuingStrategy.size`

Added in: v16.5.0

- Type: [`<Function>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Function)
  - `chunk` [`<any>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#Data_types)
  - Returns: [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type)

### Class: `TextEncoderStream`

Added in: v16.6.0

**History**

| Version | Changes |
| --- | --- |
| v18.0.0 | This class is now exposed on the global object. |

#### `new TextEncoderStream()`

Added in: v16.6.0

Creates a new `TextEncoderStream` instance.

#### `textEncoderStream.encoding`

Added in: v16.6.0

- Type: [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type)

The encoding supported by the `TextEncoderStream` instance.

#### `textEncoderStream.readable`

Added in: v16.6.0

- Type: [`<ReadableStream>`](66-webstreams.md#class-readablestream)

#### `textEncoderStream.writable`

Added in: v16.6.0

- Type: [`<WritableStream>`](66-webstreams.md#class-writablestream)

### Class: `TextDecoderStream`

Added in: v16.6.0

**History**

| Version | Changes |
| --- | --- |
| v18.0.0 | This class is now exposed on the global object. |

#### `new TextDecoderStream([encoding[, options]])`

Added in: v16.6.0

- `encoding` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) Identifies the `encoding` that this `TextDecoder` instance
  supports. **Default:** `'utf-8'`.
- `options` [`<Object>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Object)
  - `fatal` [`<boolean>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#boolean_type) `true` if decoding failures are fatal.
  - `ignoreBOM` [`<boolean>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#boolean_type) When `true`, the `TextDecoderStream` will include the
    byte order mark in the decoded result. When `false`, the byte order mark
    will be removed from the output. This option is only used when `encoding` is
    `'utf-8'`, `'utf-16be'`, or `'utf-16le'`. **Default:** `false`.

Creates a new `TextDecoderStream` instance.

#### `textDecoderStream.encoding`

Added in: v16.6.0

- Type: [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type)

The encoding supported by the `TextDecoderStream` instance.

#### `textDecoderStream.fatal`

Added in: v16.6.0

- Type: [`<boolean>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#boolean_type)

The value will be `true` if decoding errors result in a `TypeError` being
thrown.

#### `textDecoderStream.ignoreBOM`

Added in: v16.6.0

- Type: [`<boolean>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#boolean_type)

The value will be `true` if the decoding result will include the byte order
mark.

#### `textDecoderStream.readable`

Added in: v16.6.0

- Type: [`<ReadableStream>`](66-webstreams.md#class-readablestream)

#### `textDecoderStream.writable`

Added in: v16.6.0

- Type: [`<WritableStream>`](66-webstreams.md#class-writablestream)

### Class: `CompressionStream`

Added in: v17.0.0

**History**

| Version | Changes |
| --- | --- |
| v18.0.0 | This class is now exposed on the global object. |

#### `new CompressionStream(format)`

Added in: v17.0.0

**History**

| Version | Changes |
| --- | --- |
| v24.7.0, v22.20.0 | format now accepts `brotli` value. |
| v21.2.0, v20.12.0 | format now accepts `deflate-raw` value. |

- `format` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) One of `'deflate'`, `'deflate-raw'`, `'gzip'`, or `'brotli'`.

#### `compressionStream.readable`

Added in: v17.0.0

- Type: [`<ReadableStream>`](66-webstreams.md#class-readablestream)

#### `compressionStream.writable`

Added in: v17.0.0

- Type: [`<WritableStream>`](66-webstreams.md#class-writablestream)

### Class: `DecompressionStream`

Added in: v17.0.0

**History**

| Version | Changes |
| --- | --- |
| v18.0.0 | This class is now exposed on the global object. |

#### `new DecompressionStream(format)`

Added in: v17.0.0

**History**

| Version | Changes |
| --- | --- |
| v24.7.0, v22.20.0 | format now accepts `brotli` value. |
| v21.2.0, v20.12.0 | format now accepts `deflate-raw` value. |

- `format` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) One of `'deflate'`, `'deflate-raw'`, `'gzip'`, or `'brotli'`.

#### `decompressionStream.readable`

Added in: v17.0.0

- Type: [`<ReadableStream>`](66-webstreams.md#class-readablestream)

#### `decompressionStream.writable`

Added in: v17.0.0

- Type: [`<WritableStream>`](66-webstreams.md#class-writablestream)

### Utility Consumers

Added in: v16.7.0

The utility consumer functions provide common options for consuming
streams.

They are accessed using:

*ES module version:*

```js
import {
  arrayBuffer,
  blob,
  buffer,
  json,
  text,
} from 'node:stream/consumers';
```

*CommonJS version:*

```js
const {
  arrayBuffer,
  blob,
  buffer,
  json,
  text,
} = require('node:stream/consumers');
```

#### `streamConsumers.arrayBuffer(stream)`

Added in: v16.7.0

- `stream` [`<ReadableStream>`](66-webstreams.md#class-readablestream) | [`<stream.Readable>`](51-stream.md#class-streamreadable) | [`<AsyncIterator>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/AsyncIterator)
- Returns: [`<Promise>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Promise) Fulfills with an `ArrayBuffer` containing the full
  contents of the stream.

*ES module version:*

```js
import { arrayBuffer } from 'node:stream/consumers';
import { Readable } from 'node:stream';
import { TextEncoder } from 'node:util';

const encoder = new TextEncoder();
const dataArray = encoder.encode('hello world from consumers!');

const readable = Readable.from(dataArray);
const data = await arrayBuffer(readable);
console.log(`from readable: ${data.byteLength}`);
// Prints: from readable: 76
```

*CommonJS version:*

```js
const { arrayBuffer } = require('node:stream/consumers');
const { Readable } = require('node:stream');
const { TextEncoder } = require('node:util');

const encoder = new TextEncoder();
const dataArray = encoder.encode('hello world from consumers!');
const readable = Readable.from(dataArray);
arrayBuffer(readable).then((data) => {
  console.log(`from readable: ${data.byteLength}`);
  // Prints: from readable: 76
});
```

#### `streamConsumers.blob(stream)`

Added in: v16.7.0

- `stream` [`<ReadableStream>`](66-webstreams.md#class-readablestream) | [`<stream.Readable>`](51-stream.md#class-streamreadable) | [`<AsyncIterator>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/AsyncIterator)
- Returns: [`<Promise>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Promise) Fulfills with a [`<Blob>`](07-buffer.md#class-blob) containing the full contents
  of the stream.

*ES module version:*

```js
import { blob } from 'node:stream/consumers';

const dataBlob = new Blob(['hello world from consumers!']);

const readable = dataBlob.stream();
const data = await blob(readable);
console.log(`from readable: ${data.size}`);
// Prints: from readable: 27
```

*CommonJS version:*

```js
const { blob } = require('node:stream/consumers');

const dataBlob = new Blob(['hello world from consumers!']);

const readable = dataBlob.stream();
blob(readable).then((data) => {
  console.log(`from readable: ${data.size}`);
  // Prints: from readable: 27
});
```

#### `streamConsumers.buffer(stream)`

Added in: v16.7.0

- `stream` [`<ReadableStream>`](66-webstreams.md#class-readablestream) | [`<stream.Readable>`](51-stream.md#class-streamreadable) | [`<AsyncIterator>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/AsyncIterator)
- Returns: [`<Promise>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Promise) Fulfills with a [`<Buffer>`](07-buffer.md#class-buffer) containing the full
  contents of the stream.

*ES module version:*

```js
import { buffer } from 'node:stream/consumers';
import { Readable } from 'node:stream';
import { Buffer } from 'node:buffer';

const dataBuffer = Buffer.from('hello world from consumers!');

const readable = Readable.from(dataBuffer);
const data = await buffer(readable);
console.log(`from readable: ${data.length}`);
// Prints: from readable: 27
```

*CommonJS version:*

```js
const { buffer } = require('node:stream/consumers');
const { Readable } = require('node:stream');
const { Buffer } = require('node:buffer');

const dataBuffer = Buffer.from('hello world from consumers!');

const readable = Readable.from(dataBuffer);
buffer(readable).then((data) => {
  console.log(`from readable: ${data.length}`);
  // Prints: from readable: 27
});
```

#### `streamConsumers.bytes(stream)`

Added in: v25.6.0, v24.14.0

- `stream` [`<ReadableStream>`](66-webstreams.md#class-readablestream) | [`<stream.Readable>`](51-stream.md#class-streamreadable) | [`<AsyncIterator>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/AsyncIterator)
- Returns: [`<Promise>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Promise) Fulfills with a [`<Uint8Array>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Uint8Array) containing the full
  contents of the stream.

*ES module version:*

```js
import { bytes } from 'node:stream/consumers';
import { Readable } from 'node:stream';
import { Buffer } from 'node:buffer';

const dataBuffer = Buffer.from('hello world from consumers!');

const readable = Readable.from(dataBuffer);
const data = await bytes(readable);
console.log(`from readable: ${data.length}`);
// Prints: from readable: 27
```

*CommonJS version:*

```js
const { bytes } = require('node:stream/consumers');
const { Readable } = require('node:stream');
const { Buffer } = require('node:buffer');

const dataBuffer = Buffer.from('hello world from consumers!');

const readable = Readable.from(dataBuffer);
bytes(readable).then((data) => {
  console.log(`from readable: ${data.length}`);
  // Prints: from readable: 27
});
```

#### `streamConsumers.json(stream)`

Added in: v16.7.0

- `stream` [`<ReadableStream>`](66-webstreams.md#class-readablestream) | [`<stream.Readable>`](51-stream.md#class-streamreadable) | [`<AsyncIterator>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/AsyncIterator)
- Returns: [`<Promise>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Promise) Fulfills with the contents of the stream parsed as a
  UTF-8 encoded string that is then passed through `JSON.parse()`.

*ES module version:*

```js
import { json } from 'node:stream/consumers';
import { Readable } from 'node:stream';

const items = Array.from(
  {
    length: 100,
  },
  () => ({
    message: 'hello world from consumers!',
  }),
);

const readable = Readable.from(JSON.stringify(items));
const data = await json(readable);
console.log(`from readable: ${data.length}`);
// Prints: from readable: 100
```

*CommonJS version:*

```js
const { json } = require('node:stream/consumers');
const { Readable } = require('node:stream');

const items = Array.from(
  {
    length: 100,
  },
  () => ({
    message: 'hello world from consumers!',
  }),
);

const readable = Readable.from(JSON.stringify(items));
json(readable).then((data) => {
  console.log(`from readable: ${data.length}`);
  // Prints: from readable: 100
});
```

#### `streamConsumers.text(stream)`

Added in: v16.7.0

- `stream` [`<ReadableStream>`](66-webstreams.md#class-readablestream) | [`<stream.Readable>`](51-stream.md#class-streamreadable) | [`<AsyncIterator>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/AsyncIterator)
- Returns: [`<Promise>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Promise) Fulfills with the contents of the stream parsed as a
  UTF-8 encoded string.

*ES module version:*

```js
import { text } from 'node:stream/consumers';
import { Readable } from 'node:stream';

const readable = Readable.from('Hello world from consumers!');
const data = await text(readable);
console.log(`from readable: ${data.length}`);
// Prints: from readable: 27
```

*CommonJS version:*

```js
const { text } = require('node:stream/consumers');
const { Readable } = require('node:stream');

const readable = Readable.from('Hello world from consumers!');
text(readable).then((data) => {
  console.log(`from readable: ${data.length}`);
  // Prints: from readable: 27
});
```

[Contents](../README.md) | [Previous: Web Crypto API](65-webcrypto.md) | [Next: Worker threads](67-worker-threads.md)
