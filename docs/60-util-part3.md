[Contents](../README.md) | [Previous: Utilities, part 2 of 3](60-util-part2.md) | [Next: V8](61-v8.md)

Source: [https://nodejs.org/docs/latest/api/util.html](https://nodejs.org/docs/latest/api/util.html)

Part 3 of 3 of this section.

# Utilities (part 3 of 3)

Continues: util.types.

### `util.types.isFloat32Array(value)`

Added in: v10.0.0

- `value` [`<any>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#Data_types)
- Returns: [`<boolean>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#boolean_type)

Returns `true` if the value is a built-in [`<Float32Array>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Float32Array) instance.

```js
util.types.isFloat32Array(new ArrayBuffer());  // Returns false
util.types.isFloat32Array(new Float32Array());  // Returns true
util.types.isFloat32Array(new Float64Array());  // Returns false
```

### `util.types.isFloat64Array(value)`

Added in: v10.0.0

- `value` [`<any>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#Data_types)
- Returns: [`<boolean>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#boolean_type)

Returns `true` if the value is a built-in [`<Float64Array>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Float64Array) instance.

```js
util.types.isFloat64Array(new ArrayBuffer());  // Returns false
util.types.isFloat64Array(new Uint8Array());  // Returns false
util.types.isFloat64Array(new Float64Array());  // Returns true
```

### `util.types.isGeneratorFunction(value)`

Added in: v10.0.0

- `value` [`<any>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#Data_types)
- Returns: [`<boolean>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#boolean_type)

Returns `true` if the value is a generator function.
This only reports back what the JavaScript engine is seeing;
in particular, the return value may not match the original source code if
a transpilation tool was used.

```js
util.types.isGeneratorFunction(function foo() {});  // Returns false
util.types.isGeneratorFunction(function* foo() {});  // Returns true
```

### `util.types.isGeneratorObject(value)`

Added in: v10.0.0

- `value` [`<any>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#Data_types)
- Returns: [`<boolean>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#boolean_type)

Returns `true` if the value is a generator object as returned from a
built-in generator function.
This only reports back what the JavaScript engine is seeing;
in particular, the return value may not match the original source code if
a transpilation tool was used.

```js
function* foo() {}
const generator = foo();
util.types.isGeneratorObject(generator);  // Returns true
```

### `util.types.isInt8Array(value)`

Added in: v10.0.0

- `value` [`<any>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#Data_types)
- Returns: [`<boolean>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#boolean_type)

Returns `true` if the value is a built-in [`<Int8Array>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Int8Array) instance.

```js
util.types.isInt8Array(new ArrayBuffer());  // Returns false
util.types.isInt8Array(new Int8Array());  // Returns true
util.types.isInt8Array(new Float64Array());  // Returns false
```

### `util.types.isInt16Array(value)`

Added in: v10.0.0

- `value` [`<any>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#Data_types)
- Returns: [`<boolean>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#boolean_type)

Returns `true` if the value is a built-in [`<Int16Array>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Int16Array) instance.

```js
util.types.isInt16Array(new ArrayBuffer());  // Returns false
util.types.isInt16Array(new Int16Array());  // Returns true
util.types.isInt16Array(new Float64Array());  // Returns false
```

### `util.types.isInt32Array(value)`

Added in: v10.0.0

- `value` [`<any>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#Data_types)
- Returns: [`<boolean>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#boolean_type)

Returns `true` if the value is a built-in [`<Int32Array>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Int32Array) instance.

```js
util.types.isInt32Array(new ArrayBuffer());  // Returns false
util.types.isInt32Array(new Int32Array());  // Returns true
util.types.isInt32Array(new Float64Array());  // Returns false
```

### `util.types.isKeyObject(value)`

Added in: v16.2.0

- `value` [`<Object>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Object)
- Returns: [`<boolean>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#boolean_type)

Returns `true` if `value` is a [`<KeyObject>`](15-crypto-part2.md#class-keyobject), `false` otherwise.

### `util.types.isMap(value)`

Added in: v10.0.0

- `value` [`<any>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#Data_types)
- Returns: [`<boolean>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#boolean_type)

Returns `true` if the value is a built-in [`<Map>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Map) instance.

```js
util.types.isMap(new Map());  // Returns true
```

### `util.types.isMapIterator(value)`

Added in: v10.0.0

- `value` [`<any>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#Data_types)
- Returns: [`<boolean>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#boolean_type)

Returns `true` if the value is an iterator returned for a built-in
[`<Map>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Map) instance.

```js
const map = new Map();
util.types.isMapIterator(map.keys());  // Returns true
util.types.isMapIterator(map.values());  // Returns true
util.types.isMapIterator(map.entries());  // Returns true
util.types.isMapIterator(map[Symbol.iterator]());  // Returns true
```

### `util.types.isModuleNamespaceObject(value)`

Added in: v10.0.0

- `value` [`<any>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#Data_types)
- Returns: [`<boolean>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#boolean_type)

Returns `true` if the value is an instance of a [Module Namespace Object](https://tc39.github.io/ecma262/#sec-module-namespace-exotic-objects).

```js
import * as ns from './a.js';

util.types.isModuleNamespaceObject(ns);  // Returns true
```

### `util.types.isNativeError(value)`

Added in: v10.0.0Deprecated in: v24.2.0

Stability: 0 - Deprecated: Use [`Error.isError`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Error/isError) instead.

**Note:** As of Node.js 24, `Error.isError()` is currently slower than `util.types.isNativeError()`.
If performance is critical, consider benchmarking both in your environment.

- `value` [`<any>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#Data_types)
- Returns: [`<boolean>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#boolean_type)

Returns `true` if the value was returned by the constructor of a
[built-in `Error` type](https://tc39.es/ecma262/#sec-error-objects).

```js
console.log(util.types.isNativeError(new Error()));  // true
console.log(util.types.isNativeError(new TypeError()));  // true
console.log(util.types.isNativeError(new RangeError()));  // true
```

Subclasses of the native error types are also native errors:

```js
class MyError extends Error {}
console.log(util.types.isNativeError(new MyError()));  // true
```

A value being `instanceof` a native error class is not equivalent to `isNativeError()`
returning `true` for that value. `isNativeError()` returns `true` for errors
which come from a different [realm](https://tc39.es/ecma262/#realm) while `instanceof Error` returns `false`
for these errors:

*ES module version:*

```js
import { createContext, runInContext } from 'node:vm';
import { types } from 'node:util';

const context = createContext({});
const myError = runInContext('new Error()', context);
console.log(types.isNativeError(myError)); // true
console.log(myError instanceof Error); // false
```

*CommonJS version:*

```js
const { createContext, runInContext } = require('node:vm');
const { types } = require('node:util');

const context = createContext({});
const myError = runInContext('new Error()', context);
console.log(types.isNativeError(myError)); // true
console.log(myError instanceof Error); // false
```

Conversely, `isNativeError()` returns `false` for all objects which were not
returned by the constructor of a native error. That includes values
which are `instanceof` native errors:

```js
const myError = { __proto__: Error.prototype };
console.log(util.types.isNativeError(myError)); // false
console.log(myError instanceof Error); // true
```

### `util.types.isNumberObject(value)`

Added in: v10.0.0

- `value` [`<any>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#Data_types)
- Returns: [`<boolean>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#boolean_type)

Returns `true` if the value is a number object, e.g. created
by `new Number()`.

```js
util.types.isNumberObject(0);  // Returns false
util.types.isNumberObject(new Number(0));   // Returns true
```

### `util.types.isPromise(value)`

Added in: v10.0.0

- `value` [`<any>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#Data_types)
- Returns: [`<boolean>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#boolean_type)

Returns `true` if the value is a built-in [`<Promise>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Promise).

```js
util.types.isPromise(Promise.resolve(42));  // Returns true
```

### `util.types.isProxy(value)`

Added in: v10.0.0

- `value` [`<any>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#Data_types)
- Returns: [`<boolean>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#boolean_type)

Returns `true` if the value is a [`<Proxy>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Proxy) instance.

```js
const target = {};
const proxy = new Proxy(target, {});
util.types.isProxy(target);  // Returns false
util.types.isProxy(proxy);  // Returns true
```

### `util.types.isRegExp(value)`

Added in: v10.0.0

- `value` [`<any>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#Data_types)
- Returns: [`<boolean>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#boolean_type)

Returns `true` if the value is a regular expression object.

```js
util.types.isRegExp(/abc/);  // Returns true
util.types.isRegExp(new RegExp('abc'));  // Returns true
```

### `util.types.isSet(value)`

Added in: v10.0.0

- `value` [`<any>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#Data_types)
- Returns: [`<boolean>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#boolean_type)

Returns `true` if the value is a built-in [`<Set>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Set) instance.

```js
util.types.isSet(new Set());  // Returns true
```

### `util.types.isSetIterator(value)`

Added in: v10.0.0

- `value` [`<any>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#Data_types)
- Returns: [`<boolean>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#boolean_type)

Returns `true` if the value is an iterator returned for a built-in
[`<Set>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Set) instance.

```js
const set = new Set();
util.types.isSetIterator(set.keys());  // Returns true
util.types.isSetIterator(set.values());  // Returns true
util.types.isSetIterator(set.entries());  // Returns true
util.types.isSetIterator(set[Symbol.iterator]());  // Returns true
```

### `util.types.isSharedArrayBuffer(value)`

Added in: v10.0.0

- `value` [`<any>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#Data_types)
- Returns: [`<boolean>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#boolean_type)

Returns `true` if the value is a built-in [`<SharedArrayBuffer>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/SharedArrayBuffer) instance.
This does *not* include [`<ArrayBuffer>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/ArrayBuffer) instances. Usually, it is
desirable to test for both; See [`util.types.isAnyArrayBuffer()`](60-util-part2.md#utiltypesisanyarraybuffervalue) for that.

```js
util.types.isSharedArrayBuffer(new ArrayBuffer());  // Returns false
util.types.isSharedArrayBuffer(new SharedArrayBuffer());  // Returns true
```

### `util.types.isStringObject(value)`

Added in: v10.0.0

- `value` [`<any>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#Data_types)
- Returns: [`<boolean>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#boolean_type)

Returns `true` if the value is a string object, e.g. created
by `new String()`.

```js
util.types.isStringObject('foo');  // Returns false
util.types.isStringObject(new String('foo'));   // Returns true
```

### `util.types.isSymbolObject(value)`

Added in: v10.0.0

- `value` [`<any>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#Data_types)
- Returns: [`<boolean>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#boolean_type)

Returns `true` if the value is a symbol object, created
by calling `Object()` on a `Symbol` primitive.

```js
const symbol = Symbol('foo');
util.types.isSymbolObject(symbol);  // Returns false
util.types.isSymbolObject(Object(symbol));   // Returns true
```

### `util.types.isTypedArray(value)`

Added in: v10.0.0

- `value` [`<any>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#Data_types)
- Returns: [`<boolean>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#boolean_type)

Returns `true` if the value is a built-in [`<TypedArray>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/TypedArray) instance.

```js
util.types.isTypedArray(new ArrayBuffer());  // Returns false
util.types.isTypedArray(new Uint8Array());  // Returns true
util.types.isTypedArray(new Float64Array());  // Returns true
```

See also [`ArrayBuffer.isView()`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/ArrayBuffer/isView).

### `util.types.isUint8Array(value)`

Added in: v10.0.0

- `value` [`<any>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#Data_types)
- Returns: [`<boolean>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#boolean_type)

Returns `true` if the value is a built-in [`<Uint8Array>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Uint8Array) instance.

```js
util.types.isUint8Array(new ArrayBuffer());  // Returns false
util.types.isUint8Array(new Uint8Array());  // Returns true
util.types.isUint8Array(new Float64Array());  // Returns false
```

### `util.types.isUint8ClampedArray(value)`

Added in: v10.0.0

- `value` [`<any>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#Data_types)
- Returns: [`<boolean>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#boolean_type)

Returns `true` if the value is a built-in [`<Uint8ClampedArray>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Uint8ClampedArray) instance.

```js
util.types.isUint8ClampedArray(new ArrayBuffer());  // Returns false
util.types.isUint8ClampedArray(new Uint8ClampedArray());  // Returns true
util.types.isUint8ClampedArray(new Float64Array());  // Returns false
```

### `util.types.isUint16Array(value)`

Added in: v10.0.0

- `value` [`<any>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#Data_types)
- Returns: [`<boolean>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#boolean_type)

Returns `true` if the value is a built-in [`<Uint16Array>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Uint16Array) instance.

```js
util.types.isUint16Array(new ArrayBuffer());  // Returns false
util.types.isUint16Array(new Uint16Array());  // Returns true
util.types.isUint16Array(new Float64Array());  // Returns false
```

### `util.types.isUint32Array(value)`

Added in: v10.0.0

- `value` [`<any>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#Data_types)
- Returns: [`<boolean>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#boolean_type)

Returns `true` if the value is a built-in [`<Uint32Array>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Uint32Array) instance.

```js
util.types.isUint32Array(new ArrayBuffer());  // Returns false
util.types.isUint32Array(new Uint32Array());  // Returns true
util.types.isUint32Array(new Float64Array());  // Returns false
```

### `util.types.isWeakMap(value)`

Added in: v10.0.0

- `value` [`<any>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#Data_types)
- Returns: [`<boolean>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#boolean_type)

Returns `true` if the value is a built-in [`<WeakMap>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/WeakMap) instance.

```js
util.types.isWeakMap(new WeakMap());  // Returns true
```

### `util.types.isWeakSet(value)`

Added in: v10.0.0

- `value` [`<any>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#Data_types)
- Returns: [`<boolean>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#boolean_type)

Returns `true` if the value is a built-in [`<WeakSet>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/WeakSet) instance.

```js
util.types.isWeakSet(new WeakSet());  // Returns true
```

## Deprecated APIs

The following APIs are deprecated and should no longer be used. Existing
applications and modules should be updated to find alternative approaches.

### `util._extend(target, source)`

Added in: v0.7.5Deprecated in: v6.0.0

Stability: 0 - Deprecated: Use [`Object.assign()`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Object/assign) instead.

- `target` [`<Object>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Object)
- `source` [`<Object>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Object)

The `util._extend()` method was never intended to be used outside of internal
Node.js modules. The community found and used it anyway.

It is deprecated and should not be used in new code. JavaScript comes with very
similar built-in functionality through [`Object.assign()`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Object/assign).

An automated migration is available ([source](https://github.com/nodejs/userland-migrations/tree/main/recipes/util-extend-to-object-assign)):

```bash
npx codemod@latest @nodejs/util-extend-to-object-assign
```

### `util.isArray(object)`

Added in: v0.6.0Deprecated in: v4.0.0

Stability: 0 - Deprecated: Use [`Array.isArray()`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/isArray) instead.

- `object` [`<any>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#Data_types)
- Returns: [`<boolean>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#boolean_type)

Alias for [`Array.isArray()`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/isArray).

Returns `true` if the given `object` is an `Array`. Otherwise, returns `false`.

```js
const util = require('node:util');

util.isArray([]);
// Returns: true
util.isArray(new Array());
// Returns: true
util.isArray({});
// Returns: false
```

An automated migration is available ([source](https://github.com/nodejs/userland-migrations/tree/main/recipes/util-is)):

```bash
npx codemod@latest @nodejs/util-is
```

[Contents](../README.md) | [Previous: Utilities, part 2 of 3](60-util-part2.md) | [Next: V8](61-v8.md)
