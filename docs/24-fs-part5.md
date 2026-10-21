[Contents](../README.md) | [Previous: File system, part 4 of 7](24-fs-part4.md) | [Next: File system, part 6 of 7](24-fs-part6.md)

Source: [https://nodejs.org/docs/latest/api/fs.html](https://nodejs.org/docs/latest/api/fs.html)

Part 5 of 7 of this section.

# File system (part 5 of 7)

##### Filename argument

Providing `filename` argument in the callback is only supported on Linux,
macOS, Windows, and AIX. Even on supported platforms, `filename` is not always
guaranteed to be provided. Therefore, don't assume that `filename` argument is
always provided in the callback, and have some fallback logic if it is `null`.

```js
import { watch } from 'node:fs';
watch('somedir', (eventType, filename) => {
  console.log(`event type is: ${eventType}`);
  if (filename) {
    console.log(`filename provided: ${filename}`);
  } else {
    console.log('filename not provided');
  }
});
```

### `fs.watchFile(filename[, options], listener)`

Added in: v0.1.31

**History**

| Version | Changes |
| --- | --- |
| v10.5.0 | The `bigint` option is now supported. |
| v7.6.0 | The `filename` parameter can be a WHATWG `URL` object using `file:` protocol. |

- `filename` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) | [`<Buffer>`](07-buffer-part1.md#class-buffer) | [`<URL>`](59-url.md#the-whatwg-url-api)
- `options` [`<Object>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Object)
  - `bigint` [`<boolean>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#boolean_type) **Default:** `false`
  - `persistent` [`<boolean>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#boolean_type) **Default:** `true`
  - `interval` [`<integer>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) **Default:** `5007`
- `listener` [`<Function>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Function)
  - `current` [`<fs.Stats>`](24-fs-part6.md#class-fsstats)
  - `previous` [`<fs.Stats>`](24-fs-part6.md#class-fsstats)
- Returns: [`<fs.StatWatcher>`](24-fs-part6.md#class-fsstatwatcher)

Watch for changes on `filename`. The callback `listener` will be called each
time the file is accessed.

The `options` argument may be omitted. If provided, it should be an object. The
`options` object may contain a boolean named `persistent` that indicates
whether the process should continue to run as long as files are being watched.
The `options` object may specify an `interval` property indicating how often the
target should be polled in milliseconds.

The `listener` gets two arguments the current stat object and the previous
stat object:

```js
import { watchFile } from 'node:fs';

watchFile('message.text', (curr, prev) => {
  console.log(`the current mtime is: ${curr.mtime}`);
  console.log(`the previous mtime was: ${prev.mtime}`);
});
```

These stat objects are instances of `fs.Stat`. If the `bigint` option is `true`,
the numeric values in these objects are specified as `BigInt`s.

To be notified when the file was modified, not just accessed, it is necessary
to compare `curr.mtimeMs` and `prev.mtimeMs`.

When an `fs.watchFile` operation results in an `ENOENT` error, it
will invoke the listener once, with all the fields zeroed (or, for dates, the
Unix Epoch). If the file is created later on, the listener will be called
again, with the latest stat objects. This is a change in functionality since
v0.10.

Using [`fs.watch()`](24-fs-part4.md#fswatchfilename-options-listener) is more efficient than `fs.watchFile` and
`fs.unwatchFile`. `fs.watch` should be used instead of `fs.watchFile` and
`fs.unwatchFile` when possible.

When a file being watched by `fs.watchFile()` disappears and reappears,
then the contents of `previous` in the second callback event (the file's
reappearance) will be the same as the contents of `previous` in the first
callback event (its disappearance).

This happens when:

- the file is deleted, followed by a restore
- the file is renamed and then renamed a second time back to its original name

### `fs.write(fd, buffer, offset[, length[, position]], callback)`

Added in: v0.0.2

**History**

| Version | Changes |
| --- | --- |
| v18.0.0 | Passing an invalid callback to the `callback` argument now throws `ERR_INVALID_ARG_TYPE` instead of `ERR_INVALID_CALLBACK`. |
| v14.0.0 | The `buffer` parameter won't coerce unsupported input to strings anymore. |
| v10.10.0 | The `buffer` parameter can now be any `TypedArray` or a `DataView`. |
| v10.0.0 | The `callback` parameter is no longer optional. Not passing it will throw a `TypeError` at runtime. |
| v7.4.0 | The `buffer` parameter can now be a `Uint8Array`. |
| v7.2.0 | The `offset` and `length` parameters are optional now. |
| v7.0.0 | The `callback` parameter is no longer optional. Not passing it will emit a deprecation warning with id DEP0013. |

- `fd` [`<integer>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type)
- `buffer` [`<Buffer>`](07-buffer-part1.md#class-buffer) | [`<TypedArray>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/TypedArray) | [`<DataView>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/DataView)
- `offset` [`<integer>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) **Default:** `0`
- `length` [`<integer>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) **Default:** `buffer.byteLength - offset`
- `position` [`<integer>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) | [`<null>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#null_type) **Default:** `null`
- `callback` [`<Function>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Function)
  - `err` [`<Error>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Error)
  - `bytesWritten` [`<integer>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type)
  - `buffer` [`<Buffer>`](07-buffer-part1.md#class-buffer) | [`<TypedArray>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/TypedArray) | [`<DataView>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/DataView)

Write `buffer` to the file specified by `fd`.

`offset` determines the part of the buffer to be written, and `length` is
an integer specifying the number of bytes to write.

`position` refers to the offset from the beginning of the file where this data
should be written. If `typeof position !== 'number'`, the data will be written
at the current position. See [`pwrite(2)`](http://man7.org/linux/man-pages/man2/pwrite.2.html).

The callback will be given three arguments `(err, bytesWritten, buffer)` where
`bytesWritten` specifies how many *bytes* were written from `buffer`.

If this method is invoked as its [`util.promisify()`](60-util-part2.md#utilpromisifyoriginal)ed version, it returns
a promise for an `Object` with `bytesWritten` and `buffer` properties.

It is unsafe to use `fs.write()` multiple times on the same file without waiting
for the callback. For this scenario, [`fs.createWriteStream()`](24-fs-part3.md#fscreatewritestreampath-options) is
recommended.

On Linux, positional writes don't work when the file is opened in append mode.
The kernel ignores the position argument and always appends the data to
the end of the file.

### `fs.write(fd, buffer[, options], callback)`

Added in: v18.3.0, v16.17.0

- `fd` [`<integer>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type)
- `buffer` [`<Buffer>`](07-buffer-part1.md#class-buffer) | [`<TypedArray>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/TypedArray) | [`<DataView>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/DataView)
- `options` [`<Object>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Object)
  - `offset` [`<integer>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) **Default:** `0`
  - `length` [`<integer>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) **Default:** `buffer.byteLength - offset`
  - `position` [`<integer>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) | [`<null>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#null_type) **Default:** `null`
- `callback` [`<Function>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Function)
  - `err` [`<Error>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Error)
  - `bytesWritten` [`<integer>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type)
  - `buffer` [`<Buffer>`](07-buffer-part1.md#class-buffer) | [`<TypedArray>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/TypedArray) | [`<DataView>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/DataView)

Write `buffer` to the file specified by `fd`.

Similar to the above `fs.write` function, this version takes an
optional `options` object. If no `options` object is specified, it will
default with the above values.

### `fs.write(fd, string[, position[, encoding]], callback)`

Added in: v0.11.5

**History**

| Version | Changes |
| --- | --- |
| v19.0.0 | Passing to the `string` parameter an object with an own `toString` function is no longer supported. |
| v17.8.0 | Passing to the `string` parameter an object with an own `toString` function is deprecated. |
| v14.12.0 | The `string` parameter will stringify an object with an explicit `toString` function. |
| v14.0.0 | The `string` parameter won't coerce unsupported input to strings anymore. |
| v10.0.0 | The `callback` parameter is no longer optional. Not passing it will throw a `TypeError` at runtime. |
| v7.2.0 | The `position` parameter is optional now. |
| v7.0.0 | The `callback` parameter is no longer optional. Not passing it will emit a deprecation warning with id DEP0013. |

- `fd` [`<integer>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type)
- `string` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type)
- `position` [`<integer>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) | [`<null>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#null_type) **Default:** `null`
- `encoding` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) **Default:** `'utf8'`
- `callback` [`<Function>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Function)
  - `err` [`<Error>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Error)
  - `written` [`<integer>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type)
  - `string` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type)

Write `string` to the file specified by `fd`. If `string` is not a string,
an exception is thrown.

`position` refers to the offset from the beginning of the file where this data
should be written. If `typeof position !== 'number'` the data will be written at
the current position. See [`pwrite(2)`](http://man7.org/linux/man-pages/man2/pwrite.2.html).

`encoding` is the expected string encoding.

The callback will receive the arguments `(err, written, string)` where `written`
specifies how many *bytes* the passed string required to be written. Bytes
written is not necessarily the same as string characters written. See
[`Buffer.byteLength`](07-buffer-part1.md#static-method-bufferbytelengthstring-encoding).

It is unsafe to use `fs.write()` multiple times on the same file without waiting
for the callback. For this scenario, [`fs.createWriteStream()`](24-fs-part3.md#fscreatewritestreampath-options) is
recommended.

On Linux, positional writes don't work when the file is opened in append mode.
The kernel ignores the position argument and always appends the data to
the end of the file.

On Windows, if the file descriptor is connected to the console (e.g. `fd == 1`
or `stdout`) a string containing non-ASCII characters will not be rendered
properly by default, regardless of the encoding used.
It is possible to configure the console to render UTF-8 properly by changing the
active codepage with the `chcp 65001` command. See the [chcp](https://ss64.com/nt/chcp.html) docs for more
details.

### `fs.writeFile(file, data[, options], callback)`

Added in: v0.1.29

**History**

| Version | Changes |
| --- | --- |
| v21.0.0, v20.10.0 | The `flush` option is now supported. |
| v19.0.0 | Passing to the `string` parameter an object with an own `toString` function is no longer supported. |
| v18.0.0 | Passing an invalid callback to the `callback` argument now throws `ERR_INVALID_ARG_TYPE` instead of `ERR_INVALID_CALLBACK`. |
| v17.8.0 | Passing to the `string` parameter an object with an own `toString` function is deprecated. |
| v16.0.0 | The error returned may be an `AggregateError` if more than one error is returned. |
| v15.2.0, v14.17.0 | The options argument may include an AbortSignal to abort an ongoing writeFile request. |
| v14.12.0 | The `data` parameter will stringify an object with an explicit `toString` function. |
| v14.0.0 | The `data` parameter won't coerce unsupported input to strings anymore. |
| v10.10.0 | The `data` parameter can now be any `TypedArray` or a `DataView`. |
| v10.0.0 | The `callback` parameter is no longer optional. Not passing it will throw a `TypeError` at runtime. |
| v7.4.0 | The `data` parameter can now be a `Uint8Array`. |
| v7.0.0 | The `callback` parameter is no longer optional. Not passing it will emit a deprecation warning with id DEP0013. |
| v5.0.0 | The `file` parameter can be a file descriptor now. |

- `file` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) | [`<Buffer>`](07-buffer-part1.md#class-buffer) | [`<URL>`](59-url.md#the-whatwg-url-api) | [`<integer>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) filename or file descriptor
- `data` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) | [`<Buffer>`](07-buffer-part1.md#class-buffer) | [`<TypedArray>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/TypedArray) | [`<DataView>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/DataView)
- `options` [`<Object>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Object) | [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type)
  - `encoding` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) | [`<null>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#null_type) **Default:** `'utf8'`
  - `mode` [`<integer>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) **Default:** `0o666`
  - `flag` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) See [support of file system `flags`](24-fs-part7.md#file-system-flags). **Default:** `'w'`.
  - `flush` [`<boolean>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#boolean_type) If all data is successfully written to the file, and `flush` is `true`, `fs.fsync()` is used to flush the data.
    **Default:** `false`.
  - `signal` [`<AbortSignal>`](26-globals.md#class-abortsignal) allows aborting an in-progress writeFile
- `callback` [`<Function>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Function)
  - `err` [`<Error>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Error) | [`<AggregateError>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/AggregateError)

When `file` is a filename, asynchronously writes data to the file, replacing the
file if it already exists. `data` can be a string or a buffer.

When `file` is a file descriptor, the behavior is similar to calling
`fs.write()` directly (which is recommended). See the notes below on using
a file descriptor.

The `encoding` option is ignored if `data` is a buffer.

The `mode` option only affects the newly created file. See [`fs.open()`](24-fs-part4.md#fsopenpath-flags-mode-callback)
for more details.

```js
import { writeFile } from 'node:fs';
import { Buffer } from 'node:buffer';

const data = new Uint8Array(Buffer.from('Hello Node.js'));
writeFile('message.txt', data, (err) => {
  if (err) throw err;
  console.log('The file has been saved!');
});
```

If `options` is a string, then it specifies the encoding:

```js
import { writeFile } from 'node:fs';

writeFile('message.txt', 'Hello Node.js', 'utf8', callback);
```

It is unsafe to use `fs.writeFile()` multiple times on the same file without
waiting for the callback. For this scenario, [`fs.createWriteStream()`](24-fs-part3.md#fscreatewritestreampath-options) is
recommended.

Similarly to `fs.readFile` - `fs.writeFile` is a convenience method that
performs multiple `write` calls internally to write the buffer passed to it.
For performance sensitive code consider using [`fs.createWriteStream()`](24-fs-part3.md#fscreatewritestreampath-options).

It is possible to use an [`<AbortSignal>`](26-globals.md#class-abortsignal) to cancel an `fs.writeFile()`.
Cancelation is "best effort", and some amount of data is likely still
to be written.

```js
import { writeFile } from 'node:fs';
import { Buffer } from 'node:buffer';

const controller = new AbortController();
const { signal } = controller;
const data = new Uint8Array(Buffer.from('Hello Node.js'));
writeFile('message.txt', data, { signal }, (err) => {
  // When a request is aborted - the callback is called with an AbortError
});
// When the request should be aborted
controller.abort();
```

Aborting an ongoing request does not abort individual operating
system requests but rather the internal buffering `fs.writeFile` performs.

#### Using `fs.writeFile()` with file descriptors

When `file` is a file descriptor, the behavior is almost identical to directly
calling `fs.write()` like:

```js
import { write } from 'node:fs';
import { Buffer } from 'node:buffer';

write(fd, Buffer.from(data, options.encoding), callback);
```

The difference from directly calling `fs.write()` is that under some unusual
conditions, `fs.write()` might write only part of the buffer and need to be
retried to write the remaining data, whereas `fs.writeFile()` retries until
the data is entirely written (or an error occurs).

The implications of this are a common source of confusion. In
the file descriptor case, the file is not replaced! The data is not necessarily
written to the beginning of the file, and the file's original data may remain
before and/or after the newly written data.

For example, if `fs.writeFile()` is called twice in a row, first to write the
string `'Hello'`, then to write the string `', World'`, the file would contain
`'Hello, World'`, and might contain some of the file's original data (depending
on the size of the original file, and the position of the file descriptor). If
a file name had been used instead of a descriptor, the file would be guaranteed
to contain only `', World'`.

### `fs.writev(fd, buffers[, position], callback)`

Added in: v12.9.0

**History**

| Version | Changes |
| --- | --- |
| v18.0.0 | Passing an invalid callback to the `callback` argument now throws `ERR_INVALID_ARG_TYPE` instead of `ERR_INVALID_CALLBACK`. |

- `fd` [`<integer>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type)
- `buffers` [`<ArrayBufferView>`](https://developer.mozilla.org/docs/Web/API/ArrayBufferView)[]
- `position` [`<integer>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) | [`<null>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#null_type) **Default:** `null`
- `callback` [`<Function>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Function)
  - `err` [`<Error>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Error)
  - `bytesWritten` [`<integer>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type)
  - `buffers` [`<ArrayBufferView>`](https://developer.mozilla.org/docs/Web/API/ArrayBufferView)[]

Write an array of `ArrayBufferView`s to the file specified by `fd` using
`writev()`.

`position` is the offset from the beginning of the file where this data
should be written. If `typeof position !== 'number'`, the data will be written
at the current position.

The callback will be given three arguments: `err`, `bytesWritten`, and
`buffers`. `bytesWritten` is how many bytes were written from `buffers`.

If this method is [`util.promisify()`](60-util-part2.md#utilpromisifyoriginal)ed, it returns a promise for an
`Object` with `bytesWritten` and `buffers` properties.

It is unsafe to use `fs.writev()` multiple times on the same file without
waiting for the callback. For this scenario, use [`fs.createWriteStream()`](24-fs-part3.md#fscreatewritestreampath-options).

On Linux, positional writes don't work when the file is opened in append mode.
The kernel ignores the position argument and always appends the data to
the end of the file.

## Synchronous API

The synchronous APIs perform all operations synchronously, blocking the
event loop until the operation completes or fails.

### `fs.accessSync(path[, mode])`

Added in: v0.11.15

**History**

| Version | Changes |
| --- | --- |
| v7.6.0 | The `path` parameter can be a WHATWG `URL` object using `file:` protocol. |

- `path` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) | [`<Buffer>`](07-buffer-part1.md#class-buffer) | [`<URL>`](59-url.md#the-whatwg-url-api)
- `mode` [`<integer>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) **Default:** `fs.constants.F_OK`

Synchronously tests a user's permissions for the file or directory specified
by `path`. The `mode` argument is an optional integer that specifies the
accessibility checks to be performed. `mode` should be either the value
`fs.constants.F_OK` or a mask consisting of the bitwise OR of any of
`fs.constants.R_OK`, `fs.constants.W_OK`, and `fs.constants.X_OK` (e.g.
`fs.constants.W_OK | fs.constants.R_OK`). Check [File access constants](24-fs-part7.md#file-access-constants) for
possible values of `mode`.

If any of the accessibility checks fail, an `Error` will be thrown. Otherwise,
the method will return `undefined`.

```js
import { accessSync, constants } from 'node:fs';

try {
  accessSync('etc/passwd', constants.R_OK | constants.W_OK);
  console.log('can read/write');
} catch (err) {
  console.error('no access!');
}
```

### `fs.appendFileSync(path, data[, options])`

Added in: v0.6.7

**History**

| Version | Changes |
| --- | --- |
| v21.1.0, v20.10.0 | The `flush` option is now supported. |
| v7.0.0 | The passed `options` object will never be modified. |
| v5.0.0 | The `file` parameter can be a file descriptor now. |

- `path` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) | [`<Buffer>`](07-buffer-part1.md#class-buffer) | [`<URL>`](59-url.md#the-whatwg-url-api) | [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) filename or file descriptor
- `data` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) | [`<Buffer>`](07-buffer-part1.md#class-buffer)
- `options` [`<Object>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Object) | [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type)
  - `encoding` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) | [`<null>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#null_type) **Default:** `'utf8'`
  - `mode` [`<integer>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) **Default:** `0o666`
  - `flag` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) See [support of file system `flags`](24-fs-part7.md#file-system-flags). **Default:** `'a'`.
  - `flush` [`<boolean>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#boolean_type) If `true`, the underlying file descriptor is flushed
    prior to closing it. **Default:** `false`.

Synchronously append data to a file, creating the file if it does not yet
exist. `data` can be a string or a [`<Buffer>`](07-buffer-part1.md#class-buffer).

The `mode` option only affects the newly created file. See [`fs.open()`](24-fs-part4.md#fsopenpath-flags-mode-callback)
for more details.

```js
import { appendFileSync } from 'node:fs';

try {
  appendFileSync('message.txt', 'data to append');
  console.log('The "data to append" was appended to file!');
} catch (err) {
  /* Handle the error */
}
```

If `options` is a string, then it specifies the encoding:

```js
import { appendFileSync } from 'node:fs';

appendFileSync('message.txt', 'data to append', 'utf8');
```

The `path` may be specified as a numeric file descriptor that has been opened
for appending (using `fs.open()` or `fs.openSync()`). The file descriptor will
not be closed automatically.

```js
import { openSync, closeSync, appendFileSync } from 'node:fs';

let fd;

try {
  fd = openSync('message.txt', 'a');
  appendFileSync(fd, 'data to append', 'utf8');
} catch (err) {
  /* Handle the error */
} finally {
  if (fd !== undefined)
    closeSync(fd);
}
```

### `fs.chmodSync(path, mode)`

Added in: v0.6.7

**History**

| Version | Changes |
| --- | --- |
| v7.6.0 | The `path` parameter can be a WHATWG `URL` object using `file:` protocol. |

- `path` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) | [`<Buffer>`](07-buffer-part1.md#class-buffer) | [`<URL>`](59-url.md#the-whatwg-url-api)
- `mode` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) | [`<integer>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type)

For detailed information, see the documentation of the asynchronous version of
this API: [`fs.chmod()`](24-fs-part3.md#fschmodpath-mode-callback).

See the POSIX [`chmod(2)`](http://man7.org/linux/man-pages/man2/chmod.2.html) documentation for more detail.

### `fs.chownSync(path, uid, gid)`

Added in: v0.1.97

**History**

| Version | Changes |
| --- | --- |
| v7.6.0 | The `path` parameter can be a WHATWG `URL` object using `file:` protocol. |

- `path` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) | [`<Buffer>`](07-buffer-part1.md#class-buffer) | [`<URL>`](59-url.md#the-whatwg-url-api)
- `uid` [`<integer>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type)
- `gid` [`<integer>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type)

Synchronously changes owner and group of a file. Returns `undefined`.
This is the synchronous version of [`fs.chown()`](24-fs-part3.md#fschownpath-uid-gid-callback).

See the POSIX [`chown(2)`](http://man7.org/linux/man-pages/man2/chown.2.html) documentation for more detail.

### `fs.closeSync(fd)`

Added in: v0.1.21

- `fd` [`<integer>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type)

Closes the file descriptor. Returns `undefined`.

Calling `fs.closeSync()` on any file descriptor (`fd`) that is currently in use
through any other `fs` operation may lead to undefined behavior.

See the POSIX [`close(2)`](http://man7.org/linux/man-pages/man2/close.2.html) documentation for more detail.

### `fs.copyFileSync(src, dest[, mode])`

Added in: v8.5.0

**History**

| Version | Changes |
| --- | --- |
| v14.0.0 | Changed `flags` argument to `mode` and imposed stricter type validation. |

- `src` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) | [`<Buffer>`](07-buffer-part1.md#class-buffer) | [`<URL>`](59-url.md#the-whatwg-url-api) source filename to copy
- `dest` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) | [`<Buffer>`](07-buffer-part1.md#class-buffer) | [`<URL>`](59-url.md#the-whatwg-url-api) destination filename of the copy operation
- `mode` [`<integer>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) modifiers for copy operation. **Default:** `0`.

Synchronously copies `src` to `dest`. By default, `dest` is overwritten if it
already exists. Returns `undefined`. Node.js makes no guarantees about the
atomicity of the copy operation. If an error occurs after the destination file
has been opened for writing, Node.js will attempt to remove the destination.

Symbolic links are followed. If `src` is a symbolic link, the target file is
copied. If `dest` is a symbolic link, the target file is overwritten unless
`mode` contains `fs.constants.COPYFILE_EXCL`.

`mode` is an optional integer that specifies the behavior
of the copy operation. It is possible to create a mask consisting of the bitwise
OR of two or more values (e.g.
`fs.constants.COPYFILE_EXCL | fs.constants.COPYFILE_FICLONE`).

- `fs.constants.COPYFILE_EXCL`: The copy operation will fail if `dest` already
  exists.
- `fs.constants.COPYFILE_FICLONE`: The copy operation will attempt to create a
  copy-on-write reflink. If the platform does not support copy-on-write, then a
  fallback copy mechanism is used.
- `fs.constants.COPYFILE_FICLONE_FORCE`: The copy operation will attempt to
  create a copy-on-write reflink. If the platform does not support
  copy-on-write, then the operation will fail.

```js
import { copyFileSync, constants } from 'node:fs';

// destination.txt will be created or overwritten by default.
copyFileSync('source.txt', 'destination.txt');
console.log('source.txt was copied to destination.txt');

// By using COPYFILE_EXCL, the operation will fail if destination.txt exists.
copyFileSync('source.txt', 'destination.txt', constants.COPYFILE_EXCL);
```

### `fs.cpSync(src, dest[, options])`

Added in: v16.7.0

**History**

| Version | Changes |
| --- | --- |
| v22.3.0 | This API is no longer experimental. |
| v20.1.0, v18.17.0 | Accept an additional `mode` option to specify the copy behavior as the `mode` argument of `fs.copyFile()`. |
| v17.6.0, v16.15.0 | Accepts an additional `verbatimSymlinks` option to specify whether to perform path resolution for symlinks. |

- `src` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) | [`<URL>`](59-url.md#the-whatwg-url-api) source path to copy.
- `dest` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) | [`<URL>`](59-url.md#the-whatwg-url-api) destination path to copy to.
- `options` [`<Object>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Object)
  - `dereference` [`<boolean>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#boolean_type) dereference symlinks. **Default:** `false`.
  - `errorOnExist` [`<boolean>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#boolean_type) when `force` is `false`, and the destination
    exists, throw an error. **Default:** `false`.
  - `filter` [`<Function>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Function) Function to filter copied files/directories. Return `true` to copy the item, `false` to ignore it. When ignoring a directory,
    all of its contents will be skipped as well. **Default:** `undefined`
    - `src` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) source path to copy.
    - `dest` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) destination path to copy to.
    - Returns: [`<boolean>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#boolean_type) Any non-`Promise` value that is coercible
      to `boolean`.
  - `force` [`<boolean>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#boolean_type) overwrite existing file or directory. The copy
    operation will ignore errors if you set this to false and the destination
    exists. Use the `errorOnExist` option to change this behavior.
    **Default:** `true`.
  - `mode` [`<integer>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) modifiers for copy operation. **Default:** `0`.
    See `mode` flag of [`fs.copyFileSync()`](#fscopyfilesyncsrc-dest-mode).
  - `preserveTimestamps` [`<boolean>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#boolean_type) When `true` timestamps from `src` will
    be preserved. **Default:** `false`.
  - `recursive` [`<boolean>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#boolean_type) copy directories recursively **Default:** `false`
  - `verbatimSymlinks` [`<boolean>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#boolean_type) When `true`, path resolution for symlinks will
    be skipped. **Default:** `false`

Synchronously copies the entire directory structure from `src` to `dest`,
including subdirectories and files.

When copying a directory to another directory, globs are not supported and
behavior is similar to `cp dir1/ dir2/`.

### `fs.existsSync(path)`

Added in: v0.1.21

**History**

| Version | Changes |
| --- | --- |
| v7.6.0 | The `path` parameter can be a WHATWG `URL` object using `file:` protocol. |

- `path` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) | [`<Buffer>`](07-buffer-part1.md#class-buffer) | [`<URL>`](59-url.md#the-whatwg-url-api)
- Returns: [`<boolean>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#boolean_type)

Returns `true` if the path exists, `false` otherwise.

For detailed information, see the documentation of the asynchronous version of
this API: [`fs.exists()`](24-fs-part3.md#fsexistspath-callback).

`fs.exists()` is deprecated, but `fs.existsSync()` is not. The `callback`
parameter to `fs.exists()` accepts parameters that are inconsistent with other
Node.js callbacks. `fs.existsSync()` does not use a callback.

```js
import { existsSync } from 'node:fs';

if (existsSync('/etc/passwd'))
  console.log('The path exists.');
```

### `fs.fchmodSync(fd, mode)`

Added in: v0.4.7

- `fd` [`<integer>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type)
- `mode` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) | [`<integer>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type)

Sets the permissions on the file. Returns `undefined`.

See the POSIX [`fchmod(2)`](http://man7.org/linux/man-pages/man2/fchmod.2.html) documentation for more detail.

### `fs.fchownSync(fd, uid, gid)`

Added in: v0.4.7

- `fd` [`<integer>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type)
- `uid` [`<integer>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) The file's new owner's user id.
- `gid` [`<integer>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) The file's new group's group id.

Sets the owner of the file. Returns `undefined`.

See the POSIX [`fchown(2)`](http://man7.org/linux/man-pages/man2/fchown.2.html) documentation for more detail.

### `fs.fdatasyncSync(fd)`

Added in: v0.1.96

- `fd` [`<integer>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type)

Forces all currently queued I/O operations associated with the file to the
operating system's synchronized I/O completion state. Refer to the POSIX
[`fdatasync(2)`](http://man7.org/linux/man-pages/man2/fdatasync.2.html) documentation for details. Returns `undefined`.

### `fs.fstatSync(fd[, options])`

Added in: v0.1.95

**History**

| Version | Changes |
| --- | --- |
| v10.5.0 | Accepts an additional `options` object to specify whether the numeric values returned should be bigint. |

- `fd` [`<integer>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type)
- `options` [`<Object>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Object)
  - `bigint` [`<boolean>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#boolean_type) Whether the numeric values in the returned
    [`<fs.Stats>`](24-fs-part6.md#class-fsstats) object should be `bigint`. **Default:** `false`.
- Returns: [`<fs.Stats>`](24-fs-part6.md#class-fsstats)

Retrieves the [`<fs.Stats>`](24-fs-part6.md#class-fsstats) for the file descriptor.

See the POSIX [`fstat(2)`](http://man7.org/linux/man-pages/man2/fstat.2.html) documentation for more detail.

### `fs.fsyncSync(fd)`

Added in: v0.1.96

- `fd` [`<integer>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type)

Request that all data for the open file descriptor is flushed to the storage
device. The specific implementation is operating system and device specific.
Refer to the POSIX [`fsync(2)`](http://man7.org/linux/man-pages/man2/fsync.2.html) documentation for more detail. Returns `undefined`.

### `fs.ftruncateSync(fd[, len])`

Added in: v0.8.6

- `fd` [`<integer>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type)
- `len` [`<integer>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) **Default:** `0`

Truncates the file descriptor. Returns `undefined`.

For detailed information, see the documentation of the asynchronous version of
this API: [`fs.ftruncate()`](24-fs-part3.md#fsftruncatefd-len-callback).

### `fs.futimesSync(fd, atime, mtime)`

Added in: v0.4.2

**History**

| Version | Changes |
| --- | --- |
| v4.1.0 | Numeric strings, `NaN`, and `Infinity` are now allowed time specifiers. |

- `fd` [`<integer>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type)
- `atime` [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) | [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) | [`<Date>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Date)
- `mtime` [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) | [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) | [`<Date>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Date)

Synchronous version of [`fs.futimes()`](24-fs-part3.md#fsfutimesfd-atime-mtime-callback). Returns `undefined`.

### `fs.globSync(pattern[, options])`

Added in: v22.0.0

**History**

| Version | Changes |
| --- | --- |
| v26.9.0 | Add support for the `maxDepth` option. |
| v26.1.0 | Add support for the `followSymlinks` option. |
| v24.1.0, v22.17.0 | Add support for `URL` instances for `cwd` option. |
| v24.0.0, v22.17.0 | Marking the API stable. |
| v23.7.0, v22.14.0 | Add support for `exclude` option to accept glob patterns. |
| v22.2.0 | Add support for `withFileTypes` as an option. |

- `pattern` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) | [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type)[]
- `options` [`<Object>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Object)
  - `cwd` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) | [`<URL>`](59-url.md#the-whatwg-url-api) current working directory. **Default:** `process.cwd()`
  - `exclude` [`<Function>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Function) | [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type)[] Function to filter out files/directories or a
    list of glob patterns to be excluded. If a function is provided, return `true` to exclude the item, `false` to include it. **Default:** `undefined`.
  - `followSymlinks` [`<boolean>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#boolean_type) When `true`, symbolic links to directories are
    followed while expanding `**` patterns. **Default:** `false`.
  - `maxDepth` [`<integer>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) Maximum number of directory levels to traverse.
    The `cwd` directory has a depth of `0`. **Default:** `Infinity`.
  - `withFileTypes` [`<boolean>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#boolean_type) `true` if the glob should return paths as Dirents,
    `false` otherwise. **Default:** `false`.
- Returns: [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type)[] paths of files that match the pattern.

When `followSymlinks` is enabled, detected symbolic link cycles are not
traversed recursively.

*ES module version:*

```js
import { globSync } from 'node:fs';

console.log(globSync('**/*.js'));
```

*CommonJS version:*

```js
const { globSync } = require('node:fs');

console.log(globSync('**/*.js'));
```

### `fs.lchmodSync(path, mode)`

Added in: v0.5.0Deprecated in: v0.5.0

Stability: 0 - Deprecated

- `path` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) | [`<Buffer>`](07-buffer-part1.md#class-buffer) | [`<URL>`](59-url.md#the-whatwg-url-api)
- `mode` [`<integer>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type)

Changes the permissions on a symbolic link. Returns `undefined`.

This method is only implemented on macOS.

See the POSIX [`lchmod(2)`](http://man7.org/linux/man-pages/man2/lchmod.2.html) documentation for more detail.

### `fs.lchownSync(path, uid, gid)`

**History**

| Version | Changes |
| --- | --- |
| v10.6.0 | This API is no longer deprecated. |
| v0.4.7 | Documentation-only deprecation. |

- `path` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) | [`<Buffer>`](07-buffer-part1.md#class-buffer) | [`<URL>`](59-url.md#the-whatwg-url-api)
- `uid` [`<integer>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) The file's new owner's user id.
- `gid` [`<integer>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) The file's new group's group id.

Set the owner for the path. Returns `undefined`.

See the POSIX [`lchown(2)`](http://man7.org/linux/man-pages/man2/lchown.2.html) documentation for more details.

### `fs.lutimesSync(path, atime, mtime)`

Added in: v14.5.0, v12.19.0

- `path` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) | [`<Buffer>`](07-buffer-part1.md#class-buffer) | [`<URL>`](59-url.md#the-whatwg-url-api)
- `atime` [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) | [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) | [`<Date>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Date)
- `mtime` [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) | [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) | [`<Date>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Date)

Change the file system timestamps of the symbolic link referenced by `path`.
Returns `undefined`, or throws an exception when parameters are incorrect or
the operation fails. This is the synchronous version of [`fs.lutimes()`](24-fs-part3.md#fslutimespath-atime-mtime-callback).

### `fs.linkSync(existingPath, newPath)`

Added in: v0.1.31

**History**

| Version | Changes |
| --- | --- |
| v7.6.0 | The `existingPath` and `newPath` parameters can be WHATWG `URL` objects using `file:` protocol. Support is currently still *experimental*. |

- `existingPath` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) | [`<Buffer>`](07-buffer-part1.md#class-buffer) | [`<URL>`](59-url.md#the-whatwg-url-api)
- `newPath` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) | [`<Buffer>`](07-buffer-part1.md#class-buffer) | [`<URL>`](59-url.md#the-whatwg-url-api)

Creates a new link from the `existingPath` to the `newPath`. See the POSIX
[`link(2)`](http://man7.org/linux/man-pages/man2/link.2.html) documentation for more detail. Returns `undefined`.

### `fs.lstatSync(path[, options])`

Added in: v0.1.30

**History**

| Version | Changes |
| --- | --- |
| v15.3.0, v14.17.0 | Accepts a `throwIfNoEntry` option to specify whether an exception should be thrown if the entry does not exist. |
| v10.5.0 | Accepts an additional `options` object to specify whether the numeric values returned should be bigint. |
| v7.6.0 | The `path` parameter can be a WHATWG `URL` object using `file:` protocol. |

- `path` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) | [`<Buffer>`](07-buffer-part1.md#class-buffer) | [`<URL>`](59-url.md#the-whatwg-url-api)
- `options` [`<Object>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Object)
  - `bigint` [`<boolean>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#boolean_type) Whether the numeric values in the returned
    [`<fs.Stats>`](24-fs-part6.md#class-fsstats) object should be `bigint`. **Default:** `false`.
  - `throwIfNoEntry` [`<boolean>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#boolean_type) Whether an exception will be thrown
    if no file system entry exists, rather than returning `undefined`.
    **Default:** `true`.
- Returns: [`<fs.Stats>`](24-fs-part6.md#class-fsstats)

Retrieves the [`<fs.Stats>`](24-fs-part6.md#class-fsstats) for the symbolic link referred to by `path`.

See the POSIX [`lstat(2)`](http://man7.org/linux/man-pages/man2/lstat.2.html) documentation for more details.

### `fs.mkdirSync(path[, options])`

Added in: v0.1.21

**History**

| Version | Changes |
| --- | --- |
| v13.11.0, v12.17.0 | In `recursive` mode, the first created path is returned now. |
| v10.12.0 | The second argument can now be an `options` object with `recursive` and `mode` properties. |
| v7.6.0 | The `path` parameter can be a WHATWG `URL` object using `file:` protocol. |

- `path` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) | [`<Buffer>`](07-buffer-part1.md#class-buffer) | [`<URL>`](59-url.md#the-whatwg-url-api)
- `options` [`<Object>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Object) | [`<integer>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type)
  - `recursive` [`<boolean>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#boolean_type) **Default:** `false`
  - `mode` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) | [`<integer>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) Not supported on Windows. **Default:** `0o777`.
- Returns: [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) | [`<undefined>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#undefined_type)

Synchronously creates a directory. Returns `undefined`, or if `recursive` is
`true`, the first directory path created.
This is the synchronous version of [`fs.mkdir()`](24-fs-part3.md#fsmkdirpath-options-callback).

See the POSIX [`mkdir(2)`](http://man7.org/linux/man-pages/man2/mkdir.2.html) documentation for more details.

### `fs.mkdtempSync(prefix[, options])`

Added in: v5.10.0

**History**

| Version | Changes |
| --- | --- |
| v20.6.0, v18.19.0 | The `prefix` parameter now accepts buffers and URL. |
| v16.5.0, v14.18.0 | The `prefix` parameter now accepts an empty string. |

- `prefix` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) | [`<Buffer>`](07-buffer-part1.md#class-buffer) | [`<URL>`](59-url.md#the-whatwg-url-api)
- `options` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) | [`<Object>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Object)
  - `encoding` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) **Default:** `'utf8'`
- Returns: [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) | [`<Buffer>`](07-buffer-part1.md#class-buffer)

Returns the created directory path. If `encoding` is `'buffer'`, then the
resulting directory path is returned as a [`<Buffer>`](07-buffer-part1.md#class-buffer). Otherwise, the path
is returned as a [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) using the specified encoding.

For detailed information, see the documentation of the asynchronous version of
this API: [`fs.mkdtemp()`](24-fs-part3.md#fsmkdtempprefix-options-callback).

The optional `options` argument can be a string specifying an encoding, or an
object with an `encoding` property specifying the character encoding to use.

### `fs.mkdtempDisposableSync(prefix[, options])`

Added in: v24.4.0

- `prefix` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) | [`<Buffer>`](07-buffer-part1.md#class-buffer) | [`<URL>`](59-url.md#the-whatwg-url-api)
- `options` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) | [`<Object>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Object)
  - `encoding` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) **Default:** `'utf8'`
- Returns: [`<Object>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Object) A disposable object:
  - `path` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) | [`<Buffer>`](07-buffer-part1.md#class-buffer) The path of the created directory.
  - `remove` [`<Function>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Function) A function which removes the created directory.
  - `[Symbol.dispose]` [`<Function>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Function) The same as `remove`.

Returns a disposable object whose `path` property holds the created directory
path. If `encoding` is `'buffer'`, the `path` will be a [`<Buffer>`](07-buffer-part1.md#class-buffer). When the
object is disposed, the directory and its contents will be removed if it still
exists. If the directory cannot be deleted, disposal will throw an error. The
object has a `remove()` method which will perform the same task.

See the [MDN documentation on `using` statements](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Statements/using) for more information about
explicit resource management.

For detailed information, see the documentation of [`fs.mkdtemp()`](24-fs-part3.md#fsmkdtempprefix-options-callback).

There is no callback-based version of this API because it is designed for use
with the [`using`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Statements/using) syntax.

The optional `options` argument can be a string specifying an encoding, or an
object with an `encoding` property specifying the character encoding to use.

### `fs.openAsBlobSync(path[, options])`

Added in: v26.10.0

- `path` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) | [`<Buffer>`](07-buffer-part1.md#class-buffer) | [`<URL>`](59-url.md#the-whatwg-url-api)
- `options` [`<Object>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Object)
  - `type` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) An optional mime type for the blob.
- Returns: [`<Blob>`](07-buffer-part1.md#class-blob)

For detailed information, see the documentation of the Promise-returning
version of this API: [`fs.openAsBlob()`](24-fs-part4.md#fsopenasblobpath-options).

### `fs.opendirSync(path[, options])`

Added in: v12.12.0

**History**

| Version | Changes |
| --- | --- |
| v20.1.0, v18.17.0 | Added `recursive` option. |
| v13.1.0, v12.16.0 | The `bufferSize` option was introduced. |

- `path` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) | [`<Buffer>`](07-buffer-part1.md#class-buffer) | [`<URL>`](59-url.md#the-whatwg-url-api)
- `options` [`<Object>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Object)
  - `encoding` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) | [`<null>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#null_type) **Default:** `'utf8'`
  - `bufferSize` [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) Number of directory entries that are buffered
    internally when reading from the directory. Higher values lead to better
    performance but higher memory usage. **Default:** `32`
  - `recursive` [`<boolean>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#boolean_type) **Default:** `false`
- Returns: [`<fs.Dir>`](24-fs-part6.md#class-fsdir)

Synchronously open a directory. See [`opendir(3)`](http://man7.org/linux/man-pages/man3/opendir.3.html).

Creates an [`<fs.Dir>`](24-fs-part6.md#class-fsdir), which contains all further functions for reading from
and cleaning up the directory.

The `encoding` option sets the encoding for the `path` while opening the
directory and subsequent read operations.

### `fs.openSync(path[, flags[, mode]])`

Added in: v0.1.21

**History**

| Version | Changes |
| --- | --- |
| v11.1.0 | The `flags` argument is now optional and defaults to `'r'`. |
| v9.9.0 | The `as` and `as+` flags are supported now. |
| v7.6.0 | The `path` parameter can be a WHATWG `URL` object using `file:` protocol. |

- `path` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) | [`<Buffer>`](07-buffer-part1.md#class-buffer) | [`<URL>`](59-url.md#the-whatwg-url-api)
- `flags` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) | [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) **Default:** `'r'`.
  See [support of file system `flags`](24-fs-part7.md#file-system-flags).
- `mode` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) | [`<integer>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) **Default:** `0o666`
- Returns: [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type)

Returns an integer representing the file descriptor.

For detailed information, see the documentation of the asynchronous version of
this API: [`fs.open()`](24-fs-part4.md#fsopenpath-flags-mode-callback).

### `fs.readdirSync(path[, options])`

Added in: v0.1.21

**History**

| Version | Changes |
| --- | --- |
| v20.1.0, v18.17.0 | Added `recursive` option. |
| v10.10.0 | New option `withFileTypes` was added. |
| v7.6.0 | The `path` parameter can be a WHATWG `URL` object using `file:` protocol. |

- `path` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) | [`<Buffer>`](07-buffer-part1.md#class-buffer) | [`<URL>`](59-url.md#the-whatwg-url-api)
- `options` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) | [`<Object>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Object)
  - `encoding` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) **Default:** `'utf8'`
  - `withFileTypes` [`<boolean>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#boolean_type) **Default:** `false`
  - `recursive` [`<boolean>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#boolean_type) If `true`, reads the contents of a directory
    recursively. In recursive mode, it will list all files, sub files, and
    directories. **Default:** `false`.
- Returns: [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type)[] | [`<Buffer>`](07-buffer-part1.md#class-buffer)[] | [`<fs.Dirent>`](24-fs-part6.md#class-fsdirent)[]

Reads the contents of the directory.

See the POSIX [`readdir(3)`](http://man7.org/linux/man-pages/man3/readdir.3.html) documentation for more details.

The optional `options` argument can be a string specifying an encoding, or an
object with an `encoding` property specifying the character encoding to use for
the filenames returned. If the `encoding` is set to `'buffer'`,
the filenames returned will be passed as [`<Buffer>`](07-buffer-part1.md#class-buffer) objects.

If `options.withFileTypes` is set to `true`, the result will contain
[`<fs.Dirent>`](24-fs-part6.md#class-fsdirent) objects.

### `fs.readFileSync(path[, options])`

Added in: v0.1.8

**History**

| Version | Changes |
| --- | --- |
| v26.4.0 | Added support for the `buffer` option. |
| v7.6.0 | The `path` parameter can be a WHATWG `URL` object using `file:` protocol. |
| v5.0.0 | The `path` parameter can be a file descriptor now. |

- `path` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) | [`<Buffer>`](07-buffer-part1.md#class-buffer) | [`<URL>`](59-url.md#the-whatwg-url-api) | [`<integer>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) filename or file descriptor
- `options` [`<Object>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Object) | [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type)
  - `encoding` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) | [`<null>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#null_type) **Default:** `null`
  - `flag` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) See [support of file system `flags`](24-fs-part7.md#file-system-flags). **Default:** `'r'`.
  - `buffer` [`<Buffer>`](07-buffer-part1.md#class-buffer) | [`<TypedArray>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/TypedArray) | [`<DataView>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/DataView) | [`<Function>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Function) A buffer to read into, or a
    function called with the file size that returns the buffer.
- Returns: [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) | [`<Buffer>`](07-buffer-part1.md#class-buffer)

Returns the contents of the `path`.

For detailed information, see the documentation of the asynchronous version of
this API: [`fs.readFile()`](24-fs-part4.md#fsreadfilepath-options-callback).

If the `encoding` option is specified then this function returns a
string. Otherwise it returns a buffer.

If `buffer` is provided and no encoding is specified, the returned [`<Buffer>`](07-buffer-part1.md#class-buffer) is
a view over the supplied buffer containing only the bytes read. If the
supplied buffer is too small to contain the entire file, an error will be
thrown.

Similar to [`fs.readFile()`](24-fs-part4.md#fsreadfilepath-options-callback), when the path is a directory, the behavior of
`fs.readFileSync()` is platform-specific.

```js
import { readFileSync } from 'node:fs';

// macOS, Linux, and Windows
readFileSync('<directory>');
// => [Error: EISDIR: illegal operation on a directory, read <directory>]

//  FreeBSD
readFileSync('<directory>'); // => <data>
```

### `fs.readlinkSync(path[, options])`

Added in: v0.1.31

**History**

| Version | Changes |
| --- | --- |
| v7.6.0 | The `path` parameter can be a WHATWG `URL` object using `file:` protocol. |

- `path` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) | [`<Buffer>`](07-buffer-part1.md#class-buffer) | [`<URL>`](59-url.md#the-whatwg-url-api)
- `options` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) | [`<Object>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Object)
  - `encoding` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) **Default:** `'utf8'`
- Returns: [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) | [`<Buffer>`](07-buffer-part1.md#class-buffer)

Returns the symbolic link's string value.

See the POSIX [`readlink(2)`](http://man7.org/linux/man-pages/man2/readlink.2.html) documentation for more details.

The optional `options` argument can be a string specifying an encoding, or an
object with an `encoding` property specifying the character encoding to use for
the link path returned. If the `encoding` is set to `'buffer'`,
the link path returned will be passed as a [`<Buffer>`](07-buffer-part1.md#class-buffer) object.

[Contents](../README.md) | [Previous: File system, part 4 of 7](24-fs-part4.md) | [Next: File system, part 6 of 7](24-fs-part6.md)
