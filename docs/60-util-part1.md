[Contents](../README.md) | [Previous: URL](59-url.md) | [Next: Utilities, part 2 of 3](60-util-part2.md)

Source: [https://nodejs.org/docs/latest/api/util.html](https://nodejs.org/docs/latest/api/util.html)

Part 1 of 3 of this section.

# Util

[Stability: 2](01-documentation.md#stability-index) - Stable

The `node:util` module supports the needs of Node.js internal APIs. Many of the
utilities are useful for application and module developers as well. To access
it:

*ES module version:*

```js
import util from 'node:util';
```

*CommonJS version:*

```js
const util = require('node:util');
```

## `util.callbackify(original)`

Added in: v8.2.0

- `original` [`<Function>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Function) An `async` function
- Returns: [`<Function>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Function) a callback style function

Takes an `async` function (or a function that returns a `Promise`) and returns a
function following the error-first callback style, i.e. taking
an `(err, value) => ...` callback as the last argument. In the callback, the
first argument will be the rejection reason (or `null` if the `Promise`
resolved), and the second argument will be the resolved value.

*ES module version:*

```js
import { callbackify } from 'node:util';

async function fn() {
  return 'hello world';
}
const callbackFunction = callbackify(fn);

callbackFunction((err, ret) => {
  if (err) throw err;
  console.log(ret);
});
```

*CommonJS version:*

```js
const { callbackify } = require('node:util');

async function fn() {
  return 'hello world';
}
const callbackFunction = callbackify(fn);

callbackFunction((err, ret) => {
  if (err) throw err;
  console.log(ret);
});
```

Will print:

```text
hello world
```

The callback is executed asynchronously, and will have a limited stack trace.
If the callback throws, the process will emit an [`'uncaughtException'`](43-process-part1.md#event-uncaughtexception)
event, and if not handled will exit.

Since `null` has a special meaning as the first argument to a callback, if a
wrapped function rejects a `Promise` with a falsy value as a reason, the value
is wrapped in an `Error` with the original value stored in a field named
`reason`.

*ES module version:*

```js
import util from 'node:util';

function fn() {
  return Promise.reject(null);
}
const callbackFunction = util.callbackify(fn);

callbackFunction((err, ret) => {
  // When the Promise was rejected with `null` it is wrapped with an Error and
  // the original value is stored in `reason`.
  err && Object.hasOwn(err, 'reason') && err.reason === null;  // true
});
```

*CommonJS version:*

```js
const util = require('node:util');

function fn() {
  return Promise.reject(null);
}
const callbackFunction = util.callbackify(fn);

callbackFunction((err, ret) => {
  // When the Promise was rejected with `null` it is wrapped with an Error and
  // the original value is stored in `reason`.
  err && Object.hasOwn(err, 'reason') && err.reason === null;  // true
});
```

## `util.convertProcessSignalToExitCode(signal)`

Added in: v25.4.0, v24.14.0

- `signal` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) A signal name (e.g. `'SIGTERM'`)
- Returns: [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) The exit code corresponding to `signal`

The `util.convertProcessSignalToExitCode()` method converts a signal name to its
corresponding POSIX exit code. Following the POSIX standard, the exit code
for a process terminated by a signal is calculated as `128 + signal number`.

If `signal` is not a valid signal name, then an error will be thrown. See
[`signal(7)`](https://man7.org/linux/man-pages/man7/signal.7.html) for a list of valid signals.

*ES module version:*

```js
import { convertProcessSignalToExitCode } from 'node:util';

console.log(convertProcessSignalToExitCode('SIGTERM')); // 143 (128 + 15)
console.log(convertProcessSignalToExitCode('SIGKILL')); // 137 (128 + 9)
```

*CommonJS version:*

```js
const { convertProcessSignalToExitCode } = require('node:util');

console.log(convertProcessSignalToExitCode('SIGTERM')); // 143 (128 + 15)
console.log(convertProcessSignalToExitCode('SIGKILL')); // 137 (128 + 9)
```

This is particularly useful when working with processes to determine
the exit code based on the signal that terminated the process.

## `util.debuglog(section[, callback])`

Added in: v0.11.3

- `section` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) A string identifying the portion of the application for
  which the `debuglog` function is being created.
- `callback` [`<Function>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Function) A callback invoked the first time the logging function
  is called with a function argument that is a more optimized logging function.
- Returns: [`<Function>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Function) The logging function

The `util.debuglog()` method is used to create a function that conditionally
writes debug messages to `stderr` based on the existence of the `NODE_DEBUG`
environment variable. If the `section` name appears within the value of that
environment variable, then the returned function operates similar to
[`console.error()`](14-console.md#consoleerrordata-args). If not, then the returned function is a no-op.

*ES module version:*

```js
import { debuglog } from 'node:util';
const log = debuglog('foo');

log('hello from foo [%d]', 123);
```

*CommonJS version:*

```js
const { debuglog } = require('node:util');
const log = debuglog('foo');

log('hello from foo [%d]', 123);
```

If this program is run with `NODE_DEBUG=foo` in the environment, then
it will output something like:

```console
FOO 3245: hello from foo [123]
```

where `3245` is the process id. If it is not run with that
environment variable set, then it will not print anything.

The `section` supports wildcard also:

*ES module version:*

```js
import { debuglog } from 'node:util';
const log = debuglog('foo-bar');

log('hi there, it\'s foo-bar [%d]', 2333);
```

*CommonJS version:*

```js
const { debuglog } = require('node:util');
const log = debuglog('foo-bar');

log('hi there, it\'s foo-bar [%d]', 2333);
```

if it is run with `NODE_DEBUG=foo*` in the environment, then it will output
something like:

```console
FOO-BAR 3257: hi there, it's foo-bar [2333]
```

Multiple comma-separated `section` names may be specified in the `NODE_DEBUG`
environment variable: `NODE_DEBUG=fs,net,tls`.

The optional `callback` argument can be used to replace the logging function
with a different function that doesn't have any initialization or
unnecessary wrapping.

*ES module version:*

```js
import { debuglog } from 'node:util';
let log = debuglog('internals', (debug) => {
  // Replace with a logging function that optimizes out
  // testing if the section is enabled
  log = debug;
});
```

*CommonJS version:*

```js
const { debuglog } = require('node:util');
let log = debuglog('internals', (debug) => {
  // Replace with a logging function that optimizes out
  // testing if the section is enabled
  log = debug;
});
```

### `debuglog().enabled`

Added in: v14.9.0

- Type: [`<boolean>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#boolean_type)

The `util.debuglog().enabled` getter is used to create a test that can be used
in conditionals based on the existence of the `NODE_DEBUG` environment variable.
If the `section` name appears within the value of that environment variable,
then the returned value will be `true`. If not, then the returned value will be
`false`.

*ES module version:*

```js
import { debuglog } from 'node:util';
const enabled = debuglog('foo').enabled;
if (enabled) {
  console.log('hello from foo [%d]', 123);
}
```

*CommonJS version:*

```js
const { debuglog } = require('node:util');
const enabled = debuglog('foo').enabled;
if (enabled) {
  console.log('hello from foo [%d]', 123);
}
```

If this program is run with `NODE_DEBUG=foo` in the environment, then it will
output something like:

```console
hello from foo [123]
```

## `util.debug(section)`

Added in: v14.9.0

Alias for `util.debuglog`. Usage allows for readability of that doesn't imply
logging when only using `util.debuglog().enabled`.

## `util.deprecate(fn, msg[, code[, options]])`

Added in: v0.8.0

**History**

| Version | Changes |
| --- | --- |
| v25.2.0, v24.12.0 | Add options object with modifyPrototype to conditionally modify the prototype of the deprecated object. |
| v10.0.0 | Deprecation warnings are only emitted once for each code. |

- `fn` [`<Function>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Function) The function that is being deprecated.
- `msg` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) A warning message to display when the deprecated function is
  invoked.
- `code` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) A deprecation code. See the [list of deprecated APIs](17-deprecations.md#list-of-deprecated-apis) for a
  list of codes.
- `options` [`<Object>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Object)
  - `modifyPrototype` [`<boolean>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#boolean_type) When false do not change the prototype of object
    while emitting the deprecation warning. **Default:** `true`.
- Returns: [`<Function>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Function) The deprecated function wrapped to emit a warning.

The `util.deprecate()` method wraps `fn` (which may be a function or class) in
such a way that it is marked as deprecated.

*ES module version:*

```js
import { deprecate } from 'node:util';

export const obsoleteFunction = deprecate(() => {
  // Do something here.
}, 'obsoleteFunction() is deprecated. Use newShinyFunction() instead.');
```

*CommonJS version:*

```js
const { deprecate } = require('node:util');

exports.obsoleteFunction = deprecate(() => {
  // Do something here.
}, 'obsoleteFunction() is deprecated. Use newShinyFunction() instead.');
```

When called, `util.deprecate()` will return a function that will emit a
`DeprecationWarning` using the [`'warning'`](43-process-part1.md#event-warning) event. The warning will
be emitted and printed to `stderr` the first time the returned function is
called. After the warning is emitted, the wrapped function is called without
emitting a warning.

If the same optional `code` is supplied in multiple calls to `util.deprecate()`,
the warning will be emitted only once for that `code`.

*ES module version:*

```js
import { deprecate } from 'node:util';

const fn1 = deprecate(
  () => 'a value',
  'deprecation message',
  'DEP0001',
);
const fn2 = deprecate(
  () => 'a  different value',
  'other dep message',
  'DEP0001',
);
fn1(); // Emits a deprecation warning with code DEP0001
fn2(); // Does not emit a deprecation warning because it has the same code
```

*CommonJS version:*

```js
const { deprecate } = require('node:util');

const fn1 = deprecate(
  function() {
    return 'a value';
  },
  'deprecation message',
  'DEP0001',
);
const fn2 = deprecate(
  function() {
    return 'a  different value';
  },
  'other dep message',
  'DEP0001',
);
fn1(); // Emits a deprecation warning with code DEP0001
fn2(); // Does not emit a deprecation warning because it has the same code
```

If either the `--no-deprecation` or `--no-warnings` command-line flags are
used, or if the `process.noDeprecation` property is set to `true` *prior* to
the first deprecation warning, the `util.deprecate()` method does nothing.

If the `--trace-deprecation` or `--trace-warnings` command-line flags are set,
or the `process.traceDeprecation` property is set to `true`, a warning and a
stack trace are printed to `stderr` the first time the deprecated function is
called.

If the `--throw-deprecation` command-line flag is set, or the
`process.throwDeprecation` property is set to `true`, then an exception will be
thrown when the deprecated function is called.

The `--throw-deprecation` command-line flag and `process.throwDeprecation`
property take precedence over `--trace-deprecation` and
`process.traceDeprecation`.

## `util.debounce(fn, wait[, options])`

Added in: v26.10.0

- `fn` [`<Function>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Function) The function to debounce.
- `wait` [`<integer>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) The number of milliseconds to delay `fn`.
- `options` [`<Object>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Object)
  - `leading` [`<boolean>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#boolean_type) When `true`, invokes `fn` immediately when a new
    debounce window begins. **Default:** `false`.
  - `rejectOnCancel` [`<boolean>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#boolean_type) When `true`, a call superseded by a later call
    rejects with an `AbortError`. **Default:** `false`.
  - `signal` [`<AbortSignal>`](26-globals.md#class-abortsignal) An `AbortSignal` that cancels pending calls and
    prevents future calls when aborted.
- Returns: [`<Function>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Function) The debounced function.

Creates a function that delays calling `fn` until `wait` milliseconds have
elapsed since the most recent invocation. The debounced function returns a
[`<Promise>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Promise) for the value returned by `fn`. If `fn` throws or returns a rejected
promise, the returned promise is rejected with the same reason.

When the debounced function is called more than once before the delay expires,
`fn` receives the arguments from the most recent call. By default, the promises
from all calls resolve or reject with the result of that invocation. If
`options.rejectOnCancel` is `true`, the promises from superseded calls reject
with an `AbortError` instead.

When `options.leading` is `true`, the first call in a debounce window invokes
`fn` immediately. Calls made during that window are delayed until `wait`
milliseconds have elapsed since the most recent call. A trailing invocation
only occurs if the debounced function was called again during the window.
The window begins before `fn` is invoked, so recursive calls and calls made
while an asynchronous `fn` is pending are part of the same window if they occur
before the delay expires. This also applies to calls made after a synchronous
`fn` returns but before the delay expires.

If `options.signal` is aborted, pending and future calls reject with an
`AbortError`, with the signal's reason set as the error's `cause`, and `fn` is
not invoked by those calls. If the signal is already aborted, `debounce()`
throws an `AbortError`.

The returned function has the following properties:

- `cancel([reason])` cancels the current debounce window. Its pending promises
  reject with an `AbortError`. If provided, `reason` is set as the error's
  `cause`.
- `flush()` cancels the delay and invokes `fn` immediately. It has no effect if
  no invocation is pending.
- `pending` [`<Promise>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Promise) | [`<null>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#null_type) is the promise returned by the most recent call in
  the current debounce window, or `null` if no invocation is pending.
- `pendingCount` [`<integer>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) is the number of calls awaiting the invocation in
  the current debounce window.
- `ref()` makes the pending and future timeout keep the Node.js event loop
  active. Returns the debounced function.
- `unref()` allows the event loop to exit while a timeout is pending. This also
  applies to future timeouts. Returns the debounced function.

When invoked, `fn` has the debounced function as its `this` value. After a
trailing invocation, a new debounce window can begin even if a promise returned
by `fn` is still pending. The debounced function preserves the `name` and
`length` of `fn`.

```js
import { setTimeout as wait } from 'node:timers/promises';
import { debounce } from 'node:util';

const fn = debounce(async (value) => {
  await wait(100);
  return value;
}, 50);

const first = fn(1);
const second = fn(2);

console.log(await first);  // 2
console.log(await second); // 2
```

A debounced function can be used to trigger an action after a period of
inactivity. Each call resets the timeout:

```js
const { debounce } = require('node:util');

const onInactivity = debounce(() => {
  console.log('No activity for 5 seconds');
}, 5_000).unref();

process.stdin.on('data', (data) => {
  console.log(`Received ${data.length} bytes`);
  onInactivity();
});

// Start the initial inactivity timeout.
onInactivity();
```

## `util.throttle(fn, limit, interval[, options])`

Added in: v26.10.0

- `fn` [`<Function>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Function) The function to throttle.
- `limit` [`<integer>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) The maximum number of times to invoke `fn` during an
  interval. Must be greater than `0`.
- `interval` [`<integer>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) The length of each interval in milliseconds.
- `options` [`<Object>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Object)
  - `concurrency` [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) The maximum number of invocations of `fn` whose
    return values may be unsettled at once. Must be a positive integer or
    `Infinity`. **Default:** `Infinity`.
  - `maxPending` [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) The maximum number of calls that may be queued when `overflow` is `'queue'`. Must be a non-negative integer or `Infinity`.
    **Default:** `Infinity`.
  - `overflow` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) Determines how calls exceeding the limit are handled. **Default:** `'queue'`.
    - `'queue'`: Queue calls in the order received.
    - `'drop'`: Reject calls immediately without queueing them.
  - `signal` [`<AbortSignal>`](26-globals.md#class-abortsignal) An `AbortSignal` that cancels pending calls and
    prevents future calls when aborted.
  - `strict` [`<boolean>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#boolean_type) When `true`, ensures that `limit` is not exceeded during
    any rolling interval. **Default:** `false`.
- Returns: [`<Function>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Function) The throttled function.

Creates a function that limits how often `fn` is invoked. By default, calls that
exceed the limit are queued in the order received rather than discarded. The
throttled function returns a [`<Promise>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Promise) for the value returned by `fn`. If `fn`
throws or returns a rejected promise, the returned promise is rejected with the
same reason.

An invocation starts only when both rate and concurrency capacity are
available. Rate capacity is consumed when `fn` starts, not when a call enters
the queue. Concurrency capacity is released when the value returned by `fn`
settles. Non-promise values settle during the next microtask.

When `options.overflow` is `'drop'`, calls made without available rate or
concurrency capacity are rejected immediately. When `options.overflow` is
`'queue'` and `options.maxPending` calls are already queued, additional calls
are also rejected immediately. `maxPending` has no effect when `overflow` is
`'drop'`.

In both cases, rejected calls return a promise rejected with an
`ERR_THROTTLED` error. The rejected promise is marked as handled, so ignoring it
does not emit an `'unhandledRejection'` event. Awaiting or explicitly handling
the promise still observes the rejection. Rejected calls do not consume rate
or concurrency capacity, enter the queue, or schedule a timeout.

By default, the interval begins when the first call in a new window invokes
`fn`. Up to `limit` calls can invoke `fn` during that window. Queued calls are
processed in groups of up to `limit` as each subsequent window begins. This
windowed behavior can result in calls occurring close together at a window
boundary.

When `options.strict` is `true`, invocation times are tracked individually.
This ensures that no more than `limit` calls begin during any rolling interval,
at the cost of additional bookkeeping.

If `options.signal` is aborted, pending and future calls reject with an
`AbortError`, with the signal's reason set as the error's `cause`, and `fn` is
not invoked by those calls. If the signal is already aborted, `throttle()`
throws an `AbortError`.

The returned function has the following properties:

- `cancel([reason])` cancels all queued calls and resets the current throttle
  window. The queued promises reject with an `AbortError`. If provided,
  `reason` is set as the error's `cause`. Does not cancel invocations that have
  already started.
- `hasImmediateCapacity()` returns `true` if a call made at that moment could
  invoke `fn` without being queued or rejected. The check does not reserve
  capacity, and the throttled function always checks again when called. It
  returns `false` while calls are queued to preserve their order. Callers can
  avoid creating a timeout by only calling the throttled function when this
  method returns `true`.
- `pending` [`<Promise>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Promise) | [`<null>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#null_type) is the promise returned by the most recently queued
  call, or `null` if no invocation is queued.
- `pendingCount` [`<integer>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) is the number of calls awaiting invocation.
- `activeCount` [`<integer>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) is the number of invocations whose return values have
  not settled.
- `ref()` makes the pending and future timeout keep the Node.js event loop
  active. Returns the throttled function.
- `unref()` allows the event loop to exit while a timeout is pending. This also
  applies to future timeouts. Returns the throttled function.

Calls that have already invoked `fn` are not affected by `cancel()` or by an
aborted signal. When invoked, `fn` has the throttled function as its `this`
value. The throttled function preserves the `name` and `length` of `fn`.

```js
import { throttle } from 'node:util';

const request = throttle(async (id) => {
  const response = await fetch(`https://example.com/items/${id}`);
  return response.json();
}, 2, 1_000);

// At most two requests begin during each one-second interval. All other calls
// remain queued and retain their original arguments.
const results = await Promise.all([
  request(1),
  request(2),
  request(3),
  request(4),
]);
```

## `util.diff(actual, expected)`

Added in: v23.11.0, v22.15.0

Stability: 1 - Experimental

- `actual` [`<Array>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Array) | [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) The first value to compare
- `expected` [`<Array>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Array) | [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) The second value to compare
- Returns: [`<Array>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Array) An array of difference entries. Each entry is an array with two elements:

  - `0` [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) Operation code: `-1` for delete, `0` for no-op/unchanged, `1` for insert
  - `1` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) The value associated with the operation
- Algorithm complexity: O(N\*D), where:
- N is the total length of the two sequences combined (N = actual.length + expected.length)
- D is the edit distance (the minimum number of operations required to transform one sequence into the other).

[`util.diff()`](#utildiffactual-expected) compares two string or array values and returns an array of difference entries.
It uses the Myers diff algorithm to compute minimal differences, which is the same algorithm
used internally by assertion error messages.

If the values are equal, an empty array is returned.

```js
const { diff } = require('node:util');

// Comparing strings
const actualString = '12345678';
const expectedString = '12!!5!7!';
console.log(diff(actualString, expectedString));
// [
//   [0, '1'],
//   [0, '2'],
//   [1, '3'],
//   [1, '4'],
//   [-1, '!'],
//   [-1, '!'],
//   [0, '5'],
//   [1, '6'],
//   [-1, '!'],
//   [0, '7'],
//   [1, '8'],
//   [-1, '!'],
// ]
// Comparing arrays
const actualArray = ['1', '2', '3'];
const expectedArray = ['1', '3', '4'];
console.log(diff(actualArray, expectedArray));
// [
//   [0, '1'],
//   [1, '2'],
//   [0, '3'],
//   [-1, '4'],
// ]
// Equal values return empty array
console.log(diff('same', 'same'));
// []
```

## `util.format(format[, ...args])`

Added in: v0.5.3

**History**

| Version | Changes |
| --- | --- |
| v12.11.0 | The `%c` specifier is ignored now. |
| v12.0.0 | The `format` argument is now only taken as such if it actually contains format specifiers. |
| v12.0.0 | If the `format` argument is not a format string, the output string's formatting is no longer dependent on the type of the first argument. This change removes previously present quotes from strings that were being output when the first argument was not a string. |
| v11.4.0 | The `%d`, `%f`, and `%i` specifiers now support Symbols properly. |
| v11.4.0 | The `%o` specifier's `depth` has default depth of 4 again. |
| v11.0.0 | The `%o` specifier's `depth` option will now fall back to the default depth. |
| v10.12.0 | The `%d` and `%i` specifiers now support BigInt. |
| v8.4.0 | The `%o` and `%O` specifiers are supported now. |

- `format` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) A `printf`-like format string.

The `util.format()` method returns a formatted string using the first argument
as a `printf`-like format string which can contain zero or more format
specifiers. Each specifier is replaced with the converted value from the
corresponding argument. Supported specifiers are:

- `%s`: `String` will be used to convert all values except `BigInt`, `Object`
  and `-0`. `BigInt` values will be represented with an `n` and Objects that
  have neither a user defined `toString` function nor `Symbol.toPrimitive` function are inspected using `util.inspect()`
  with options `{ depth: 0, colors: false, compact: 3 }`.
- `%d`: `Number` will be used to convert all values except `BigInt` and
  `Symbol`.
- `%i`: `parseInt(value, 10)` is used for all values except `BigInt` and
  `Symbol`.
- `%f`: `parseFloat(value)` is used for all values except `Symbol`.
- `%j`: JSON. Replaced with the string `'[Circular]'` if the argument contains
  circular references.
- `%o`: `Object`. A string representation of an object with generic JavaScript
  object formatting. Similar to `util.inspect()` with options
  `{ showHidden: true, showProxy: true }`. This will show the full object
  including non-enumerable properties and proxies.
- `%O`: `Object`. A string representation of an object with generic JavaScript
  object formatting. Similar to `util.inspect()` without options. This will show
  the full object not including non-enumerable properties and proxies.
- `%c`: `CSS`. This specifier is ignored and will skip any CSS passed in.
- `%%`: single percent sign (`'%'`). This does not consume an argument.
- Returns: [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) The formatted string

If a specifier does not have a corresponding argument, it is not replaced:

```js
util.format('%s:%s', 'foo');
// Returns: 'foo:%s'
```

Values that are not part of the format string are formatted using
`util.inspect()` if their type is not `string`.

If there are more arguments passed to the `util.format()` method than the
number of specifiers, the extra arguments are concatenated to the returned
string, separated by spaces:

```js
util.format('%s:%s', 'foo', 'bar', 'baz');
// Returns: 'foo:bar baz'
```

If the first argument does not contain a valid format specifier, `util.format()`
returns a string that is the concatenation of all arguments separated by spaces:

```js
util.format(1, 2, 3);
// Returns: '1 2 3'
```

If only one argument is passed to `util.format()`, it is returned as it is
without any formatting:

```js
util.format('%% %s');
// Returns: '%% %s'
```

`util.format()` is a synchronous method that is intended as a debugging tool.
Some input values can have a significant performance overhead that can block the
event loop. Use this function with care and never in a hot code path.

## `util.formatWithOptions(inspectOptions, format[, ...args])`

Added in: v10.0.0

- `inspectOptions` [`<Object>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Object)
- `format` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type)

This function is identical to [`util.format()`](#utilformatformat-args), except in that it takes
an `inspectOptions` argument which specifies options that are passed along to
[`util.inspect()`](#utilinspectobject-options).

```js
util.formatWithOptions({ colors: true }, 'See object %O', { foo: 42 });
// Returns 'See object { foo: 42 }', where `42` is colored as a number
// when printed to a terminal.
```

## `util.getCallSites([frameCount][, options])`

Added in: v22.9.0

**History**

| Version | Changes |
| --- | --- |
| v23.7.0, v22.14.0 | Property `column` is deprecated in favor of `columnNumber`. |
| v23.7.0, v22.14.0 | Property `CallSite.scriptId` is exposed. |
| v23.3.0, v22.12.0 | The API is renamed from `util.getCallSite` to `util.getCallSites()`. |

Stability: 1.1 - Active development

- `frameCount` [`<integer>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) Optional number of frames to capture as call site objects. **Default:** `10`. Allowable range is between 1 and 200.
- `options` [`<Object>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Object) Optional
  - `sourceMap` [`<boolean>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#boolean_type) Reconstruct the original location in the stacktrace from the source-map.
    Enabled by default with the flag `--enable-source-maps`.
- Returns: [`<Object>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Object)[] An array of call site objects
  - `functionName` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) Returns the name of the function associated with this call site.
  - `scriptName` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) Returns the name of the resource that contains the script for the
    function for this call site.
  - `scriptId` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) Returns the unique id of the script, as in Chrome DevTools protocol [`Runtime.ScriptId`](https://chromedevtools.github.io/devtools-protocol/1-3/Runtime/#type-ScriptId).
  - `lineNumber` [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) Returns the JavaScript script line number (1-based).
  - `columnNumber` [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) Returns the JavaScript script column number (1-based).

Returns an array of call site objects containing the stack of
the caller function.

Unlike accessing an `error.stack`, the result returned from this API is not
interfered with `Error.prepareStackTrace`.

*ES module version:*

```js
import { getCallSites } from 'node:util';

function exampleFunction() {
  const callSites = getCallSites();

  console.log('Call Sites:');
  callSites.forEach((callSite, index) => {
    console.log(`CallSite ${index + 1}:`);
    console.log(`Function Name: ${callSite.functionName}`);
    console.log(`Script Name: ${callSite.scriptName}`);
    console.log(`Line Number: ${callSite.lineNumber}`);
    console.log(`Column Number: ${callSite.columnNumber}`);
  });
  // CallSite 1:
  // Function Name: exampleFunction
  // Script Name: /home/example.js
  // Line Number: 5
  // Column Number: 26

  // CallSite 2:
  // Function Name: anotherFunction
  // Script Name: /home/example.js
  // Line Number: 22
  // Column Number: 3

  // ...
}

// A function to simulate another stack layer
function anotherFunction() {
  exampleFunction();
}

anotherFunction();
```

*CommonJS version:*

```js
const { getCallSites } = require('node:util');

function exampleFunction() {
  const callSites = getCallSites();

  console.log('Call Sites:');
  callSites.forEach((callSite, index) => {
    console.log(`CallSite ${index + 1}:`);
    console.log(`Function Name: ${callSite.functionName}`);
    console.log(`Script Name: ${callSite.scriptName}`);
    console.log(`Line Number: ${callSite.lineNumber}`);
    console.log(`Column Number: ${callSite.columnNumber}`);
  });
  // CallSite 1:
  // Function Name: exampleFunction
  // Script Name: /home/example.js
  // Line Number: 5
  // Column Number: 26

  // CallSite 2:
  // Function Name: anotherFunction
  // Script Name: /home/example.js
  // Line Number: 22
  // Column Number: 3

  // ...
}

// A function to simulate another stack layer
function anotherFunction() {
  exampleFunction();
}

anotherFunction();
```

It is possible to reconstruct the original locations by setting the option `sourceMap` to `true`.
If the source map is not available, the original location will be the same as the current location.
When the `--enable-source-maps` flag is enabled,`sourceMap` will be true by default.

```ts
import { getCallSites } from 'node:util';

interface Foo {
  foo: string;
}

const callSites = getCallSites({ sourceMap: true });

// With sourceMap:
// Function Name: ''
// Script Name: example.js
// Line Number: 7
// Column Number: 26

// Without sourceMap:
// Function Name: ''
// Script Name: example.js
// Line Number: 2
// Column Number: 26
```

```js
const { getCallSites } = require('node:util');

const callSites = getCallSites({ sourceMap: true });

// With sourceMap:
// Function Name: ''
// Script Name: example.js
// Line Number: 7
// Column Number: 26

// Without sourceMap:
// Function Name: ''
// Script Name: example.js
// Line Number: 2
// Column Number: 26
```

## `util.getSystemErrorName(err)`

Added in: v9.7.0

- `err` [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type)
- Returns: [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type)

Returns the string name for a numeric error code that comes from a Node.js API.
The mapping between error codes and error names is platform-dependent.
See [Common System Errors](22-errors-part1.md#common-system-errors) for the names of common errors.

```js
fs.access('file/that/does/not/exist', (err) => {
  const name = util.getSystemErrorName(err.errno);
  console.error(name);  // ENOENT
});
```

## `util.getSystemErrorMap()`

Added in: v16.0.0, v14.17.0

- Returns: [`<Map>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Map)

Returns a Map of all system error codes available from the Node.js API.
The mapping between error codes and error names is platform-dependent.
See [Common System Errors](22-errors-part1.md#common-system-errors) for the names of common errors.

```js
fs.access('file/that/does/not/exist', (err) => {
  const errorMap = util.getSystemErrorMap();
  const name = errorMap.get(err.errno);
  console.error(name);  // ENOENT
});
```

## `util.getSystemErrorMessage(err)`

Added in: v23.1.0, v22.12.0

- `err` [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type)
- Returns: [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type)

Returns the string message for a numeric error code that comes from a Node.js
API.
The mapping between error codes and string messages is platform-dependent.

```js
fs.access('file/that/does/not/exist', (err) => {
  const message = util.getSystemErrorMessage(err.errno);
  console.error(message);  // No such file or directory
});
```

## `util.setTraceSigInt(enable)`

Added in: v24.6.0, v22.19.0

- `enable` [`<boolean>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#boolean_type)

Enable or disable printing a stack trace on `SIGINT`. The API is only available on the main thread.

## `util.inherits(constructor, superConstructor)`

Added in: v0.3.0

**History**

| Version | Changes |
| --- | --- |
| v5.0.0 | The `constructor` parameter can refer to an ES6 class now. |

Stability: 3 - Legacy: Use ES2015 class syntax and `extends` keyword instead.

- `constructor` [`<Function>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Function)
- `superConstructor` [`<Function>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Function)

Usage of `util.inherits()` is discouraged. Please use the ES6 `class` and
`extends` keywords to get language level inheritance support. Also note
that the two styles are [semantically incompatible](https://github.com/nodejs/node/issues/4179).

Inherit the prototype methods from one [constructor](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Object/constructor) into another. The
prototype of `constructor` will be set to a new object created from
`superConstructor`.

This mainly adds some input validation on top of
`Object.setPrototypeOf(constructor.prototype, superConstructor.prototype)`.
As an additional convenience, `superConstructor` will be accessible
through the `constructor.super_` property.

```js
const util = require('node:util');
const EventEmitter = require('node:events');

function MyStream() {
  EventEmitter.call(this);
}

util.inherits(MyStream, EventEmitter);

MyStream.prototype.write = function(data) {
  this.emit('data', data);
};

const stream = new MyStream();

console.log(stream instanceof EventEmitter); // true
console.log(MyStream.super_ === EventEmitter); // true

stream.on('data', (data) => {
  console.log(`Received data: "${data}"`);
});
stream.write('It works!'); // Received data: "It works!"
```

ES6 example using `class` and `extends`:

*ES module version:*

```js
import EventEmitter from 'node:events';

class MyStream extends EventEmitter {
  write(data) {
    this.emit('data', data);
  }
}

const stream = new MyStream();

stream.on('data', (data) => {
  console.log(`Received data: "${data}"`);
});
stream.write('With ES6');
```

*CommonJS version:*

```js
const EventEmitter = require('node:events');

class MyStream extends EventEmitter {
  write(data) {
    this.emit('data', data);
  }
}

const stream = new MyStream();

stream.on('data', (data) => {
  console.log(`Received data: "${data}"`);
});
stream.write('With ES6');
```

## `util.inspect(object[, options])`

## `util.inspect(object[, showHidden[, depth[, colors]]])`

Added in: v0.3.0

**History**

| Version | Changes |
| --- | --- |
| v25.0.0 | The util.inspect.styles.regexp style is now a method that is invoked for coloring the stringified regular expression. |
| v17.3.0, v16.14.0 | The `numericSeparator` option is supported now. |
| v16.18.0 | add support for `maxArrayLength` when inspecting `Set` and `Map`. |
| v14.6.0, v12.19.0 | If `object` is from a different `vm.Context` now, a custom inspection function on it will not receive context-specific arguments anymore. |
| v13.13.0, v12.17.0 | The `maxStringLength` option is supported now. |
| v13.5.0, v12.16.0 | User defined prototype properties are inspected in case `showHidden` is `true`. |
| v13.0.0 | Circular references now include a marker to the reference. |
| v12.0.0 | The `compact` options default is changed to `3` and the `breakLength` options default is changed to `80`. |
| v12.0.0 | Internal properties no longer appear in the context argument of a custom inspection function. |
| v11.11.0 | The `compact` option accepts numbers for a new output mode. |
| v11.7.0 | ArrayBuffers now also show their binary contents. |
| v11.5.0 | The `getters` option is supported now. |
| v11.4.0 | The `depth` default changed back to `2`. |
| v11.0.0 | The `depth` default changed to `20`. |
| v11.0.0 | The inspection output is now limited to about 128 MiB. Data above that size will not be fully inspected. |
| v10.12.0 | The `sorted` option is supported now. |
| v10.6.0 | Inspecting linked lists and similar objects is now possible up to the maximum call stack size. |
| v10.0.0 | The `WeakMap` and `WeakSet` entries can now be inspected as well. |
| v9.9.0 | The `compact` option is supported now. |
| v6.6.0 | Custom inspection functions can now return `this`. |
| v6.3.0 | The `breakLength` option is supported now. |
| v6.1.0 | The `maxArrayLength` option is supported now; in particular, long arrays are truncated by default. |
| v6.1.0 | The `showProxy` option is supported now. |

- `object` [`<any>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#Data_types) Any JavaScript primitive or `Object`.
- `options` [`<Object>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Object)
  - `showHidden` [`<boolean>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#boolean_type) If `true`, `object`'s non-enumerable symbols and
    properties are included in the formatted result. [`<WeakMap>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/WeakMap) and
    [`<WeakSet>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/WeakSet) entries are also included as well as user defined prototype
    properties (excluding method properties). **Default:** `false`.
  - `depth` [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) Specifies the number of times to recurse while formatting `object`. This is useful for inspecting large objects. To recurse up to
    the maximum call stack size pass `Infinity` or `null`.
    **Default:** `2`.
  - `colors` [`<boolean>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#boolean_type) If `true`, the output is styled with ANSI color
    codes. Colors are customizable. See [Customizing `util.inspect` colors](#customizing-utilinspect-colors).
    **Default:** `false`.
  - `customInspect` [`<boolean>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#boolean_type) If `false`,
    `[util.inspect.custom](depth, opts, inspect)` functions are not invoked.
    **Default:** `true`.
  - `showProxy` [`<boolean>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#boolean_type) If `true`, `Proxy` inspection includes
    the [`target` and `handler`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Proxy#terminology) objects. **Default:** `false`.
  - `maxArrayLength` [`<integer>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) Specifies the maximum number of `Array`,
    [`<TypedArray>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/TypedArray), [`<Map>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Map), [`<WeakMap>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/WeakMap), and [`<WeakSet>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/WeakSet) elements to include when formatting.
    Set to `null` or `Infinity` to show all elements. Set to `0` or
    negative to show no elements. **Default:** `100`.
  - `maxStringLength` [`<integer>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) Specifies the maximum number of characters to
    include when formatting. Set to `null` or `Infinity` to show all elements.
    Set to `0` or negative to show no characters. **Default:** `10000`.
  - `breakLength` [`<integer>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) The length at which input values are split across
    multiple lines. Set to `Infinity` to format the input as a single line
    (in combination with `compact` set to `true` or any number >= `1`).
    **Default:** `80`.
  - `compact` [`<boolean>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#boolean_type) | [`<integer>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) Setting this to `false` causes each object key
    to be displayed on a new line. It will break on new lines in text that is
    longer than `breakLength`. If set to a number, the most `n` inner elements
    are united on a single line as long as all properties fit into
    `breakLength`. Short array elements are also grouped together. For more
    information, see the example below. **Default:** `3`.
  - `sorted` [`<boolean>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#boolean_type) | [`<Function>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Function) If set to `true` or a function, all properties
    of an object, and `Set` and `Map` entries are sorted in the resulting
    string. If set to `true` the [default sort](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/sort) is used. If set to a function,
    it is used as a [compare function](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/sort#parameters).
  - `getters` [`<boolean>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#boolean_type) | [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) If set to `true`, getters are inspected. If set
    to `'get'`, only getters without a corresponding setter are inspected. If
    set to `'set'`, only getters with a corresponding setter are inspected.
    This might cause side effects depending on the getter function.
    **Default:** `false`.
  - `numericSeparator` [`<boolean>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#boolean_type) If set to `true`, an underscore is used to
    separate every three digits in all bigints and numbers.
    **Default:** `false`.
- Returns: [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) The representation of `object`.

The `util.inspect()` method returns a string representation of `object` that is
intended for debugging. The output of `util.inspect` may change at any time
and should not be depended upon programmatically. Additional `options` may be
passed that alter the result.
`util.inspect()` will use the constructor's name and/or `Symbol.toStringTag`
property to make an identifiable tag for an inspected value.

```js
class Foo {
  get [Symbol.toStringTag]() {
    return 'bar';
  }
}

class Bar {}

const baz = Object.create(null, { [Symbol.toStringTag]: { value: 'foo' } });

util.inspect(new Foo()); // 'Foo [bar] {}'
util.inspect(new Bar()); // 'Bar {}'
util.inspect(baz);       // '[foo] {}'
```

Circular references point to their anchor by using a reference index:

*ES module version:*

```js
import { inspect } from 'node:util';

const obj = {};
obj.a = [obj];
obj.b = {};
obj.b.inner = obj.b;
obj.b.obj = obj;

console.log(inspect(obj));
// <ref *1> {
//   a: [ [Circular *1] ],
//   b: <ref *2> { inner: [Circular *2], obj: [Circular *1] }
// }
```

*CommonJS version:*

```js
const { inspect } = require('node:util');

const obj = {};
obj.a = [obj];
obj.b = {};
obj.b.inner = obj.b;
obj.b.obj = obj;

console.log(inspect(obj));
// <ref *1> {
//   a: [ [Circular *1] ],
//   b: <ref *2> { inner: [Circular *2], obj: [Circular *1] }
// }
```

The following example inspects all properties of the `util` object:

*ES module version:*

```js
import util from 'node:util';

console.log(util.inspect(util, { showHidden: true, depth: null }));
```

*CommonJS version:*

```js
const util = require('node:util');

console.log(util.inspect(util, { showHidden: true, depth: null }));
```

The following example highlights the effect of the `compact` option:

*ES module version:*

```js
import { inspect } from 'node:util';

const o = {
  a: [1, 2, [[
    'Lorem ipsum dolor sit amet,\nconsectetur adipiscing elit, sed do ' +
      'eiusmod \ntempor incididunt ut labore et dolore magna aliqua.',
    'test',
    'foo']], 4],
  b: new Map([['za', 1], ['zb', 'test']]),
};
console.log(inspect(o, { compact: true, depth: 5, breakLength: 80 }));

// { a:
//   [ 1,
//     2,
//     [ [ 'Lorem ipsum dolor sit amet,\nconsectetur [...]', // A long line
//           'test',
//           'foo' ] ],
//     4 ],
//   b: Map(2) { 'za' => 1, 'zb' => 'test' } }

// Setting `compact` to false or an integer creates more reader friendly output.
console.log(inspect(o, { compact: false, depth: 5, breakLength: 80 }));

// {
//   a: [
//     1,
//     2,
//     [
//       [
//         'Lorem ipsum dolor sit amet,\n' +
//           'consectetur adipiscing elit, sed do eiusmod \n' +
//           'tempor incididunt ut labore et dolore magna aliqua.',
//         'test',
//         'foo'
//       ]
//     ],
//     4
//   ],
//   b: Map(2) {
//     'za' => 1,
//     'zb' => 'test'
//   }
// }

// Setting `breakLength` to e.g. 150 will print the "Lorem ipsum" text in a
// single line.
```

*CommonJS version:*

```js
const { inspect } = require('node:util');

const o = {
  a: [1, 2, [[
    'Lorem ipsum dolor sit amet,\nconsectetur adipiscing elit, sed do ' +
      'eiusmod \ntempor incididunt ut labore et dolore magna aliqua.',
    'test',
    'foo']], 4],
  b: new Map([['za', 1], ['zb', 'test']]),
};
console.log(inspect(o, { compact: true, depth: 5, breakLength: 80 }));

// { a:
//   [ 1,
//     2,
//     [ [ 'Lorem ipsum dolor sit amet,\nconsectetur [...]', // A long line
//           'test',
//           'foo' ] ],
//     4 ],
//   b: Map(2) { 'za' => 1, 'zb' => 'test' } }

// Setting `compact` to false or an integer creates more reader friendly output.
console.log(inspect(o, { compact: false, depth: 5, breakLength: 80 }));

// {
//   a: [
//     1,
//     2,
//     [
//       [
//         'Lorem ipsum dolor sit amet,\n' +
//           'consectetur adipiscing elit, sed do eiusmod \n' +
//           'tempor incididunt ut labore et dolore magna aliqua.',
//         'test',
//         'foo'
//       ]
//     ],
//     4
//   ],
//   b: Map(2) {
//     'za' => 1,
//     'zb' => 'test'
//   }
// }

// Setting `breakLength` to e.g. 150 will print the "Lorem ipsum" text in a
// single line.
```

The `showHidden` option allows [`<WeakMap>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/WeakMap) and [`<WeakSet>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/WeakSet) entries to be
inspected. If there are more entries than `maxArrayLength`, there is no
guarantee which entries are displayed. That means retrieving the same
[`<WeakSet>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/WeakSet) entries twice may result in different output. Furthermore, entries
with no remaining strong references may be garbage collected at any time.

*ES module version:*

```js
import { inspect } from 'node:util';

const obj = { a: 1 };
const obj2 = { b: 2 };
const weakSet = new WeakSet([obj, obj2]);

console.log(inspect(weakSet, { showHidden: true }));
// WeakSet { { a: 1 }, { b: 2 } }
```

*CommonJS version:*

```js
const { inspect } = require('node:util');

const obj = { a: 1 };
const obj2 = { b: 2 };
const weakSet = new WeakSet([obj, obj2]);

console.log(inspect(weakSet, { showHidden: true }));
// WeakSet { { a: 1 }, { b: 2 } }
```

The `sorted` option ensures that an object's property insertion order does not
impact the result of `util.inspect()`.

*ES module version:*

```js
import { inspect } from 'node:util';
import assert from 'node:assert';

const o1 = {
  b: [2, 3, 1],
  a: '`a` comes before `b`',
  c: new Set([2, 3, 1]),
};
console.log(inspect(o1, { sorted: true }));
// { a: '`a` comes before `b`', b: [ 2, 3, 1 ], c: Set(3) { 1, 2, 3 } }
console.log(inspect(o1, { sorted: (a, b) => b.localeCompare(a) }));
// { c: Set(3) { 3, 2, 1 }, b: [ 2, 3, 1 ], a: '`a` comes before `b`' }

const o2 = {
  c: new Set([2, 1, 3]),
  a: '`a` comes before `b`',
  b: [2, 3, 1],
};
assert.strict.equal(
  inspect(o1, { sorted: true }),
  inspect(o2, { sorted: true }),
);
```

*CommonJS version:*

```js
const { inspect } = require('node:util');
const assert = require('node:assert');

const o1 = {
  b: [2, 3, 1],
  a: '`a` comes before `b`',
  c: new Set([2, 3, 1]),
};
console.log(inspect(o1, { sorted: true }));
// { a: '`a` comes before `b`', b: [ 2, 3, 1 ], c: Set(3) { 1, 2, 3 } }
console.log(inspect(o1, { sorted: (a, b) => b.localeCompare(a) }));
// { c: Set(3) { 3, 2, 1 }, b: [ 2, 3, 1 ], a: '`a` comes before `b`' }

const o2 = {
  c: new Set([2, 1, 3]),
  a: '`a` comes before `b`',
  b: [2, 3, 1],
};
assert.strict.equal(
  inspect(o1, { sorted: true }),
  inspect(o2, { sorted: true }),
);
```

The `numericSeparator` option adds an underscore every three digits to all
numbers.

*ES module version:*

```js
import { inspect } from 'node:util';

const thousand = 1000;
const million = 1000000;
const bigNumber = 123456789n;
const bigDecimal = 1234.12345;

console.log(inspect(thousand, { numericSeparator: true }));
// 1_000
console.log(inspect(million, { numericSeparator: true }));
// 1_000_000
console.log(inspect(bigNumber, { numericSeparator: true }));
// 123_456_789n
console.log(inspect(bigDecimal, { numericSeparator: true }));
// 1_234.123_45
```

*CommonJS version:*

```js
const { inspect } = require('node:util');

const thousand = 1000;
const million = 1000000;
const bigNumber = 123456789n;
const bigDecimal = 1234.12345;

console.log(inspect(thousand, { numericSeparator: true }));
// 1_000
console.log(inspect(million, { numericSeparator: true }));
// 1_000_000
console.log(inspect(bigNumber, { numericSeparator: true }));
// 123_456_789n
console.log(inspect(bigDecimal, { numericSeparator: true }));
// 1_234.123_45
```

`util.inspect()` is a synchronous method intended for debugging. Its maximum
output length is approximately 128 MiB. Inputs that result in longer output will
be truncated.

### Customizing `util.inspect` colors

Color output (if enabled) of `util.inspect` is customizable globally
via the `util.inspect.styles` and `util.inspect.colors` properties.

`util.inspect.styles` is a map associating a style name to a color from
`util.inspect.colors`.

The default styles and associated colors are:

- `bigint`: `yellow`
- `boolean`: `yellow`
- `date`: `magenta`
- `module`: `underline`
- `name`: (no styling)
- `null`: `bold`
- `number`: `yellow`
- `regexp`: A method that colors character classes, groups, assertions, and
  other parts for improved readability. To customize the coloring, change the
  `colors` property. It is set to
  `['red', 'green', 'yellow', 'cyan', 'magenta']` by default and may be
  adjusted as needed. The array is repetitively iterated through depending on
  the "depth".
- `special`: `cyan` (e.g., `Proxies`)
- `string`: `green`
- `symbol`: `green`
- `undefined`: `grey`

Color styling uses ANSI control codes that may not be supported on all
terminals. To verify color support use [`tty.hasColors()`](57-tty.md#writestreamhascolorscount-env).

Predefined control codes are listed below (grouped as "Modifiers", "Foreground
colors", and "Background colors").

#### Complex custom coloring

It is possible to define a method as style. It receives the stringified value
of the input. It is invoked in case coloring is active and the type is
inspected.

Example: `util.inspect.styles.regexp(value)`

- `value` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) The string representation of the input type.
- Returns: [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) The adjusted representation of `object`.

#### Modifiers

Modifier support varies throughout different terminals. They will mostly be
ignored, if not supported.

- `reset` - Resets all (color) modifiers to their defaults
- **bold** - Make text bold
- *italic* - Make text italic
- underline - Make text underlined
- ~~strikethrough~~ - Puts a horizontal line through the center of the text
  (Alias: `strikeThrough`, `crossedout`, `crossedOut`)
- `hidden` - Prints the text, but makes it invisible (Alias: conceal)
- dim - Decreased color intensity (Alias:
  `faint`)
- overlined - Make text overlined
- blink - Hides and shows the text in an interval
- inverse - Swap foreground and
  background colors (Alias: `swapcolors`, `swapColors`)
- doubleunderline - Make text
  double underlined (Alias: `doubleUnderline`)
- framed - Draw a frame around the text

#### Foreground colors

- `black`
- `red`
- `green`
- `yellow`
- `blue`
- `magenta`
- `cyan`
- `white`
- `gray` (alias: `grey`, `blackBright`)
- `redBright`
- `greenBright`
- `yellowBright`
- `blueBright`
- `magentaBright`
- `cyanBright`
- `whiteBright`

#### Background colors

- `bgBlack`
- `bgRed`
- `bgGreen`
- `bgYellow`
- `bgBlue`
- `bgMagenta`
- `bgCyan`
- `bgWhite`
- `bgGray` (alias: `bgGrey`, `bgBlackBright`)
- `bgRedBright`
- `bgGreenBright`
- `bgYellowBright`
- `bgBlueBright`
- `bgMagentaBright`
- `bgCyanBright`
- `bgWhiteBright`

### Custom inspection functions on objects

Added in: v0.1.97

**History**

| Version | Changes |
| --- | --- |
| v17.3.0, v16.14.0 | The inspect argument is added for more interoperability. |

Objects may also define their own
[`[util.inspect.custom](depth, opts, inspect)`](60-util-part2.md#utilinspectcustom) function,
which `util.inspect()` will invoke and use the result of when inspecting
the object.

*ES module version:*

```js
import { inspect } from 'node:util';

class Box {
  constructor(value) {
    this.value = value;
  }

  [inspect.custom](depth, options, inspect) {
    if (depth < 0) {
      return options.stylize('[Box]', 'special');
    }

    const newOptions = Object.assign({}, options, {
      depth: options.depth === null ? null : options.depth - 1,
    });

    // Five space padding because that's the size of "Box< ".
    const padding = ' '.repeat(5);
    const inner = inspect(this.value, newOptions)
                  .replace(/\n/g, `\n${padding}`);
    return `${options.stylize('Box', 'special')}< ${inner} >`;
  }
}

const box = new Box(true);

console.log(inspect(box));
// "Box< true >"
```

*CommonJS version:*

```js
const { inspect } = require('node:util');

class Box {
  constructor(value) {
    this.value = value;
  }

  [inspect.custom](depth, options, inspect) {
    if (depth < 0) {
      return options.stylize('[Box]', 'special');
    }

    const newOptions = Object.assign({}, options, {
      depth: options.depth === null ? null : options.depth - 1,
    });

    // Five space padding because that's the size of "Box< ".
    const padding = ' '.repeat(5);
    const inner = inspect(this.value, newOptions)
                  .replace(/\n/g, `\n${padding}`);
    return `${options.stylize('Box', 'special')}< ${inner} >`;
  }
}

const box = new Box(true);

console.log(inspect(box));
// "Box< true >"
```

Custom `[util.inspect.custom](depth, opts, inspect)` functions typically return
a string but may return a value of any type that will be formatted accordingly
by `util.inspect()`.

*ES module version:*

```js
import { inspect } from 'node:util';

const obj = { foo: 'this will not show up in the inspect() output' };
obj[inspect.custom] = (depth) => {
  return { bar: 'baz' };
};

console.log(inspect(obj));
// "{ bar: 'baz' }"
```

*CommonJS version:*

```js
const { inspect } = require('node:util');

const obj = { foo: 'this will not show up in the inspect() output' };
obj[inspect.custom] = (depth) => {
  return { bar: 'baz' };
};

console.log(inspect(obj));
// "{ bar: 'baz' }"
```

[Contents](../README.md) | [Previous: URL](59-url.md) | [Next: Utilities, part 2 of 3](60-util-part2.md)
