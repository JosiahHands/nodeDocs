[Contents](../README.md) | [Previous: Events](23-events.md) | [Next: File system, part 2 of 7](24-fs-part2.md)

Source: [https://nodejs.org/docs/latest/api/fs.html](https://nodejs.org/docs/latest/api/fs.html)

Part 1 of 7 of this section.

# File system

[Stability: 2](01-documentation.md#stability-index) - Stable

The `node:fs` module enables interacting with the file system in a
way modeled on standard POSIX functions.

To use the promise-based APIs:

*ES module version:*

```js
import * as fs from 'node:fs/promises';
```

*CommonJS version:*

```js
const fs = require('node:fs/promises');
```

To use the callback and sync APIs:

*ES module version:*

```js
import * as fs from 'node:fs';
```

*CommonJS version:*

```js
const fs = require('node:fs');
```

All file system operations have synchronous, callback, and promise-based
forms, and are accessible using both CommonJS syntax and ES6 Modules (ESM).

## Promise example

Promise-based operations return a promise that is fulfilled when the
asynchronous operation is complete.

*ES module version:*

```js
import { unlink } from 'node:fs/promises';

try {
  await unlink('/tmp/hello');
  console.log('successfully deleted /tmp/hello');
} catch (error) {
  console.error('there was an error:', error.message);
}
```

*CommonJS version:*

```js
const { unlink } = require('node:fs/promises');

(async function(path) {
  try {
    await unlink(path);
    console.log(`successfully deleted ${path}`);
  } catch (error) {
    console.error('there was an error:', error.message);
  }
})('/tmp/hello');
```

## Callback example

The callback form takes a completion callback function as its last
argument and invokes the operation asynchronously. The arguments passed to
the completion callback depend on the method, but the first argument is always
reserved for an exception. If the operation is completed successfully, then
the first argument is `null` or `undefined`.

*ES module version:*

```js
import { unlink } from 'node:fs';

unlink('/tmp/hello', (err) => {
  if (err) throw err;
  console.log('successfully deleted /tmp/hello');
});
```

*CommonJS version:*

```js
const { unlink } = require('node:fs');

unlink('/tmp/hello', (err) => {
  if (err) throw err;
  console.log('successfully deleted /tmp/hello');
});
```

The callback-based versions of the `node:fs` module APIs are preferable over
the use of the promise APIs when maximal performance (both in terms of
execution time and memory allocation) is required.

## Synchronous example

The synchronous APIs block the Node.js event loop and further JavaScript
execution until the operation is complete. Exceptions are thrown immediately
and can be handled using `try…catch`, or can be allowed to bubble up.

*ES module version:*

```js
import { unlinkSync } from 'node:fs';

try {
  unlinkSync('/tmp/hello');
  console.log('successfully deleted /tmp/hello');
} catch (err) {
  // handle the error
}
```

*CommonJS version:*

```js
const { unlinkSync } = require('node:fs');

try {
  unlinkSync('/tmp/hello');
  console.log('successfully deleted /tmp/hello');
} catch (err) {
  // handle the error
}
```

## Promises API

Added in: v10.0.0

**History**

| Version | Changes |
| --- | --- |
| v14.0.0 | Exposed as `require('fs/promises')`. |
| v11.14.0, v10.17.0 | This API is no longer experimental. |
| v10.1.0 | The API is accessible via `require('fs').promises` only. |

The `fs/promises` API provides asynchronous file system methods that return
promises.

The promise APIs use the underlying Node.js threadpool to perform file
system operations off the event loop thread. These operations are not
synchronized or threadsafe. Care must be taken when performing multiple
concurrent modifications on the same file or data corruption may occur.

### Class: `FileHandle`

Added in: v10.0.0

A [`<FileHandle>`](24-fs-part1.md#class-filehandle) object is an object wrapper for a numeric file descriptor.

Instances of the [`<FileHandle>`](24-fs-part1.md#class-filehandle) object are created by the `fsPromises.open()`
method.

All [`<FileHandle>`](24-fs-part1.md#class-filehandle) objects are [`<EventEmitter>`](23-events.md#class-eventemitter)s.

If a [`<FileHandle>`](24-fs-part1.md#class-filehandle) is not closed using the `filehandle.close()` method, it will
try to automatically close the file descriptor and emit a process warning,
helping to prevent memory leaks. Please do not rely on this behavior because
it can be unreliable and the file may not be closed. Instead, always explicitly
close [`<FileHandle>`](24-fs-part1.md#class-filehandle)s. Node.js may change this behavior in the future.

#### Event: `'close'`

Added in: v15.4.0

The `'close'` event is emitted when the [`<FileHandle>`](24-fs-part1.md#class-filehandle) has been closed and can no
longer be used.

#### `filehandle.appendFile(data[, options])`

Added in: v10.0.0

**History**

| Version | Changes |
| --- | --- |
| v21.1.0, v20.10.0 | The `flush` option is now supported. |
| v15.14.0, v14.18.0 | The `data` argument supports `AsyncIterable`, `Iterable`, and `Stream`. |
| v14.0.0 | The `data` parameter won't coerce unsupported input to strings anymore. |

- `data` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) | [`<Buffer>`](07-buffer-part1.md#class-buffer) | [`<TypedArray>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/TypedArray) | [`<DataView>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/DataView) | [`<AsyncIterable>`](https://tc39.github.io/ecma262/#sec-asynciterable-interface) | [`<Iterable>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Iteration_protocols#the_iterable_protocol)
- `options` [`<Object>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Object) | [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type)
  - `encoding` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) | [`<null>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#null_type) **Default:** `'utf8'`
  - `signal` [`<AbortSignal>`](26-globals.md#class-abortsignal) | [`<undefined>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#undefined_type) allows aborting an in-progress writeFile. **Default:** `undefined`
- Returns: [`<Promise>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Promise) Fulfills with `undefined` upon success.

Alias of [`filehandle.writeFile()`](#filehandlewritefiledata-options).

When operating on file handles, the mode cannot be changed from what it was set
to with [`fsPromises.open()`](24-fs-part2.md#fspromisesopenpath-flags-mode). Therefore, this is equivalent to
[`filehandle.writeFile()`](#filehandlewritefiledata-options).

#### `filehandle.chmod(mode)`

Added in: v10.0.0

- `mode` [`<integer>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) the file mode bit mask.
- Returns: [`<Promise>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Promise) Fulfills with `undefined` upon success.

Modifies the permissions on the file. See [`chmod(2)`](http://man7.org/linux/man-pages/man2/chmod.2.html).

#### `filehandle.chown(uid, gid)`

Added in: v10.0.0

- `uid` [`<integer>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) The file's new owner's user id.
- `gid` [`<integer>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) The file's new group's group id.
- Returns: [`<Promise>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Promise) Fulfills with `undefined` upon success.

Changes the ownership of the file. A wrapper for [`chown(2)`](http://man7.org/linux/man-pages/man2/chown.2.html).

#### `filehandle.close()`

Added in: v10.0.0

- Returns: [`<Promise>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Promise) Fulfills with `undefined` upon success.

Closes the file handle after waiting for any pending operation on the handle to
complete.

```js
import { open } from 'node:fs/promises';

let filehandle;
try {
  filehandle = await open('thefile.txt', 'r');
} finally {
  await filehandle?.close();
}
```

#### `filehandle.createReadStream([options])`

Added in: v16.11.0

- `options` [`<Object>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Object)
  - `encoding` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) **Default:** `null`
  - `autoClose` [`<boolean>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#boolean_type) **Default:** `true`
  - `emitClose` [`<boolean>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#boolean_type) **Default:** `true`
  - `start` [`<integer>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type)
  - `end` [`<integer>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) **Default:** `Infinity`
  - `highWaterMark` [`<integer>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) **Default:** `64 * 1024`
  - `signal` [`<AbortSignal>`](26-globals.md#class-abortsignal) | [`<undefined>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#undefined_type) **Default:** `undefined`
- Returns: [`<fs.ReadStream>`](24-fs-part6.md#class-fsreadstream)

`options` can include `start` and `end` values to read a range of bytes from
the file instead of the entire file. Both `start` and `end` are inclusive and
start counting at 0, allowed values are in the
[0, [`Number.MAX_SAFE_INTEGER`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Number/MAX_SAFE_INTEGER)] range. If `start` is
omitted or `undefined`, `filehandle.createReadStream()` reads sequentially from
the current file position. The `encoding` can be any one of those accepted by
[`<Buffer>`](07-buffer-part1.md#class-buffer).

If the `FileHandle` points to a character device that only supports blocking
reads (such as keyboard or sound card), read operations do not finish until data
is available. This can prevent the process from exiting and the stream from
closing naturally.

By default, the stream will emit a `'close'` event after it has been
destroyed. Set the `emitClose` option to `false` to change this behavior.

```js
import { open } from 'node:fs/promises';

const fd = await open('/dev/input/event0');
// Create a stream from some character device.
const stream = fd.createReadStream();
setTimeout(() => {
  stream.close(); // This may not close the stream.
  // Artificially marking end-of-stream, as if the underlying resource had
  // indicated end-of-file by itself, allows the stream to close.
  // This does not cancel pending read operations, and if there is such an
  // operation, the process may still not be able to exit successfully
  // until it finishes.
  stream.push(null);
  stream.read(0);
}, 100);
```

If `autoClose` is false, then the file descriptor won't be closed, even if
there's an error. It is the application's responsibility to close it and make
sure there's no file descriptor leak. If `autoClose` is set to true (default
behavior), on `'error'` or `'end'` the file descriptor will be closed
automatically.

An example to read the last 10 bytes of a file which is 100 bytes long:

```js
import { open } from 'node:fs/promises';

const fd = await open('sample.txt');
fd.createReadStream({ start: 90, end: 99 });
```

#### `filehandle.createWriteStream([options])`

Added in: v16.11.0

**History**

| Version | Changes |
| --- | --- |
| v22.0.0 | bump default highWaterMark. |
| v21.0.0, v20.10.0 | The `flush` option is now supported. |

- `options` [`<Object>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Object)
  - `encoding` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) **Default:** `'utf8'`
  - `autoClose` [`<boolean>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#boolean_type) **Default:** `true`
  - `emitClose` [`<boolean>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#boolean_type) **Default:** `true`
  - `start` [`<integer>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type)
  - `highWaterMark` [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) **Default:** See
    [`stream.getDefaultHighWaterMark()`](51-stream-part3.md#streamgetdefaulthighwatermarkobjectmode).
  - `flush` [`<boolean>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#boolean_type) If `true`, the underlying file descriptor is flushed
    prior to closing it. **Default:** `false`.
- Returns: [`<fs.WriteStream>`](24-fs-part7.md#class-fswritestream)

`options` may also include a `start` option to allow writing data at some
position past the beginning of the file, allowed values are in the
[0, [`Number.MAX_SAFE_INTEGER`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Number/MAX_SAFE_INTEGER)] range. Modifying a file rather than
replacing it may require the `flags` `open` option to be set to `r+` rather than
the default `r`. The `encoding` can be any one of those accepted by [`<Buffer>`](07-buffer-part1.md#class-buffer).

If `autoClose` is set to true (default behavior) on `'error'` or `'finish'`
the file descriptor will be closed automatically. If `autoClose` is false,
then the file descriptor won't be closed, even if there's an error.
It is the application's responsibility to close it and make sure there's no
file descriptor leak.

By default, the stream will emit a `'close'` event after it has been
destroyed. Set the `emitClose` option to `false` to change this behavior.

#### `filehandle.datasync()`

Added in: v10.0.0

- Returns: [`<Promise>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Promise) Fulfills with `undefined` upon success.

Forces all currently queued I/O operations associated with the file to the
operating system's synchronized I/O completion state. Refer to the POSIX
[`fdatasync(2)`](http://man7.org/linux/man-pages/man2/fdatasync.2.html) documentation for details.

Unlike `filehandle.sync` this method does not flush modified metadata.

#### `filehandle.fd`

Added in: v10.0.0

- Type: [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) The numeric file descriptor managed by the [`<FileHandle>`](24-fs-part1.md#class-filehandle) object.

#### `filehandle.pull([...transforms][, options])`

Added in: v25.9.0

Stability: 1 - Experimental

- `...transforms` [`<Function>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Function) | [`<Object>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Object) Optional transforms to apply via [`stream/iter pull()`](32-stream-iter.md#pullsource-transforms-options).
- `options` [`<Object>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Object)
  - `signal` [`<AbortSignal>`](26-globals.md#class-abortsignal)
  - `autoClose` [`<boolean>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#boolean_type) Close the file handle when the stream ends. **Default:** `false`.
  - `start` [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) Byte offset to begin reading from. When specified,
    reads use explicit positioning (`pread` semantics). **Default:** current
    file position.
  - `limit` [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) Maximum number of bytes to read before ending the
    iterator. Reads stop when `limit` bytes have been delivered or EOF is
    reached, whichever comes first. **Default:** read until EOF.
  - `chunkSize` [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) Size in bytes of the buffer allocated for each
    read operation. **Default:** `131072` (128 KB).
- Returns: [`<AsyncIterable>`](https://tc39.github.io/ecma262/#sec-asynciterable-interface) whose chunks fulfill with [`<Uint8Array>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Uint8Array)[]

Return the file contents as an async iterable using the
[`node:stream/iter`](32-stream-iter.md) pull model. Reads are performed in `chunkSize`-byte
chunks (default 128 KB). If transforms are provided, they are applied
via [`stream/iter pull()`](32-stream-iter.md#pullsource-transforms-options).

The file handle is locked from the first read of the iterable, and unlocked
when iteration completes, an error occurs, or the consumer breaks. An iterable
that is never read does not lock the file handle.

This function is only available when the `--experimental-stream-iter` flag is
enabled.

*ES module version:*

```js
import { open } from 'node:fs/promises';
import { text } from 'node:stream/iter';
import { compressGzip } from 'node:zlib/iter';

const fh = await open('input.txt', 'r');

// Read as text
console.log(await text(fh.pull({ autoClose: true })));

// Read 1 KB starting at byte 100
const fh2 = await open('input.txt', 'r');
console.log(await text(fh2.pull({ start: 100, limit: 1024, autoClose: true })));

// Read with compression
const fh3 = await open('input.txt', 'r');
const compressed = fh3.pull(compressGzip(), { autoClose: true });
```

*CommonJS version:*

```js
const { open } = require('node:fs/promises');
const { text } = require('node:stream/iter');
const { compressGzip } = require('node:zlib/iter');

async function run() {
  const fh = await open('input.txt', 'r');

  // Read as text
  console.log(await text(fh.pull({ autoClose: true })));

  // Read 1 KB starting at byte 100
  const fh2 = await open('input.txt', 'r');
  console.log(await text(fh2.pull({ start: 100, limit: 1024, autoClose: true })));

  // Read with compression
  const fh3 = await open('input.txt', 'r');
  const compressed = fh3.pull(compressGzip(), { autoClose: true });
}

run().catch(console.error);
```

#### `filehandle.pullSync([...transforms][, options])`

Added in: v25.9.0

Stability: 1 - Experimental

- `...transforms` [`<Function>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Function) | [`<Object>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Object) Optional transforms to apply via [`stream/iter pullSync()`](32-stream-iter.md#pullsyncsource-transforms).
- `options` [`<Object>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Object)
  - `autoClose` [`<boolean>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#boolean_type) Close the file handle when the stream ends. **Default:** `false`.
  - `start` [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) Byte offset to begin reading from. When specified,
    reads use explicit positioning. **Default:** current file position.
  - `limit` [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) Maximum number of bytes to read before ending the
    iterator. **Default:** read until EOF.
  - `chunkSize` [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) Size in bytes of the buffer allocated for each
    read operation. **Default:** `131072` (128 KB).
- Returns: [`<Iterable>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Iteration_protocols#the_iterable_protocol) whose chunks return [`<Uint8Array>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Uint8Array)[]

Synchronous counterpart of [`filehandle.pull()`](#filehandlepulltransforms-options). Returns a sync iterable
that reads the file using synchronous I/O on the main thread. Reads are
performed in `chunkSize`-byte chunks (default 128 KB).

The file handle is locked from the first read of the iterable until iteration
ends, as with [`filehandle.pull()`](#filehandlepulltransforms-options). Unlike the async `pull()`, this method does not support `AbortSignal` since all
operations are synchronous.

This function is only available when the `--experimental-stream-iter` flag is
enabled.

*ES module version:*

```js
import { open } from 'node:fs/promises';
import { textSync, pipeToSync } from 'node:stream/iter';
import { compressGzipSync, decompressGzipSync } from 'node:zlib/iter';

const fh = await open('input.txt', 'r');

// Read as text (sync)
console.log(textSync(fh.pullSync({ autoClose: true })));

// Sync compress pipeline: file -> gzip -> file
const src = await open('input.txt', 'r');
const dst = await open('output.gz', 'w');
pipeToSync(src.pullSync(compressGzipSync(), { autoClose: true }), dst.writer({ autoClose: true }));
```

*CommonJS version:*

```js
const { open } = require('node:fs/promises');
const { textSync, pipeToSync } = require('node:stream/iter');
const { compressGzipSync, decompressGzipSync } = require('node:zlib/iter');

async function run() {
  const fh = await open('input.txt', 'r');

  // Read as text (sync)
  console.log(textSync(fh.pullSync({ autoClose: true })));

  // Sync compress pipeline: file -> gzip -> file
  const src = await open('input.txt', 'r');
  const dst = await open('output.gz', 'w');
  pipeToSync(
    src.pullSync(compressGzipSync(), { autoClose: true }),
    dst.writer({ autoClose: true }),
  );
}

run().catch(console.error);
```

#### `filehandle.read(buffer, offset, length, position)`

Added in: v10.0.0

**History**

| Version | Changes |
| --- | --- |
| v21.0.0 | Accepts bigint values as `position`. |

- `buffer` [`<Buffer>`](07-buffer-part1.md#class-buffer) | [`<TypedArray>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/TypedArray) | [`<DataView>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/DataView) A buffer that will be filled with the
  file data read.
- `offset` [`<integer>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) The location in the buffer at which to start filling. **Default:** `0`
- `length` [`<integer>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) The number of bytes to read. **Default:**
  `buffer.byteLength - offset`
- `position` [`<integer>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) | [`<bigint>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#bigint_type) | [`<null>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#null_type) The location where to begin reading data
  from the file. If `null` or `-1`, data will be read from the current file
  position, and the position will be updated. If `position` is a non-negative
  integer, the current file position will remain unchanged.
  **Default:** `null`
- Returns: [`<Promise>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Promise) Fulfills upon success with an object with two properties:
  - `bytesRead` [`<integer>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) The number of bytes read
  - `buffer` [`<Buffer>`](07-buffer-part1.md#class-buffer) | [`<TypedArray>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/TypedArray) | [`<DataView>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/DataView) A reference to the passed in `buffer`
    argument.

Reads data from the file and stores that in the given buffer.

If the file is not modified concurrently, the end-of-file is reached when the
number of bytes read is zero.

#### `filehandle.read([options])`

Added in: v13.11.0, v12.17.0

**History**

| Version | Changes |
| --- | --- |
| v21.0.0 | Accepts bigint values as `position`. |

- `options` [`<Object>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Object)
  - `buffer` [`<Buffer>`](07-buffer-part1.md#class-buffer) | [`<TypedArray>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/TypedArray) | [`<DataView>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/DataView) A buffer that will be filled with the
    file data read. **Default:** `Buffer.alloc(16384)`
  - `offset` [`<integer>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) The location in the buffer at which to start filling. **Default:** `0`
  - `length` [`<integer>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) The number of bytes to read. **Default:**
    `buffer.byteLength - offset`
  - `position` [`<integer>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) | [`<bigint>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#bigint_type) | [`<null>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#null_type) The location where to begin reading data
    from the file. If `null` or `-1`, data will be read from the current file
    position, and the position will be updated. If `position` is a non-negative
    integer, the current file position will remain unchanged.
    **Default:**: `null`
- Returns: [`<Promise>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Promise) Fulfills upon success with an object with two properties:
  - `bytesRead` [`<integer>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) The number of bytes read
  - `buffer` [`<Buffer>`](07-buffer-part1.md#class-buffer) | [`<TypedArray>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/TypedArray) | [`<DataView>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/DataView) A reference to the passed in `buffer`
    argument.

Reads data from the file and stores that in the given buffer.

If the file is not modified concurrently, the end-of-file is reached when the
number of bytes read is zero.

#### `filehandle.read(buffer[, options])`

Added in: v18.2.0, v16.17.0

**History**

| Version | Changes |
| --- | --- |
| v21.0.0 | Accepts bigint values as `position`. |

- `buffer` [`<Buffer>`](07-buffer-part1.md#class-buffer) | [`<TypedArray>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/TypedArray) | [`<DataView>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/DataView) A buffer that will be filled with the
  file data read.
- `options` [`<Object>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Object)
  - `offset` [`<integer>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) The location in the buffer at which to start filling. **Default:** `0`
  - `length` [`<integer>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) The number of bytes to read. **Default:**
    `buffer.byteLength - offset`
  - `position` [`<integer>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) | [`<bigint>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#bigint_type) | [`<null>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#null_type) The location where to begin reading data
    from the file. If `null` or `-1`, data will be read from the current file
    position, and the position will be updated. If `position` is a non-negative
    integer, the current file position will remain unchanged.
    **Default:**: `null`
- Returns: [`<Promise>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Promise) Fulfills upon success with an object with two properties:
  - `bytesRead` [`<integer>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) The number of bytes read
  - `buffer` [`<Buffer>`](07-buffer-part1.md#class-buffer) | [`<TypedArray>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/TypedArray) | [`<DataView>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/DataView) A reference to the passed in `buffer`
    argument.

Reads data from the file and stores that in the given buffer.

If the file is not modified concurrently, the end-of-file is reached when the
number of bytes read is zero.

#### `filehandle.readableWebStream([options])`

Added in: v17.0.0

**History**

| Version | Changes |
| --- | --- |
| v24.0.0, v22.17.0 | Marking the API stable. |
| v23.8.0, v22.15.0 | Removed option to create a 'bytes' stream. Streams are now always 'bytes' streams. |
| v20.0.0, v18.17.0 | Added option to create a 'bytes' stream. |

- `options` [`<Object>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Object)
  - `autoClose` [`<boolean>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#boolean_type) When true, causes the [`<FileHandle>`](24-fs-part1.md#class-filehandle) to be closed when the
    stream is closed. **Default:** `false`
- Returns: [`<ReadableStream>`](66-webstreams.md#class-readablestream)

Returns a byte-oriented `ReadableStream` that may be used to read the file's
contents.

An error will be thrown if this method is called more than once or is called
after the `FileHandle` is closed or closing.

*ES module version:*

```js
import {
  open,
} from 'node:fs/promises';

const file = await open('./some/file/to/read');

for await (const chunk of file.readableWebStream())
  console.log(chunk);

await file.close();
```

*CommonJS version:*

```js
const {
  open,
} = require('node:fs/promises');

(async () => {
  const file = await open('./some/file/to/read');

  for await (const chunk of file.readableWebStream())
    console.log(chunk);

  await file.close();
})();
```

While the `ReadableStream` will read the file to completion, it will not
close the `FileHandle` automatically. User code must still call the
`fileHandle.close()` method unless the `autoClose` option is set to `true`.

#### `filehandle.readFile(options)`

Added in: v10.0.0

**History**

| Version | Changes |
| --- | --- |
| v26.4.0 | Added support for the `buffer` option. |

- `options` [`<Object>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Object) | [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type)
  - `encoding` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) | [`<null>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#null_type) **Default:** `null`
  - `signal` [`<AbortSignal>`](26-globals.md#class-abortsignal) allows aborting an in-progress readFile
  - `buffer` [`<Buffer>`](07-buffer-part1.md#class-buffer) | [`<TypedArray>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/TypedArray) | [`<DataView>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/DataView) | [`<Function>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Function) A buffer to read into, or a
    function called with the file size that returns the buffer.
- Returns: [`<Promise>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Promise) Fulfills upon a successful read with the contents of the
  file. If no encoding is specified (using `options.encoding`), the data is
  returned as a [`<Buffer>`](07-buffer-part1.md#class-buffer) object. Otherwise, the data will be a string.

Asynchronously reads the entire contents of a file.

If `options` is a string, then it specifies the `encoding`.

If `buffer` is provided and no encoding is specified, the returned [`<Buffer>`](07-buffer-part1.md#class-buffer) is
a view over the supplied buffer containing only the bytes read. If the
supplied buffer is too small to contain the entire file, the operation will
fail.

The [`<FileHandle>`](24-fs-part1.md#class-filehandle) has to support reading.

If one or more `filehandle.read()` calls are made on a file handle and then a
`filehandle.readFile()` call is made, the data will be read from the current
position till the end of the file. It doesn't always read from the beginning
of the file.

An example using the `buffer` option with a pre-allocated buffer:

```js
import { Buffer } from 'node:buffer';
import { open } from 'node:fs/promises';

const file = await open('./some/file/to/read');
try {
  const buf = Buffer.alloc(16384);
  const contents = await file.readFile({ buffer: buf });
  console.log(contents); // A view over `buf` containing only the bytes read
} finally {
  await file.close();
}
```

An example using the `buffer` option with a function returning a buffer:

```js
import { Buffer } from 'node:buffer';
import { open } from 'node:fs/promises';

const file = await open('./some/file/to/read');
try {
  const contents = await file.readFile({
    buffer: (size) => Buffer.alloc(size),
  });
  console.log(contents);
} finally {
  await file.close();
}
```

#### `filehandle.readLines([options])`

Added in: v18.11.0

- `options` [`<Object>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Object)
  - `encoding` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) **Default:** `null`
  - `autoClose` [`<boolean>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#boolean_type) **Default:** `true`
  - `emitClose` [`<boolean>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#boolean_type) **Default:** `true`
  - `start` [`<integer>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type)
  - `end` [`<integer>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) **Default:** `Infinity`
  - `highWaterMark` [`<integer>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) **Default:** `64 * 1024`
- Returns: [`<readline.InterfaceConstructor>`](46-readline.md#class-readlineinterfaceconstructor)

Convenience method to create a `readline` interface and stream over the file.
See [`filehandle.createReadStream()`](#filehandlecreatereadstreamoptions) for the options.

*ES module version:*

```js
import { open } from 'node:fs/promises';

const file = await open('./some/file/to/read');

for await (const line of file.readLines()) {
  console.log(line);
}
```

*CommonJS version:*

```js
const { open } = require('node:fs/promises');

(async () => {
  const file = await open('./some/file/to/read');

  for await (const line of file.readLines()) {
    console.log(line);
  }
})();
```

#### `filehandle.readv(buffers[, position])`

Added in: v13.13.0, v12.17.0

- `buffers` [`<Buffer>`](07-buffer-part1.md#class-buffer)[] | [`<TypedArray>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/TypedArray)[] | [`<DataView>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/DataView)[]
- `position` [`<integer>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) | [`<null>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#null_type) The offset from the beginning of the file where
  the data should be read from. If `position` is not a `number`, the data will
  be read from the current position. **Default:** `null`
- Returns: [`<Promise>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Promise) Fulfills upon success an object containing two properties:
  - `bytesRead` [`<integer>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) the number of bytes read
  - `buffers` [`<Buffer>`](07-buffer-part1.md#class-buffer)[] | [`<TypedArray>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/TypedArray)[] | [`<DataView>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/DataView)[] property containing
    a reference to the `buffers` input.

Read from a file and write to an array of [`<ArrayBufferView>`](https://developer.mozilla.org/docs/Web/API/ArrayBufferView)s

#### `filehandle.stat([options])`

Added in: v10.0.0

**History**

| Version | Changes |
| --- | --- |
| v26.1.0 | Now accepts an additional `signal` property to allow aborting the operation. |
| v10.5.0 | Accepts an additional `options` object to specify whether the numeric values returned should be bigint. |

- `options` [`<Object>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Object)
  - `bigint` [`<boolean>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#boolean_type) Whether the numeric values in the returned [`<fs.Stats>`](24-fs-part6.md#class-fsstats) object should be `bigint`. **Default:** `false`.
  - `signal` [`<AbortSignal>`](26-globals.md#class-abortsignal) An AbortSignal to cancel the operation. **Default:** `undefined`.
- Returns: [`<Promise>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Promise) Fulfills with an [`<fs.Stats>`](24-fs-part6.md#class-fsstats) for the file.

#### `filehandle.sync()`

Added in: v10.0.0

- Returns: [`<Promise>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Promise) Fulfills with `undefined` upon success.

Request that all data for the open file descriptor is flushed to the storage
device. The specific implementation is operating system and device specific.
Refer to the POSIX [`fsync(2)`](http://man7.org/linux/man-pages/man2/fsync.2.html) documentation for more detail.

#### `filehandle.truncate(len)`

Added in: v10.0.0

- `len` [`<integer>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) **Default:** `0`
- Returns: [`<Promise>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Promise) Fulfills with `undefined` upon success.

Truncates the file.

If the file was larger than `len` bytes, only the first `len` bytes will be
retained in the file.

The following example retains only the first four bytes of the file:

```js
import { open } from 'node:fs/promises';

let filehandle = null;
try {
  filehandle = await open('temp.txt', 'r+');
  await filehandle.truncate(4);
} finally {
  await filehandle?.close();
}
```

If the file previously was shorter than `len` bytes, it is extended, and the
extended part is filled with null bytes (`'\0'`):

If `len` is negative then `0` will be used.

#### `filehandle.utimes(atime, mtime)`

Added in: v10.0.0

- `atime` [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) | [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) | [`<Date>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Date)
- `mtime` [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) | [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) | [`<Date>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Date)
- Returns: [`<Promise>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Promise)

Change the file system timestamps of the object referenced by the [`<FileHandle>`](24-fs-part1.md#class-filehandle)
then fulfills the promise with no arguments upon success.

#### `filehandle.write(buffer, offset[, length[, position]])`

Added in: v10.0.0

**History**

| Version | Changes |
| --- | --- |
| v14.0.0 | The `buffer` parameter won't coerce unsupported input to buffers anymore. |

- `buffer` [`<Buffer>`](07-buffer-part1.md#class-buffer) | [`<TypedArray>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/TypedArray) | [`<DataView>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/DataView)
- `offset` [`<integer>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) The start position from within `buffer` where the data
  to write begins.
- `length` [`<integer>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) The number of bytes from `buffer` to write. **Default:**
  `buffer.byteLength - offset`
- `position` [`<integer>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) | [`<null>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#null_type) The offset from the beginning of the file where the
  data from `buffer` should be written. If `position` is not a `number`,
  the data will be written at the current position. See the POSIX [`pwrite(2)`](http://man7.org/linux/man-pages/man2/pwrite.2.html)
  documentation for more detail. **Default:** `null`
- Returns: [`<Promise>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Promise)

Write `buffer` to the file.

The promise is fulfilled with an object containing two properties:

- `bytesWritten` [`<integer>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) the number of bytes written
- `buffer` [`<Buffer>`](07-buffer-part1.md#class-buffer) | [`<TypedArray>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/TypedArray) | [`<DataView>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/DataView) a reference to the `buffer` written.

It is unsafe to use `filehandle.write()` multiple times on the same file
without waiting for the promise to be fulfilled (or rejected). For this
scenario, use [`filehandle.createWriteStream()`](#filehandlecreatewritestreamoptions).

On Linux, positional writes do not work when the file is opened in append mode.
The kernel ignores the position argument and always appends the data to
the end of the file.

#### `filehandle.write(buffer[, options])`

Added in: v18.3.0, v16.17.0

- `buffer` [`<Buffer>`](07-buffer-part1.md#class-buffer) | [`<TypedArray>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/TypedArray) | [`<DataView>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/DataView)
- `options` [`<Object>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Object)
  - `offset` [`<integer>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) **Default:** `0`
  - `length` [`<integer>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) **Default:** `buffer.byteLength - offset`
  - `position` [`<integer>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) | [`<null>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#null_type) **Default:** `null`
- Returns: [`<Promise>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Promise)

Write `buffer` to the file.

Similar to the above `filehandle.write` function, this version takes an
optional `options` object. If no `options` object is specified, it will
default with the above values.

#### `filehandle.write(string[, position[, encoding]])`

Added in: v10.0.0

**History**

| Version | Changes |
| --- | --- |
| v14.0.0 | The `string` parameter won't coerce unsupported input to strings anymore. |

- `string` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type)
- `position` [`<integer>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) | [`<null>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#null_type) The offset from the beginning of the file where the
  data from `string` should be written. If `position` is not a `number` the
  data will be written at the current position. See the POSIX [`pwrite(2)`](http://man7.org/linux/man-pages/man2/pwrite.2.html)
  documentation for more detail. **Default:** `null`
- `encoding` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) The expected string encoding. **Default:** `'utf8'`
- Returns: [`<Promise>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Promise)

Write `string` to the file. If `string` is not a string, the promise is
rejected with an error.

The promise is fulfilled with an object containing two properties:

- `bytesWritten` [`<integer>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) the number of bytes written
- `buffer` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) a reference to the `string` written.

It is unsafe to use `filehandle.write()` multiple times on the same file
without waiting for the promise to be fulfilled (or rejected). For this
scenario, use [`filehandle.createWriteStream()`](#filehandlecreatewritestreamoptions).

On Linux, positional writes do not work when the file is opened in append mode.
The kernel ignores the position argument and always appends the data to
the end of the file.

#### `filehandle.writeFile(data, options)`

Added in: v10.0.0

**History**

| Version | Changes |
| --- | --- |
| v15.14.0, v14.18.0 | The `data` argument supports `AsyncIterable`, `Iterable`, and `Stream`. |
| v14.0.0 | The `data` parameter won't coerce unsupported input to strings anymore. |

- `data` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) | [`<Buffer>`](07-buffer-part1.md#class-buffer) | [`<TypedArray>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/TypedArray) | [`<DataView>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/DataView) | [`<AsyncIterable>`](https://tc39.github.io/ecma262/#sec-asynciterable-interface) | [`<Iterable>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Iteration_protocols#the_iterable_protocol)
- `options` [`<Object>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Object) | [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type)
  - `encoding` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) | [`<null>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#null_type) The expected character encoding when `data` is a
    string. **Default:** `'utf8'`
  - `signal` [`<AbortSignal>`](26-globals.md#class-abortsignal) | [`<undefined>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#undefined_type) allows aborting an in-progress writeFile. **Default:** `undefined`
- Returns: [`<Promise>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Promise)

Asynchronously writes data to a file, replacing the file if it already exists.
`data` can be a string, a buffer, an [`<AsyncIterable>`](https://tc39.github.io/ecma262/#sec-asynciterable-interface), or an [`<Iterable>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Iteration_protocols#the_iterable_protocol) object.
The promise is fulfilled with no arguments upon success.

If `options` is a string, then it specifies the `encoding`.

The [`<FileHandle>`](24-fs-part1.md#class-filehandle) has to support writing.

It is unsafe to use `filehandle.writeFile()` multiple times on the same file
without waiting for the promise to be fulfilled (or rejected).

If one or more `filehandle.write()` calls are made on a file handle and then a
`filehandle.writeFile()` call is made, the data will be written from the
current position till the end of the file. It doesn't always write from the
beginning of the file.

#### `filehandle.writev(buffers[, position])`

Added in: v12.9.0

- `buffers` [`<Buffer>`](07-buffer-part1.md#class-buffer)[] | [`<TypedArray>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/TypedArray)[] | [`<DataView>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/DataView)[]
- `position` [`<integer>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) | [`<null>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#null_type) The offset from the beginning of the file where the
  data from `buffers` should be written. If `position` is not a `number`,
  the data will be written at the current position. **Default:** `null`
- Returns: [`<Promise>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Promise)

Write an array of [`<ArrayBufferView>`](https://developer.mozilla.org/docs/Web/API/ArrayBufferView)s to the file.

The promise is fulfilled with an object containing a two properties:

- `bytesWritten` [`<integer>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) the number of bytes written
- `buffers` [`<Buffer>`](07-buffer-part1.md#class-buffer)[] | [`<TypedArray>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/TypedArray)[] | [`<DataView>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/DataView)[] a reference to the `buffers`
  input.

It is unsafe to call `writev()` multiple times on the same file without waiting
for the promise to be fulfilled (or rejected).

On Linux, positional writes don't work when the file is opened in append mode.
The kernel ignores the position argument and always appends the data to
the end of the file.

#### `filehandle.writer([options])`

Added in: v25.9.0

Stability: 1 - Experimental

- `options` [`<Object>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Object)
  - `autoClose` [`<boolean>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#boolean_type) Close the file handle when the writer ends or
    fails. **Default:** `false`.
  - `start` [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) Byte offset to start writing at. When specified,
    writes use explicit positioning. **Default:** current file position.
  - `limit` [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) Maximum number of bytes the writer will accept.
    Async writes (`write()`, `writev()`) that would exceed the limit reject
    with `ERR_OUT_OF_RANGE`. Sync writes (`writeSync()`, `writevSync()`)
    return `false`. **Default:** no limit.
  - `chunkSize` [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) Maximum chunk size in bytes for synchronous write
    operations. Writes larger than this threshold fall back to async I/O.
    Set this to match the reader's `chunkSize` for optimal `pipeTo()`
    performance. **Default:** `131072` (128 KB).
- Returns: [`<Object>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Object)
  - `write(chunk[, options])` [`<Function>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Function) Returns [`<Promise>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Promise).
    Accepts `Uint8Array`, `Buffer`, or string (UTF-8 encoded).
    - `chunk` [`<Buffer>`](07-buffer-part1.md#class-buffer) | [`<TypedArray>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/TypedArray) | [`<DataView>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/DataView) | [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type)
    - `options` [`<Object>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Object)
      - `signal` [`<AbortSignal>`](26-globals.md#class-abortsignal) If the signal is already aborted, the write
        rejects with `AbortError` without performing I/O.
  - `writev(chunks[, options])` [`<Function>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Function) Returns [`<Promise>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Promise). Uses
    scatter/gather I/O via a single `writev()` syscall. Accepts mixed
    `Uint8Array`/string arrays.
    - `chunks` [`<Buffer>`](07-buffer-part1.md#class-buffer)[] | [`<TypedArray>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/TypedArray)[] | [`<DataView>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/DataView)[] | [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type)[]
    - `options` [`<Object>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Object)
      - `signal` [`<AbortSignal>`](26-globals.md#class-abortsignal) If the signal is already aborted, the write
        rejects with `AbortError` without performing I/O.
  - `writeSync(chunk)` [`<Function>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Function) Returns [`<boolean>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#boolean_type). Attempts a synchronous
    write. Returns `true` if the write succeeded, `false` if the caller
    should fall back to async `write()`. Returns `false` when: the writer
    is closed/errored, an async operation is in flight, the chunk exceeds
    `chunkSize`, or the write would exceed `limit`.
    - `chunk` [`<Buffer>`](07-buffer-part1.md#class-buffer) | [`<TypedArray>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/TypedArray) | [`<DataView>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/DataView) | [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type)
  - `writevSync(chunks)` [`<Function>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Function) Returns [`<boolean>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#boolean_type). Synchronous batch
    write. Same fallback semantics as `writeSync()`.
    - `chunks` [`<Buffer>`](07-buffer-part1.md#class-buffer)[] | [`<TypedArray>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/TypedArray)[] | [`<DataView>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/DataView)[] | [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type)[]
  - `end([options])` [`<Function>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Function) Returns [`<Promise>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Promise), fulfills with the total
    number of bytes written. Idempotent: returns `totalBytesWritten` if already
    closed, returns the pending promise if already closing. Rejects if the writer
    is in an errored state.
    - `options` [`<Object>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Object)
      - `signal` [`<AbortSignal>`](26-globals.md#class-abortsignal) If the signal is already aborted, `end()`
        rejects with `AbortError` and the writer remains open.
  - `endSync()` [`<Function>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Function) Returns [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) | [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) total bytes written on
    success, `-1` if the writer is errored or an async operation is in
    flight. Idempotent when already closed.
  - `fail(reason)` [`<Function>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Function) Puts the writer into a terminal error state.
    Synchronous. If the writer is already closed or errored, this is a
    no-op. If `autoClose` is true, closes the file handle synchronously.

Return a [`node:stream/iter`](32-stream-iter.md) writer backed by this file handle.

The writer supports both `Symbol.asyncDispose` and `Symbol.dispose`:

- `await using w = fh.writer()` — if the writer is still open (no `end()`
  called), `asyncDispose` calls `fail()`. If `end()` is pending, it waits
  for it to complete.
- `using w = fh.writer()` — calls `fail()` unconditionally.

Async writes (`write()` and `writev()`) that are started without awaiting the
previous one are performed one at a time, in the order they were called, so
they never overlap in the file. A queued write is not performed if the writer
fails, or its `signal` aborts, before its turn.

The `writeSync()` and `writevSync()` methods enable the try-sync fast path
used by [`stream/iter pipeTo()`](32-stream-iter.md#pipetosource-transforms-writer-options). When the reader's chunk size matches the
writer's `chunkSize`, all writes in a `pipeTo()` pipeline complete
synchronously with zero promise overhead.

This function is only available when the `--experimental-stream-iter` flag is
enabled.

*ES module version:*

```js
import { open } from 'node:fs/promises';
import { from, pipeTo } from 'node:stream/iter';
import { compressGzip } from 'node:zlib/iter';

// Async pipeline
const fh = await open('output.gz', 'w');
await pipeTo(from('Hello!'), compressGzip(), fh.writer({ autoClose: true }));

// Sync pipeline with limit
const src = await open('input.txt', 'r');
const dst = await open('output.txt', 'w');
const w = dst.writer({ limit: 1024 * 1024 }); // Max 1 MB
await pipeTo(src.pull({ autoClose: true }), w);
await w.end();
await dst.close();
```

*CommonJS version:*

```js
const { open } = require('node:fs/promises');
const { from, pipeTo } = require('node:stream/iter');
const { compressGzip } = require('node:zlib/iter');

async function run() {
  // Async pipeline
  const fh = await open('output.gz', 'w');
  await pipeTo(from('Hello!'), compressGzip(), fh.writer({ autoClose: true }));

  // Sync pipeline with limit
  const src = await open('input.txt', 'r');
  const dst = await open('output.txt', 'w');
  const w = dst.writer({ limit: 1024 * 1024 }); // Max 1 MB
  await pipeTo(src.pull({ autoClose: true }), w);
  await w.end();
  await dst.close();
}

run().catch(console.error);
```

#### `filehandle[Symbol.asyncDispose]()`

Added in: v20.4.0, v18.18.0

**History**

| Version | Changes |
| --- | --- |
| v24.2.0 | No longer experimental. |

- Returns: [`<Promise>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Promise)

Calls `filehandle.close()` and returns a promise that fulfills when the
filehandle is closed.

This method enables the filehandle to be used with [`await using`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Statements/await_using), which
will automatically close the file when the scope exits. For more information,
see the [MDN documentation on `using` statements](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Statements/using).

### `fsPromises.access(path[, mode])`

Added in: v10.0.0

- `path` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) | [`<Buffer>`](07-buffer-part1.md#class-buffer) | [`<URL>`](59-url.md#the-whatwg-url-api)
- `mode` [`<integer>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) **Default:** `fs.constants.F_OK`
- Returns: [`<Promise>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Promise) Fulfills with `undefined` upon success.

Tests a user's permissions for the file or directory specified by `path`.
The `mode` argument is an optional integer that specifies the accessibility
checks to be performed. `mode` should be either the value `fs.constants.F_OK`
or a mask consisting of the bitwise OR of any of `fs.constants.R_OK`,
`fs.constants.W_OK`, and `fs.constants.X_OK` (e.g.
`fs.constants.W_OK | fs.constants.R_OK`). Check [File access constants](24-fs-part7.md#file-access-constants) for
possible values of `mode`.

If the accessibility check is successful, the promise is fulfilled with no
value. If any of the accessibility checks fail, the promise is rejected
with an [`<Error>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Error) object. The following example checks if the file `/etc/passwd` can be read and written by the current process.

```js
import { access, constants } from 'node:fs/promises';

try {
  await access('/etc/passwd', constants.R_OK | constants.W_OK);
  console.log('can access');
} catch {
  console.error('cannot access');
}
```

Using `fsPromises.access()` to check for the accessibility of a file before
calling `fsPromises.open()` is not recommended. Doing so introduces a race
condition, since other processes may change the file's state between the two
calls. Instead, user code should open/read/write the file directly and handle
the error raised if the file is not accessible.

[Contents](../README.md) | [Previous: Events](23-events.md) | [Next: File system, part 2 of 7](24-fs-part2.md)
