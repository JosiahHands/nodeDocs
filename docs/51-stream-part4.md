[Contents](../README.md) | [Previous: Stream, part 3 of 4](51-stream-part3.md) | [Next: String decoder](52-string-decoder.md)

Source: [https://nodejs.org/docs/latest/api/stream.html](https://nodejs.org/docs/latest/api/stream.html)

Part 4 of 4 of this section.

# Stream (part 4 of 4)

### Compatibility with older Node.js versions

Prior to Node.js 0.10, the `Readable` stream interface was simpler, but also
less powerful and less useful.

- Rather than waiting for calls to the [`stream.read()`](51-stream-part1.md#readablereadsize) method,
  [`'data'`](51-stream-part1.md#event-data) events would begin emitting immediately. Applications that
  would need to perform some amount of work to decide how to handle data
  were required to store read data into buffers so the data would not be lost.
- The [`stream.pause()`](51-stream-part1.md#readablepause) method was advisory, rather than
  guaranteed. This meant that it was still necessary to be prepared to receive
  [`'data'`](51-stream-part1.md#event-data) events *even when the stream was in a paused state*.

In Node.js 0.10, the [`Readable`](51-stream-part1.md#class-streamreadable) class was added. For backward
compatibility with older Node.js programs, `Readable` streams switch into
"flowing mode" when a [`'data'`](51-stream-part1.md#event-data) event handler is added, or when the
[`stream.resume()`](51-stream-part2.md#readableresume) method is called. The effect is that, even
when not using the new [`stream.read()`](51-stream-part1.md#readablereadsize) method and
[`'readable'`](51-stream-part1.md#event-readable) event, it is no longer necessary to worry about losing
[`'data'`](51-stream-part1.md#event-data) chunks.

While most applications will continue to function normally, this introduces an
edge case in the following conditions:

- No [`'data'`](51-stream-part1.md#event-data) event listener is added.
- The [`stream.resume()`](51-stream-part2.md#readableresume) method is never called.
- The stream is not piped to any writable destination.

For example, consider the following code:

```js
// WARNING!  BROKEN!
net.createServer((socket) => {

  // We add an 'end' listener, but never consume the data.
  socket.on('end', () => {
    // It will never get here.
    socket.end('The message was received but was not processed.\n');
  });

}).listen(1337);
```

Prior to Node.js 0.10, the incoming message data would be simply discarded.
However, in Node.js 0.10 and beyond, the socket remains paused forever.

The workaround in this situation is to call the
[`stream.resume()`](51-stream-part2.md#readableresume) method to begin the flow of data:

```js
// Workaround.
net.createServer((socket) => {
  socket.on('end', () => {
    socket.end('The message was received but was not processed.\n');
  });

  // Start the flow of data, discarding it.
  socket.resume();
}).listen(1337);
```

In addition to new `Readable` streams switching into flowing mode,
pre-0.10 style streams can be wrapped in a `Readable` class using the
[`readable.wrap()`](51-stream-part2.md#readablewrapstream) method.

### `readable.read(0)`

There are some cases where it is necessary to trigger a refresh of the
underlying readable stream mechanisms, without actually consuming any
data. In such cases, it is possible to call `readable.read(0)`, which will
always return `null`.

If the internal read buffer is below the `highWaterMark`, and the
stream is not currently reading, then calling `stream.read(0)` will trigger
a low-level [`stream._read()`](51-stream-part3.md#readable_readsize) call.

While most applications will almost never need to do this, there are
situations within Node.js where this is done, particularly in the
`Readable` stream class internals.

### `readable.push('')`

Use of `readable.push('')` is not recommended.

Pushing a zero-byte [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type), [`<Buffer>`](07-buffer-part1.md#class-buffer), [`<TypedArray>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/TypedArray) or [`<DataView>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/DataView) to a stream
that is not in object mode has an interesting side effect.
Because it *is* a call to
[`readable.push()`](51-stream-part3.md#readablepushchunk-encoding), the call will end the reading process.
However, because the argument is an empty string, no data is added to the
readable buffer so there is nothing for a user to consume.

### `highWaterMark` discrepancy after calling `readable.setEncoding()`

The use of `readable.setEncoding()` will change the behavior of how the
`highWaterMark` operates in non-object mode.

Typically, the size of the current buffer is measured against the
`highWaterMark` in *bytes*. However, after `setEncoding()` is called, the
comparison function will begin to measure the buffer's size in *characters*.

This is not a problem in common cases with `latin1` or `ascii`. But it is
advised to be mindful about this behavior when working with strings that could
contain multi-byte characters.

[Contents](../README.md) | [Previous: Stream, part 3 of 4](51-stream-part3.md) | [Next: String decoder](52-string-decoder.md)
