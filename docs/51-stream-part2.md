[Contents](../README.md) | [Previous: Stream, part 1 of 4](51-stream-part1.md) | [Next: Stream, part 3 of 4](51-stream-part3.md)

Source: [https://nodejs.org/docs/latest/api/stream.html](https://nodejs.org/docs/latest/api/stream.html)

Part 2 of 4 of this section.

# Stream (part 2 of 4)

Continues: API for stream consumers. Subsection: Readable streams.

##### `readable.readableEncoding`

Added in: v12.7.0

- Type: [`<null>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#null_type) | [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type)

Getter for the property `encoding` of a given `Readable` stream. The `encoding`
property can be set using the [`readable.setEncoding()`](#readablesetencodingencoding) method.

##### `readable.readableEnded`

Added in: v12.9.0

- Type: [`<boolean>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#boolean_type)

Becomes `true` when [`'end'`](51-stream-part1.md#event-end) event is emitted.

##### `readable.errored`

Added in: v18.0.0

- Type: [`<Error>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Error)

Returns error if the stream has been destroyed with an error.

##### `readable.readableFlowing`

Added in: v9.4.0

- Type: [`<boolean>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#boolean_type)

This property reflects the current state of a `Readable` stream as described
in the [Three states](51-stream-part1.md#three-states) section.

##### `readable.readableHighWaterMark`

Added in: v9.3.0

- Type: [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type)

Returns the value of `highWaterMark` passed when creating this `Readable`.

##### `readable.readableLength`

Added in: v9.4.0

- Type: [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type)

This property contains the number of bytes (or objects) in the queue
ready to be read. The value provides introspection data regarding
the status of the `highWaterMark`.

##### `readable.readableObjectMode`

Added in: v12.3.0

- Type: [`<boolean>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#boolean_type)

Getter for the property `objectMode` of a given `Readable` stream.

##### `readable.resume()`

Added in: v0.9.4

**History**

| Version | Changes |
| --- | --- |
| v10.0.0 | The `resume()` has no effect if there is a `'readable'` event listening. |

- Returns: [`<this>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Operators/this)

The `readable.resume()` method causes an explicitly paused `Readable` stream to
resume emitting [`'data'`](51-stream-part1.md#event-data) events, switching the stream into flowing mode.

The `readable.resume()` method can be used to fully consume the data from a
stream without actually processing any of that data:

```js
getReadableStreamSomehow()
  .resume()
  .on('end', () => {
    console.log('Reached the end, but did not read anything.');
  });
```

The `readable.resume()` method has no effect if there is a `'readable'`
event listener.

##### `readable.setEncoding(encoding)`

Added in: v0.9.4

- `encoding` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) The encoding to use.
- Returns: [`<this>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Operators/this)

The `readable.setEncoding()` method sets the character encoding for
data read from the `Readable` stream.

By default, no encoding is assigned and stream data will be returned as
`Buffer` objects. Setting an encoding causes the stream data
to be returned as strings of the specified encoding rather than as `Buffer`
objects. For instance, calling `readable.setEncoding('utf8')` will cause the
output data to be interpreted as UTF-8 data, and passed as strings. Calling
`readable.setEncoding('hex')` will cause the data to be encoded in hexadecimal
string format.

The `Readable` stream will properly handle multi-byte characters delivered
through the stream that would otherwise become improperly decoded if simply
pulled from the stream as `Buffer` objects.

```js
const readable = getReadableStreamSomehow();
readable.setEncoding('utf8');
readable.on('data', (chunk) => {
  assert.equal(typeof chunk, 'string');
  console.log('Got %d characters of string data:', chunk.length);
});
```

##### `readable.unpipe([destination])`

Added in: v0.9.4

- `destination` [`<stream.Writable>`](51-stream-part1.md#class-streamwritable) Optional specific stream to unpipe
- Returns: [`<this>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Operators/this)

The `readable.unpipe()` method detaches a `Writable` stream previously attached
using the [`stream.pipe()`](51-stream-part1.md#readablepipedestination-options) method.

If the `destination` is not specified, then *all* pipes are detached.

If the `destination` is specified, but no pipe is set up for it, then
the method does nothing.

```js
const fs = require('node:fs');
const readable = getReadableStreamSomehow();
const writable = fs.createWriteStream('file.txt');
// All the data from readable goes into 'file.txt',
// but only for the first second.
readable.pipe(writable);
setTimeout(() => {
  console.log('Stop writing to file.txt.');
  readable.unpipe(writable);
  console.log('Manually close the file stream.');
  writable.end();
}, 1000);
```

##### `readable.unshift(chunk[, encoding])`

Added in: v0.9.11

**History**

| Version | Changes |
| --- | --- |
| v22.0.0, v20.13.0 | The `chunk` argument can now be a `TypedArray` or `DataView` instance. |
| v8.0.0 | The `chunk` argument can now be a `Uint8Array` instance. |

- `chunk` [`<Buffer>`](07-buffer-part1.md#class-buffer) | [`<TypedArray>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/TypedArray) | [`<DataView>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/DataView) | [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) | [`<null>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#null_type) | [`<any>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#Data_types) Chunk of data to unshift
  onto the read queue. For streams not operating in object mode, `chunk` must
  be a [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type), [`<Buffer>`](07-buffer-part1.md#class-buffer), [`<TypedArray>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/TypedArray), [`<DataView>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/DataView) or `null`.
  For object mode streams, `chunk` may be any JavaScript value.
- `encoding` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) Encoding of string chunks. Must be a valid `Buffer` encoding, such as `'utf8'` or `'ascii'`.

Passing `chunk` as `null` signals the end of the stream (EOF) and behaves the
same as `readable.push(null)`, after which no more data can be written. The EOF
signal is put at the end of the buffer and any buffered data will still be
flushed.

The `readable.unshift()` method pushes a chunk of data back into the internal
buffer. This is useful in certain situations where a stream is being consumed by
code that needs to "un-consume" some amount of data that it has optimistically
pulled out of the source, so that the data can be passed on to some other party.

The `stream.unshift(chunk)` method cannot be called after the [`'end'`](51-stream-part1.md#event-end) event
has been emitted or a runtime error will be thrown.

Developers using `stream.unshift()` often should consider switching to
use of a [`Transform`](#class-streamtransform) stream instead. See the [API for stream implementers](51-stream-part3.md#api-for-stream-implementers)
section for more information.

```js
// Pull off a header delimited by \n\n.
// Use unshift() if we get too much.
// Call the callback with (error, header, stream).
const { StringDecoder } = require('node:string_decoder');
function parseHeader(stream, callback) {
  stream.on('error', callback);
  stream.on('readable', onReadable);
  const decoder = new StringDecoder('utf8');
  let header = '';
  function onReadable() {
    let chunk;
    while (null !== (chunk = stream.read())) {
      const str = decoder.write(chunk);
      if (str.includes('\n\n')) {
        // Found the header boundary.
        const split = str.split(/\n\n/);
        header += split.shift();
        const remaining = split.join('\n\n');
        const buf = Buffer.from(remaining, 'utf8');
        stream.removeListener('error', callback);
        // Remove the 'readable' listener before unshifting.
        stream.removeListener('readable', onReadable);
        if (buf.length)
          stream.unshift(buf);
        // Now the body of the message can be read from the stream.
        callback(null, header, stream);
        return;
      }
      // Still reading the header.
      header += str;
    }
  }
}
```

Unlike [`stream.push(chunk)`](51-stream-part3.md#readablepushchunk-encoding), `stream.unshift(chunk)` will not
end the reading process by resetting the internal reading state of the stream.
This can cause unexpected results if `readable.unshift()` is called during a
read (i.e. from within a [`stream._read()`](51-stream-part3.md#readable_readsize) implementation on a
custom stream). Following the call to `readable.unshift()` with an immediate
[`stream.push('')`](51-stream-part3.md#readablepushchunk-encoding) will reset the reading state appropriately,
however it is best to simply avoid calling `readable.unshift()` while in the
process of performing a read.

##### `readable.wrap(stream)`

Added in: v0.9.4

- `stream` [`<Stream>`](51-stream-part1.md#stream) An "old style" readable stream
- Returns: [`<this>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Operators/this)

Prior to Node.js 0.10, streams did not implement the entire `node:stream`
module API as it is currently defined. (See [Compatibility](51-stream-part4.md#compatibility-with-older-nodejs-versions) for more
information.)

When using an older Node.js library that emits [`'data'`](51-stream-part1.md#event-data) events and has a
[`stream.pause()`](51-stream-part1.md#readablepause) method that is advisory only, the
`readable.wrap()` method can be used to create a [`Readable`](51-stream-part1.md#class-streamreadable) stream that uses
the old stream as its data source.

It will rarely be necessary to use `readable.wrap()` but the method has been
provided as a convenience for interacting with older Node.js applications and
libraries.

```js
const { OldReader } = require('./old-api-module.js');
const { Readable } = require('node:stream');
const oreader = new OldReader();
const myReader = new Readable().wrap(oreader);

myReader.on('readable', () => {
  myReader.read(); // etc.
});
```

##### `readable[Symbol.asyncIterator]()`

Added in: v10.0.0

**History**

| Version | Changes |
| --- | --- |
| v11.14.0 | Symbol.asyncIterator support is no longer experimental. |

- Returns: [`<AsyncIterator>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/AsyncIterator) to fully consume the stream.

```js
const fs = require('node:fs');

async function print(readable) {
  readable.setEncoding('utf8');
  let data = '';
  for await (const chunk of readable) {
    data += chunk;
  }
  console.log(data);
}

print(fs.createReadStream('file')).catch(console.error);
```

If the loop terminates with a `break`, `return`, or a `throw`, the stream will
be destroyed. In other terms, iterating over a stream will consume the stream
fully. The stream will be read in chunks of size equal to the `highWaterMark`
option. In the code example above, data will be in a single chunk if the file
has less than 64 KiB of data because no `highWaterMark` option is provided to
[`fs.createReadStream()`](24-fs-part3.md#fscreatereadstreampath-options).

##### `readable[Symbol.for('Stream.toAsyncStreamable')]()`

Added in: v26.1.0

Stability: 1 - Experimental

- Returns: [`<AsyncIterable>`](https://tc39.github.io/ecma262/#sec-asynciterable-interface) An `AsyncIterable<Uint8Array[]>` that yields
  batched chunks from the stream.

When the `--experimental-stream-iter` flag is enabled, `Readable` streams
implement the [`Stream.toAsyncStreamable`](32-stream-iter.md#streamtoasyncstreamable) protocol, enabling efficient
consumption by the [`stream/iter`](32-stream-iter.md) API.

This provides a batched async iterator that drains the stream's internal
buffer into `Uint8Array[]` batches, amortizing the per-chunk Promise overhead
of the standard `Symbol.asyncIterator` path. For byte-mode streams, chunks
are yielded directly as `Buffer` instances (which are `Uint8Array` subclasses).
For object-mode or encoded streams, each chunk is normalized to `Uint8Array`
before batching.

The returned iterator is tagged as a validated source, so [`from()`](32-stream-iter.md#frominput)
passes it through without additional normalization.

*ES module version:*

```js
import { Readable } from 'node:stream';
import { text, from } from 'node:stream/iter';

const readable = new Readable({
  read() { this.push('hello'); this.push(null); },
});

// Readable is automatically consumed via toAsyncStreamable
console.log(await text(from(readable))); // 'hello'
```

*CommonJS version:*

```js
const { Readable } = require('node:stream');
const { text, from } = require('node:stream/iter');

async function run() {
  const readable = new Readable({
    read() { this.push('hello'); this.push(null); },
  });

  console.log(await text(from(readable))); // 'hello'
}

run().catch(console.error);
```

Without the `--experimental-stream-iter` flag, calling this method throws
[`ERR_STREAM_ITER_MISSING_FLAG`](22-errors-part3.md#err_stream_iter_missing_flag).

##### `readable[Symbol.asyncDispose]()`

Added in: v20.4.0, v18.18.0

**History**

| Version | Changes |
| --- | --- |
| v24.2.0 | No longer experimental. |

Calls [`readable.destroy()`](51-stream-part1.md#readabledestroyerror) with an `AbortError` and returns
a promise that fulfills when the stream is finished.

##### `readable.compose(stream[, options])`

Added in: v19.1.0, v18.13.0

**History**

| Version | Changes |
| --- | --- |
| v24.0.0, v22.17.0 | Marking the API stable. |

- `stream` [`<Writable>`](51-stream-part1.md#class-streamwritable) | [`<Duplex>`](51-stream-part2.md#class-streamduplex) | [`<WritableStream>`](66-webstreams.md#class-writablestream) | [`<TransformStream>`](66-webstreams.md#class-transformstream) | [`<Function>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Function)
- `options` [`<Object>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Object)
  - `signal` [`<AbortSignal>`](26-globals.md#class-abortsignal) allows destroying the stream if the signal is
    aborted.
- Returns: [`<Duplex>`](51-stream-part2.md#class-streamduplex) a stream composed with the stream `stream`.

```js
import { Readable } from 'node:stream';

async function* splitToWords(source) {
  for await (const chunk of source) {
    const words = String(chunk).split(' ');

    for (const word of words) {
      yield word;
    }
  }
}

const wordsStream = Readable.from(['text passed through', 'composed stream']).compose(splitToWords);
const words = await wordsStream.toArray();

console.log(words); // prints ['text', 'passed', 'through', 'composed', 'stream']
```

`readable.compose(s)` is equivalent to `stream.compose(readable, s)`.

This method also allows for an [`<AbortSignal>`](26-globals.md#class-abortsignal) to be provided, which will destroy
the composed stream when aborted.

See [`stream.compose(...streams)`](#streamcomposestreams) for more information.

##### `readable.iterator([options])`

Added in: v16.3.0

**History**

| Version | Changes |
| --- | --- |
| v24.0.0, v22.17.0 | Marking the API stable. |

- `options` [`<Object>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Object)
  - `destroyOnReturn` [`<boolean>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#boolean_type) When set to `false`, calling `return` on the
    async iterator, or exiting a `for await...of` iteration using a `break`,
    `return`, or `throw` will not destroy the stream. **Default:** `true`.
- Returns: [`<AsyncIterator>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/AsyncIterator) to consume the stream.

The iterator created by this method gives users the option to cancel the
destruction of the stream if the `for await...of` loop is exited by `return`,
`break`, or `throw`, or if the iterator should destroy the stream if the stream
emitted an error during iteration.

```js
const { Readable } = require('node:stream');

async function printIterator(readable) {
  for await (const chunk of readable.iterator({ destroyOnReturn: false })) {
    console.log(chunk); // 1
    break;
  }

  console.log(readable.destroyed); // false

  for await (const chunk of readable.iterator({ destroyOnReturn: false })) {
    console.log(chunk); // Will print 2 and then 3
  }

  console.log(readable.destroyed); // True, stream was totally consumed
}

async function printSymbolAsyncIterator(readable) {
  for await (const chunk of readable) {
    console.log(chunk); // 1
    break;
  }

  console.log(readable.destroyed); // true
}

async function showBoth() {
  await printIterator(Readable.from([1, 2, 3]));
  await printSymbolAsyncIterator(Readable.from([1, 2, 3]));
}

showBoth();
```

##### `readable.map(fn[, options])`

Added in: v17.4.0, v16.14.0

**History**

| Version | Changes |
| --- | --- |
| v20.7.0, v18.19.0 | added `highWaterMark` in options. |

Stability: 1 - Experimental

- `fn` [`<Function>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Function) | [`<AsyncFunction>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/AsyncFunction) a function to map over every chunk in the
  stream.
  - `data` [`<any>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#Data_types) a chunk of data from the stream.
  - `options` [`<Object>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Object)
    - `signal` [`<AbortSignal>`](26-globals.md#class-abortsignal) aborted if the stream is destroyed allowing to
      abort the `fn` call early.
- `options` [`<Object>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Object)
  - `concurrency` [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) the maximum concurrent invocation of `fn` to call
    on the stream at once. **Default:** `1`.
  - `highWaterMark` [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) how many items to buffer while waiting for user
    consumption of the mapped items. **Default:** `concurrency * 2 - 1`.
  - `signal` [`<AbortSignal>`](26-globals.md#class-abortsignal) allows destroying the stream if the signal is
    aborted.
- Returns: [`<Readable>`](51-stream-part1.md#class-streamreadable) a stream mapped with the function `fn`.

This method allows mapping over the stream. The `fn` function will be called
for every chunk in the stream. If the `fn` function returns a promise - that
promise will be `await`ed before being passed to the result stream.

```js
import { Readable } from 'node:stream';
import { Resolver } from 'node:dns/promises';

// With a synchronous mapper.
for await (const chunk of Readable.from([1, 2, 3, 4]).map((x) => x * 2)) {
  console.log(chunk); // 2, 4, 6, 8
}
// With an asynchronous mapper, making at most 2 queries at a time.
const resolver = new Resolver();
const dnsResults = Readable.from([
  'nodejs.org',
  'openjsf.org',
  'www.linuxfoundation.org',
]).map((domain) => resolver.resolve4(domain), { concurrency: 2 });
for await (const result of dnsResults) {
  console.log(result); // Logs the DNS result of resolver.resolve4.
}
```

##### `readable.filter(fn[, options])`

Added in: v17.4.0, v16.14.0

**History**

| Version | Changes |
| --- | --- |
| v20.7.0, v18.19.0 | added `highWaterMark` in options. |

Stability: 1 - Experimental

- `fn` [`<Function>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Function) | [`<AsyncFunction>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/AsyncFunction) a function to filter chunks from the stream.
  - `data` [`<any>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#Data_types) a chunk of data from the stream.
  - `options` [`<Object>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Object)
    - `signal` [`<AbortSignal>`](26-globals.md#class-abortsignal) aborted if the stream is destroyed allowing to
      abort the `fn` call early.
- `options` [`<Object>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Object)
  - `concurrency` [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) the maximum concurrent invocation of `fn` to call
    on the stream at once. **Default:** `1`.
  - `highWaterMark` [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) how many items to buffer while waiting for user
    consumption of the filtered items. **Default:** `concurrency * 2 - 1`.
  - `signal` [`<AbortSignal>`](26-globals.md#class-abortsignal) allows destroying the stream if the signal is
    aborted.
- Returns: [`<Readable>`](51-stream-part1.md#class-streamreadable) a stream filtered with the predicate `fn`.

This method allows filtering the stream. For each chunk in the stream the `fn`
function will be called and if it returns a truthy value, the chunk will be
passed to the result stream. If the `fn` function returns a promise - that
promise will be `await`ed.

```js
import { Readable } from 'node:stream';
import { Resolver } from 'node:dns/promises';

// With a synchronous predicate.
for await (const chunk of Readable.from([1, 2, 3, 4]).filter((x) => x > 2)) {
  console.log(chunk); // 3, 4
}
// With an asynchronous predicate, making at most 2 queries at a time.
const resolver = new Resolver();
const dnsResults = Readable.from([
  'nodejs.org',
  'openjsf.org',
  'www.linuxfoundation.org',
]).filter(async (domain) => {
  const { address } = await resolver.resolve4(domain, { ttl: true });
  return address.ttl > 60;
}, { concurrency: 2 });
for await (const result of dnsResults) {
  // Logs domains with more than 60 seconds on the resolved dns record.
  console.log(result);
}
```

##### `readable.forEach(fn[, options])`

Added in: v17.5.0, v16.15.0

Stability: 1 - Experimental

- `fn` [`<Function>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Function) | [`<AsyncFunction>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/AsyncFunction) a function to call on each chunk of the stream.
  - `data` [`<any>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#Data_types) a chunk of data from the stream.
  - `options` [`<Object>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Object)
    - `signal` [`<AbortSignal>`](26-globals.md#class-abortsignal) aborted if the stream is destroyed allowing to
      abort the `fn` call early.
- `options` [`<Object>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Object)
  - `concurrency` [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) the maximum concurrent invocation of `fn` to call
    on the stream at once. **Default:** `1`.
  - `signal` [`<AbortSignal>`](26-globals.md#class-abortsignal) allows destroying the stream if the signal is
    aborted.
- Returns: [`<Promise>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Promise) a promise for when the stream has finished.

This method allows iterating a stream. For each chunk in the stream the
`fn` function will be called. If the `fn` function returns a promise - that
promise will be `await`ed.

This method is different from `for await...of` loops in that it can optionally
process chunks concurrently. In addition, a `forEach` iteration can only be
stopped by having passed a `signal` option and aborting the related
`AbortController` while `for await...of` can be stopped with `break` or
`return`. In either case the stream will be destroyed.

This method is different from listening to the [`'data'`](51-stream-part1.md#event-data) event in that it
uses the [`readable`](51-stream-part1.md#class-streamreadable) event in the underlying machinery and can limit the
number of concurrent `fn` calls.

```js
import { Readable } from 'node:stream';
import { Resolver } from 'node:dns/promises';

// With a synchronous predicate.
for await (const chunk of Readable.from([1, 2, 3, 4]).filter((x) => x > 2)) {
  console.log(chunk); // 3, 4
}
// With an asynchronous predicate, making at most 2 queries at a time.
const resolver = new Resolver();
const dnsResults = Readable.from([
  'nodejs.org',
  'openjsf.org',
  'www.linuxfoundation.org',
]).map(async (domain) => {
  const { address } = await resolver.resolve4(domain, { ttl: true });
  return address;
}, { concurrency: 2 });
await dnsResults.forEach((result) => {
  // Logs result, similar to `for await (const result of dnsResults)`
  console.log(result);
});
console.log('done'); // Stream has finished
```

##### `readable.toArray([options])`

Added in: v17.5.0, v16.15.0

Stability: 1 - Experimental

- `options` [`<Object>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Object)
  - `signal` [`<AbortSignal>`](26-globals.md#class-abortsignal) allows cancelling the toArray operation if the
    signal is aborted.
- Returns: [`<Promise>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Promise) a promise containing an array with the contents of the
  stream.

This method allows easily obtaining the contents of a stream.

As this method reads the entire stream into memory, it negates the benefits of
streams. It's intended for interoperability and convenience, not as the primary
way to consume streams.

```js
import { Readable } from 'node:stream';
import { Resolver } from 'node:dns/promises';

await Readable.from([1, 2, 3, 4]).toArray(); // [1, 2, 3, 4]

const resolver = new Resolver();

// Make dns queries concurrently using .map and collect
// the results into an array using toArray
const dnsResults = await Readable.from([
  'nodejs.org',
  'openjsf.org',
  'www.linuxfoundation.org',
]).map(async (domain) => {
  const { address } = await resolver.resolve4(domain, { ttl: true });
  return address;
}, { concurrency: 2 }).toArray();
```

##### `readable.some(fn[, options])`

Added in: v17.5.0, v16.15.0

Stability: 1 - Experimental

- `fn` [`<Function>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Function) | [`<AsyncFunction>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/AsyncFunction) a function to call on each chunk of the stream.
  - `data` [`<any>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#Data_types) a chunk of data from the stream.
  - `options` [`<Object>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Object)
    - `signal` [`<AbortSignal>`](26-globals.md#class-abortsignal) aborted if the stream is destroyed allowing to
      abort the `fn` call early.
- `options` [`<Object>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Object)
  - `concurrency` [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) the maximum concurrent invocation of `fn` to call
    on the stream at once. **Default:** `1`.
  - `signal` [`<AbortSignal>`](26-globals.md#class-abortsignal) allows destroying the stream if the signal is
    aborted.
- Returns: [`<Promise>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Promise) a promise evaluating to `true` if `fn` returned a truthy
  value for at least one of the chunks.

This method is similar to `Array.prototype.some` and calls `fn` on each chunk
in the stream until the awaited return value is `true` (or any truthy value).
Once an `fn` call on a chunk awaited return value is truthy, the stream is
destroyed and the promise is fulfilled with `true`. If none of the `fn`
calls on the chunks return a truthy value, the promise is fulfilled with
`false`.

```js
import { Readable } from 'node:stream';
import { stat } from 'node:fs/promises';

// With a synchronous predicate.
await Readable.from([1, 2, 3, 4]).some((x) => x > 2); // true
await Readable.from([1, 2, 3, 4]).some((x) => x < 0); // false

// With an asynchronous predicate, making at most 2 file checks at a time.
const anyBigFile = await Readable.from([
  'file1',
  'file2',
  'file3',
]).some(async (fileName) => {
  const stats = await stat(fileName);
  return stats.size > 1024 * 1024;
}, { concurrency: 2 });
console.log(anyBigFile); // `true` if any file in the list is bigger than 1MB
console.log('done'); // Stream has finished
```

##### `readable.find(fn[, options])`

Added in: v17.5.0, v16.17.0

Stability: 1 - Experimental

- `fn` [`<Function>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Function) | [`<AsyncFunction>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/AsyncFunction) a function to call on each chunk of the stream.
  - `data` [`<any>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#Data_types) a chunk of data from the stream.
  - `options` [`<Object>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Object)
    - `signal` [`<AbortSignal>`](26-globals.md#class-abortsignal) aborted if the stream is destroyed allowing to
      abort the `fn` call early.
- `options` [`<Object>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Object)
  - `concurrency` [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) the maximum concurrent invocation of `fn` to call
    on the stream at once. **Default:** `1`.
  - `signal` [`<AbortSignal>`](26-globals.md#class-abortsignal) allows destroying the stream if the signal is
    aborted.
- Returns: [`<Promise>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Promise) a promise evaluating to the first chunk for which `fn`
  evaluated with a truthy value, or `undefined` if no element was found.

This method is similar to `Array.prototype.find` and calls `fn` on each chunk
in the stream to find a chunk with a truthy value for `fn`. Once an `fn` call's
awaited return value is truthy, the stream is destroyed and the promise is
fulfilled with value for which `fn` returned a truthy value. If all of the
`fn` calls on the chunks return a falsy value, the promise is fulfilled with
`undefined`.

```js
import { Readable } from 'node:stream';
import { stat } from 'node:fs/promises';

// With a synchronous predicate.
await Readable.from([1, 2, 3, 4]).find((x) => x > 2); // 3
await Readable.from([1, 2, 3, 4]).find((x) => x > 0); // 1
await Readable.from([1, 2, 3, 4]).find((x) => x > 10); // undefined

// With an asynchronous predicate, making at most 2 file checks at a time.
const foundBigFile = await Readable.from([
  'file1',
  'file2',
  'file3',
]).find(async (fileName) => {
  const stats = await stat(fileName);
  return stats.size > 1024 * 1024;
}, { concurrency: 2 });
console.log(foundBigFile); // File name of large file, if any file in the list is bigger than 1MB
console.log('done'); // Stream has finished
```

##### `readable.every(fn[, options])`

Added in: v17.5.0, v16.15.0

Stability: 1 - Experimental

- `fn` [`<Function>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Function) | [`<AsyncFunction>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/AsyncFunction) a function to call on each chunk of the stream.
  - `data` [`<any>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#Data_types) a chunk of data from the stream.
  - `options` [`<Object>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Object)
    - `signal` [`<AbortSignal>`](26-globals.md#class-abortsignal) aborted if the stream is destroyed allowing to
      abort the `fn` call early.
- `options` [`<Object>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Object)
  - `concurrency` [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) the maximum concurrent invocation of `fn` to call
    on the stream at once. **Default:** `1`.
  - `signal` [`<AbortSignal>`](26-globals.md#class-abortsignal) allows destroying the stream if the signal is
    aborted.
- Returns: [`<Promise>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Promise) a promise evaluating to `true` if `fn` returned a truthy
  value for all of the chunks.

This method is similar to `Array.prototype.every` and calls `fn` on each chunk
in the stream to check if all awaited return values are truthy value for `fn`.
Once an `fn` call on a chunk awaited return value is falsy, the stream is
destroyed and the promise is fulfilled with `false`. If all of the `fn` calls
on the chunks return a truthy value, the promise is fulfilled with `true`.

```js
import { Readable } from 'node:stream';
import { stat } from 'node:fs/promises';

// With a synchronous predicate.
await Readable.from([1, 2, 3, 4]).every((x) => x > 2); // false
await Readable.from([1, 2, 3, 4]).every((x) => x > 0); // true

// With an asynchronous predicate, making at most 2 file checks at a time.
const allBigFiles = await Readable.from([
  'file1',
  'file2',
  'file3',
]).every(async (fileName) => {
  const stats = await stat(fileName);
  return stats.size > 1024 * 1024;
}, { concurrency: 2 });
// `true` if all files in the list are bigger than 1MiB
console.log(allBigFiles);
console.log('done'); // Stream has finished
```

##### `readable.flatMap(fn[, options])`

Added in: v17.5.0, v16.15.0

Stability: 1 - Experimental

- `fn` [`<Function>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Function) | [`<AsyncGeneratorFunction>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/AsyncGeneratorFunction) | [`<AsyncFunction>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/AsyncFunction) a function to map over
  every chunk in the stream.
  - `data` [`<any>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#Data_types) a chunk of data from the stream.
  - `options` [`<Object>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Object)
    - `signal` [`<AbortSignal>`](26-globals.md#class-abortsignal) aborted if the stream is destroyed allowing to
      abort the `fn` call early.
- `options` [`<Object>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Object)
  - `concurrency` [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) the maximum concurrent invocation of `fn` to call
    on the stream at once. **Default:** `1`.
  - `signal` [`<AbortSignal>`](26-globals.md#class-abortsignal) allows destroying the stream if the signal is
    aborted.
- Returns: [`<Readable>`](51-stream-part1.md#class-streamreadable) a stream flat-mapped with the function `fn`.

This method returns a new stream by applying the given callback to each
chunk of the stream and then flattening the result.

It is possible to return a stream or another iterable or async iterable from
`fn` and the result streams will be merged (flattened) into the returned
stream.

```js
import { Readable } from 'node:stream';
import { createReadStream } from 'node:fs';

// With a synchronous mapper.
for await (const chunk of Readable.from([1, 2, 3, 4]).flatMap((x) => [x, x])) {
  console.log(chunk); // 1, 1, 2, 2, 3, 3, 4, 4
}
// With an asynchronous mapper, combine the contents of 4 files
const concatResult = Readable.from([
  './1.mjs',
  './2.mjs',
  './3.mjs',
  './4.mjs',
]).flatMap((fileName) => createReadStream(fileName));
for await (const result of concatResult) {
  // This will contain the contents (all chunks) of all 4 files
  console.log(result);
}
```

##### `readable.drop(limit[, options])`

Added in: v17.5.0, v16.15.0

Stability: 1 - Experimental

- `limit` [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) the number of chunks to drop from the readable.
- `options` [`<Object>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Object)
  - `signal` [`<AbortSignal>`](26-globals.md#class-abortsignal) allows destroying the stream if the signal is
    aborted.
- Returns: [`<Readable>`](51-stream-part1.md#class-streamreadable) a stream with `limit` chunks dropped.

This method returns a new stream with the first `limit` chunks dropped.

```js
import { Readable } from 'node:stream';

await Readable.from([1, 2, 3, 4]).drop(2).toArray(); // [3, 4]
```

##### `readable.take(limit[, options])`

Added in: v17.5.0, v16.15.0

Stability: 1 - Experimental

- `limit` [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) the number of chunks to take from the readable.
- `options` [`<Object>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Object)
  - `signal` [`<AbortSignal>`](26-globals.md#class-abortsignal) allows destroying the stream if the signal is
    aborted.
- Returns: [`<Readable>`](51-stream-part1.md#class-streamreadable) a stream with `limit` chunks taken.

This method returns a new stream with the first `limit` chunks.

```js
import { Readable } from 'node:stream';

await Readable.from([1, 2, 3, 4]).take(2).toArray(); // [1, 2]
```

##### `readable.reduce(fn[, initial[, options]])`

Added in: v17.5.0, v16.15.0

Stability: 1 - Experimental

- `fn` [`<Function>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Function) | [`<AsyncFunction>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/AsyncFunction) a reducer function to call over every chunk
  in the stream.
  - `previous` [`<any>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#Data_types) the value obtained from the last call to `fn` or the
    `initial` value if specified or the first chunk of the stream otherwise.
  - `data` [`<any>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#Data_types) a chunk of data from the stream.
  - `options` [`<Object>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Object)
    - `signal` [`<AbortSignal>`](26-globals.md#class-abortsignal) aborted if the stream is destroyed allowing to
      abort the `fn` call early.
- `initial` [`<any>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#Data_types) the initial value to use in the reduction.
- `options` [`<Object>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Object)
  - `signal` [`<AbortSignal>`](26-globals.md#class-abortsignal) allows destroying the stream if the signal is
    aborted.
- Returns: [`<Promise>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Promise) a promise for the final value of the reduction.

This method calls `fn` on each chunk of the stream in order, passing it the
result from the calculation on the previous element. It returns a promise for
the final value of the reduction.

If no `initial` value is supplied the first chunk of the stream is used as the
initial value. If the stream is empty, the promise is rejected with a
`TypeError` with the `ERR_INVALID_ARGS` code property.

```js
import { Readable } from 'node:stream';
import { readdir, stat } from 'node:fs/promises';
import { join } from 'node:path';

const directoryPath = './src';
const filesInDir = await readdir(directoryPath);

const folderSize = await Readable.from(filesInDir)
  .reduce(async (totalSize, file) => {
    const { size } = await stat(join(directoryPath, file));
    return totalSize + size;
  }, 0);

console.log(folderSize);
```

The reducer function iterates the stream element-by-element which means that
there is no `concurrency` parameter or parallelism. To perform a `reduce`
concurrently, you can extract the async function to [`readable.map`](#readablemapfn-options) method.

```js
import { Readable } from 'node:stream';
import { readdir, stat } from 'node:fs/promises';
import { join } from 'node:path';

const directoryPath = './src';
const filesInDir = await readdir(directoryPath);

const folderSize = await Readable.from(filesInDir)
  .map((file) => stat(join(directoryPath, file)), { concurrency: 2 })
  .reduce((totalSize, { size }) => totalSize + size, 0);

console.log(folderSize);
```

### Duplex and transform streams

#### Class: `stream.Duplex`

Added in: v0.9.4

**History**

| Version | Changes |
| --- | --- |
| v6.8.0 | Instances of `Duplex` now return `true` when checking `instanceof stream.Writable`. |

Duplex streams are streams that implement both the [`Readable`](51-stream-part1.md#class-streamreadable) and
[`Writable`](51-stream-part1.md#class-streamwritable) interfaces.

Examples of `Duplex` streams include:

- [TCP sockets](38-net.md#class-netsocket)
- [zlib streams](68-zlib.md)
- [crypto streams](15-crypto-part1.md)

##### `duplex.allowHalfOpen`

Added in: v0.9.4

- Type: [`<boolean>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#boolean_type)

If `false` then the stream will automatically end the writable side when the
readable side ends. Set initially by the `allowHalfOpen` constructor option,
which defaults to `true`.

This can be changed manually to change the half-open behavior of an existing
`Duplex` stream instance, but must be changed before the `'end'` event is
emitted.

#### Class: `stream.Transform`

Added in: v0.9.4

Transform streams are [`Duplex`](#class-streamduplex) streams where the output is in some way
related to the input. Like all [`Duplex`](#class-streamduplex) streams, `Transform` streams
implement both the [`Readable`](51-stream-part1.md#class-streamreadable) and [`Writable`](51-stream-part1.md#class-streamwritable) interfaces.

Examples of `Transform` streams include:

- [zlib streams](68-zlib.md)
- [crypto streams](15-crypto-part1.md)

##### `transform.destroy([error])`

Added in: v8.0.0

**History**

| Version | Changes |
| --- | --- |
| v14.0.0 | Work as a no-op on a stream that has already been destroyed. |

- `error` [`<Error>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Error)
- Returns: [`<this>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Operators/this)

Destroy the stream, and optionally emit an `'error'` event. After this call, the
transform stream would release any internal resources.
Implementors should not override this method, but instead implement
[`readable._destroy()`](51-stream-part3.md#readable_destroyerr-callback).
The default implementation of `_destroy()` for `Transform` also emit `'close'`
unless `emitClose` is set in false.

Once `destroy()` has been called, any further calls will be a no-op and no
further errors except from `_destroy()` may be emitted as `'error'`.

#### `stream.duplexPair([options])`

Added in: v22.6.0, v20.17.0

- `options` [`<Object>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Object) A value to pass to both [`Duplex`](#class-streamduplex) constructors,
  to set options such as buffering.
- Returns: [`<Array>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Array) of two [`Duplex`](#class-streamduplex) instances.

The utility function `duplexPair` returns an Array with two items,
each being a `Duplex` stream connected to the other side:

```js
const [ sideA, sideB ] = duplexPair();
```

Whatever is written to one stream is made readable on the other. It provides
behavior analogous to a network connection, where the data written by the client
becomes readable by the server, and vice-versa.

The Duplex streams are symmetrical; one or the other may be used without any
difference in behavior.

### `stream.finished(stream[, options], callback)`

Added in: v10.0.0

**History**

| Version | Changes |
| --- | --- |
| v19.5.0 | Added support for `ReadableStream` and `WritableStream`. |
| v15.11.0 | The `signal` option was added. |
| v14.0.0 | The `finished(stream, cb)` will wait for the `'close'` event before invoking the callback. The implementation tries to detect legacy streams and only apply this behavior to streams which are expected to emit `'close'`. |
| v14.0.0 | Emitting `'close'` before `'end'` on a `Readable` stream will cause an `ERR_STREAM_PREMATURE_CLOSE` error. |
| v14.0.0 | Callback will be invoked on streams which have already finished before the call to `finished(stream, cb)`. |

- `stream` [`<Stream>`](51-stream-part1.md#stream) | [`<ReadableStream>`](66-webstreams.md#class-readablestream) | [`<WritableStream>`](66-webstreams.md#class-writablestream) A readable and/or writable
  stream/webstream.
- `options` [`<Object>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Object)
  - `error` [`<boolean>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#boolean_type) If set to `false`, then a call to `emit('error', err)` is
    not treated as finished. **Default:** `true`.
  - `readable` [`<boolean>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#boolean_type) When set to `false`, the callback will be called when
    the stream ends even though the stream might still be readable.
    **Default:** `true`.
  - `writable` [`<boolean>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#boolean_type) When set to `false`, the callback will be called when
    the stream ends even though the stream might still be writable.
    **Default:** `true`.
  - `signal` [`<AbortSignal>`](26-globals.md#class-abortsignal) allows aborting the wait for the stream finish. The
    underlying stream will *not* be aborted if the signal is aborted. The
    callback will get called with an `AbortError`. All registered
    listeners added by this function will also be removed.
- `callback` [`<Function>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Function) A callback function that takes an optional error
  argument.
- Returns: [`<Function>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Function) A cleanup function which removes all registered
  listeners.

A function to get notified when a stream is no longer readable, writable
or has experienced an error or a premature close event.

```js
const { finished } = require('node:stream');
const fs = require('node:fs');

const rs = fs.createReadStream('archive.tar');

finished(rs, (err) => {
  if (err) {
    console.error('Stream failed.', err);
  } else {
    console.log('Stream is done reading.');
  }
});

rs.resume(); // Drain the stream.
```

Especially useful in error handling scenarios where a stream is destroyed
prematurely (like an aborted HTTP request), and will not emit `'end'`
or `'finish'`.

The `finished` API provides [promise version](51-stream-part1.md#streamfinishedstream-options).

`stream.finished()` leaves dangling event listeners (in particular
`'error'`, `'end'`, `'finish'` and `'close'`) after `callback` has been
invoked. The reason for this is so that unexpected `'error'` events (due to
incorrect stream implementations) do not cause unexpected crashes.
If this is unwanted behavior then the returned cleanup function needs to be
invoked in the callback:

```js
const cleanup = finished(rs, (err) => {
  cleanup();
  // ...
});
```

### `stream.pipeline(source[, ...transforms], destination, callback)`

### `stream.pipeline(streams, callback)`

Added in: v10.0.0

**History**

| Version | Changes |
| --- | --- |
| v19.7.0, v18.16.0 | Added support for webstreams. |
| v18.0.0 | Passing an invalid callback to the `callback` argument now throws `ERR_INVALID_ARG_TYPE` instead of `ERR_INVALID_CALLBACK`. |
| v14.0.0 | The `pipeline(..., cb)` will wait for the `'close'` event before invoking the callback. The implementation tries to detect legacy streams and only apply this behavior to streams which are expected to emit `'close'`. |
| v13.10.0 | Add support for async generators. |

- `streams` {Stream[]|Iterable[]|AsyncIterable[]|Function[]|
  ReadableStream[]|WritableStream[]|TransformStream[]}
- `source` [`<Stream>`](51-stream-part1.md#stream) | [`<Iterable>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Iteration_protocols#the_iterable_protocol) | [`<AsyncIterable>`](https://tc39.github.io/ecma262/#sec-asynciterable-interface) | [`<Function>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Function) | [`<ReadableStream>`](66-webstreams.md#class-readablestream)
  - Returns: [`<Iterable>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Iteration_protocols#the_iterable_protocol) | [`<AsyncIterable>`](https://tc39.github.io/ecma262/#sec-asynciterable-interface)
- `...transforms` [`<Stream>`](51-stream-part1.md#stream) | [`<Function>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Function) | [`<TransformStream>`](66-webstreams.md#class-transformstream)
  - `source` [`<AsyncIterable>`](https://tc39.github.io/ecma262/#sec-asynciterable-interface)
  - Returns: [`<AsyncIterable>`](https://tc39.github.io/ecma262/#sec-asynciterable-interface)
- `destination` [`<Stream>`](51-stream-part1.md#stream) | [`<Function>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Function) | [`<WritableStream>`](66-webstreams.md#class-writablestream)
  - `source` [`<AsyncIterable>`](https://tc39.github.io/ecma262/#sec-asynciterable-interface)
  - Returns: [`<AsyncIterable>`](https://tc39.github.io/ecma262/#sec-asynciterable-interface) | [`<Promise>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Promise)
- `callback` [`<Function>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Function) Called when the pipeline is fully done.
  - `err` [`<Error>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Error)
  - `val` Resolved value of `Promise` returned by `destination`.
- Returns: [`<Stream>`](51-stream-part1.md#stream)

A module method to pipe between streams and generators forwarding errors and
properly cleaning up and provide a callback when the pipeline is complete.

```js
const { pipeline } = require('node:stream');
const fs = require('node:fs');
const zlib = require('node:zlib');

// Use the pipeline API to easily pipe a series of streams
// together and get notified when the pipeline is fully done.

// A pipeline to gzip a potentially huge tar file efficiently:

pipeline(
  fs.createReadStream('archive.tar'),
  zlib.createGzip(),
  fs.createWriteStream('archive.tar.gz'),
  (err) => {
    if (err) {
      console.error('Pipeline failed.', err);
    } else {
      console.log('Pipeline succeeded.');
    }
  },
);
```

The `pipeline` API provides a [promise version](51-stream-part1.md#streampipelinesource-transforms-destination-options).

`stream.pipeline()` will call `stream.destroy(err)` on all streams except:

- `Readable` streams which have emitted `'end'` or `'close'`.
- `Writable` streams which have emitted `'finish'` or `'close'`.

`stream.pipeline()` leaves dangling event listeners on the streams
after the `callback` has been invoked. In the case of reuse of streams after
failure, this can cause event listener leaks and swallowed errors. If the last
stream is readable, dangling event listeners will be removed so that the last
stream can be consumed later.

`stream.pipeline()` closes all the streams when an error is raised.
The `IncomingRequest` usage with `pipeline` could lead to an unexpected behavior
once it would destroy the socket without sending the expected response.
See the example below:

```js
const fs = require('node:fs');
const http = require('node:http');
const { pipeline } = require('node:stream');

const server = http.createServer((req, res) => {
  const fileStream = fs.createReadStream('./fileNotExist.txt');
  pipeline(fileStream, res, (err) => {
    if (err) {
      console.log(err); // No such file
      // this message can't be sent once `pipeline` already destroyed the socket
      return res.end('error!!!');
    }
  });
});
```

### `stream.compose(...streams)`

Added in: v16.9.0

**History**

| Version | Changes |
| --- | --- |
| v26.2.0 | Marking the API stable. |
| v21.1.0, v20.10.0 | Added support for stream class. |
| v19.8.0, v18.16.0 | Added support for webstreams. |

- `streams` {Stream[]|Iterable[]|AsyncIterable[]|Function[]|
  ReadableStream[]|WritableStream[]|TransformStream[]|Duplex[]|Function}
- Returns: [`<stream.Duplex>`](51-stream-part2.md#class-streamduplex)

Combines two or more streams into a `Duplex` stream that writes to the
first stream and reads from the last. Each provided stream is piped into
the next, using `stream.pipeline`. If any of the streams error then all
are destroyed, including the outer `Duplex` stream.

Because `stream.compose` returns a new stream that in turn can (and
should) be piped into other streams, it enables composition. In contrast,
when passing streams to `stream.pipeline`, typically the first stream is
a readable stream and the last a writable stream, forming a closed
circuit.

If passed a `Function` it must be a factory method taking a `source`
`Iterable`.

```js
import { compose, Transform } from 'node:stream';

const removeSpaces = new Transform({
  transform(chunk, encoding, callback) {
    callback(null, String(chunk).replace(' ', ''));
  },
});

async function* toUpper(source) {
  for await (const chunk of source) {
    yield String(chunk).toUpperCase();
  }
}

let res = '';
for await (const buf of compose(removeSpaces, toUpper).end('hello world')) {
  res += buf;
}

console.log(res); // prints 'HELLOWORLD'
```

`stream.compose` can be used to convert async iterables, generators and
functions into streams.

- `AsyncIterable` converts into a readable `Duplex`. Cannot yield
  `null`.
- `AsyncGeneratorFunction` converts into a readable/writable transform `Duplex`.
  Must take a source `AsyncIterable` as first parameter. Cannot yield
  `null`.
- `AsyncFunction` converts into a writable `Duplex`. Must return
  either `null` or `undefined`.

```js
import { compose } from 'node:stream';
import { finished } from 'node:stream/promises';

// Convert AsyncIterable into readable Duplex.
const s1 = compose(async function*() {
  yield 'Hello';
  yield 'World';
}());

// Convert AsyncGenerator into transform Duplex.
const s2 = compose(async function*(source) {
  for await (const chunk of source) {
    yield String(chunk).toUpperCase();
  }
});

let res = '';

// Convert AsyncFunction into writable Duplex.
const s3 = compose(async function(source) {
  for await (const chunk of source) {
    res += chunk;
  }
});

await finished(compose(s1, s2, s3));

console.log(res); // prints 'HELLOWORLD'
```

For convenience, the [`readable.compose(stream)`](#readablecomposestream-options) method is available on
[`<Readable>`](51-stream-part1.md#class-streamreadable) and [`<Duplex>`](51-stream-part2.md#class-streamduplex) streams as a wrapper for this function.

### `stream.isDestroyed(stream)`

Added in: v19.9.0, v18.17.0

- `stream` [`<Readable>`](51-stream-part1.md#class-streamreadable) | [`<Writable>`](51-stream-part1.md#class-streamwritable) | [`<Duplex>`](51-stream-part2.md#class-streamduplex)
- Returns: [`<boolean>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#boolean_type) | [`<null>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#null_type) - Only returns `null` if `stream` is not a valid `Readable`, `Writable` or `Duplex`.

Returns whether the stream has been destroyed.

### `stream.isErrored(stream)`

Added in: v17.3.0, v16.14.0

**History**

| Version | Changes |
| --- | --- |
| v24.0.0, v22.17.0 | Marking the API stable. |

- `stream` [`<Readable>`](51-stream-part1.md#class-streamreadable) | [`<Writable>`](51-stream-part1.md#class-streamwritable) | [`<Duplex>`](51-stream-part2.md#class-streamduplex) | [`<WritableStream>`](66-webstreams.md#class-writablestream) | [`<ReadableStream>`](66-webstreams.md#class-readablestream)
- Returns: [`<boolean>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#boolean_type)

Returns whether the stream has encountered an error.

### `stream.isReadable(stream)`

Added in: v17.4.0, v16.14.0

**History**

| Version | Changes |
| --- | --- |
| v24.0.0, v22.17.0 | Marking the API stable. |

- `stream` [`<Readable>`](51-stream-part1.md#class-streamreadable) | [`<Duplex>`](51-stream-part2.md#class-streamduplex) | [`<ReadableStream>`](66-webstreams.md#class-readablestream)
- Returns: [`<boolean>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#boolean_type) | [`<null>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#null_type) - Only returns `null` if `stream` is not a valid `Readable`, `Duplex` or `ReadableStream`.

Returns whether the stream is readable.

### `stream.isWritable(stream)`

- `stream` [`<Writable>`](51-stream-part1.md#class-streamwritable) | [`<Duplex>`](51-stream-part2.md#class-streamduplex) | [`<WritableStream>`](66-webstreams.md#class-writablestream)
- Returns: [`<boolean>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#boolean_type) | [`<null>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#null_type) - Only returns `null` if `stream` is not a valid `Writable`, `Duplex` or `WritableStream`.

Returns whether the stream is writable.

### `stream.Readable.from(iterable[, options])`

Added in: v12.3.0, v10.17.0

- `iterable` [`<Iterable>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Iteration_protocols#the_iterable_protocol) Object implementing the `Symbol.asyncIterator` or
  `Symbol.iterator` iterable protocol. Emits an 'error' event if a null
  value is passed.
- `options` [`<Object>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Object) Options provided to `new stream.Readable([options])`.
  By default, `Readable.from()` will set `options.objectMode` to `true`, unless
  this is explicitly opted out by setting `options.objectMode` to `false`.
- Returns: [`<stream.Readable>`](51-stream-part1.md#class-streamreadable)

A utility method for creating readable streams out of iterators.

```js
const { Readable } = require('node:stream');

async function * generate() {
  yield 'hello';
  yield 'streams';
}

const readable = Readable.from(generate());

readable.on('data', (chunk) => {
  console.log(chunk);
});
```

Calling `Readable.from(string)` or `Readable.from(buffer)` will not have
the strings or buffers be iterated to match the other streams semantics
for performance reasons.

If an `Iterable` object containing promises is passed as an argument,
it might result in unhandled rejection.

```js
const { Readable } = require('node:stream');

Readable.from([
  new Promise((resolve) => setTimeout(resolve('1'), 1500)),
  new Promise((_, reject) => setTimeout(reject(new Error('2')), 1000)), // Unhandled rejection
]);
```

### `stream.Readable.fromWeb(readableStream[, options])`

Added in: v17.0.0

**History**

| Version | Changes |
| --- | --- |
| v24.0.0, v22.17.0 | Marking the API stable. |

- `readableStream` [`<ReadableStream>`](66-webstreams.md#class-readablestream)
- `options` [`<Object>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Object)
  - `encoding` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type)
  - `highWaterMark` [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type)
  - `objectMode` [`<boolean>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#boolean_type)
  - `signal` [`<AbortSignal>`](26-globals.md#class-abortsignal)
- Returns: [`<stream.Readable>`](51-stream-part1.md#class-streamreadable)

### `stream.Readable.isDisturbed(stream)`

Added in: v16.8.0

**History**

| Version | Changes |
| --- | --- |
| v24.0.0, v22.17.0 | Marking the API stable. |

- `stream` [`<stream.Readable>`](51-stream-part1.md#class-streamreadable) | [`<ReadableStream>`](66-webstreams.md#class-readablestream)
- Returns: `boolean`

Returns whether the stream has been read from or cancelled.

[Contents](../README.md) | [Previous: Stream, part 1 of 4](51-stream-part1.md) | [Next: Stream, part 3 of 4](51-stream-part3.md)
