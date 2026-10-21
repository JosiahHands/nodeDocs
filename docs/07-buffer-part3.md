[Contents](../README.md) | [Previous: Buffer, part 2 of 4](07-buffer-part2.md) | [Next: Buffer, part 4 of 4](07-buffer-part4.md)

Source: [https://nodejs.org/docs/latest/api/buffer.html](https://nodejs.org/docs/latest/api/buffer.html)

Part 3 of 4 of this section.

# Buffer (part 3 of 4)

Continues: Class: Buffer.

### `buf.swap32()`

Added in: v5.10.0

- Returns: [`<Buffer>`](07-buffer-part1.md#class-buffer) A reference to `buf`.

Interprets `buf` as an array of unsigned 32-bit integers and swaps the
byte order *in-place*. Throws [`ERR_INVALID_BUFFER_SIZE`](22-errors-part2.md#err_invalid_buffer_size) if [`buf.length`](07-buffer-part2.md#buflength)
is not a multiple of 4.

*ES module version:*

```js
import { Buffer } from 'node:buffer';

const buf1 = Buffer.from([0x1, 0x2, 0x3, 0x4, 0x5, 0x6, 0x7, 0x8]);

console.log(buf1);
// Prints: <Buffer 01 02 03 04 05 06 07 08>

buf1.swap32();

console.log(buf1);
// Prints: <Buffer 04 03 02 01 08 07 06 05>

const buf2 = Buffer.from([0x1, 0x2, 0x3]);

buf2.swap32();
// Throws ERR_INVALID_BUFFER_SIZE.
```

*CommonJS version:*

```js
const { Buffer } = require('node:buffer');

const buf1 = Buffer.from([0x1, 0x2, 0x3, 0x4, 0x5, 0x6, 0x7, 0x8]);

console.log(buf1);
// Prints: <Buffer 01 02 03 04 05 06 07 08>

buf1.swap32();

console.log(buf1);
// Prints: <Buffer 04 03 02 01 08 07 06 05>

const buf2 = Buffer.from([0x1, 0x2, 0x3]);

buf2.swap32();
// Throws ERR_INVALID_BUFFER_SIZE.
```

### `buf.swap64()`

Added in: v6.3.0

- Returns: [`<Buffer>`](07-buffer-part1.md#class-buffer) A reference to `buf`.

Interprets `buf` as an array of 64-bit numbers and swaps byte order *in-place*.
Throws [`ERR_INVALID_BUFFER_SIZE`](22-errors-part2.md#err_invalid_buffer_size) if [`buf.length`](07-buffer-part2.md#buflength) is not a multiple of 8.

*ES module version:*

```js
import { Buffer } from 'node:buffer';

const buf1 = Buffer.from([0x1, 0x2, 0x3, 0x4, 0x5, 0x6, 0x7, 0x8]);

console.log(buf1);
// Prints: <Buffer 01 02 03 04 05 06 07 08>

buf1.swap64();

console.log(buf1);
// Prints: <Buffer 08 07 06 05 04 03 02 01>

const buf2 = Buffer.from([0x1, 0x2, 0x3]);

buf2.swap64();
// Throws ERR_INVALID_BUFFER_SIZE.
```

*CommonJS version:*

```js
const { Buffer } = require('node:buffer');

const buf1 = Buffer.from([0x1, 0x2, 0x3, 0x4, 0x5, 0x6, 0x7, 0x8]);

console.log(buf1);
// Prints: <Buffer 01 02 03 04 05 06 07 08>

buf1.swap64();

console.log(buf1);
// Prints: <Buffer 08 07 06 05 04 03 02 01>

const buf2 = Buffer.from([0x1, 0x2, 0x3]);

buf2.swap64();
// Throws ERR_INVALID_BUFFER_SIZE.
```

### `buf.toJSON()`

Added in: v0.9.2

- Returns: [`<Object>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Object)

Returns a JSON representation of `buf`. [`JSON.stringify()`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/JSON/stringify) implicitly calls
this function when stringifying a `Buffer` instance.

`Buffer.from()` accepts objects in the format returned from this method.
In particular, `Buffer.from(buf.toJSON())` works like `Buffer.from(buf)`.

*ES module version:*

```js
import { Buffer } from 'node:buffer';

const buf = Buffer.from([0x1, 0x2, 0x3, 0x4, 0x5]);
const json = JSON.stringify(buf);

console.log(json);
// Prints: {"type":"Buffer","data":[1,2,3,4,5]}

const copy = JSON.parse(json, (key, value) => {
  return value && value.type === 'Buffer' ?
    Buffer.from(value) :
    value;
});

console.log(copy);
// Prints: <Buffer 01 02 03 04 05>
```

*CommonJS version:*

```js
const { Buffer } = require('node:buffer');

const buf = Buffer.from([0x1, 0x2, 0x3, 0x4, 0x5]);
const json = JSON.stringify(buf);

console.log(json);
// Prints: {"type":"Buffer","data":[1,2,3,4,5]}

const copy = JSON.parse(json, (key, value) => {
  return value && value.type === 'Buffer' ?
    Buffer.from(value) :
    value;
});

console.log(copy);
// Prints: <Buffer 01 02 03 04 05>
```

### `buf.toString([encoding[, start[, end]]])`

Added in: v0.1.90

**History**

| Version | Changes |
| --- | --- |
| v25.5.0, v24.13.1 | supports Uint8Array as `this` value. |

- `encoding` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) The character encoding to use. **Default:** `'utf8'`.
- `start` [`<integer>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) The byte offset to start decoding at. **Default:** `0`.
- `end` [`<integer>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) The byte offset to stop decoding at (not inclusive). **Default:** [`buf.length`](07-buffer-part2.md#buflength).
- Returns: [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type)

Decodes `buf` to a string according to the specified character encoding in
`encoding`. `start` and `end` may be passed to decode only a subset of `buf`.

If `encoding` is `'utf8'` and a byte sequence in the input is not valid UTF-8,
then each invalid byte is replaced with the replacement character `U+FFFD`.

The maximum length of a string instance (in UTF-16 code units) is available
as [`buffer.constants.MAX_STRING_LENGTH`](#bufferconstantsmax_string_length).

*ES module version:*

```js
import { Buffer } from 'node:buffer';

const buf1 = Buffer.allocUnsafe(26);

for (let i = 0; i < 26; i++) {
  // 97 is the decimal ASCII value for 'a'.
  buf1[i] = i + 97;
}

console.log(buf1.toString('utf8'));
// Prints: abcdefghijklmnopqrstuvwxyz
console.log(buf1.toString('utf8', 0, 5));
// Prints: abcde

const buf2 = Buffer.from('tést');

console.log(buf2.toString('hex'));
// Prints: 74c3a97374
console.log(buf2.toString('utf8', 0, 3));
// Prints: té
console.log(buf2.toString(undefined, 0, 3));
// Prints: té
```

*CommonJS version:*

```js
const { Buffer } = require('node:buffer');

const buf1 = Buffer.allocUnsafe(26);

for (let i = 0; i < 26; i++) {
  // 97 is the decimal ASCII value for 'a'.
  buf1[i] = i + 97;
}

console.log(buf1.toString('utf8'));
// Prints: abcdefghijklmnopqrstuvwxyz
console.log(buf1.toString('utf8', 0, 5));
// Prints: abcde

const buf2 = Buffer.from('tést');

console.log(buf2.toString('hex'));
// Prints: 74c3a97374
console.log(buf2.toString('utf8', 0, 3));
// Prints: té
console.log(buf2.toString(undefined, 0, 3));
// Prints: té
```

### `buf.values()`

Added in: v1.1.0

- Returns: [`<Iterator>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Iterator)

Creates and returns an [iterator](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Iteration_protocols) for `buf` values (bytes). This function is
called automatically when a `Buffer` is used in a `for..of` statement.

*ES module version:*

```js
import { Buffer } from 'node:buffer';

const buf = Buffer.from('buffer');

for (const value of buf.values()) {
  console.log(value);
}
// Prints:
//   98
//   117
//   102
//   102
//   101
//   114

for (const value of buf) {
  console.log(value);
}
// Prints:
//   98
//   117
//   102
//   102
//   101
//   114
```

*CommonJS version:*

```js
const { Buffer } = require('node:buffer');

const buf = Buffer.from('buffer');

for (const value of buf.values()) {
  console.log(value);
}
// Prints:
//   98
//   117
//   102
//   102
//   101
//   114

for (const value of buf) {
  console.log(value);
}
// Prints:
//   98
//   117
//   102
//   102
//   101
//   114
```

### `buf.write(string[, offset[, length]][, encoding])`

Added in: v0.1.90

**History**

| Version | Changes |
| --- | --- |
| v25.5.0, v24.13.1 | supports Uint8Array as `this` value. |

- `string` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) String to write to `buf`.
- `offset` [`<integer>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) Number of bytes to skip before starting to write `string`.
  **Default:** `0`.
- `length` [`<integer>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) Maximum number of bytes to write (written bytes will not
  exceed `buf.length - offset`). **Default:** `buf.length - offset`.
- `encoding` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) The character encoding of `string`. **Default:** `'utf8'`.
- Returns: [`<integer>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) Number of bytes written.

Writes `string` to `buf` at `offset` according to the character encoding in
`encoding`. The `length` parameter is the number of bytes to write. If `buf` did
not contain enough space to fit the entire string, only part of `string` will be
written. However, partially encoded characters will not be written.

*ES module version:*

```js
import { Buffer } from 'node:buffer';

const buf = Buffer.alloc(256);

const len = buf.write('\u00bd + \u00bc = \u00be', 0);

console.log(`${len} bytes: ${buf.toString('utf8', 0, len)}`);
// Prints: 12 bytes: ½ + ¼ = ¾

const buffer = Buffer.alloc(10);

const length = buffer.write('abcd', 8);

console.log(`${length} bytes: ${buffer.toString('utf8', 8, 10)}`);
// Prints: 2 bytes : ab
```

*CommonJS version:*

```js
const { Buffer } = require('node:buffer');

const buf = Buffer.alloc(256);

const len = buf.write('\u00bd + \u00bc = \u00be', 0);

console.log(`${len} bytes: ${buf.toString('utf8', 0, len)}`);
// Prints: 12 bytes: ½ + ¼ = ¾

const buffer = Buffer.alloc(10);

const length = buffer.write('abcd', 8);

console.log(`${length} bytes: ${buffer.toString('utf8', 8, 10)}`);
// Prints: 2 bytes : ab
```

### `buf.writeBigInt64BE(value[, offset])`

Added in: v12.0.0, v10.20.0

- `value` [`<bigint>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#bigint_type) Number to be written to `buf`.
- `offset` [`<integer>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) Number of bytes to skip before starting to write. Must
  satisfy: `0 <= offset <= buf.length - 8`. **Default:** `0`.
- Returns: [`<integer>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) `offset` plus the number of bytes written.

Writes `value` to `buf` at the specified `offset` as big-endian.

`value` is interpreted and written as a two's complement signed integer.

*ES module version:*

```js
import { Buffer } from 'node:buffer';

const buf = Buffer.allocUnsafe(8);

buf.writeBigInt64BE(0x0102030405060708n, 0);

console.log(buf);
// Prints: <Buffer 01 02 03 04 05 06 07 08>
```

*CommonJS version:*

```js
const { Buffer } = require('node:buffer');

const buf = Buffer.allocUnsafe(8);

buf.writeBigInt64BE(0x0102030405060708n, 0);

console.log(buf);
// Prints: <Buffer 01 02 03 04 05 06 07 08>
```

### `buf.writeBigInt64LE(value[, offset])`

Added in: v12.0.0, v10.20.0

- `value` [`<bigint>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#bigint_type) Number to be written to `buf`.
- `offset` [`<integer>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) Number of bytes to skip before starting to write. Must
  satisfy: `0 <= offset <= buf.length - 8`. **Default:** `0`.
- Returns: [`<integer>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) `offset` plus the number of bytes written.

Writes `value` to `buf` at the specified `offset` as little-endian.

`value` is interpreted and written as a two's complement signed integer.

*ES module version:*

```js
import { Buffer } from 'node:buffer';

const buf = Buffer.allocUnsafe(8);

buf.writeBigInt64LE(0x0102030405060708n, 0);

console.log(buf);
// Prints: <Buffer 08 07 06 05 04 03 02 01>
```

*CommonJS version:*

```js
const { Buffer } = require('node:buffer');

const buf = Buffer.allocUnsafe(8);

buf.writeBigInt64LE(0x0102030405060708n, 0);

console.log(buf);
// Prints: <Buffer 08 07 06 05 04 03 02 01>
```

### `buf.writeBigUInt64BE(value[, offset])`

Added in: v12.0.0, v10.20.0

**History**

| Version | Changes |
| --- | --- |
| v14.10.0, v12.19.0 | This function is also available as `buf.writeBigUint64BE()`. |

- `value` [`<bigint>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#bigint_type) Number to be written to `buf`.
- `offset` [`<integer>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) Number of bytes to skip before starting to write. Must
  satisfy: `0 <= offset <= buf.length - 8`. **Default:** `0`.
- Returns: [`<integer>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) `offset` plus the number of bytes written.

Writes `value` to `buf` at the specified `offset` as big-endian.

This function is also available under the `writeBigUint64BE` alias.

*ES module version:*

```js
import { Buffer } from 'node:buffer';

const buf = Buffer.allocUnsafe(8);

buf.writeBigUInt64BE(0xdecafafecacefaden, 0);

console.log(buf);
// Prints: <Buffer de ca fa fe ca ce fa de>
```

*CommonJS version:*

```js
const { Buffer } = require('node:buffer');

const buf = Buffer.allocUnsafe(8);

buf.writeBigUInt64BE(0xdecafafecacefaden, 0);

console.log(buf);
// Prints: <Buffer de ca fa fe ca ce fa de>
```

### `buf.writeBigUInt64LE(value[, offset])`

Added in: v12.0.0, v10.20.0

**History**

| Version | Changes |
| --- | --- |
| v14.10.0, v12.19.0 | This function is also available as `buf.writeBigUint64LE()`. |

- `value` [`<bigint>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#bigint_type) Number to be written to `buf`.
- `offset` [`<integer>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) Number of bytes to skip before starting to write. Must
  satisfy: `0 <= offset <= buf.length - 8`. **Default:** `0`.
- Returns: [`<integer>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) `offset` plus the number of bytes written.

Writes `value` to `buf` at the specified `offset` as little-endian

*ES module version:*

```js
import { Buffer } from 'node:buffer';

const buf = Buffer.allocUnsafe(8);

buf.writeBigUInt64LE(0xdecafafecacefaden, 0);

console.log(buf);
// Prints: <Buffer de fa ce ca fe fa ca de>
```

*CommonJS version:*

```js
const { Buffer } = require('node:buffer');

const buf = Buffer.allocUnsafe(8);

buf.writeBigUInt64LE(0xdecafafecacefaden, 0);

console.log(buf);
// Prints: <Buffer de fa ce ca fe fa ca de>
```

This function is also available under the `writeBigUint64LE` alias.

### `buf.writeDoubleBE(value[, offset])`

Added in: v0.11.15

**History**

| Version | Changes |
| --- | --- |
| v10.0.0 | Removed `noAssert` and no implicit coercion of the offset to `uint32` anymore. |

- `value` [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) Number to be written to `buf`.
- `offset` [`<integer>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) Number of bytes to skip before starting to write. Must
  satisfy `0 <= offset <= buf.length - 8`. **Default:** `0`.
- Returns: [`<integer>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) `offset` plus the number of bytes written.

Writes `value` to `buf` at the specified `offset` as big-endian. The `value`
must be a JavaScript number. Behavior is undefined when `value` is anything
other than a JavaScript number.

*ES module version:*

```js
import { Buffer } from 'node:buffer';

const buf = Buffer.allocUnsafe(8);

buf.writeDoubleBE(123.456, 0);

console.log(buf);
// Prints: <Buffer 40 5e dd 2f 1a 9f be 77>
```

*CommonJS version:*

```js
const { Buffer } = require('node:buffer');

const buf = Buffer.allocUnsafe(8);

buf.writeDoubleBE(123.456, 0);

console.log(buf);
// Prints: <Buffer 40 5e dd 2f 1a 9f be 77>
```

### `buf.writeDoubleLE(value[, offset])`

Added in: v0.11.15

**History**

| Version | Changes |
| --- | --- |
| v10.0.0 | Removed `noAssert` and no implicit coercion of the offset to `uint32` anymore. |

- `value` [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) Number to be written to `buf`.
- `offset` [`<integer>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) Number of bytes to skip before starting to write. Must
  satisfy `0 <= offset <= buf.length - 8`. **Default:** `0`.
- Returns: [`<integer>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) `offset` plus the number of bytes written.

Writes `value` to `buf` at the specified `offset` as little-endian. The `value`
must be a JavaScript number. Behavior is undefined when `value` is anything
other than a JavaScript number.

*ES module version:*

```js
import { Buffer } from 'node:buffer';

const buf = Buffer.allocUnsafe(8);

buf.writeDoubleLE(123.456, 0);

console.log(buf);
// Prints: <Buffer 77 be 9f 1a 2f dd 5e 40>
```

*CommonJS version:*

```js
const { Buffer } = require('node:buffer');

const buf = Buffer.allocUnsafe(8);

buf.writeDoubleLE(123.456, 0);

console.log(buf);
// Prints: <Buffer 77 be 9f 1a 2f dd 5e 40>
```

### `buf.writeFloatBE(value[, offset])`

Added in: v0.11.15

**History**

| Version | Changes |
| --- | --- |
| v10.0.0 | Removed `noAssert` and no implicit coercion of the offset to `uint32` anymore. |

- `value` [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) Number to be written to `buf`.
- `offset` [`<integer>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) Number of bytes to skip before starting to write. Must
  satisfy `0 <= offset <= buf.length - 4`. **Default:** `0`.
- Returns: [`<integer>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) `offset` plus the number of bytes written.

Writes `value` to `buf` at the specified `offset` as big-endian. Behavior is
undefined when `value` is anything other than a JavaScript number.

*ES module version:*

```js
import { Buffer } from 'node:buffer';

const buf = Buffer.allocUnsafe(4);

buf.writeFloatBE(0xcafebabe, 0);

console.log(buf);
// Prints: <Buffer 4f 4a fe bb>
```

*CommonJS version:*

```js
const { Buffer } = require('node:buffer');

const buf = Buffer.allocUnsafe(4);

buf.writeFloatBE(0xcafebabe, 0);

console.log(buf);
// Prints: <Buffer 4f 4a fe bb>
```

### `buf.writeFloatLE(value[, offset])`

Added in: v0.11.15

**History**

| Version | Changes |
| --- | --- |
| v10.0.0 | Removed `noAssert` and no implicit coercion of the offset to `uint32` anymore. |

- `value` [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) Number to be written to `buf`.
- `offset` [`<integer>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) Number of bytes to skip before starting to write. Must
  satisfy `0 <= offset <= buf.length - 4`. **Default:** `0`.
- Returns: [`<integer>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) `offset` plus the number of bytes written.

Writes `value` to `buf` at the specified `offset` as little-endian. Behavior is
undefined when `value` is anything other than a JavaScript number.

*ES module version:*

```js
import { Buffer } from 'node:buffer';

const buf = Buffer.allocUnsafe(4);

buf.writeFloatLE(0xcafebabe, 0);

console.log(buf);
// Prints: <Buffer bb fe 4a 4f>
```

*CommonJS version:*

```js
const { Buffer } = require('node:buffer');

const buf = Buffer.allocUnsafe(4);

buf.writeFloatLE(0xcafebabe, 0);

console.log(buf);
// Prints: <Buffer bb fe 4a 4f>
```

### `buf.writeInt8(value[, offset])`

Added in: v0.5.0

**History**

| Version | Changes |
| --- | --- |
| v10.0.0 | Removed `noAssert` and no implicit coercion of the offset to `uint32` anymore. |

- `value` [`<integer>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) Number to be written to `buf`.
- `offset` [`<integer>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) Number of bytes to skip before starting to write. Must
  satisfy `0 <= offset <= buf.length - 1`. **Default:** `0`.
- Returns: [`<integer>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) `offset` plus the number of bytes written.

Writes `value` to `buf` at the specified `offset`. `value` must be a valid
signed 8-bit integer. Behavior is undefined when `value` is anything other than
a signed 8-bit integer.

`value` is interpreted and written as a two's complement signed integer.

*ES module version:*

```js
import { Buffer } from 'node:buffer';

const buf = Buffer.allocUnsafe(2);

buf.writeInt8(2, 0);
buf.writeInt8(-2, 1);

console.log(buf);
// Prints: <Buffer 02 fe>
```

*CommonJS version:*

```js
const { Buffer } = require('node:buffer');

const buf = Buffer.allocUnsafe(2);

buf.writeInt8(2, 0);
buf.writeInt8(-2, 1);

console.log(buf);
// Prints: <Buffer 02 fe>
```

### `buf.writeInt16BE(value[, offset])`

Added in: v0.5.5

**History**

| Version | Changes |
| --- | --- |
| v10.0.0 | Removed `noAssert` and no implicit coercion of the offset to `uint32` anymore. |

- `value` [`<integer>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) Number to be written to `buf`.
- `offset` [`<integer>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) Number of bytes to skip before starting to write. Must
  satisfy `0 <= offset <= buf.length - 2`. **Default:** `0`.
- Returns: [`<integer>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) `offset` plus the number of bytes written.

Writes `value` to `buf` at the specified `offset` as big-endian. The `value`
must be a valid signed 16-bit integer. Behavior is undefined when `value` is
anything other than a signed 16-bit integer.

The `value` is interpreted and written as a two's complement signed integer.

*ES module version:*

```js
import { Buffer } from 'node:buffer';

const buf = Buffer.allocUnsafe(2);

buf.writeInt16BE(0x0102, 0);

console.log(buf);
// Prints: <Buffer 01 02>
```

*CommonJS version:*

```js
const { Buffer } = require('node:buffer');

const buf = Buffer.allocUnsafe(2);

buf.writeInt16BE(0x0102, 0);

console.log(buf);
// Prints: <Buffer 01 02>
```

### `buf.writeInt16LE(value[, offset])`

Added in: v0.5.5

**History**

| Version | Changes |
| --- | --- |
| v10.0.0 | Removed `noAssert` and no implicit coercion of the offset to `uint32` anymore. |

- `value` [`<integer>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) Number to be written to `buf`.
- `offset` [`<integer>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) Number of bytes to skip before starting to write. Must
  satisfy `0 <= offset <= buf.length - 2`. **Default:** `0`.
- Returns: [`<integer>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) `offset` plus the number of bytes written.

Writes `value` to `buf` at the specified `offset` as little-endian. The `value`
must be a valid signed 16-bit integer. Behavior is undefined when `value` is
anything other than a signed 16-bit integer.

The `value` is interpreted and written as a two's complement signed integer.

*ES module version:*

```js
import { Buffer } from 'node:buffer';

const buf = Buffer.allocUnsafe(2);

buf.writeInt16LE(0x0304, 0);

console.log(buf);
// Prints: <Buffer 04 03>
```

*CommonJS version:*

```js
const { Buffer } = require('node:buffer');

const buf = Buffer.allocUnsafe(2);

buf.writeInt16LE(0x0304, 0);

console.log(buf);
// Prints: <Buffer 04 03>
```

### `buf.writeInt32BE(value[, offset])`

Added in: v0.5.5

**History**

| Version | Changes |
| --- | --- |
| v10.0.0 | Removed `noAssert` and no implicit coercion of the offset to `uint32` anymore. |

- `value` [`<integer>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) Number to be written to `buf`.
- `offset` [`<integer>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) Number of bytes to skip before starting to write. Must
  satisfy `0 <= offset <= buf.length - 4`. **Default:** `0`.
- Returns: [`<integer>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) `offset` plus the number of bytes written.

Writes `value` to `buf` at the specified `offset` as big-endian. The `value`
must be a valid signed 32-bit integer. Behavior is undefined when `value` is
anything other than a signed 32-bit integer.

The `value` is interpreted and written as a two's complement signed integer.

*ES module version:*

```js
import { Buffer } from 'node:buffer';

const buf = Buffer.allocUnsafe(4);

buf.writeInt32BE(0x01020304, 0);

console.log(buf);
// Prints: <Buffer 01 02 03 04>
```

*CommonJS version:*

```js
const { Buffer } = require('node:buffer');

const buf = Buffer.allocUnsafe(4);

buf.writeInt32BE(0x01020304, 0);

console.log(buf);
// Prints: <Buffer 01 02 03 04>
```

### `buf.writeInt32LE(value[, offset])`

Added in: v0.5.5

**History**

| Version | Changes |
| --- | --- |
| v10.0.0 | Removed `noAssert` and no implicit coercion of the offset to `uint32` anymore. |

- `value` [`<integer>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) Number to be written to `buf`.
- `offset` [`<integer>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) Number of bytes to skip before starting to write. Must
  satisfy `0 <= offset <= buf.length - 4`. **Default:** `0`.
- Returns: [`<integer>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) `offset` plus the number of bytes written.

Writes `value` to `buf` at the specified `offset` as little-endian. The `value`
must be a valid signed 32-bit integer. Behavior is undefined when `value` is
anything other than a signed 32-bit integer.

The `value` is interpreted and written as a two's complement signed integer.

*ES module version:*

```js
import { Buffer } from 'node:buffer';

const buf = Buffer.allocUnsafe(4);

buf.writeInt32LE(0x05060708, 0);

console.log(buf);
// Prints: <Buffer 08 07 06 05>
```

*CommonJS version:*

```js
const { Buffer } = require('node:buffer');

const buf = Buffer.allocUnsafe(4);

buf.writeInt32LE(0x05060708, 0);

console.log(buf);
// Prints: <Buffer 08 07 06 05>
```

### `buf.writeIntBE(value, offset, byteLength)`

Added in: v0.11.15

**History**

| Version | Changes |
| --- | --- |
| v10.0.0 | Removed `noAssert` and no implicit coercion of the offset and `byteLength` to `uint32` anymore. |

- `value` [`<integer>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) Number to be written to `buf`.
- `offset` [`<integer>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) Number of bytes to skip before starting to write. Must
  satisfy `0 <= offset <= buf.length - byteLength`.
- `byteLength` [`<integer>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) Number of bytes to write. Must satisfy `0 < byteLength <= 6`.
- Returns: [`<integer>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) `offset` plus the number of bytes written.

Writes `byteLength` bytes of `value` to `buf` at the specified `offset`
as big-endian. Supports up to 48 bits of accuracy. Behavior is undefined when
`value` is anything other than a signed integer.

*ES module version:*

```js
import { Buffer } from 'node:buffer';

const buf = Buffer.allocUnsafe(6);

buf.writeIntBE(0x1234567890ab, 0, 6);

console.log(buf);
// Prints: <Buffer 12 34 56 78 90 ab>
```

*CommonJS version:*

```js
const { Buffer } = require('node:buffer');

const buf = Buffer.allocUnsafe(6);

buf.writeIntBE(0x1234567890ab, 0, 6);

console.log(buf);
// Prints: <Buffer 12 34 56 78 90 ab>
```

### `buf.writeIntLE(value, offset, byteLength)`

Added in: v0.11.15

**History**

| Version | Changes |
| --- | --- |
| v10.0.0 | Removed `noAssert` and no implicit coercion of the offset and `byteLength` to `uint32` anymore. |

- `value` [`<integer>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) Number to be written to `buf`.
- `offset` [`<integer>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) Number of bytes to skip before starting to write. Must
  satisfy `0 <= offset <= buf.length - byteLength`.
- `byteLength` [`<integer>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) Number of bytes to write. Must satisfy `0 < byteLength <= 6`.
- Returns: [`<integer>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) `offset` plus the number of bytes written.

Writes `byteLength` bytes of `value` to `buf` at the specified `offset`
as little-endian. Supports up to 48 bits of accuracy. Behavior is undefined
when `value` is anything other than a signed integer.

*ES module version:*

```js
import { Buffer } from 'node:buffer';

const buf = Buffer.allocUnsafe(6);

buf.writeIntLE(0x1234567890ab, 0, 6);

console.log(buf);
// Prints: <Buffer ab 90 78 56 34 12>
```

*CommonJS version:*

```js
const { Buffer } = require('node:buffer');

const buf = Buffer.allocUnsafe(6);

buf.writeIntLE(0x1234567890ab, 0, 6);

console.log(buf);
// Prints: <Buffer ab 90 78 56 34 12>
```

### `buf.writeUInt8(value[, offset])`

Added in: v0.5.0

**History**

| Version | Changes |
| --- | --- |
| v14.9.0, v12.19.0 | This function is also available as `buf.writeUint8()`. |
| v10.0.0 | Removed `noAssert` and no implicit coercion of the offset to `uint32` anymore. |

- `value` [`<integer>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) Number to be written to `buf`.
- `offset` [`<integer>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) Number of bytes to skip before starting to write. Must
  satisfy `0 <= offset <= buf.length - 1`. **Default:** `0`.
- Returns: [`<integer>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) `offset` plus the number of bytes written.

Writes `value` to `buf` at the specified `offset`. `value` must be a
valid unsigned 8-bit integer. Behavior is undefined when `value` is anything
other than an unsigned 8-bit integer.

This function is also available under the `writeUint8` alias.

*ES module version:*

```js
import { Buffer } from 'node:buffer';

const buf = Buffer.allocUnsafe(4);

buf.writeUInt8(0x3, 0);
buf.writeUInt8(0x4, 1);
buf.writeUInt8(0x23, 2);
buf.writeUInt8(0x42, 3);

console.log(buf);
// Prints: <Buffer 03 04 23 42>
```

*CommonJS version:*

```js
const { Buffer } = require('node:buffer');

const buf = Buffer.allocUnsafe(4);

buf.writeUInt8(0x3, 0);
buf.writeUInt8(0x4, 1);
buf.writeUInt8(0x23, 2);
buf.writeUInt8(0x42, 3);

console.log(buf);
// Prints: <Buffer 03 04 23 42>
```

### `buf.writeUInt16BE(value[, offset])`

Added in: v0.5.5

**History**

| Version | Changes |
| --- | --- |
| v14.9.0, v12.19.0 | This function is also available as `buf.writeUint16BE()`. |
| v10.0.0 | Removed `noAssert` and no implicit coercion of the offset to `uint32` anymore. |

- `value` [`<integer>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) Number to be written to `buf`.
- `offset` [`<integer>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) Number of bytes to skip before starting to write. Must
  satisfy `0 <= offset <= buf.length - 2`. **Default:** `0`.
- Returns: [`<integer>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) `offset` plus the number of bytes written.

Writes `value` to `buf` at the specified `offset` as big-endian. The `value`
must be a valid unsigned 16-bit integer. Behavior is undefined when `value`
is anything other than an unsigned 16-bit integer.

This function is also available under the `writeUint16BE` alias.

*ES module version:*

```js
import { Buffer } from 'node:buffer';

const buf = Buffer.allocUnsafe(4);

buf.writeUInt16BE(0xdead, 0);
buf.writeUInt16BE(0xbeef, 2);

console.log(buf);
// Prints: <Buffer de ad be ef>
```

*CommonJS version:*

```js
const { Buffer } = require('node:buffer');

const buf = Buffer.allocUnsafe(4);

buf.writeUInt16BE(0xdead, 0);
buf.writeUInt16BE(0xbeef, 2);

console.log(buf);
// Prints: <Buffer de ad be ef>
```

### `buf.writeUInt16LE(value[, offset])`

Added in: v0.5.5

**History**

| Version | Changes |
| --- | --- |
| v14.9.0, v12.19.0 | This function is also available as `buf.writeUint16LE()`. |
| v10.0.0 | Removed `noAssert` and no implicit coercion of the offset to `uint32` anymore. |

- `value` [`<integer>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) Number to be written to `buf`.
- `offset` [`<integer>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) Number of bytes to skip before starting to write. Must
  satisfy `0 <= offset <= buf.length - 2`. **Default:** `0`.
- Returns: [`<integer>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) `offset` plus the number of bytes written.

Writes `value` to `buf` at the specified `offset` as little-endian. The `value`
must be a valid unsigned 16-bit integer. Behavior is undefined when `value` is
anything other than an unsigned 16-bit integer.

This function is also available under the `writeUint16LE` alias.

*ES module version:*

```js
import { Buffer } from 'node:buffer';

const buf = Buffer.allocUnsafe(4);

buf.writeUInt16LE(0xdead, 0);
buf.writeUInt16LE(0xbeef, 2);

console.log(buf);
// Prints: <Buffer ad de ef be>
```

*CommonJS version:*

```js
const { Buffer } = require('node:buffer');

const buf = Buffer.allocUnsafe(4);

buf.writeUInt16LE(0xdead, 0);
buf.writeUInt16LE(0xbeef, 2);

console.log(buf);
// Prints: <Buffer ad de ef be>
```

### `buf.writeUInt32BE(value[, offset])`

Added in: v0.5.5

**History**

| Version | Changes |
| --- | --- |
| v14.9.0, v12.19.0 | This function is also available as `buf.writeUint32BE()`. |
| v10.0.0 | Removed `noAssert` and no implicit coercion of the offset to `uint32` anymore. |

- `value` [`<integer>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) Number to be written to `buf`.
- `offset` [`<integer>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) Number of bytes to skip before starting to write. Must
  satisfy `0 <= offset <= buf.length - 4`. **Default:** `0`.
- Returns: [`<integer>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) `offset` plus the number of bytes written.

Writes `value` to `buf` at the specified `offset` as big-endian. The `value`
must be a valid unsigned 32-bit integer. Behavior is undefined when `value`
is anything other than an unsigned 32-bit integer.

This function is also available under the `writeUint32BE` alias.

*ES module version:*

```js
import { Buffer } from 'node:buffer';

const buf = Buffer.allocUnsafe(4);

buf.writeUInt32BE(0xfeedface, 0);

console.log(buf);
// Prints: <Buffer fe ed fa ce>
```

*CommonJS version:*

```js
const { Buffer } = require('node:buffer');

const buf = Buffer.allocUnsafe(4);

buf.writeUInt32BE(0xfeedface, 0);

console.log(buf);
// Prints: <Buffer fe ed fa ce>
```

### `buf.writeUInt32LE(value[, offset])`

Added in: v0.5.5

**History**

| Version | Changes |
| --- | --- |
| v14.9.0, v12.19.0 | This function is also available as `buf.writeUint32LE()`. |
| v10.0.0 | Removed `noAssert` and no implicit coercion of the offset to `uint32` anymore. |

- `value` [`<integer>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) Number to be written to `buf`.
- `offset` [`<integer>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) Number of bytes to skip before starting to write. Must
  satisfy `0 <= offset <= buf.length - 4`. **Default:** `0`.
- Returns: [`<integer>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) `offset` plus the number of bytes written.

Writes `value` to `buf` at the specified `offset` as little-endian. The `value`
must be a valid unsigned 32-bit integer. Behavior is undefined when `value` is
anything other than an unsigned 32-bit integer.

This function is also available under the `writeUint32LE` alias.

*ES module version:*

```js
import { Buffer } from 'node:buffer';

const buf = Buffer.allocUnsafe(4);

buf.writeUInt32LE(0xfeedface, 0);

console.log(buf);
// Prints: <Buffer ce fa ed fe>
```

*CommonJS version:*

```js
const { Buffer } = require('node:buffer');

const buf = Buffer.allocUnsafe(4);

buf.writeUInt32LE(0xfeedface, 0);

console.log(buf);
// Prints: <Buffer ce fa ed fe>
```

### `buf.writeUIntBE(value, offset, byteLength)`

Added in: v0.5.5

**History**

| Version | Changes |
| --- | --- |
| v14.9.0, v12.19.0 | This function is also available as `buf.writeUintBE()`. |
| v10.0.0 | Removed `noAssert` and no implicit coercion of the offset and `byteLength` to `uint32` anymore. |

- `value` [`<integer>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) Number to be written to `buf`.
- `offset` [`<integer>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) Number of bytes to skip before starting to write. Must
  satisfy `0 <= offset <= buf.length - byteLength`.
- `byteLength` [`<integer>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) Number of bytes to write. Must satisfy `0 < byteLength <= 6`.
- Returns: [`<integer>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) `offset` plus the number of bytes written.

Writes `byteLength` bytes of `value` to `buf` at the specified `offset`
as big-endian. Supports up to 48 bits of accuracy. Behavior is undefined
when `value` is anything other than an unsigned integer.

This function is also available under the `writeUintBE` alias.

*ES module version:*

```js
import { Buffer } from 'node:buffer';

const buf = Buffer.allocUnsafe(6);

buf.writeUIntBE(0x1234567890ab, 0, 6);

console.log(buf);
// Prints: <Buffer 12 34 56 78 90 ab>
```

*CommonJS version:*

```js
const { Buffer } = require('node:buffer');

const buf = Buffer.allocUnsafe(6);

buf.writeUIntBE(0x1234567890ab, 0, 6);

console.log(buf);
// Prints: <Buffer 12 34 56 78 90 ab>
```

### `buf.writeUIntLE(value, offset, byteLength)`

Added in: v0.5.5

**History**

| Version | Changes |
| --- | --- |
| v14.9.0, v12.19.0 | This function is also available as `buf.writeUintLE()`. |
| v10.0.0 | Removed `noAssert` and no implicit coercion of the offset and `byteLength` to `uint32` anymore. |

- `value` [`<integer>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) Number to be written to `buf`.
- `offset` [`<integer>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) Number of bytes to skip before starting to write. Must
  satisfy `0 <= offset <= buf.length - byteLength`.
- `byteLength` [`<integer>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) Number of bytes to write. Must satisfy `0 < byteLength <= 6`.
- Returns: [`<integer>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) `offset` plus the number of bytes written.

Writes `byteLength` bytes of `value` to `buf` at the specified `offset`
as little-endian. Supports up to 48 bits of accuracy. Behavior is undefined
when `value` is anything other than an unsigned integer.

This function is also available under the `writeUintLE` alias.

*ES module version:*

```js
import { Buffer } from 'node:buffer';

const buf = Buffer.allocUnsafe(6);

buf.writeUIntLE(0x1234567890ab, 0, 6);

console.log(buf);
// Prints: <Buffer ab 90 78 56 34 12>
```

*CommonJS version:*

```js
const { Buffer } = require('node:buffer');

const buf = Buffer.allocUnsafe(6);

buf.writeUIntLE(0x1234567890ab, 0, 6);

console.log(buf);
// Prints: <Buffer ab 90 78 56 34 12>
```

### `new Buffer(array)`

Deprecated in: v6.0.0

**History**

| Version | Changes |
| --- | --- |
| v10.0.0 | Calling this constructor emits a deprecation warning when run from code outside the `node_modules` directory. |
| v7.2.1 | Calling this constructor no longer emits a deprecation warning. |
| v7.0.0 | Calling this constructor emits a deprecation warning now. |

Stability: 0 - Deprecated: Use [`Buffer.from(array)`](07-buffer-part1.md#static-method-bufferfromarray) instead.

- `array` [`<integer>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type)[] An array of bytes to copy from.

See [`Buffer.from(array)`](07-buffer-part1.md#static-method-bufferfromarray).

### `new Buffer(arrayBuffer[, byteOffset[, length]])`

Added in: v3.0.0Deprecated in: v6.0.0

**History**

| Version | Changes |
| --- | --- |
| v10.0.0 | Calling this constructor emits a deprecation warning when run from code outside the `node_modules` directory. |
| v7.2.1 | Calling this constructor no longer emits a deprecation warning. |
| v7.0.0 | Calling this constructor emits a deprecation warning now. |
| v6.0.0 | The `byteOffset` and `length` parameters are supported now. |

Stability: 0 - Deprecated: Use
[`Buffer.from(arrayBuffer[, byteOffset[, length]])`](07-buffer-part1.md#static-method-bufferfromarraybuffer-byteoffset-length)
instead.

- `arrayBuffer` [`<ArrayBuffer>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/ArrayBuffer) | [`<SharedArrayBuffer>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/SharedArrayBuffer) An [`<ArrayBuffer>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/ArrayBuffer),
  [`<SharedArrayBuffer>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/SharedArrayBuffer) or the `.buffer` property of a [`<TypedArray>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/TypedArray).
- `byteOffset` [`<integer>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) Index of first byte to expose. **Default:** `0`.
- `length` [`<integer>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) Number of bytes to expose. **Default:** `arrayBuffer.byteLength - byteOffset`.

See
[`Buffer.from(arrayBuffer[, byteOffset[, length]])`](07-buffer-part1.md#static-method-bufferfromarraybuffer-byteoffset-length).

### `new Buffer(buffer)`

Deprecated in: v6.0.0

**History**

| Version | Changes |
| --- | --- |
| v10.0.0 | Calling this constructor emits a deprecation warning when run from code outside the `node_modules` directory. |
| v7.2.1 | Calling this constructor no longer emits a deprecation warning. |
| v7.0.0 | Calling this constructor emits a deprecation warning now. |

Stability: 0 - Deprecated: Use [`Buffer.from(buffer)`](07-buffer-part1.md#static-method-bufferfrombuffer) instead.

- `buffer` [`<Buffer>`](07-buffer-part1.md#class-buffer) | [`<Uint8Array>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Uint8Array) An existing `Buffer` or [`<Uint8Array>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Uint8Array) from
  which to copy data.

See [`Buffer.from(buffer)`](07-buffer-part1.md#static-method-bufferfrombuffer).

### `new Buffer(size)`

Deprecated in: v6.0.0

**History**

| Version | Changes |
| --- | --- |
| v10.0.0 | Calling this constructor emits a deprecation warning when run from code outside the `node_modules` directory. |
| v8.0.0 | The `new Buffer(size)` will return zero-filled memory by default. |
| v7.2.1 | Calling this constructor no longer emits a deprecation warning. |
| v7.0.0 | Calling this constructor emits a deprecation warning now. |

Stability: 0 - Deprecated: Use [`Buffer.alloc()`](07-buffer-part1.md#static-method-bufferallocsize-fill-encoding) instead (also see
[`Buffer.allocUnsafe()`](07-buffer-part1.md#static-method-bufferallocunsafesize-alignment)).

- `size` [`<integer>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) The desired length of the new `Buffer`.

See [`Buffer.alloc()`](07-buffer-part1.md#static-method-bufferallocsize-fill-encoding) and [`Buffer.allocUnsafe()`](07-buffer-part1.md#static-method-bufferallocunsafesize-alignment). This variant of the
constructor is equivalent to [`Buffer.alloc()`](07-buffer-part1.md#static-method-bufferallocsize-fill-encoding).

### `new Buffer(string[, encoding])`

Deprecated in: v6.0.0

**History**

| Version | Changes |
| --- | --- |
| v10.0.0 | Calling this constructor emits a deprecation warning when run from code outside the `node_modules` directory. |
| v7.2.1 | Calling this constructor no longer emits a deprecation warning. |
| v7.0.0 | Calling this constructor emits a deprecation warning now. |

Stability: 0 - Deprecated:
Use [`Buffer.from(string[, encoding])`](07-buffer-part1.md#static-method-bufferfromstring-encoding) instead.

- `string` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) String to encode.
- `encoding` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) The encoding of `string`. **Default:** `'utf8'`.

See [`Buffer.from(string[, encoding])`](07-buffer-part1.md#static-method-bufferfromstring-encoding).

## Class: `File`

Added in: v19.2.0, v18.13.0

**History**

| Version | Changes |
| --- | --- |
| v23.0.0 | Makes File instances cloneable. |
| v20.0.0 | No longer experimental. |

- Extends: [`<Blob>`](07-buffer-part1.md#class-blob)

A [`<File>`](07-buffer-part3.md#class-file) provides information about files.

### `new buffer.File(sources, fileName[, options])`

Added in: v19.2.0, v18.13.0

- `sources` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type)[] | [`<ArrayBuffer>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/ArrayBuffer)[] | [`<TypedArray>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/TypedArray)[] | [`<DataView>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/DataView)[] | [`<Blob>`](07-buffer-part1.md#class-blob)[] | [`<File>`](07-buffer-part3.md#class-file)[]
  An array of string, [`<ArrayBuffer>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/ArrayBuffer), [`<TypedArray>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/TypedArray), [`<DataView>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/DataView), [`<File>`](07-buffer-part3.md#class-file), or [`<Blob>`](07-buffer-part1.md#class-blob)
  objects, or any mix of such objects, that will be stored within the `File`.
- `fileName` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) The name of the file.
- `options` [`<Object>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Object)
  - `endings` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) One of either `'transparent'` or `'native'`. When set
    to `'native'`, line endings in string source parts will be converted to
    the platform native line-ending as specified by `require('node:os').EOL`.
  - `type` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) The File content-type.
  - `lastModified` [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) The last modified date of the file. **Default:** `Date.now()`.

### `file.name`

Added in: v19.2.0, v18.13.0

- Type: [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type)

The name of the `File`.

### `file.lastModified`

Added in: v19.2.0, v18.13.0

- Type: [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type)

The last modified date of the `File`.

## `node:buffer` module APIs

While, the `Buffer` object is available as a global, there are additional
`Buffer`-related APIs that are available only via the `node:buffer` module
accessed using `require('node:buffer')`.

### `buffer.atob(data)`

Added in: v15.13.0, v14.17.0

Stability: 3 - Legacy. Use `Buffer.from(data, 'base64')` instead.

- `data` [`<any>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#Data_types) The Base64-encoded input string.
- Returns: [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type)

Decodes a string of Base64-encoded data into bytes, and encodes those bytes
into a string using Latin-1 (ISO-8859-1).

The `data` may be any JavaScript-value that can be coerced into a string.

**This function is only provided for compatibility with legacy web platform APIs
and should never be used in new code, because they use strings to represent
binary data and predate the introduction of typed arrays in JavaScript.
For code running using Node.js APIs, converting between base64-encoded strings
and binary data should be performed using `Buffer.from(str, 'base64')` and
`buf.toString('base64')`.**

An automated migration is available ([source](https://github.com/nodejs/userland-migrations/tree/main/recipes/buffer-atob-btoa):

```bash
npx codemod@latest @nodejs/buffer-atob-btoa
```

### `buffer.btoa(data)`

Added in: v15.13.0, v14.17.0

Stability: 3 - Legacy. Use `buf.toString('base64')` instead.

- `data` [`<any>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#Data_types) An ASCII (Latin1) string.
- Returns: [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type)

Decodes a string into bytes using Latin-1 (ISO-8859), and encodes those bytes
into a string using Base64.

The `data` may be any JavaScript-value that can be coerced into a string.

**This function is only provided for compatibility with legacy web platform APIs
and should never be used in new code, because they use strings to represent
binary data and predate the introduction of typed arrays in JavaScript.
For code running using Node.js APIs, converting between base64-encoded strings
and binary data should be performed using `Buffer.from(str, 'base64')` and
`buf.toString('base64')`.**

An automated migration is available ([source](https://github.com/nodejs/userland-migrations/tree/main/recipes/buffer-atob-btoa):

```bash
npx codemod@latest @nodejs/buffer-atob-btoa
```

### `buffer.isAscii(input)`

Added in: v19.6.0, v18.15.0

**History**

| Version | Changes |
| --- | --- |
| v26.8.0 | Detached `ArrayBuffer`s and views backed by them are treated as empty. |

- `input` [`<Buffer>`](07-buffer-part1.md#class-buffer) | [`<ArrayBuffer>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/ArrayBuffer) | [`<TypedArray>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/TypedArray) The input to validate.
- Returns: [`<boolean>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#boolean_type)

This function returns `true` if `input` contains only valid ASCII-encoded data,
including the case in which `input` is empty.

A detached `ArrayBuffer`, or a `TypedArray` backed by one, is treated as empty.

### `buffer.isLatin1(input)`

Added in: v26.11.0

- `input` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) The string to validate.
- Returns: [`<boolean>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#boolean_type)

This function returns `true` if `input` can be losslessly encoded using the
Node.js [`'latin1'`](07-buffer-part1.md#buffers-and-character-encodings) encoding, including the case in
which `input` is empty. That is, it returns `true` if every UTF-16 code unit
of `input` is in the range `U+0000` to `U+00FF`. Such a string is also a valid
[WebIDL `ByteString`](https://webidl.spec.whatwg.org/#idl-ByteString).

This check uses the Node.js definition of `'latin1'`, in which each code unit
from `U+0000` to `U+00FF` maps directly to the byte of the same value. It does
not use the [WHATWG Encoding Standard](https://encoding.spec.whatwg.org/) definition, in which the `'latin1'`
label is an alias for `windows-1252`. For example, `'\u0080'` is considered
latin1 by this function, while `'€'` (`U+20AC`, which `windows-1252` encodes as
`0x80`) is not.

Unlike [`buffer.isAscii()`](#bufferisasciiinput) and [`buffer.isUtf8()`](#bufferisutf8input), this function
validates a string rather than a `Buffer`, `TypedArray`, or `ArrayBuffer`.
Every byte sequence would trivially be valid `'latin1'`, since every byte maps
to a code unit less than or equal to `0xFF`.

*ES module version:*

```js
import { isLatin1 } from 'node:buffer';

isLatin1('hello');   // true
isLatin1('café');    // true
isLatin1('\u00ff');  // true
isLatin1('\u0100');  // false
isLatin1('€');       // false
```

*CommonJS version:*

```js
const { isLatin1 } = require('node:buffer');

isLatin1('hello');   // true
isLatin1('café');    // true
isLatin1('\u00ff');  // true
isLatin1('\u0100');  // false
isLatin1('€');       // false
```

### `buffer.isUtf8(input)`

Added in: v19.4.0, v18.14.0

**History**

| Version | Changes |
| --- | --- |
| v26.8.0 | Detached `ArrayBuffer`s and views backed by them are treated as empty. |

- `input` [`<Buffer>`](07-buffer-part1.md#class-buffer) | [`<ArrayBuffer>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/ArrayBuffer) | [`<TypedArray>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/TypedArray) The input to validate.
- Returns: [`<boolean>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#boolean_type)

This function returns `true` if `input` contains only valid UTF-8-encoded data,
including the case in which `input` is empty.

A detached `ArrayBuffer`, or a `TypedArray` backed by one, is treated as empty.

### `buffer.INSPECT_MAX_BYTES`

Added in: v0.5.4

- Type: [`<integer>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) **Default:** `50`

Returns the maximum number of bytes that will be returned when
`buf.inspect()` is called. This can be overridden by user modules. See
[`util.inspect()`](60-util-part1.md#utilinspectobject-options) for more details on `buf.inspect()` behavior.

### `buffer.kMaxLength`

Added in: v3.0.0

- Type: [`<integer>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) The largest size allowed for a single `Buffer` instance.

An alias for [`buffer.constants.MAX_LENGTH`](#bufferconstantsmax_length).

### `buffer.kStringMaxLength`

Added in: v3.0.0

- Type: [`<integer>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) The largest length allowed for a single `string` instance.

An alias for [`buffer.constants.MAX_STRING_LENGTH`](#bufferconstantsmax_string_length).

### `buffer.resolveObjectURL(id)`

Added in: v16.7.0

**History**

| Version | Changes |
| --- | --- |
| v24.0.0, v22.17.0 | Marking the API stable. |

- `id` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) A `'blob:nodedata:...` URL string returned by a prior call to
  `URL.createObjectURL()`.
- Returns: [`<Blob>`](07-buffer-part1.md#class-blob)

Resolves a `'blob:nodedata:...'` an associated [`<Blob>`](07-buffer-part1.md#class-blob) object registered using
a prior call to `URL.createObjectURL()`.

### `buffer.transcode(source, fromEnc, toEnc)`

Added in: v7.1.0

**History**

| Version | Changes |
| --- | --- |
| v8.0.0 | The `source` parameter can now be a `Uint8Array`. |

- `source` [`<Buffer>`](07-buffer-part1.md#class-buffer) | [`<Uint8Array>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Uint8Array) A `Buffer` or `Uint8Array` instance.
- `fromEnc` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) The current encoding.
- `toEnc` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) To target encoding.
- Returns: [`<Buffer>`](07-buffer-part1.md#class-buffer)

Re-encodes the given `Buffer` or `Uint8Array` instance from one character
encoding to another. Returns a new `Buffer` instance.

Throws if the `fromEnc` or `toEnc` specify invalid character encodings or if
conversion from `fromEnc` to `toEnc` is not permitted.

Encodings supported by `buffer.transcode()` are: `'ascii'`, `'utf8'`,
`'utf16le'`, `'ucs2'`, `'latin1'`, and `'binary'`.

The transcoding process will use substitution characters if a given byte
sequence cannot be adequately represented in the target encoding. For instance:

*ES module version:*

```js
import { Buffer, transcode } from 'node:buffer';

const newBuf = transcode(Buffer.from('€'), 'utf8', 'ascii');
console.log(newBuf.toString('ascii'));
// Prints: '?'
```

*CommonJS version:*

```js
const { Buffer, transcode } = require('node:buffer');

const newBuf = transcode(Buffer.from('€'), 'utf8', 'ascii');
console.log(newBuf.toString('ascii'));
// Prints: '?'
```

Because the Euro (`€`) sign is not representable in US-ASCII, it is replaced
with `?` in the transcoded `Buffer`.

### Buffer constants

Added in: v8.2.0

#### `buffer.constants.MAX_LENGTH`

Added in: v8.2.0

**History**

| Version | Changes |
| --- | --- |
| v22.0.0 | Value is changed to 253 - 1 on 64-bit architectures, and 231 - 1 on 32-bit architectures. |
| v15.0.0 | Value is changed to 232 on 64-bit architectures. |
| v14.0.0 | Value is changed from 231 - 1 to 232 - 1 on 64-bit architectures. |

- Type: [`<integer>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) The largest size allowed for a single `Buffer` instance.

On 32-bit architectures, this value is equal to 231 - 1 (about 2
GiB).

On 64-bit architectures, this value is equal to [`Number.MAX_SAFE_INTEGER`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Number/MAX_SAFE_INTEGER)
(253 - 1, about 8 PiB).

It reflects [`v8::Uint8Array::kMaxLength`](https://v8.github.io/api/head/classv8_1_1Uint8Array.html#a7677e3d0c9c92e4d40bef7212f5980c6) under the hood.

This value is also available as [`buffer.kMaxLength`](#bufferkmaxlength).

#### `buffer.constants.MAX_STRING_LENGTH`

Added in: v8.2.0

- Type: [`<integer>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) The largest length allowed for a single `string` instance.

Represents the largest `length` that a `string` primitive can have, counted
in UTF-16 code units.

This value may depend on the JS engine that is being used.

## `Buffer.from()`, `Buffer.alloc()`, and `Buffer.allocUnsafe()`

In versions of Node.js prior to 6.0.0, `Buffer` instances were created using the
`Buffer` constructor function, which allocates the returned `Buffer`
differently based on what arguments are provided:

- Passing a number as the first argument to `Buffer()` (e.g. `new Buffer(10)`)
  allocates a new `Buffer` object of the specified size. Prior to Node.js 8.0.0,
  the memory allocated for such `Buffer` instances is *not* initialized and
  *can contain sensitive data*. Such `Buffer` instances *must* be subsequently
  initialized by using either [`buf.fill(0)`](07-buffer-part2.md#buffillvalue-offset-end-encoding) or by writing to the
  entire `Buffer` before reading data from the `Buffer`.
  While this behavior is *intentional* to improve performance,
  development experience has demonstrated that a more explicit distinction is
  required between creating a fast-but-uninitialized `Buffer` versus creating a
  slower-but-safer `Buffer`. Since Node.js 8.0.0, `Buffer(num)` and `new Buffer(num)` return a `Buffer` with initialized memory.
- Passing a string, array, or `Buffer` as the first argument copies the
  passed object's data into the `Buffer`.
- Passing an [`<ArrayBuffer>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/ArrayBuffer) or a [`<SharedArrayBuffer>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/SharedArrayBuffer) returns a `Buffer`
  that shares allocated memory with the given array buffer.

Because the behavior of `new Buffer()` is different depending on the type of the
first argument, security and reliability issues can be inadvertently introduced
into applications when argument validation or `Buffer` initialization is not
performed.

For example, if an attacker can cause an application to receive a number where
a string is expected, the application may call `new Buffer(100)`
instead of `new Buffer("100")`, leading it to allocate a 100 byte buffer instead
of allocating a 3 byte buffer with content `"100"`. This is commonly possible
using JSON API calls. Since JSON distinguishes between numeric and string types,
it allows injection of numbers where a naively written application that does not
validate its input sufficiently might expect to always receive a string.
Before Node.js 8.0.0, the 100 byte buffer might contain
arbitrary pre-existing in-memory data, so may be used to expose in-memory
secrets to a remote attacker. Since Node.js 8.0.0, exposure of memory cannot
occur because the data is zero-filled. However, other attacks are still
possible, such as causing very large buffers to be allocated by the server,
leading to performance degradation or crashing on memory exhaustion.

To make the creation of `Buffer` instances more reliable and less error-prone,
the various forms of the `new Buffer()` constructor have been **deprecated**
and replaced by separate `Buffer.from()`, [`Buffer.alloc()`](07-buffer-part1.md#static-method-bufferallocsize-fill-encoding), and
[`Buffer.allocUnsafe()`](07-buffer-part1.md#static-method-bufferallocunsafesize-alignment) methods.

*Developers should migrate all existing uses of the `new Buffer()` constructors
to one of these new APIs.*

- [`Buffer.from(array)`](07-buffer-part1.md#static-method-bufferfromarray) returns a new `Buffer` that *contains a copy* of the
  provided octets.
- [`Buffer.from(arrayBuffer[, byteOffset[, length]])`](07-buffer-part1.md#static-method-bufferfromarraybuffer-byteoffset-length)
  returns a new `Buffer` that *shares the same allocated memory* as the given
  [`<ArrayBuffer>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/ArrayBuffer).
- [`Buffer.from(buffer)`](07-buffer-part1.md#static-method-bufferfrombuffer) returns a new `Buffer` that *contains a copy* of the
  contents of the given `Buffer`.
- [`Buffer.from(string[, encoding])`](07-buffer-part1.md#static-method-bufferfromstring-encoding) returns a new
  `Buffer` that *contains a copy* of the provided string.
- [`Buffer.alloc(size[, fill[, encoding]])`](07-buffer-part1.md#static-method-bufferallocsize-fill-encoding) returns a new
  initialized `Buffer` of the specified size. This method is slower than
  [`Buffer.allocUnsafe(size)`](07-buffer-part1.md#static-method-bufferallocunsafesize-alignment) but guarantees that newly
  created `Buffer` instances never contain old data that is potentially
  sensitive. A `TypeError` will be thrown if `size` is not a number.
- [`Buffer.allocUnsafe(size)`](07-buffer-part1.md#static-method-bufferallocunsafesize-alignment) and
  [`Buffer.allocUnsafeSlow(size)`](07-buffer-part1.md#static-method-bufferallocunsafeslowsize-alignment) each return a
  new uninitialized `Buffer` of the specified `size`. Because the `Buffer` is
  uninitialized, the allocated segment of memory might contain old data that is
  potentially sensitive.

`Buffer` instances returned by [`Buffer.allocUnsafe()`](07-buffer-part1.md#static-method-bufferallocunsafesize-alignment), [`Buffer.from(string)`](07-buffer-part1.md#static-method-bufferfromstring-encoding),
[`Buffer.concat()`](07-buffer-part1.md#static-method-bufferconcatlist-totallength) and [`Buffer.from(array)`](07-buffer-part1.md#static-method-bufferfromarray) *may* be allocated off a shared
internal memory pool if `size` is less than or equal to half [`Buffer.poolSize`](07-buffer-part1.md#bufferpoolsize).
Instances returned by [`Buffer.allocUnsafeSlow()`](07-buffer-part1.md#static-method-bufferallocunsafeslowsize-alignment) *never* use the shared internal
memory pool.

[Contents](../README.md) | [Previous: Buffer, part 2 of 4](07-buffer-part2.md) | [Next: Buffer, part 4 of 4](07-buffer-part4.md)
