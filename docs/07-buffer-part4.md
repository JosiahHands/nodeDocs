[Contents](../README.md) | [Previous: Buffer, part 3 of 4](07-buffer-part3.md) | [Next: C++ addons](08-addons.md)

Source: [https://nodejs.org/docs/latest/api/buffer.html](https://nodejs.org/docs/latest/api/buffer.html)

Part 4 of 4 of this section.

# Buffer (part 4 of 4)

Continues: Buffer.from(), Buffer.alloc(), and Buffer.allocUnsafe().

### The `--zero-fill-buffers` command-line option

Added in: v5.10.0

Node.js can be started using the `--zero-fill-buffers` command-line option to
cause all newly-allocated `Buffer` instances to be zero-filled upon creation by
default. Without the option, buffers created with [`Buffer.allocUnsafe()`](07-buffer-part1.md#static-method-bufferallocunsafesize-alignment) and
[`Buffer.allocUnsafeSlow()`](07-buffer-part1.md#static-method-bufferallocunsafeslowsize-alignment) are not zero-filled. Use of this flag can have a
measurable negative impact on performance. Use the `--zero-fill-buffers` option
only when necessary to enforce that newly allocated `Buffer` instances cannot
contain old data that is potentially sensitive.

```console
$ node --zero-fill-buffers
> Buffer.allocUnsafe(5);
<Buffer 00 00 00 00 00>
```

### What makes `Buffer.allocUnsafe()` and `Buffer.allocUnsafeSlow()` "unsafe"?

When calling [`Buffer.allocUnsafe()`](07-buffer-part1.md#static-method-bufferallocunsafesize-alignment) and [`Buffer.allocUnsafeSlow()`](07-buffer-part1.md#static-method-bufferallocunsafeslowsize-alignment), the
segment of allocated memory is *uninitialized* (it is not zeroed-out). While
this design makes the allocation of memory quite fast, the allocated segment of
memory might contain old data that is potentially sensitive. Using a `Buffer`
created by [`Buffer.allocUnsafe()`](07-buffer-part1.md#static-method-bufferallocunsafesize-alignment) without *completely* overwriting the
memory can allow this old data to be leaked when the `Buffer` memory is read.

While there are clear performance advantages to using
[`Buffer.allocUnsafe()`](07-buffer-part1.md#static-method-bufferallocunsafesize-alignment), extra care *must* be taken in order to avoid
introducing security vulnerabilities into an application.

### Aligned allocations

Some operating system interfaces require the memory they operate on to be
aligned, and on some hardware alignment is merely faster. The most common
example of the former is unbuffered ("direct") file I/O, which on Linux requires
the buffer address, the file offset and the transfer length to all be multiples
of the logical block size of the underlying device:

*ES module version:*

```js
import { open } from 'node:fs/promises';
import { constants } from 'node:fs';
import { Buffer } from 'node:buffer';

const blockSize = 4096;

// The buffer address must be block-aligned for O_DIRECT to accept it.
const buf = Buffer.allocUnsafeSlow(blockSize, blockSize);

const file = await open('/dev/sda', constants.O_RDONLY | constants.O_DIRECT);
try {
  await file.read(buf, 0, blockSize, 0);
} finally {
  await file.close();
}
```

*CommonJS version:*

```js
const fs = require('node:fs');
const { Buffer } = require('node:buffer');

const blockSize = 4096;

// The buffer address must be block-aligned for O_DIRECT to accept it.
const buf = Buffer.allocUnsafeSlow(blockSize, blockSize);

const flags = fs.constants.O_RDONLY | fs.constants.O_DIRECT;
fs.open('/dev/sda', flags, (err, fd) => {
  if (err) throw err;
  fs.read(fd, buf, 0, blockSize, 0, (err) => {
    fs.close(fd, () => {});
    if (err) throw err;
  });
});
```

Alignment can also be worth requesting purely for performance, even when no
interface demands it. Aligning a hot `Buffer` to the cache line size (64 bytes on
most contemporary CPUs) keeps it from straddling one more cache line than it
needs to, so that a small structure is fetched with one cache miss instead of
two, and page-aligned (4096 bytes) allocations similarly help interfaces that map
or pin memory. These are micro-optimizations: measure before reaching for them,
since the extra bytes are not free.

Because the address of a `Buffer`'s memory cannot be chosen directly, extra bytes
have to be allocated or skipped to reach an aligned address.
[`Buffer.allocUnsafeSlow()`](07-buffer-part1.md#static-method-bufferallocunsafeslowsize-alignment) over-allocates `alignment` bytes, or 8 when
`alignment` is smaller than that, and positions the returned `Buffer` at the
first suitably aligned byte within them.
[`Buffer.allocUnsafe()`](07-buffer-part1.md#static-method-bufferallocunsafesize-alignment) instead pads its offset into the shared internal pool,
whose start is always aligned to 64 bytes, and only falls back to an allocation
of its own when `alignment` is larger than that. Either way,
[`buf.byteOffset`](07-buffer-part1.md#bufbyteoffset) is usually not 0 and [`buf.buffer`](07-buffer-part1.md#bufbuffer) is larger than `size`,
so code that reaches past the `Buffer` into its underlying `ArrayBuffer` must
take the offset into account, as it must for pooled `Buffer`s.

The alignment is a property of the returned `Buffer` and is preserved for its
whole lifetime, but it is not inherited by other views: [`buf.subarray`](07-buffer-part2.md#bufsubarraystart-end),
[`buf.slice()`](07-buffer-part2.md#bufslicestart-end) and `structuredClone()` may all produce unaligned `Buffer`s.

Alignment also does not survive being captured in a startup snapshot: memory does
not keep its address across serialization, so a `Buffer` allocated while
[`--build-snapshot`](13-cli.md#--build-snapshot) is in effect is not aligned in the deserialized process.
Allocate inside a [`v8.startupSnapshot.setDeserializeMainFunction()`](61-v8.md#v8startupsnapshotsetdeserializemainfunctioncallback-data) callback,
or after startup, if the alignment has to hold at run time.

[Contents](../README.md) | [Previous: Buffer, part 3 of 4](07-buffer-part3.md) | [Next: C++ addons](08-addons.md)
