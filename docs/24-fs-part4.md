[Contents](../README.md) | [Previous: File system, part 3 of 7](24-fs-part3.md) | [Next: File system, part 5 of 7](24-fs-part5.md)

Source: [https://nodejs.org/docs/latest/api/fs.html](https://nodejs.org/docs/latest/api/fs.html)

Part 4 of 7 of this section.

# File system (part 4 of 7)

### `fs.open(path[, flags[, mode]], callback)`

Added in: v0.0.2

**History**

| Version | Changes |
| --- | --- |
| v18.0.0 | Passing an invalid callback to the `callback` argument now throws `ERR_INVALID_ARG_TYPE` instead of `ERR_INVALID_CALLBACK`. |
| v11.1.0 | The `flags` argument is now optional and defaults to `'r'`. |
| v9.9.0 | The `as` and `as+` flags are supported now. |
| v7.6.0 | The `path` parameter can be a WHATWG `URL` object using `file:` protocol. |

- `path` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) | [`<Buffer>`](07-buffer-part1.md#class-buffer) | [`<URL>`](59-url.md#the-whatwg-url-api)
- `flags` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) | [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) See [support of file system `flags`](24-fs-part7.md#file-system-flags).
  **Default:** `'r'`.
- `mode` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) | [`<integer>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) **Default:** `0o666` (readable and writable)
- `callback` [`<Function>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Function)
  - `err` [`<Error>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Error)
  - `fd` [`<integer>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type)

Asynchronous file open. See the POSIX [`open(2)`](http://man7.org/linux/man-pages/man2/open.2.html) documentation for more details.

`mode` sets the file mode (permission and sticky bits), but only if the file was
created. On Windows, only the write permission can be manipulated; see
[`fs.chmod()`](24-fs-part3.md#fschmodpath-mode-callback).

The callback gets two arguments `(err, fd)`.

Some characters (`< > : " / \ | ? *`) are reserved under Windows as documented
by [Naming Files, Paths, and Namespaces](https://docs.microsoft.com/en-us/windows/desktop/FileIO/naming-a-file). Under NTFS, if the filename contains
a colon, Node.js will open a file system stream, as described by
[this MSDN page](https://docs.microsoft.com/en-us/windows/desktop/FileIO/using-streams).

Functions based on `fs.open()` exhibit this behavior as well:
`fs.writeFile()`, `fs.readFile()`, etc.

### `fs.openAsBlob(path[, options])`

Added in: v19.8.0

**History**

| Version | Changes |
| --- | --- |
| v24.0.0, v22.17.0 | Marking the API stable. |

- `path` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) | [`<Buffer>`](07-buffer-part1.md#class-buffer) | [`<URL>`](59-url.md#the-whatwg-url-api)
- `options` [`<Object>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Object)
  - `type` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) An optional mime type for the blob.
- Returns: [`<Promise>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Promise) Fulfills with a [`<Blob>`](07-buffer-part1.md#class-blob) upon success.

Returns a [`<Blob>`](07-buffer-part1.md#class-blob) whose data is backed by the given file.

The file must not be modified after the [`<Blob>`](07-buffer-part1.md#class-blob) is created. Any modifications
will cause reading the [`<Blob>`](07-buffer-part1.md#class-blob) data to fail with a `DOMException` error.
Synchronous stat operations on the file when the `Blob` is created, and before
each read in order to detect whether the file data has been modified on disk.

*ES module version:*

```js
import { openAsBlob } from 'node:fs';

const blob = await openAsBlob('the.file.txt');
const ab = await blob.arrayBuffer();
blob.stream();
```

*CommonJS version:*

```js
const { openAsBlob } = require('node:fs');

(async () => {
  const blob = await openAsBlob('the.file.txt');
  const ab = await blob.arrayBuffer();
  blob.stream();
})();
```

### `fs.opendir(path[, options], callback)`

Added in: v12.12.0

**History**

| Version | Changes |
| --- | --- |
| v20.1.0, v18.17.0 | Added `recursive` option. |
| v18.0.0 | Passing an invalid callback to the `callback` argument now throws `ERR_INVALID_ARG_TYPE` instead of `ERR_INVALID_CALLBACK`. |
| v13.1.0, v12.16.0 | The `bufferSize` option was introduced. |

- `path` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) | [`<Buffer>`](07-buffer-part1.md#class-buffer) | [`<URL>`](59-url.md#the-whatwg-url-api)
- `options` [`<Object>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Object)
  - `encoding` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) | [`<null>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#null_type) **Default:** `'utf8'`
  - `bufferSize` [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) Number of directory entries that are buffered
    internally when reading from the directory. Higher values lead to better
    performance but higher memory usage. **Default:** `32`
  - `recursive` [`<boolean>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#boolean_type) **Default:** `false`
- `callback` [`<Function>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Function)
  - `err` [`<Error>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Error)
  - `dir` [`<fs.Dir>`](24-fs-part6.md#class-fsdir)

Asynchronously open a directory. See the POSIX [`opendir(3)`](http://man7.org/linux/man-pages/man3/opendir.3.html) documentation for
more details.

Creates an [`<fs.Dir>`](24-fs-part6.md#class-fsdir), which contains all further functions for reading from
and cleaning up the directory.

The `encoding` option sets the encoding for the `path` while opening the
directory and subsequent read operations.

### `fs.read(fd, buffer, offset, length, position, callback)`

Added in: v0.0.2

**History**

| Version | Changes |
| --- | --- |
| v18.0.0 | Passing an invalid callback to the `callback` argument now throws `ERR_INVALID_ARG_TYPE` instead of `ERR_INVALID_CALLBACK`. |
| v10.10.0 | The `buffer` parameter can now be any `TypedArray`, or a `DataView`. |
| v7.4.0 | The `buffer` parameter can now be a `Uint8Array`. |
| v6.0.0 | The `length` parameter can now be `0`. |

- `fd` [`<integer>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type)
- `buffer` [`<Buffer>`](07-buffer-part1.md#class-buffer) | [`<TypedArray>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/TypedArray) | [`<DataView>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/DataView) The buffer that the data will be
  written to.
- `offset` [`<integer>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) The position in `buffer` to write the data to.
- `length` [`<integer>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) The number of bytes to read.
- `position` [`<integer>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) | [`<bigint>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#bigint_type) | [`<null>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#null_type) Specifies where to begin reading from in the
  file. If `position` is `null` or `-1` , data will be read from the current
  file position, and the file position will be updated. If `position` is
  a non-negative integer, the file position will be unchanged.
- `callback` [`<Function>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Function)
  - `err` [`<Error>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Error)
  - `bytesRead` [`<integer>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type)
  - `buffer` [`<Buffer>`](07-buffer-part1.md#class-buffer)

Read data from the file specified by `fd`.

The callback is given the three arguments, `(err, bytesRead, buffer)`.

If the file is not modified concurrently, the end-of-file is reached when the
number of bytes read is zero.

If this method is invoked as its [`util.promisify()`](60-util-part2.md#utilpromisifyoriginal)ed version, it returns
a promise for an `Object` with `bytesRead` and `buffer` properties.

The `fs.read()` method reads data from the file specified
by the file descriptor (`fd`).
The `length` argument indicates the maximum number
of bytes that Node.js
will attempt to read from the kernel.
However, the actual number of bytes read (`bytesRead`) can be lower
than the specified `length` for various reasons.

For example:

- If the file is shorter than the specified `length`, `bytesRead`
  will be set to the actual number of bytes read.
- If the file encounters EOF (End of File) before the buffer could
  be filled, Node.js will read all available bytes until EOF is encountered,
  and the `bytesRead` parameter in the callback will indicate
  the actual number of bytes read, which may be less than the specified `length`.
- If the file is on a slow network `filesystem`
  or encounters any other issue during reading,
  `bytesRead` can be lower than the specified `length`.

Therefore, when using `fs.read()`, it's important to
check the `bytesRead` value to
determine how many bytes were actually read from the file.
Depending on your application
logic, you may need to handle cases where `bytesRead`
is lower than the specified `length`,
such as by wrapping the read call in a loop if you require
a minimum amount of bytes.

This behavior is similar to the POSIX `preadv2` function.

### `fs.read(fd[, options], callback)`

Added in: v13.11.0, v12.17.0

**History**

| Version | Changes |
| --- | --- |
| v13.11.0, v12.17.0 | Options object can be passed in to make buffer, offset, length, and position optional. |

- `fd` [`<integer>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type)
- `options` [`<Object>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Object)
  - `buffer` [`<Buffer>`](07-buffer-part1.md#class-buffer) | [`<TypedArray>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/TypedArray) | [`<DataView>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/DataView) **Default:** `Buffer.alloc(16384)`
  - `offset` [`<integer>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) **Default:** `0`
  - `length` [`<integer>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) **Default:** `buffer.byteLength - offset`
  - `position` [`<integer>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) | [`<bigint>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#bigint_type) | [`<null>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#null_type) **Default:** `null`
- `callback` [`<Function>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Function)
  - `err` [`<Error>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Error)
  - `bytesRead` [`<integer>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type)
  - `buffer` [`<Buffer>`](07-buffer-part1.md#class-buffer)

Similar to the [`fs.read()`](#fsreadfd-buffer-offset-length-position-callback) function, this version takes an optional
`options` object. If no `options` object is specified, it will default with the
above values.

### `fs.read(fd, buffer[, options], callback)`

Added in: v18.2.0, v16.17.0

- `fd` [`<integer>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type)
- `buffer` [`<Buffer>`](07-buffer-part1.md#class-buffer) | [`<TypedArray>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/TypedArray) | [`<DataView>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/DataView) The buffer that the data will be
  written to.
- `options` [`<Object>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Object)
  - `offset` [`<integer>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) **Default:** `0`
  - `length` [`<integer>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) **Default:** `buffer.byteLength - offset`
  - `position` [`<integer>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) | [`<bigint>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#bigint_type) **Default:** `null`
- `callback` [`<Function>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Function)
  - `err` [`<Error>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Error)
  - `bytesRead` [`<integer>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type)
  - `buffer` [`<Buffer>`](07-buffer-part1.md#class-buffer)

Similar to the [`fs.read()`](#fsreadfd-buffer-offset-length-position-callback) function, this version takes an optional
`options` object. If no `options` object is specified, it will default with the
above values.

### `fs.readdir(path[, options], callback)`

Added in: v0.1.8

**History**

| Version | Changes |
| --- | --- |
| v20.1.0, v18.17.0 | Added `recursive` option. |
| v18.0.0 | Passing an invalid callback to the `callback` argument now throws `ERR_INVALID_ARG_TYPE` instead of `ERR_INVALID_CALLBACK`. |
| v10.10.0 | New option `withFileTypes` was added. |
| v10.0.0 | The `callback` parameter is no longer optional. Not passing it will throw a `TypeError` at runtime. |
| v7.6.0 | The `path` parameter can be a WHATWG `URL` object using `file:` protocol. |
| v7.0.0 | The `callback` parameter is no longer optional. Not passing it will emit a deprecation warning with id DEP0013. |
| v6.0.0 | The `options` parameter was added. |

- `path` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) | [`<Buffer>`](07-buffer-part1.md#class-buffer) | [`<URL>`](59-url.md#the-whatwg-url-api)
- `options` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) | [`<Object>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Object)
  - `encoding` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) **Default:** `'utf8'`
  - `withFileTypes` [`<boolean>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#boolean_type) **Default:** `false`
  - `recursive` [`<boolean>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#boolean_type) If `true`, reads the contents of a directory
    recursively. In recursive mode, it will list all files, sub files and
    directories. **Default:** `false`.
- `callback` [`<Function>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Function)
  - `err` [`<Error>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Error)
  - `files` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type)[] | [`<Buffer>`](07-buffer-part1.md#class-buffer)[] | [`<fs.Dirent>`](24-fs-part6.md#class-fsdirent)[]

Reads the contents of a directory. The callback gets two arguments `(err, files)`
where `files` is an array of the names of the files in the directory excluding
`'.'` and `'..'`.

See the POSIX [`readdir(3)`](http://man7.org/linux/man-pages/man3/readdir.3.html) documentation for more details.

The optional `options` argument can be a string specifying an encoding, or an
object with an `encoding` property specifying the character encoding to use for
the filenames passed to the callback. If the `encoding` is set to `'buffer'`,
the filenames returned will be passed as [`<Buffer>`](07-buffer-part1.md#class-buffer) objects.

If `options.withFileTypes` is set to `true`, the `files` array will contain
[`<fs.Dirent>`](24-fs-part6.md#class-fsdirent) objects.

### `fs.readFile(path[, options], callback)`

Added in: v0.1.29

**History**

| Version | Changes |
| --- | --- |
| v26.4.0 | Added support for the `buffer` option. |
| v18.0.0 | Passing an invalid callback to the `callback` argument now throws `ERR_INVALID_ARG_TYPE` instead of `ERR_INVALID_CALLBACK`. |
| v16.0.0 | The error returned may be an `AggregateError` if more than one error is returned. |
| v15.2.0, v14.17.0 | The options argument may include an AbortSignal to abort an ongoing readFile request. |
| v10.0.0 | The `callback` parameter is no longer optional. Not passing it will throw a `TypeError` at runtime. |
| v7.6.0 | The `path` parameter can be a WHATWG `URL` object using `file:` protocol. |
| v7.0.0 | The `callback` parameter is no longer optional. Not passing it will emit a deprecation warning with id DEP0013. |
| v5.1.0 | The `callback` will always be called with `null` as the `error` parameter in case of success. |
| v5.0.0 | The `path` parameter can be a file descriptor now. |

- `path` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) | [`<Buffer>`](07-buffer-part1.md#class-buffer) | [`<URL>`](59-url.md#the-whatwg-url-api) | [`<integer>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) filename or file descriptor
- `options` [`<Object>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Object) | [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type)
  - `encoding` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) | [`<null>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#null_type) **Default:** `null`
  - `flag` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) See [support of file system `flags`](24-fs-part7.md#file-system-flags). **Default:** `'r'`.
  - `signal` [`<AbortSignal>`](26-globals.md#class-abortsignal) allows aborting an in-progress readFile
  - `buffer` [`<Buffer>`](07-buffer-part1.md#class-buffer) | [`<TypedArray>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/TypedArray) | [`<DataView>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/DataView) | [`<Function>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Function) A buffer to read into, or a
    function called with the file size that returns the buffer.
- `callback` [`<Function>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Function)
  - `err` [`<Error>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Error) | [`<AggregateError>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/AggregateError)
  - `data` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) | [`<Buffer>`](07-buffer-part1.md#class-buffer)

Asynchronously reads the entire contents of a file.

```js
import { readFile } from 'node:fs';

readFile('/etc/passwd', (err, data) => {
  if (err) throw err;
  console.log(data);
});
```

The callback is passed two arguments `(err, data)`, where `data` is the
contents of the file.

If no encoding is specified, then the raw buffer is returned.

If `buffer` is provided and no encoding is specified, the returned [`<Buffer>`](07-buffer-part1.md#class-buffer) is
a view over the supplied buffer containing only the bytes read. If the
supplied buffer is too small to contain the entire file, the callback is
called with an error.

If `options` is a string, then it specifies the encoding:

```js
import { readFile } from 'node:fs';

readFile('/etc/passwd', 'utf8', callback);
```

When the path is a directory, the behavior of `fs.readFile()` and
[`fs.readFileSync()`](24-fs-part5.md#fsreadfilesyncpath-options) is platform-specific. On macOS, Linux, and Windows, an
error will be returned. On FreeBSD, a representation of the directory's contents
will be returned.

```js
import { readFile } from 'node:fs';

// macOS, Linux, and Windows
readFile('<directory>', (err, data) => {
  // => [Error: EISDIR: illegal operation on a directory, read <directory>]
});

//  FreeBSD
readFile('<directory>', (err, data) => {
  // => null, <data>
});
```

It is possible to abort an ongoing request using an `AbortSignal`. If a
request is aborted the callback is called with an `AbortError`:

```js
import { readFile } from 'node:fs';

const controller = new AbortController();
const signal = controller.signal;
readFile(fileInfo[0].name, { signal }, (err, buf) => {
  // ...
});
// When you want to abort the request
controller.abort();
```

The `fs.readFile()` function buffers the entire file. To minimize memory costs,
when possible prefer streaming via `fs.createReadStream()`.

Aborting an ongoing request does not abort individual operating
system requests but rather the internal buffering `fs.readFile` performs.

An example using the `buffer` option with a pre-allocated buffer:

```js
import { Buffer } from 'node:buffer';
import { readFile } from 'node:fs';

const buf = Buffer.alloc(16384);
readFile('/path/to/file', { buffer: buf }, (err, data) => {
  if (err) throw err;
  console.log(data); // A view over `buf` containing only the bytes read
});
```

An example using the `buffer` option with a function returning a buffer:

```js
import { Buffer } from 'node:buffer';
import { readFile } from 'node:fs';

readFile('/path/to/file', {
  buffer: (size) => Buffer.alloc(size),
}, (err, data) => {
  if (err) throw err;
  console.log(data);
});
```

#### File descriptors

1. Any specified file descriptor has to support reading.
2. If a file descriptor is specified as the `path`, it will not be closed
   automatically.
3. The reading will begin at the current position. For example, if the file
   already had `'Hello World'` and six bytes are read with the file descriptor,
   the call to `fs.readFile()` with the same file descriptor, would give
   `'World'`, rather than `'Hello World'`.

#### Performance Considerations

The `fs.readFile()` method asynchronously reads the contents of a file into
memory one chunk at a time, allowing the event loop to turn between each chunk.
This allows the read operation to have less impact on other activity that may
be using the underlying libuv thread pool but means that it will take longer
to read a complete file into memory.

The additional read overhead can vary broadly on different systems and depends
on the type of file being read. If the file type is not a regular file (a pipe
for instance) and Node.js is unable to determine an actual file size, each read
operation will load on 64 KiB of data. For regular files, each read will process
512 KiB of data.

For applications that require as-fast-as-possible reading of file contents, it
is better to use `fs.read()` directly and for application code to manage
reading the full contents of the file itself.

The Node.js GitHub issue [#25741](https://github.com/nodejs/node/issues/25741) provides more information and a detailed
analysis on the performance of `fs.readFile()` for multiple file sizes in
different Node.js versions.

### `fs.readlink(path[, options], callback)`

Added in: v0.1.31

**History**

| Version | Changes |
| --- | --- |
| v18.0.0 | Passing an invalid callback to the `callback` argument now throws `ERR_INVALID_ARG_TYPE` instead of `ERR_INVALID_CALLBACK`. |
| v10.0.0 | The `callback` parameter is no longer optional. Not passing it will throw a `TypeError` at runtime. |
| v7.6.0 | The `path` parameter can be a WHATWG `URL` object using `file:` protocol. |
| v7.0.0 | The `callback` parameter is no longer optional. Not passing it will emit a deprecation warning with id DEP0013. |

- `path` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) | [`<Buffer>`](07-buffer-part1.md#class-buffer) | [`<URL>`](59-url.md#the-whatwg-url-api)
- `options` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) | [`<Object>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Object)
  - `encoding` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) **Default:** `'utf8'`
- `callback` [`<Function>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Function)
  - `err` [`<Error>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Error)
  - `linkString` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) | [`<Buffer>`](07-buffer-part1.md#class-buffer)

Reads the contents of the symbolic link referred to by `path`. The callback gets
two arguments `(err, linkString)`.

See the POSIX [`readlink(2)`](http://man7.org/linux/man-pages/man2/readlink.2.html) documentation for more details.

The optional `options` argument can be a string specifying an encoding, or an
object with an `encoding` property specifying the character encoding to use for
the link path passed to the callback. If the `encoding` is set to `'buffer'`,
the link path returned will be passed as a [`<Buffer>`](07-buffer-part1.md#class-buffer) object.

### `fs.readv(fd, buffers[, position], callback)`

Added in: v13.13.0, v12.17.0

**History**

| Version | Changes |
| --- | --- |
| v18.0.0 | Passing an invalid callback to the `callback` argument now throws `ERR_INVALID_ARG_TYPE` instead of `ERR_INVALID_CALLBACK`. |

- `fd` [`<integer>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type)
- `buffers` [`<ArrayBufferView>`](https://developer.mozilla.org/docs/Web/API/ArrayBufferView)[]
- `position` [`<integer>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) | [`<null>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#null_type) **Default:** `null`
- `callback` [`<Function>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Function)
  - `err` [`<Error>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Error)
  - `bytesRead` [`<integer>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type)
  - `buffers` [`<ArrayBufferView>`](https://developer.mozilla.org/docs/Web/API/ArrayBufferView)[]

Read from a file specified by `fd` and write to an array of `ArrayBufferView`s
using `readv()`.

`position` is the offset from the beginning of the file from where data
should be read. If `typeof position !== 'number'`, the data will be read
from the current position.

The callback will be given three arguments: `err`, `bytesRead`, and
`buffers`. `bytesRead` is how many bytes were read from the file.

If this method is invoked as its [`util.promisify()`](60-util-part2.md#utilpromisifyoriginal)ed version, it returns
a promise for an `Object` with `bytesRead` and `buffers` properties.

### `fs.realpath(path[, options], callback)`

Added in: v0.1.31

**History**

| Version | Changes |
| --- | --- |
| v18.0.0 | Passing an invalid callback to the `callback` argument now throws `ERR_INVALID_ARG_TYPE` instead of `ERR_INVALID_CALLBACK`. |
| v10.0.0 | The `callback` parameter is no longer optional. Not passing it will throw a `TypeError` at runtime. |
| v8.0.0 | Pipe/Socket resolve support was added. |
| v7.6.0 | The `path` parameter can be a WHATWG `URL` object using `file:` protocol. |
| v7.0.0 | The `callback` parameter is no longer optional. Not passing it will emit a deprecation warning with id DEP0013. |
| v6.4.0 | Calling `realpath` now works again for various edge cases on Windows. |
| v6.0.0 | The `cache` parameter was removed. |

- `path` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) | [`<Buffer>`](07-buffer-part1.md#class-buffer) | [`<URL>`](59-url.md#the-whatwg-url-api)
- `options` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) | [`<Object>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Object)
  - `encoding` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) **Default:** `'utf8'`
- `callback` [`<Function>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Function)
  - `err` [`<Error>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Error)
  - `resolvedPath` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) | [`<Buffer>`](07-buffer-part1.md#class-buffer)

Asynchronously computes the canonical pathname by resolving `.`, `..`, and
symbolic links.

A canonical pathname is not necessarily unique. Hard links and bind mounts can
expose a file system entity through many pathnames.

This function behaves like [`realpath(3)`](http://man7.org/linux/man-pages/man3/realpath.3.html), with some exceptions:

1. No case conversion is performed on case-insensitive file systems.
2. The maximum number of symbolic links is platform-independent and generally
   (much) higher than what the native [`realpath(3)`](http://man7.org/linux/man-pages/man3/realpath.3.html) implementation supports.

The `callback` gets two arguments `(err, resolvedPath)`. May use `process.cwd`
to resolve relative paths.

Only paths that can be converted to UTF8 strings are supported.

The optional `options` argument can be a string specifying an encoding, or an
object with an `encoding` property specifying the character encoding to use for
the path passed to the callback. If the `encoding` is set to `'buffer'`,
the path returned will be passed as a [`<Buffer>`](07-buffer-part1.md#class-buffer) object.

If `path` resolves to a socket or a pipe, the function will return a system
dependent name for that object.

A path that does not exist results in an ENOENT error.
`error.path` is the absolute file path.

### `fs.realpath.native(path[, options], callback)`

Added in: v9.2.0

**History**

| Version | Changes |
| --- | --- |
| v18.0.0 | Passing an invalid callback to the `callback` argument now throws `ERR_INVALID_ARG_TYPE` instead of `ERR_INVALID_CALLBACK`. |

- `path` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) | [`<Buffer>`](07-buffer-part1.md#class-buffer) | [`<URL>`](59-url.md#the-whatwg-url-api)
- `options` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) | [`<Object>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Object)
  - `encoding` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) **Default:** `'utf8'`
- `callback` [`<Function>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Function)
  - `err` [`<Error>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Error)
  - `resolvedPath` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) | [`<Buffer>`](07-buffer-part1.md#class-buffer)

Asynchronous [`realpath(3)`](http://man7.org/linux/man-pages/man3/realpath.3.html).

The `callback` gets two arguments `(err, resolvedPath)`.

Only paths that can be converted to UTF8 strings are supported.

The optional `options` argument can be a string specifying an encoding, or an
object with an `encoding` property specifying the character encoding to use for
the path passed to the callback. If the `encoding` is set to `'buffer'`,
the path returned will be passed as a [`<Buffer>`](07-buffer-part1.md#class-buffer) object.

On Linux, when Node.js is linked against musl libc, the procfs file system must
be mounted on `/proc` in order for this function to work. Glibc does not have
this restriction.

### `fs.rename(oldPath, newPath, callback)`

Added in: v0.0.2

**History**

| Version | Changes |
| --- | --- |
| v18.0.0 | Passing an invalid callback to the `callback` argument now throws `ERR_INVALID_ARG_TYPE` instead of `ERR_INVALID_CALLBACK`. |
| v10.0.0 | The `callback` parameter is no longer optional. Not passing it will throw a `TypeError` at runtime. |
| v7.6.0 | The `oldPath` and `newPath` parameters can be WHATWG `URL` objects using `file:` protocol. Support is currently still *experimental*. |
| v7.0.0 | The `callback` parameter is no longer optional. Not passing it will emit a deprecation warning with id DEP0013. |

- `oldPath` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) | [`<Buffer>`](07-buffer-part1.md#class-buffer) | [`<URL>`](59-url.md#the-whatwg-url-api)
- `newPath` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) | [`<Buffer>`](07-buffer-part1.md#class-buffer) | [`<URL>`](59-url.md#the-whatwg-url-api)
- `callback` [`<Function>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Function)
  - `err` [`<Error>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Error)

Asynchronously rename file at `oldPath` to the pathname provided
as `newPath`. In the case that `newPath` already exists, it will
be overwritten. If there is a directory at `newPath`, an error will
be raised instead. No arguments other than a possible exception are
given to the completion callback.

See also: [`rename(2)`](http://man7.org/linux/man-pages/man2/rename.2.html).

```js
import { rename } from 'node:fs';

rename('oldFile.txt', 'newFile.txt', (err) => {
  if (err) throw err;
  console.log('Rename complete!');
});
```

### `fs.rmdir(path[, options], callback)`

Added in: v0.0.2

**History**

| Version | Changes |
| --- | --- |
| v25.0.0 | Remove `recursive` option. |
| v18.0.0 | Passing an invalid callback to the `callback` argument now throws `ERR_INVALID_ARG_TYPE` instead of `ERR_INVALID_CALLBACK`. |
| v16.0.0 | Using `fs.rmdir(path, { recursive: true })` on a `path` that is a file is no longer permitted and results in an `ENOENT` error on Windows and an `ENOTDIR` error on POSIX. |
| v16.0.0 | Using `fs.rmdir(path, { recursive: true })` on a `path` that does not exist is no longer permitted and results in a `ENOENT` error. |
| v16.0.0 | The `recursive` option is deprecated, using it triggers a deprecation warning. |
| v14.14.0 | The `recursive` option is deprecated, use `fs.rm` instead. |
| v13.3.0, v12.16.0 | The `maxBusyTries` option is renamed to `maxRetries`, and its default is 0. The `emfileWait` option has been removed, and `EMFILE` errors use the same retry logic as other errors. The `retryDelay` option is now supported. `ENFILE` errors are now retried. |
| v12.10.0 | The `recursive`, `maxBusyTries`, and `emfileWait` options are now supported. |
| v10.0.0 | The `callback` parameter is no longer optional. Not passing it will throw a `TypeError` at runtime. |
| v7.6.0 | The `path` parameters can be a WHATWG `URL` object using `file:` protocol. |
| v7.0.0 | The `callback` parameter is no longer optional. Not passing it will emit a deprecation warning with id DEP0013. |

- `path` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) | [`<Buffer>`](07-buffer-part1.md#class-buffer) | [`<URL>`](59-url.md#the-whatwg-url-api)
- `options` [`<Object>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Object) There are currently no options exposed. There used to
  be options for `recursive`, `maxBusyTries`, and `emfileWait` but they were
  deprecated and removed. The `options` argument is still accepted for
  backwards compatibility but it is not used.
- `callback` [`<Function>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Function)
  - `err` [`<Error>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Error)

Asynchronous [`rmdir(2)`](http://man7.org/linux/man-pages/man2/rmdir.2.html). No arguments other than a possible exception are given
to the completion callback.

Using `fs.rmdir()` on a file (not a directory) results in an `ENOENT` error on
Windows and an `ENOTDIR` error on POSIX.

To get a behavior similar to the `rm -rf` Unix command, use [`fs.rm()`](#fsrmpath-options-callback)
with options `{ recursive: true, force: true }`.

### `fs.rm(path[, options], callback)`

Added in: v14.14.0

**History**

| Version | Changes |
| --- | --- |
| v17.3.0, v16.14.0 | The `path` parameter can be a WHATWG `URL` object using `file:` protocol. |

- `path` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) | [`<Buffer>`](07-buffer-part1.md#class-buffer) | [`<URL>`](59-url.md#the-whatwg-url-api)
- `options` [`<Object>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Object)
  - `force` [`<boolean>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#boolean_type) When `true`, exceptions will be ignored if `path` does
    not exist. **Default:** `false`.
  - `maxRetries` [`<integer>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) If an `EBUSY`, `EMFILE`, `ENFILE`, `ENOTEMPTY`, or
    `EPERM` error is encountered, Node.js will retry the operation with a linear
    backoff wait of `retryDelay` milliseconds longer on each try. This option
    represents the number of retries. This option is ignored if the `recursive`
    option is not `true`. **Default:** `0`.
  - `recursive` [`<boolean>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#boolean_type) If `true`, perform a recursive removal. In
    recursive mode operations are retried on failure. **Default:** `false`.
  - `retryDelay` [`<integer>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) The amount of time in milliseconds to wait between
    retries. This option is ignored if the `recursive` option is not `true`.
    **Default:** `100`.
- `callback` [`<Function>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Function)
  - `err` [`<Error>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Error)

Asynchronously removes files and directories (modeled on the standard POSIX `rm`
utility). No arguments other than a possible exception are given to the
completion callback.

### `fs.stat(path[, options], callback)`

Added in: v0.0.2

**History**

| Version | Changes |
| --- | --- |
| v25.7.0 | Accepts a `throwIfNoEntry` option to specify whether an exception should be thrown if the entry does not exist. |
| v18.0.0 | Passing an invalid callback to the `callback` argument now throws `ERR_INVALID_ARG_TYPE` instead of `ERR_INVALID_CALLBACK`. |
| v10.5.0 | Accepts an additional `options` object to specify whether the numeric values returned should be bigint. |
| v10.0.0 | The `callback` parameter is no longer optional. Not passing it will throw a `TypeError` at runtime. |
| v7.6.0 | The `path` parameter can be a WHATWG `URL` object using `file:` protocol. |
| v7.0.0 | The `callback` parameter is no longer optional. Not passing it will emit a deprecation warning with id DEP0013. |

- `path` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) | [`<Buffer>`](07-buffer-part1.md#class-buffer) | [`<URL>`](59-url.md#the-whatwg-url-api)
- `options` [`<Object>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Object)
  - `bigint` [`<boolean>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#boolean_type) Whether the numeric values in the returned
    [`<fs.Stats>`](24-fs-part6.md#class-fsstats) object should be `bigint`. **Default:** `false`.
  - `throwIfNoEntry` [`<boolean>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#boolean_type) Whether an exception will be thrown
    if no file system entry exists, rather than returning `undefined`.
    **Default:** `true`.
- `callback` [`<Function>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Function)
  - `err` [`<Error>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Error)
  - `stats` [`<fs.Stats>`](24-fs-part6.md#class-fsstats)

Asynchronous [`stat(2)`](http://man7.org/linux/man-pages/man2/stat.2.html). The callback gets two arguments `(err, stats)` where
`stats` is an [`<fs.Stats>`](24-fs-part6.md#class-fsstats) object.

In case of an error, the `err.code` will be one of [Common System Errors](22-errors-part1.md#common-system-errors).

[`fs.stat()`](#fsstatpath-options-callback) follows symbolic links. Use [`fs.lstat()`](24-fs-part3.md#fslstatpath-options-callback) to look at the
links themselves.

Using `fs.stat()` to check for the existence of a file before calling
`fs.open()`, `fs.readFile()`, or `fs.writeFile()` is not recommended.
Instead, user code should open/read/write the file directly and handle the
error raised if the file is not available.

To check if a file exists without manipulating it afterwards, [`fs.access()`](24-fs-part2.md#fsaccesspath-mode-callback)
is recommended.

For example, given the following directory structure:

```text
- txtDir
-- file.txt
- app.js
```

The next program will check for the stats of the given paths:

```js
import { stat } from 'node:fs';

const pathsToCheck = ['./txtDir', './txtDir/file.txt'];

for (let i = 0; i < pathsToCheck.length; i++) {
  stat(pathsToCheck[i], (err, stats) => {
    console.log(stats.isDirectory());
    console.log(stats);
  });
}
```

The resulting output will resemble:

```console
true
Stats {
  dev: 16777220,
  mode: 16877,
  nlink: 3,
  uid: 501,
  gid: 20,
  rdev: 0,
  blksize: 4096,
  ino: 14214262,
  size: 96,
  blocks: 0,
  atimeMs: 1561174653071.963,
  mtimeMs: 1561174614583.3518,
  ctimeMs: 1561174626623.5366,
  birthtimeMs: 1561174126937.2893,
  atime: 2019-06-22T03:37:33.072Z,
  mtime: 2019-06-22T03:36:54.583Z,
  ctime: 2019-06-22T03:37:06.624Z,
  birthtime: 2019-06-22T03:28:46.937Z,
  atimeInstant: 2019-06-22T03:37:33.071963Z,
  mtimeInstant: 2019-06-22T03:36:54.5833518Z,
  ctimeInstant: 2019-06-22T03:37:06.6235366Z,
  birthtimeInstant: 2019-06-22T03:28:46.9372893Z
}
false
Stats {
  dev: 16777220,
  mode: 33188,
  nlink: 1,
  uid: 501,
  gid: 20,
  rdev: 0,
  blksize: 4096,
  ino: 14214074,
  size: 8,
  blocks: 8,
  atimeMs: 1561174616618.8555,
  mtimeMs: 1561174614584,
  ctimeMs: 1561174614583.8145,
  birthtimeMs: 1561174007710.7478,
  atime: 2019-06-22T03:36:56.619Z,
  mtime: 2019-06-22T03:36:54.584Z,
  ctime: 2019-06-22T03:36:54.584Z,
  birthtime: 2019-06-22T03:26:47.711Z,
  atimeInstant: 2019-06-22T03:36:56.6188555Z,
  mtimeInstant: 2019-06-22T03:36:54.584Z,
  ctimeInstant: 2019-06-22T03:36:54.5838145Z,
  birthtimeInstant: 2019-06-22T03:26:47.7107478Z
}
```

### `fs.statfs(path[, options], callback)`

Added in: v19.6.0, v18.15.0

- `path` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) | [`<Buffer>`](07-buffer-part1.md#class-buffer) | [`<URL>`](59-url.md#the-whatwg-url-api)
- `options` [`<Object>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Object)
  - `bigint` [`<boolean>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#boolean_type) Whether the numeric values in the returned
    [`<fs.StatFs>`](24-fs-part6.md#class-fsstatfs) object should be `bigint`. **Default:** `false`.
- `callback` [`<Function>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Function)
  - `err` [`<Error>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Error)
  - `stats` [`<fs.StatFs>`](24-fs-part6.md#class-fsstatfs)

Asynchronous [`statfs(2)`](http://man7.org/linux/man-pages/man2/statfs.2.html). Returns information about the mounted file system which
contains `path`. The callback gets two arguments `(err, stats)` where `stats` is an [`<fs.StatFs>`](24-fs-part6.md#class-fsstatfs) object.

In case of an error, the `err.code` will be one of [Common System Errors](22-errors-part1.md#common-system-errors).

### `fs.symlink(target, path[, type], callback)`

Added in: v0.1.31

**History**

| Version | Changes |
| --- | --- |
| v18.0.0 | Passing an invalid callback to the `callback` argument now throws `ERR_INVALID_ARG_TYPE` instead of `ERR_INVALID_CALLBACK`. |
| v12.0.0 | If the `type` argument is left undefined, Node will autodetect `target` type and automatically select `dir` or `file`. |
| v7.6.0 | The `target` and `path` parameters can be WHATWG `URL` objects using `file:` protocol. Support is currently still *experimental*. |

- `target` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) | [`<Buffer>`](07-buffer-part1.md#class-buffer) | [`<URL>`](59-url.md#the-whatwg-url-api)
- `path` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) | [`<Buffer>`](07-buffer-part1.md#class-buffer) | [`<URL>`](59-url.md#the-whatwg-url-api)
- `type` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) | [`<null>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#null_type) **Default:** `null`
- `callback` [`<Function>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Function)
  - `err` [`<Error>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Error)

Creates the link called `path` pointing to `target`. No arguments other than a
possible exception are given to the completion callback.

See the POSIX [`symlink(2)`](http://man7.org/linux/man-pages/man2/symlink.2.html) documentation for more details.

The `type` argument is only available on Windows and ignored on other platforms.
It can be set to `'dir'`, `'file'`, or `'junction'`. If the `type` argument is
`null`, Node.js will autodetect `target` type and use `'file'` or `'dir'`.
If the `target` does not exist, `'file'` will be used. Windows junction points
require the destination path to be absolute. When using `'junction'`, the
`target` argument will automatically be normalized to absolute path. Junction
points on NTFS volumes can only point to directories.

Relative targets are relative to the link's parent directory.

```js
import { symlink } from 'node:fs';

symlink('./mew', './mewtwo', callback);
```

The above example creates a symbolic link `mewtwo` which points to `mew` in the
same directory:

```bash
$ tree .
.
├── mew
└── mewtwo -> ./mew
```

### `fs.truncate(path[, len], callback)`

Added in: v0.8.6

**History**

| Version | Changes |
| --- | --- |
| v18.0.0 | Passing an invalid callback to the `callback` argument now throws `ERR_INVALID_ARG_TYPE` instead of `ERR_INVALID_CALLBACK`. |
| v16.0.0 | The error returned may be an `AggregateError` if more than one error is returned. |
| v10.0.0 | The `callback` parameter is no longer optional. Not passing it will throw a `TypeError` at runtime. |
| v7.0.0 | The `callback` parameter is no longer optional. Not passing it will emit a deprecation warning with id DEP0013. |

- `path` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) | [`<Buffer>`](07-buffer-part1.md#class-buffer) | [`<URL>`](59-url.md#the-whatwg-url-api)
- `len` [`<integer>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) **Default:** `0`
- `callback` [`<Function>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Function)
  - `err` [`<Error>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Error) | [`<AggregateError>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/AggregateError)

Truncates the file. No arguments other than a possible exception are
given to the completion callback. A file descriptor can also be passed as the
first argument. In this case, `fs.ftruncate()` is called.

*ES module version:*

```js
import { truncate } from 'node:fs';
// Assuming that 'path/file.txt' is a regular file.
truncate('path/file.txt', (err) => {
  if (err) throw err;
  console.log('path/file.txt was truncated');
});
```

*CommonJS version:*

```js
const { truncate } = require('node:fs');
// Assuming that 'path/file.txt' is a regular file.
truncate('path/file.txt', (err) => {
  if (err) throw err;
  console.log('path/file.txt was truncated');
});
```

Passing a file descriptor is deprecated and may result in an error being thrown
in the future.

See the POSIX [`truncate(2)`](http://man7.org/linux/man-pages/man2/truncate.2.html) documentation for more details.

### `fs.unlink(path, callback)`

Added in: v0.0.2

**History**

| Version | Changes |
| --- | --- |
| v18.0.0 | Passing an invalid callback to the `callback` argument now throws `ERR_INVALID_ARG_TYPE` instead of `ERR_INVALID_CALLBACK`. |
| v10.0.0 | The `callback` parameter is no longer optional. Not passing it will throw a `TypeError` at runtime. |
| v7.6.0 | The `path` parameter can be a WHATWG `URL` object using `file:` protocol. |
| v7.0.0 | The `callback` parameter is no longer optional. Not passing it will emit a deprecation warning with id DEP0013. |

- `path` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) | [`<Buffer>`](07-buffer-part1.md#class-buffer) | [`<URL>`](59-url.md#the-whatwg-url-api)
- `callback` [`<Function>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Function)
  - `err` [`<Error>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Error)

Asynchronously removes a file or symbolic link. No arguments other than a
possible exception are given to the completion callback.

```js
import { unlink } from 'node:fs';
// Assuming that 'path/file.txt' is a regular file.
unlink('path/file.txt', (err) => {
  if (err) throw err;
  console.log('path/file.txt was deleted');
});
```

`fs.unlink()` will not work on a directory, empty or otherwise. To remove a
directory, use [`fs.rmdir()`](#fsrmdirpath-options-callback).

See the POSIX [`unlink(2)`](http://man7.org/linux/man-pages/man2/unlink.2.html) documentation for more details.

### `fs.unwatchFile(filename[, listener])`

Added in: v0.1.31

- `filename` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) | [`<Buffer>`](07-buffer-part1.md#class-buffer) | [`<URL>`](59-url.md#the-whatwg-url-api)
- `listener` [`<Function>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Function) Optional, a listener previously attached using `fs.watchFile()`

Stop watching for changes on `filename`. If `listener` is specified, only that
particular listener is removed. Otherwise, *all* listeners are removed,
effectively stopping watching of `filename`.

Calling `fs.unwatchFile()` with a filename that is not being watched is a
no-op, not an error.

Using [`fs.watch()`](#fswatchfilename-options-listener) is more efficient than `fs.watchFile()` and
`fs.unwatchFile()`. `fs.watch()` should be used instead of `fs.watchFile()`
and `fs.unwatchFile()` when possible.

### `fs.utimes(path, atime, mtime, callback)`

Added in: v0.4.2

**History**

| Version | Changes |
| --- | --- |
| v18.0.0 | Passing an invalid callback to the `callback` argument now throws `ERR_INVALID_ARG_TYPE` instead of `ERR_INVALID_CALLBACK`. |
| v10.0.0 | The `callback` parameter is no longer optional. Not passing it will throw a `TypeError` at runtime. |
| v8.0.0 | `NaN`, `Infinity`, and `-Infinity` are no longer valid time specifiers. |
| v7.6.0 | The `path` parameter can be a WHATWG `URL` object using `file:` protocol. |
| v7.0.0 | The `callback` parameter is no longer optional. Not passing it will emit a deprecation warning with id DEP0013. |
| v4.1.0 | Numeric strings, `NaN`, and `Infinity` are now allowed time specifiers. |

- `path` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) | [`<Buffer>`](07-buffer-part1.md#class-buffer) | [`<URL>`](59-url.md#the-whatwg-url-api)
- `atime` [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) | [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) | [`<Date>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Date)
- `mtime` [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) | [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) | [`<Date>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Date)
- `callback` [`<Function>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Function)
  - `err` [`<Error>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Error)

Change the file system timestamps of the object referenced by `path`.

The `atime` and `mtime` arguments follow these rules:

- Values can be either numbers representing Unix epoch time in seconds,
  `Date`s, or a numeric string like `'123456789.0'`.
- If the value can not be converted to a number, or is `NaN`, `Infinity`, or
  `-Infinity`, an `Error` will be thrown.

### `fs.watch(filename[, options][, listener])`

Added in: v0.5.10

**History**

| Version | Changes |
| --- | --- |
| v26.1.0 | Added `throwIfNoEntry` option. |
| v19.1.0 | Added recursive support for Linux, AIX and IBMi. |
| v15.9.0, v14.17.0 | Added support for closing the watcher with an AbortSignal. |
| v7.6.0 | The `filename` parameter can be a WHATWG `URL` object using `file:` protocol. |
| v7.0.0 | The passed `options` object will never be modified. |

- `filename` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) | [`<Buffer>`](07-buffer-part1.md#class-buffer) | [`<URL>`](59-url.md#the-whatwg-url-api)
- `options` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) | [`<Object>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Object)
  - `persistent` [`<boolean>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#boolean_type) Indicates whether the process should continue to run
    as long as files are being watched. **Default:** `true`.
  - `recursive` [`<boolean>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#boolean_type) Indicates whether all subdirectories should be
    watched, or only the current directory. This applies when a directory is
    specified, and only on supported platforms (See [caveats](#caveats)). **Default:**
    `false`.
  - `encoding` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) Specifies the character encoding to be used for the
    filename passed to the listener. **Default:** `'utf8'`.
  - `signal` [`<AbortSignal>`](26-globals.md#class-abortsignal) allows closing the watcher with an AbortSignal.
  - `throwIfNoEntry` [`<boolean>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#boolean_type) Indicates whether an exception should be thrown when the
    path does not exist. **Default:** `true`.
  - `ignore` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) | [`<RegExp>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/RegExp) | [`<Function>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Function) | [`<Array>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Array) Pattern(s) to ignore. Strings are
    glob patterns (using [`minimatch`](https://github.com/isaacs/minimatch)), RegExp patterns are tested against
    the filename, and functions receive the filename and return `true` to
    ignore. **Default:** `undefined`.
- `listener` [`<Function>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Function) | [`<undefined>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#undefined_type) **Default:** `undefined`
  - `eventType` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type)
  - `filename` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) | [`<Buffer>`](07-buffer-part1.md#class-buffer) | [`<null>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#null_type)
- Returns: [`<fs.FSWatcher>`](24-fs-part1.md#fsfswatcher)

Watch for changes on `filename`, where `filename` is either a file or a
directory.

The second argument is optional. If `options` is provided as a string, it
specifies the `encoding`. Otherwise `options` should be passed as an object.

The listener callback gets two arguments `(eventType, filename)`. `eventType`
is either `'rename'` or `'change'`, and `filename` is the name of the file
which triggered the event.

On most platforms, `'rename'` is emitted whenever a filename appears or
disappears in the directory.

The listener callback is attached to the `'change'` event fired by
[`<fs.FSWatcher>`](24-fs-part1.md#fsfswatcher), but it is not the same thing as the `'change'` value of
`eventType`.

If a `signal` is passed, aborting the corresponding AbortController will close
the returned [`<fs.FSWatcher>`](24-fs-part1.md#fsfswatcher).

#### Caveats

The `fs.watch` API is not 100% consistent across platforms, and is
unavailable in some situations.

On Windows, no events will be emitted if the watched directory is moved or
renamed. An `EPERM` error is reported when the watched directory is deleted.

The `fs.watch` API does not provide any protection with respect
to malicious actions on the file system. For example, on Windows it is
implemented by monitoring changes in a directory versus specific files. This
allows substitution of a file and fs reporting changes on the new file
with the same filename.

##### Availability

This feature depends on the underlying operating system providing a way
to be notified of file system changes.

- On Linux systems, this uses [`inotify(7)`](https://man7.org/linux/man-pages/man7/inotify.7.html).
- On BSD systems, this uses [`kqueue(2)`](https://www.freebsd.org/cgi/man.cgi?query=kqueue&sektion=2).
- On macOS, this uses [`kqueue(2)`](https://www.freebsd.org/cgi/man.cgi?query=kqueue&sektion=2) for files and [`FSEvents`](https://developer.apple.com/documentation/coreservices/file_system_events) for
  directories.
- On SunOS systems (including Solaris and SmartOS), this uses [`event ports`](https://illumos.org/man/port_create).
- On Windows systems, this feature depends on [`ReadDirectoryChangesW`](https://docs.microsoft.com/en-us/windows/desktop/api/winbase/nf-winbase-readdirectorychangesw).
- On AIX systems, this feature depends on [`AHAFS`](https://www.ibm.com/docs/en/aix/7.3.0?topic=management-aix-event-infrastructure-aix-aix-clusters-ahafs), which must be enabled.
- On IBM i systems, this feature is not supported.

If the underlying functionality is not available for some reason, then
`fs.watch()` will not be able to function and may throw an exception.
For example, watching files or directories can be unreliable, and in some
cases impossible, on network file systems (NFS, SMB, etc) or host file systems
when using virtualization software such as Vagrant or Docker.

It is still possible to use `fs.watchFile()`, which uses stat polling, but
this method is slower and less reliable.

##### Inodes

On Linux and macOS systems, `fs.watch()` resolves the path to an [inode](https://en.wikipedia.org/wiki/Inode) and
watches the inode. If the watched path is deleted and recreated, it is assigned
a new inode. The watch will emit an event for the delete but will continue
watching the *original* inode. Events for the new inode will not be emitted.
This is expected behavior.

AIX files retain the same inode for the lifetime of a file. Saving and closing a
watched file on AIX will result in two notifications (one for adding new
content, and one for truncation).

[Contents](../README.md) | [Previous: File system, part 3 of 7](24-fs-part3.md) | [Next: File system, part 5 of 7](24-fs-part5.md)
