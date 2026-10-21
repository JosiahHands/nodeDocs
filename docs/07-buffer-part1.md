[Contents](../README.md) | [Previous: Benchmark runner](06-bench.md) | [Next: Buffer, part 2 of 4](07-buffer-part2.md)

Source: [https://nodejs.org/docs/latest/api/buffer.html](https://nodejs.org/docs/latest/api/buffer.html)

Part 1 of 4 of this section.

# Buffer

[Stability: 2](01-documentation.md#stability-index) - Stable

`Buffer` objects are used to represent a fixed-length sequence of bytes. Many
Node.js APIs support `Buffer`s.

The `Buffer` class is a subclass of JavaScript's [`<Uint8Array>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Uint8Array) class and
extends it with methods that cover additional use cases. Node.js APIs accept
plain [`<Uint8Array>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Uint8Array)s wherever `Buffer`s are supported as well.

While the `Buffer` class is available within the global scope, it is still
recommended to explicitly reference it via an import or require statement.

*ES module version:*

```js
import { Buffer } from 'node:buffer';

// Creates a zero-filled Buffer of length 10.
const buf1 = Buffer.alloc(10);

// Creates a Buffer of length 10,
// filled with bytes which all have the value `1`.
const buf2 = Buffer.alloc(10, 1);

// Creates an uninitialized buffer of length 10.
// This is faster than calling Buffer.alloc() but the returned
// Buffer instance might contain old data that needs to be
// overwritten using fill(), write(), or other functions that fill the Buffer's
// contents.
const buf3 = Buffer.allocUnsafe(10);

// Creates a Buffer containing the bytes [1, 2, 3].
const buf4 = Buffer.from([1, 2, 3]);

// Creates a Buffer containing the bytes [1, 1, 1, 1] – the entries
// are all truncated using `(value & 255)` to fit into the range 0–255.
const buf5 = Buffer.from([257, 257.5, -255, '1']);

// Creates a Buffer containing the UTF-8-encoded bytes for the string 'tést':
// [0x74, 0xc3, 0xa9, 0x73, 0x74] (in hexadecimal notation)
// [116, 195, 169, 115, 116] (in decimal notation)
const buf6 = Buffer.from('tést');

// Creates a Buffer containing the Latin-1 bytes [0x74, 0xe9, 0x73, 0x74].
const buf7 = Buffer.from('tést', 'latin1');
```

*CommonJS version:*

```js
const { Buffer } = require('node:buffer');

// Creates a zero-filled Buffer of length 10.
const buf1 = Buffer.alloc(10);

// Creates a Buffer of length 10,
// filled with bytes which all have the value `1`.
const buf2 = Buffer.alloc(10, 1);

// Creates an uninitialized buffer of length 10.
// This is faster than calling Buffer.alloc() but the returned
// Buffer instance might contain old data that needs to be
// overwritten using fill(), write(), or other functions that fill the Buffer's
// contents.
const buf3 = Buffer.allocUnsafe(10);

// Creates a Buffer containing the bytes [1, 2, 3].
const buf4 = Buffer.from([1, 2, 3]);

// Creates a Buffer containing the bytes [1, 1, 1, 1] – the entries
// are all truncated using `(value & 255)` to fit into the range 0–255.
const buf5 = Buffer.from([257, 257.5, -255, '1']);

// Creates a Buffer containing the UTF-8-encoded bytes for the string 'tést':
// [0x74, 0xc3, 0xa9, 0x73, 0x74] (in hexadecimal notation)
// [116, 195, 169, 115, 116] (in decimal notation)
const buf6 = Buffer.from('tést');

// Creates a Buffer containing the Latin-1 bytes [0x74, 0xe9, 0x73, 0x74].
const buf7 = Buffer.from('tést', 'latin1');
```

## Buffers and character encodings

**History**

| Version | Changes |
| --- | --- |
| v15.7.0, v14.18.0 | Introduced `base64url` encoding. |
| v6.4.0 | Introduced `latin1` as an alias for `binary`. |
| v5.0.0 | Removed the deprecated `raw` and `raws` encodings. |

When converting between `Buffer`s and strings, a character encoding may be
specified. If no character encoding is specified, UTF-8 will be used as the
default.

*ES module version:*

```js
import { Buffer } from 'node:buffer';

const buf = Buffer.from('hello world', 'utf8');

console.log(buf.toString('hex'));
// Prints: 68656c6c6f20776f726c64
console.log(buf.toString('base64'));
// Prints: aGVsbG8gd29ybGQ=

console.log(Buffer.from('fhqwhgads', 'utf8'));
// Prints: <Buffer 66 68 71 77 68 67 61 64 73>
console.log(Buffer.from('fhqwhgads', 'utf16le'));
// Prints: <Buffer 66 00 68 00 71 00 77 00 68 00 67 00 61 00 64 00 73 00>
```

*CommonJS version:*

```js
const { Buffer } = require('node:buffer');

const buf = Buffer.from('hello world', 'utf8');

console.log(buf.toString('hex'));
// Prints: 68656c6c6f20776f726c64
console.log(buf.toString('base64'));
// Prints: aGVsbG8gd29ybGQ=

console.log(Buffer.from('fhqwhgads', 'utf8'));
// Prints: <Buffer 66 68 71 77 68 67 61 64 73>
console.log(Buffer.from('fhqwhgads', 'utf16le'));
// Prints: <Buffer 66 00 68 00 71 00 77 00 68 00 67 00 61 00 64 00 73 00>
```

Node.js buffers accept all case variations of encoding strings that they
receive. For example, UTF-8 can be specified as `'utf8'`, `'UTF8'`, or `'uTf8'`.

The character encodings currently supported by Node.js are the following:

- `'utf8'` (alias: `'utf-8'`): Multi-byte encoded Unicode characters. Many web
  pages and other document formats use [UTF-8](https://en.wikipedia.org/wiki/UTF-8). This is the default character
  encoding. When decoding a `Buffer` into a string that does not exclusively
  contain valid UTF-8 data, the Unicode replacement character `U+FFFD` � will be
  used to represent those errors.
- `'utf16le'` (alias: `'utf-16le'`): Multi-byte encoded Unicode characters.
  Unlike `'utf8'`, each character in the string will be encoded using either 2
  or 4 bytes. Node.js only supports the [little-endian](https://en.wikipedia.org/wiki/Endianness) variant of
  [UTF-16](https://en.wikipedia.org/wiki/UTF-16).
- `'latin1'`: Latin-1 stands for [ISO-8859-1](https://en.wikipedia.org/wiki/ISO-8859-1). This character encoding only
  supports the Unicode characters from `U+0000` to `U+00FF`. Each character is
  encoded using a single byte. Characters that do not fit into that range are
  truncated and will be mapped to characters in that range.

Converting a `Buffer` into a string using one of the above is referred to as
decoding, and converting a string into a `Buffer` is referred to as encoding.

Node.js also supports the following binary-to-text encodings. For
binary-to-text encodings, the naming convention is reversed: Converting a
`Buffer` into a string is typically referred to as encoding, and converting a
string into a `Buffer` as decoding.

- `'base64'`: [Base64](https://en.wikipedia.org/wiki/Base64) encoding. When creating a `Buffer` from a string,
  this encoding will also correctly accept "URL and Filename Safe Alphabet" as
  specified in [RFC 4648, Section 5](https://tools.ietf.org/html/rfc4648#section-5). Whitespace characters such as spaces,
  tabs, and new lines contained within the base64-encoded string are ignored.
- `'base64url'`: [base64url](https://tools.ietf.org/html/rfc4648#section-5) encoding as specified in
  [RFC 4648, Section 5](https://tools.ietf.org/html/rfc4648#section-5). When creating a `Buffer` from a string, this
  encoding will also correctly accept regular base64-encoded strings. When
  encoding a `Buffer` to a string, this encoding will omit padding.
- `'hex'`: Encode each byte as two hexadecimal characters. Data truncation
  may occur when decoding strings that do not exclusively consist of an even
  number of hexadecimal characters. See below for an example.

The following legacy character encodings are also supported:

- `'ascii'`: For 7-bit [ASCII](https://en.wikipedia.org/wiki/ASCII) data only. When encoding a string into a
  `Buffer`, this is equivalent to using `'latin1'`. When decoding a `Buffer`
  into a string, using this encoding will additionally unset the highest bit of
  each byte before decoding as `'latin1'`.
  Generally, there should be no reason to use this encoding, as `'utf8'`
  (or, if the data is known to always be ASCII-only, `'latin1'`) will be a
  better choice when encoding or decoding ASCII-only text. It is only provided
  for legacy compatibility.
- `'binary'`: Alias for `'latin1'`.
  The name of this encoding can be very misleading, as all of the
  encodings listed here convert between strings and binary data. For converting
  between strings and `Buffer`s, typically `'utf8'` is the right choice.
- `'ucs2'`, `'ucs-2'`: Aliases of `'utf16le'`. UCS-2 used to refer to a variant
  of UTF-16 that did not support characters that had code points larger than
  U+FFFF. In Node.js, these code points are always supported.

*ES module version:*

```js
import { Buffer } from 'node:buffer';

Buffer.from('1ag123', 'hex');
// Prints <Buffer 1a>, data truncated when first non-hexadecimal value
// ('g') encountered.

Buffer.from('1a7', 'hex');
// Prints <Buffer 1a>, data truncated when data ends in single digit ('7').

Buffer.from('1634', 'hex');
// Prints <Buffer 16 34>, all data represented.
```

*CommonJS version:*

```js
const { Buffer } = require('node:buffer');

Buffer.from('1ag123', 'hex');
// Prints <Buffer 1a>, data truncated when first non-hexadecimal value
// ('g') encountered.

Buffer.from('1a7', 'hex');
// Prints <Buffer 1a>, data truncated when data ends in single digit ('7').

Buffer.from('1634', 'hex');
// Prints <Buffer 16 34>, all data represented.
```

Modern Web browsers follow the [WHATWG Encoding Standard](https://encoding.spec.whatwg.org/) which aliases
both `'latin1'` and `'ISO-8859-1'` to `'win-1252'`. This means that while doing
something like `http.get()`, if the returned charset is one of those listed in
the WHATWG specification it is possible that the server actually returned
`'win-1252'`-encoded data, and using `'latin1'` encoding may incorrectly decode
the characters.

## Buffers and TypedArrays

**History**

| Version | Changes |
| --- | --- |
| v3.0.0 | The `Buffer` class now inherits from `Uint8Array`. |

`Buffer` instances are also JavaScript [`<Uint8Array>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Uint8Array) and [`<TypedArray>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/TypedArray)
instances. All [`<TypedArray>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/TypedArray) methods and properties are available on `Buffer`s. There are,
however, subtle incompatibilities between the `Buffer` API and the
[`<TypedArray>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/TypedArray) API.

In particular:

- While [`TypedArray.prototype.slice()`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/TypedArray/slice) creates a copy of part of the `TypedArray`,
  [`Buffer.prototype.slice()`](07-buffer-part2.md#bufslicestart-end) creates a view over the existing `Buffer`
  without copying. This behavior can be surprising, and only exists for legacy
  compatibility. [`TypedArray.prototype.subarray()`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/TypedArray/subarray) can be used to achieve
  the behavior of [`Buffer.prototype.slice()`](07-buffer-part2.md#bufslicestart-end) on both `Buffer`s
  and other `TypedArray`s and should be preferred.
- [`buf.toString()`](07-buffer-part3.md#buftostringencoding-start-end) is incompatible with its `TypedArray` equivalent.
- A number of methods, e.g. [`buf.indexOf()`](07-buffer-part2.md#bufindexofvalue-start-end-encoding), support additional arguments.

There are two ways to create new [`<TypedArray>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/TypedArray) instances from a `Buffer`:

- Passing a `Buffer` to a [`<TypedArray>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/TypedArray) constructor will copy the `Buffer`'s
  contents, interpreted as an array of integers, and not as a byte sequence
  of the target type.

*ES module version:*

```js
import { Buffer } from 'node:buffer';

const buf = Buffer.from([1, 2, 3, 4]);
const uint32array = new Uint32Array(buf);

console.log(uint32array);

// Prints: Uint32Array(4) [ 1, 2, 3, 4 ]
```

*CommonJS version:*

```js
const { Buffer } = require('node:buffer');

const buf = Buffer.from([1, 2, 3, 4]);
const uint32array = new Uint32Array(buf);

console.log(uint32array);

// Prints: Uint32Array(4) [ 1, 2, 3, 4 ]
```

- Passing the `Buffer`'s underlying [`<ArrayBuffer>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/ArrayBuffer) will create a
  [`<TypedArray>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/TypedArray) that shares its memory with the `Buffer`.

*ES module version:*

```js
import { Buffer } from 'node:buffer';

const buf = Buffer.from('hello', 'utf16le');
const uint16array = new Uint16Array(
  buf.buffer,
  buf.byteOffset,
  buf.length / Uint16Array.BYTES_PER_ELEMENT);

console.log(uint16array);

// Prints: Uint16Array(5) [ 104, 101, 108, 108, 111 ]
```

*CommonJS version:*

```js
const { Buffer } = require('node:buffer');

const buf = Buffer.from('hello', 'utf16le');
const uint16array = new Uint16Array(
  buf.buffer,
  buf.byteOffset,
  buf.length / Uint16Array.BYTES_PER_ELEMENT);

console.log(uint16array);

// Prints: Uint16Array(5) [ 104, 101, 108, 108, 111 ]
```

It is possible to create a new `Buffer` that shares the same allocated
memory as a [`<TypedArray>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/TypedArray) instance by using the `TypedArray` object's
`.buffer` property in the same way. [`Buffer.from()`](#static-method-bufferfromarraybuffer-byteoffset-length)
behaves like `new Uint8Array()` in this context.

*ES module version:*

```js
import { Buffer } from 'node:buffer';

const arr = new Uint16Array(2);

arr[0] = 5000;
arr[1] = 4000;

// Copies the contents of `arr`.
const buf1 = Buffer.from(arr);

// Shares memory with `arr`.
const buf2 = Buffer.from(arr.buffer);

console.log(buf1);
// Prints: <Buffer 88 a0>
console.log(buf2);
// Prints: <Buffer 88 13 a0 0f>

arr[1] = 6000;

console.log(buf1);
// Prints: <Buffer 88 a0>
console.log(buf2);
// Prints: <Buffer 88 13 70 17>
```

*CommonJS version:*

```js
const { Buffer } = require('node:buffer');

const arr = new Uint16Array(2);

arr[0] = 5000;
arr[1] = 4000;

// Copies the contents of `arr`.
const buf1 = Buffer.from(arr);

// Shares memory with `arr`.
const buf2 = Buffer.from(arr.buffer);

console.log(buf1);
// Prints: <Buffer 88 a0>
console.log(buf2);
// Prints: <Buffer 88 13 a0 0f>

arr[1] = 6000;

console.log(buf1);
// Prints: <Buffer 88 a0>
console.log(buf2);
// Prints: <Buffer 88 13 70 17>
```

When creating a `Buffer` using a [`<TypedArray>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/TypedArray)'s `.buffer`, it is
possible to use only a portion of the underlying [`<ArrayBuffer>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/ArrayBuffer) by passing in `byteOffset` and `length` parameters.

*ES module version:*

```js
import { Buffer } from 'node:buffer';

const arr = new Uint16Array(20);
const buf = Buffer.from(arr.buffer, 0, 16);

console.log(buf.length);
// Prints: 16
```

*CommonJS version:*

```js
const { Buffer } = require('node:buffer');

const arr = new Uint16Array(20);
const buf = Buffer.from(arr.buffer, 0, 16);

console.log(buf.length);
// Prints: 16
```

The `Buffer.from()` and [`TypedArray.from()`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/TypedArray/from) have different signatures and
implementations. Specifically, the [`<TypedArray>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/TypedArray) variants accept a second
argument that is a mapping function that is invoked on every element of the
typed array:

- [`TypedArray.from(source[, mapFn[, thisArg]])`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/TypedArray/from)

The `Buffer.from()` method, however, does not support the use of a mapping
function:

- [`Buffer.from(array)`](#static-method-bufferfromarray)
- [`Buffer.from(buffer)`](#static-method-bufferfrombuffer)
- [`Buffer.from(arrayBuffer[, byteOffset[, length]])`](#static-method-bufferfromarraybuffer-byteoffset-length)
- [`Buffer.from(string[, encoding])`](#static-method-bufferfromstring-encoding)

### Buffer methods are callable with `Uint8Array` instances

All methods on the Buffer prototype are callable with a `Uint8Array` instance.

```js
const { toString, write } = Buffer.prototype;

const uint8array = new Uint8Array(5);

write.call(uint8array, 'hello', 0, 5, 'utf8'); // 5
// <Uint8Array 68 65 6c 6c 6f>

toString.call(uint8array, 'utf8'); // 'hello'
```

## Buffers and iteration

`Buffer` instances can be iterated over using `for..of` syntax:

*ES module version:*

```js
import { Buffer } from 'node:buffer';

const buf = Buffer.from([1, 2, 3]);

for (const b of buf) {
  console.log(b);
}
// Prints:
//   1
//   2
//   3
```

*CommonJS version:*

```js
const { Buffer } = require('node:buffer');

const buf = Buffer.from([1, 2, 3]);

for (const b of buf) {
  console.log(b);
}
// Prints:
//   1
//   2
//   3
```

Additionally, the [`buf.values()`](07-buffer-part3.md#bufvalues), [`buf.keys()`](07-buffer-part2.md#bufkeys), and
[`buf.entries()`](07-buffer-part2.md#bufentries) methods can be used to create iterators.

## Class: `Blob`

Added in: v15.7.0, v14.18.0

**History**

| Version | Changes |
| --- | --- |
| v18.0.0, v16.17.0 | No longer experimental. |

A [`<Blob>`](07-buffer-part1.md#class-blob) encapsulates immutable, raw data that can be safely shared across
multiple worker threads.

### `new buffer.Blob([sources[, options]])`

Added in: v15.7.0, v14.18.0

**History**

| Version | Changes |
| --- | --- |
| v16.7.0 | Added the standard `endings` option to replace line-endings, and removed the non-standard `encoding` option. |

- `sources` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type)[] | [`<ArrayBuffer>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/ArrayBuffer)[] | [`<TypedArray>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/TypedArray)[] | [`<DataView>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/DataView)[] | [`<Blob>`](07-buffer-part1.md#class-blob)[] An
  array of string, [`<ArrayBuffer>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/ArrayBuffer), [`<TypedArray>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/TypedArray), [`<DataView>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/DataView), or [`<Blob>`](07-buffer-part1.md#class-blob) objects,
  or any mix of such objects, that will be stored within the `Blob`.
- `options` [`<Object>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Object)
  - `endings` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) One of either `'transparent'` or `'native'`. When set
    to `'native'`, line endings in string source parts will be converted to
    the platform native line-ending as specified by `require('node:os').EOL`.
  - `type` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) The Blob content-type. The intent is for `type` to convey
    the MIME media type of the data, however no validation of the type format
    is performed.

Creates a new `Blob` object containing a concatenation of the given sources.

[`<ArrayBuffer>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/ArrayBuffer), [`<TypedArray>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/TypedArray), [`<DataView>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/DataView), and [`<Buffer>`](07-buffer-part1.md#class-buffer) sources are copied into
the 'Blob' and can therefore be safely modified after the 'Blob' is created.

String sources are encoded as UTF-8 byte sequences and copied into the Blob.
Unmatched surrogate pairs within each string part will be replaced by Unicode
U+FFFD replacement characters.

### `blob.arrayBuffer()`

Added in: v15.7.0, v14.18.0

- Returns: [`<Promise>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Promise)

Returns a promise that fulfills with an [`<ArrayBuffer>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/ArrayBuffer) containing a copy of
the `Blob` data.

### `blob.bytes()`

Added in: v22.3.0, v20.16.0

- Returns: [`<Promise>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Promise)

The `blob.bytes()` method returns the byte of the `Blob` object as a `Promise<Uint8Array>`.

```js
const blob = new Blob(['hello']);
blob.bytes().then((bytes) => {
  console.log(bytes); // Outputs: Uint8Array(5) [ 104, 101, 108, 108, 111 ]
});
```

### `blob.size`

Added in: v15.7.0, v14.18.0

The total size of the `Blob` in bytes.

### `blob.slice([start[, end[, type]]])`

Added in: v15.7.0, v14.18.0

- `start` [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) The starting index.
- `end` [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) The ending index.
- `type` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) The content-type for the new `Blob`
- Returns: [`<Blob>`](07-buffer-part1.md#class-blob)

Creates and returns a new `Blob` containing a subset of this `Blob` objects
data. The original `Blob` is not altered.

### `blob.stream()`

Added in: v16.7.0

- Returns: [`<ReadableStream>`](66-webstreams.md#class-readablestream)

Returns a new `ReadableStream` that allows the content of the `Blob` to be read.

### `blob.text()`

Added in: v15.7.0, v14.18.0

- Returns: [`<Promise>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Promise)

Returns a promise that fulfills with the contents of the `Blob` decoded as a
UTF-8 string.

### `blob.textStream()`

Added in: v26.5.0

- Returns: [`<ReadableStream>`](66-webstreams.md#class-readablestream)

Returns a new `ReadableStream` that allows the content of the `Blob` to be read
as a stream of UTF-8 decoded strings. It is equivalent to piping
[`blob.stream()`](#blobstream) through a [`TextDecoderStream`](66-webstreams.md#class-textdecoderstream) set up with UTF-8.

### `blob.type`

Added in: v15.7.0, v14.18.0

- Type: [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type)

The content-type of the `Blob`.

### `Blob` objects and `MessageChannel`

Once a [`<Blob>`](07-buffer-part1.md#class-blob) object is created, it can be sent via `MessagePort` to multiple
destinations without transferring or immediately copying the data. The data
contained by the `Blob` is copied only when the `arrayBuffer()` or `text()`
methods are called.

*ES module version:*

```js
import { Blob } from 'node:buffer';
import { setTimeout as delay } from 'node:timers/promises';

const blob = new Blob(['hello there']);

const mc1 = new MessageChannel();
const mc2 = new MessageChannel();

mc1.port1.onmessage = async ({ data }) => {
  console.log(await data.arrayBuffer());
  mc1.port1.close();
};

mc2.port1.onmessage = async ({ data }) => {
  await delay(1000);
  console.log(await data.arrayBuffer());
  mc2.port1.close();
};

mc1.port2.postMessage(blob);
mc2.port2.postMessage(blob);

// The Blob is still usable after posting.
blob.text().then(console.log);
```

*CommonJS version:*

```js
const { Blob } = require('node:buffer');
const { setTimeout: delay } = require('node:timers/promises');

const blob = new Blob(['hello there']);

const mc1 = new MessageChannel();
const mc2 = new MessageChannel();

mc1.port1.onmessage = async ({ data }) => {
  console.log(await data.arrayBuffer());
  mc1.port1.close();
};

mc2.port1.onmessage = async ({ data }) => {
  await delay(1000);
  console.log(await data.arrayBuffer());
  mc2.port1.close();
};

mc1.port2.postMessage(blob);
mc2.port2.postMessage(blob);

// The Blob is still usable after posting.
blob.text().then(console.log);
```

## Class: `Buffer`

The `Buffer` class is a global type for dealing with binary data directly.
It can be constructed in a variety of ways.

### Static method: `Buffer.alloc(size[, fill[, encoding]])`

Added in: v5.10.0

**History**

| Version | Changes |
| --- | --- |
| v20.0.0 | Throw ERR\_INVALID\_ARG\_TYPE or ERR\_OUT\_OF\_RANGE instead of ERR\_INVALID\_ARG\_VALUE for invalid input arguments. |
| v15.0.0 | Throw ERR\_INVALID\_ARG\_VALUE instead of ERR\_INVALID\_OPT\_VALUE for invalid input arguments. |
| v10.0.0 | Attempting to fill a non-zero length buffer with a zero length buffer triggers a thrown exception. |
| v10.0.0 | Specifying an invalid string for `fill` triggers a thrown exception. |
| v8.9.3 | Specifying an invalid string for `fill` now results in a zero-filled buffer. |

- `size` [`<integer>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) The desired length of the new `Buffer`.
- `fill` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) | [`<Buffer>`](07-buffer-part1.md#class-buffer) | [`<Uint8Array>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Uint8Array) | [`<integer>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) A value to pre-fill the new `Buffer`
  with. **Default:** `0`.
- `encoding` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) If `fill` is a string, this is its encoding.
  **Default:** `'utf8'`.
- Returns: [`<Buffer>`](07-buffer-part1.md#class-buffer)

Allocates a new `Buffer` of `size` bytes. If `fill` is `undefined`, the
`Buffer` will be zero-filled.

*ES module version:*

```js
import { Buffer } from 'node:buffer';

const buf = Buffer.alloc(5);

console.log(buf);
// Prints: <Buffer 00 00 00 00 00>
```

*CommonJS version:*

```js
const { Buffer } = require('node:buffer');

const buf = Buffer.alloc(5);

console.log(buf);
// Prints: <Buffer 00 00 00 00 00>
```

If `size` is larger than
[`buffer.constants.MAX_LENGTH`](07-buffer-part3.md#bufferconstantsmax_length) or smaller than 0, [`ERR_OUT_OF_RANGE`](22-errors-part3.md#err_out_of_range)
is thrown.

If `fill` is specified, the allocated `Buffer` will be initialized by calling
[`buf.fill(fill)`](07-buffer-part2.md#buffillvalue-offset-end-encoding).

*ES module version:*

```js
import { Buffer } from 'node:buffer';

const buf = Buffer.alloc(5, 'a');

console.log(buf);
// Prints: <Buffer 61 61 61 61 61>
```

*CommonJS version:*

```js
const { Buffer } = require('node:buffer');

const buf = Buffer.alloc(5, 'a');

console.log(buf);
// Prints: <Buffer 61 61 61 61 61>
```

If both `fill` and `encoding` are specified, the allocated `Buffer` will be
initialized by calling [`buf.fill(fill, encoding)`](07-buffer-part2.md#buffillvalue-offset-end-encoding).

*ES module version:*

```js
import { Buffer } from 'node:buffer';

const buf = Buffer.alloc(11, 'aGVsbG8gd29ybGQ=', 'base64');

console.log(buf);
// Prints: <Buffer 68 65 6c 6c 6f 20 77 6f 72 6c 64>
```

*CommonJS version:*

```js
const { Buffer } = require('node:buffer');

const buf = Buffer.alloc(11, 'aGVsbG8gd29ybGQ=', 'base64');

console.log(buf);
// Prints: <Buffer 68 65 6c 6c 6f 20 77 6f 72 6c 64>
```

Calling [`Buffer.alloc()`](#static-method-bufferallocsize-fill-encoding) can be measurably slower than the alternative
[`Buffer.allocUnsafe()`](#static-method-bufferallocunsafesize-alignment) but ensures that the newly created `Buffer` instance
contents will never contain sensitive data from previous allocations, including
data that might not have been allocated for `Buffer`s.

A `TypeError` will be thrown if `size` is not a number.

### Static method: `Buffer.allocUnsafe(size[, alignment])`

Added in: v5.10.0

**History**

| Version | Changes |
| --- | --- |
| v26.8.0 | Added the `alignment` argument. |
| v20.0.0 | Throw ERR\_INVALID\_ARG\_TYPE or ERR\_OUT\_OF\_RANGE instead of ERR\_INVALID\_ARG\_VALUE for invalid input arguments. |
| v15.0.0 | Throw ERR\_INVALID\_ARG\_VALUE instead of ERR\_INVALID\_OPT\_VALUE for invalid input arguments. |
| v7.0.0 | Passing a negative `size` will now throw an error. |

- `size` [`<integer>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) The desired length of the new `Buffer`.
- `alignment` [`<integer>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) If given, the memory backing the new `Buffer` will start
  at an address that is a multiple of `alignment`. Must be a power of two no
  larger than `2 ** 30`. See [Aligned allocations](07-buffer-part4.md#aligned-allocations).
- Returns: [`<Buffer>`](07-buffer-part1.md#class-buffer)

Allocates a new `Buffer` of `size` bytes. If `size` is larger than
[`buffer.constants.MAX_LENGTH`](07-buffer-part3.md#bufferconstantsmax_length) or smaller than 0, [`ERR_OUT_OF_RANGE`](22-errors-part3.md#err_out_of_range)
is thrown.

The underlying memory for `Buffer` instances created in this way is *not
initialized*. The contents of the newly created `Buffer` are unknown and
*may contain sensitive data*. Use [`Buffer.alloc()`](#static-method-bufferallocsize-fill-encoding) instead to initialize
`Buffer` instances with zeroes.

*ES module version:*

```js
import { Buffer } from 'node:buffer';

const buf = Buffer.allocUnsafe(10);

console.log(buf);
// Prints (contents may vary): <Buffer a0 8b 28 3f 01 00 00 00 50 32>

buf.fill(0);

console.log(buf);
// Prints: <Buffer 00 00 00 00 00 00 00 00 00 00>
```

*CommonJS version:*

```js
const { Buffer } = require('node:buffer');

const buf = Buffer.allocUnsafe(10);

console.log(buf);
// Prints (contents may vary): <Buffer a0 8b 28 3f 01 00 00 00 50 32>

buf.fill(0);

console.log(buf);
// Prints: <Buffer 00 00 00 00 00 00 00 00 00 00>
```

A `TypeError` will be thrown if `size` is not a number.

The `Buffer` module pre-allocates an internal `Buffer` instance of
size [`Buffer.poolSize`](#bufferpoolsize) that is used as a pool for the fast allocation of new
`Buffer` instances created using [`Buffer.allocUnsafe()`](#static-method-bufferallocunsafesize-alignment), [`Buffer.from(array)`](#static-method-bufferfromarray),
[`Buffer.from(string)`](#static-method-bufferfromstring-encoding), and [`Buffer.concat()`](#static-method-bufferconcatlist-totallength) only when `size` is less than
`Buffer.poolSize >>> 1` (floor of [`Buffer.poolSize`](#bufferpoolsize) divided by two).

Use of this pre-allocated internal memory pool is a key difference between
calling `Buffer.alloc(size, fill)` vs. `Buffer.allocUnsafe(size).fill(fill)`.
Specifically, `Buffer.alloc(size, fill)` will *never* use the internal `Buffer`
pool, while `Buffer.allocUnsafe(size).fill(fill)` *will* use the internal
`Buffer` pool if `size` is less than or equal to half [`Buffer.poolSize`](#bufferpoolsize). The
difference is subtle but can be important when an application requires the
additional performance that [`Buffer.allocUnsafe()`](#static-method-bufferallocunsafesize-alignment) provides.

### Static method: `Buffer.allocUnsafeSlow(size[, alignment])`

Added in: v5.12.0

**History**

| Version | Changes |
| --- | --- |
| v26.8.0 | Added the `alignment` argument. |
| v20.0.0 | Throw ERR\_INVALID\_ARG\_TYPE or ERR\_OUT\_OF\_RANGE instead of ERR\_INVALID\_ARG\_VALUE for invalid input arguments. |
| v15.0.0 | Throw ERR\_INVALID\_ARG\_VALUE instead of ERR\_INVALID\_OPT\_VALUE for invalid input arguments. |

- `size` [`<integer>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) The desired length of the new `Buffer`.
- `alignment` [`<integer>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) If given, the memory backing the new `Buffer` will start
  at an address that is a multiple of `alignment`. Must be a power of two no
  larger than `2 ** 30`. See [Aligned allocations](07-buffer-part4.md#aligned-allocations).
- Returns: [`<Buffer>`](07-buffer-part1.md#class-buffer)

Allocates a new `Buffer` of `size` bytes. If `size` is larger than
[`buffer.constants.MAX_LENGTH`](07-buffer-part3.md#bufferconstantsmax_length) or smaller than 0, [`ERR_OUT_OF_RANGE`](22-errors-part3.md#err_out_of_range)
is thrown. A zero-length `Buffer` is created if `size` is 0.

The underlying memory for `Buffer` instances created in this way is *not
initialized*. The contents of the newly created `Buffer` are unknown and
*may contain sensitive data*. Use [`buf.fill(0)`](07-buffer-part2.md#buffillvalue-offset-end-encoding) to initialize
such `Buffer` instances with zeroes.

When using [`Buffer.allocUnsafe()`](#static-method-bufferallocunsafesize-alignment) to allocate new `Buffer` instances,
allocations less than `Buffer.poolSize >>> 1` (32KiB when default poolSize is used) are sliced
from a single pre-allocated `Buffer`. This allows applications to avoid the
garbage collection overhead of creating many individually allocated `Buffer`
instances. This approach improves both performance and memory usage by
eliminating the need to track and clean up as many individual `ArrayBuffer` objects.

However, in the case where a developer may need to retain a small chunk of
memory from a pool for an indeterminate amount of time, it may be appropriate
to create an un-pooled `Buffer` instance using `Buffer.allocUnsafeSlow()` and
then copying out the relevant bits.

*ES module version:*

```js
import { Buffer } from 'node:buffer';

// Need to keep around a few small chunks of memory.
const store = [];

socket.on('readable', () => {
  let data;
  while (null !== (data = readable.read())) {
    // Allocate for retained data.
    const sb = Buffer.allocUnsafeSlow(10);

    // Copy the data into the new allocation.
    data.copy(sb, 0, 0, 10);

    store.push(sb);
  }
});
```

*CommonJS version:*

```js
const { Buffer } = require('node:buffer');

// Need to keep around a few small chunks of memory.
const store = [];

socket.on('readable', () => {
  let data;
  while (null !== (data = readable.read())) {
    // Allocate for retained data.
    const sb = Buffer.allocUnsafeSlow(10);

    // Copy the data into the new allocation.
    data.copy(sb, 0, 0, 10);

    store.push(sb);
  }
});
```

A `TypeError` will be thrown if `size` is not a number.

### Static method: `Buffer.byteLength(string[, encoding])`

Added in: v0.1.90

**History**

| Version | Changes |
| --- | --- |
| v7.0.0 | Passing invalid input will now throw an error. |
| v5.10.0 | The `string` parameter can now be any `TypedArray`, `DataView` or `ArrayBuffer`. |

- `string` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) | [`<Buffer>`](07-buffer-part1.md#class-buffer) | [`<TypedArray>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/TypedArray) | [`<DataView>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/DataView) | [`<ArrayBuffer>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/ArrayBuffer) | [`<SharedArrayBuffer>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/SharedArrayBuffer) A
  value to calculate the length of.
- `encoding` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) If `string` is a string, this is its encoding.
  **Default:** `'utf8'`.
- Returns: [`<integer>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) The number of bytes contained within `string`.

Returns the byte length of a string when encoded using `encoding`.
This is not the same as [`String.prototype.length`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/String/length), which does not account
for the encoding that is used to convert the string into bytes.

For `'base64'`, `'base64url'`, and `'hex'`, this function assumes valid input.
For strings that contain non-base64/hex-encoded data (e.g. whitespace), the
return value might be greater than the length of a `Buffer` created from the
string.

*ES module version:*

```js
import { Buffer } from 'node:buffer';

const str = '\u00bd + \u00bc = \u00be';

console.log(`${str}: ${str.length} characters, ` +
            `${Buffer.byteLength(str, 'utf8')} bytes`);
// Prints: ½ + ¼ = ¾: 9 characters, 12 bytes
```

*CommonJS version:*

```js
const { Buffer } = require('node:buffer');

const str = '\u00bd + \u00bc = \u00be';

console.log(`${str}: ${str.length} characters, ` +
            `${Buffer.byteLength(str, 'utf8')} bytes`);
// Prints: ½ + ¼ = ¾: 9 characters, 12 bytes
```

When `string` is a [`<Buffer>`](07-buffer-part1.md#class-buffer) | [`<DataView>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/DataView) | [`<TypedArray>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/TypedArray) | [`<ArrayBuffer>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/ArrayBuffer) | [`<SharedArrayBuffer>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/SharedArrayBuffer),
the byte length as reported by `.byteLength` is returned.

### Static method: `Buffer.stringLength(input[, encoding])`

Added in: v26.11.0

- `input` [`<Buffer>`](07-buffer-part1.md#class-buffer) | [`<ArrayBuffer>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/ArrayBuffer) | [`<TypedArray>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/TypedArray) The bytes that would be decoded.
- `encoding` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) The character encoding `input` would be decoded with.
  **Default:** `'utf8'`.
- Returns: [`<integer>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type)

Returns the length, in UTF-16 code units, of the string that
`buf.toString(encoding)` would produce for the same bytes, without decoding
them. This is the counterpart of [`Buffer.byteLength()`](#static-method-bufferbytelengthstring-encoding), which returns the
number of bytes a string would encode to.

For `'utf8'`, invalid byte sequences are counted as they would be decoded:
each maximal invalid subsequence becomes one `U+FFFD` replacement character.
For every other encoding the result is computed from `input.byteLength` alone.

A detached `ArrayBuffer`, or a `TypedArray` backed by one, is treated as empty.

The result is not capped: compare it with
[`buffer.constants.MAX_STRING_LENGTH`](07-buffer-part3.md#bufferconstantsmax_string_length) before decoding to know whether the
decode can succeed at all. A string of `n` code units occupies between `n` and
`2 * n` bytes of memory.

*ES module version:*

```js
import { Buffer, constants } from 'node:buffer';

const buf = Buffer.from('€ 100', 'utf8');

console.log(Buffer.stringLength(buf));
// Prints: 5
console.log(Buffer.stringLength(buf, 'hex'));
// Prints: 14
console.log(Buffer.stringLength(buf) <= constants.MAX_STRING_LENGTH);
// Prints: true
```

*CommonJS version:*

```js
const { Buffer, constants } = require('node:buffer');

const buf = Buffer.from('€ 100', 'utf8');

console.log(Buffer.stringLength(buf));
// Prints: 5
console.log(Buffer.stringLength(buf, 'hex'));
// Prints: 14
console.log(Buffer.stringLength(buf) <= constants.MAX_STRING_LENGTH);
// Prints: true
```

### Static method: `Buffer.compare(buf1, buf2)`

Added in: v0.11.13

**History**

| Version | Changes |
| --- | --- |
| v8.0.0 | The arguments can now be `Uint8Array`s. |

- `buf1` [`<Buffer>`](07-buffer-part1.md#class-buffer) | [`<Uint8Array>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Uint8Array)
- `buf2` [`<Buffer>`](07-buffer-part1.md#class-buffer) | [`<Uint8Array>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Uint8Array)
- Returns: [`<integer>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) Either `-1`, `0`, or `1`, depending on the result of the
  comparison. See [`buf.compare()`](07-buffer-part2.md#bufcomparetarget-targetstart-targetend-sourcestart-sourceend) for details.

Compares `buf1` to `buf2`, typically for the purpose of sorting arrays of
`Buffer` instances. This is equivalent to calling
[`buf1.compare(buf2)`](07-buffer-part2.md#bufcomparetarget-targetstart-targetend-sourcestart-sourceend).

*ES module version:*

```js
import { Buffer } from 'node:buffer';

const buf1 = Buffer.from('1234');
const buf2 = Buffer.from('0123');
const arr = [buf1, buf2];

console.log(arr.sort(Buffer.compare));
// Prints: [ <Buffer 30 31 32 33>, <Buffer 31 32 33 34> ]
// (This result is equal to: [buf2, buf1].)
```

*CommonJS version:*

```js
const { Buffer } = require('node:buffer');

const buf1 = Buffer.from('1234');
const buf2 = Buffer.from('0123');
const arr = [buf1, buf2];

console.log(arr.sort(Buffer.compare));
// Prints: [ <Buffer 30 31 32 33>, <Buffer 31 32 33 34> ]
// (This result is equal to: [buf2, buf1].)
```

### Static method: `Buffer.concat(list[, totalLength])`

Added in: v0.7.11

**History**

| Version | Changes |
| --- | --- |
| v8.0.0 | The elements of `list` can now be `Uint8Array`s. |

- `list` [`<Buffer>`](07-buffer-part1.md#class-buffer)[] | [`<Uint8Array>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Uint8Array)[] List of `Buffer` or [`<Uint8Array>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Uint8Array)
  instances to concatenate.
- `totalLength` [`<integer>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) Total length of the `Buffer` instances in `list`
  when concatenated.
- Returns: [`<Buffer>`](07-buffer-part1.md#class-buffer)

Returns a new `Buffer` which is the result of concatenating all the `Buffer`
instances in the `list` together.

If the list has no items, or if the `totalLength` is 0, then a new zero-length
`Buffer` is returned.

If `totalLength` is not provided, it is calculated from the `Buffer` instances
in `list` by adding their lengths.

If `totalLength` is provided, it must be an unsigned integer. If the
combined length of the `Buffer`s in `list` exceeds `totalLength`, the result is
truncated to `totalLength`. If the combined length of the `Buffer`s in `list` is
less than `totalLength`, the remaining space is filled with zeros.

*ES module version:*

```js
import { Buffer } from 'node:buffer';

// Create a single `Buffer` from a list of three `Buffer` instances.

const buf1 = Buffer.alloc(10);
const buf2 = Buffer.alloc(14);
const buf3 = Buffer.alloc(18);
const totalLength = buf1.length + buf2.length + buf3.length;

console.log(totalLength);
// Prints: 42

const bufA = Buffer.concat([buf1, buf2, buf3], totalLength);

console.log(bufA);
// Prints: <Buffer 00 00 00 00 ...>
console.log(bufA.length);
// Prints: 42
```

*CommonJS version:*

```js
const { Buffer } = require('node:buffer');

// Create a single `Buffer` from a list of three `Buffer` instances.

const buf1 = Buffer.alloc(10);
const buf2 = Buffer.alloc(14);
const buf3 = Buffer.alloc(18);
const totalLength = buf1.length + buf2.length + buf3.length;

console.log(totalLength);
// Prints: 42

const bufA = Buffer.concat([buf1, buf2, buf3], totalLength);

console.log(bufA);
// Prints: <Buffer 00 00 00 00 ...>
console.log(bufA.length);
// Prints: 42
```

`Buffer.concat()` may also use the internal `Buffer` pool like
[`Buffer.allocUnsafe()`](#static-method-bufferallocunsafesize-alignment) does.

### Static method: `Buffer.copyBytesFrom(view[, offset[, length]])`

Added in: v19.8.0, v18.16.0

- `view` [`<TypedArray>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/TypedArray) The [`<TypedArray>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/TypedArray) to copy.
- `offset` [`<integer>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) The starting offset within `view`. **Default:** `0`.
- `length` [`<integer>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) The number of elements from `view` to copy.
  **Default:** `view.length - offset`.
- Returns: [`<Buffer>`](07-buffer-part1.md#class-buffer)

Copies the underlying memory of `view` into a new `Buffer`.

```js
const u16 = new Uint16Array([0, 0xffff]);
const buf = Buffer.copyBytesFrom(u16, 1, 1);
u16[1] = 0;
console.log(buf.length); // 2
console.log(buf[0]); // 255
console.log(buf[1]); // 255
```

### Static method: `Buffer.from(array)`

Added in: v5.10.0

- `array` [`<integer>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type)[]
- Returns: [`<Buffer>`](07-buffer-part1.md#class-buffer)

Allocates a new `Buffer` using an `array` of bytes in the range `0` – `255`.
Array entries outside that range will be truncated to fit into it.

*ES module version:*

```js
import { Buffer } from 'node:buffer';

// Creates a new Buffer containing the UTF-8 bytes of the string 'buffer'.
const buf = Buffer.from([0x62, 0x75, 0x66, 0x66, 0x65, 0x72]);
```

*CommonJS version:*

```js
const { Buffer } = require('node:buffer');

// Creates a new Buffer containing the UTF-8 bytes of the string 'buffer'.
const buf = Buffer.from([0x62, 0x75, 0x66, 0x66, 0x65, 0x72]);
```

If `array` is an `Array`-like object (that is, one with a `length` property of
type `number`), it is treated as if it is an array, unless it is a `Buffer` or
a `Uint8Array`. This means all other `TypedArray` variants get treated as an
`Array`. To create a `Buffer` from the bytes backing a `TypedArray`, use
[`Buffer.copyBytesFrom()`](#static-method-buffercopybytesfromview-offset-length).

A `TypeError` will be thrown if `array` is not an `Array` or another type
appropriate for `Buffer.from()` variants.

`Buffer.from(array)` and [`Buffer.from(string)`](#static-method-bufferfromstring-encoding) may also use the internal
`Buffer` pool like [`Buffer.allocUnsafe()`](#static-method-bufferallocunsafesize-alignment) does.

### Static method: `Buffer.from(arrayBuffer[, byteOffset[, length]])`

Added in: v5.10.0

- `arrayBuffer` [`<ArrayBuffer>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/ArrayBuffer) | [`<SharedArrayBuffer>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/SharedArrayBuffer) An [`<ArrayBuffer>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/ArrayBuffer),
  [`<SharedArrayBuffer>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/SharedArrayBuffer), for example the `.buffer` property of a
  [`<TypedArray>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/TypedArray).
- `byteOffset` [`<integer>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) Index of first byte to expose. **Default:** `0`.
- `length` [`<integer>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) Number of bytes to expose. **Default:** `arrayBuffer.byteLength - byteOffset`.
- Returns: [`<Buffer>`](07-buffer-part1.md#class-buffer)

This creates a view of the [`<ArrayBuffer>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/ArrayBuffer) without copying the underlying
memory. For example, when passed a reference to the `.buffer` property of a
[`<TypedArray>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/TypedArray) instance, the newly created `Buffer` will share the same
allocated memory as the [`<TypedArray>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/TypedArray)'s underlying `ArrayBuffer`.

*ES module version:*

```js
import { Buffer } from 'node:buffer';

const arr = new Uint16Array(2);

arr[0] = 5000;
arr[1] = 4000;

// Shares memory with `arr`.
const buf = Buffer.from(arr.buffer);

console.log(buf);
// Prints: <Buffer 88 13 a0 0f>

// Changing the original Uint16Array changes the Buffer also.
arr[1] = 6000;

console.log(buf);
// Prints: <Buffer 88 13 70 17>
```

*CommonJS version:*

```js
const { Buffer } = require('node:buffer');

const arr = new Uint16Array(2);

arr[0] = 5000;
arr[1] = 4000;

// Shares memory with `arr`.
const buf = Buffer.from(arr.buffer);

console.log(buf);
// Prints: <Buffer 88 13 a0 0f>

// Changing the original Uint16Array changes the Buffer also.
arr[1] = 6000;

console.log(buf);
// Prints: <Buffer 88 13 70 17>
```

The optional `byteOffset` and `length` arguments specify a memory range within
the `arrayBuffer` that will be shared by the `Buffer`.

*ES module version:*

```js
import { Buffer } from 'node:buffer';

const ab = new ArrayBuffer(10);
const buf = Buffer.from(ab, 0, 2);

console.log(buf.length);
// Prints: 2
```

*CommonJS version:*

```js
const { Buffer } = require('node:buffer');

const ab = new ArrayBuffer(10);
const buf = Buffer.from(ab, 0, 2);

console.log(buf.length);
// Prints: 2
```

A `TypeError` will be thrown if `arrayBuffer` is not an [`<ArrayBuffer>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/ArrayBuffer) or a
[`<SharedArrayBuffer>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/SharedArrayBuffer) or another type appropriate for `Buffer.from()`
variants.

It is important to remember that a backing `ArrayBuffer` can cover a range
of memory that extends beyond the bounds of a `TypedArray` view. A new
`Buffer` created using the `buffer` property of a `TypedArray` may extend
beyond the range of the `TypedArray`:

*ES module version:*

```js
import { Buffer } from 'node:buffer';

const arrA = Uint8Array.from([0x63, 0x64, 0x65, 0x66]); // 4 elements
const arrB = new Uint8Array(arrA.buffer, 1, 2); // 2 elements
console.log(arrA.buffer === arrB.buffer); // true

const buf = Buffer.from(arrB.buffer);
console.log(buf);
// Prints: <Buffer 63 64 65 66>
```

*CommonJS version:*

```js
const { Buffer } = require('node:buffer');

const arrA = Uint8Array.from([0x63, 0x64, 0x65, 0x66]); // 4 elements
const arrB = new Uint8Array(arrA.buffer, 1, 2); // 2 elements
console.log(arrA.buffer === arrB.buffer); // true

const buf = Buffer.from(arrB.buffer);
console.log(buf);
// Prints: <Buffer 63 64 65 66>
```

### Static method: `Buffer.from(buffer)`

Added in: v5.10.0

- `buffer` [`<Buffer>`](07-buffer-part1.md#class-buffer) | [`<Uint8Array>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Uint8Array) An existing `Buffer` or [`<Uint8Array>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Uint8Array) from
  which to copy data.
- Returns: [`<Buffer>`](07-buffer-part1.md#class-buffer)

Copies the passed `buffer` data onto a new `Buffer` instance.

*ES module version:*

```js
import { Buffer } from 'node:buffer';

const buf1 = Buffer.from('buffer');
const buf2 = Buffer.from(buf1);

buf1[0] = 0x61;

console.log(buf1.toString());
// Prints: auffer
console.log(buf2.toString());
// Prints: buffer
```

*CommonJS version:*

```js
const { Buffer } = require('node:buffer');

const buf1 = Buffer.from('buffer');
const buf2 = Buffer.from(buf1);

buf1[0] = 0x61;

console.log(buf1.toString());
// Prints: auffer
console.log(buf2.toString());
// Prints: buffer
```

A `TypeError` will be thrown if `buffer` is not a `Buffer` or another type
appropriate for `Buffer.from()` variants.

### Static method: `Buffer.from(object[, offsetOrEncoding[, length]])`

Added in: v8.2.0

- `object` [`<Object>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Object) An object supporting `Symbol.toPrimitive` or `valueOf()`.
- `offsetOrEncoding` [`<integer>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) | [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) A byte-offset or encoding.
- `length` [`<integer>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) A length.
- Returns: [`<Buffer>`](07-buffer-part1.md#class-buffer)

For objects whose `valueOf()` function returns a value not strictly equal to
`object`, returns `Buffer.from(object.valueOf(), offsetOrEncoding, length)`.

*ES module version:*

```js
import { Buffer } from 'node:buffer';

const buf = Buffer.from(new String('this is a test'));
// Prints: <Buffer 74 68 69 73 20 69 73 20 61 20 74 65 73 74>
```

*CommonJS version:*

```js
const { Buffer } = require('node:buffer');

const buf = Buffer.from(new String('this is a test'));
// Prints: <Buffer 74 68 69 73 20 69 73 20 61 20 74 65 73 74>
```

For objects that support `Symbol.toPrimitive`, returns
`Buffer.from(object[Symbol.toPrimitive]('string'), offsetOrEncoding)`.

*ES module version:*

```js
import { Buffer } from 'node:buffer';

class Foo {
  [Symbol.toPrimitive]() {
    return 'this is a test';
  }
}

const buf = Buffer.from(new Foo(), 'utf8');
// Prints: <Buffer 74 68 69 73 20 69 73 20 61 20 74 65 73 74>
```

*CommonJS version:*

```js
const { Buffer } = require('node:buffer');

class Foo {
  [Symbol.toPrimitive]() {
    return 'this is a test';
  }
}

const buf = Buffer.from(new Foo(), 'utf8');
// Prints: <Buffer 74 68 69 73 20 69 73 20 61 20 74 65 73 74>
```

A `TypeError` will be thrown if `object` does not have the mentioned methods or
is not of another type appropriate for `Buffer.from()` variants.

### Static method: `Buffer.from(string[, encoding])`

Added in: v5.10.0

- `string` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) A string to encode.
- `encoding` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) The encoding of `string`. **Default:** `'utf8'`.
- Returns: [`<Buffer>`](07-buffer-part1.md#class-buffer)

Creates a new `Buffer` containing `string`. The `encoding` parameter identifies
the character encoding to be used when converting `string` into bytes.

*ES module version:*

```js
import { Buffer } from 'node:buffer';

const buf1 = Buffer.from('this is a tést');
const buf2 = Buffer.from('7468697320697320612074c3a97374', 'hex');

console.log(buf1.toString());
// Prints: this is a tést
console.log(buf2.toString());
// Prints: this is a tést
console.log(buf1.toString('latin1'));
// Prints: this is a tÃ©st
```

*CommonJS version:*

```js
const { Buffer } = require('node:buffer');

const buf1 = Buffer.from('this is a tést');
const buf2 = Buffer.from('7468697320697320612074c3a97374', 'hex');

console.log(buf1.toString());
// Prints: this is a tést
console.log(buf2.toString());
// Prints: this is a tést
console.log(buf1.toString('latin1'));
// Prints: this is a tÃ©st
```

A `TypeError` will be thrown if `string` is not a string or another type
appropriate for `Buffer.from()` variants.

[`Buffer.from(string)`](#static-method-bufferfromstring-encoding) may also use the internal `Buffer` pool like
[`Buffer.allocUnsafe()`](#static-method-bufferallocunsafesize-alignment) does.

### Static method: `Buffer.isBuffer(obj)`

Added in: v0.1.101

- `obj` [`<Object>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Object)
- Returns: [`<boolean>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#boolean_type)

Returns `true` if `obj` is a `Buffer`, `false` otherwise.

*ES module version:*

```js
import { Buffer } from 'node:buffer';

Buffer.isBuffer(Buffer.alloc(10)); // true
Buffer.isBuffer(Buffer.from('foo')); // true
Buffer.isBuffer('a string'); // false
Buffer.isBuffer([]); // false
Buffer.isBuffer(new Uint8Array(1024)); // false
```

*CommonJS version:*

```js
const { Buffer } = require('node:buffer');

Buffer.isBuffer(Buffer.alloc(10)); // true
Buffer.isBuffer(Buffer.from('foo')); // true
Buffer.isBuffer('a string'); // false
Buffer.isBuffer([]); // false
Buffer.isBuffer(new Uint8Array(1024)); // false
```

### Static method: `Buffer.isEncoding(encoding)`

Added in: v0.9.1

- `encoding` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) A character encoding name to check.
- Returns: [`<boolean>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#boolean_type)

Returns `true` if `encoding` is the name of a supported character encoding,
or `false` otherwise.

*ES module version:*

```js
import { Buffer } from 'node:buffer';

console.log(Buffer.isEncoding('utf8'));
// Prints: true

console.log(Buffer.isEncoding('hex'));
// Prints: true

console.log(Buffer.isEncoding('utf/8'));
// Prints: false

console.log(Buffer.isEncoding(''));
// Prints: false
```

*CommonJS version:*

```js
const { Buffer } = require('node:buffer');

console.log(Buffer.isEncoding('utf8'));
// Prints: true

console.log(Buffer.isEncoding('hex'));
// Prints: true

console.log(Buffer.isEncoding('utf/8'));
// Prints: false

console.log(Buffer.isEncoding(''));
// Prints: false
```

### `Buffer.poolSize`

Added in: v0.11.3

**History**

| Version | Changes |
| --- | --- |
| v26.3.0 | Default raised from 8192 to 65536. |

- Type: [`<integer>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) **Default:** `65536`

This is the size (in bytes) of pre-allocated internal `Buffer` instances used
for pooling. This value may be modified.

### `buf[index]`

- `index` [`<integer>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type)

The index operator `[index]` can be used to get and set the octet at position
`index` in `buf`. The values refer to individual bytes, so the legal value
range is between `0x00` and `0xFF` (hex) or `0` and `255` (decimal).

This operator is inherited from `Uint8Array`, so its behavior on out-of-bounds
access is the same as `Uint8Array`. In other words, `buf[index]` returns
`undefined` when `index` is negative or greater or equal to `buf.length`, and
`buf[index] = value` does not modify the buffer if `index` is negative or
`>= buf.length`.

*ES module version:*

```js
import { Buffer } from 'node:buffer';

// Copy an ASCII string into a `Buffer` one byte at a time.
// (This only works for ASCII-only strings. In general, one should use
// `Buffer.from()` to perform this conversion.)

const str = 'Node.js';
const buf = Buffer.allocUnsafe(str.length);

for (let i = 0; i < str.length; i++) {
  buf[i] = str.charCodeAt(i);
}

console.log(buf.toString('utf8'));
// Prints: Node.js
```

*CommonJS version:*

```js
const { Buffer } = require('node:buffer');

// Copy an ASCII string into a `Buffer` one byte at a time.
// (This only works for ASCII-only strings. In general, one should use
// `Buffer.from()` to perform this conversion.)

const str = 'Node.js';
const buf = Buffer.allocUnsafe(str.length);

for (let i = 0; i < str.length; i++) {
  buf[i] = str.charCodeAt(i);
}

console.log(buf.toString('utf8'));
// Prints: Node.js
```

### `buf.buffer`

- Type: [`<ArrayBuffer>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/ArrayBuffer) The underlying `ArrayBuffer` object based on which this `Buffer`
  object is created.

This `ArrayBuffer` is not guaranteed to correspond exactly to the original
`Buffer`. See the notes on `buf.byteOffset` for details.

*ES module version:*

```js
import { Buffer } from 'node:buffer';

const arrayBuffer = new ArrayBuffer(16);
const buffer = Buffer.from(arrayBuffer);

console.log(buffer.buffer === arrayBuffer);
// Prints: true
```

*CommonJS version:*

```js
const { Buffer } = require('node:buffer');

const arrayBuffer = new ArrayBuffer(16);
const buffer = Buffer.from(arrayBuffer);

console.log(buffer.buffer === arrayBuffer);
// Prints: true
```

### `buf.byteOffset`

- Type: [`<integer>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) The `byteOffset` of the `Buffer`'s underlying `ArrayBuffer` object.

When setting `byteOffset` in `Buffer.from(ArrayBuffer, byteOffset, length)`,
or sometimes when allocating a `Buffer` smaller than `Buffer.poolSize`, the
buffer does not start from a zero offset on the underlying `ArrayBuffer`.

This can cause problems when accessing the underlying `ArrayBuffer` directly
using `buf.buffer`, as other parts of the `ArrayBuffer` may be unrelated
to the `Buffer` object itself.

A common issue when creating a `TypedArray` object that shares its memory with
a `Buffer` is that in this case one needs to specify the `byteOffset` correctly:

*ES module version:*

```js
import { Buffer } from 'node:buffer';

// Create a buffer smaller than `Buffer.poolSize`.
const nodeBuffer = Buffer.from([0, 1, 2, 3, 4, 5, 6, 7, 8, 9]);

// When casting the Node.js Buffer to an Int8Array, use the byteOffset
// to refer only to the part of `nodeBuffer.buffer` that contains the memory
// for `nodeBuffer`.
new Int8Array(nodeBuffer.buffer, nodeBuffer.byteOffset, nodeBuffer.length);
```

*CommonJS version:*

```js
const { Buffer } = require('node:buffer');

// Create a buffer smaller than `Buffer.poolSize`.
const nodeBuffer = Buffer.from([0, 1, 2, 3, 4, 5, 6, 7, 8, 9]);

// When casting the Node.js Buffer to an Int8Array, use the byteOffset
// to refer only to the part of `nodeBuffer.buffer` that contains the memory
// for `nodeBuffer`.
new Int8Array(nodeBuffer.buffer, nodeBuffer.byteOffset, nodeBuffer.length);
```

[Contents](../README.md) | [Previous: Benchmark runner](06-bench.md) | [Next: Buffer, part 2 of 4](07-buffer-part2.md)
