[Contents](../README.md) | [Previous: Utilities, part 1 of 3](60-util-part1.md) | [Next: Utilities, part 3 of 3](60-util-part3.md)

Source: [https://nodejs.org/docs/latest/api/util.html](https://nodejs.org/docs/latest/api/util.html)

Part 2 of 3 of this section.

# Utilities (part 2 of 3)

Continues: util.inspect(object[, showHidden[, depth[, colors]]]).

### `util.inspect.custom`

Added in: v6.6.0

**History**

| Version | Changes |
| --- | --- |
| v10.12.0 | This is now defined as a shared symbol. |

- Type: [`<symbol>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#symbol_type) that can be used to declare custom inspect functions.

In addition to being accessible through `util.inspect.custom`, this
symbol is [registered globally](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Symbol/for) and can be
accessed in any environment as `Symbol.for('nodejs.util.inspect.custom')`.

Using this allows code to be written in a portable fashion, so that the custom
inspect function is used in a Node.js environment and ignored in the browser.
The `util.inspect()` function itself is passed as third argument to the custom
inspect function to allow further portability.

```js
const customInspectSymbol = Symbol.for('nodejs.util.inspect.custom');

class Password {
  constructor(value) {
    this.value = value;
  }

  toString() {
    return 'xxxxxxxx';
  }

  [customInspectSymbol](depth, inspectOptions, inspect) {
    return `Password <${this.toString()}>`;
  }
}

const password = new Password('r0sebud');
console.log(password);
// Prints Password <xxxxxxxx>
```

See [Custom inspection functions on Objects](60-util-part1.md#custom-inspection-functions-on-objects) for more details.

### `util.inspect.defaultOptions`

Added in: v6.4.0

The `defaultOptions` value allows customization of the default options used by
`util.inspect`. This is useful for functions like `console.log` or
`util.format` which implicitly call into `util.inspect`. It shall be set to an
object containing one or more valid [`util.inspect()`](60-util-part1.md#utilinspectobject-options) options. Setting
option properties directly is also supported.

*ES module version:*

```js
import { inspect } from 'node:util';
const arr = Array(156).fill(0);

console.log(arr); // Logs the truncated array
inspect.defaultOptions.maxArrayLength = null;
console.log(arr); // logs the full array
```

*CommonJS version:*

```js
const { inspect } = require('node:util');
const arr = Array(156).fill(0);

console.log(arr); // Logs the truncated array
inspect.defaultOptions.maxArrayLength = null;
console.log(arr); // logs the full array
```

## `util.isDeepStrictEqual(val1, val2[, options])`

Added in: v9.0.0

**History**

| Version | Changes |
| --- | --- |
| v24.9.0 | Added `options` parameter to allow skipping prototype comparison. |

- `val1` [`<any>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#Data_types)
- `val2` [`<any>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#Data_types)
- `skipPrototype` [`<boolean>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#boolean_type) If `true`, prototype and constructor
  comparison is skipped during deep strict equality check. **Default:** `false`.
- Returns: [`<boolean>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#boolean_type)

Returns `true` if there is deep strict equality between `val1` and `val2`.
Otherwise, returns `false`.

By default, deep strict equality includes comparison of object prototypes and
constructors. When `skipPrototype` is `true`, objects with
different prototypes or constructors can still be considered equal if their
enumerable properties are deeply strictly equal.

```js
const util = require('node:util');

class Foo {
  constructor(a) {
    this.a = a;
  }
}

class Bar {
  constructor(a) {
    this.a = a;
  }
}

const foo = new Foo(1);
const bar = new Bar(1);

// Different constructors, same properties
console.log(util.isDeepStrictEqual(foo, bar));
// false

console.log(util.isDeepStrictEqual(foo, bar, true));
// true
```

See [`assert.deepStrictEqual()`](03-assert.md#assertdeepstrictequalactual-expected-message) for more information about deep strict
equality.

## `util.isPartialDeepStrictEqual(val1, val2)`

Added in: v26.11.0

- `val1` [`<any>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#Data_types)
- `val2` [`<any>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#Data_types)
- Returns: [`<boolean>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#boolean_type)

Returns `true` if there is partial deep strict equality between `val1` and
`val2`. Otherwise, returns `false`.

"Partial" equality means that only properties that exist on `val2` are going
to be compared.

See [`assert.partialDeepStrictEqual()`](03-assert.md#assertpartialdeepstrictequalactual-expected-message) for more information about partial
deep strict equality.

## `util.markPromiseAsHandled(promise)`

Added in: v26.10.0

- `promise` [`<Promise>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Promise) The promise to mark as handled

Marks a promise as handled so that unhandled rejections are ignored and are not
reported to the `'unhandledrejection'` event.

## Class: `util.MIMEType`

Added in: v19.1.0, v18.13.0

**History**

| Version | Changes |
| --- | --- |
| v23.11.0, v22.15.0 | Marking the API stable. |

An implementation of [the MIMEType class](https://bmeck.github.io/node-proposal-mime-api/).

In accordance with browser conventions, all properties of `MIMEType` objects
are implemented as getters and setters on the class prototype, rather than as
data properties on the object itself.

A MIME string is a structured string containing multiple meaningful
components. When parsed, a `MIMEType` object is returned containing
properties for each of these components.

### `new MIMEType(input)`

- `input` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) The input MIME to parse

Creates a new `MIMEType` object by parsing the `input`.

*ES module version:*

```js
import { MIMEType } from 'node:util';

const myMIME = new MIMEType('text/plain');
```

*CommonJS version:*

```js
const { MIMEType } = require('node:util');

const myMIME = new MIMEType('text/plain');
```

A `TypeError` will be thrown if the `input` is not a valid MIME. Note
that an effort will be made to coerce the given values into strings. For
instance:

*ES module version:*

```js
import { MIMEType } from 'node:util';
const myMIME = new MIMEType({ toString: () => 'text/plain' });
console.log(String(myMIME));
// Prints: text/plain
```

*CommonJS version:*

```js
const { MIMEType } = require('node:util');
const myMIME = new MIMEType({ toString: () => 'text/plain' });
console.log(String(myMIME));
// Prints: text/plain
```

### `mime.type`

- Type: [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type)

Gets and sets the type portion of the MIME.

*ES module version:*

```js
import { MIMEType } from 'node:util';

const myMIME = new MIMEType('text/javascript');
console.log(myMIME.type);
// Prints: text
myMIME.type = 'application';
console.log(myMIME.type);
// Prints: application
console.log(String(myMIME));
// Prints: application/javascript
```

*CommonJS version:*

```js
const { MIMEType } = require('node:util');

const myMIME = new MIMEType('text/javascript');
console.log(myMIME.type);
// Prints: text
myMIME.type = 'application';
console.log(myMIME.type);
// Prints: application
console.log(String(myMIME));
// Prints: application/javascript
```

### `mime.subtype`

- Type: [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type)

Gets and sets the subtype portion of the MIME.

*ES module version:*

```js
import { MIMEType } from 'node:util';

const myMIME = new MIMEType('text/ecmascript');
console.log(myMIME.subtype);
// Prints: ecmascript
myMIME.subtype = 'javascript';
console.log(myMIME.subtype);
// Prints: javascript
console.log(String(myMIME));
// Prints: text/javascript
```

*CommonJS version:*

```js
const { MIMEType } = require('node:util');

const myMIME = new MIMEType('text/ecmascript');
console.log(myMIME.subtype);
// Prints: ecmascript
myMIME.subtype = 'javascript';
console.log(myMIME.subtype);
// Prints: javascript
console.log(String(myMIME));
// Prints: text/javascript
```

### `mime.essence`

- Type: [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type)

Gets the essence of the MIME. This property is read only.
Use `mime.type` or `mime.subtype` to alter the MIME.

*ES module version:*

```js
import { MIMEType } from 'node:util';

const myMIME = new MIMEType('text/javascript;key=value');
console.log(myMIME.essence);
// Prints: text/javascript
myMIME.type = 'application';
console.log(myMIME.essence);
// Prints: application/javascript
console.log(String(myMIME));
// Prints: application/javascript;key=value
```

*CommonJS version:*

```js
const { MIMEType } = require('node:util');

const myMIME = new MIMEType('text/javascript;key=value');
console.log(myMIME.essence);
// Prints: text/javascript
myMIME.type = 'application';
console.log(myMIME.essence);
// Prints: application/javascript
console.log(String(myMIME));
// Prints: application/javascript;key=value
```

### `mime.params`

- Type: [`<MIMEParams>`](60-util-part2.md#class-utilmimeparams)

Gets the [`MIMEParams`](#class-utilmimeparams) object representing the
parameters of the MIME. This property is read-only. See
[`MIMEParams`](#class-utilmimeparams) documentation for details.

### `mime.toString()`

- Returns: [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type)

The `toString()` method on the `MIMEType` object returns the serialized MIME.

Because of the need for standard compliance, this method does not allow users
to customize the serialization process of the MIME.

### `mime.toJSON()`

- Returns: [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type)

Alias for [`mime.toString()`](#mimetostring).

This method is automatically called when an `MIMEType` object is serialized
with [`JSON.stringify()`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/JSON/stringify).

*ES module version:*

```js
import { MIMEType } from 'node:util';

const myMIMES = [
  new MIMEType('image/png'),
  new MIMEType('image/gif'),
];
console.log(JSON.stringify(myMIMES));
// Prints: ["image/png", "image/gif"]
```

*CommonJS version:*

```js
const { MIMEType } = require('node:util');

const myMIMES = [
  new MIMEType('image/png'),
  new MIMEType('image/gif'),
];
console.log(JSON.stringify(myMIMES));
// Prints: ["image/png", "image/gif"]
```

### `MIMEType.parse(string)`

- `string` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) The input MIME to parse
- Returns: [`<MIMEType>`](60-util-part2.md#class-utilmimetype) | [`<null>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#null_type)

Attempts to parse the given `string` as a MIMEType. If the string cannot be
parsed, `null` is returned.

## Class: `util.MIMEParams`

Added in: v19.1.0, v18.13.0

The `MIMEParams` API provides read and write access to the parameters of a
`MIMEType`.

### `new MIMEParams()`

Creates a new `MIMEParams` object by with empty parameters

*ES module version:*

```js
import { MIMEParams } from 'node:util';

const myParams = new MIMEParams();
```

*CommonJS version:*

```js
const { MIMEParams } = require('node:util');

const myParams = new MIMEParams();
```

### `mimeParams.delete(name)`

- `name` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type)

Remove all name-value pairs whose name is `name`.

### `mimeParams.entries()`

- Returns: [`<Iterator>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Iterator)

Returns an iterator over each of the name-value pairs in the parameters.
Each item of the iterator is a JavaScript `Array`. The first item of the array
is the `name`, the second item of the array is the `value`.

### `mimeParams.get(name)`

- `name` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type)
- Returns: [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) | [`<null>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#null_type) A string or `null` if there is no name-value pair
  with the given `name`.

Returns the value of the first name-value pair whose name is `name`. If there
are no such pairs, `null` is returned.

### `mimeParams.has(name)`

- `name` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type)
- Returns: [`<boolean>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#boolean_type)

Returns `true` if there is at least one name-value pair whose name is `name`.

### `mimeParams.keys()`

- Returns: [`<Iterator>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Iterator)

Returns an iterator over the names of each name-value pair.

*ES module version:*

```js
import { MIMEType } from 'node:util';

const { params } = new MIMEType('text/plain;foo=0;bar=1');
for (const name of params.keys()) {
  console.log(name);
}
// Prints:
//   foo
//   bar
```

*CommonJS version:*

```js
const { MIMEType } = require('node:util');

const { params } = new MIMEType('text/plain;foo=0;bar=1');
for (const name of params.keys()) {
  console.log(name);
}
// Prints:
//   foo
//   bar
```

### `mimeParams.set(name, value)`

- `name` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type)
- `value` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type)

Sets the value in the `MIMEParams` object associated with `name` to
`value`. If there are any pre-existing name-value pairs whose names are `name`,
set the first such pair's value to `value`.

*ES module version:*

```js
import { MIMEType } from 'node:util';

const { params } = new MIMEType('text/plain;foo=0;bar=1');
params.set('foo', 'def');
params.set('baz', 'xyz');
console.log(params.toString());
// Prints: foo=def;bar=1;baz=xyz
```

*CommonJS version:*

```js
const { MIMEType } = require('node:util');

const { params } = new MIMEType('text/plain;foo=0;bar=1');
params.set('foo', 'def');
params.set('baz', 'xyz');
console.log(params.toString());
// Prints: foo=def;bar=1;baz=xyz
```

### `mimeParams.values()`

- Returns: [`<Iterator>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Iterator)

Returns an iterator over the values of each name-value pair.

### `mimeParams[Symbol.iterator]()`

- Returns: [`<Iterator>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Iterator)

Alias for [`mimeParams.entries()`](#mimeparamsentries).

*ES module version:*

```js
import { MIMEType } from 'node:util';

const { params } = new MIMEType('text/plain;foo=bar;xyz=baz');
for (const [name, value] of params) {
  console.log(name, value);
}
// Prints:
//   foo bar
//   xyz baz
```

*CommonJS version:*

```js
const { MIMEType } = require('node:util');

const { params } = new MIMEType('text/plain;foo=bar;xyz=baz');
for (const [name, value] of params) {
  console.log(name, value);
}
// Prints:
//   foo bar
//   xyz baz
```

## `util.parseArgs([config])`

Added in: v18.3.0, v16.17.0

**History**

| Version | Changes |
| --- | --- |
| v22.4.0, v20.16.0 | add support for allowing negative options in input `config`. |
| v20.0.0 | The API is no longer experimental. |
| v18.11.0, v16.19.0 | Add support for default values in input `config`. |
| v18.7.0, v16.17.0 | add support for returning detailed parse information using `tokens` in input `config` and returned properties. |

- `config` [`<Object>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Object) Used to provide arguments for parsing and to configure
  the parser. `config` supports the following properties:

  - `args` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type)[] array of argument strings. **Default:** `process.argv`
    with `execPath` and `filename` removed.
  - `options` [`<Object>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Object) Used to describe arguments known to the parser.
    Keys of `options` are the long names of options and values are an
    [`<Object>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Object) accepting the following properties:
    - `type` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) Type of argument, which must be either `boolean` or `string`.
    - `multiple` [`<boolean>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#boolean_type) Whether this option can be provided multiple
      times. If `true`, all values will be collected in an array. If
      `false`, values for the option are last-wins. **Default:** `false`.
    - `short` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) A single character alias for the option.
    - `default` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) | [`<boolean>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#boolean_type) | [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type)[] | [`<boolean>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#boolean_type)[] The value to assign to
      the option if it does not appear in the arguments to be parsed. The value
      must match the type specified by the `type` property. If `multiple` is
      `true`, it must be an array. No default value is applied when the option
      does appear in the arguments to be parsed, even if the provided value
      is falsy.
  - `strict` [`<boolean>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#boolean_type) Should an error be thrown when unknown arguments
    are encountered, or when arguments are passed that do not match the `type` configured in `options`.
    **Default:** `true`.
  - `allowPositionals` [`<boolean>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#boolean_type) Whether this command accepts positional
    arguments. **Default:** `false` if `strict` is `true`, otherwise `true`.
  - `allowNegative` [`<boolean>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#boolean_type) If `true`, allows explicitly setting boolean
    options to `false` by prefixing the option name with `--no-`.
    **Default:** `false`.
  - `tokens` [`<boolean>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#boolean_type) Return the parsed tokens. This is useful for extending
    the built-in behavior, from adding additional checks through to reprocessing
    the tokens in different ways. **Default:** `false`.
- Returns: [`<Object>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Object) The parsed command line arguments:

  - `values` [`<Object>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Object) A mapping of parsed option names with their [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type)
    or [`<boolean>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#boolean_type) values.
  - `positionals` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type)[] Positional arguments.
  - `tokens` [`<Object>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Object)[] | [`<undefined>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#undefined_type) See [parseArgs tokens](#parseargs-tokens)
    section. Only returned if `config` includes `tokens: true`.

Provides a higher level API for command-line argument parsing than interacting
with `process.argv` directly. Takes a specification for the expected arguments
and returns a structured object with the parsed options and positionals.

*ES module version:*

```js
import { parseArgs } from 'node:util';
const args = ['-f', '--bar', 'b'];
const options = {
  foo: {
    type: 'boolean',
    short: 'f',
  },
  bar: {
    type: 'string',
  },
};
const {
  values,
  positionals,
} = parseArgs({ args, options });
console.log(values, positionals);
// Prints: [Object: null prototype] { foo: true, bar: 'b' } []
```

*CommonJS version:*

```js
const { parseArgs } = require('node:util');
const args = ['-f', '--bar', 'b'];
const options = {
  foo: {
    type: 'boolean',
    short: 'f',
  },
  bar: {
    type: 'string',
  },
};
const {
  values,
  positionals,
} = parseArgs({ args, options });
console.log(values, positionals);
// Prints: [Object: null prototype] { foo: true, bar: 'b' } []
```

### `parseArgs` `tokens`

Detailed parse information is available for adding custom behaviors by
specifying `tokens: true` in the configuration.
The returned tokens have properties describing:

- all tokens
  - `kind` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) One of 'option', 'positional', or 'option-terminator'.
  - `index` [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) Index of element in `args` containing token. So the
    source argument for a token is `args[token.index]`.
- option tokens
  - `name` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) Long name of option.
  - `rawName` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) How option used in args, like `-f` of `--foo`.
  - `value` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) | [`<undefined>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#undefined_type) Option value specified in args.
    Undefined for boolean options.
  - `inlineValue` [`<boolean>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#boolean_type) | [`<undefined>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#undefined_type) Whether option value specified inline,
    like `--foo=bar`.
- positional tokens
  - `value` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) The value of the positional argument in args (i.e. `args[index]`).
- option-terminator token

The returned tokens are in the order encountered in the input args. Options
that appear more than once in args produce a token for each use. Short option
groups like `-xy` expand to a token for each option. So `-xxx` produces
three tokens.

For example, to add support for a negated option like `--no-color` (which
`allowNegative` supports when the option is of `boolean` type), the returned
tokens can be reprocessed to change the value stored for the negated option.

*ES module version:*

```js
import { parseArgs } from 'node:util';

const options = {
  'color': { type: 'boolean' },
  'no-color': { type: 'boolean' },
  'logfile': { type: 'string' },
  'no-logfile': { type: 'boolean' },
};
const { values, tokens } = parseArgs({ options, tokens: true });

// Reprocess the option tokens and overwrite the returned values.
tokens
  .filter((token) => token.kind === 'option')
  .forEach((token) => {
    if (token.name.startsWith('no-')) {
      // Store foo:false for --no-foo
      const positiveName = token.name.slice(3);
      values[positiveName] = false;
      delete values[token.name];
    } else {
      // Resave value so last one wins if both --foo and --no-foo.
      values[token.name] = token.value ?? true;
    }
  });

const color = values.color;
const logfile = values.logfile ?? 'default.log';

console.log({ logfile, color });
```

*CommonJS version:*

```js
const { parseArgs } = require('node:util');

const options = {
  'color': { type: 'boolean' },
  'no-color': { type: 'boolean' },
  'logfile': { type: 'string' },
  'no-logfile': { type: 'boolean' },
};
const { values, tokens } = parseArgs({ options, tokens: true });

// Reprocess the option tokens and overwrite the returned values.
tokens
  .filter((token) => token.kind === 'option')
  .forEach((token) => {
    if (token.name.startsWith('no-')) {
      // Store foo:false for --no-foo
      const positiveName = token.name.slice(3);
      values[positiveName] = false;
      delete values[token.name];
    } else {
      // Resave value so last one wins if both --foo and --no-foo.
      values[token.name] = token.value ?? true;
    }
  });

const color = values.color;
const logfile = values.logfile ?? 'default.log';

console.log({ logfile, color });
```

Example usage showing negated options, and when an option is used
multiple ways then last one wins.

```console
$ node negate.js
{ logfile: 'default.log', color: undefined }
$ node negate.js --no-logfile --no-color
{ logfile: false, color: false }
$ node negate.js --logfile=test.log --color
{ logfile: 'test.log', color: true }
$ node negate.js --no-logfile --logfile=test.log --color --no-color
{ logfile: 'test.log', color: false }
```

## `util.parseEnv(content)`

Added in: v21.7.0, v20.12.0

**History**

| Version | Changes |
| --- | --- |
| v24.10.0, v22.21.0 | This API is no longer experimental. |

- `content` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type)

The raw contents of a `.env` file.

- Returns: [`<Object>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Object)

Given an example `.env` file:

*CommonJS version:*

```js
const { parseEnv } = require('node:util');

parseEnv('HELLO=world\nHELLO=oh my\n');
// Returns: { HELLO: 'oh my' }
```

*ES module version:*

```js
import { parseEnv } from 'node:util';

parseEnv('HELLO=world\nHELLO=oh my\n');
// Returns: { HELLO: 'oh my' }
```

## `util.promisify(original)`

Added in: v8.0.0

**History**

| Version | Changes |
| --- | --- |
| v20.8.0 | Calling `promisify` on a function that returns a `Promise` is deprecated. |

- `original` [`<Function>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Function)
- Returns: [`<Function>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Function)

Takes a function following the common error-first callback style, i.e. taking
an `(err, value) => ...` callback as the last argument, and returns a version
that returns promises.

*ES module version:*

```js
import { promisify } from 'node:util';
import { stat } from 'node:fs';

const promisifiedStat = promisify(stat);
promisifiedStat('.').then((stats) => {
  // Do something with `stats`
}).catch((error) => {
  // Handle the error.
});
```

*CommonJS version:*

```js
const { promisify } = require('node:util');
const { stat } = require('node:fs');

const promisifiedStat = promisify(stat);
promisifiedStat('.').then((stats) => {
  // Do something with `stats`
}).catch((error) => {
  // Handle the error.
});
```

Or, equivalently using `async function`s:

*ES module version:*

```js
import { promisify } from 'node:util';
import { stat } from 'node:fs';

const promisifiedStat = promisify(stat);

async function callStat() {
  const stats = await promisifiedStat('.');
  console.log(`This directory is owned by ${stats.uid}`);
}

callStat();
```

*CommonJS version:*

```js
const { promisify } = require('node:util');
const { stat } = require('node:fs');

const promisifiedStat = promisify(stat);

async function callStat() {
  const stats = await promisifiedStat('.');
  console.log(`This directory is owned by ${stats.uid}`);
}

callStat();
```

If there is an `original[util.promisify.custom]` property present, `promisify`
will return its value, see [Custom promisified functions](#custom-promisified-functions).

`promisify()` assumes that `original` is a function taking a callback as its
final argument in all cases. If `original` is not a function, `promisify()`
will throw an error. If `original` is a function but its last argument is not
an error-first callback, it will still be passed an error-first
callback as its last argument.

Using `promisify()` on class methods or other methods that use `this` may not
work as expected unless handled specially:

*ES module version:*

```js
import { promisify } from 'node:util';

class Foo {
  constructor() {
    this.a = 42;
  }

  bar(callback) {
    callback(null, this.a);
  }
}

const foo = new Foo();

const naiveBar = promisify(foo.bar);
// TypeError: Cannot read properties of undefined (reading 'a')
// naiveBar().then(a => console.log(a));

naiveBar.call(foo).then((a) => console.log(a)); // '42'

const bindBar = naiveBar.bind(foo);
bindBar().then((a) => console.log(a)); // '42'
```

*CommonJS version:*

```js
const { promisify } = require('node:util');

class Foo {
  constructor() {
    this.a = 42;
  }

  bar(callback) {
    callback(null, this.a);
  }
}

const foo = new Foo();

const naiveBar = promisify(foo.bar);
// TypeError: Cannot read properties of undefined (reading 'a')
// naiveBar().then(a => console.log(a));

naiveBar.call(foo).then((a) => console.log(a)); // '42'

const bindBar = naiveBar.bind(foo);
bindBar().then((a) => console.log(a)); // '42'
```

### Custom promisified functions

Using the `util.promisify.custom` symbol one can override the return value of
[`util.promisify()`](#utilpromisifyoriginal):

*ES module version:*

```js
import { promisify } from 'node:util';

function doSomething(foo, callback) {
  // ...
}

doSomething[promisify.custom] = (foo) => {
  return getPromiseSomehow();
};

const promisified = promisify(doSomething);
console.log(promisified === doSomething[promisify.custom]);
// prints 'true'
```

*CommonJS version:*

```js
const { promisify } = require('node:util');

function doSomething(foo, callback) {
  // ...
}

doSomething[promisify.custom] = (foo) => {
  return getPromiseSomehow();
};

const promisified = promisify(doSomething);
console.log(promisified === doSomething[promisify.custom]);
// prints 'true'
```

This can be useful for cases where the original function does not follow the
standard format of taking an error-first callback as the last argument.

For example, with a function that takes in
`(foo, onSuccessCallback, onErrorCallback)`:

```js
doSomething[util.promisify.custom] = (foo) => {
  return new Promise((resolve, reject) => {
    doSomething(foo, resolve, reject);
  });
};
```

If `promisify.custom` is defined but is not a function, `promisify()` will
throw an error.

### `util.promisify.custom`

Added in: v8.0.0

**History**

| Version | Changes |
| --- | --- |
| v13.12.0, v12.16.2 | This is now defined as a shared symbol. |

- Type: [`<symbol>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#symbol_type) that can be used to declare custom promisified variants of functions,
  see [Custom promisified functions](#custom-promisified-functions).

In addition to being accessible through `util.promisify.custom`, this
symbol is [registered globally](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Symbol/for) and can be
accessed in any environment as `Symbol.for('nodejs.util.promisify.custom')`.

For example, with a function that takes in
`(foo, onSuccessCallback, onErrorCallback)`:

```js
const kCustomPromisifiedSymbol = Symbol.for('nodejs.util.promisify.custom');

doSomething[kCustomPromisifiedSymbol] = (foo) => {
  return new Promise((resolve, reject) => {
    doSomething(foo, resolve, reject);
  });
};
```

## `util.stripVTControlCharacters(str)`

Added in: v16.11.0

- `str` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type)
- Returns: [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type)

Returns `str` with any ANSI escape codes removed.

```js
console.log(util.stripVTControlCharacters('\u001B[4mvalue\u001B[0m'));
// Prints "value"
```

## `util.styleText(format, text[, options])`

Added in: v21.7.0, v20.12.0

**History**

| Version | Changes |
| --- | --- |
| v26.1.0 | Add support for hexadecimal colors. |
| v24.2.0, v22.17.0 | Added the `'none'` format as a non-op format. |
| v23.5.0, v22.13.0 | styleText is now stable. |
| v22.8.0, v20.18.0 | Respect isTTY and environment variables such as NO\_COLOR, NODE\_DISABLE\_COLORS, and FORCE\_COLOR. |

- `format` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) | [`<Array>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Array) A text format or an Array
  of text formats defined in `util.inspect.colors`, or a hex color in `#RGB`
  or `#RRGGBB` form.
- `text` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) The text to be formatted.
- `options` [`<Object>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Object)
  - `validateStream` [`<boolean>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#boolean_type) When true, `stream` is checked to see if it can handle colors. **Default:** `true`.
  - `stream` [`<Stream>`](51-stream-part1.md#stream) A stream that will be validated if it can be colored. **Default:** `process.stdout`.

This function returns a formatted text considering the `format` passed
for printing in a terminal. It is aware of the terminal's capabilities
and acts according to the configuration set via `NO_COLOR`,
`NODE_DISABLE_COLORS` and `FORCE_COLOR` environment variables.

*ES module version:*

```js
import { styleText } from 'node:util';
import { stderr } from 'node:process';

const successMessage = styleText('green', 'Success!');
console.log(successMessage);

const errorMessage = styleText(
  'red',
  'Error! Error!',
  // Validate if process.stderr has TTY
  { stream: stderr },
);
console.error(errorMessage);
```

*CommonJS version:*

```js
const { styleText } = require('node:util');
const { stderr } = require('node:process');

const successMessage = styleText('green', 'Success!');
console.log(successMessage);

const errorMessage = styleText(
  'red',
  'Error! Error!',
  // Validate if process.stderr has TTY
  { stream: stderr },
);
console.error(errorMessage);
```

`util.inspect.colors` also provides text formats such as `italic`, and
`underline` and you can combine both:

```js
console.log(
  util.styleText(['underline', 'italic'], 'My italic underlined message'),
);
```

When passing an array of formats, the order of the format applied
is left to right so the following style might overwrite the previous one.

```js
console.log(
  util.styleText(['red', 'green'], 'text'), // green
);
```

The special format value `none` applies no additional styling to the text.

In addition to predefined color names, `util.styleText()` supports hex color
strings using ANSI TrueColor (24-bit) escape sequences. Hex colors can be
specified in either 3-digit (`#RGB`) or 6-digit (`#RRGGBB`) format:

*ES module version:*

```js
import { styleText } from 'node:util';

// 6-digit hex color
console.log(styleText('#ff5733', 'Orange text'));

// 3-digit hex color (shorthand)
console.log(styleText('#f00', 'Red text'));
```

*CommonJS version:*

```js
const { styleText } = require('node:util');

// 6-digit hex color
console.log(styleText('#ff5733', 'Orange text'));

// 3-digit hex color (shorthand)
console.log(styleText('#f00', 'Red text'));
```

The full list of formats can be found in [modifiers](60-util-part1.md#modifiers).

## Class: `util.TextDecoder`

Added in: v8.3.0

**History**

| Version | Changes |
| --- | --- |
| v11.0.0 | The class is now available on the global object. |

An implementation of the [WHATWG Encoding Standard](https://encoding.spec.whatwg.org/) `TextDecoder` API.

```js
const decoder = new TextDecoder();
const u8arr = new Uint8Array([72, 101, 108, 108, 111]);
console.log(decoder.decode(u8arr)); // Hello
```

### WHATWG supported encodings

Per the [WHATWG Encoding Standard](https://encoding.spec.whatwg.org/), the encodings supported by the
`TextDecoder` API are outlined in the tables below. For each encoding,
one or more aliases may be used.

Different Node.js build configurations support different sets of encodings.
(see [Internationalization](31-intl.md))

#### Encodings supported by default (with full ICU data)

| Encoding | Aliases |
| --- | --- |
| `'ibm866'` | `'866'`, `'cp866'`, `'csibm866'` |
| `'iso-8859-2'` | `'csisolatin2'`, `'iso-ir-101'`, `'iso8859-2'`, `'iso88592'`, `'iso_8859-2'`, `'iso_8859-2:1987'`, `'l2'`, `'latin2'` |
| `'iso-8859-3'` | `'csisolatin3'`, `'iso-ir-109'`, `'iso8859-3'`, `'iso88593'`, `'iso_8859-3'`, `'iso_8859-3:1988'`, `'l3'`, `'latin3'` |
| `'iso-8859-4'` | `'csisolatin4'`, `'iso-ir-110'`, `'iso8859-4'`, `'iso88594'`, `'iso_8859-4'`, `'iso_8859-4:1988'`, `'l4'`, `'latin4'` |
| `'iso-8859-5'` | `'csisolatincyrillic'`, `'cyrillic'`, `'iso-ir-144'`, `'iso8859-5'`, `'iso88595'`, `'iso_8859-5'`, `'iso_8859-5:1988'` |
| `'iso-8859-6'` | `'arabic'`, `'asmo-708'`, `'csiso88596e'`, `'csiso88596i'`, `'csisolatinarabic'`, `'ecma-114'`, `'iso-8859-6-e'`, `'iso-8859-6-i'`, `'iso-ir-127'`, `'iso8859-6'`, `'iso88596'`, `'iso_8859-6'`, `'iso_8859-6:1987'` |
| `'iso-8859-7'` | `'csisolatingreek'`, `'ecma-118'`, `'elot_928'`, `'greek'`, `'greek8'`, `'iso-ir-126'`, `'iso8859-7'`, `'iso88597'`, `'iso_8859-7'`, `'iso_8859-7:1987'`, `'sun_eu_greek'` |
| `'iso-8859-8'` | `'csiso88598e'`, `'csisolatinhebrew'`, `'hebrew'`, `'iso-8859-8-e'`, `'iso-ir-138'`, `'iso8859-8'`, `'iso88598'`, `'iso_8859-8'`, `'iso_8859-8:1988'`, `'visual'` |
| `'iso-8859-8-i'` | `'csiso88598i'`, `'logical'` |
| `'iso-8859-10'` | `'csisolatin6'`, `'iso-ir-157'`, `'iso8859-10'`, `'iso885910'`, `'l6'`, `'latin6'` |
| `'iso-8859-13'` | `'iso8859-13'`, `'iso885913'` |
| `'iso-8859-14'` | `'iso8859-14'`, `'iso885914'` |
| `'iso-8859-15'` | `'csisolatin9'`, `'iso8859-15'`, `'iso885915'`, `'iso_8859-15'`, `'l9'` |
| `'koi8-r'` | `'cskoi8r'`, `'koi'`, `'koi8'`, `'koi8_r'` |
| `'koi8-u'` | `'koi8-ru'` |
| `'macintosh'` | `'csmacintosh'`, `'mac'`, `'x-mac-roman'` |
| `'windows-874'` | `'dos-874'`, `'iso-8859-11'`, `'iso8859-11'`, `'iso885911'`, `'tis-620'` |
| `'windows-1250'` | `'cp1250'`, `'x-cp1250'` |
| `'windows-1251'` | `'cp1251'`, `'x-cp1251'` |
| `'windows-1252'` | `'ansi_x3.4-1968'`, `'ascii'`, `'cp1252'`, `'cp819'`, `'csisolatin1'`, `'ibm819'`, `'iso-8859-1'`, `'iso-ir-100'`, `'iso8859-1'`, `'iso88591'`, `'iso_8859-1'`, `'iso_8859-1:1987'`, `'l1'`, `'latin1'`, `'us-ascii'`, `'x-cp1252'` |
| `'windows-1253'` | `'cp1253'`, `'x-cp1253'` |
| `'windows-1254'` | `'cp1254'`, `'csisolatin5'`, `'iso-8859-9'`, `'iso-ir-148'`, `'iso8859-9'`, `'iso88599'`, `'iso_8859-9'`, `'iso_8859-9:1989'`, `'l5'`, `'latin5'`, `'x-cp1254'` |
| `'windows-1255'` | `'cp1255'`, `'x-cp1255'` |
| `'windows-1256'` | `'cp1256'`, `'x-cp1256'` |
| `'windows-1257'` | `'cp1257'`, `'x-cp1257'` |
| `'windows-1258'` | `'cp1258'`, `'x-cp1258'` |
| `'x-mac-cyrillic'` | `'x-mac-ukrainian'` |
| `'gbk'` | `'chinese'`, `'csgb2312'`, `'csiso58gb231280'`, `'gb2312'`, `'gb_2312'`, `'gb_2312-80'`, `'iso-ir-58'`, `'x-gbk'` |
| `'gb18030'` |  |
| `'big5'` | `'big5-hkscs'`, `'cn-big5'`, `'csbig5'`, `'x-x-big5'` |
| `'euc-jp'` | `'cseucpkdfmtjapanese'`, `'x-euc-jp'` |
| `'iso-2022-jp'` | `'csiso2022jp'` |
| `'shift_jis'` | `'csshiftjis'`, `'ms932'`, `'ms_kanji'`, `'shift-jis'`, `'sjis'`, `'windows-31j'`, `'x-sjis'` |
| `'euc-kr'` | `'cseuckr'`, `'csksc56011987'`, `'iso-ir-149'`, `'korean'`, `'ks_c_5601-1987'`, `'ks_c_5601-1989'`, `'ksc5601'`, `'ksc_5601'`, `'windows-949'` |

#### Encodings supported when Node.js is built with the `small-icu` option

| Encoding | Aliases |
| --- | --- |
| `'utf-8'` | `'unicode-1-1-utf-8'`, `'utf8'` |
| `'utf-16le'` | `'utf-16'` |
| `'utf-16be'` |  |

#### Encodings supported when ICU is disabled

| Encoding | Aliases |
| --- | --- |
| `'utf-8'` | `'unicode-1-1-utf-8'`, `'utf8'` |
| `'utf-16le'` | `'utf-16'` |

The `'iso-8859-16'` encoding listed in the [WHATWG Encoding Standard](https://encoding.spec.whatwg.org/)
is not supported.

### `new TextDecoder([encoding[, options]])`

- `encoding` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) Identifies the `encoding` that this `TextDecoder` instance
  supports. **Default:** `'utf-8'`.
- `options` [`<Object>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Object)
  - `fatal` [`<boolean>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#boolean_type) `true` if decoding failures are fatal.
    This option is not supported when ICU is disabled
    (see [Internationalization](31-intl.md)). **Default:** `false`.
  - `ignoreBOM` [`<boolean>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#boolean_type) When `true`, the `TextDecoder` will include the byte
    order mark in the decoded result. When `false`, the byte order mark will
    be removed from the output. This option is only used when `encoding` is
    `'utf-8'`, `'utf-16be'`, or `'utf-16le'`. **Default:** `false`.

Creates a new `TextDecoder` instance. The `encoding` may specify one of the
supported encodings or an alias.

The `TextDecoder` class is also available on the global object.

### `textDecoder.decode([input[, options]])`

- `input` [`<ArrayBuffer>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/ArrayBuffer) | [`<DataView>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/DataView) | [`<TypedArray>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/TypedArray) An `ArrayBuffer`, `DataView`, or
  `TypedArray` instance containing the encoded data.
- `options` [`<Object>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Object)
  - `stream` [`<boolean>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#boolean_type) `true` if additional chunks of data are expected.
    **Default:** `false`.
- Returns: [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type)

Decodes the `input` and returns a string. If `options.stream` is `true`, any
incomplete byte sequences occurring at the end of the `input` are buffered
internally and emitted after the next call to `textDecoder.decode()`.

If `textDecoder.fatal` is `true`, decoding errors that occur will result in a
`TypeError` being thrown.

### `textDecoder.encoding`

- Type: [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type)

The encoding supported by the `TextDecoder` instance.

### `textDecoder.fatal`

- Type: [`<boolean>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#boolean_type)

The value will be `true` if decoding errors result in a `TypeError` being
thrown.

### `textDecoder.ignoreBOM`

- Type: [`<boolean>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#boolean_type)

The value will be `true` if the decoding result will include the byte order
mark.

## Class: `util.TextEncoder`

Added in: v8.3.0

**History**

| Version | Changes |
| --- | --- |
| v11.0.0 | The class is now available on the global object. |

An implementation of the [WHATWG Encoding Standard](https://encoding.spec.whatwg.org/) `TextEncoder` API. All
instances of `TextEncoder` only support UTF-8 encoding.

```js
const encoder = new TextEncoder();
const uint8array = encoder.encode('this is some data');
```

The `TextEncoder` class is also available on the global object.

### `textEncoder.encode([input])`

- `input` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) The text to encode. **Default:** an empty string.
- Returns: [`<Uint8Array>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Uint8Array)

UTF-8 encodes the `input` string and returns a `Uint8Array` containing the
encoded bytes.

### `textEncoder.encodeInto(src, dest)`

Added in: v12.11.0

- `src` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) The text to encode.
- `dest` [`<Uint8Array>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Uint8Array) The array to hold the encode result.
- Returns: [`<Object>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Object)
  - `read` [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) The read Unicode code units of src.
  - `written` [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) The written UTF-8 bytes of dest.

UTF-8 encodes the `src` string to the `dest` Uint8Array and returns an object
containing the read Unicode code units and written UTF-8 bytes.

```js
const encoder = new TextEncoder();
const src = 'this is some data';
const dest = new Uint8Array(10);
const { read, written } = encoder.encodeInto(src, dest);
```

### `textEncoder.encoding`

- Type: [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type)

The encoding supported by the `TextEncoder` instance. Always set to `'utf-8'`.

## `util.toUSVString(string)`

Added in: v16.8.0, v14.18.0

- `string` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type)

Returns the `string` after replacing any surrogate code points
(or equivalently, any unpaired surrogate code units) with the
Unicode "replacement character" U+FFFD.

## `util.transferableAbortController()`

Added in: v18.11.0

**History**

| Version | Changes |
| --- | --- |
| v23.11.0, v22.15.0 | Marking the API stable. |

Creates and returns an [`<AbortController>`](26-globals.md#class-abortcontroller) instance whose [`<AbortSignal>`](26-globals.md#class-abortsignal) is marked
as transferable and can be used with `structuredClone()` or `postMessage()`.

## `util.transferableAbortSignal(signal)`

Added in: v18.11.0

**History**

| Version | Changes |
| --- | --- |
| v23.11.0, v22.15.0 | Marking the API stable. |

- `signal` [`<AbortSignal>`](26-globals.md#class-abortsignal)
- Returns: [`<AbortSignal>`](26-globals.md#class-abortsignal)

Marks the given [`<AbortSignal>`](26-globals.md#class-abortsignal) as transferable so that it can be used with `structuredClone()` and `postMessage()`.

```js
const signal = transferableAbortSignal(AbortSignal.timeout(100));
const channel = new MessageChannel();
channel.port2.postMessage(signal, [signal]);
```

## `util.aborted(signal, resource)`

Added in: v19.7.0, v18.16.0

**History**

| Version | Changes |
| --- | --- |
| v24.0.0, v22.16.0 | Change stability index for this feature from Experimental to Stable. |

- `signal` [`<AbortSignal>`](26-globals.md#class-abortsignal)
- `resource` [`<Object>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Object) Any non-null object tied to the abortable operation and held weakly.
  If `resource` is garbage collected before the `signal` aborts, the promise remains pending,
  allowing Node.js to stop tracking it.
  This helps prevent memory leaks in long-running or non-cancelable operations.
- Returns: [`<Promise>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Promise)

Listens to abort event on the provided `signal` and returns a promise that resolves when the `signal` is aborted.
If `resource` is provided, it weakly references the operation's associated object,
so if `resource` is garbage collected before the `signal` aborts,
then returned promise shall remain pending.
This prevents memory leaks in long-running or non-cancelable operations.

*CommonJS version:*

```js
const { aborted } = require('node:util');

// Obtain an object with an abortable signal, like a custom resource or operation.
const dependent = obtainSomethingAbortable();

// Pass `dependent` as the resource, indicating the promise should only resolve
// if `dependent` is still in memory when the signal is aborted.
aborted(dependent.signal, dependent).then(() => {

  // This code runs when `dependent` is aborted.
  console.log('Dependent resource was aborted.');
});

// Simulate an event that triggers the abort.
dependent.on('event', () => {
  dependent.abort(); // This will cause the `aborted` promise to resolve.
});
```

*ES module version:*

```js
import { aborted } from 'node:util';

// Obtain an object with an abortable signal, like a custom resource or operation.
const dependent = obtainSomethingAbortable();

// Pass `dependent` as the resource, indicating the promise should only resolve
// if `dependent` is still in memory when the signal is aborted.
aborted(dependent.signal, dependent).then(() => {

  // This code runs when `dependent` is aborted.
  console.log('Dependent resource was aborted.');
});

// Simulate an event that triggers the abort.
dependent.on('event', () => {
  dependent.abort(); // This will cause the `aborted` promise to resolve.
});
```

## `util.types`

Added in: v10.0.0

**History**

| Version | Changes |
| --- | --- |
| v15.3.0 | Exposed as `require('util/types')`. |

`util.types` provides type checks for different kinds of built-in objects.
Unlike `instanceof` or `Object.prototype.toString.call(value)`, these checks do
not inspect properties of the object that are accessible from JavaScript (like
their prototype), and usually have the overhead of calling into C++.

The result generally does not make any guarantees about what kinds of
properties or behavior a value exposes in JavaScript. They are primarily
useful for addon developers who prefer to do type checking in JavaScript.

The API is accessible via `require('node:util').types` or `require('node:util/types')`.

### `util.types.isAnyArrayBuffer(value)`

Added in: v10.0.0

- `value` [`<any>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#Data_types)
- Returns: [`<boolean>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#boolean_type)

Returns `true` if the value is a built-in [`<ArrayBuffer>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/ArrayBuffer) or
[`<SharedArrayBuffer>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/SharedArrayBuffer) instance.

See also [`util.types.isArrayBuffer()`](#utiltypesisarraybuffervalue) and
[`util.types.isSharedArrayBuffer()`](60-util-part3.md#utiltypesissharedarraybuffervalue).

```js
util.types.isAnyArrayBuffer(new ArrayBuffer());  // Returns true
util.types.isAnyArrayBuffer(new SharedArrayBuffer());  // Returns true
```

### `util.types.isArrayBufferView(value)`

Added in: v10.0.0

- `value` [`<any>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#Data_types)
- Returns: [`<boolean>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#boolean_type)

Returns `true` if the value is an instance of one of the [`<ArrayBuffer>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/ArrayBuffer)
views, such as typed array objects or [`<DataView>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/DataView). Equivalent to [`ArrayBuffer.isView()`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/ArrayBuffer/isView).

```js
util.types.isArrayBufferView(new Int8Array());  // true
util.types.isArrayBufferView(Buffer.from('hello world')); // true
util.types.isArrayBufferView(new DataView(new ArrayBuffer(16)));  // true
util.types.isArrayBufferView(new ArrayBuffer());  // false
```

### `util.types.isArgumentsObject(value)`

Added in: v10.0.0

- `value` [`<any>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#Data_types)
- Returns: [`<boolean>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#boolean_type)

Returns `true` if the value is an `arguments` object.

```js
function foo() {
  util.types.isArgumentsObject(arguments);  // Returns true
}
```

### `util.types.isArrayBuffer(value)`

Added in: v10.0.0

- `value` [`<any>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#Data_types)
- Returns: [`<boolean>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#boolean_type)

Returns `true` if the value is a built-in [`<ArrayBuffer>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/ArrayBuffer) instance.
This does *not* include [`<SharedArrayBuffer>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/SharedArrayBuffer) instances. Usually, it is
desirable to test for both; See [`util.types.isAnyArrayBuffer()`](#utiltypesisanyarraybuffervalue) for that.

```js
util.types.isArrayBuffer(new ArrayBuffer());  // Returns true
util.types.isArrayBuffer(new SharedArrayBuffer());  // Returns false
```

### `util.types.isAsyncFunction(value)`

Added in: v10.0.0

- `value` [`<any>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#Data_types)
- Returns: [`<boolean>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#boolean_type)

Returns `true` if the value is an [async function](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Statements/async_function).
This only reports back what the JavaScript engine is seeing;
in particular, the return value may not match the original source code if
a transpilation tool was used.

```js
util.types.isAsyncFunction(function foo() {});  // Returns false
util.types.isAsyncFunction(async function foo() {});  // Returns true
```

### `util.types.isBigInt64Array(value)`

Added in: v10.0.0

- `value` [`<any>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#Data_types)
- Returns: [`<boolean>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#boolean_type)

Returns `true` if the value is a `BigInt64Array` instance.

```js
util.types.isBigInt64Array(new BigInt64Array());   // Returns true
util.types.isBigInt64Array(new BigUint64Array());  // Returns false
```

### `util.types.isBigIntObject(value)`

Added in: v10.4.0

- `value` [`<any>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#Data_types)
- Returns: [`<boolean>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#boolean_type)

Returns `true` if the value is a BigInt object, e.g. created
by `Object(BigInt(123))`.

```js
util.types.isBigIntObject(Object(BigInt(123)));   // Returns true
util.types.isBigIntObject(BigInt(123));   // Returns false
util.types.isBigIntObject(123);  // Returns false
```

### `util.types.isBigUint64Array(value)`

Added in: v10.0.0

- `value` [`<any>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#Data_types)
- Returns: [`<boolean>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#boolean_type)

Returns `true` if the value is a `BigUint64Array` instance.

```js
util.types.isBigUint64Array(new BigInt64Array());   // Returns false
util.types.isBigUint64Array(new BigUint64Array());  // Returns true
```

### `util.types.isBooleanObject(value)`

Added in: v10.0.0

- `value` [`<any>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#Data_types)
- Returns: [`<boolean>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#boolean_type)

Returns `true` if the value is a boolean object, e.g. created
by `new Boolean()`.

```js
util.types.isBooleanObject(false);  // Returns false
util.types.isBooleanObject(true);   // Returns false
util.types.isBooleanObject(new Boolean(false)); // Returns true
util.types.isBooleanObject(new Boolean(true));  // Returns true
util.types.isBooleanObject(Boolean(false)); // Returns false
util.types.isBooleanObject(Boolean(true));  // Returns false
```

### `util.types.isBoxedPrimitive(value)`

Added in: v10.11.0

- `value` [`<any>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#Data_types)
- Returns: [`<boolean>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#boolean_type)

Returns `true` if the value is any boxed primitive object, e.g. created
by `new Boolean()`, `new String()` or `Object(Symbol())`.

For example:

```js
util.types.isBoxedPrimitive(false); // Returns false
util.types.isBoxedPrimitive(new Boolean(false)); // Returns true
util.types.isBoxedPrimitive(Symbol('foo')); // Returns false
util.types.isBoxedPrimitive(Object(Symbol('foo'))); // Returns true
util.types.isBoxedPrimitive(Object(BigInt(5))); // Returns true
```

### `util.types.isCryptoKey(value)`

Added in: v16.2.0

- `value` [`<Object>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Object)
- Returns: [`<boolean>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#boolean_type)

Returns `true` if `value` is a [`<CryptoKey>`](65-webcrypto.md#class-cryptokey), `false` otherwise.

### `util.types.isDataView(value)`

Added in: v10.0.0

- `value` [`<any>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#Data_types)
- Returns: [`<boolean>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#boolean_type)

Returns `true` if the value is a built-in [`<DataView>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/DataView) instance.

```js
const ab = new ArrayBuffer(20);
util.types.isDataView(new DataView(ab));  // Returns true
util.types.isDataView(new Float64Array());  // Returns false
```

See also [`ArrayBuffer.isView()`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/ArrayBuffer/isView).

### `util.types.isDate(value)`

Added in: v10.0.0

- `value` [`<any>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#Data_types)
- Returns: [`<boolean>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#boolean_type)

Returns `true` if the value is a built-in [`<Date>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Date) instance.

```js
util.types.isDate(new Date());  // Returns true
```

### `util.types.isExternal(value)`

Added in: v10.0.0

- `value` [`<any>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#Data_types)
- Returns: [`<boolean>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#boolean_type)

Returns `true` if the value is a native `External` value.

A native `External` value is a special type of object that contains a
raw C++ pointer (`void*`) for access from native code, and has no other
properties. Such objects are created either by Node.js internals or native
addons. In JavaScript, they are [frozen](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Object/freeze) objects with a
`null` prototype.

*ES module version:*

```js
import native from 'napi_addon.node';
import { types } from 'node:util';

const data = native.myNapi();
types.isExternal(data); // returns true
types.isExternal(0); // returns false
types.isExternal(new String('foo')); // returns false
```

*CommonJS version:*

```js
const native = require('napi_addon.node');
const { types } = require('node:util');

const data = native.myNapi();
types.isExternal(data); // returns true
types.isExternal(0); // returns false
types.isExternal(new String('foo')); // returns false
```

For further information on `napi_create_external`, refer to
[`napi_create_external()`](09-n-api-part2.md#napi_create_external).

### `util.types.isFloat16Array(value)`

Added in: v24.0.0, v22.16.0

- `value` [`<any>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#Data_types)
- Returns: [`<boolean>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#boolean_type)

Returns `true` if the value is a built-in [`<Float16Array>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Float16Array) instance.

```js
util.types.isFloat16Array(new ArrayBuffer());  // Returns false
util.types.isFloat16Array(new Float16Array());  // Returns true
util.types.isFloat16Array(new Float32Array());  // Returns false
```

[Contents](../README.md) | [Previous: Utilities, part 1 of 3](60-util-part1.md) | [Next: Utilities, part 3 of 3](60-util-part3.md)
