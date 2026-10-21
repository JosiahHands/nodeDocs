[Contents](../README.md) | [Previous: File system, part 6 of 7](24-fs-part6.md) | [Next: FFI](25-ffi.md)

Source: [https://nodejs.org/docs/latest/api/fs.html](https://nodejs.org/docs/latest/api/fs.html)

Part 7 of 7 of this section.

# File system (part 7 of 7)

#### `statfs.type`

Added in: v19.6.0, v18.15.0

- Type: [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) | [`<bigint>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#bigint_type)

Type of file system. A platform-specific numeric identifier for the type of
file system. This value corresponds to the `f_type` field returned by
`statfs(2)` on POSIX systems (for example, `0xEF53` for ext4 on Linux). Its
meaning is OS-dependent and is not guaranteed to be consistent across
platforms.

### Class: `fs.Utf8Stream`

Added in: v24.6.0

Stability: 1 - Experimental

An optimized UTF-8 stream writer that allows for flushing all the internal
buffering on demand. It handles `EAGAIN` errors correctly, allowing for
customization, for example, by dropping content if the disk is busy.

#### Event: `'close'`

The `'close'` event is emitted when the stream is fully closed.

#### Event: `'drain'`

The `'drain'` event is emitted when the internal buffer has drained sufficiently
to allow continued writing.

#### Event: `'drop'`

The `'drop'` event is emitted when the maximal length is reached and that data
will not be written. The data that was dropped is passed as the first argument
to the event handler.

#### Event: `'error'`

The `'error'` event is emitted when an error occurs.

#### Event: `'finish'`

The `'finish'` event is emitted when the stream has been ended and all data has
been flushed to the underlying file.

#### Event: `'ready'`

The `'ready'` event is emitted when the stream is ready to accept writes.

#### Event: `'write'`

The `'write'` event is emitted when a write operation has completed. The number
of bytes written is passed as the first argument to the event handler.

#### `new fs.Utf8Stream([options])`

- `options` [`<Object>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Object)
  - `append`: [`<boolean>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#boolean_type) Appends writes to dest file instead of truncating it. **Default**: `true`.
  - `contentMode`: [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) Which type of data you can send to the write
    function, supported values are `'utf8'` or `'buffer'`. **Default**:
    `'utf8'`.
  - `dest`: [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) A path to a file to be written to (mode controlled by the
    append option).
  - `fd`: [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) A file descriptor, something that is returned by `fs.open()`
    or `fs.openSync()`.
  - `fs`: [`<Object>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Object) An object that has the same API as the `fs` module, useful
    for mocking, testing, or customizing the behavior of the stream.
  - `fsync`: [`<boolean>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#boolean_type) Perform a `fs.fsyncSync()` every time a write is
    completed.
  - `maxLength`: [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) The maximum length of the internal buffer. If a write
    operation would cause the buffer to exceed `maxLength`, the data written is
    dropped and a drop event is emitted with the dropped data
  - `maxWrite`: [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) The maximum number of bytes that can be written; **Default**: `16384`
  - `minLength`: [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) The minimum length of the internal buffer that is
    required to be full before flushing.
  - `mkdir`: [`<boolean>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#boolean_type) Ensure directory for `dest` file exists when true.
    **Default**: `false`.
  - `mode`: [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) | [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) Specify the creating file mode (see `fs.open()`).
  - `periodicFlush`: [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) Calls flush every `periodicFlush` milliseconds.
  - `retryEAGAIN` [`<Function>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Function) A function that will be called when `write()`,
    `writeSync()`, or `flushSync()` encounters an `EAGAIN` or `EBUSY` error.
    If the return value is `true` the operation will be retried, otherwise it
    will bubble the error. The `err` is the error that caused this function to
    be called, `writeBufferLen` is the length of the buffer that was written,
    and `remainingBufferLen` is the length of the remaining buffer that the
    stream did not try to write.
    - `err` [`<any>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#Data_types) An error or `null`.
    - `writeBufferLen` [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type)
    - `remainingBufferLen`: [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type)
  - `sync`: [`<boolean>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#boolean_type) Perform writes synchronously.

#### `utf8Stream.append`

- [`<boolean>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#boolean_type) Whether the stream is appending to the file or truncating it.

#### `utf8Stream.contentMode`

- [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) The type of data that can be written to the stream. Supported
  values are `'utf8'` or `'buffer'`. **Default**: `'utf8'`.

#### `utf8Stream.destroy()`

Close the stream immediately, without flushing the internal buffer.

#### `utf8Stream.end()`

Close the stream gracefully, flushing the internal buffer before closing.

#### `utf8Stream.fd`

- [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) The file descriptor that is being written to.

#### `utf8Stream.file`

- [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) The file that is being written to.

#### `utf8Stream.flush(callback)`

- `callback` [`<Function>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Function)
  - `err` [`<Error>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Error) | [`<null>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#null_type) An error if the flush failed, otherwise `null`.

Writes the current buffer to the file. The callback is invoked after pending
writes complete and, unless the `fsync` option is enabled, after the data has
been synchronized with `fs.fsync()`. `fsync` errors indicating that the file
descriptor does not support synchronization (for example, a pipe or a TTY)
are ignored.

#### `utf8Stream.flushSync()`

Flushes the buffered data synchronously. This is a costly operation.

If an asynchronous write is in flight when `flushSync()` is called, only the
data queued after it is written, and it may reach the file before the data
of the in-flight write.

#### `utf8Stream.fsync`

- [`<boolean>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#boolean_type) Whether the stream is performing a `fs.fsyncSync()` after every
  write operation.

#### `utf8Stream.maxLength`

- [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) The maximum length of the internal buffer. If a write
  operation would cause the buffer to exceed `maxLength`, the data written is
  dropped and a drop event is emitted with the dropped data.

#### `utf8Stream.minLength`

- [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) The minimum length of the internal buffer that is required to be
  full before flushing.

#### `utf8Stream.mkdir`

- [`<boolean>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#boolean_type) Whether the stream should ensure that the directory for the `dest` file exists. If `true`, it will create the directory if it does not
  exist. **Default**: `false`.

#### `utf8Stream.mode`

- [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) | [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) The mode of the file that is being written to.

#### `utf8Stream.periodicFlush`

- [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) The number of milliseconds between flushes. If set to `0`, no
  periodic flushes will be performed.

#### `utf8Stream.reopen(file)`

- `file`: [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) | [`<Buffer>`](07-buffer-part1.md#class-buffer) | [`<URL>`](59-url.md#the-whatwg-url-api) A path to a file to be written to (mode
  controlled by the append option).

Reopen the file in place, useful for log rotation.

#### `utf8Stream.sync`

- [`<boolean>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#boolean_type) Whether the stream is writing synchronously or asynchronously.

#### `utf8Stream.write(data)`

- `data` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) | [`<Buffer>`](07-buffer-part1.md#class-buffer) | [`<TypedArray>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/TypedArray) | [`<DataView>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/DataView) The data to write.
- Returns [`<boolean>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#boolean_type)

When the `options.contentMode` is set to `'utf8'` when the stream is created,
the `data` argument must be a string. If the `contentMode` is set to `'buffer'`,
the `data` argument must be a [`<Buffer>`](07-buffer-part1.md#class-buffer), [`<TypedArray>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/TypedArray), or [`<DataView>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/DataView).

#### `utf8Stream.writing`

- [`<boolean>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#boolean_type) Whether the stream is currently writing data to the file.

#### `utf8Stream[Symbol.dispose]()`

Calls `utf8Stream.destroy()`.

This method enables the stream to be used with [`using`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Statements/using), which
will automatically destroy the stream when the scope exits. For more
information, see the [MDN documentation on `using` statements](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Statements/using).

### Class: `fs.WriteStream`

Added in: v0.1.93

- Extends [`<stream.Writable>`](51-stream-part1.md#class-streamwritable)

Instances of [`<fs.WriteStream>`](24-fs-part7.md#class-fswritestream) cannot be constructed directly. They are created and
returned using the [`fs.createWriteStream()`](24-fs-part3.md#fscreatewritestreampath-options) function.

#### Event: `'close'`

Added in: v0.1.93

Emitted when the [`<fs.WriteStream>`](24-fs-part7.md#class-fswritestream)'s underlying file descriptor has been closed.

#### Event: `'open'`

Added in: v0.1.93

- `fd` [`<integer>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) Integer file descriptor used by the [`<fs.WriteStream>`](24-fs-part7.md#class-fswritestream).

Emitted when the [`<fs.WriteStream>`](24-fs-part7.md#class-fswritestream)'s file is opened.

#### Event: `'ready'`

Added in: v9.11.0

Emitted when the [`<fs.WriteStream>`](24-fs-part7.md#class-fswritestream) is ready to be used.

Fires immediately after `'open'`.

#### `writeStream.bytesWritten`

Added in: v0.4.7

The number of bytes written so far. Does not include data that is still queued
for writing.

#### `writeStream.close([callback])`

Added in: v0.9.4

- `callback` [`<Function>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Function)
  - `err` [`<Error>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Error)

Closes `writeStream`. Optionally accepts a
callback that will be executed once the `writeStream`
is closed.

#### `writeStream.path`

Added in: v0.1.93

The path to the file the stream is writing to as specified in the first
argument to [`fs.createWriteStream()`](24-fs-part3.md#fscreatewritestreampath-options). If `path` is passed as a string, then
`writeStream.path` will be a string. If `path` is passed as a [`<Buffer>`](07-buffer-part1.md#class-buffer), then `writeStream.path` will be a [`<Buffer>`](07-buffer-part1.md#class-buffer).

#### `writeStream.pending`

Added in: v11.2.0

- Type: [`<boolean>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#boolean_type)

This property is `true` if the underlying file has not been opened yet,
i.e. before the `'ready'` event is emitted.

### `fs.constants`

- Type: [`<Object>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Object)

Returns an object containing commonly used constants for file system
operations.

#### FS constants

The following constants are exported by `fs.constants` and `fsPromises.constants`.

Not every constant will be available on every operating system;
this is especially important for Windows, where many of the POSIX specific
definitions are not available.
For portable applications it is recommended to check for their presence
before use.

To use more than one constant, use the bitwise OR `|` operator.

Example:

```js
import { open, constants } from 'node:fs';

const {
  O_RDWR,
  O_CREAT,
  O_EXCL,
} = constants;

open('/path/to/my/file', O_RDWR | O_CREAT | O_EXCL, (err, fd) => {
  // ...
});
```

##### File access constants

The following constants are meant for use as the `mode` parameter passed to
[`fsPromises.access()`](24-fs-part1.md#fspromisesaccesspath-mode), [`fs.access()`](24-fs-part2.md#fsaccesspath-mode-callback), and [`fs.accessSync()`](24-fs-part5.md#fsaccesssyncpath-mode).

| Constant | Description |
| --- | --- |
| `F_OK` | Flag indicating that the file is visible to the calling process. This is useful for determining if a file exists, but says nothing about `rwx` permissions. Default if no mode is specified. |
| `R_OK` | Flag indicating that the file can be read by the calling process. |
| `W_OK` | Flag indicating that the file can be written by the calling process. |
| `X_OK` | Flag indicating that the file can be executed by the calling process. This has no effect on Windows (will behave like `fs.constants.F_OK`). |

The definitions are also available on Windows.

##### File copy constants

The following constants are meant for use with [`fs.copyFile()`](24-fs-part3.md#fscopyfilesrc-dest-mode-callback).

| Constant | Description |
| --- | --- |
| `COPYFILE_EXCL` | If present, the copy operation will fail with an error if the destination path already exists. |
| `COPYFILE_FICLONE` | If present, the copy operation will attempt to create a copy-on-write reflink. If the underlying platform does not support copy-on-write, then a fallback copy mechanism is used. |
| `COPYFILE_FICLONE_FORCE` | If present, the copy operation will attempt to create a copy-on-write reflink. If the underlying platform does not support copy-on-write, then the operation will fail with an error. |

The definitions are also available on Windows.

##### File open constants

The following constants are meant for use with `fs.open()`.

| Constant | Description |
| --- | --- |
| `O_RDONLY` | Flag indicating to open a file for read-only access. |
| `O_WRONLY` | Flag indicating to open a file for write-only access. |
| `O_RDWR` | Flag indicating to open a file for read-write access. |
| `O_CREAT` | Flag indicating to create the file if it does not already exist. |
| `O_EXCL` | Flag indicating that opening a file should fail if the `O_CREAT` flag is set and the file already exists. |
| `O_NOCTTY` | Flag indicating that if path identifies a terminal device, opening the path shall not cause that terminal to become the controlling terminal for the process (if the process does not already have one). |
| `O_TRUNC` | Flag indicating that if the file exists and is a regular file, and the file is opened successfully for write access, its length shall be truncated to zero. |
| `O_APPEND` | Flag indicating that data will be appended to the end of the file. |
| `O_DIRECTORY` | Flag indicating that the open should fail if the path is not a directory. |
| `O_NOATIME` | Flag indicating reading accesses to the file system will no longer result in an update to the `atime` information associated with the file. This flag is available on Linux operating systems only. |
| `O_NOFOLLOW` | Flag indicating that the open should fail if the path is a symbolic link. |
| `O_SYNC` | Flag indicating that the file is opened for synchronized I/O with write operations waiting for file integrity. On Windows, this maps to `FILE_FLAG_WRITE_THROUGH`. |
| `O_DSYNC` | Flag indicating that the file is opened for synchronized I/O with write operations waiting for data integrity. On Windows, this maps to `FILE_FLAG_WRITE_THROUGH`. |
| `O_SYMLINK` | Flag indicating to open the symbolic link itself rather than the resource it is pointing to. |
| `O_DIRECT` | When set, an attempt will be made to minimize caching effects of file I/O. On Windows, this maps to `FILE_FLAG_NO_BUFFERING`. |
| `O_NONBLOCK` | Flag indicating to open the file in nonblocking mode when possible. |
| `UV_FS_O_FILEMAP` | When set, a memory file mapping is used to access the file. This flag is available on Windows operating systems only. On other operating systems, this flag is ignored. |
| `UV_FS_O_TEMPORARY` | When set, the file is deleted automatically when the last handle to it is closed. This flag is available on Windows operating systems only. On other operating systems, this flag is ignored. |
| `UV_FS_O_SHORT_LIVED` | Hint that the file is short-lived, so the system avoids flushing it to disk when possible. This flag is available on Windows operating systems only. On other operating systems, this flag is ignored. |
| `UV_FS_O_SEQUENTIAL` | Hint that the file is accessed sequentially from beginning to end, to optimize caching. This flag is available on Windows operating systems only. On other operating systems, this flag is ignored. |
| `UV_FS_O_RANDOM` | Hint that the file is accessed randomly, to optimize caching. This flag is available on Windows operating systems only. On other operating systems, this flag is ignored. |

On Windows, only `O_APPEND`, `O_CREAT`, `O_EXCL`, `O_RDONLY`, `O_RDWR`,
`O_TRUNC`, `O_WRONLY`, `UV_FS_O_FILEMAP`, `UV_FS_O_TEMPORARY`,
`UV_FS_O_SHORT_LIVED`, `UV_FS_O_SEQUENTIAL`, and `UV_FS_O_RANDOM` are
available.

##### File type constants

The following constants are meant for use with the [`<fs.Stats>`](24-fs-part6.md#class-fsstats) object's `mode` property for determining a file's type.

| Constant | Description |
| --- | --- |
| `S_IFMT` | Bit mask used to extract the file type code. |
| `S_IFREG` | File type constant for a regular file. |
| `S_IFDIR` | File type constant for a directory. |
| `S_IFCHR` | File type constant for a character-oriented device file. |
| `S_IFBLK` | File type constant for a block-oriented device file. |
| `S_IFIFO` | File type constant for a FIFO/pipe. |
| `S_IFLNK` | File type constant for a symbolic link. |
| `S_IFSOCK` | File type constant for a socket. |

On Windows, only `S_IFCHR`, `S_IFDIR`, `S_IFLNK`, `S_IFMT`, and `S_IFREG`,
are available.

##### File mode constants

The following constants are meant for use with the [`<fs.Stats>`](24-fs-part6.md#class-fsstats) object's `mode` property for determining the access permissions for a file.

| Constant | Description |
| --- | --- |
| `S_IRWXU` | File mode indicating readable, writable, and executable by owner. |
| `S_IRUSR` | File mode indicating readable by owner. |
| `S_IWUSR` | File mode indicating writable by owner. |
| `S_IXUSR` | File mode indicating executable by owner. |
| `S_IRWXG` | File mode indicating readable, writable, and executable by group. |
| `S_IRGRP` | File mode indicating readable by group. |
| `S_IWGRP` | File mode indicating writable by group. |
| `S_IXGRP` | File mode indicating executable by group. |
| `S_IRWXO` | File mode indicating readable, writable, and executable by others. |
| `S_IROTH` | File mode indicating readable by others. |
| `S_IWOTH` | File mode indicating writable by others. |
| `S_IXOTH` | File mode indicating executable by others. |

On Windows, only `S_IRUSR` and `S_IWUSR` are available.

## Notes

### Ordering of callback and promise-based operations

Because they are executed asynchronously by the underlying thread pool,
there is no guaranteed ordering when using either the callback or
promise-based methods.

For example, the following is prone to error because the `fs.stat()`
operation might complete before the `fs.rename()` operation:

```js
const fs = require('node:fs');

fs.rename('/tmp/hello', '/tmp/world', (err) => {
  if (err) throw err;
  console.log('renamed complete');
});
fs.stat('/tmp/world', (err, stats) => {
  if (err) throw err;
  console.log(`stats: ${JSON.stringify(stats)}`);
});
```

It is important to correctly order the operations by awaiting the results
of one before invoking the other:

*ES module version:*

```js
import { rename, stat } from 'node:fs/promises';

const oldPath = '/tmp/hello';
const newPath = '/tmp/world';

try {
  await rename(oldPath, newPath);
  const stats = await stat(newPath);
  console.log(`stats: ${JSON.stringify(stats)}`);
} catch (error) {
  console.error('there was an error:', error.message);
}
```

*CommonJS version:*

```js
const { rename, stat } = require('node:fs/promises');

(async function(oldPath, newPath) {
  try {
    await rename(oldPath, newPath);
    const stats = await stat(newPath);
    console.log(`stats: ${JSON.stringify(stats)}`);
  } catch (error) {
    console.error('there was an error:', error.message);
  }
})('/tmp/hello', '/tmp/world');
```

Or, when using the callback APIs, move the `fs.stat()` call into the callback
of the `fs.rename()` operation:

*ES module version:*

```js
import { rename, stat } from 'node:fs';

rename('/tmp/hello', '/tmp/world', (err) => {
  if (err) throw err;
  stat('/tmp/world', (err, stats) => {
    if (err) throw err;
    console.log(`stats: ${JSON.stringify(stats)}`);
  });
});
```

*CommonJS version:*

```js
const { rename, stat } = require('node:fs');

rename('/tmp/hello', '/tmp/world', (err) => {
  if (err) throw err;
  stat('/tmp/world', (err, stats) => {
    if (err) throw err;
    console.log(`stats: ${JSON.stringify(stats)}`);
  });
});
```

### File paths

Most `fs` operations accept file paths that may be specified in the form of
a string, a [`<Buffer>`](07-buffer-part1.md#class-buffer), or a [`<URL>`](59-url.md#the-whatwg-url-api) object using the `file:` protocol.

#### String paths

String paths are interpreted as UTF-8 character sequences identifying
the absolute or relative filename. Relative paths will be resolved relative
to the current working directory as determined by calling `process.cwd()`.

Example using an absolute path on POSIX:

```js
import { open } from 'node:fs/promises';

let fd;
try {
  fd = await open('/open/some/file.txt', 'r');
  // Do something with the file
} finally {
  await fd?.close();
}
```

Example using a relative path on POSIX (relative to `process.cwd()`):

```js
import { open } from 'node:fs/promises';

let fd;
try {
  fd = await open('file.txt', 'r');
  // Do something with the file
} finally {
  await fd?.close();
}
```

#### File URL paths

Added in: v7.6.0

For most `node:fs` module functions, the `path` or `filename` argument may be
passed as a [`<URL>`](59-url.md#the-whatwg-url-api) object using the `file:` protocol.

```js
import { readFileSync } from 'node:fs';

readFileSync(new URL('file:///tmp/hello'));
```

`file:` URLs are always absolute paths.

##### Platform-specific considerations

On Windows, `file:` [`<URL>`](59-url.md#the-whatwg-url-api)s with a host name convert to UNC paths, while `file:` [`<URL>`](59-url.md#the-whatwg-url-api)s with drive letters convert to local absolute paths. `file:` [`<URL>`](59-url.md#the-whatwg-url-api)s
with no host name and no drive letter will result in an error:

```js
import { readFileSync } from 'node:fs';
// On Windows :

// - WHATWG file URLs with hostname convert to UNC path
// file://hostname/p/a/t/h/file => \\hostname\p\a\t\h\file
readFileSync(new URL('file://hostname/p/a/t/h/file'));

// - WHATWG file URLs with drive letters convert to absolute path
// file:///C:/tmp/hello => C:\tmp\hello
readFileSync(new URL('file:///C:/tmp/hello'));

// - WHATWG file URLs without hostname must have a drive letters
readFileSync(new URL('file:///notdriveletter/p/a/t/h/file'));
readFileSync(new URL('file:///c/p/a/t/h/file'));
// TypeError [ERR_INVALID_FILE_URL_PATH]: File URL path must be absolute
```

`file:` [`<URL>`](59-url.md#the-whatwg-url-api)s with drive letters must use `:` as a separator just after
the drive letter. Using another separator will result in an error.

On all other platforms, `file:` [`<URL>`](59-url.md#the-whatwg-url-api)s with a host name are unsupported and
will result in an error:

```js
import { readFileSync } from 'node:fs';
// On other platforms:

// - WHATWG file URLs with hostname are unsupported
// file://hostname/p/a/t/h/file => throw!
readFileSync(new URL('file://hostname/p/a/t/h/file'));
// TypeError [ERR_INVALID_FILE_URL_PATH]: must be absolute

// - WHATWG file URLs convert to absolute path
// file:///tmp/hello => /tmp/hello
readFileSync(new URL('file:///tmp/hello'));
```

A `file:` [`<URL>`](59-url.md#the-whatwg-url-api) having encoded slash characters will result in an error on all
platforms:

```js
import { readFileSync } from 'node:fs';

// On Windows
readFileSync(new URL('file:///C:/p/a/t/h/%2F'));
readFileSync(new URL('file:///C:/p/a/t/h/%2f'));
/* TypeError [ERR_INVALID_FILE_URL_PATH]: File URL path must not include encoded
\ or / characters */

// On POSIX
readFileSync(new URL('file:///p/a/t/h/%2F'));
readFileSync(new URL('file:///p/a/t/h/%2f'));
/* TypeError [ERR_INVALID_FILE_URL_PATH]: File URL path must not include encoded
/ characters */
```

On Windows, `file:` [`<URL>`](59-url.md#the-whatwg-url-api)s having encoded backslash will result in an error:

```js
import { readFileSync } from 'node:fs';

// On Windows
readFileSync(new URL('file:///C:/path/%5C'));
readFileSync(new URL('file:///C:/path/%5c'));
/* TypeError [ERR_INVALID_FILE_URL_PATH]: File URL path must not include encoded
\ or / characters */
```

#### Buffer paths

Paths specified using a [`<Buffer>`](07-buffer-part1.md#class-buffer) are useful primarily on certain POSIX
operating systems that treat file paths as opaque byte sequences. On such
systems, it is possible for a single file path to contain sub-sequences that
use multiple character encodings. As with string paths, [`<Buffer>`](07-buffer-part1.md#class-buffer) paths may
be relative or absolute:

Example using an absolute path on POSIX:

```js
import { open } from 'node:fs/promises';
import { Buffer } from 'node:buffer';

let fd;
try {
  fd = await open(Buffer.from('/open/some/file.txt'), 'r');
  // Do something with the file
} finally {
  await fd?.close();
}
```

#### Per-drive working directories on Windows

On Windows, Node.js follows the concept of per-drive working directory. This
behavior can be observed when using a drive path without a backslash. For
example `fs.readdirSync('C:\\')` can potentially return a different result than
`fs.readdirSync('C:')`. For more information, see
[this MSDN page](https://docs.microsoft.com/en-us/windows/desktop/FileIO/naming-a-file#fully-qualified-vs-relative-paths).

### File descriptors

On POSIX systems, for every process, the kernel maintains a table of currently
open files and resources. Each open file is assigned a simple numeric
identifier called a *file descriptor*. At the system-level, all file system
operations use these file descriptors to identify and track each specific
file. Windows systems use a different but conceptually similar mechanism for
tracking resources. To simplify things for users, Node.js abstracts away the
differences between operating systems and assigns all open files a numeric file
descriptor.

The callback-based `fs.open()`, and synchronous `fs.openSync()` methods open a
file and allocate a new file descriptor. Once allocated, the file descriptor may
be used to read data from, write data to, or request information about the file.

Operating systems limit the number of file descriptors that may be open
at any given time so it is critical to close the descriptor when operations
are completed. Failure to do so will result in a memory leak that will
eventually cause an application to crash.

```js
import { open, close, fstat } from 'node:fs';

function closeFd(fd) {
  close(fd, (err) => {
    if (err) throw err;
  });
}

open('/open/some/file.txt', 'r', (err, fd) => {
  if (err) throw err;
  try {
    fstat(fd, (err, stat) => {
      if (err) {
        closeFd(fd);
        throw err;
      }

      // use stat

      closeFd(fd);
    });
  } catch (err) {
    closeFd(fd);
    throw err;
  }
});
```

The promise-based APIs use a [`<FileHandle>`](24-fs-part1.md#class-filehandle) object in place of the numeric
file descriptor. These objects are better managed by the system to ensure
that resources are not leaked. However, it is still required that they are
closed when operations are completed:

```js
import { open } from 'node:fs/promises';

let file;
try {
  file = await open('/open/some/file.txt', 'r');
  const stat = await file.stat();
  // use stat
} finally {
  await file.close();
}
```

### Threadpool usage

All callback and promise-based file system APIs (with the exception of
`fs.FSWatcher()`) use libuv's threadpool. This can have surprising and negative
performance implications for some applications. See the
[`UV_THREADPOOL_SIZE`](13-cli.md#uv_threadpool_sizesize) documentation for more information.

### File system flags

The following flags are available wherever the `flag` option takes a
string.

- `'a'`: Open file for appending.
  The file is created if it does not exist.
- `'ax'`: Like `'a'` but fails if the path exists.
- `'a+'`: Open file for reading and appending.
  The file is created if it does not exist.
- `'ax+'`: Like `'a+'` but fails if the path exists.
- `'as'`: Open file for appending in synchronous mode.
  The file is created if it does not exist.
- `'as+'`: Open file for reading and appending in synchronous mode.
  The file is created if it does not exist.
- `'r'`: Open file for reading.
  An exception occurs if the file does not exist.
- `'rs'`: Open file for reading in synchronous mode.
  An exception occurs if the file does not exist.
- `'r+'`: Open file for reading and writing.
  An exception occurs if the file does not exist.
- `'rs+'`: Open file for reading and writing in synchronous mode. Instructs
  the operating system to bypass the local file system cache.

  This is primarily useful for opening files on NFS mounts as it allows
  skipping the potentially stale local cache. It has a very real impact on
  I/O performance so using this flag is not recommended unless it is needed.

  This doesn't turn `fs.open()` or `fsPromises.open()` into a synchronous
  blocking call. If synchronous operation is desired, something like
  `fs.openSync()` should be used.
- `'w'`: Open file for writing.
  The file is created (if it does not exist) or truncated (if it exists).
- `'wx'`: Like `'w'` but fails if the path exists.
- `'w+'`: Open file for reading and writing.
  The file is created (if it does not exist) or truncated (if it exists).
- `'wx+'`: Like `'w+'` but fails if the path exists.

`flag` can also be a number as documented by [`open(2)`](http://man7.org/linux/man-pages/man2/open.2.html); commonly used constants
are available from `fs.constants`. On Windows, flags are translated to
their equivalent ones where applicable, e.g. `O_WRONLY` to `FILE_GENERIC_WRITE`,
or `O_EXCL|O_CREAT` to `CREATE_NEW`, as accepted by `CreateFileW`.

The exclusive flag `'x'` (`O_EXCL` flag in [`open(2)`](http://man7.org/linux/man-pages/man2/open.2.html)) causes the operation to
return an error if the path already exists. On POSIX, if the path is a symbolic
link, using `O_EXCL` returns an error even if the link is to a path that does
not exist. The exclusive flag might not work with network file systems.

On Linux, positional writes don't work when the file is opened in append mode.
The kernel ignores the position argument and always appends the data to
the end of the file.

Modifying a file rather than replacing it may require the `flag` option to be
set to `'r+'` rather than the default `'w'`.

The behavior of some flags are platform-specific. As such, opening a directory
on macOS and Linux with the `'a+'` flag, as in the example below, will return an
error. In contrast, on Windows and FreeBSD, a file descriptor or a `FileHandle`
will be returned.

```js
// macOS and Linux
fs.open('<directory>', 'a+', (err, fd) => {
  // => [Error: EISDIR: illegal operation on a directory, open <directory>]
});

// Windows and FreeBSD
fs.open('<directory>', 'a+', (err, fd) => {
  // => null, <fd>
});
```

On Windows, opening an existing hidden file using the `'w'` flag (either
through `fs.open()`, `fs.writeFile()`, or `fsPromises.open()`) will fail with
`EPERM`. Existing hidden files can be opened for writing with the `'r+'` flag.

A call to `fs.ftruncate()` or `filehandle.truncate()` can be used to reset
the file contents.

[Contents](../README.md) | [Previous: File system, part 6 of 7](24-fs-part6.md) | [Next: FFI](25-ffi.md)
