[Contents](../README.md) | [Previous: Stream, part 2 of 4](51-stream-part2.md) | [Next: Stream, part 4 of 4](51-stream-part4.md)

Source: [https://nodejs.org/docs/latest/api/stream.html](https://nodejs.org/docs/latest/api/stream.html)

Part 3 of 4 of this section.

# Stream (part 3 of 4)

### `stream.Readable.toWeb(streamReadable[, options])`

Added in: v17.0.0

**History**

| Version | Changes |
| --- | --- |
| v25.4.0, v24.14.0 | Add 'type' option to specify 'bytes'. |
| v24.0.0, v22.17.0 | Marking the API stable. |
| v18.7.0 | include strategy options on Readable. |

- `streamReadable` [`<stream.Readable>`](51-stream-part1.md#class-streamreadable)
- `options` [`<Object>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Object)
  - `strategy` [`<Object>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Object)
    - `highWaterMark` [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) The maximum internal queue size (of the created `ReadableStream`) before backpressure is applied in reading from the given
      `stream.Readable`. If no value is provided, it will be taken from the
      given `stream.Readable`.
    - `size` [`<Function>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Function) A function that size of the given chunk of data.
      If no value is provided, the size will be `1` for all the chunks.
      - `chunk` [`<any>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#Data_types)
      - Returns: [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type)
  - `type` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) Specifies the type of the created `ReadableStream`. Must be
    `'bytes'` or undefined.
- Returns: [`<ReadableStream>`](66-webstreams.md#class-readablestream)

### `stream.Writable.fromWeb(writableStream[, options])`

Added in: v17.0.0

**History**

| Version | Changes |
| --- | --- |
| v24.0.0, v22.17.0 | Marking the API stable. |

- `writableStream` [`<WritableStream>`](66-webstreams.md#class-writablestream)
- `options` [`<Object>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Object)
  - `decodeStrings` [`<boolean>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#boolean_type)
  - `highWaterMark` [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type)
  - `objectMode` [`<boolean>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#boolean_type)
  - `signal` [`<AbortSignal>`](26-globals.md#class-abortsignal)
- Returns: [`<stream.Writable>`](51-stream-part1.md#class-streamwritable)

### `stream.Writable.toWeb(streamWritable)`

Added in: v17.0.0

**History**

| Version | Changes |
| --- | --- |
| v24.0.0, v22.17.0 | Marking the API stable. |

- `streamWritable` [`<stream.Writable>`](51-stream-part1.md#class-streamwritable)
- Returns: [`<WritableStream>`](66-webstreams.md#class-writablestream)

### `stream.Duplex.from(src)`

Added in: v16.8.0

**History**

| Version | Changes |
| --- | --- |
| v19.5.0, v18.17.0 | The `src` argument can now be a `ReadableStream` or `WritableStream`. |

- `src` {Stream|Blob|ArrayBuffer|string|Iterable|AsyncIterable|
  AsyncGeneratorFunction|AsyncFunction|Promise|Object|
  ReadableStream|WritableStream}

A utility method for creating duplex streams.

- `Stream` converts writable stream into writable `Duplex` and readable stream
  to `Duplex`.
- `Blob` converts into readable `Duplex`.
- `string` converts into readable `Duplex`.
- `ArrayBuffer` converts into readable `Duplex`.
- `AsyncIterable` converts into a readable `Duplex`. Cannot yield
  `null`.
- `AsyncGeneratorFunction` converts into a readable/writable transform
  `Duplex`. Must take a source `AsyncIterable` as first parameter. Cannot yield
  `null`.
- `AsyncFunction` converts into a writable `Duplex`. Must return
  either `null` or `undefined`
- `Object ({ writable, readable })` converts `readable` and
  `writable` into `Stream` and then combines them into `Duplex` where the
  `Duplex` will write to the `writable` and read from the `readable`.
- `Promise` converts into readable `Duplex`. Value `null` is ignored.
- `ReadableStream` converts into readable `Duplex`.
- `WritableStream` converts into writable `Duplex`.
- Returns: [`<stream.Duplex>`](51-stream-part2.md#class-streamduplex)

If an `Iterable` object containing promises is passed as an argument,
it might result in unhandled rejection.

```js
const { Duplex } = require('node:stream');

Duplex.from([
  new Promise((resolve) => setTimeout(resolve('1'), 1500)),
  new Promise((_, reject) => setTimeout(reject(new Error('2')), 1000)), // Unhandled rejection
]);
```

### `stream.Duplex.fromWeb(pair[, options])`

Added in: v17.0.0

**History**

| Version | Changes |
| --- | --- |
| v24.0.0, v22.17.0 | Marking the API stable. |

- `pair` [`<Object>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Object)
  - `readable` [`<ReadableStream>`](66-webstreams.md#class-readablestream)
  - `writable` [`<WritableStream>`](66-webstreams.md#class-writablestream)
- `options` [`<Object>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Object)
  - `allowHalfOpen` [`<boolean>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#boolean_type)
  - `decodeStrings` [`<boolean>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#boolean_type)
  - `encoding` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type)
  - `highWaterMark` [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type)
  - `objectMode` [`<boolean>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#boolean_type)
  - `signal` [`<AbortSignal>`](26-globals.md#class-abortsignal)
- Returns: [`<stream.Duplex>`](51-stream-part2.md#class-streamduplex)

*ES module version:*

```js
import { Duplex } from 'node:stream';
import {
  ReadableStream,
  WritableStream,
} from 'node:stream/web';

const readable = new ReadableStream({
  start(controller) {
    controller.enqueue('world');
  },
});

const writable = new WritableStream({
  write(chunk) {
    console.log('writable', chunk);
  },
});

const pair = {
  readable,
  writable,
};
const duplex = Duplex.fromWeb(pair, { encoding: 'utf8', objectMode: true });

duplex.write('hello');

for await (const chunk of duplex) {
  console.log('readable', chunk);
}
```

*CommonJS version:*

```js
const { Duplex } = require('node:stream');
const {
  ReadableStream,
  WritableStream,
} = require('node:stream/web');

const readable = new ReadableStream({
  start(controller) {
    controller.enqueue('world');
  },
});

const writable = new WritableStream({
  write(chunk) {
    console.log('writable', chunk);
  },
});

const pair = {
  readable,
  writable,
};
const duplex = Duplex.fromWeb(pair, { encoding: 'utf8', objectMode: true });

duplex.write('hello');
duplex.once('readable', () => console.log('readable', duplex.read()));
```

### `stream.Duplex.toWeb(streamDuplex[, options])`

Added in: v17.0.0

**History**

| Version | Changes |
| --- | --- |
| v25.7.0 | Added the 'readableType' option to specify the ReadableStream type. The 'type' option is deprecated. |
| v25.4.0, v24.14.0 | Added the 'type' option to specify the ReadableStream type. |
| v24.0.0, v22.17.0 | Marking the API stable. |

- `streamDuplex` [`<stream.Duplex>`](51-stream-part2.md#class-streamduplex)
- `options` [`<Object>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Object)
  - `readableType` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) Specifies the type of the `ReadableStream` half of
    the created readable-writable pair. Must be `'bytes'` or undefined.
    (`options.type` is a deprecated alias for this option.)
- Returns: [`<Object>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Object)
  - `readable` [`<ReadableStream>`](66-webstreams.md#class-readablestream)
  - `writable` [`<WritableStream>`](66-webstreams.md#class-writablestream)

*ES module version:*

```js
import { Duplex } from 'node:stream';

const duplex = Duplex({
  objectMode: true,
  read() {
    this.push('world');
    this.push(null);
  },
  write(chunk, encoding, callback) {
    console.log('writable', chunk);
    callback();
  },
});

const { readable, writable } = Duplex.toWeb(duplex);
writable.getWriter().write('hello');

const { value } = await readable.getReader().read();
console.log('readable', value);
```

*CommonJS version:*

```js
const { Duplex } = require('node:stream');

const duplex = Duplex({
  objectMode: true,
  read() {
    this.push('world');
    this.push(null);
  },
  write(chunk, encoding, callback) {
    console.log('writable', chunk);
    callback();
  },
});

const { readable, writable } = Duplex.toWeb(duplex);
writable.getWriter().write('hello');

readable.getReader().read().then((result) => {
  console.log('readable', result.value);
});
```

### `stream.addAbortSignal(signal, stream)`

Added in: v15.4.0

**History**

| Version | Changes |
| --- | --- |
| v19.7.0, v18.16.0 | Added support for `ReadableStream` and `WritableStream`. |

- `signal` [`<AbortSignal>`](26-globals.md#class-abortsignal) A signal representing possible cancellation
- `stream` [`<Stream>`](51-stream-part1.md#stream) | [`<ReadableStream>`](66-webstreams.md#class-readablestream) | [`<WritableStream>`](66-webstreams.md#class-writablestream) A stream to attach a signal
  to.

Attaches an AbortSignal to a readable or writable stream. This lets code
control stream destruction using an `AbortController`.

Stability: 0 - Deprecated. Using [`stream.addAbortSignal()`](#streamaddabortsignalsignal-stream) to destroy
long-lived stream resources is documentation-only deprecated. See
[DEP0209](17-deprecations.md#dep0209-using-abortsignal-to-dispose-of-resources).

Calling `abort` on the `AbortController` corresponding to the passed
`AbortSignal` will behave the same way as calling `.destroy(new AbortError())`
on the stream, and `controller.error(new AbortError())` for webstreams.

```js
const fs = require('node:fs');

const controller = new AbortController();
const read = addAbortSignal(
  controller.signal,
  fs.createReadStream(('object.json')),
);
// Later, abort the operation closing the stream
controller.abort();
```

Or using an `AbortSignal` with a readable stream as an async iterable:

```js
const controller = new AbortController();
setTimeout(() => controller.abort(), 10_000); // set a timeout
const stream = addAbortSignal(
  controller.signal,
  fs.createReadStream(('object.json')),
);
(async () => {
  try {
    for await (const chunk of stream) {
      await process(chunk);
    }
  } catch (e) {
    if (e.name === 'AbortError') {
      // The operation was cancelled
    } else {
      throw e;
    }
  }
})();
```

Or using an `AbortSignal` with a ReadableStream:

```js
const controller = new AbortController();
const rs = new ReadableStream({
  start(controller) {
    controller.enqueue('hello');
    controller.enqueue('world');
    controller.close();
  },
});

addAbortSignal(controller.signal, rs);

finished(rs, (err) => {
  if (err) {
    if (err.name === 'AbortError') {
      // The operation was cancelled
    }
  }
});

const reader = rs.getReader();

reader.read().then(({ value, done }) => {
  console.log(value); // hello
  console.log(done); // false
  controller.abort();
});
```

### `stream.getDefaultHighWaterMark(objectMode)`

Added in: v19.9.0, v18.17.0

**History**

| Version | Changes |
| --- | --- |
| v22.0.0 | bump default highWaterMark. |

- `objectMode` [`<boolean>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#boolean_type)
- Returns: [`<integer>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type)

Returns the default highWaterMark used by streams. Defaults to `16` for
`objectMode`. For byte streams, it defaults to `65536` (64 KiB) on non-Windows
platforms and `16384` (16 KiB) on Windows.

### `stream.setDefaultHighWaterMark(objectMode, value)`

Added in: v19.9.0, v18.17.0

- `objectMode` [`<boolean>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#boolean_type)
- `value` [`<integer>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) highWaterMark value

Sets the default highWaterMark used by streams.

## API for stream implementers

The `node:stream` module API has been designed to make it possible to easily
implement streams using JavaScript's prototypal inheritance model.

First, a stream developer would declare a new JavaScript class that extends one
of the four basic stream classes (`stream.Writable`, `stream.Readable`,
`stream.Duplex`, or `stream.Transform`), making sure they call the appropriate
parent class constructor:

```js
const { Writable } = require('node:stream');

class MyWritable extends Writable {
  constructor({ highWaterMark, ...options }) {
    super({ highWaterMark });
    // ...
  }
}
```

When extending streams, keep in mind what options the user
can and should provide before forwarding these to the base constructor. For
example, if the implementation makes assumptions in regard to the
`autoDestroy` and `emitClose` options, do not allow the
user to override these. Be explicit about what
options are forwarded instead of implicitly forwarding all options.

The new stream class must then implement one or more specific methods, depending
on the type of stream being created, as detailed in the chart below:

| Use-case | Class | Method(s) to implement |
| --- | --- | --- |
| Reading only | [`Readable`](51-stream-part1.md#class-streamreadable) | [`_read()`](#readable_readsize) |
| Writing only | [`Writable`](51-stream-part1.md#class-streamwritable) | [`_write()`](#writable_writechunk-encoding-callback), [`_writev()`](#writable_writevchunks-callback), [`_final()`](#writable_finalcallback) |
| Reading and writing | [`Duplex`](51-stream-part2.md#class-streamduplex) | [`_read()`](#readable_readsize), [`_write()`](#writable_writechunk-encoding-callback), [`_writev()`](#writable_writevchunks-callback), [`_final()`](#writable_finalcallback) |
| Operate on written data, then read the result | [`Transform`](51-stream-part2.md#class-streamtransform) | [`_transform()`](#transform_transformchunk-encoding-callback), [`_flush()`](#transform_flushcallback), [`_final()`](#writable_finalcallback) |

The implementation code for a stream should *never* call the "public" methods
of a stream that are intended for use by consumers (as described in the
[API for stream consumers](51-stream-part1.md#api-for-stream-consumers) section). Doing so may lead to adverse side effects
in application code consuming the stream.

Avoid overriding public methods such as `write()`, `end()`, `cork()`,
`uncork()`, `read()` and `destroy()`, or emitting internal events such
as `'error'`, `'data'`, `'end'`, `'finish'` and `'close'` through `.emit()`.
Doing so can break current and future stream invariants leading to behavior
and/or compatibility issues with other streams, stream utilities, and user
expectations.

### Simplified construction

Added in: v1.2.0

For many simple cases, it is possible to create a stream without relying on
inheritance. This can be accomplished by directly creating instances of the
`stream.Writable`, `stream.Readable`, `stream.Duplex`, or `stream.Transform`
objects and passing appropriate methods as constructor options.

```js
const { Writable } = require('node:stream');

const myWritable = new Writable({
  construct(callback) {
    // Initialize state and load resources...
  },
  write(chunk, encoding, callback) {
    // ...
  },
  destroy() {
    // Free resources...
  },
});
```

### Implementing a writable stream

The `stream.Writable` class is extended to implement a [`Writable`](51-stream-part1.md#class-streamwritable) stream.

Custom `Writable` streams *must* call the `new stream.Writable([options])`
constructor and implement the `writable._write()` and/or `writable._writev()`
method.

#### `new stream.Writable([options])`

**History**

| Version | Changes |
| --- | --- |
| v22.0.0 | bump default highWaterMark. |
| v15.5.0 | support passing in an AbortSignal. |
| v14.0.0 | Change `autoDestroy` option default to `true`. |
| v11.2.0, v10.16.0 | Add `autoDestroy` option to automatically `destroy()` the stream when it emits `'finish'` or errors. |
| v10.0.0 | Add `emitClose` option to specify if `'close'` is emitted on destroy. |

- `options` [`<Object>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Object)
  - `highWaterMark` [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) Buffer level when [`stream.write()`](51-stream-part1.md#writablewritechunk-encoding-callback) starts returning `false`. **Default:**
    See [`stream.getDefaultHighWaterMark()`](#streamgetdefaulthighwatermarkobjectmode).
  - `decodeStrings` [`<boolean>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#boolean_type) Whether to encode `string`s passed to
    [`stream.write()`](51-stream-part1.md#writablewritechunk-encoding-callback) to `Buffer`s (with the encoding
    specified in the [`stream.write()`](51-stream-part1.md#writablewritechunk-encoding-callback) call) before passing
    them to [`stream._write()`](#writable_writechunk-encoding-callback). Other types of data are not
    converted (i.e. `Buffer`s are not decoded into `string`s). Setting to
    false will prevent `string`s from being converted. **Default:** `true`.
  - `defaultEncoding` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) The default encoding that is used when no
    encoding is specified as an argument to [`stream.write()`](51-stream-part1.md#writablewritechunk-encoding-callback).
    **Default:** `'utf8'`.
  - `objectMode` [`<boolean>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#boolean_type) Whether or not the [`stream.write(anyObj)`](51-stream-part1.md#writablewritechunk-encoding-callback) is a valid operation. When set,
    it becomes possible to write JavaScript values other than string, [`<Buffer>`](07-buffer-part1.md#class-buffer),
    [`<TypedArray>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/TypedArray) or [`<DataView>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/DataView) if supported by the stream implementation. **Default:** `false`.
  - `emitClose` [`<boolean>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#boolean_type) Whether or not the stream should emit `'close'`
    after it has been destroyed. **Default:** `true`.
  - `write` [`<Function>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Function) Implementation for the [`stream._write()`](#writable_writechunk-encoding-callback) method.
  - `writev` [`<Function>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Function) Implementation for the [`stream._writev()`](#writable_writevchunks-callback) method.
  - `destroy` [`<Function>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Function) Implementation for the [`stream._destroy()`](#writable_destroyerr-callback) method.
  - `final` [`<Function>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Function) Implementation for the [`stream._final()`](#writable_finalcallback) method.
  - `construct` [`<Function>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Function) Implementation for the [`stream._construct()`](#writable_constructcallback) method.
  - `autoDestroy` [`<boolean>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#boolean_type) Whether this stream should automatically call `.destroy()` on itself after ending. **Default:** `true`.
  - `signal` [`<AbortSignal>`](26-globals.md#class-abortsignal) A signal representing possible cancellation.

*CommonJS version:*

```js
const { Writable } = require('node:stream');

class MyWritable extends Writable {
  constructor(options) {
    // Calls the stream.Writable() constructor.
    super(options);
    // ...
  }
}
```

*ES module version:*

```js
import { Writable } from 'node:stream';

class MyWritable extends Writable {
  constructor(options) {
    // Calls the stream.Writable() constructor.
    super(options);
    // ...
  }
}
```

Or, using the simplified constructor approach:

```js
const { Writable } = require('node:stream');

const myWritable = new Writable({
  write(chunk, encoding, callback) {
    // ...
  },
  writev(chunks, callback) {
    // ...
  },
});
```

Calling `abort` on the `AbortController` corresponding to the passed
`AbortSignal` will behave the same way as calling `.destroy(new AbortError())`
on the writable stream.

```js
const { Writable } = require('node:stream');

const controller = new AbortController();
const myWritable = new Writable({
  write(chunk, encoding, callback) {
    // ...
  },
  writev(chunks, callback) {
    // ...
  },
  signal: controller.signal,
});
// Later, abort the operation closing the stream
controller.abort();
```

#### `writable._construct(callback)`

Added in: v15.0.0

- `callback` [`<Function>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Function) Call this function (optionally with an error
  argument) when the stream has finished initializing.

The `_construct()` method MUST NOT be called directly. It may be implemented
by child classes, and if so, will be called by the internal `Writable`
class methods only.

This optional function will be called in a tick after the stream constructor
has returned, delaying any `_write()`, `_final()` and `_destroy()` calls until
`callback` is called. This is useful to initialize state or asynchronously
initialize resources before the stream can be used.

```js
const { Writable } = require('node:stream');
const fs = require('node:fs');

class WriteStream extends Writable {
  constructor(filename) {
    super();
    this.filename = filename;
    this.fd = null;
  }
  _construct(callback) {
    fs.open(this.filename, 'w', (err, fd) => {
      if (err) {
        callback(err);
      } else {
        this.fd = fd;
        callback();
      }
    });
  }
  _write(chunk, encoding, callback) {
    fs.write(this.fd, chunk, callback);
  }
  _destroy(err, callback) {
    if (this.fd) {
      fs.close(this.fd, (er) => callback(er || err));
    } else {
      callback(err);
    }
  }
}
```

#### `writable._write(chunk, encoding, callback)`

**History**

| Version | Changes |
| --- | --- |
| v12.11.0 | \_write() is optional when providing \_writev(). |

- `chunk` [`<Buffer>`](07-buffer-part1.md#class-buffer) | [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) | [`<any>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#Data_types) The `Buffer` to be written, converted from the
  `string` passed to [`stream.write()`](51-stream-part1.md#writablewritechunk-encoding-callback). If the stream's
  `decodeStrings` option is `false` or the stream is operating in object mode,
  the chunk will not be converted & will be whatever was passed to
  [`stream.write()`](51-stream-part1.md#writablewritechunk-encoding-callback).
- `encoding` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) If the chunk is a string, then `encoding` is the
  character encoding of that string. If chunk is a `Buffer`, or if the
  stream is operating in object mode, `encoding` may be ignored.
- `callback` [`<Function>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Function) Call this function (optionally with an error
  argument) when processing is complete for the supplied chunk.

All `Writable` stream implementations must provide a
[`writable._write()`](#writable_writechunk-encoding-callback) and/or
[`writable._writev()`](#writable_writevchunks-callback) method to send data to the underlying
resource.

[`Transform`](51-stream-part2.md#class-streamtransform) streams provide their own implementation of the
[`writable._write()`](#writable_writechunk-encoding-callback).

This function MUST NOT be called by application code directly. It should be
implemented by child classes, and called by the internal `Writable` class
methods only.

The `callback` function must be called synchronously inside of
`writable._write()` or asynchronously (i.e. different tick) to signal either
that the write completed successfully or failed with an error.
The first argument passed to the `callback` must be the `Error` object if the
call failed or `null` if the write succeeded.

All calls to `writable.write()` that occur between the time `writable._write()`
is called and the `callback` is called will cause the written data to be
buffered. When the `callback` is invoked, the stream might emit a [`'drain'`](51-stream-part1.md#event-drain)
event. If a stream implementation is capable of processing multiple chunks of
data at once, the `writable._writev()` method should be implemented.

If the `decodeStrings` property is explicitly set to `false` in the constructor
options, then `chunk` will remain the same object that is passed to `.write()`,
and may be a string rather than a `Buffer`. This is to support implementations
that have an optimized handling for certain string data encodings. In that case,
the `encoding` argument will indicate the character encoding of the string.
Otherwise, the `encoding` argument can be safely ignored.

The `writable._write()` method is prefixed with an underscore because it is
internal to the class that defines it, and should never be called directly by
user programs.

#### `writable._writev(chunks, callback)`

- `chunks` [`<Object>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Object)[] The data to be written. The value is an array of [`<Object>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Object)
  that each represent a discrete chunk of data to write. The properties of
  these objects are:
  - `chunk` [`<Buffer>`](07-buffer-part1.md#class-buffer) | [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) A buffer instance or string containing the data to
    be written. The `chunk` will be a string if the `Writable` was created with
    the `decodeStrings` option set to `false` and a string was passed to `write()`.
  - `encoding` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) The character encoding of the `chunk`. If `chunk` is
    a `Buffer`, the `encoding` will be `'buffer'`.
- `callback` [`<Function>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Function) A callback function (optionally with an error
  argument) to be invoked when processing is complete for the supplied chunks.

This function MUST NOT be called by application code directly. It should be
implemented by child classes, and called by the internal `Writable` class
methods only.

The `writable._writev()` method may be implemented in addition or alternatively
to `writable._write()` in stream implementations that are capable of processing
multiple chunks of data at once. If implemented and if there is buffered data
from previous writes, `_writev()` will be called instead of `_write()`.

The `writable._writev()` method is prefixed with an underscore because it is
internal to the class that defines it, and should never be called directly by
user programs.

#### `writable._destroy(err, callback)`

Added in: v8.0.0

- `err` [`<Error>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Error) A possible error.
- `callback` [`<Function>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Function) A callback function that takes an optional error
  argument.

The `_destroy()` method is called by [`writable.destroy()`](51-stream-part1.md#writabledestroyerror).
It can be overridden by child classes but it **must not** be called directly.

#### `writable._final(callback)`

Added in: v8.0.0

- `callback` [`<Function>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Function) Call this function (optionally with an error
  argument) when finished writing any remaining data.

The `_final()` method **must not** be called directly. It may be implemented
by child classes, and if so, will be called by the internal `Writable`
class methods only.

This optional function will be called before the stream closes, delaying the
`'finish'` event until `callback` is called. This is useful to close resources
or write buffered data before a stream ends.

#### Errors while writing

Errors occurring during the processing of the [`writable._write()`](#writable_writechunk-encoding-callback),
[`writable._writev()`](#writable_writevchunks-callback) and [`writable._final()`](#writable_finalcallback) methods must be propagated
by invoking the callback and passing the error as the first argument.
Throwing an `Error` from within these methods or manually emitting an `'error'`
event results in undefined behavior.

If a `Readable` stream pipes into a `Writable` stream when `Writable` emits an
error, the `Readable` stream will be unpiped.

```js
const { Writable } = require('node:stream');

const myWritable = new Writable({
  write(chunk, encoding, callback) {
    if (chunk.toString().indexOf('a') >= 0) {
      callback(new Error('chunk is invalid'));
    } else {
      callback();
    }
  },
});
```

#### An example writable stream

The following illustrates a rather simplistic (and somewhat pointless) custom
`Writable` stream implementation. While this specific `Writable` stream instance
is not of any real particular usefulness, the example illustrates each of the
required elements of a custom [`Writable`](51-stream-part1.md#class-streamwritable) stream instance:

```js
const { Writable } = require('node:stream');

class MyWritable extends Writable {
  _write(chunk, encoding, callback) {
    if (chunk.toString().indexOf('a') >= 0) {
      callback(new Error('chunk is invalid'));
    } else {
      callback();
    }
  }
}
```

#### Decoding buffers in a writable stream

Decoding buffers is a common task, for instance, when using transformers whose
input is a string. This is not a trivial process when using multi-byte
characters encoding, such as UTF-8. The following example shows how to decode
multi-byte strings using `StringDecoder` and [`Writable`](51-stream-part1.md#class-streamwritable).

```js
const { Writable } = require('node:stream');
const { StringDecoder } = require('node:string_decoder');

class StringWritable extends Writable {
  constructor(options) {
    super(options);
    this._decoder = new StringDecoder(options?.defaultEncoding);
    this.data = '';
  }
  _write(chunk, encoding, callback) {
    if (encoding === 'buffer') {
      chunk = this._decoder.write(chunk);
    }
    this.data += chunk;
    callback();
  }
  _final(callback) {
    this.data += this._decoder.end();
    callback();
  }
}

const euro = [[0xE2, 0x82], [0xAC]].map(Buffer.from);
const w = new StringWritable();

w.write('currency: ');
w.write(euro[0]);
w.end(euro[1]);

console.log(w.data); // currency: €
```

### Implementing a readable stream

The `stream.Readable` class is extended to implement a [`Readable`](51-stream-part1.md#class-streamreadable) stream.

Custom `Readable` streams *must* call the `new stream.Readable([options])`
constructor and implement the [`readable._read()`](#readable_readsize) method.

#### `new stream.Readable([options])`

**History**

| Version | Changes |
| --- | --- |
| v22.0.0 | bump default highWaterMark. |
| v15.5.0 | support passing in an AbortSignal. |
| v14.0.0 | Change `autoDestroy` option default to `true`. |
| v11.2.0, v10.16.0 | Add `autoDestroy` option to automatically `destroy()` the stream when it emits `'end'` or errors. |

- `options` [`<Object>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Object)
  - `highWaterMark` [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) The maximum [number of bytes](51-stream-part4.md#highwatermark-discrepancy-after-calling-readablesetencoding) to store
    in the internal buffer before ceasing to read from the underlying resource.
    **Default:** See [`stream.getDefaultHighWaterMark()`](#streamgetdefaulthighwatermarkobjectmode).
  - `encoding` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) If specified, then buffers will be decoded to
    strings using the specified encoding. **Default:** `null`.
  - `objectMode` [`<boolean>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#boolean_type) Whether this stream should behave
    as a stream of objects. Meaning that [`stream.read(n)`](51-stream-part1.md#readablereadsize) returns
    a single value instead of a `Buffer` of size `n`. **Default:** `false`.
  - `emitClose` [`<boolean>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#boolean_type) Whether or not the stream should emit `'close'`
    after it has been destroyed. **Default:** `true`.
  - `read` [`<Function>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Function) Implementation for the [`stream._read()`](#readable_readsize)
    method.
  - `destroy` [`<Function>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Function) Implementation for the [`stream._destroy()`](#readable_destroyerr-callback) method.
  - `construct` [`<Function>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Function) Implementation for the [`stream._construct()`](#readable_constructcallback) method.
  - `autoDestroy` [`<boolean>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#boolean_type) Whether this stream should automatically call `.destroy()` on itself after ending. **Default:** `true`.
  - `signal` [`<AbortSignal>`](26-globals.md#class-abortsignal) A signal representing possible cancellation.

```js
const { Readable } = require('node:stream');

class MyReadable extends Readable {
  constructor(options) {
    // Calls the stream.Readable(options) constructor.
    super(options);
    // ...
  }
}
```

Or, using the simplified constructor approach:

```js
const { Readable } = require('node:stream');

const myReadable = new Readable({
  read(size) {
    // ...
  },
});
```

Calling `abort` on the `AbortController` corresponding to the passed
`AbortSignal` will behave the same way as calling `.destroy(new AbortError())`
on the readable created.

```js
const { Readable } = require('node:stream');
const controller = new AbortController();
const read = new Readable({
  read(size) {
    // ...
  },
  signal: controller.signal,
});
// Later, abort the operation closing the stream
controller.abort();
```

#### `readable._construct(callback)`

Added in: v15.0.0

- `callback` [`<Function>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Function) Call this function (optionally with an error
  argument) when the stream has finished initializing.

The `_construct()` method MUST NOT be called directly. It may be implemented
by child classes, and if so, will be called by the internal `Readable`
class methods only.

This optional function will be scheduled in the next tick by the stream
constructor, delaying any `_read()` and `_destroy()` calls until `callback` is
called. This is useful to initialize state or asynchronously initialize
resources before the stream can be used.

```js
const { Readable } = require('node:stream');
const fs = require('node:fs');

class ReadStream extends Readable {
  constructor(filename) {
    super();
    this.filename = filename;
    this.fd = null;
  }
  _construct(callback) {
    fs.open(this.filename, (err, fd) => {
      if (err) {
        callback(err);
      } else {
        this.fd = fd;
        callback();
      }
    });
  }
  _read(n) {
    const buf = Buffer.alloc(n);
    fs.read(this.fd, buf, 0, n, null, (err, bytesRead) => {
      if (err) {
        this.destroy(err);
      } else {
        this.push(bytesRead > 0 ? buf.slice(0, bytesRead) : null);
      }
    });
  }
  _destroy(err, callback) {
    if (this.fd) {
      fs.close(this.fd, (er) => callback(er || err));
    } else {
      callback(err);
    }
  }
}
```

#### `readable._read(size)`

Added in: v0.9.4

- `size` [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) Number of bytes to read asynchronously

This function MUST NOT be called by application code directly. It should be
implemented by child classes, and called by the internal `Readable` class
methods only.

All `Readable` stream implementations must provide an implementation of the
[`readable._read()`](#readable_readsize) method to fetch data from the underlying resource.

When [`readable._read()`](#readable_readsize) is called, if data is available from the resource,
the implementation should begin pushing that data into the read queue using the
[`this.push(dataChunk)`](#readablepushchunk-encoding) method. `_read()` will be called again
after each call to [`this.push(dataChunk)`](#readablepushchunk-encoding) once the stream is
ready to accept more data. `_read()` may continue reading from the resource and
pushing data until `readable.push()` returns `false`. Only when `_read()` is
called again after it has stopped should it resume pushing additional data into
the queue.

Once the [`readable._read()`](#readable_readsize) method has been called, it will not be called
again until more data is pushed through the [`readable.push()`](#readablepushchunk-encoding)
method. Empty data such as empty buffers and strings will not cause
[`readable._read()`](#readable_readsize) to be called.

The `size` argument is advisory. For implementations where a "read" is a
single operation that returns data can use the `size` argument to determine how
much data to fetch. Other implementations may ignore this argument and simply
provide data whenever it becomes available. There is no need to "wait" until
`size` bytes are available before calling [`stream.push(chunk)`](#readablepushchunk-encoding).

The [`readable._read()`](#readable_readsize) method is prefixed with an underscore because it is
internal to the class that defines it, and should never be called directly by
user programs.

#### `readable._destroy(err, callback)`

Added in: v8.0.0

- `err` [`<Error>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Error) A possible error.
- `callback` [`<Function>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Function) A callback function that takes an optional error
  argument.

The `_destroy()` method is called by [`readable.destroy()`](51-stream-part1.md#readabledestroyerror).
It can be overridden by child classes but it **must not** be called directly.

#### `readable.push(chunk[, encoding])`

**History**

| Version | Changes |
| --- | --- |
| v22.0.0, v20.13.0 | The `chunk` argument can now be a `TypedArray` or `DataView` instance. |
| v8.0.0 | The `chunk` argument can now be a `Uint8Array` instance. |

- `chunk` [`<Buffer>`](07-buffer-part1.md#class-buffer) | [`<TypedArray>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/TypedArray) | [`<DataView>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/DataView) | [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) | [`<null>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#null_type) | [`<any>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#Data_types) Chunk of data to push
  into the read queue. For streams not operating in object mode, `chunk` must
  be a [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type), [`<Buffer>`](07-buffer-part1.md#class-buffer), [`<TypedArray>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/TypedArray) or [`<DataView>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/DataView). For object mode streams, `chunk` may be any JavaScript value.
- `encoding` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) Encoding of string chunks. Must be a valid `Buffer` encoding, such as `'utf8'` or `'ascii'`.
- Returns: [`<boolean>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#boolean_type) `true` if additional chunks of data may continue to be
  pushed; `false` otherwise.

When `chunk` is a [`<Buffer>`](07-buffer-part1.md#class-buffer), [`<TypedArray>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/TypedArray), [`<DataView>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/DataView) or [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type), the `chunk`
of data will be added to the internal queue for users of the stream to consume.
Passing `chunk` as `null` signals the end of the stream (EOF), after which no
more data can be written.

When the `Readable` is operating in paused mode, the data added with
`readable.push()` can be read out by calling the
[`readable.read()`](51-stream-part1.md#readablereadsize) method when the [`'readable'`](51-stream-part1.md#event-readable) event is
emitted.

When the `Readable` is operating in flowing mode, the data added with
`readable.push()` will be delivered by emitting a `'data'` event.

The `readable.push()` method is designed to be as flexible as possible. For
example, when wrapping a lower-level source that provides some form of
pause/resume mechanism, and a data callback, the low-level source can be wrapped
by the custom `Readable` instance:

```js
// `_source` is an object with readStop() and readStart() methods,
// and an `ondata` member that gets called when it has data, and
// an `onend` member that gets called when the data is over.

class SourceWrapper extends Readable {
  constructor(options) {
    super(options);

    this._source = getLowLevelSourceObject();

    // Every time there's data, push it into the internal buffer.
    this._source.ondata = (chunk) => {
      // If push() returns false, then stop reading from source.
      if (!this.push(chunk))
        this._source.readStop();
    };

    // When the source ends, push the EOF-signaling `null` chunk.
    this._source.onend = () => {
      this.push(null);
    };
  }
  // _read() will be called when the stream wants to pull more data in.
  // The advisory size argument is ignored in this case.
  _read(size) {
    this._source.readStart();
  }
}
```

The `readable.push()` method is used to push the content
into the internal buffer. It can be driven by the [`readable._read()`](#readable_readsize) method.

For streams not operating in object mode, if the `chunk` parameter of
`readable.push()` is `undefined`, it will be treated as empty string or
buffer. See [`readable.push('')`](51-stream-part4.md#readablepush) for more information.

#### Errors while reading

Errors occurring during processing of the [`readable._read()`](#readable_readsize) must be
propagated through the [`readable.destroy(err)`](#readable_destroyerr-callback) method.
Throwing an `Error` from within [`readable._read()`](#readable_readsize) or manually emitting an
`'error'` event results in undefined behavior.

```js
const { Readable } = require('node:stream');

const myReadable = new Readable({
  read(size) {
    const err = checkSomeErrorCondition();
    if (err) {
      this.destroy(err);
    } else {
      // Do some work.
    }
  },
});
```

#### An example counting stream

The following is a basic example of a `Readable` stream that emits the numerals
from 1 to 1,000,000 in ascending order, and then ends.

```js
const { Readable } = require('node:stream');

class Counter extends Readable {
  constructor(opt) {
    super(opt);
    this._max = 1000000;
    this._index = 1;
  }

  _read() {
    const i = this._index++;
    if (i > this._max)
      this.push(null);
    else {
      const str = String(i);
      const buf = Buffer.from(str, 'ascii');
      this.push(buf);
    }
  }
}
```

### Implementing a duplex stream

A [`Duplex`](51-stream-part2.md#class-streamduplex) stream is one that implements both [`Readable`](51-stream-part1.md#class-streamreadable) and
[`Writable`](51-stream-part1.md#class-streamwritable), such as a TCP socket connection.

Because JavaScript does not have support for multiple inheritance, the
`stream.Duplex` class is extended to implement a [`Duplex`](51-stream-part2.md#class-streamduplex) stream (as opposed
to extending the `stream.Readable` *and* `stream.Writable` classes).

The `stream.Duplex` class prototypically inherits from `stream.Readable` and
parasitically from `stream.Writable`, but `instanceof` will work properly for
both base classes due to overriding [`Symbol.hasInstance`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Symbol/hasInstance) on
`stream.Writable`.

Custom `Duplex` streams *must* call the `new stream.Duplex([options])`
constructor and implement *both* the [`readable._read()`](#readable_readsize) and
`writable._write()` methods.

#### `new stream.Duplex(options)`

**History**

| Version | Changes |
| --- | --- |
| v8.4.0 | The `readableHighWaterMark` and `writableHighWaterMark` options are supported now. |

- `options` [`<Object>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Object) Passed to both `Writable` and `Readable`
  constructors. Also has the following fields:
  - `allowHalfOpen` [`<boolean>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#boolean_type) If set to `false`, then the stream will
    automatically end the writable side when the readable side ends.
    **Default:** `true`.
  - `readable` [`<boolean>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#boolean_type) Sets whether the `Duplex` should be readable.
    **Default:** `true`.
  - `writable` [`<boolean>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#boolean_type) Sets whether the `Duplex` should be writable.
    **Default:** `true`.
  - `readableObjectMode` [`<boolean>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#boolean_type) Sets `objectMode` for readable side of the
    stream. Has no effect if `objectMode` is `true`. **Default:** `false`.
  - `writableObjectMode` [`<boolean>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#boolean_type) Sets `objectMode` for writable side of the
    stream. Has no effect if `objectMode` is `true`. **Default:** `false`.
  - `readableHighWaterMark` [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) Sets `highWaterMark` for the readable side
    of the stream. Has no effect if `highWaterMark` is provided.
  - `writableHighWaterMark` [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) Sets `highWaterMark` for the writable side
    of the stream. Has no effect if `highWaterMark` is provided.

*CommonJS version:*

```js
const { Duplex } = require('node:stream');

class MyDuplex extends Duplex {
  constructor(options) {
    super(options);
    // ...
  }
}
```

*ES module version:*

```js
import { Duplex } from 'node:stream';

class MyDuplex extends Duplex {
  constructor(options) {
    super(options);
    // ...
  }
}
```

Or, using the simplified constructor approach:

```js
const { Duplex } = require('node:stream');

const myDuplex = new Duplex({
  read(size) {
    // ...
  },
  write(chunk, encoding, callback) {
    // ...
  },
});
```

When using pipeline:

```js
const { Transform, pipeline } = require('node:stream');
const fs = require('node:fs');

pipeline(
  fs.createReadStream('object.json')
    .setEncoding('utf8'),
  new Transform({
    decodeStrings: false, // Accept string input rather than Buffers
    construct(callback) {
      this.data = '';
      callback();
    },
    transform(chunk, encoding, callback) {
      this.data += chunk;
      callback();
    },
    flush(callback) {
      try {
        // Make sure is valid json.
        JSON.parse(this.data);
        this.push(this.data);
        callback();
      } catch (err) {
        callback(err);
      }
    },
  }),
  fs.createWriteStream('valid-object.json'),
  (err) => {
    if (err) {
      console.error('failed', err);
    } else {
      console.log('completed');
    }
  },
);
```

#### An example duplex stream

The following illustrates a simple example of a `Duplex` stream that wraps a
hypothetical lower-level source object to which data can be written, and
from which data can be read, albeit using an API that is not compatible with
Node.js streams.
The following illustrates a simple example of a `Duplex` stream that buffers
incoming written data via the [`Writable`](51-stream-part1.md#class-streamwritable) interface that is read back out
via the [`Readable`](51-stream-part1.md#class-streamreadable) interface.

```js
const { Duplex } = require('node:stream');
const kSource = Symbol('source');

class MyDuplex extends Duplex {
  constructor(source, options) {
    super(options);
    this[kSource] = source;
  }

  _write(chunk, encoding, callback) {
    // The underlying source only deals with strings.
    if (Buffer.isBuffer(chunk))
      chunk = chunk.toString();
    this[kSource].writeSomeData(chunk);
    callback();
  }

  _read(size) {
    this[kSource].fetchSomeData(size, (data, encoding) => {
      this.push(Buffer.from(data, encoding));
    });
  }
}
```

The most important aspect of a `Duplex` stream is that the `Readable` and
`Writable` sides operate independently of one another despite co-existing within
a single object instance.

#### Object mode duplex streams

For `Duplex` streams, `objectMode` can be set exclusively for either the
`Readable` or `Writable` side using the `readableObjectMode` and
`writableObjectMode` options respectively.

In the following example, for instance, a new `Transform` stream (which is a
type of [`Duplex`](51-stream-part2.md#class-streamduplex) stream) is created that has an object mode `Writable` side
that accepts JavaScript numbers that are converted to hexadecimal strings on
the `Readable` side.

```js
const { Transform } = require('node:stream');

// All Transform streams are also Duplex Streams.
const myTransform = new Transform({
  writableObjectMode: true,

  transform(chunk, encoding, callback) {
    // Coerce the chunk to a number if necessary.
    chunk |= 0;

    // Transform the chunk into something else.
    const data = chunk.toString(16);

    // Push the data onto the readable queue.
    callback(null, '0'.repeat(data.length % 2) + data);
  },
});

myTransform.setEncoding('ascii');
myTransform.on('data', (chunk) => console.log(chunk));

myTransform.write(1);
// Prints: 01
myTransform.write(10);
// Prints: 0a
myTransform.write(100);
// Prints: 64
```

### Implementing a transform stream

A [`Transform`](51-stream-part2.md#class-streamtransform) stream is a [`Duplex`](51-stream-part2.md#class-streamduplex) stream where the output is computed
in some way from the input. Examples include [zlib](68-zlib.md) streams or [crypto](15-crypto-part1.md)
streams that compress, encrypt, or decrypt data.

There is no requirement that the output be the same size as the input, the same
number of chunks, or arrive at the same time. For example, a `Hash` stream will
only ever have a single chunk of output which is provided when the input is
ended. A `zlib` stream will produce output that is either much smaller or much
larger than its input.

The `stream.Transform` class is extended to implement a [`Transform`](51-stream-part2.md#class-streamtransform) stream.

The `stream.Transform` class prototypically inherits from `stream.Duplex` and
implements its own versions of the `writable._write()` and
[`readable._read()`](#readable_readsize) methods. Custom `Transform` implementations *must*
implement the [`transform._transform()`](#transform_transformchunk-encoding-callback) method and *may*
also implement the [`transform._flush()`](#transform_flushcallback) method.

Care must be taken when using `Transform` streams in that data written to the
stream can cause the `Writable` side of the stream to become paused if the
output on the `Readable` side is not consumed.

#### `new stream.Transform([options])`

- `options` [`<Object>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Object) Passed to both `Writable` and `Readable`
  constructors. Also has the following fields:
  - `transform` [`<Function>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Function) Implementation for the [`stream._transform()`](#transform_transformchunk-encoding-callback) method.
  - `flush` [`<Function>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Function) Implementation for the [`stream._flush()`](#transform_flushcallback)
    method.

*CommonJS version:*

```js
const { Transform } = require('node:stream');

class MyTransform extends Transform {
  constructor(options) {
    super(options);
    // ...
  }
}
```

*ES module version:*

```js
import { Transform } from 'node:stream';

class MyTransform extends Transform {
  constructor(options) {
    super(options);
    // ...
  }
}
```

Or, using the simplified constructor approach:

```js
const { Transform } = require('node:stream');

const myTransform = new Transform({
  transform(chunk, encoding, callback) {
    // ...
  },
});
```

#### Event: `'end'`

The [`'end'`](51-stream-part1.md#event-end) event is from the `stream.Readable` class. The `'end'` event is
emitted after all data has been output, which occurs after the callback in
[`transform._flush()`](#transform_flushcallback) has been called. In the case of an error,
`'end'` should not be emitted.

#### Event: `'finish'`

The [`'finish'`](51-stream-part1.md#event-finish) event is from the `stream.Writable` class. The `'finish'`
event is emitted after [`stream.end()`](51-stream-part1.md#writableendchunk-encoding-callback) is called and all chunks
have been processed by [`stream._transform()`](#transform_transformchunk-encoding-callback). In the case
of an error, `'finish'` should not be emitted.

#### `transform._flush(callback)`

- `callback` [`<Function>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Function) A callback function (optionally with an error
  argument and data) to be called when remaining data has been flushed.

This function MUST NOT be called by application code directly. It should be
implemented by child classes, and called by the internal `Readable` class
methods only.

In some cases, a transform operation may need to emit an additional bit of
data at the end of the stream. For example, a `zlib` compression stream will
store an amount of internal state used to optimally compress the output. When
the stream ends, however, that additional data needs to be flushed so that the
compressed data will be complete.

Custom [`Transform`](51-stream-part2.md#class-streamtransform) implementations *may* implement the `transform._flush()`
method. This will be called when there is no more written data to be consumed,
but before the [`'end'`](51-stream-part1.md#event-end) event is emitted signaling the end of the
[`Readable`](51-stream-part1.md#class-streamreadable) stream.

Within the `transform._flush()` implementation, the `transform.push()` method
may be called zero or more times, as appropriate. The `callback` function must
be called when the flush operation is complete.

The `transform._flush()` method is prefixed with an underscore because it is
internal to the class that defines it, and should never be called directly by
user programs.

#### `transform._transform(chunk, encoding, callback)`

- `chunk` [`<Buffer>`](07-buffer-part1.md#class-buffer) | [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) | [`<any>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#Data_types) The `Buffer` to be transformed, converted from
  the `string` passed to [`stream.write()`](51-stream-part1.md#writablewritechunk-encoding-callback). If the stream's
  `decodeStrings` option is `false` or the stream is operating in object mode,
  the chunk will not be converted & will be whatever was passed to
  [`stream.write()`](51-stream-part1.md#writablewritechunk-encoding-callback).
- `encoding` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) If the chunk is a string, then this is the
  encoding type. If chunk is a buffer, then this is the special
  value `'buffer'`. Ignore it in that case.
- `callback` [`<Function>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Function) A callback function (optionally with an error
  argument and data) to be called after the supplied `chunk` has been
  processed.

This function MUST NOT be called by application code directly. It should be
implemented by child classes, and called by the internal `Readable` class
methods only.

All `Transform` stream implementations must provide a `_transform()`
method to accept input and produce output. The `transform._transform()`
implementation handles the bytes being written, computes an output, then passes
that output off to the readable portion using the `transform.push()` method.

The `transform.push()` method may be called zero or more times to generate
output from a single input chunk, depending on how much is to be output
as a result of the chunk.

It is possible that no output is generated from any given chunk of input data.

The `callback` function must be called only when the current chunk is completely
consumed. The first argument passed to the `callback` must be an `Error` object
if an error occurred while processing the input or `null` otherwise. If a second
argument is passed to the `callback`, it will be forwarded on to the
`transform.push()` method, but only if the first argument is falsy. In other
words, the following are equivalent:

```js
transform.prototype._transform = function(data, encoding, callback) {
  this.push(data);
  callback();
};

transform.prototype._transform = function(data, encoding, callback) {
  callback(null, data);
};
```

The `transform._transform()` method is prefixed with an underscore because it
is internal to the class that defines it, and should never be called directly by
user programs.

`transform._transform()` is never called in parallel; streams implement a
queue mechanism, and to receive the next chunk, `callback` must be
called, either synchronously or asynchronously.

#### Class: `stream.PassThrough`

The `stream.PassThrough` class is a trivial implementation of a [`Transform`](51-stream-part2.md#class-streamtransform)
stream that simply passes the input bytes across to the output. Its purpose is
primarily for examples and testing, but there are some use cases where
`stream.PassThrough` is useful as a building block for novel sorts of streams.

## Additional notes

### Streams compatibility with async generators and async iterators

With the support of async generators and iterators in JavaScript, async
generators are effectively a first-class language-level stream construct at
this point.

Some common interop cases of using Node.js streams with async generators
and async iterators are provided below.

#### Consuming readable streams with async iterators

```js
(async function() {
  for await (const chunk of readable) {
    console.log(chunk);
  }
})();
```

Async iterators register a permanent error handler on the stream to prevent any
unhandled post-destroy errors.

#### Creating readable streams with async generators

A Node.js readable stream can be created from an asynchronous generator using
the `Readable.from()` utility method:

```js
const { Readable } = require('node:stream');

const ac = new AbortController();
const signal = ac.signal;

async function * generate() {
  yield 'a';
  await someLongRunningFn({ signal });
  yield 'b';
  yield 'c';
}

const readable = Readable.from(generate());
readable.on('close', () => {
  ac.abort();
});

readable.on('data', (chunk) => {
  console.log(chunk);
});
```

#### Piping to writable streams from async iterators

When writing to a writable stream from an async iterator, ensure correct
handling of backpressure and errors. [`stream.pipeline()`](51-stream-part2.md#streampipelinesource-transforms-destination-callback) abstracts away
the handling of backpressure and backpressure-related errors:

```js
const fs = require('node:fs');
const { pipeline } = require('node:stream');
const { pipeline: pipelinePromise } = require('node:stream/promises');

const writable = fs.createWriteStream('./file');

const ac = new AbortController();
const signal = ac.signal;

const iterator = createIterator({ signal });

// Callback Pattern
pipeline(iterator, writable, (err, value) => {
  if (err) {
    console.error(err);
  } else {
    console.log(value, 'value returned');
  }
}).on('close', () => {
  ac.abort();
});

// Promise Pattern
pipelinePromise(iterator, writable)
  .then((value) => {
    console.log(value, 'value returned');
  })
  .catch((err) => {
    console.error(err);
    ac.abort();
  });
```

[Contents](../README.md) | [Previous: Stream, part 2 of 4](51-stream-part2.md) | [Next: Stream, part 4 of 4](51-stream-part4.md)
