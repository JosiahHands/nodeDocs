[Contents](../README.md) | [Previous: Buffer, part 1 of 4](07-buffer-part1.md) | [Next: Buffer, part 3 of 4](07-buffer-part3.md)

Source: [https://nodejs.org/docs/latest/api/buffer.html](https://nodejs.org/docs/latest/api/buffer.html)

Part 2 of 4 of this section.

# Buffer (part 2 of 4)

### `buf.compare(target[, targetStart[, targetEnd[, sourceStart[, sourceEnd]]]])`

Added in: v0.11.13

**History**

| Version | Changes |
| --- | --- |
| v8.0.0 | The `target` parameter can now be a `Uint8Array`. |
| v5.11.0 | Additional parameters for specifying offsets are supported now. |

- `target` [`<Buffer>`](07-buffer-part1.md#class-buffer) | [`<Uint8Array>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Uint8Array) A `Buffer` or [`<Uint8Array>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Uint8Array) with which to
  compare `buf`.
- `targetStart` [`<integer>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) The offset within `target` at which to begin
  comparison. **Default:** `0`.
- `targetEnd` [`<integer>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) The offset within `target` at which to end comparison
  (not inclusive). **Default:** `target.length`.
- `sourceStart` [`<integer>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) The offset within `buf` at which to begin comparison.
  **Default:** `0`.
- `sourceEnd` [`<integer>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) The offset within `buf` at which to end comparison
  (not inclusive). **Default:** [`buf.length`](#buflength).
- Returns: [`<integer>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type)

Compares `buf` with `target` and returns a number indicating whether `buf`
comes before, after, or is the same as `target` in sort order.
Comparison is based on the actual sequence of bytes in each `Buffer`.

- `0` is returned if `target` is the same as `buf`
- `1` is returned if `target` should come *before* `buf` when sorted.
- `-1` is returned if `target` should come *after* `buf` when sorted.

*ES module version:*

```js
import { Buffer } from 'node:buffer';

const buf1 = Buffer.from('ABC');
const buf2 = Buffer.from('BCD');
const buf3 = Buffer.from('ABCD');

console.log(buf1.compare(buf1));
// Prints: 0
console.log(buf1.compare(buf2));
// Prints: -1
console.log(buf1.compare(buf3));
// Prints: -1
console.log(buf2.compare(buf1));
// Prints: 1
console.log(buf2.compare(buf3));
// Prints: 1
console.log([buf1, buf2, buf3].sort(Buffer.compare));
// Prints: [ <Buffer 41 42 43>, <Buffer 41 42 43 44>, <Buffer 42 43 44> ]
// (This result is equal to: [buf1, buf3, buf2].)
```

*CommonJS version:*

```js
const { Buffer } = require('node:buffer');

const buf1 = Buffer.from('ABC');
const buf2 = Buffer.from('BCD');
const buf3 = Buffer.from('ABCD');

console.log(buf1.compare(buf1));
// Prints: 0
console.log(buf1.compare(buf2));
// Prints: -1
console.log(buf1.compare(buf3));
// Prints: -1
console.log(buf2.compare(buf1));
// Prints: 1
console.log(buf2.compare(buf3));
// Prints: 1
console.log([buf1, buf2, buf3].sort(Buffer.compare));
// Prints: [ <Buffer 41 42 43>, <Buffer 41 42 43 44>, <Buffer 42 43 44> ]
// (This result is equal to: [buf1, buf3, buf2].)
```

The optional `targetStart`, `targetEnd`, `sourceStart`, and `sourceEnd`
arguments can be used to limit the comparison to specific ranges within `target`
and `buf` respectively.

*ES module version:*

```js
import { Buffer } from 'node:buffer';

const buf1 = Buffer.from([1, 2, 3, 4, 5, 6, 7, 8, 9]);
const buf2 = Buffer.from([5, 6, 7, 8, 9, 1, 2, 3, 4]);

console.log(buf1.compare(buf2, 5, 9, 0, 4));
// Prints: 0
console.log(buf1.compare(buf2, 0, 6, 4));
// Prints: -1
console.log(buf1.compare(buf2, 5, 6, 5));
// Prints: 1
```

*CommonJS version:*

```js
const { Buffer } = require('node:buffer');

const buf1 = Buffer.from([1, 2, 3, 4, 5, 6, 7, 8, 9]);
const buf2 = Buffer.from([5, 6, 7, 8, 9, 1, 2, 3, 4]);

console.log(buf1.compare(buf2, 5, 9, 0, 4));
// Prints: 0
console.log(buf1.compare(buf2, 0, 6, 4));
// Prints: -1
console.log(buf1.compare(buf2, 5, 6, 5));
// Prints: 1
```

[`ERR_OUT_OF_RANGE`](22-errors-part3.md#err_out_of_range) is thrown if `targetStart < 0`, `sourceStart < 0`,
`targetEnd > target.byteLength`, or `sourceEnd > source.byteLength`.

### `buf.copy(target[, targetStart[, sourceStart[, sourceEnd]]])`

Added in: v0.1.90

- `target` [`<Buffer>`](07-buffer-part1.md#class-buffer) | [`<Uint8Array>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Uint8Array) A `Buffer` or [`<Uint8Array>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Uint8Array) to copy into.
- `targetStart` [`<integer>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) The offset within `target` at which to begin
  writing. **Default:** `0`.
- `sourceStart` [`<integer>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) The offset within `buf` from which to begin copying.
  **Default:** `0`.
- `sourceEnd` [`<integer>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) The offset within `buf` at which to stop copying (not
  inclusive). **Default:** [`buf.length`](#buflength).
- Returns: [`<integer>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) The number of bytes copied.

Copies data from a region of `buf` to a region in `target`, even if the `target`
memory region overlaps with `buf`.

[`TypedArray.prototype.set()`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/TypedArray/set) performs the same operation, and is available
for all TypedArrays, including Node.js `Buffer`s, although it takes
different function arguments.

*ES module version:*

```js
import { Buffer } from 'node:buffer';

// Create two `Buffer` instances.
const buf1 = Buffer.allocUnsafe(26);
const buf2 = Buffer.allocUnsafe(26).fill('!');

for (let i = 0; i < 26; i++) {
  // 97 is the decimal ASCII value for 'a'.
  buf1[i] = i + 97;
}

// Copy `buf1` bytes 16 through 19 into `buf2` starting at byte 8 of `buf2`.
buf1.copy(buf2, 8, 16, 20);
// This is equivalent to:
// buf2.set(buf1.subarray(16, 20), 8);

console.log(buf2.toString('ascii', 0, 25));
// Prints: !!!!!!!!qrst!!!!!!!!!!!!!
```

*CommonJS version:*

```js
const { Buffer } = require('node:buffer');

// Create two `Buffer` instances.
const buf1 = Buffer.allocUnsafe(26);
const buf2 = Buffer.allocUnsafe(26).fill('!');

for (let i = 0; i < 26; i++) {
  // 97 is the decimal ASCII value for 'a'.
  buf1[i] = i + 97;
}

// Copy `buf1` bytes 16 through 19 into `buf2` starting at byte 8 of `buf2`.
buf1.copy(buf2, 8, 16, 20);
// This is equivalent to:
// buf2.set(buf1.subarray(16, 20), 8);

console.log(buf2.toString('ascii', 0, 25));
// Prints: !!!!!!!!qrst!!!!!!!!!!!!!
```

*ES module version:*

```js
import { Buffer } from 'node:buffer';

// Create a `Buffer` and copy data from one region to an overlapping region
// within the same `Buffer`.

const buf = Buffer.allocUnsafe(26);

for (let i = 0; i < 26; i++) {
  // 97 is the decimal ASCII value for 'a'.
  buf[i] = i + 97;
}

buf.copy(buf, 0, 4, 10);

console.log(buf.toString());
// Prints: efghijghijklmnopqrstuvwxyz
```

*CommonJS version:*

```js
const { Buffer } = require('node:buffer');

// Create a `Buffer` and copy data from one region to an overlapping region
// within the same `Buffer`.

const buf = Buffer.allocUnsafe(26);

for (let i = 0; i < 26; i++) {
  // 97 is the decimal ASCII value for 'a'.
  buf[i] = i + 97;
}

buf.copy(buf, 0, 4, 10);

console.log(buf.toString());
// Prints: efghijghijklmnopqrstuvwxyz
```

### `buf.entries()`

Added in: v1.1.0

- Returns: [`<Iterator>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Iterator)

Creates and returns an [iterator](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Iteration_protocols) of `[index, byte]` pairs from the contents
of `buf`.

*ES module version:*

```js
import { Buffer } from 'node:buffer';

// Log the entire contents of a `Buffer`.

const buf = Buffer.from('buffer');

for (const pair of buf.entries()) {
  console.log(pair);
}
// Prints:
//   [0, 98]
//   [1, 117]
//   [2, 102]
//   [3, 102]
//   [4, 101]
//   [5, 114]
```

*CommonJS version:*

```js
const { Buffer } = require('node:buffer');

// Log the entire contents of a `Buffer`.

const buf = Buffer.from('buffer');

for (const pair of buf.entries()) {
  console.log(pair);
}
// Prints:
//   [0, 98]
//   [1, 117]
//   [2, 102]
//   [3, 102]
//   [4, 101]
//   [5, 114]
```

### `buf.equals(otherBuffer)`

Added in: v0.11.13

**History**

| Version | Changes |
| --- | --- |
| v8.0.0 | The arguments can now be `Uint8Array`s. |

- `otherBuffer` [`<Buffer>`](07-buffer-part1.md#class-buffer) | [`<Uint8Array>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Uint8Array) A `Buffer` or [`<Uint8Array>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Uint8Array) with which to
  compare `buf`.
- Returns: [`<boolean>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#boolean_type)

Returns `true` if both `buf` and `otherBuffer` have exactly the same bytes,
`false` otherwise. Equivalent to
[`buf.compare(otherBuffer) === 0`](#bufcomparetarget-targetstart-targetend-sourcestart-sourceend).

*ES module version:*

```js
import { Buffer } from 'node:buffer';

const buf1 = Buffer.from('ABC');
const buf2 = Buffer.from('414243', 'hex');
const buf3 = Buffer.from('ABCD');

console.log(buf1.equals(buf2));
// Prints: true
console.log(buf1.equals(buf3));
// Prints: false
```

*CommonJS version:*

```js
const { Buffer } = require('node:buffer');

const buf1 = Buffer.from('ABC');
const buf2 = Buffer.from('414243', 'hex');
const buf3 = Buffer.from('ABCD');

console.log(buf1.equals(buf2));
// Prints: true
console.log(buf1.equals(buf3));
// Prints: false
```

### `buf.fill(value[, offset[, end]][, encoding])`

Added in: v0.5.0

**History**

| Version | Changes |
| --- | --- |
| v11.0.0 | Throws `ERR_OUT_OF_RANGE` instead of `ERR_INDEX_OUT_OF_RANGE`. |
| v10.0.0 | Negative `end` values throw an `ERR_INDEX_OUT_OF_RANGE` error. |
| v10.0.0 | Attempting to fill a non-zero length buffer with a zero length buffer triggers a thrown exception. |
| v10.0.0 | Specifying an invalid string for `value` triggers a thrown exception. |
| v5.7.0 | The `encoding` parameter is supported now. |

- `value` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) | [`<Buffer>`](07-buffer-part1.md#class-buffer) | [`<Uint8Array>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Uint8Array) | [`<integer>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) The value with which to fill `buf`.
  Empty value (string, Uint8Array, Buffer) is coerced to `0`.
- `offset` [`<integer>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) Number of bytes to skip before starting to fill `buf`.
  **Default:** `0`.
- `end` [`<integer>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) Where to stop filling `buf` (not inclusive). **Default:**
  [`buf.length`](#buflength).
- `encoding` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) The encoding for `value` if `value` is a string.
  **Default:** `'utf8'`.
- Returns: [`<Buffer>`](07-buffer-part1.md#class-buffer) A reference to `buf`.

Fills `buf` with the specified `value`. If the `offset` and `end` are not given,
the entire `buf` will be filled:

*ES module version:*

```js
import { Buffer } from 'node:buffer';

// Fill a `Buffer` with the ASCII character 'h'.

const b = Buffer.allocUnsafe(50).fill('h');

console.log(b.toString());
// Prints: hhhhhhhhhhhhhhhhhhhhhhhhhhhhhhhhhhhhhhhhhhhhhhhhhh

// Fill a buffer with empty string
const c = Buffer.allocUnsafe(5).fill('');

console.log(c.fill(''));
// Prints: <Buffer 00 00 00 00 00>
```

*CommonJS version:*

```js
const { Buffer } = require('node:buffer');

// Fill a `Buffer` with the ASCII character 'h'.

const b = Buffer.allocUnsafe(50).fill('h');

console.log(b.toString());
// Prints: hhhhhhhhhhhhhhhhhhhhhhhhhhhhhhhhhhhhhhhhhhhhhhhhhh

// Fill a buffer with empty string
const c = Buffer.allocUnsafe(5).fill('');

console.log(c.fill(''));
// Prints: <Buffer 00 00 00 00 00>
```

`value` is coerced to a `uint32` value if it is not a string, `Buffer`, or
integer. If the resulting integer is greater than `255` (decimal), `buf` will be
filled with `value & 255`.

If the final write of a `fill()` operation falls on a multi-byte character,
then only the bytes of that character that fit into `buf` are written:

*ES module version:*

```js
import { Buffer } from 'node:buffer';

// Fill a `Buffer` with character that takes up two bytes in UTF-8.

console.log(Buffer.allocUnsafe(5).fill('\u0222'));
// Prints: <Buffer c8 a2 c8 a2 c8>
```

*CommonJS version:*

```js
const { Buffer } = require('node:buffer');

// Fill a `Buffer` with character that takes up two bytes in UTF-8.

console.log(Buffer.allocUnsafe(5).fill('\u0222'));
// Prints: <Buffer c8 a2 c8 a2 c8>
```

If `value` contains invalid characters, it is truncated; if no valid
fill data remains, an exception is thrown:

*ES module version:*

```js
import { Buffer } from 'node:buffer';

const buf = Buffer.allocUnsafe(5);

console.log(buf.fill('a'));
// Prints: <Buffer 61 61 61 61 61>
console.log(buf.fill('aazz', 'hex'));
// Prints: <Buffer aa aa aa aa aa>
console.log(buf.fill('zz', 'hex'));
// Throws an exception.
```

*CommonJS version:*

```js
const { Buffer } = require('node:buffer');

const buf = Buffer.allocUnsafe(5);

console.log(buf.fill('a'));
// Prints: <Buffer 61 61 61 61 61>
console.log(buf.fill('aazz', 'hex'));
// Prints: <Buffer aa aa aa aa aa>
console.log(buf.fill('zz', 'hex'));
// Throws an exception.
```

### `buf.includes(value[, start[, end]][, encoding])`

Added in: v5.3.0

**History**

| Version | Changes |
| --- | --- |
| v26.1.0 | Added the `end` parameter. |
| v25.5.0, v24.13.1 | supports Uint8Array as `this` value. |

- `value` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) | [`<Buffer>`](07-buffer-part1.md#class-buffer) | [`<Uint8Array>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Uint8Array) | [`<integer>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) What to search for.
- `start` [`<integer>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) Where to begin searching in `buf`. If negative, then
  offset is calculated from the end of `buf`. **Default:** `0`.
- `end` [`<integer>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) Where to stop searching in `buf` (exclusive). **Default:**
  `buf.length`.
- `encoding` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) If `value` is a string, this is its encoding.
  **Default:** `'utf8'`.
- Returns: [`<boolean>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#boolean_type) `true` if `value` was found in `buf`, `false` otherwise.

Equivalent to [`buf.indexOf() !== -1`](#bufindexofvalue-start-end-encoding).

*ES module version:*

```js
import { Buffer } from 'node:buffer';

const buf = Buffer.from('this is a buffer');

console.log(buf.includes('this'));
// Prints: true
console.log(buf.includes('is'));
// Prints: true
console.log(buf.includes(Buffer.from('a buffer')));
// Prints: true
console.log(buf.includes(97));
// Prints: true (97 is the decimal ASCII value for 'a')
console.log(buf.includes(Buffer.from('a buffer example')));
// Prints: false
console.log(buf.includes(Buffer.from('a buffer example').slice(0, 8)));
// Prints: true
console.log(buf.includes('this', 4));
// Prints: false
```

*CommonJS version:*

```js
const { Buffer } = require('node:buffer');

const buf = Buffer.from('this is a buffer');

console.log(buf.includes('this'));
// Prints: true
console.log(buf.includes('is'));
// Prints: true
console.log(buf.includes(Buffer.from('a buffer')));
// Prints: true
console.log(buf.includes(97));
// Prints: true (97 is the decimal ASCII value for 'a')
console.log(buf.includes(Buffer.from('a buffer example')));
// Prints: false
console.log(buf.includes(Buffer.from('a buffer example').slice(0, 8)));
// Prints: true
console.log(buf.includes('this', 4));
// Prints: false
```

### `buf.indexOf(value[, start[, end]][, encoding])`

Added in: v1.5.0

**History**

| Version | Changes |
| --- | --- |
| v26.1.0 | Added the `end` parameter. |
| v8.0.0 | The `value` can now be a `Uint8Array`. |
| v5.7.0, v4.4.0 | When `encoding` is being passed, the `byteOffset` parameter is no longer required. |

- `value` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) | [`<Buffer>`](07-buffer-part1.md#class-buffer) | [`<Uint8Array>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Uint8Array) | [`<integer>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) What to search for.
- `start` [`<integer>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) Where to begin searching in `buf`. If negative, then
  offset is calculated from the end of `buf`. **Default:** `0`.
- `end` [`<integer>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) Where to stop searching in `buf` (exclusive). **Default:**
  `buf.length`.
- `encoding` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) If `value` is a string, this is the encoding used to
  determine the binary representation of the string that will be searched for in
  `buf`. **Default:** `'utf8'`.
- Returns: [`<integer>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) The index of the first occurrence of `value` in `buf`, or
  `-1` if `buf` does not contain `value`.

If `value` is:

- a string, `value` is interpreted according to the character encoding in
  `encoding`.
- a `Buffer` or [`<Uint8Array>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Uint8Array), `value` will be used in its entirety.
  To compare a partial `Buffer`, use [`buf.subarray`](#bufsubarraystart-end).
- a number, `value` will be interpreted as an unsigned 8-bit integer
  value between `0` and `255`.

*ES module version:*

```js
import { Buffer } from 'node:buffer';

const buf = Buffer.from('this is a buffer');

console.log(buf.indexOf('this'));
// Prints: 0
console.log(buf.indexOf('is'));
// Prints: 2
console.log(buf.indexOf(Buffer.from('a buffer')));
// Prints: 8
console.log(buf.indexOf(97));
// Prints: 8 (97 is the decimal ASCII value for 'a')
console.log(buf.indexOf(Buffer.from('a buffer example')));
// Prints: -1
console.log(buf.indexOf(Buffer.from('a buffer example').slice(0, 8)));
// Prints: 8

const utf16Buffer = Buffer.from('\u039a\u0391\u03a3\u03a3\u0395', 'utf16le');

console.log(utf16Buffer.indexOf('\u03a3', 0, 'utf16le'));
// Prints: 4
console.log(utf16Buffer.indexOf('\u03a3', -4, 'utf16le'));
// Prints: 6
```

*CommonJS version:*

```js
const { Buffer } = require('node:buffer');

const buf = Buffer.from('this is a buffer');

console.log(buf.indexOf('this'));
// Prints: 0
console.log(buf.indexOf('is'));
// Prints: 2
console.log(buf.indexOf(Buffer.from('a buffer')));
// Prints: 8
console.log(buf.indexOf(97));
// Prints: 8 (97 is the decimal ASCII value for 'a')
console.log(buf.indexOf(Buffer.from('a buffer example')));
// Prints: -1
console.log(buf.indexOf(Buffer.from('a buffer example').slice(0, 8)));
// Prints: 8

const utf16Buffer = Buffer.from('\u039a\u0391\u03a3\u03a3\u0395', 'utf16le');

console.log(utf16Buffer.indexOf('\u03a3', 0, 'utf16le'));
// Prints: 4
console.log(utf16Buffer.indexOf('\u03a3', -4, 'utf16le'));
// Prints: 6
```

If `value` is not a string, number, or `Buffer`, this method will throw a
`TypeError`. If `value` is a number, it will be coerced to a valid byte value,
an integer between 0 and 255.

If `byteOffset` is not a number, it will be coerced to a number. If the result
of coercion is `NaN` or `0`, then the entire buffer will be searched. This
behavior matches [`String.prototype.indexOf()`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/String/indexOf).

*ES module version:*

```js
import { Buffer } from 'node:buffer';

const b = Buffer.from('abcdef');

// Passing a value that's a number, but not a valid byte.
// Prints: 2, equivalent to searching for 99 or 'c'.
console.log(b.indexOf(99.9));
console.log(b.indexOf(256 + 99));

// Passing a byteOffset that coerces to NaN or 0.
// Prints: 1, searching the whole buffer.
console.log(b.indexOf('b', undefined));
console.log(b.indexOf('b', {}));
console.log(b.indexOf('b', null));
console.log(b.indexOf('b', []));
```

*CommonJS version:*

```js
const { Buffer } = require('node:buffer');

const b = Buffer.from('abcdef');

// Passing a value that's a number, but not a valid byte.
// Prints: 2, equivalent to searching for 99 or 'c'.
console.log(b.indexOf(99.9));
console.log(b.indexOf(256 + 99));

// Passing a byteOffset that coerces to NaN or 0.
// Prints: 1, searching the whole buffer.
console.log(b.indexOf('b', undefined));
console.log(b.indexOf('b', {}));
console.log(b.indexOf('b', null));
console.log(b.indexOf('b', []));
```

If `value` is an empty string or empty `Buffer` and `byteOffset` is less
than `buf.length`, `byteOffset` will be returned. If `value` is empty and
`byteOffset` is at least `buf.length`, `buf.length` will be returned.

### `buf.keys()`

Added in: v1.1.0

- Returns: [`<Iterator>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Iterator)

Creates and returns an [iterator](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Iteration_protocols) of `buf` keys (indexes).

*ES module version:*

```js
import { Buffer } from 'node:buffer';

const buf = Buffer.from('buffer');

for (const key of buf.keys()) {
  console.log(key);
}
// Prints:
//   0
//   1
//   2
//   3
//   4
//   5
```

*CommonJS version:*

```js
const { Buffer } = require('node:buffer');

const buf = Buffer.from('buffer');

for (const key of buf.keys()) {
  console.log(key);
}
// Prints:
//   0
//   1
//   2
//   3
//   4
//   5
```

### `buf.lastIndexOf(value[, start[, end]][, encoding])`

Added in: v6.0.0

**History**

| Version | Changes |
| --- | --- |
| v26.1.0 | Added the `end` parameter. |
| v8.0.0 | The `value` can now be a `Uint8Array`. |

- `value` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) | [`<Buffer>`](07-buffer-part1.md#class-buffer) | [`<Uint8Array>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Uint8Array) | [`<integer>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) What to search for.
- `start` [`<integer>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) Where to begin searching in `buf`. If negative, then
  offset is calculated from the end of `buf`. **Default:**
  `buf.length - 1`.
- `end` [`<integer>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) Where to stop searching in `buf` (exclusive). **Default:**
  `buf.length`.
- `encoding` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) If `value` is a string, this is the encoding used to
  determine the binary representation of the string that will be searched for in
  `buf`. **Default:** `'utf8'`.
- Returns: [`<integer>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) The index of the last occurrence of `value` in `buf`, or
  `-1` if `buf` does not contain `value`.

Identical to [`buf.indexOf()`](#bufindexofvalue-start-end-encoding), except the last occurrence of `value` is found
rather than the first occurrence.

*ES module version:*

```js
import { Buffer } from 'node:buffer';

const buf = Buffer.from('this buffer is a buffer');

console.log(buf.lastIndexOf('this'));
// Prints: 0
console.log(buf.lastIndexOf('buffer'));
// Prints: 17
console.log(buf.lastIndexOf(Buffer.from('buffer')));
// Prints: 17
console.log(buf.lastIndexOf(97));
// Prints: 15 (97 is the decimal ASCII value for 'a')
console.log(buf.lastIndexOf(Buffer.from('yolo')));
// Prints: -1
console.log(buf.lastIndexOf('buffer', 5));
// Prints: 5
console.log(buf.lastIndexOf('buffer', 4));
// Prints: -1

const utf16Buffer = Buffer.from('\u039a\u0391\u03a3\u03a3\u0395', 'utf16le');

console.log(utf16Buffer.lastIndexOf('\u03a3', undefined, 'utf16le'));
// Prints: 6
console.log(utf16Buffer.lastIndexOf('\u03a3', -5, 'utf16le'));
// Prints: 4
```

*CommonJS version:*

```js
const { Buffer } = require('node:buffer');

const buf = Buffer.from('this buffer is a buffer');

console.log(buf.lastIndexOf('this'));
// Prints: 0
console.log(buf.lastIndexOf('buffer'));
// Prints: 17
console.log(buf.lastIndexOf(Buffer.from('buffer')));
// Prints: 17
console.log(buf.lastIndexOf(97));
// Prints: 15 (97 is the decimal ASCII value for 'a')
console.log(buf.lastIndexOf(Buffer.from('yolo')));
// Prints: -1
console.log(buf.lastIndexOf('buffer', 5));
// Prints: 5
console.log(buf.lastIndexOf('buffer', 4));
// Prints: -1

const utf16Buffer = Buffer.from('\u039a\u0391\u03a3\u03a3\u0395', 'utf16le');

console.log(utf16Buffer.lastIndexOf('\u03a3', undefined, 'utf16le'));
// Prints: 6
console.log(utf16Buffer.lastIndexOf('\u03a3', -5, 'utf16le'));
// Prints: 4
```

If `value` is not a string, number, or `Buffer`, this method will throw a
`TypeError`. If `value` is a number, it will be coerced to a valid byte value,
an integer between 0 and 255.

If `byteOffset` is not a number, it will be coerced to a number. Any arguments
that coerce to `NaN`, like `{}` or `undefined`, will search the whole buffer.
This behavior matches [`String.prototype.lastIndexOf()`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/String/lastIndexOf).

*ES module version:*

```js
import { Buffer } from 'node:buffer';

const b = Buffer.from('abcdef');

// Passing a value that's a number, but not a valid byte.
// Prints: 2, equivalent to searching for 99 or 'c'.
console.log(b.lastIndexOf(99.9));
console.log(b.lastIndexOf(256 + 99));

// Passing a byteOffset that coerces to NaN.
// Prints: 1, searching the whole buffer.
console.log(b.lastIndexOf('b', undefined));
console.log(b.lastIndexOf('b', {}));

// Passing a byteOffset that coerces to 0.
// Prints: -1, equivalent to passing 0.
console.log(b.lastIndexOf('b', null));
console.log(b.lastIndexOf('b', []));
```

*CommonJS version:*

```js
const { Buffer } = require('node:buffer');

const b = Buffer.from('abcdef');

// Passing a value that's a number, but not a valid byte.
// Prints: 2, equivalent to searching for 99 or 'c'.
console.log(b.lastIndexOf(99.9));
console.log(b.lastIndexOf(256 + 99));

// Passing a byteOffset that coerces to NaN.
// Prints: 1, searching the whole buffer.
console.log(b.lastIndexOf('b', undefined));
console.log(b.lastIndexOf('b', {}));

// Passing a byteOffset that coerces to 0.
// Prints: -1, equivalent to passing 0.
console.log(b.lastIndexOf('b', null));
console.log(b.lastIndexOf('b', []));
```

If `value` is an empty string or empty `Buffer`, `byteOffset` will be returned.

### `buf.length`

Added in: v0.1.90

- Type: [`<integer>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type)

Returns the number of bytes in `buf`.

*ES module version:*

```js
import { Buffer } from 'node:buffer';

// Create a `Buffer` and write a shorter string to it using UTF-8.

const buf = Buffer.alloc(1234);

console.log(buf.length);
// Prints: 1234

buf.write('some string', 0, 'utf8');

console.log(buf.length);
// Prints: 1234
```

*CommonJS version:*

```js
const { Buffer } = require('node:buffer');

// Create a `Buffer` and write a shorter string to it using UTF-8.

const buf = Buffer.alloc(1234);

console.log(buf.length);
// Prints: 1234

buf.write('some string', 0, 'utf8');

console.log(buf.length);
// Prints: 1234
```

### `buf.parent`

Deprecated in: v8.0.0

Stability: 0 - Deprecated: Use [`buf.buffer`](07-buffer-part1.md#bufbuffer) instead.

The `buf.parent` property is a deprecated alias for `buf.buffer`.

### `buf.readBigInt64BE([offset])`

Added in: v12.0.0, v10.20.0

- `offset` [`<integer>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) Number of bytes to skip before starting to read. Must
  satisfy: `0 <= offset <= buf.length - 8`. **Default:** `0`.
- Returns: [`<bigint>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#bigint_type)

Reads a signed, big-endian 64-bit integer from `buf` at the specified `offset`.

Integers read from a `Buffer` are interpreted as two's complement signed
values.

### `buf.readBigInt64LE([offset])`

Added in: v12.0.0, v10.20.0

- `offset` [`<integer>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) Number of bytes to skip before starting to read. Must
  satisfy: `0 <= offset <= buf.length - 8`. **Default:** `0`.
- Returns: [`<bigint>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#bigint_type)

Reads a signed, little-endian 64-bit integer from `buf` at the specified
`offset`.

Integers read from a `Buffer` are interpreted as two's complement signed
values.

### `buf.readBigUInt64BE([offset])`

Added in: v12.0.0, v10.20.0

**History**

| Version | Changes |
| --- | --- |
| v14.10.0, v12.19.0 | This function is also available as `buf.readBigUint64BE()`. |

- `offset` [`<integer>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) Number of bytes to skip before starting to read. Must
  satisfy: `0 <= offset <= buf.length - 8`. **Default:** `0`.
- Returns: [`<bigint>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#bigint_type)

Reads an unsigned, big-endian 64-bit integer from `buf` at the specified
`offset`.

This function is also available under the `readBigUint64BE` alias.

*ES module version:*

```js
import { Buffer } from 'node:buffer';

const buf = Buffer.from([0x00, 0x00, 0x00, 0x00, 0xff, 0xff, 0xff, 0xff]);

console.log(buf.readBigUInt64BE(0));
// Prints: 4294967295n
```

*CommonJS version:*

```js
const { Buffer } = require('node:buffer');

const buf = Buffer.from([0x00, 0x00, 0x00, 0x00, 0xff, 0xff, 0xff, 0xff]);

console.log(buf.readBigUInt64BE(0));
// Prints: 4294967295n
```

### `buf.readBigUInt64LE([offset])`

Added in: v12.0.0, v10.20.0

**History**

| Version | Changes |
| --- | --- |
| v14.10.0, v12.19.0 | This function is also available as `buf.readBigUint64LE()`. |

- `offset` [`<integer>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) Number of bytes to skip before starting to read. Must
  satisfy: `0 <= offset <= buf.length - 8`. **Default:** `0`.
- Returns: [`<bigint>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#bigint_type)

Reads an unsigned, little-endian 64-bit integer from `buf` at the specified
`offset`.

This function is also available under the `readBigUint64LE` alias.

*ES module version:*

```js
import { Buffer } from 'node:buffer';

const buf = Buffer.from([0x00, 0x00, 0x00, 0x00, 0xff, 0xff, 0xff, 0xff]);

console.log(buf.readBigUInt64LE(0));
// Prints: 18446744069414584320n
```

*CommonJS version:*

```js
const { Buffer } = require('node:buffer');

const buf = Buffer.from([0x00, 0x00, 0x00, 0x00, 0xff, 0xff, 0xff, 0xff]);

console.log(buf.readBigUInt64LE(0));
// Prints: 18446744069414584320n
```

### `buf.readDoubleBE([offset])`

Added in: v0.11.15

**History**

| Version | Changes |
| --- | --- |
| v10.0.0 | Removed `noAssert` and no implicit coercion of the offset to `uint32` anymore. |

- `offset` [`<integer>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) Number of bytes to skip before starting to read. Must
  satisfy `0 <= offset <= buf.length - 8`. **Default:** `0`.
- Returns: [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type)

Reads a 64-bit, big-endian double from `buf` at the specified `offset`.

*ES module version:*

```js
import { Buffer } from 'node:buffer';

const buf = Buffer.from([1, 2, 3, 4, 5, 6, 7, 8]);

console.log(buf.readDoubleBE(0));
// Prints: 8.20788039913184e-304
```

*CommonJS version:*

```js
const { Buffer } = require('node:buffer');

const buf = Buffer.from([1, 2, 3, 4, 5, 6, 7, 8]);

console.log(buf.readDoubleBE(0));
// Prints: 8.20788039913184e-304
```

### `buf.readDoubleLE([offset])`

Added in: v0.11.15

**History**

| Version | Changes |
| --- | --- |
| v10.0.0 | Removed `noAssert` and no implicit coercion of the offset to `uint32` anymore. |

- `offset` [`<integer>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) Number of bytes to skip before starting to read. Must
  satisfy `0 <= offset <= buf.length - 8`. **Default:** `0`.
- Returns: [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type)

Reads a 64-bit, little-endian double from `buf` at the specified `offset`.

*ES module version:*

```js
import { Buffer } from 'node:buffer';

const buf = Buffer.from([1, 2, 3, 4, 5, 6, 7, 8]);

console.log(buf.readDoubleLE(0));
// Prints: 5.447603722011605e-270
console.log(buf.readDoubleLE(1));
// Throws ERR_OUT_OF_RANGE.
```

*CommonJS version:*

```js
const { Buffer } = require('node:buffer');

const buf = Buffer.from([1, 2, 3, 4, 5, 6, 7, 8]);

console.log(buf.readDoubleLE(0));
// Prints: 5.447603722011605e-270
console.log(buf.readDoubleLE(1));
// Throws ERR_OUT_OF_RANGE.
```

### `buf.readFloatBE([offset])`

Added in: v0.11.15

**History**

| Version | Changes |
| --- | --- |
| v10.0.0 | Removed `noAssert` and no implicit coercion of the offset to `uint32` anymore. |

- `offset` [`<integer>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) Number of bytes to skip before starting to read. Must
  satisfy `0 <= offset <= buf.length - 4`. **Default:** `0`.
- Returns: [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type)

Reads a 32-bit, big-endian float from `buf` at the specified `offset`.

*ES module version:*

```js
import { Buffer } from 'node:buffer';

const buf = Buffer.from([1, 2, 3, 4]);

console.log(buf.readFloatBE(0));
// Prints: 2.387939260590663e-38
```

*CommonJS version:*

```js
const { Buffer } = require('node:buffer');

const buf = Buffer.from([1, 2, 3, 4]);

console.log(buf.readFloatBE(0));
// Prints: 2.387939260590663e-38
```

### `buf.readFloatLE([offset])`

Added in: v0.11.15

**History**

| Version | Changes |
| --- | --- |
| v10.0.0 | Removed `noAssert` and no implicit coercion of the offset to `uint32` anymore. |

- `offset` [`<integer>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) Number of bytes to skip before starting to read. Must
  satisfy `0 <= offset <= buf.length - 4`. **Default:** `0`.
- Returns: [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type)

Reads a 32-bit, little-endian float from `buf` at the specified `offset`.

*ES module version:*

```js
import { Buffer } from 'node:buffer';

const buf = Buffer.from([1, 2, 3, 4]);

console.log(buf.readFloatLE(0));
// Prints: 1.539989614439558e-36
console.log(buf.readFloatLE(1));
// Throws ERR_OUT_OF_RANGE.
```

*CommonJS version:*

```js
const { Buffer } = require('node:buffer');

const buf = Buffer.from([1, 2, 3, 4]);

console.log(buf.readFloatLE(0));
// Prints: 1.539989614439558e-36
console.log(buf.readFloatLE(1));
// Throws ERR_OUT_OF_RANGE.
```

### `buf.readInt8([offset])`

Added in: v0.5.0

**History**

| Version | Changes |
| --- | --- |
| v10.0.0 | Removed `noAssert` and no implicit coercion of the offset to `uint32` anymore. |

- `offset` [`<integer>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) Number of bytes to skip before starting to read. Must
  satisfy `0 <= offset <= buf.length - 1`. **Default:** `0`.
- Returns: [`<integer>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type)

Reads a signed 8-bit integer from `buf` at the specified `offset`.

Integers read from a `Buffer` are interpreted as two's complement signed values.

*ES module version:*

```js
import { Buffer } from 'node:buffer';

const buf = Buffer.from([-1, 5]);

console.log(buf.readInt8(0));
// Prints: -1
console.log(buf.readInt8(1));
// Prints: 5
console.log(buf.readInt8(2));
// Throws ERR_OUT_OF_RANGE.
```

*CommonJS version:*

```js
const { Buffer } = require('node:buffer');

const buf = Buffer.from([-1, 5]);

console.log(buf.readInt8(0));
// Prints: -1
console.log(buf.readInt8(1));
// Prints: 5
console.log(buf.readInt8(2));
// Throws ERR_OUT_OF_RANGE.
```

### `buf.readInt16BE([offset])`

Added in: v0.5.5

**History**

| Version | Changes |
| --- | --- |
| v10.0.0 | Removed `noAssert` and no implicit coercion of the offset to `uint32` anymore. |

- `offset` [`<integer>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) Number of bytes to skip before starting to read. Must
  satisfy `0 <= offset <= buf.length - 2`. **Default:** `0`.
- Returns: [`<integer>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type)

Reads a signed, big-endian 16-bit integer from `buf` at the specified `offset`.

Integers read from a `Buffer` are interpreted as two's complement signed values.

*ES module version:*

```js
import { Buffer } from 'node:buffer';

const buf = Buffer.from([0, 5]);

console.log(buf.readInt16BE(0));
// Prints: 5
```

*CommonJS version:*

```js
const { Buffer } = require('node:buffer');

const buf = Buffer.from([0, 5]);

console.log(buf.readInt16BE(0));
// Prints: 5
```

### `buf.readInt16LE([offset])`

Added in: v0.5.5

**History**

| Version | Changes |
| --- | --- |
| v10.0.0 | Removed `noAssert` and no implicit coercion of the offset to `uint32` anymore. |

- `offset` [`<integer>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) Number of bytes to skip before starting to read. Must
  satisfy `0 <= offset <= buf.length - 2`. **Default:** `0`.
- Returns: [`<integer>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type)

Reads a signed, little-endian 16-bit integer from `buf` at the specified
`offset`.

Integers read from a `Buffer` are interpreted as two's complement signed values.

*ES module version:*

```js
import { Buffer } from 'node:buffer';

const buf = Buffer.from([0, 5]);

console.log(buf.readInt16LE(0));
// Prints: 1280
console.log(buf.readInt16LE(1));
// Throws ERR_OUT_OF_RANGE.
```

*CommonJS version:*

```js
const { Buffer } = require('node:buffer');

const buf = Buffer.from([0, 5]);

console.log(buf.readInt16LE(0));
// Prints: 1280
console.log(buf.readInt16LE(1));
// Throws ERR_OUT_OF_RANGE.
```

### `buf.readInt32BE([offset])`

Added in: v0.5.5

**History**

| Version | Changes |
| --- | --- |
| v10.0.0 | Removed `noAssert` and no implicit coercion of the offset to `uint32` anymore. |

- `offset` [`<integer>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) Number of bytes to skip before starting to read. Must
  satisfy `0 <= offset <= buf.length - 4`. **Default:** `0`.
- Returns: [`<integer>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type)

Reads a signed, big-endian 32-bit integer from `buf` at the specified `offset`.

Integers read from a `Buffer` are interpreted as two's complement signed values.

*ES module version:*

```js
import { Buffer } from 'node:buffer';

const buf = Buffer.from([0, 0, 0, 5]);

console.log(buf.readInt32BE(0));
// Prints: 5
```

*CommonJS version:*

```js
const { Buffer } = require('node:buffer');

const buf = Buffer.from([0, 0, 0, 5]);

console.log(buf.readInt32BE(0));
// Prints: 5
```

### `buf.readInt32LE([offset])`

Added in: v0.5.5

**History**

| Version | Changes |
| --- | --- |
| v10.0.0 | Removed `noAssert` and no implicit coercion of the offset to `uint32` anymore. |

- `offset` [`<integer>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) Number of bytes to skip before starting to read. Must
  satisfy `0 <= offset <= buf.length - 4`. **Default:** `0`.
- Returns: [`<integer>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type)

Reads a signed, little-endian 32-bit integer from `buf` at the specified
`offset`.

Integers read from a `Buffer` are interpreted as two's complement signed values.

*ES module version:*

```js
import { Buffer } from 'node:buffer';

const buf = Buffer.from([0, 0, 0, 5]);

console.log(buf.readInt32LE(0));
// Prints: 83886080
console.log(buf.readInt32LE(1));
// Throws ERR_OUT_OF_RANGE.
```

*CommonJS version:*

```js
const { Buffer } = require('node:buffer');

const buf = Buffer.from([0, 0, 0, 5]);

console.log(buf.readInt32LE(0));
// Prints: 83886080
console.log(buf.readInt32LE(1));
// Throws ERR_OUT_OF_RANGE.
```

### `buf.readIntBE(offset, byteLength)`

Added in: v0.11.15

**History**

| Version | Changes |
| --- | --- |
| v25.5.0, v24.13.1 | supports Uint8Array as `this` value. |
| v10.0.0 | Removed `noAssert` and no implicit coercion of the offset and `byteLength` to `uint32` anymore. |

- `offset` [`<integer>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) Number of bytes to skip before starting to read. Must
  satisfy `0 <= offset <= buf.length - byteLength`.
- `byteLength` [`<integer>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) Number of bytes to read. Must satisfy `0 < byteLength <= 6`.
- Returns: [`<integer>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type)

Reads `byteLength` number of bytes from `buf` at the specified `offset`
and interprets the result as a big-endian, two's complement signed value
supporting up to 48 bits of accuracy.

*ES module version:*

```js
import { Buffer } from 'node:buffer';

const buf = Buffer.from([0x12, 0x34, 0x56, 0x78, 0x90, 0xab]);

console.log(buf.readIntBE(0, 6).toString(16));
// Prints: 1234567890ab
console.log(buf.readIntBE(1, 6).toString(16));
// Throws ERR_OUT_OF_RANGE.
console.log(buf.readIntBE(1, 0).toString(16));
// Throws ERR_OUT_OF_RANGE.
```

*CommonJS version:*

```js
const { Buffer } = require('node:buffer');

const buf = Buffer.from([0x12, 0x34, 0x56, 0x78, 0x90, 0xab]);

console.log(buf.readIntBE(0, 6).toString(16));
// Prints: 1234567890ab
console.log(buf.readIntBE(1, 6).toString(16));
// Throws ERR_OUT_OF_RANGE.
console.log(buf.readIntBE(1, 0).toString(16));
// Throws ERR_OUT_OF_RANGE.
```

### `buf.readIntLE(offset, byteLength)`

Added in: v0.11.15

**History**

| Version | Changes |
| --- | --- |
| v25.5.0, v24.13.1 | supports Uint8Array as `this` value. |
| v10.0.0 | Removed `noAssert` and no implicit coercion of the offset and `byteLength` to `uint32` anymore. |

- `offset` [`<integer>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) Number of bytes to skip before starting to read. Must
  satisfy `0 <= offset <= buf.length - byteLength`.
- `byteLength` [`<integer>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) Number of bytes to read. Must satisfy `0 < byteLength <= 6`.
- Returns: [`<integer>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type)

Reads `byteLength` number of bytes from `buf` at the specified `offset`
and interprets the result as a little-endian, two's complement signed value
supporting up to 48 bits of accuracy.

*ES module version:*

```js
import { Buffer } from 'node:buffer';

const buf = Buffer.from([0x12, 0x34, 0x56, 0x78, 0x90, 0xab]);

console.log(buf.readIntLE(0, 6).toString(16));
// Prints: -546f87a9cbee
```

*CommonJS version:*

```js
const { Buffer } = require('node:buffer');

const buf = Buffer.from([0x12, 0x34, 0x56, 0x78, 0x90, 0xab]);

console.log(buf.readIntLE(0, 6).toString(16));
// Prints: -546f87a9cbee
```

### `buf.readUInt8([offset])`

Added in: v0.5.0

**History**

| Version | Changes |
| --- | --- |
| v14.9.0, v12.19.0 | This function is also available as `buf.readUint8()`. |
| v10.0.0 | Removed `noAssert` and no implicit coercion of the offset to `uint32` anymore. |

- `offset` [`<integer>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) Number of bytes to skip before starting to read. Must
  satisfy `0 <= offset <= buf.length - 1`. **Default:** `0`.
- Returns: [`<integer>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type)

Reads an unsigned 8-bit integer from `buf` at the specified `offset`.

This function is also available under the `readUint8` alias.

*ES module version:*

```js
import { Buffer } from 'node:buffer';

const buf = Buffer.from([1, -2]);

console.log(buf.readUInt8(0));
// Prints: 1
console.log(buf.readUInt8(1));
// Prints: 254
console.log(buf.readUInt8(2));
// Throws ERR_OUT_OF_RANGE.
```

*CommonJS version:*

```js
const { Buffer } = require('node:buffer');

const buf = Buffer.from([1, -2]);

console.log(buf.readUInt8(0));
// Prints: 1
console.log(buf.readUInt8(1));
// Prints: 254
console.log(buf.readUInt8(2));
// Throws ERR_OUT_OF_RANGE.
```

### `buf.readUInt16BE([offset])`

Added in: v0.5.5

**History**

| Version | Changes |
| --- | --- |
| v14.9.0, v12.19.0 | This function is also available as `buf.readUint16BE()`. |
| v10.0.0 | Removed `noAssert` and no implicit coercion of the offset to `uint32` anymore. |

- `offset` [`<integer>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) Number of bytes to skip before starting to read. Must
  satisfy `0 <= offset <= buf.length - 2`. **Default:** `0`.
- Returns: [`<integer>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type)

Reads an unsigned, big-endian 16-bit integer from `buf` at the specified
`offset`.

This function is also available under the `readUint16BE` alias.

*ES module version:*

```js
import { Buffer } from 'node:buffer';

const buf = Buffer.from([0x12, 0x34, 0x56]);

console.log(buf.readUInt16BE(0).toString(16));
// Prints: 1234
console.log(buf.readUInt16BE(1).toString(16));
// Prints: 3456
```

*CommonJS version:*

```js
const { Buffer } = require('node:buffer');

const buf = Buffer.from([0x12, 0x34, 0x56]);

console.log(buf.readUInt16BE(0).toString(16));
// Prints: 1234
console.log(buf.readUInt16BE(1).toString(16));
// Prints: 3456
```

### `buf.readUInt16LE([offset])`

Added in: v0.5.5

**History**

| Version | Changes |
| --- | --- |
| v14.9.0, v12.19.0 | This function is also available as `buf.readUint16LE()`. |
| v10.0.0 | Removed `noAssert` and no implicit coercion of the offset to `uint32` anymore. |

- `offset` [`<integer>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) Number of bytes to skip before starting to read. Must
  satisfy `0 <= offset <= buf.length - 2`. **Default:** `0`.
- Returns: [`<integer>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type)

Reads an unsigned, little-endian 16-bit integer from `buf` at the specified
`offset`.

This function is also available under the `readUint16LE` alias.

*ES module version:*

```js
import { Buffer } from 'node:buffer';

const buf = Buffer.from([0x12, 0x34, 0x56]);

console.log(buf.readUInt16LE(0).toString(16));
// Prints: 3412
console.log(buf.readUInt16LE(1).toString(16));
// Prints: 5634
console.log(buf.readUInt16LE(2).toString(16));
// Throws ERR_OUT_OF_RANGE.
```

*CommonJS version:*

```js
const { Buffer } = require('node:buffer');

const buf = Buffer.from([0x12, 0x34, 0x56]);

console.log(buf.readUInt16LE(0).toString(16));
// Prints: 3412
console.log(buf.readUInt16LE(1).toString(16));
// Prints: 5634
console.log(buf.readUInt16LE(2).toString(16));
// Throws ERR_OUT_OF_RANGE.
```

### `buf.readUInt32BE([offset])`

Added in: v0.5.5

**History**

| Version | Changes |
| --- | --- |
| v14.9.0, v12.19.0 | This function is also available as `buf.readUint32BE()`. |
| v10.0.0 | Removed `noAssert` and no implicit coercion of the offset to `uint32` anymore. |

- `offset` [`<integer>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) Number of bytes to skip before starting to read. Must
  satisfy `0 <= offset <= buf.length - 4`. **Default:** `0`.
- Returns: [`<integer>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type)

Reads an unsigned, big-endian 32-bit integer from `buf` at the specified
`offset`.

This function is also available under the `readUint32BE` alias.

*ES module version:*

```js
import { Buffer } from 'node:buffer';

const buf = Buffer.from([0x12, 0x34, 0x56, 0x78]);

console.log(buf.readUInt32BE(0).toString(16));
// Prints: 12345678
```

*CommonJS version:*

```js
const { Buffer } = require('node:buffer');

const buf = Buffer.from([0x12, 0x34, 0x56, 0x78]);

console.log(buf.readUInt32BE(0).toString(16));
// Prints: 12345678
```

### `buf.readUInt32LE([offset])`

Added in: v0.5.5

**History**

| Version | Changes |
| --- | --- |
| v14.9.0, v12.19.0 | This function is also available as `buf.readUint32LE()`. |
| v10.0.0 | Removed `noAssert` and no implicit coercion of the offset to `uint32` anymore. |

- `offset` [`<integer>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) Number of bytes to skip before starting to read. Must
  satisfy `0 <= offset <= buf.length - 4`. **Default:** `0`.
- Returns: [`<integer>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type)

Reads an unsigned, little-endian 32-bit integer from `buf` at the specified
`offset`.

This function is also available under the `readUint32LE` alias.

*ES module version:*

```js
import { Buffer } from 'node:buffer';

const buf = Buffer.from([0x12, 0x34, 0x56, 0x78]);

console.log(buf.readUInt32LE(0).toString(16));
// Prints: 78563412
console.log(buf.readUInt32LE(1).toString(16));
// Throws ERR_OUT_OF_RANGE.
```

*CommonJS version:*

```js
const { Buffer } = require('node:buffer');

const buf = Buffer.from([0x12, 0x34, 0x56, 0x78]);

console.log(buf.readUInt32LE(0).toString(16));
// Prints: 78563412
console.log(buf.readUInt32LE(1).toString(16));
// Throws ERR_OUT_OF_RANGE.
```

### `buf.readUIntBE(offset, byteLength)`

Added in: v0.11.15

**History**

| Version | Changes |
| --- | --- |
| v25.5.0, v24.13.1 | supports Uint8Array as `this` value. |
| v14.9.0, v12.19.0 | This function is also available as `buf.readUintBE()`. |
| v10.0.0 | Removed `noAssert` and no implicit coercion of the offset and `byteLength` to `uint32` anymore. |

- `offset` [`<integer>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) Number of bytes to skip before starting to read. Must
  satisfy `0 <= offset <= buf.length - byteLength`.
- `byteLength` [`<integer>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) Number of bytes to read. Must satisfy `0 < byteLength <= 6`.
- Returns: [`<integer>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type)

Reads `byteLength` number of bytes from `buf` at the specified `offset`
and interprets the result as an unsigned big-endian integer supporting
up to 48 bits of accuracy.

This function is also available under the `readUintBE` alias.

*ES module version:*

```js
import { Buffer } from 'node:buffer';

const buf = Buffer.from([0x12, 0x34, 0x56, 0x78, 0x90, 0xab]);

console.log(buf.readUIntBE(0, 6).toString(16));
// Prints: 1234567890ab
console.log(buf.readUIntBE(1, 6).toString(16));
// Throws ERR_OUT_OF_RANGE.
```

*CommonJS version:*

```js
const { Buffer } = require('node:buffer');

const buf = Buffer.from([0x12, 0x34, 0x56, 0x78, 0x90, 0xab]);

console.log(buf.readUIntBE(0, 6).toString(16));
// Prints: 1234567890ab
console.log(buf.readUIntBE(1, 6).toString(16));
// Throws ERR_OUT_OF_RANGE.
```

### `buf.readUIntLE(offset, byteLength)`

Added in: v0.11.15

**History**

| Version | Changes |
| --- | --- |
| v25.5.0, v24.13.1 | supports Uint8Array as `this` value. |
| v14.9.0, v12.19.0 | This function is also available as `buf.readUintLE()`. |
| v10.0.0 | Removed `noAssert` and no implicit coercion of the offset and `byteLength` to `uint32` anymore. |

- `offset` [`<integer>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) Number of bytes to skip before starting to read. Must
  satisfy `0 <= offset <= buf.length - byteLength`.
- `byteLength` [`<integer>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) Number of bytes to read. Must satisfy `0 < byteLength <= 6`.
- Returns: [`<integer>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type)

Reads `byteLength` number of bytes from `buf` at the specified `offset`
and interprets the result as an unsigned, little-endian integer supporting
up to 48 bits of accuracy.

This function is also available under the `readUintLE` alias.

*ES module version:*

```js
import { Buffer } from 'node:buffer';

const buf = Buffer.from([0x12, 0x34, 0x56, 0x78, 0x90, 0xab]);

console.log(buf.readUIntLE(0, 6).toString(16));
// Prints: ab9078563412
```

*CommonJS version:*

```js
const { Buffer } = require('node:buffer');

const buf = Buffer.from([0x12, 0x34, 0x56, 0x78, 0x90, 0xab]);

console.log(buf.readUIntLE(0, 6).toString(16));
// Prints: ab9078563412
```

### `buf.subarray([start[, end]])`

Added in: v3.0.0

- `start` [`<integer>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) Where the new `Buffer` will start. **Default:** `0`.
- `end` [`<integer>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) Where the new `Buffer` will end (not inclusive).
  **Default:** [`buf.length`](#buflength).
- Returns: [`<Buffer>`](07-buffer-part1.md#class-buffer)

Returns a new `Buffer` that references the same memory as the original, but
offset and cropped by the `start` and `end` indexes.

Specifying `end` greater than [`buf.length`](#buflength) will return the same result as
that of `end` equal to [`buf.length`](#buflength).

This method is inherited from [`TypedArray.prototype.subarray()`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/TypedArray/subarray).

Modifying the new `Buffer` slice will modify the memory in the original `Buffer`
because the allocated memory of the two objects overlap.

*ES module version:*

```js
import { Buffer } from 'node:buffer';

// Create a `Buffer` with the ASCII alphabet, take a slice, and modify one byte
// from the original `Buffer`.

const buf1 = Buffer.allocUnsafe(26);

for (let i = 0; i < 26; i++) {
  // 97 is the decimal ASCII value for 'a'.
  buf1[i] = i + 97;
}

const buf2 = buf1.subarray(0, 3);

console.log(buf2.toString('ascii', 0, buf2.length));
// Prints: abc

buf1[0] = 33;

console.log(buf2.toString('ascii', 0, buf2.length));
// Prints: !bc
```

*CommonJS version:*

```js
const { Buffer } = require('node:buffer');

// Create a `Buffer` with the ASCII alphabet, take a slice, and modify one byte
// from the original `Buffer`.

const buf1 = Buffer.allocUnsafe(26);

for (let i = 0; i < 26; i++) {
  // 97 is the decimal ASCII value for 'a'.
  buf1[i] = i + 97;
}

const buf2 = buf1.subarray(0, 3);

console.log(buf2.toString('ascii', 0, buf2.length));
// Prints: abc

buf1[0] = 33;

console.log(buf2.toString('ascii', 0, buf2.length));
// Prints: !bc
```

Specifying negative indexes causes the slice to be generated relative to the
end of `buf` rather than the beginning.

*ES module version:*

```js
import { Buffer } from 'node:buffer';

const buf = Buffer.from('buffer');

console.log(buf.subarray(-6, -1).toString());
// Prints: buffe
// (Equivalent to buf.subarray(0, 5).)

console.log(buf.subarray(-6, -2).toString());
// Prints: buff
// (Equivalent to buf.subarray(0, 4).)

console.log(buf.subarray(-5, -2).toString());
// Prints: uff
// (Equivalent to buf.subarray(1, 4).)
```

*CommonJS version:*

```js
const { Buffer } = require('node:buffer');

const buf = Buffer.from('buffer');

console.log(buf.subarray(-6, -1).toString());
// Prints: buffe
// (Equivalent to buf.subarray(0, 5).)

console.log(buf.subarray(-6, -2).toString());
// Prints: buff
// (Equivalent to buf.subarray(0, 4).)

console.log(buf.subarray(-5, -2).toString());
// Prints: uff
// (Equivalent to buf.subarray(1, 4).)
```

### `buf.slice([start[, end]])`

Added in: v0.3.0

**History**

| Version | Changes |
| --- | --- |
| v17.5.0, v16.15.0 | The buf.slice() method has been deprecated. |
| v7.1.0, v6.9.2 | Coercing the offsets to integers now handles values outside the 32-bit integer range properly. |
| v7.0.0 | All offsets are now coerced to integers before doing any calculations with them. |

Stability: 0 - Deprecated: Use [`buf.subarray`](#bufsubarraystart-end) instead.

- `start` [`<integer>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) Where the new `Buffer` will start. **Default:** `0`.
- `end` [`<integer>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) Where the new `Buffer` will end (not inclusive).
  **Default:** [`buf.length`](#buflength).
- Returns: [`<Buffer>`](07-buffer-part1.md#class-buffer)

Returns a new `Buffer` that references the same memory as the original, but
offset and cropped by the `start` and `end` indexes.

This method is not compatible with the `Uint8Array.prototype.slice()`,
which is a superclass of `Buffer`. To copy the slice, use
`Uint8Array.prototype.slice()`.

*ES module version:*

```js
import { Buffer } from 'node:buffer';

const buf = Buffer.from('buffer');

const copiedBuf = Uint8Array.prototype.slice.call(buf);
copiedBuf[0]++;
console.log(copiedBuf.toString());
// Prints: cuffer

console.log(buf.toString());
// Prints: buffer

// With buf.slice(), the original buffer is modified.
const notReallyCopiedBuf = buf.slice();
notReallyCopiedBuf[0]++;
console.log(notReallyCopiedBuf.toString());
// Prints: cuffer
console.log(buf.toString());
// Also prints: cuffer (!)
```

*CommonJS version:*

```js
const { Buffer } = require('node:buffer');

const buf = Buffer.from('buffer');

const copiedBuf = Uint8Array.prototype.slice.call(buf);
copiedBuf[0]++;
console.log(copiedBuf.toString());
// Prints: cuffer

console.log(buf.toString());
// Prints: buffer

// With buf.slice(), the original buffer is modified.
const notReallyCopiedBuf = buf.slice();
notReallyCopiedBuf[0]++;
console.log(notReallyCopiedBuf.toString());
// Prints: cuffer
console.log(buf.toString());
// Also prints: cuffer (!)
```

### `buf.swap16()`

Added in: v5.10.0

- Returns: [`<Buffer>`](07-buffer-part1.md#class-buffer) A reference to `buf`.

Interprets `buf` as an array of unsigned 16-bit integers and swaps the
byte order *in-place*. Throws [`ERR_INVALID_BUFFER_SIZE`](22-errors-part2.md#err_invalid_buffer_size) if [`buf.length`](#buflength)
is not a multiple of 2.

*ES module version:*

```js
import { Buffer } from 'node:buffer';

const buf1 = Buffer.from([0x1, 0x2, 0x3, 0x4, 0x5, 0x6, 0x7, 0x8]);

console.log(buf1);
// Prints: <Buffer 01 02 03 04 05 06 07 08>

buf1.swap16();

console.log(buf1);
// Prints: <Buffer 02 01 04 03 06 05 08 07>

const buf2 = Buffer.from([0x1, 0x2, 0x3]);

buf2.swap16();
// Throws ERR_INVALID_BUFFER_SIZE.
```

*CommonJS version:*

```js
const { Buffer } = require('node:buffer');

const buf1 = Buffer.from([0x1, 0x2, 0x3, 0x4, 0x5, 0x6, 0x7, 0x8]);

console.log(buf1);
// Prints: <Buffer 01 02 03 04 05 06 07 08>

buf1.swap16();

console.log(buf1);
// Prints: <Buffer 02 01 04 03 06 05 08 07>

const buf2 = Buffer.from([0x1, 0x2, 0x3]);

buf2.swap16();
// Throws ERR_INVALID_BUFFER_SIZE.
```

One convenient use of `buf.swap16()` is to perform a fast in-place conversion
between UTF-16 little-endian and UTF-16 big-endian:

*ES module version:*

```js
import { Buffer } from 'node:buffer';

const buf = Buffer.from('This is little-endian UTF-16', 'utf16le');
buf.swap16(); // Convert to big-endian UTF-16 text.
```

*CommonJS version:*

```js
const { Buffer } = require('node:buffer');

const buf = Buffer.from('This is little-endian UTF-16', 'utf16le');
buf.swap16(); // Convert to big-endian UTF-16 text.
```

[Contents](../README.md) | [Previous: Buffer, part 1 of 4](07-buffer-part1.md) | [Next: Buffer, part 3 of 4](07-buffer-part3.md)
