[Contents](../README.md) | [Previous: File system, part 5 of 7](24-fs-part5.md) | [Next: File system, part 7 of 7](24-fs-part7.md)

Source: [https://nodejs.org/docs/latest/api/fs.html](https://nodejs.org/docs/latest/api/fs.html)

Part 6 of 7 of this section.

# File system (part 6 of 7)

Continues: Synchronous API.

### `fs.readSync(fd, buffer, offset, length[, position])`

Added in: v0.1.21

**History**

| Version | Changes |
| --- | --- |
| v10.10.0 | The `buffer` parameter can now be any `TypedArray` or a `DataView`. |
| v6.0.0 | The `length` parameter can now be `0`. |

- `fd` [`<integer>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type)
- `buffer` [`<Buffer>`](07-buffer-part1.md#class-buffer) | [`<TypedArray>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/TypedArray) | [`<DataView>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/DataView)
- `offset` [`<integer>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type)
- `length` [`<integer>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type)
- `position` [`<integer>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) | [`<bigint>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#bigint_type) | [`<null>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#null_type) **Default:** `null`
- Returns: [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type)

Returns the number of `bytesRead`.

For detailed information, see the documentation of the asynchronous version of
this API: [`fs.read()`](24-fs-part4.md#fsreadfd-buffer-offset-length-position-callback).

### `fs.readSync(fd, buffer[, options])`

Added in: v13.13.0, v12.17.0

**History**

| Version | Changes |
| --- | --- |
| v13.13.0, v12.17.0 | Options object can be passed in to make offset, length, and position optional. |

- `fd` [`<integer>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type)
- `buffer` [`<Buffer>`](07-buffer-part1.md#class-buffer) | [`<TypedArray>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/TypedArray) | [`<DataView>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/DataView)
- `options` [`<Object>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Object)
  - `offset` [`<integer>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) **Default:** `0`
  - `length` [`<integer>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) **Default:** `buffer.byteLength - offset`
  - `position` [`<integer>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) | [`<bigint>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#bigint_type) | [`<null>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#null_type) **Default:** `null`
- Returns: [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type)

Returns the number of `bytesRead`.

Similar to the above `fs.readSync` function, this version takes an optional `options` object.
If no `options` object is specified, it will default with the above values.

For detailed information, see the documentation of the asynchronous version of
this API: [`fs.read()`](24-fs-part4.md#fsreadfd-buffer-offset-length-position-callback).

### `fs.readvSync(fd, buffers[, position])`

Added in: v13.13.0, v12.17.0

- `fd` [`<integer>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type)
- `buffers` [`<ArrayBufferView>`](https://developer.mozilla.org/docs/Web/API/ArrayBufferView)[]
- `position` [`<integer>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) | [`<null>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#null_type) **Default:** `null`
- Returns: [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) The number of bytes read.

For detailed information, see the documentation of the asynchronous version of
this API: [`fs.readv()`](24-fs-part4.md#fsreadvfd-buffers-position-callback).

### `fs.realpathSync(path[, options])`

Added in: v0.1.31

**History**

| Version | Changes |
| --- | --- |
| v8.0.0 | Pipe/Socket resolve support was added. |
| v7.6.0 | The `path` parameter can be a WHATWG `URL` object using `file:` protocol. |
| v6.4.0 | Calling `realpathSync` now works again for various edge cases on Windows. |
| v6.0.0 | The `cache` parameter was removed. |

- `path` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) | [`<Buffer>`](07-buffer-part1.md#class-buffer) | [`<URL>`](59-url.md#the-whatwg-url-api)
- `options` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) | [`<Object>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Object)
  - `encoding` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) **Default:** `'utf8'`
- Returns: [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) | [`<Buffer>`](07-buffer-part1.md#class-buffer)

Returns the resolved pathname.

For detailed information, see the documentation of the asynchronous version of
this API: [`fs.realpath()`](24-fs-part4.md#fsrealpathpath-options-callback).

### `fs.realpathSync.native(path[, options])`

Added in: v9.2.0

- `path` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) | [`<Buffer>`](07-buffer-part1.md#class-buffer) | [`<URL>`](59-url.md#the-whatwg-url-api)
- `options` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) | [`<Object>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Object)
  - `encoding` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) **Default:** `'utf8'`
- Returns: [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) | [`<Buffer>`](07-buffer-part1.md#class-buffer)

Synchronous [`realpath(3)`](http://man7.org/linux/man-pages/man3/realpath.3.html).

Only paths that can be converted to UTF8 strings are supported.

The optional `options` argument can be a string specifying an encoding, or an
object with an `encoding` property specifying the character encoding to use for
the path returned. If the `encoding` is set to `'buffer'`,
the path returned will be passed as a [`<Buffer>`](07-buffer-part1.md#class-buffer) object.

On Linux, when Node.js is linked against musl libc, the procfs file system must
be mounted on `/proc` in order for this function to work. Glibc does not have
this restriction.

### `fs.renameSync(oldPath, newPath)`

Added in: v0.1.21

**History**

| Version | Changes |
| --- | --- |
| v7.6.0 | The `oldPath` and `newPath` parameters can be WHATWG `URL` objects using `file:` protocol. Support is currently still *experimental*. |

- `oldPath` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) | [`<Buffer>`](07-buffer-part1.md#class-buffer) | [`<URL>`](59-url.md#the-whatwg-url-api)
- `newPath` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) | [`<Buffer>`](07-buffer-part1.md#class-buffer) | [`<URL>`](59-url.md#the-whatwg-url-api)

Renames the file from `oldPath` to `newPath`. Returns `undefined`.

See the POSIX [`rename(2)`](http://man7.org/linux/man-pages/man2/rename.2.html) documentation for more details.

### `fs.rmdirSync(path[, options])`

Added in: v0.1.21

**History**

| Version | Changes |
| --- | --- |
| v25.0.0 | Remove `recursive` option. |
| v16.0.0 | Using `fs.rmdirSync(path, { recursive: true })` on a `path` that is a file is no longer permitted and results in an `ENOENT` error on Windows and an `ENOTDIR` error on POSIX. |
| v16.0.0 | Using `fs.rmdirSync(path, { recursive: true })` on a `path` that does not exist is no longer permitted and results in a `ENOENT` error. |
| v16.0.0 | The `recursive` option is deprecated, using it triggers a deprecation warning. |
| v14.14.0 | The `recursive` option is deprecated, use `fs.rmSync` instead. |
| v13.3.0, v12.16.0 | The `maxBusyTries` option is renamed to `maxRetries`, and its default is 0. The `emfileWait` option has been removed, and `EMFILE` errors use the same retry logic as other errors. The `retryDelay` option is now supported. `ENFILE` errors are now retried. |
| v12.10.0 | The `recursive`, `maxBusyTries`, and `emfileWait` options are now supported. |
| v7.6.0 | The `path` parameters can be a WHATWG `URL` object using `file:` protocol. |

- `path` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) | [`<Buffer>`](07-buffer-part1.md#class-buffer) | [`<URL>`](59-url.md#the-whatwg-url-api)
- `options` [`<Object>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Object) There are currently no options exposed. There used to
  be options for `recursive`, `maxBusyTries`, and `emfileWait` but they were
  deprecated and removed. The `options` argument is still accepted for
  backwards compatibility but it is not used.

Synchronous [`rmdir(2)`](http://man7.org/linux/man-pages/man2/rmdir.2.html). Returns `undefined`.

Using `fs.rmdirSync()` on a file (not a directory) results in an `ENOENT` error
on Windows and an `ENOTDIR` error on POSIX.

To get a behavior similar to the `rm -rf` Unix command, use [`fs.rmSync()`](#fsrmsyncpath-options)
with options `{ recursive: true, force: true }`.

### `fs.rmSync(path[, options])`

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
  - `recursive` [`<boolean>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#boolean_type) If `true`, perform a recursive directory removal. In
    recursive mode operations are retried on failure. **Default:** `false`.
  - `retryDelay` [`<integer>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) The amount of time in milliseconds to wait between
    retries. This option is ignored if the `recursive` option is not `true`.
    **Default:** `100`.

Synchronously removes files and directories (modeled on the standard POSIX `rm`
utility). Returns `undefined`.

### `fs.statSync(path[, options])`

Added in: v0.1.21

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

Retrieves the [`<fs.Stats>`](24-fs-part6.md#class-fsstats) for the path.

### `fs.statfsSync(path[, options])`

Added in: v19.6.0, v18.15.0

- `path` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) | [`<Buffer>`](07-buffer-part1.md#class-buffer) | [`<URL>`](59-url.md#the-whatwg-url-api)
- `options` [`<Object>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Object)
  - `bigint` [`<boolean>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#boolean_type) Whether the numeric values in the returned
    [`<fs.StatFs>`](24-fs-part6.md#class-fsstatfs) object should be `bigint`. **Default:** `false`.
- Returns: [`<fs.StatFs>`](24-fs-part6.md#class-fsstatfs)

Synchronous [`statfs(2)`](http://man7.org/linux/man-pages/man2/statfs.2.html). Returns information about the mounted file system which
contains `path`.

In case of an error, the `err.code` will be one of [Common System Errors](22-errors-part1.md#common-system-errors).

### `fs.symlinkSync(target, path[, type])`

Added in: v0.1.31

**History**

| Version | Changes |
| --- | --- |
| v12.0.0 | If the `type` argument is left undefined, Node will autodetect `target` type and automatically select `dir` or `file`. |
| v7.6.0 | The `target` and `path` parameters can be WHATWG `URL` objects using `file:` protocol. Support is currently still *experimental*. |

- `target` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) | [`<Buffer>`](07-buffer-part1.md#class-buffer) | [`<URL>`](59-url.md#the-whatwg-url-api)
- `path` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) | [`<Buffer>`](07-buffer-part1.md#class-buffer) | [`<URL>`](59-url.md#the-whatwg-url-api)
- `type` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) | [`<null>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#null_type) **Default:** `null`
- Returns: `undefined`.

For detailed information, see the documentation of the asynchronous version of
this API: [`fs.symlink()`](24-fs-part4.md#fssymlinktarget-path-type-callback).

### `fs.truncateSync(path[, len])`

Added in: v0.8.6

- `path` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) | [`<Buffer>`](07-buffer-part1.md#class-buffer) | [`<URL>`](59-url.md#the-whatwg-url-api)
- `len` [`<integer>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) **Default:** `0`

Truncates the file. Returns `undefined`. A file descriptor can also be
passed as the first argument. In this case, `fs.ftruncateSync()` is called.

Passing a file descriptor is deprecated and may result in an error being thrown
in the future.

### `fs.unlinkSync(path)`

Added in: v0.1.21

**History**

| Version | Changes |
| --- | --- |
| v7.6.0 | The `path` parameter can be a WHATWG `URL` object using `file:` protocol. |

- `path` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) | [`<Buffer>`](07-buffer-part1.md#class-buffer) | [`<URL>`](59-url.md#the-whatwg-url-api)

Synchronous [`unlink(2)`](http://man7.org/linux/man-pages/man2/unlink.2.html). Returns `undefined`.

### `fs.utimesSync(path, atime, mtime)`

Added in: v0.4.2

**History**

| Version | Changes |
| --- | --- |
| v8.0.0 | `NaN`, `Infinity`, and `-Infinity` are no longer valid time specifiers. |
| v7.6.0 | The `path` parameter can be a WHATWG `URL` object using `file:` protocol. |
| v4.1.0 | Numeric strings, `NaN`, and `Infinity` are now allowed time specifiers. |

- `path` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) | [`<Buffer>`](07-buffer-part1.md#class-buffer) | [`<URL>`](59-url.md#the-whatwg-url-api)
- `atime` [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) | [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) | [`<Date>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Date)
- `mtime` [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) | [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) | [`<Date>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Date)
- Returns: `undefined`.

For detailed information, see the documentation of the asynchronous version of
this API: [`fs.utimes()`](24-fs-part4.md#fsutimespath-atime-mtime-callback).

### `fs.writeFileSync(file, data[, options])`

Added in: v0.1.29

**History**

| Version | Changes |
| --- | --- |
| v21.0.0, v20.10.0 | The `flush` option is now supported. |
| v19.0.0 | Passing to the `data` parameter an object with an own `toString` function is no longer supported. |
| v17.8.0 | Passing to the `data` parameter an object with an own `toString` function is deprecated. |
| v14.12.0 | The `data` parameter will stringify an object with an explicit `toString` function. |
| v14.0.0 | The `data` parameter won't coerce unsupported input to strings anymore. |
| v10.10.0 | The `data` parameter can now be any `TypedArray` or a `DataView`. |
| v7.4.0 | The `data` parameter can now be a `Uint8Array`. |
| v5.0.0 | The `file` parameter can be a file descriptor now. |

- `file` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) | [`<Buffer>`](07-buffer-part1.md#class-buffer) | [`<URL>`](59-url.md#the-whatwg-url-api) | [`<integer>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) filename or file descriptor
- `data` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) | [`<Buffer>`](07-buffer-part1.md#class-buffer) | [`<TypedArray>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/TypedArray) | [`<DataView>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/DataView)
- `options` [`<Object>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Object) | [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type)
  - `encoding` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) | [`<null>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#null_type) **Default:** `'utf8'`
  - `mode` [`<integer>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) **Default:** `0o666`
  - `flag` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) See [support of file system `flags`](24-fs-part7.md#file-system-flags). **Default:** `'w'`.
  - `flush` [`<boolean>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#boolean_type) If all data is successfully written to the file, and `flush` is `true`, `fs.fsyncSync()` is used to flush the data.
- Returns: `undefined`.

The `mode` option only affects the newly created file. See [`fs.open()`](24-fs-part4.md#fsopenpath-flags-mode-callback)
for more details.

For detailed information, see the documentation of the asynchronous version of
this API: [`fs.writeFile()`](24-fs-part5.md#fswritefilefile-data-options-callback).

### `fs.writeSync(fd, buffer, offset[, length[, position]])`

Added in: v0.1.21

**History**

| Version | Changes |
| --- | --- |
| v14.0.0 | The `buffer` parameter won't coerce unsupported input to strings anymore. |
| v10.10.0 | The `buffer` parameter can now be any `TypedArray` or a `DataView`. |
| v7.4.0 | The `buffer` parameter can now be a `Uint8Array`. |
| v7.2.0 | The `offset` and `length` parameters are optional now. |

- `fd` [`<integer>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type)
- `buffer` [`<Buffer>`](07-buffer-part1.md#class-buffer) | [`<TypedArray>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/TypedArray) | [`<DataView>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/DataView)
- `offset` [`<integer>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) **Default:** `0`
- `length` [`<integer>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) **Default:** `buffer.byteLength - offset`
- `position` [`<integer>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) | [`<null>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#null_type) **Default:** `null`
- Returns: [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) The number of bytes written.

For detailed information, see the documentation of the asynchronous version of
this API: [`fs.write(fd, buffer...)`](24-fs-part5.md#fswritefd-buffer-offset-length-position-callback).

### `fs.writeSync(fd, buffer[, options])`

Added in: v18.3.0, v16.17.0

- `fd` [`<integer>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type)
- `buffer` [`<Buffer>`](07-buffer-part1.md#class-buffer) | [`<TypedArray>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/TypedArray) | [`<DataView>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/DataView)
- `options` [`<Object>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Object)
  - `offset` [`<integer>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) **Default:** `0`
  - `length` [`<integer>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) **Default:** `buffer.byteLength - offset`
  - `position` [`<integer>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) | [`<null>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#null_type) **Default:** `null`
- Returns: [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) The number of bytes written.

For detailed information, see the documentation of the asynchronous version of
this API: [`fs.write(fd, buffer...)`](24-fs-part5.md#fswritefd-buffer-offset-length-position-callback).

### `fs.writeSync(fd, string[, position[, encoding]])`

Added in: v0.11.5

**History**

| Version | Changes |
| --- | --- |
| v14.0.0 | The `string` parameter won't coerce unsupported input to strings anymore. |
| v7.2.0 | The `position` parameter is optional now. |

- `fd` [`<integer>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type)
- `string` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type)
- `position` [`<integer>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) | [`<null>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#null_type) **Default:** `null`
- `encoding` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) **Default:** `'utf8'`
- Returns: [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) The number of bytes written.

For detailed information, see the documentation of the asynchronous version of
this API: [`fs.write(fd, string...)`](24-fs-part5.md#fswritefd-string-position-encoding-callback).

### `fs.writevSync(fd, buffers[, position])`

Added in: v12.9.0

- `fd` [`<integer>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type)
- `buffers` [`<ArrayBufferView>`](https://developer.mozilla.org/docs/Web/API/ArrayBufferView)[]
- `position` [`<integer>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) | [`<null>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#null_type) **Default:** `null`
- Returns: [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) The number of bytes written.

For detailed information, see the documentation of the asynchronous version of
this API: [`fs.writev()`](24-fs-part5.md#fswritevfd-buffers-position-callback).

## Common Objects

The common objects are shared by all of the file system API variants
(promise, callback, and synchronous).

### Class: `fs.Dir`

Added in: v12.12.0

A class representing a directory stream.

Created by [`fs.opendir()`](24-fs-part4.md#fsopendirpath-options-callback), [`fs.opendirSync()`](24-fs-part5.md#fsopendirsyncpath-options), or
[`fsPromises.opendir()`](24-fs-part2.md#fspromisesopendirpath-options).

```js
import { opendir } from 'node:fs/promises';

try {
  const dir = await opendir('./');
  for await (const dirent of dir)
    console.log(dirent.name);
} catch (err) {
  console.error(err);
}
```

When using the async iterator, the [`<fs.Dir>`](24-fs-part6.md#class-fsdir) object will be automatically
closed after the iterator exits.

#### `dir.close()`

Added in: v12.12.0

- Returns: [`<Promise>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Promise)

Asynchronously close the directory's underlying resource handle.
Subsequent reads will result in errors.

A promise is returned that will be fulfilled after the resource has been
closed.

#### `dir.close(callback)`

Added in: v12.12.0

**History**

| Version | Changes |
| --- | --- |
| v18.0.0 | Passing an invalid callback to the `callback` argument now throws `ERR_INVALID_ARG_TYPE` instead of `ERR_INVALID_CALLBACK`. |

- `callback` [`<Function>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Function)
  - `err` [`<Error>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Error)

Asynchronously close the directory's underlying resource handle.
Subsequent reads will result in errors.

The `callback` will be called after the resource handle has been closed.

#### `dir.closeSync()`

Added in: v12.12.0

Synchronously close the directory's underlying resource handle.
Subsequent reads will result in errors.

#### `dir.path`

Added in: v12.12.0

- Type: [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type)

The read-only path of this directory as was provided to [`fs.opendir()`](24-fs-part4.md#fsopendirpath-options-callback),
[`fs.opendirSync()`](24-fs-part5.md#fsopendirsyncpath-options), or [`fsPromises.opendir()`](24-fs-part2.md#fspromisesopendirpath-options).

#### `dir.read()`

Added in: v12.12.0

- Returns: [`<Promise>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Promise) Fulfills with a [`<fs.Dirent>`](24-fs-part6.md#class-fsdirent) | [`<null>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#null_type)

Asynchronously read the next directory entry via [`readdir(3)`](http://man7.org/linux/man-pages/man3/readdir.3.html) as an [`<fs.Dirent>`](24-fs-part6.md#class-fsdirent).

A promise is returned that will be fulfilled with an [`<fs.Dirent>`](24-fs-part6.md#class-fsdirent), or `null`
if there are no more directory entries to read.

For directory reads handled by the native file system, directory entries
returned by this function are in no particular order as provided by the
operating system's underlying directory mechanisms.
Entries added or removed while iterating over the directory might not be
included in the iteration results.

#### `dir.read(callback)`

Added in: v12.12.0

- `callback` [`<Function>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Function)
  - `err` [`<Error>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Error)
  - `dirent` [`<fs.Dirent>`](24-fs-part6.md#class-fsdirent) | [`<null>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#null_type)

Asynchronously read the next directory entry via [`readdir(3)`](http://man7.org/linux/man-pages/man3/readdir.3.html) as an [`<fs.Dirent>`](24-fs-part6.md#class-fsdirent).

After the read is completed, the `callback` will be called with an
[`<fs.Dirent>`](24-fs-part6.md#class-fsdirent), or `null` if there are no more directory entries to read.

Directory entries returned by this function are in no particular order as
provided by the operating system's underlying directory mechanisms.
Entries added or removed while iterating over the directory might not be
included in the iteration results.

#### `dir.readSync()`

Added in: v12.12.0

- Returns: [`<fs.Dirent>`](24-fs-part6.md#class-fsdirent) | [`<null>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#null_type)

Synchronously read the next directory entry as an [`<fs.Dirent>`](24-fs-part6.md#class-fsdirent). See the
POSIX [`readdir(3)`](http://man7.org/linux/man-pages/man3/readdir.3.html) documentation for more detail.

If there are no more directory entries to read, `null` will be returned.

Directory entries returned by this function are in no particular order as
provided by the operating system's underlying directory mechanisms.
Entries added or removed while iterating over the directory might not be
included in the iteration results.

#### `dir[Symbol.asyncIterator]()`

Added in: v12.12.0

- Returns: [`<AsyncIterator>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/AsyncIterator) An AsyncIterator of [`<fs.Dirent>`](24-fs-part6.md#class-fsdirent)

Asynchronously iterates over the directory until all entries have
been read. Refer to the POSIX [`readdir(3)`](http://man7.org/linux/man-pages/man3/readdir.3.html) documentation for more detail.

Entries returned by the async iterator are always an [`<fs.Dirent>`](24-fs-part6.md#class-fsdirent).
The `null` case from `dir.read()` is handled internally.

See [`<fs.Dir>`](24-fs-part6.md#class-fsdir) for an example.

Directory entries returned by this iterator are in no particular order as
provided by the operating system's underlying directory mechanisms.
Entries added or removed while iterating over the directory might not be
included in the iteration results.

#### `dir[Symbol.asyncDispose]()`

Added in: v24.1.0, v22.1.0

**History**

| Version | Changes |
| --- | --- |
| v24.2.0 | No longer experimental. |

- Returns: [`<Promise>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Promise)

Calls `dir.close()` if the directory handle is open, and returns a promise that
fulfills when disposal is complete.

This method enables the directory to be used with [`await using`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Statements/await_using), which
will automatically close the directory when the scope exits. For more
information, see the [MDN documentation on `using` statements](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Statements/using).

#### `dir[Symbol.dispose]()`

Added in: v24.1.0, v22.1.0

**History**

| Version | Changes |
| --- | --- |
| v24.2.0 | No longer experimental. |

Calls `dir.closeSync()` if the directory handle is open, and returns
`undefined`.

This method enables the directory to be used with [`using`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Statements/using), which
will automatically close the directory when the scope exits. For more
information, see the [MDN documentation on `using` statements](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Statements/using).

### Class: `fs.Dirent`

Added in: v10.10.0

A representation of a directory entry, which can be a file or a subdirectory
within the directory, as returned by reading from an [`<fs.Dir>`](24-fs-part6.md#class-fsdir). The
directory entry is a combination of the file name and file type pairs.

Additionally, when [`fs.readdir()`](24-fs-part4.md#fsreaddirpath-options-callback) or [`fs.readdirSync()`](24-fs-part5.md#fsreaddirsyncpath-options) is called with
the `withFileTypes` option set to `true`, the resulting array is filled with
[`<fs.Dirent>`](24-fs-part6.md#class-fsdirent) objects, rather than strings or [`<Buffer>`](07-buffer-part1.md#class-buffer)s.

When a directory is read, such as with [`fs.readdir()`](24-fs-part4.md#fsreaddirpath-options-callback) or
[`fs.opendir()`](24-fs-part4.md#fsopendirpath-options-callback), the file type of each entry is the type reported by the
operating system and may depend on the file system; for example, some file
systems may report a type that differs from what [`fs.lstat()`](24-fs-part3.md#fslstatpath-options-callback) returns.
Node.js calls [`fs.lstat()`](24-fs-part3.md#fslstatpath-options-callback) on such an entry only when the reported type
is unknown. Use [`fs.lstat()`](24-fs-part3.md#fslstatpath-options-callback) when an accurate file type is required.

#### `dirent.isBlockDevice()`

Added in: v10.10.0

- Returns: [`<boolean>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#boolean_type)

Returns `true` if the [`<fs.Dirent>`](24-fs-part6.md#class-fsdirent) object describes a block device.

#### `dirent.isCharacterDevice()`

Added in: v10.10.0

- Returns: [`<boolean>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#boolean_type)

Returns `true` if the [`<fs.Dirent>`](24-fs-part6.md#class-fsdirent) object describes a character device.

#### `dirent.isDirectory()`

Added in: v10.10.0

- Returns: [`<boolean>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#boolean_type)

Returns `true` if the [`<fs.Dirent>`](24-fs-part6.md#class-fsdirent) object describes a file system
directory.

#### `dirent.isFIFO()`

Added in: v10.10.0

- Returns: [`<boolean>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#boolean_type)

Returns `true` if the [`<fs.Dirent>`](24-fs-part6.md#class-fsdirent) object describes a first-in-first-out
(FIFO) pipe.

#### `dirent.isFile()`

Added in: v10.10.0

- Returns: [`<boolean>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#boolean_type)

Returns `true` if the [`<fs.Dirent>`](24-fs-part6.md#class-fsdirent) object describes a regular file.

#### `dirent.isSocket()`

Added in: v10.10.0

- Returns: [`<boolean>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#boolean_type)

Returns `true` if the [`<fs.Dirent>`](24-fs-part6.md#class-fsdirent) object describes a socket.

#### `dirent.isSymbolicLink()`

Added in: v10.10.0

- Returns: [`<boolean>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#boolean_type)

Returns `true` if the [`<fs.Dirent>`](24-fs-part6.md#class-fsdirent) object describes a symbolic link.

#### `dirent.name`

Added in: v10.10.0

- Type: [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) | [`<Buffer>`](07-buffer-part1.md#class-buffer)

The file name that this [`<fs.Dirent>`](24-fs-part6.md#class-fsdirent) object refers to. The type of this
value is determined by the `options.encoding` passed to [`fs.readdir()`](24-fs-part4.md#fsreaddirpath-options-callback) or
[`fs.readdirSync()`](24-fs-part5.md#fsreaddirsyncpath-options).

#### `dirent.parentPath`

Added in: v21.4.0, v20.12.0, v18.20.0

**History**

| Version | Changes |
| --- | --- |
| v24.0.0, v22.17.0 | Marking the API stable. |

- Type: [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type)

The path to the parent directory of the file this [`<fs.Dirent>`](24-fs-part6.md#class-fsdirent) object refers to.

### Class: `fs.FSWatcher`

Added in: v0.5.8

- Extends [`<EventEmitter>`](23-events.md#class-eventemitter)

A successful call to [`fs.watch()`](24-fs-part4.md#fswatchfilename-options-listener) method will return a new [`<fs.FSWatcher>`](24-fs-part1.md#fsfswatcher)
object.

All [`<fs.FSWatcher>`](24-fs-part1.md#fsfswatcher) objects emit a `'change'` event whenever a specific watched
file is modified.

#### Event: `'change'`

Added in: v0.5.8

- `eventType` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) The type of change event that has occurred
- `filename` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) | [`<Buffer>`](07-buffer-part1.md#class-buffer) The filename that changed (if relevant/available)

Emitted when something changes in a watched directory or file.
See more details in [`fs.watch()`](24-fs-part4.md#fswatchfilename-options-listener).

The `filename` argument may not be provided depending on operating system
support. If `filename` is provided, it will be provided as a [`<Buffer>`](07-buffer-part1.md#class-buffer) if `fs.watch()` is called with its `encoding` option set to `'buffer'`, otherwise
`filename` will be a UTF-8 string.

```js
import { watch } from 'node:fs';
// Example when handled through fs.watch() listener
watch('./tmp', { encoding: 'buffer' }, (eventType, filename) => {
  if (filename) {
    console.log(filename);
    // Prints: <Buffer ...>
  }
});
```

#### Event: `'close'`

Added in: v10.0.0

Emitted when the watcher stops watching for changes. The closed
[`<fs.FSWatcher>`](24-fs-part1.md#fsfswatcher) object is no longer usable in the event handler.

#### Event: `'error'`

Added in: v0.5.8

- `error` [`<Error>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Error)

Emitted when an error occurs while watching the file. The errored
[`<fs.FSWatcher>`](24-fs-part1.md#fsfswatcher) object is no longer usable in the event handler.

#### `watcher.close()`

Added in: v0.5.8

Stop watching for changes on the given [`<fs.FSWatcher>`](24-fs-part1.md#fsfswatcher). Once stopped, the
[`<fs.FSWatcher>`](24-fs-part1.md#fsfswatcher) object is no longer usable.

#### `watcher.ref()`

Added in: v14.3.0, v12.20.0

- Returns: [`<fs.FSWatcher>`](24-fs-part1.md#fsfswatcher)

When called, requests that the Node.js event loop *not* exit so long as the
[`<fs.FSWatcher>`](24-fs-part1.md#fsfswatcher) is active. Calling `watcher.ref()` multiple times will have
no effect.

By default, all [`<fs.FSWatcher>`](24-fs-part1.md#fsfswatcher) objects are "ref'ed", making it normally
unnecessary to call `watcher.ref()` unless `watcher.unref()` had been
called previously.

#### `watcher.unref()`

Added in: v14.3.0, v12.20.0

- Returns: [`<fs.FSWatcher>`](24-fs-part1.md#fsfswatcher)

When called, the active [`<fs.FSWatcher>`](24-fs-part1.md#fsfswatcher) object will not require the Node.js
event loop to remain active. If there is no other activity keeping the
event loop running, the process may exit before the [`<fs.FSWatcher>`](24-fs-part1.md#fsfswatcher) object's
callback is invoked. Calling `watcher.unref()` multiple times will have
no effect.

### Class: `fs.StatWatcher`

Added in: v14.3.0, v12.20.0

- Extends [`<EventEmitter>`](23-events.md#class-eventemitter)

A successful call to `fs.watchFile()` method will return a new [`<fs.StatWatcher>`](24-fs-part6.md#class-fsstatwatcher)
object.

#### `watcher.ref()`

Added in: v14.3.0, v12.20.0

- Returns: [`<fs.StatWatcher>`](24-fs-part6.md#class-fsstatwatcher)

When called, requests that the Node.js event loop *not* exit so long as the
[`<fs.StatWatcher>`](24-fs-part6.md#class-fsstatwatcher) is active. Calling `watcher.ref()` multiple times will have
no effect.

By default, all [`<fs.StatWatcher>`](24-fs-part6.md#class-fsstatwatcher) objects are "ref'ed", making it normally
unnecessary to call `watcher.ref()` unless `watcher.unref()` had been
called previously.

#### `watcher.unref()`

Added in: v14.3.0, v12.20.0

- Returns: [`<fs.StatWatcher>`](24-fs-part6.md#class-fsstatwatcher)

When called, the active [`<fs.StatWatcher>`](24-fs-part6.md#class-fsstatwatcher) object will not require the Node.js
event loop to remain active. If there is no other activity keeping the
event loop running, the process may exit before the [`<fs.StatWatcher>`](24-fs-part6.md#class-fsstatwatcher) object's
callback is invoked. Calling `watcher.unref()` multiple times will have
no effect.

### Class: `fs.ReadStream`

Added in: v0.1.93

- Extends: [`<stream.Readable>`](51-stream-part1.md#class-streamreadable)

Instances of [`<fs.ReadStream>`](24-fs-part6.md#class-fsreadstream) cannot be constructed directly. They are created and
returned using the [`fs.createReadStream()`](24-fs-part3.md#fscreatereadstreampath-options) function.

#### Event: `'close'`

Added in: v0.1.93

Emitted when the [`<fs.ReadStream>`](24-fs-part6.md#class-fsreadstream)'s underlying file descriptor has been closed.

#### Event: `'open'`

Added in: v0.1.93

- `fd` [`<integer>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) Integer file descriptor used by the [`<fs.ReadStream>`](24-fs-part6.md#class-fsreadstream).

Emitted when the [`<fs.ReadStream>`](24-fs-part6.md#class-fsreadstream)'s file descriptor has been opened.

#### Event: `'ready'`

Added in: v9.11.0

Emitted when the [`<fs.ReadStream>`](24-fs-part6.md#class-fsreadstream) is ready to be used.

Fires immediately after `'open'`.

#### `readStream.bytesRead`

Added in: v6.4.0

- Type: [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type)

The number of bytes that have been read so far.

#### `readStream.path`

Added in: v0.1.93

- Type: [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) | [`<Buffer>`](07-buffer-part1.md#class-buffer)

The path to the file the stream is reading from as specified in the first
argument to `fs.createReadStream()`. If `path` is passed as a string, then
`readStream.path` will be a string. If `path` is passed as a [`<Buffer>`](07-buffer-part1.md#class-buffer), then `readStream.path` will be a [`<Buffer>`](07-buffer-part1.md#class-buffer). If `fd` is specified, then
`readStream.path` will be `undefined`.

#### `readStream.pending`

Added in: v11.2.0, v10.16.0

- Type: [`<boolean>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#boolean_type)

This property is `true` if the underlying file has not been opened yet,
i.e. before the `'ready'` event is emitted.

### Class: `fs.Stats`

Added in: v0.1.21

**History**

| Version | Changes |
| --- | --- |
| v26.2.0 | Added `Temporal.Instant` support. |
| v22.0.0, v20.13.0 | Public constructor is deprecated. |
| v8.1.0 | Added times as numbers. |

A [`<fs.Stats>`](24-fs-part6.md#class-fsstats) object provides information about a file.

Objects returned from [`fs.stat()`](24-fs-part4.md#fsstatpath-options-callback), [`fs.lstat()`](24-fs-part3.md#fslstatpath-options-callback), [`fs.fstat()`](24-fs-part3.md#fsfstatfd-options-callback), and
their synchronous counterparts are of this type.
If `bigint` in the `options` passed to those methods is true, the numeric values
will be `bigint` instead of `number`, and the object will contain additional
nanosecond-precision properties suffixed with `Ns`.
`Stat` objects are not to be created directly using the `new` keyword.

```console
Stats {
  dev: 2114,
  ino: 48064969,
  mode: 33188,
  nlink: 1,
  uid: 85,
  gid: 100,
  rdev: 0,
  size: 527,
  blksize: 4096,
  blocks: 8,
  atimeMs: 1318289051000.1,
  mtimeMs: 1318289051000.1,
  ctimeMs: 1318289051000.1,
  birthtimeMs: 1318289051000.1,

  // Instances of Date
  atime: Mon, 10 Oct 2011 23:24:11 GMT,
  mtime: Mon, 10 Oct 2011 23:24:11 GMT,
  ctime: Mon, 10 Oct 2011 23:24:11 GMT,
  birthtime: Mon, 10 Oct 2011 23:24:11 GMT,

  // Instances of Temporal.Instant
  atimeInstant: 2011-10-10T23:24:11.0001Z,
  mtimeInstant: 2011-10-10T23:24:11.0001Z,
  ctimeInstant: 2011-10-10T23:24:11.0001Z,
  birthtimeInstant: 2011-10-10T23:24:11.0001Z
}
```

`bigint` version:

```console
BigIntStats {
  dev: 2114n,
  ino: 48064969n,
  mode: 33188n,
  nlink: 1n,
  uid: 85n,
  gid: 100n,
  rdev: 0n,
  size: 527n,
  blksize: 4096n,
  blocks: 8n,
  atimeMs: 1318289051000n,
  mtimeMs: 1318289051000n,
  ctimeMs: 1318289051000n,
  birthtimeMs: 1318289051000n,
  atimeNs: 1318289051000000000n,
  mtimeNs: 1318289051000000000n,
  ctimeNs: 1318289051000000000n,
  birthtimeNs: 1318289051000000000n,

  // Instances of Date
  atime: Mon, 10 Oct 2011 23:24:11 GMT,
  mtime: Mon, 10 Oct 2011 23:24:11 GMT,
  ctime: Mon, 10 Oct 2011 23:24:11 GMT,
  birthtime: Mon, 10 Oct 2011 23:24:11 GMT,

  // Instances of Temporal.Instant
  atimeInstant: 2011-10-10T23:24:11Z,
  mtimeInstant: 2011-10-10T23:24:11Z,
  ctimeInstant: 2011-10-10T23:24:11Z,
  birthtimeInstant: 2011-10-10T23:24:11Z
}
```

#### `stats.isBlockDevice()`

Added in: v0.1.10

- Returns: [`<boolean>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#boolean_type)

Returns `true` if the [`<fs.Stats>`](24-fs-part6.md#class-fsstats) object describes a block device.

#### `stats.isCharacterDevice()`

Added in: v0.1.10

- Returns: [`<boolean>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#boolean_type)

Returns `true` if the [`<fs.Stats>`](24-fs-part6.md#class-fsstats) object describes a character device.

#### `stats.isDirectory()`

Added in: v0.1.10

- Returns: [`<boolean>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#boolean_type)

Returns `true` if the [`<fs.Stats>`](24-fs-part6.md#class-fsstats) object describes a file system directory.

If the [`<fs.Stats>`](24-fs-part6.md#class-fsstats) object was obtained from calling [`fs.lstat()`](24-fs-part3.md#fslstatpath-options-callback) on a
symbolic link which resolves to a directory, this method will return `false`.
This is because [`fs.lstat()`](24-fs-part3.md#fslstatpath-options-callback) returns information
about a symbolic link itself and not the path it resolves to.

#### `stats.isFIFO()`

Added in: v0.1.10

- Returns: [`<boolean>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#boolean_type)

Returns `true` if the [`<fs.Stats>`](24-fs-part6.md#class-fsstats) object describes a first-in-first-out (FIFO)
pipe.

#### `stats.isFile()`

Added in: v0.1.10

- Returns: [`<boolean>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#boolean_type)

Returns `true` if the [`<fs.Stats>`](24-fs-part6.md#class-fsstats) object describes a regular file.

#### `stats.isSocket()`

Added in: v0.1.10

- Returns: [`<boolean>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#boolean_type)

Returns `true` if the [`<fs.Stats>`](24-fs-part6.md#class-fsstats) object describes a socket.

#### `stats.isSymbolicLink()`

Added in: v0.1.10

- Returns: [`<boolean>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#boolean_type)

Returns `true` if the [`<fs.Stats>`](24-fs-part6.md#class-fsstats) object describes a symbolic link.

This method is only valid when using [`fs.lstat()`](24-fs-part3.md#fslstatpath-options-callback).

#### `stats.dev`

- Type: [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) | [`<bigint>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#bigint_type)

The numeric identifier of the device containing the file.

#### `stats.ino`

- Type: [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) | [`<bigint>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#bigint_type)

The file system specific "Inode" number for the file.

#### `stats.mode`

- Type: [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) | [`<bigint>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#bigint_type)

A bit-field describing the file type and mode.

#### `stats.nlink`

- Type: [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) | [`<bigint>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#bigint_type)

The number of hard-links that exist for the file.

#### `stats.uid`

- Type: [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) | [`<bigint>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#bigint_type)

The numeric user identifier of the user that owns the file (POSIX).

#### `stats.gid`

- Type: [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) | [`<bigint>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#bigint_type)

The numeric group identifier of the group that owns the file (POSIX).

#### `stats.rdev`

- Type: [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) | [`<bigint>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#bigint_type)

A numeric device identifier if the file represents a device.

#### `stats.size`

- Type: [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) | [`<bigint>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#bigint_type)

The size of the file in bytes.

If the underlying file system does not support getting the size of the file,
this will be `0`.

#### `stats.blksize`

- Type: [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) | [`<bigint>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#bigint_type)

The file system block size for i/o operations.

#### `stats.blocks`

- Type: [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) | [`<bigint>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#bigint_type)

The number of blocks allocated for this file.

#### `stats.atimeMs`

Added in: v8.1.0

- Type: [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) | [`<bigint>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#bigint_type)

The timestamp indicating the last time this file was accessed expressed in
milliseconds since the POSIX Epoch.

#### `stats.mtimeMs`

Added in: v8.1.0

- Type: [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) | [`<bigint>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#bigint_type)

The timestamp indicating the last time this file was modified expressed in
milliseconds since the POSIX Epoch.

#### `stats.ctimeMs`

Added in: v8.1.0

- Type: [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) | [`<bigint>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#bigint_type)

The timestamp indicating the last time the file status was changed expressed
in milliseconds since the POSIX Epoch.

#### `stats.birthtimeMs`

Added in: v8.1.0

- Type: [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) | [`<bigint>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#bigint_type)

The timestamp indicating the creation time of this file expressed in
milliseconds since the POSIX Epoch.

#### `stats.atimeNs`

Added in: v12.10.0

- Type: [`<bigint>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#bigint_type)

Only present when `bigint: true` is passed into the method that generates
the object.
The timestamp indicating the last time this file was accessed expressed in
nanoseconds since the POSIX Epoch.

#### `stats.mtimeNs`

Added in: v12.10.0

- Type: [`<bigint>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#bigint_type)

Only present when `bigint: true` is passed into the method that generates
the object.
The timestamp indicating the last time this file was modified expressed in
nanoseconds since the POSIX Epoch.

#### `stats.ctimeNs`

Added in: v12.10.0

- Type: [`<bigint>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#bigint_type)

Only present when `bigint: true` is passed into the method that generates
the object.
The timestamp indicating the last time the file status was changed expressed
in nanoseconds since the POSIX Epoch.

#### `stats.birthtimeNs`

Added in: v12.10.0

- Type: [`<bigint>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#bigint_type)

Only present when `bigint: true` is passed into the method that generates
the object.
The timestamp indicating the creation time of this file expressed in
nanoseconds since the POSIX Epoch.

#### `stats.atime`

Added in: v0.11.13

- Type: [`<Date>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Date)

The timestamp indicating the last time this file was accessed.

#### `stats.mtime`

Added in: v0.11.13

- Type: [`<Date>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Date)

The timestamp indicating the last time this file was modified.

#### `stats.ctime`

Added in: v0.11.13

- Type: [`<Date>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Date)

The timestamp indicating the last time the file status was changed.

#### `stats.birthtime`

Added in: v0.11.13

- Type: [`<Date>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Date)

The timestamp indicating the creation time of this file.

#### Stat time values

The `atimeMs`, `mtimeMs`, `ctimeMs`, `birthtimeMs` properties are
numeric values that hold the corresponding times in milliseconds. Their
precision is platform specific. When `bigint: true` is passed into the
method that generates the object, the properties will be [bigints](https://tc39.github.io/proposal-bigint),
otherwise they will be [numbers](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Guide/Data_structures#number_type).

The `atimeNs`, `mtimeNs`, `ctimeNs`, `birthtimeNs` properties are
[bigints](https://tc39.github.io/proposal-bigint) that hold the corresponding times in nanoseconds. They are
only present when `bigint: true` is passed into the method that generates
the object. Their precision is platform specific.

`atime`, `mtime`, `ctime`, and `birthtime` are
[`Date`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Date) object alternate representations of the various times. The
`Date` and number values are not connected. Assigning a new number value, or
mutating the `Date` value, will not be reflected in the corresponding alternate
representation.

The times in the stat object have the following semantics:

- `atime` "Access Time": Time when file data last accessed. Changed
  by the [`mknod(2)`](http://man7.org/linux/man-pages/man2/mknod.2.html), [`utimes(2)`](http://man7.org/linux/man-pages/man2/utimes.2.html), and [`read(2)`](http://man7.org/linux/man-pages/man2/read.2.html) system calls.
- `mtime` "Modified Time": Time when file data last modified.
  Changed by the [`mknod(2)`](http://man7.org/linux/man-pages/man2/mknod.2.html), [`utimes(2)`](http://man7.org/linux/man-pages/man2/utimes.2.html), and [`write(2)`](http://man7.org/linux/man-pages/man2/write.2.html) system calls.
- `ctime` "Change Time": Time when file status was last changed
  (inode data modification). Changed by the [`chmod(2)`](http://man7.org/linux/man-pages/man2/chmod.2.html), [`chown(2)`](http://man7.org/linux/man-pages/man2/chown.2.html),
  [`link(2)`](http://man7.org/linux/man-pages/man2/link.2.html), [`mknod(2)`](http://man7.org/linux/man-pages/man2/mknod.2.html), [`rename(2)`](http://man7.org/linux/man-pages/man2/rename.2.html), [`unlink(2)`](http://man7.org/linux/man-pages/man2/unlink.2.html), [`utimes(2)`](http://man7.org/linux/man-pages/man2/utimes.2.html),
  [`read(2)`](http://man7.org/linux/man-pages/man2/read.2.html), and [`write(2)`](http://man7.org/linux/man-pages/man2/write.2.html) system calls.
- `birthtime` "Birth Time": Time of file creation. Set once when the
  file is created. On file systems where birthtime is not available,
  this field may instead hold either the `ctime` or
  `1970-01-01T00:00Z` (ie, Unix epoch timestamp `0`). This value may be greater
  than `atime` or `mtime` in this case. On Darwin and other FreeBSD variants,
  also set if the `atime` is explicitly set to an earlier value than the current
  `birthtime` using the [`utimes(2)`](http://man7.org/linux/man-pages/man2/utimes.2.html) system call.

Prior to Node.js 0.12, the `ctime` held the `birthtime` on Windows systems. As
of 0.12, `ctime` is not "creation time", and on Unix systems, it never was.

### Class: `fs.StatFs`

Added in: v19.6.0, v18.15.0

Provides information about a mounted file system.

Objects returned from [`fs.statfs()`](24-fs-part4.md#fsstatfspath-options-callback) and its synchronous counterpart are of
this type. If `bigint` in the `options` passed to those methods is `true`, the
numeric values will be `bigint` instead of `number`.

```console
StatFs {
  type: 1397114950,
  bsize: 4096,
  frsize: 4096,
  blocks: 121938943,
  bfree: 61058895,
  bavail: 61058895,
  files: 999,
  ffree: 1000000
}
```

`bigint` version:

```console
StatFs {
  type: 1397114950n,
  bsize: 4096n,
  frsize: 4096n,
  blocks: 121938943n,
  bfree: 61058895n,
  bavail: 61058895n,
  files: 999n,
  ffree: 1000000n
}
```

#### `statfs.bavail`

Added in: v19.6.0, v18.15.0

- Type: [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) | [`<bigint>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#bigint_type)

Free blocks available to unprivileged users. Multiply by [`statfs.bsize`](#statfsbsize)
to get the number of available bytes.

*ES module version:*

```js
import { statfs } from 'node:fs/promises';

const stats = await statfs('/');
const availableBytes = stats.bsize * stats.bavail;
console.log(`Available space: ${availableBytes} bytes`);
```

*CommonJS version:*

```js
const { statfs } = require('node:fs/promises');

(async () => {
  const stats = await statfs('/');
  const availableBytes = stats.bsize * stats.bavail;
  console.log(`Available space: ${availableBytes} bytes`);
})();
```

#### `statfs.bfree`

Added in: v19.6.0, v18.15.0

- Type: [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) | [`<bigint>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#bigint_type)

Free blocks in file system. Multiply by [`statfs.bsize`](#statfsbsize) to get the number
of free bytes.

*ES module version:*

```js
import { statfs } from 'node:fs/promises';

const stats = await statfs('/');
const freeBytes = stats.bsize * stats.bfree;
console.log(`Free space: ${freeBytes} bytes`);
```

*CommonJS version:*

```js
const { statfs } = require('node:fs/promises');

(async () => {
  const stats = await statfs('/');
  const freeBytes = stats.bsize * stats.bfree;
  console.log(`Free space: ${freeBytes} bytes`);
})();
```

#### `statfs.blocks`

Added in: v19.6.0, v18.15.0

- Type: [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) | [`<bigint>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#bigint_type)

Total data blocks in file system. Multiply by [`statfs.bsize`](#statfsbsize) to get the
total size in bytes.

*ES module version:*

```js
import { statfs } from 'node:fs/promises';

const stats = await statfs('/');
const totalBytes = stats.bsize * stats.blocks;
console.log(`Total space: ${totalBytes} bytes`);
```

*CommonJS version:*

```js
const { statfs } = require('node:fs/promises');

(async () => {
  const stats = await statfs('/');
  const totalBytes = stats.bsize * stats.blocks;
  console.log(`Total space: ${totalBytes} bytes`);
})();
```

#### `statfs.bsize`

Added in: v19.6.0, v18.15.0

- Type: [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) | [`<bigint>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#bigint_type)

Optimal transfer block size in bytes.

#### `statfs.frsize`

Added in: v26.1.0

- Type: [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) | [`<bigint>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#bigint_type)

Fundamental file system block size.

#### `statfs.ffree`

Added in: v19.6.0, v18.15.0

- Type: [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) | [`<bigint>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#bigint_type)

Free file nodes in file system.

#### `statfs.files`

Added in: v19.6.0, v18.15.0

- Type: [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) | [`<bigint>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#bigint_type)

Total file nodes in file system.

[Contents](../README.md) | [Previous: File system, part 5 of 7](24-fs-part5.md) | [Next: File system, part 7 of 7](24-fs-part7.md)
