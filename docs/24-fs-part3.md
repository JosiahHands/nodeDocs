[Contents](../README.md) | [Previous: File system, part 2 of 7](24-fs-part2.md) | [Next: File system, part 4 of 7](24-fs-part4.md)

Source: [https://nodejs.org/docs/latest/api/fs.html](https://nodejs.org/docs/latest/api/fs.html)

Part 3 of 7 of this section.

# File system (part 3 of 7)

### `fs.appendFile(path, data[, options], callback)`

Added in: v0.6.7

**History**

| Version | Changes |
| --- | --- |
| v21.1.0, v20.10.0 | The `flush` option is now supported. |
| v18.0.0 | Passing an invalid callback to the `callback` argument now throws `ERR_INVALID_ARG_TYPE` instead of `ERR_INVALID_CALLBACK`. |
| v10.0.0 | The `callback` parameter is no longer optional. Not passing it will throw a `TypeError` at runtime. |
| v7.0.0 | The `callback` parameter is no longer optional. Not passing it will emit a deprecation warning with id DEP0013. |
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
- `callback` [`<Function>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Function)
  - `err` [`<Error>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Error)

Asynchronously append data to a file, creating the file if it does not yet
exist. `data` can be a string or a [`<Buffer>`](07-buffer-part1.md#class-buffer).

The `mode` option only affects the newly created file. See [`fs.open()`](24-fs-part4.md#fsopenpath-flags-mode-callback)
for more details.

```js
import { appendFile } from 'node:fs';

appendFile('message.txt', 'data to append', (err) => {
  if (err) throw err;
  console.log('The "data to append" was appended to file!');
});
```

If `options` is a string, then it specifies the encoding:

```js
import { appendFile } from 'node:fs';

appendFile('message.txt', 'data to append', 'utf8', callback);
```

The `path` may be specified as a numeric file descriptor that has been opened
for appending (using `fs.open()` or `fs.openSync()`). The file descriptor will
not be closed automatically.

```js
import { open, close, appendFile } from 'node:fs';

function closeFd(fd) {
  close(fd, (err) => {
    if (err) throw err;
  });
}

open('message.txt', 'a', (err, fd) => {
  if (err) throw err;

  try {
    appendFile(fd, 'data to append', 'utf8', (err) => {
      closeFd(fd);
      if (err) throw err;
    });
  } catch (err) {
    closeFd(fd);
    throw err;
  }
});
```

### `fs.chmod(path, mode, callback)`

Added in: v0.1.30

**History**

| Version | Changes |
| --- | --- |
| v18.0.0 | Passing an invalid callback to the `callback` argument now throws `ERR_INVALID_ARG_TYPE` instead of `ERR_INVALID_CALLBACK`. |
| v10.0.0 | The `callback` parameter is no longer optional. Not passing it will throw a `TypeError` at runtime. |
| v7.6.0 | The `path` parameter can be a WHATWG `URL` object using `file:` protocol. |
| v7.0.0 | The `callback` parameter is no longer optional. Not passing it will emit a deprecation warning with id DEP0013. |

- `path` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) | [`<Buffer>`](07-buffer-part1.md#class-buffer) | [`<URL>`](59-url.md#the-whatwg-url-api)
- `mode` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) | [`<integer>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type)
- `callback` [`<Function>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Function)
  - `err` [`<Error>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Error)

Asynchronously changes the permissions of a file. No arguments other than a
possible exception are given to the completion callback.

See the POSIX [`chmod(2)`](http://man7.org/linux/man-pages/man2/chmod.2.html) documentation for more detail.

```js
import { chmod } from 'node:fs';

chmod('my_file.txt', 0o775, (err) => {
  if (err) throw err;
  console.log('The permissions for file "my_file.txt" have been changed!');
});
```

#### File modes

The `mode` argument used in both the `fs.chmod()` and `fs.chmodSync()`
methods is a numeric bitmask created using a logical OR of the following
constants:

| Constant | Octal | Description |
| --- | --- | --- |
| `fs.constants.S_IRUSR` | `0o400` | read by owner |
| `fs.constants.S_IWUSR` | `0o200` | write by owner |
| `fs.constants.S_IXUSR` | `0o100` | execute/search by owner |
| `fs.constants.S_IRGRP` | `0o40` | read by group |
| `fs.constants.S_IWGRP` | `0o20` | write by group |
| `fs.constants.S_IXGRP` | `0o10` | execute/search by group |
| `fs.constants.S_IROTH` | `0o4` | read by others |
| `fs.constants.S_IWOTH` | `0o2` | write by others |
| `fs.constants.S_IXOTH` | `0o1` | execute/search by others |

An easier method of constructing the `mode` is to use a sequence of three
octal digits (e.g. `765`). The left-most digit (`7` in the example), specifies
the permissions for the file owner. The middle digit (`6` in the example),
specifies permissions for the group. The right-most digit (`5` in the example),
specifies the permissions for others.

| Number | Description |
| --- | --- |
| `7` | read, write, and execute |
| `6` | read and write |
| `5` | read and execute |
| `4` | read only |
| `3` | write and execute |
| `2` | write only |
| `1` | execute only |
| `0` | no permission |

For example, the octal value `0o765` means:

- The owner may read, write, and execute the file.
- The group may read and write the file.
- Others may read and execute the file.

When using raw numbers where file modes are expected, any value larger than
`0o777` may result in platform-specific behaviors that are not supported to work
consistently. Therefore constants like `S_ISVTX`, `S_ISGID`, or `S_ISUID` are
not exposed in `fs.constants`.

Caveats: on Windows only the write permission can be changed, and the
distinction among the permissions of group, owner, or others is not
implemented.

### `fs.chown(path, uid, gid, callback)`

Added in: v0.1.97

**History**

| Version | Changes |
| --- | --- |
| v18.0.0 | Passing an invalid callback to the `callback` argument now throws `ERR_INVALID_ARG_TYPE` instead of `ERR_INVALID_CALLBACK`. |
| v10.0.0 | The `callback` parameter is no longer optional. Not passing it will throw a `TypeError` at runtime. |
| v7.6.0 | The `path` parameter can be a WHATWG `URL` object using `file:` protocol. |
| v7.0.0 | The `callback` parameter is no longer optional. Not passing it will emit a deprecation warning with id DEP0013. |

- `path` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) | [`<Buffer>`](07-buffer-part1.md#class-buffer) | [`<URL>`](59-url.md#the-whatwg-url-api)
- `uid` [`<integer>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type)
- `gid` [`<integer>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type)
- `callback` [`<Function>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Function)
  - `err` [`<Error>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Error)

Asynchronously changes owner and group of a file. No arguments other than a
possible exception are given to the completion callback.

See the POSIX [`chown(2)`](http://man7.org/linux/man-pages/man2/chown.2.html) documentation for more detail.

### `fs.close(fd[, callback])`

Added in: v0.0.2

**History**

| Version | Changes |
| --- | --- |
| v18.0.0 | Passing an invalid callback to the `callback` argument now throws `ERR_INVALID_ARG_TYPE` instead of `ERR_INVALID_CALLBACK`. |
| v15.9.0, v14.17.0 | A default callback is now used if one is not provided. |
| v10.0.0 | The `callback` parameter is no longer optional. Not passing it will throw a `TypeError` at runtime. |
| v7.0.0 | The `callback` parameter is no longer optional. Not passing it will emit a deprecation warning with id DEP0013. |

- `fd` [`<integer>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type)
- `callback` [`<Function>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Function)
  - `err` [`<Error>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Error)

Closes the file descriptor. No arguments other than a possible exception are
given to the completion callback.

Calling `fs.close()` on any file descriptor (`fd`) that is currently in use
through any other `fs` operation may lead to undefined behavior.

See the POSIX [`close(2)`](http://man7.org/linux/man-pages/man2/close.2.html) documentation for more detail.

### `fs.copyFile(src, dest[, mode], callback)`

Added in: v8.5.0

**History**

| Version | Changes |
| --- | --- |
| v18.0.0 | Passing an invalid callback to the `callback` argument now throws `ERR_INVALID_ARG_TYPE` instead of `ERR_INVALID_CALLBACK`. |
| v14.0.0 | Changed `flags` argument to `mode` and imposed stricter type validation. |

- `src` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) | [`<Buffer>`](07-buffer-part1.md#class-buffer) | [`<URL>`](59-url.md#the-whatwg-url-api) source filename to copy
- `dest` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) | [`<Buffer>`](07-buffer-part1.md#class-buffer) | [`<URL>`](59-url.md#the-whatwg-url-api) destination filename of the copy operation
- `mode` [`<integer>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) modifiers for copy operation. **Default:** `0`.
- `callback` [`<Function>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Function)
  - `err` [`<Error>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Error)

Asynchronously copies `src` to `dest`. By default, `dest` is overwritten if it
already exists. No arguments other than a possible exception are given to the
callback function. Node.js makes no guarantees about the atomicity of the copy
operation. If an error occurs after the destination file has been opened for
writing, Node.js will attempt to remove the destination.

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
import { copyFile, constants } from 'node:fs';

function callback(err) {
  if (err) throw err;
  console.log('source.txt was copied to destination.txt');
}

// destination.txt will be created or overwritten by default.
copyFile('source.txt', 'destination.txt', callback);

// By using COPYFILE_EXCL, the operation will fail if destination.txt exists.
copyFile('source.txt', 'destination.txt', constants.COPYFILE_EXCL, callback);
```

### `fs.cp(src, dest[, options], callback)`

Added in: v16.7.0

**History**

| Version | Changes |
| --- | --- |
| v22.3.0 | This API is no longer experimental. |
| v20.1.0, v18.17.0 | Accept an additional `mode` option to specify the copy behavior as the `mode` argument of `fs.copyFile()`. |
| v18.0.0 | Passing an invalid callback to the `callback` argument now throws `ERR_INVALID_ARG_TYPE` instead of `ERR_INVALID_CALLBACK`. |
| v17.6.0, v16.15.0 | Accepts an additional `verbatimSymlinks` option to specify whether to perform path resolution for symlinks. |

- `src` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) | [`<URL>`](59-url.md#the-whatwg-url-api) source path to copy.
- `dest` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) | [`<URL>`](59-url.md#the-whatwg-url-api) destination path to copy to.
- `options` [`<Object>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Object)
  - `dereference` [`<boolean>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#boolean_type) dereference symlinks. **Default:** `false`.
  - `errorOnExist` [`<boolean>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#boolean_type) when `force` is `false`, and the destination
    exists, throw an error. **Default:** `false`.
  - `filter` [`<Function>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Function) Function to filter copied files/directories. Return `true` to copy the item, `false` to ignore it. When ignoring a directory,
    all of its contents will be skipped as well. Can also return a `Promise`
    that fulfills with `true` or `false`. **Default:** `undefined`.
    - `src` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) source path to copy.
    - `dest` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) destination path to copy to.
    - Returns: [`<boolean>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#boolean_type) | [`<Promise>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Promise) A value that is coercible to `boolean` or
      a `Promise` that fulfils with such value.
  - `force` [`<boolean>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#boolean_type) overwrite existing file or directory. The copy
    operation will ignore errors if you set this to false and the destination
    exists. Use the `errorOnExist` option to change this behavior.
    **Default:** `true`.
  - `mode` [`<integer>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) modifiers for copy operation. **Default:** `0`.
    See `mode` flag of [`fs.copyFile()`](#fscopyfilesrc-dest-mode-callback).
  - `preserveTimestamps` [`<boolean>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#boolean_type) When `true` timestamps from `src` will
    be preserved. **Default:** `false`.
  - `recursive` [`<boolean>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#boolean_type) copy directories recursively **Default:** `false`
  - `verbatimSymlinks` [`<boolean>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#boolean_type) When `true`, path resolution for symlinks will
    be skipped. **Default:** `false`
- `callback` [`<Function>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Function)
  - `err` [`<Error>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Error)

Asynchronously copies the entire directory structure from `src` to `dest`,
including subdirectories and files.

When copying a directory to another directory, globs are not supported and
behavior is similar to `cp dir1/ dir2/`.

### `fs.createReadStream(path[, options])`

Added in: v0.1.31

**History**

| Version | Changes |
| --- | --- |
| v26.8.0 | Add the `windowsHandle` option. |
| v16.10.0 | The `fs` option does not need `open` method if an `fd` was provided. |
| v16.10.0 | The `fs` option does not need `close` method if `autoClose` is `false`. |
| v15.5.0 | Add support for `AbortSignal`. |
| v15.4.0 | The `fd` option accepts FileHandle arguments. |
| v14.0.0 | Change `emitClose` default to `true`. |
| v13.6.0, v12.17.0 | The `fs` options allow overriding the used `fs` implementation. |
| v12.10.0 | Enable `emitClose` option. |
| v11.0.0 | Impose new restrictions on `start` and `end`, throwing more appropriate errors in cases when we cannot reasonably handle the input values. |
| v7.6.0 | The `path` parameter can be a WHATWG `URL` object using `file:` protocol. |
| v7.0.0 | The passed `options` object will never be modified. |
| v2.3.0 | The passed `options` object can be a string now. |

- `path` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) | [`<Buffer>`](07-buffer-part1.md#class-buffer) | [`<URL>`](59-url.md#the-whatwg-url-api)
- `options` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) | [`<Object>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Object)
  - `flags` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) See [support of file system `flags`](24-fs-part7.md#file-system-flags). **Default:**
    `'r'`.
  - `encoding` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) **Default:** `null`
  - `fd` [`<integer>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) | [`<FileHandle>`](24-fs-part1.md#class-filehandle) **Default:** `null`
  - `mode` [`<integer>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) **Default:** `0o666`
  - `autoClose` [`<boolean>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#boolean_type) **Default:** `true`
  - `emitClose` [`<boolean>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#boolean_type) **Default:** `true`
  - `start` [`<integer>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type)
  - `end` [`<integer>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) **Default:** `Infinity`
  - `highWaterMark` [`<integer>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) **Default:** `64 * 1024`
  - `fs` [`<Object>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Object) | [`<null>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#null_type) **Default:** `null`
  - `signal` [`<AbortSignal>`](26-globals.md#class-abortsignal) | [`<null>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#null_type) **Default:** `null`
  - `windowsHandle` [`<bigint>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#bigint_type) A raw Win32 `HANDLE` value to read from, in place
    of `fd`. Windows only. **Default:** `null`
- Returns: [`<fs.ReadStream>`](24-fs-part6.md#class-fsreadstream)

`options` can include `start` and `end` values to read a range of bytes from
the file instead of the entire file. Both `start` and `end` are inclusive and
start counting at 0, allowed values are in the
[0, [`Number.MAX_SAFE_INTEGER`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Number/MAX_SAFE_INTEGER)] range. If `fd` is specified and `start` is
omitted or `undefined`, `fs.createReadStream()` reads sequentially from the
current file position. The `encoding` can be any one of those accepted by
[`<Buffer>`](07-buffer-part1.md#class-buffer).

If `fd` is specified, `ReadStream` will ignore the `path` argument and will use
the specified file descriptor. This means that no `'open'` event will be
emitted. `fd` should be blocking; non-blocking `fd`s should be passed to
[`<net.Socket>`](38-net.md#class-netsocket).

If `fd` points to a character device that only supports blocking reads
(such as keyboard or sound card), read operations do not finish until data is
available. This can prevent the process from exiting and the stream from
closing naturally.

On Windows, a value passed in `fd` is interpreted as a CRT file descriptor. To
use a raw Win32 `HANDLE` instead, such as an inherited anonymous pipe handle
obtained from another process, pass it as `windowsHandle`. The handle is wrapped
in a file descriptor that the stream owns and closes. The `windowsHandle` option
throws on non-Windows platforms and cannot be combined with the `fs` option.

By default, the stream will emit a `'close'` event after it has been
destroyed. Set the `emitClose` option to `false` to change this behavior.

By providing the `fs` option, it is possible to override the corresponding `fs`
implementations for `open`, `read`, and `close`. When providing the `fs` option,
an override for `read` is required. If no `fd` is provided, an override for
`open` is also required. If `autoClose` is `true`, an override for `close` is
also required.

```js
import { createReadStream } from 'node:fs';

// Create a stream from some character device.
const stream = createReadStream('/dev/input/event0');
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

`mode` sets the file mode (permission and sticky bits), but only if the
file was created.

An example to read the last 10 bytes of a file which is 100 bytes long:

```js
import { createReadStream } from 'node:fs';

createReadStream('sample.txt', { start: 90, end: 99 });
```

If `options` is a string, then it specifies the encoding.

### `fs.createWriteStream(path[, options])`

Added in: v0.1.31

**History**

| Version | Changes |
| --- | --- |
| v26.8.0 | Add the `windowsHandle` option. |
| v22.0.0 | bump default highWaterMark. |
| v21.0.0, v20.10.0 | The `flush` option is now supported. |
| v16.10.0 | The `fs` option does not need `open` method if an `fd` was provided. |
| v16.10.0 | The `fs` option does not need `close` method if `autoClose` is `false`. |
| v15.5.0 | Add support for `AbortSignal`. |
| v15.4.0 | The `fd` option accepts FileHandle arguments. |
| v14.0.0 | Change `emitClose` default to `true`. |
| v13.6.0, v12.17.0 | The `fs` options allow overriding the used `fs` implementation. |
| v12.10.0 | Enable `emitClose` option. |
| v7.6.0 | The `path` parameter can be a WHATWG `URL` object using `file:` protocol. |
| v7.0.0 | The passed `options` object will never be modified. |
| v5.5.0 | The `autoClose` option is supported now. |
| v2.3.0 | The passed `options` object can be a string now. |

- `path` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) | [`<Buffer>`](07-buffer-part1.md#class-buffer) | [`<URL>`](59-url.md#the-whatwg-url-api)
- `options` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) | [`<Object>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Object)
  - `flags` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) See [support of file system `flags`](24-fs-part7.md#file-system-flags). **Default:**
    `'w'`.
  - `encoding` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) **Default:** `'utf8'`
  - `fd` [`<integer>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) | [`<FileHandle>`](24-fs-part1.md#class-filehandle) **Default:** `null`
  - `mode` [`<integer>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) **Default:** `0o666`
  - `autoClose` [`<boolean>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#boolean_type) **Default:** `true`
  - `emitClose` [`<boolean>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#boolean_type) **Default:** `true`
  - `start` [`<integer>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type)
  - `fs` [`<Object>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Object) | [`<null>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#null_type) **Default:** `null`
  - `signal` [`<AbortSignal>`](26-globals.md#class-abortsignal) | [`<null>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#null_type) **Default:** `null`
  - `highWaterMark` [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) **Default:** See
    [`stream.getDefaultHighWaterMark()`](51-stream-part3.md#streamgetdefaulthighwatermarkobjectmode).
  - `flush` [`<boolean>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#boolean_type) If `true`, the underlying file descriptor is flushed
    prior to closing it. **Default:** `false`.
  - `windowsHandle` [`<bigint>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#bigint_type) A raw Win32 `HANDLE` value to write to, in place
    of `fd`. Windows only. **Default:** `null`
- Returns: [`<fs.WriteStream>`](24-fs-part7.md#class-fswritestream)

`options` may also include a `start` option to allow writing data at some
position past the beginning of the file, allowed values are in the
[0, [`Number.MAX_SAFE_INTEGER`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Number/MAX_SAFE_INTEGER)] range. Modifying a file rather than
replacing it may require the `flags` option to be set to `r+` rather than the
default `w`. The `encoding` can be any one of those accepted by [`<Buffer>`](07-buffer-part1.md#class-buffer).

If `autoClose` is set to true (default behavior) on `'error'` or `'finish'`
the file descriptor will be closed automatically. If `autoClose` is false,
then the file descriptor won't be closed, even if there's an error.
It is the application's responsibility to close it and make sure there's no
file descriptor leak.

On Windows, a value passed in `fd` is interpreted as a CRT file descriptor. To
use a raw Win32 `HANDLE` instead, such as an inherited anonymous pipe handle
obtained from another process, pass it as `windowsHandle`. The handle is wrapped
in a file descriptor that the stream owns and closes. The `windowsHandle` option
throws on non-Windows platforms and cannot be combined with the `fs` option.

By default, the stream will emit a `'close'` event after it has been
destroyed. Set the `emitClose` option to `false` to change this behavior.

By providing the `fs` option it is possible to override the corresponding `fs`
implementations for `open`, `write`, `writev`, and `close`. Overriding `write()`
without `writev()` can reduce performance as some optimizations (`_writev()`)
will be disabled. When providing the `fs` option, overrides for at least one of
`write` and `writev` are required. If no `fd` option is supplied, an override
for `open` is also required. If `autoClose` is `true`, an override for `close`
is also required.

Like [`<fs.ReadStream>`](24-fs-part6.md#class-fsreadstream), if `fd` is specified, [`<fs.WriteStream>`](24-fs-part7.md#class-fswritestream) will ignore the `path` argument and will use the specified file descriptor. This means that no
`'open'` event will be emitted. `fd` should be blocking; non-blocking `fd`s
should be passed to [`<net.Socket>`](38-net.md#class-netsocket).

If `options` is a string, then it specifies the encoding.

### `fs.exists(path, callback)`

Added in: v0.0.2Deprecated in: v1.0.0

**History**

| Version | Changes |
| --- | --- |
| v18.0.0 | Passing an invalid callback to the `callback` argument now throws `ERR_INVALID_ARG_TYPE` instead of `ERR_INVALID_CALLBACK`. |
| v7.6.0 | The `path` parameter can be a WHATWG `URL` object using `file:` protocol. |

Stability: 0 - Deprecated: Use [`fs.stat()`](24-fs-part4.md#fsstatpath-options-callback) or [`fs.access()`](24-fs-part2.md#fsaccesspath-mode-callback) instead.

- `path` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) | [`<Buffer>`](07-buffer-part1.md#class-buffer) | [`<URL>`](59-url.md#the-whatwg-url-api)
- `callback` [`<Function>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Function)
  - `exists` [`<boolean>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#boolean_type)

Test whether or not the element at the given `path` exists by checking with the file system.
Then call the `callback` argument with either true or false:

```js
import { exists } from 'node:fs';

exists('/etc/passwd', (e) => {
  console.log(e ? 'it exists' : 'no passwd!');
});
```

**The parameters for this callback are not consistent with other Node.js
callbacks.** Normally, the first parameter to a Node.js callback is an `err`
parameter, optionally followed by other parameters. The `fs.exists()` callback
has only one boolean parameter. This is one reason `fs.access()` is recommended
instead of `fs.exists()`.

If `path` is a symbolic link, it is followed. Thus, if `path` exists but points
to a non-existent element, the callback will receive the value `false`.

Using `fs.exists()` to check for the existence of a file before calling
`fs.open()`, `fs.readFile()`, or `fs.writeFile()` is not recommended. Doing
so introduces a race condition, since other processes may change the file's
state between the two calls. Instead, user code should open/read/write the
file directly and handle the error raised if the file does not exist.

**write (NOT RECOMMENDED)**

```js
import { exists, open, close } from 'node:fs';

exists('myfile', (e) => {
  if (e) {
    console.error('myfile already exists');
  } else {
    open('myfile', 'wx', (err, fd) => {
      if (err) throw err;

      try {
        writeMyData(fd);
      } finally {
        close(fd, (err) => {
          if (err) throw err;
        });
      }
    });
  }
});
```

**write (RECOMMENDED)**

```js
import { open, close } from 'node:fs';
open('myfile', 'wx', (err, fd) => {
  if (err) {
    if (err.code === 'EEXIST') {
      console.error('myfile already exists');
      return;
    }

    throw err;
  }

  try {
    writeMyData(fd);
  } finally {
    close(fd, (err) => {
      if (err) throw err;
    });
  }
});
```

**read (NOT RECOMMENDED)**

```js
import { open, close, exists } from 'node:fs';

exists('myfile', (e) => {
  if (e) {
    open('myfile', 'r', (err, fd) => {
      if (err) throw err;

      try {
        readMyData(fd);
      } finally {
        close(fd, (err) => {
          if (err) throw err;
        });
      }
    });
  } else {
    console.error('myfile does not exist');
  }
});
```

**read (RECOMMENDED)**

```js
import { open, close } from 'node:fs';

open('myfile', 'r', (err, fd) => {
  if (err) {
    if (err.code === 'ENOENT') {
      console.error('myfile does not exist');
      return;
    }

    throw err;
  }

  try {
    readMyData(fd);
  } finally {
    close(fd, (err) => {
      if (err) throw err;
    });
  }
});
```

The "not recommended" examples above check for existence and then use the
file; the "recommended" examples are better because they use the file directly
and handle the error, if any.

In general, check for the existence of a file only if the file won't be
used directly, for example when its existence is a signal from another
process.

### `fs.fchmod(fd, mode, callback)`

Added in: v0.4.7

**History**

| Version | Changes |
| --- | --- |
| v18.0.0 | Passing an invalid callback to the `callback` argument now throws `ERR_INVALID_ARG_TYPE` instead of `ERR_INVALID_CALLBACK`. |
| v10.0.0 | The `callback` parameter is no longer optional. Not passing it will throw a `TypeError` at runtime. |
| v7.0.0 | The `callback` parameter is no longer optional. Not passing it will emit a deprecation warning with id DEP0013. |

- `fd` [`<integer>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type)
- `mode` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) | [`<integer>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type)
- `callback` [`<Function>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Function)
  - `err` [`<Error>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Error)

Sets the permissions on the file. No arguments other than a possible exception
are given to the completion callback.

See the POSIX [`fchmod(2)`](http://man7.org/linux/man-pages/man2/fchmod.2.html) documentation for more detail.

### `fs.fchown(fd, uid, gid, callback)`

Added in: v0.4.7

**History**

| Version | Changes |
| --- | --- |
| v18.0.0 | Passing an invalid callback to the `callback` argument now throws `ERR_INVALID_ARG_TYPE` instead of `ERR_INVALID_CALLBACK`. |
| v10.0.0 | The `callback` parameter is no longer optional. Not passing it will throw a `TypeError` at runtime. |
| v7.0.0 | The `callback` parameter is no longer optional. Not passing it will emit a deprecation warning with id DEP0013. |

- `fd` [`<integer>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type)
- `uid` [`<integer>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type)
- `gid` [`<integer>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type)
- `callback` [`<Function>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Function)
  - `err` [`<Error>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Error)

Sets the owner of the file. No arguments other than a possible exception are
given to the completion callback.

See the POSIX [`fchown(2)`](http://man7.org/linux/man-pages/man2/fchown.2.html) documentation for more detail.

### `fs.fdatasync(fd, callback)`

Added in: v0.1.96

**History**

| Version | Changes |
| --- | --- |
| v18.0.0 | Passing an invalid callback to the `callback` argument now throws `ERR_INVALID_ARG_TYPE` instead of `ERR_INVALID_CALLBACK`. |
| v10.0.0 | The `callback` parameter is no longer optional. Not passing it will throw a `TypeError` at runtime. |
| v7.0.0 | The `callback` parameter is no longer optional. Not passing it will emit a deprecation warning with id DEP0013. |

- `fd` [`<integer>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type)
- `callback` [`<Function>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Function)
  - `err` [`<Error>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Error)

Forces all currently queued I/O operations associated with the file to the
operating system's synchronized I/O completion state. Refer to the POSIX
[`fdatasync(2)`](http://man7.org/linux/man-pages/man2/fdatasync.2.html) documentation for details. No arguments other than a possible
exception are given to the completion callback.

### `fs.fstat(fd[, options], callback)`

Added in: v0.1.95

**History**

| Version | Changes |
| --- | --- |
| v26.8.0 | Accepts an additional `signal` option to allow aborting the operation. |
| v18.0.0 | Passing an invalid callback to the `callback` argument now throws `ERR_INVALID_ARG_TYPE` instead of `ERR_INVALID_CALLBACK`. |
| v10.5.0 | Accepts an additional `options` object to specify whether the numeric values returned should be bigint. |
| v10.0.0 | The `callback` parameter is no longer optional. Not passing it will throw a `TypeError` at runtime. |
| v7.0.0 | The `callback` parameter is no longer optional. Not passing it will emit a deprecation warning with id DEP0013. |

- `fd` [`<integer>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type)
- `options` [`<Object>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Object)
  - `bigint` [`<boolean>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#boolean_type) Whether the numeric values in the returned
    [`<fs.Stats>`](24-fs-part6.md#class-fsstats) object should be `bigint`. **Default:** `false`.
  - `signal` [`<AbortSignal>`](26-globals.md#class-abortsignal) An AbortSignal to cancel the operation. **Default:** `undefined`.
- `callback` [`<Function>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Function)
  - `err` [`<Error>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Error)
  - `stats` [`<fs.Stats>`](24-fs-part6.md#class-fsstats)

Invokes the callback with the [`<fs.Stats>`](24-fs-part6.md#class-fsstats) for the file descriptor.

See the POSIX [`fstat(2)`](http://man7.org/linux/man-pages/man2/fstat.2.html) documentation for more detail.

### `fs.fsync(fd, callback)`

Added in: v0.1.96

**History**

| Version | Changes |
| --- | --- |
| v18.0.0 | Passing an invalid callback to the `callback` argument now throws `ERR_INVALID_ARG_TYPE` instead of `ERR_INVALID_CALLBACK`. |
| v10.0.0 | The `callback` parameter is no longer optional. Not passing it will throw a `TypeError` at runtime. |
| v7.0.0 | The `callback` parameter is no longer optional. Not passing it will emit a deprecation warning with id DEP0013. |

- `fd` [`<integer>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type)
- `callback` [`<Function>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Function)
  - `err` [`<Error>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Error)

Request that all data for the open file descriptor is flushed to the storage
device. The specific implementation is operating system and device specific.
Refer to the POSIX [`fsync(2)`](http://man7.org/linux/man-pages/man2/fsync.2.html) documentation for more detail. No arguments other
than a possible exception are given to the completion callback.

### `fs.ftruncate(fd[, len], callback)`

Added in: v0.8.6

**History**

| Version | Changes |
| --- | --- |
| v18.0.0 | Passing an invalid callback to the `callback` argument now throws `ERR_INVALID_ARG_TYPE` instead of `ERR_INVALID_CALLBACK`. |
| v10.0.0 | The `callback` parameter is no longer optional. Not passing it will throw a `TypeError` at runtime. |
| v7.0.0 | The `callback` parameter is no longer optional. Not passing it will emit a deprecation warning with id DEP0013. |

- `fd` [`<integer>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type)
- `len` [`<integer>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) **Default:** `0`
- `callback` [`<Function>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Function)
  - `err` [`<Error>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Error)

Truncates the file descriptor. No arguments other than a possible exception are
given to the completion callback.

See the POSIX [`ftruncate(2)`](http://man7.org/linux/man-pages/man2/ftruncate.2.html) documentation for more detail.

If the file referred to by the file descriptor was larger than `len` bytes, only
the first `len` bytes will be retained in the file.

For example, the following program retains only the first four bytes of the
file:

```js
import { open, close, ftruncate } from 'node:fs';

function closeFd(fd) {
  close(fd, (err) => {
    if (err) throw err;
  });
}

open('temp.txt', 'r+', (err, fd) => {
  if (err) throw err;

  try {
    ftruncate(fd, 4, (err) => {
      closeFd(fd);
      if (err) throw err;
    });
  } catch (err) {
    closeFd(fd);
    if (err) throw err;
  }
});
```

If the file previously was shorter than `len` bytes, it is extended, and the
extended part is filled with null bytes (`'\0'`):

If `len` is negative then `0` will be used.

### `fs.futimes(fd, atime, mtime, callback)`

Added in: v0.4.2

**History**

| Version | Changes |
| --- | --- |
| v18.0.0 | Passing an invalid callback to the `callback` argument now throws `ERR_INVALID_ARG_TYPE` instead of `ERR_INVALID_CALLBACK`. |
| v10.0.0 | The `callback` parameter is no longer optional. Not passing it will throw a `TypeError` at runtime. |
| v7.0.0 | The `callback` parameter is no longer optional. Not passing it will emit a deprecation warning with id DEP0013. |
| v4.1.0 | Numeric strings, `NaN`, and `Infinity` are now allowed time specifiers. |

- `fd` [`<integer>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type)
- `atime` [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) | [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) | [`<Date>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Date)
- `mtime` [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) | [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) | [`<Date>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Date)
- `callback` [`<Function>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Function)
  - `err` [`<Error>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Error)

Change the file system timestamps of the object referenced by the supplied file
descriptor. See [`fs.utimes()`](24-fs-part4.md#fsutimespath-atime-mtime-callback).

### `fs.glob(pattern[, options], callback)`

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
- `callback` [`<Function>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Function)

  - `err` [`<Error>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Error)
- Retrieves the files matching the specified pattern.

When `followSymlinks` is enabled, detected symbolic link cycles are not
traversed recursively.

*ES module version:*

```js
import { glob } from 'node:fs';

glob('**/*.js', (err, matches) => {
  if (err) throw err;
  console.log(matches);
});
```

*CommonJS version:*

```js
const { glob } = require('node:fs');

glob('**/*.js', (err, matches) => {
  if (err) throw err;
  console.log(matches);
});
```

### `fs.lchmod(path, mode, callback)`

Added in: v0.5.0Deprecated in: v0.5.0

**History**

| Version | Changes |
| --- | --- |
| v18.0.0 | Passing an invalid callback to the `callback` argument now throws `ERR_INVALID_ARG_TYPE` instead of `ERR_INVALID_CALLBACK`. |
| v16.0.0 | The error returned may be an `AggregateError` if more than one error is returned. |
| v10.0.0 | The `callback` parameter is no longer optional. Not passing it will throw a `TypeError` at runtime. |
| v7.0.0 | The `callback` parameter is no longer optional. Not passing it will emit a deprecation warning with id DEP0013. |

Stability: 0 - Deprecated

- `path` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) | [`<Buffer>`](07-buffer-part1.md#class-buffer) | [`<URL>`](59-url.md#the-whatwg-url-api)
- `mode` [`<integer>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type)
- `callback` [`<Function>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Function)
  - `err` [`<Error>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Error) | [`<AggregateError>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/AggregateError)

Changes the permissions on a symbolic link. No arguments other than a possible
exception are given to the completion callback.

This method is only implemented on macOS.

See the POSIX [`lchmod(2)`](http://man7.org/linux/man-pages/man2/lchmod.2.html) documentation for more detail.

### `fs.lchown(path, uid, gid, callback)`

**History**

| Version | Changes |
| --- | --- |
| v18.0.0 | Passing an invalid callback to the `callback` argument now throws `ERR_INVALID_ARG_TYPE` instead of `ERR_INVALID_CALLBACK`. |
| v10.6.0 | This API is no longer deprecated. |
| v10.0.0 | The `callback` parameter is no longer optional. Not passing it will throw a `TypeError` at runtime. |
| v7.0.0 | The `callback` parameter is no longer optional. Not passing it will emit a deprecation warning with id DEP0013. |
| v0.4.7 | Documentation-only deprecation. |

- `path` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) | [`<Buffer>`](07-buffer-part1.md#class-buffer) | [`<URL>`](59-url.md#the-whatwg-url-api)
- `uid` [`<integer>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type)
- `gid` [`<integer>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type)
- `callback` [`<Function>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Function)
  - `err` [`<Error>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Error)

Set the owner of the symbolic link. No arguments other than a possible
exception are given to the completion callback.

See the POSIX [`lchown(2)`](http://man7.org/linux/man-pages/man2/lchown.2.html) documentation for more detail.

### `fs.lutimes(path, atime, mtime, callback)`

Added in: v14.5.0, v12.19.0

**History**

| Version | Changes |
| --- | --- |
| v18.0.0 | Passing an invalid callback to the `callback` argument now throws `ERR_INVALID_ARG_TYPE` instead of `ERR_INVALID_CALLBACK`. |

- `path` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) | [`<Buffer>`](07-buffer-part1.md#class-buffer) | [`<URL>`](59-url.md#the-whatwg-url-api)
- `atime` [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) | [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) | [`<Date>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Date)
- `mtime` [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) | [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) | [`<Date>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Date)
- `callback` [`<Function>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Function)
  - `err` [`<Error>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Error)

Changes the access and modification times of a file in the same way as
[`fs.utimes()`](24-fs-part4.md#fsutimespath-atime-mtime-callback), with the difference that if the path refers to a symbolic
link, then the link is not dereferenced: instead, the timestamps of the
symbolic link itself are changed.

No arguments other than a possible exception are given to the completion
callback.

### `fs.link(existingPath, newPath, callback)`

Added in: v0.1.31

**History**

| Version | Changes |
| --- | --- |
| v18.0.0 | Passing an invalid callback to the `callback` argument now throws `ERR_INVALID_ARG_TYPE` instead of `ERR_INVALID_CALLBACK`. |
| v10.0.0 | The `callback` parameter is no longer optional. Not passing it will throw a `TypeError` at runtime. |
| v7.6.0 | The `existingPath` and `newPath` parameters can be WHATWG `URL` objects using `file:` protocol. Support is currently still *experimental*. |
| v7.0.0 | The `callback` parameter is no longer optional. Not passing it will emit a deprecation warning with id DEP0013. |

- `existingPath` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) | [`<Buffer>`](07-buffer-part1.md#class-buffer) | [`<URL>`](59-url.md#the-whatwg-url-api)
- `newPath` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) | [`<Buffer>`](07-buffer-part1.md#class-buffer) | [`<URL>`](59-url.md#the-whatwg-url-api)
- `callback` [`<Function>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Function)
  - `err` [`<Error>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Error)

Creates a new link from the `existingPath` to the `newPath`. See the POSIX
[`link(2)`](http://man7.org/linux/man-pages/man2/link.2.html) documentation for more detail. No arguments other than a possible
exception are given to the completion callback.

### `fs.lstat(path[, options], callback)`

Added in: v0.1.30

**History**

| Version | Changes |
| --- | --- |
| v26.8.0 | Accepts an additional `signal` option to allow aborting the operation. |
| v18.0.0 | Passing an invalid callback to the `callback` argument now throws `ERR_INVALID_ARG_TYPE` instead of `ERR_INVALID_CALLBACK`. |
| v10.5.0 | Accepts an additional `options` object to specify whether the numeric values returned should be bigint. |
| v10.0.0 | The `callback` parameter is no longer optional. Not passing it will throw a `TypeError` at runtime. |
| v7.6.0 | The `path` parameter can be a WHATWG `URL` object using `file:` protocol. |
| v7.0.0 | The `callback` parameter is no longer optional. Not passing it will emit a deprecation warning with id DEP0013. |

- `path` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) | [`<Buffer>`](07-buffer-part1.md#class-buffer) | [`<URL>`](59-url.md#the-whatwg-url-api)
- `options` [`<Object>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Object)
  - `bigint` [`<boolean>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#boolean_type) Whether the numeric values in the returned
    [`<fs.Stats>`](24-fs-part6.md#class-fsstats) object should be `bigint`. **Default:** `false`.
  - `signal` [`<AbortSignal>`](26-globals.md#class-abortsignal) An AbortSignal to cancel the operation. **Default:** `undefined`.
- `callback` [`<Function>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Function)
  - `err` [`<Error>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Error)
  - `stats` [`<fs.Stats>`](24-fs-part6.md#class-fsstats)

Retrieves the [`<fs.Stats>`](24-fs-part6.md#class-fsstats) for the symbolic link referred to by the path.
The callback gets two arguments `(err, stats)` where `stats` is a [`<fs.Stats>`](24-fs-part6.md#class-fsstats)
object. `lstat()` is identical to `stat()`, except that if `path` is a symbolic
link, then the link itself is stat-ed, not the file that it refers to.

See the POSIX [`lstat(2)`](http://man7.org/linux/man-pages/man2/lstat.2.html) documentation for more details.

### `fs.mkdir(path[, options], callback)`

Added in: v0.1.8

**History**

| Version | Changes |
| --- | --- |
| v18.0.0 | Passing an invalid callback to the `callback` argument now throws `ERR_INVALID_ARG_TYPE` instead of `ERR_INVALID_CALLBACK`. |
| v13.11.0, v12.17.0 | In `recursive` mode, the callback now receives the first created path as an argument. |
| v10.12.0 | The second argument can now be an `options` object with `recursive` and `mode` properties. |
| v10.0.0 | The `callback` parameter is no longer optional. Not passing it will throw a `TypeError` at runtime. |
| v7.6.0 | The `path` parameter can be a WHATWG `URL` object using `file:` protocol. |
| v7.0.0 | The `callback` parameter is no longer optional. Not passing it will emit a deprecation warning with id DEP0013. |

- `path` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) | [`<Buffer>`](07-buffer-part1.md#class-buffer) | [`<URL>`](59-url.md#the-whatwg-url-api)
- `options` [`<Object>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Object) | [`<integer>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type)
  - `recursive` [`<boolean>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#boolean_type) **Default:** `false`
  - `mode` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) | [`<integer>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) Not supported on Windows. See [File modes](#file-modes)
    for more details. **Default:** `0o777`.
- `callback` [`<Function>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Function)
  - `err` [`<Error>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Error)
  - `path` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) | [`<undefined>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#undefined_type) Present only if a directory is created with `recursive` set to `true`.

Asynchronously creates a directory.

The callback is given a possible exception and, if `recursive` is `true`, the
first directory path created, `(err[, path])`.
`path` can still be `undefined` when `recursive` is `true`, if no directory was
created (for instance, if it was previously created).

The optional `options` argument can be an integer specifying `mode` (permission
and sticky bits), or an object with a `mode` property and a `recursive`
property indicating whether parent directories should be created. Calling
`fs.mkdir()` when `path` is a directory that exists results in an error only
when `recursive` is false. If `recursive` is false and the directory exists,
an `EEXIST` error occurs.

```js
import { mkdir } from 'node:fs';

// Create ./tmp/a/apple, regardless of whether ./tmp and ./tmp/a exist.
mkdir('./tmp/a/apple', { recursive: true }, (err) => {
  if (err) throw err;
});
```

On Windows, using `fs.mkdir()` on the root directory even with recursion will
result in an error:

```js
import { mkdir } from 'node:fs';

mkdir('/', { recursive: true }, (err) => {
  // => [Error: EPERM: operation not permitted, mkdir 'C:\']
});
```

See the POSIX [`mkdir(2)`](http://man7.org/linux/man-pages/man2/mkdir.2.html) documentation for more details.

### `fs.mkdtemp(prefix[, options], callback)`

Added in: v5.10.0

**History**

| Version | Changes |
| --- | --- |
| v20.6.0, v18.19.0 | The `prefix` parameter now accepts buffers and URL. |
| v18.0.0 | Passing an invalid callback to the `callback` argument now throws `ERR_INVALID_ARG_TYPE` instead of `ERR_INVALID_CALLBACK`. |
| v16.5.0, v14.18.0 | The `prefix` parameter now accepts an empty string. |
| v10.0.0 | The `callback` parameter is no longer optional. Not passing it will throw a `TypeError` at runtime. |
| v7.0.0 | The `callback` parameter is no longer optional. Not passing it will emit a deprecation warning with id DEP0013. |
| v6.2.1 | The `callback` parameter is optional now. |

- `prefix` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) | [`<Buffer>`](07-buffer-part1.md#class-buffer) | [`<URL>`](59-url.md#the-whatwg-url-api)
- `options` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) | [`<Object>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Object)
  - `encoding` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) **Default:** `'utf8'`
- `callback` [`<Function>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Function)
  - `err` [`<Error>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Error)
  - `directory` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) | [`<Buffer>`](07-buffer-part1.md#class-buffer)

Creates a unique temporary directory.

Generates six random characters to be appended behind a required
`prefix` to create a unique temporary directory. Due to platform
inconsistencies, avoid trailing `X` characters in `prefix`. Some platforms,
notably the BSDs, can return more than six random characters, and replace
trailing `X` characters in `prefix` with random characters.

The optional `options` argument can be a string specifying an encoding, or an
object with an `encoding` property specifying the character encoding to use.

The created directory path is passed to the callback's second parameter. If
`encoding` is `'buffer'`, then the resulting directory path is passed as a
[`<Buffer>`](07-buffer-part1.md#class-buffer). Otherwise, the path is passed as a [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) using the specified
encoding.

```js
import { mkdtemp } from 'node:fs';
import { join } from 'node:path';
import { tmpdir } from 'node:os';

mkdtemp(join(tmpdir(), 'foo-'), (err, directory) => {
  if (err) throw err;
  console.log(directory);
  // Prints: /tmp/foo-itXde2 or C:\Users\...\AppData\Local\Temp\foo-itXde2
});
```

The `fs.mkdtemp()` method will append the six randomly selected characters
directly to the `prefix` string. For instance, given a directory `/tmp`, if the
intention is to create a temporary directory *within* `/tmp`, the `prefix`
must end with a trailing platform-specific path separator
(`require('node:path').sep`).

```js
import { tmpdir } from 'node:os';
import { mkdtemp } from 'node:fs';

// The parent directory for the new temporary directory
const tmpDir = tmpdir();

// This method is *INCORRECT*:
mkdtemp(tmpDir, (err, directory) => {
  if (err) throw err;
  console.log(directory);
  // Will print something similar to `/tmpabc123`.
  // A new temporary directory is created at the file system root
  // rather than *within* the /tmp directory.
});

// This method is *CORRECT*:
import { sep } from 'node:path';
mkdtemp(`${tmpDir}${sep}`, (err, directory) => {
  if (err) throw err;
  console.log(directory);
  // Will print something similar to `/tmp/abc123`.
  // A new temporary directory is created within
  // the /tmp directory.
});
```

[Contents](../README.md) | [Previous: File system, part 2 of 7](24-fs-part2.md) | [Next: File system, part 4 of 7](24-fs-part4.md)
