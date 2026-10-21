[Contents](../README.md) | [Previous: Stream](51-stream.md) | [Next: Test runner](53-test.md)

Source: [https://nodejs.org/docs/latest/api/string_decoder.html](https://nodejs.org/docs/latest/api/string_decoder.html)

# String decoder

[Stability: 2](01-documentation.md#stability-index) - Stable

The `node:string_decoder` module provides an API for decoding `Buffer` objects
into strings in a manner that preserves encoded multi-byte UTF-8 and UTF-16
characters. It can be accessed using:

*ES module version:*

```js
import { StringDecoder } from 'node:string_decoder';
```

*CommonJS version:*

```js
const { StringDecoder } = require('node:string_decoder');
```

The following example shows the basic use of the `StringDecoder` class.

*ES module version:*

```js
import { StringDecoder } from 'node:string_decoder';
import { Buffer } from 'node:buffer';
const decoder = new StringDecoder('utf8');

const cent = Buffer.from([0xC2, 0xA2]);
console.log(decoder.write(cent)); // Prints: ¢

const euro = Buffer.from([0xE2, 0x82, 0xAC]);
console.log(decoder.write(euro)); // Prints: €
```

*CommonJS version:*

```js
const { StringDecoder } = require('node:string_decoder');
const decoder = new StringDecoder('utf8');

const cent = Buffer.from([0xC2, 0xA2]);
console.log(decoder.write(cent)); // Prints: ¢

const euro = Buffer.from([0xE2, 0x82, 0xAC]);
console.log(decoder.write(euro)); // Prints: €
```

When a `Buffer` instance is written to the `StringDecoder` instance, an
internal buffer is used to ensure that the decoded string does not contain
any incomplete multibyte characters. These are held in the buffer until the
next call to `stringDecoder.write()` or until `stringDecoder.end()` is called.

In the following example, the three UTF-8 encoded bytes of the European Euro
symbol (`€`) are written over three separate operations:

*ES module version:*

```js
import { StringDecoder } from 'node:string_decoder';
import { Buffer } from 'node:buffer';
const decoder = new StringDecoder('utf8');

decoder.write(Buffer.from([0xE2]));
decoder.write(Buffer.from([0x82]));
console.log(decoder.end(Buffer.from([0xAC]))); // Prints: €
```

*CommonJS version:*

```js
const { StringDecoder } = require('node:string_decoder');
const decoder = new StringDecoder('utf8');

decoder.write(Buffer.from([0xE2]));
decoder.write(Buffer.from([0x82]));
console.log(decoder.end(Buffer.from([0xAC]))); // Prints: €
```

## Class: `StringDecoder`

### `new StringDecoder([encoding])`

Added in: v0.1.99

- `encoding` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) The character [encoding](07-buffer.md#buffers-and-character-encodings) the `StringDecoder` will use.
  **Default:** `'utf8'`.

Creates a new `StringDecoder` instance.

### `stringDecoder.end([buffer])`

Added in: v0.9.3

- `buffer` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) | [`<Buffer>`](07-buffer.md#class-buffer) | [`<TypedArray>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/TypedArray) | [`<DataView>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/DataView) The bytes to decode.
- Returns: [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type)

Returns any remaining input stored in the internal buffer as a string. Bytes
representing incomplete UTF-8 and UTF-16 characters will be replaced with
substitution characters appropriate for the character encoding.

If the `buffer` argument is provided, one final call to `stringDecoder.write()`
is performed before returning the remaining input.
After `end()` is called, the `stringDecoder` object can be reused for new input.

### `stringDecoder.write(buffer)`

Added in: v0.1.99

**History**

| Version | Changes |
| --- | --- |
| v8.0.0 | Each invalid character is now replaced by a single replacement character instead of one for each individual byte. |

- `buffer` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) | [`<Buffer>`](07-buffer.md#class-buffer) | [`<TypedArray>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/TypedArray) | [`<DataView>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/DataView) The bytes to decode.
- Returns: [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type)

Returns a decoded string, ensuring that any incomplete multibyte characters at
the end of the `Buffer`, or `TypedArray`, or `DataView` are omitted from the
returned string and stored in an internal buffer for the next call to
`stringDecoder.write()` or `stringDecoder.end()`.

[Contents](../README.md) | [Previous: Stream](51-stream.md) | [Next: Test runner](53-test.md)
