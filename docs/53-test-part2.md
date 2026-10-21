[Contents](../README.md) | [Previous: Test runner, part 1 of 4](53-test-part1.md) | [Next: Test runner, part 3 of 4](53-test-part3.md)

Source: [https://nodejs.org/docs/latest/api/test.html](https://nodejs.org/docs/latest/api/test.html)

Part 2 of 4 of this section.

# Test runner (part 2 of 4)

## `suite([name][, options][, fn])`

Added in: v22.0.0, v20.13.0

- `name` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) The name of the suite, which is displayed when reporting test
  results. **Default:** The `name` property of `fn`, or `'<anonymous>'` if `fn`
  does not have a name.
- `options` [`<Object>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Object) Optional configuration options for the suite.
  This supports the same options as `test([name][, options][, fn])`.
- `fn` [`<Function>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Function) | [`<AsyncFunction>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/AsyncFunction) The suite function declaring nested tests and
  suites. The first argument to this function is a [`SuiteContext`](53-test-part4.md#class-suitecontext) object.
  **Default:** A no-op function.
- Returns: [`<Promise>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Promise) Immediately fulfilled with `undefined`.

The `suite()` function is imported from the `node:test` module.

## `suite.skip([name][, options][, fn])`

Added in: v22.0.0, v20.13.0

Shorthand for skipping a suite. This is the same as
[`suite([name], { skip: true }[, fn])`](#suitename-options-fn).

## `suite.todo([name][, options][, fn])`

Added in: v22.0.0, v20.13.0

Shorthand for marking a suite as `TODO`. This is the same as
[`suite([name], { todo: true }[, fn])`](#suitename-options-fn).

## `suite.only([name][, options][, fn])`

Added in: v22.0.0, v20.13.0

Shorthand for marking a suite as `only`. This is the same as
[`suite([name], { only: true }[, fn])`](#suitename-options-fn).

## `test([name][, options][, fn])`

Added in: v18.0.0, v16.17.0

**History**

| Version | Changes |
| --- | --- |
| v26.2.0 | Added the `tags` option. |
| v20.2.0, v18.17.0 | Added the `skip`, `todo`, and `only` shorthands. |
| v18.8.0, v16.18.0 | Add a `signal` option. |
| v18.7.0, v16.17.0 | Add a `timeout` option. |

- `name` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) The name of the test, which is displayed when reporting test
  results. **Default:** The `name` property of `fn`, or `'<anonymous>'` if `fn`
  does not have a name.
- `options` [`<Object>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Object) Configuration options for the test. The following
  properties are supported:
  - `concurrency` [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) | [`<boolean>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#boolean_type) If a number is provided,
    then that many tests would run asynchronously (they are still managed by the single-threaded event loop).
    If `true`, all scheduled asynchronous tests run concurrently within the
    thread. If `false`, only one test runs at a time.
    If unspecified, subtests inherit this value from their parent.
    **Default:** `false`.
  - `expectFailure` [`<boolean>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#boolean_type) | [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) | [`<RegExp>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/RegExp) | [`<Function>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Function) | [`<Object>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Object) | [`<Error>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Error) If truthy, the
    test is expected to fail. If a non-empty string is provided, that string is displayed
    in the test results as the reason why the test is expected to fail. If a [`<RegExp>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/RegExp) | [`<Function>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Function) | [`<Object>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Object) | [`<Error>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Error)
    is provided directly (without wrapping in `{ match: … }`), the test passes
    only if the thrown error matches, following the behavior of
    [`assert.throws`](03-assert.md#assertthrowsfn-error-message). To provide both a reason and validation, pass an object
    with `label` (string) and `match` (RegExp, Function, Object, or Error).
    **Default:** `false`.
  - `only` [`<boolean>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#boolean_type) If truthy, and the test context is configured to run `only` tests, then this test will be run. Otherwise, the test is skipped.
    **Default:** `false`.
  - `signal` [`<AbortSignal>`](26-globals.md#class-abortsignal) Allows aborting an in-progress test.
  - `skip` [`<boolean>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#boolean_type) | [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) If truthy, the test is skipped. If a string is
    provided, that string is displayed in the test results as the reason for
    skipping the test. **Default:** `false`.
  - `tags` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type)[] An array of string labels associated with the test.
    Used together with [`--experimental-test-tag-filter`](13-cli.md#--experimental-test-tag-filtertag) to filter which
    tests run. Tags inherit from suites to nested tests by union. See
    [Test tags](53-test-part1.md#test-tags). **Default:** `[]`.
  - `todo` [`<boolean>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#boolean_type) | [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) If truthy, the test marked as `TODO`. If a string
    is provided, that string is displayed in the test results as the reason why
    the test is `TODO`. **Default:** `false`.
  - `timeout` [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) A number of milliseconds the test will fail after.
    If unspecified, subtests inherit this value from their parent. **Default:** `Infinity`.
  - `plan` [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) The number of assertions and subtests expected to be run in the test.
    If the number of assertions run in the test does not match the number
    specified in the plan, the test will fail. **Default:** `undefined`.
  - `fn` [`<Function>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Function) | [`<AsyncFunction>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/AsyncFunction) The function under test. If provided, it will take
    precedence over the `fn` parameter.
  - `name` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) The name of the test. If provided, it will take precedence over the `name` parameter.
- `fn` [`<Function>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Function) | [`<AsyncFunction>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/AsyncFunction) The function under test. The first argument
  to this function is a [`TestContext`](53-test-part3.md#class-testcontext) object. If the test uses callbacks,
  the callback function is passed as the second argument. **Default:** A no-op
  function.
- Returns: [`<Promise>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Promise) Fulfilled with `undefined` once
  the test completes, or immediately if the test runs within a suite.

The `test()` function is the value imported from the `test` module. Each
invocation of this function results in reporting the test to the [`<TestsStream>`](53-test-part2.md#class-testsstream).

The `TestContext` object passed to the `fn` argument can be used to perform
actions related to the current test. Examples include skipping the test, adding
additional diagnostic information, or creating subtests.

`test()` returns a `Promise` that fulfills once the test completes.
if `test()` is called within a suite, it fulfills immediately.
The return value can usually be discarded for top level tests.
However, the return value from subtests should be used to prevent the parent
test from finishing first and cancelling the subtest
as shown in the following example.

```js
test('top level test', async (t) => {
  // The setTimeout() in the following subtest would cause it to outlive its
  // parent test if 'await' is removed on the next line. Once the parent test
  // completes, it will cancel any outstanding subtests.
  await t.test('longer running subtest', async (t) => {
    return new Promise((resolve, reject) => {
      setTimeout(resolve, 1000);
    });
  });
});
```

The `timeout` option can be used to fail the test if it takes longer than
`timeout` milliseconds to complete. However, it is not a reliable mechanism for
canceling tests because a running test might block the application thread and
thus prevent the scheduled cancellation.

## `test.skip([name][, options][, fn])`

Shorthand for skipping a test,
same as [`test([name], { skip: true }[, fn])`](#testname-options-fn).

## `test.todo([name][, options][, fn])`

Shorthand for marking a test as `TODO`,
same as [`test([name], { todo: true }[, fn])`](#testname-options-fn).

## `test.only([name][, options][, fn])`

Shorthand for marking a test as `only`,
same as [`test([name], { only: true }[, fn])`](#testname-options-fn).

## `describe([name][, options][, fn])`

Alias for [`suite()`](#suitename-options-fn).

The `describe()` function is imported from the `node:test` module.

## `describe.skip([name][, options][, fn])`

Shorthand for skipping a suite. This is the same as
[`describe([name], { skip: true }[, fn])`](#describename-options-fn).

## `describe.todo([name][, options][, fn])`

Shorthand for marking a suite as `TODO`. This is the same as
[`describe([name], { todo: true }[, fn])`](#describename-options-fn).

## `describe.only([name][, options][, fn])`

Added in: v19.8.0, v18.15.0

Shorthand for marking a suite as `only`. This is the same as
[`describe([name], { only: true }[, fn])`](#describename-options-fn).

## `it([name][, options][, fn])`

Added in: v18.6.0, v16.17.0

**History**

| Version | Changes |
| --- | --- |
| v19.8.0, v18.16.0 | Calling `it()` is now equivalent to calling `test()`. |

Alias for [`test()`](#testname-options-fn).

The `it()` function is imported from the `node:test` module.

## `it.skip([name][, options][, fn])`

Shorthand for skipping a test,
same as [`it([name], { skip: true }[, fn])`](#testname-options-fn).

## `it.todo([name][, options][, fn])`

Shorthand for marking a test as `TODO`,
same as [`it([name], { todo: true }[, fn])`](#testname-options-fn).

## `it.only([name][, options][, fn])`

Added in: v19.8.0, v18.15.0

Shorthand for marking a test as `only`,
same as [`it([name], { only: true }[, fn])`](#testname-options-fn).

## `before([fn][, options])`

Added in: v18.8.0, v16.18.0

- `fn` [`<Function>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Function) | [`<AsyncFunction>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/AsyncFunction) The hook function.
  If the hook uses callbacks,
  the callback function is passed as the second argument. **Default:** A no-op
  function.
- `options` [`<Object>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Object) Configuration options for the hook. The following
  properties are supported:
  - `signal` [`<AbortSignal>`](26-globals.md#class-abortsignal) Allows aborting an in-progress hook.
  - `timeout` [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) A number of milliseconds the hook will fail after.
    If unspecified, subtests inherit this value from their parent. **Default:** `Infinity`.

This function creates a hook that runs before executing a suite.

```js
describe('tests', async () => {
  before(() => console.log('about to run some test'));
  it('is a subtest', () => {
    // Some relevant assertions here
  });
});
```

## `after([fn][, options])`

Added in: v18.8.0, v16.18.0

- `fn` [`<Function>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Function) | [`<AsyncFunction>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/AsyncFunction) The hook function.
  If the hook uses callbacks,
  the callback function is passed as the second argument. **Default:** A no-op
  function.
- `options` [`<Object>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Object) Configuration options for the hook. The following
  properties are supported:
  - `signal` [`<AbortSignal>`](26-globals.md#class-abortsignal) Allows aborting an in-progress hook.
  - `timeout` [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) A number of milliseconds the hook will fail after.
    If unspecified, subtests inherit this value from their parent. **Default:** `Infinity`.

This function creates a hook that runs after executing a suite.

```js
describe('tests', async () => {
  after(() => console.log('finished running tests'));
  it('is a subtest', () => {
    // Some relevant assertion here
  });
});
```

**Note:** The `after` hook is guaranteed to run,
even if tests within the suite fail.

## `beforeEach([fn][, options])`

Added in: v18.8.0, v16.18.0

- `fn` [`<Function>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Function) | [`<AsyncFunction>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/AsyncFunction) The hook function.
  If the hook uses callbacks,
  the callback function is passed as the second argument. **Default:** A no-op
  function.
- `options` [`<Object>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Object) Configuration options for the hook. The following
  properties are supported:
  - `signal` [`<AbortSignal>`](26-globals.md#class-abortsignal) Allows aborting an in-progress hook.
  - `timeout` [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) A number of milliseconds the hook will fail after.
    If unspecified, subtests inherit this value from their parent. **Default:** `Infinity`.

This function creates a hook that runs before each test in the current suite.

```js
describe('tests', async () => {
  beforeEach(() => console.log('about to run a test'));
  it('is a subtest', () => {
    // Some relevant assertion here
  });
});
```

## `afterEach([fn][, options])`

Added in: v18.8.0, v16.18.0

- `fn` [`<Function>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Function) | [`<AsyncFunction>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/AsyncFunction) The hook function.
  If the hook uses callbacks,
  the callback function is passed as the second argument. **Default:** A no-op
  function.
- `options` [`<Object>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Object) Configuration options for the hook. The following
  properties are supported:
  - `signal` [`<AbortSignal>`](26-globals.md#class-abortsignal) Allows aborting an in-progress hook.
  - `timeout` [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) A number of milliseconds the hook will fail after.
    If unspecified, subtests inherit this value from their parent. **Default:** `Infinity`.

This function creates a hook that runs after each test in the current suite.
The `afterEach()` hook is run even if the test fails.

```js
describe('tests', async () => {
  afterEach(() => console.log('finished running a test'));
  it('is a subtest', () => {
    // Some relevant assertion here
  });
});
```

## `assert`

Added in: v23.7.0, v22.14.0

An object whose methods are used to configure available assertions on the
`TestContext` objects in the current process. The methods from `node:assert`
and snapshot testing functions are available by default.

It is possible to apply the same configuration to all files by placing common
configuration code in a module
preloaded with `--require` or `--import`.

### `assert.register(name, fn)`

Added in: v23.7.0, v22.14.0

Defines a new assertion function with the provided name and function. If an
assertion already exists with the same name, it is overwritten.

## `snapshot`

Added in: v22.3.0

An object whose methods are used to configure default snapshot settings in the
current process. It is possible to apply the same configuration to all files by
placing common configuration code in a module preloaded with `--require` or
`--import`.

### `snapshot.setDefaultSnapshotSerializers(serializers)`

Added in: v22.3.0

- `serializers` [`<Array>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Array) An array of synchronous functions used as the default
  serializers for snapshot tests.

This function is used to customize the default serialization mechanism used by
the test runner. By default, the test runner performs serialization by calling
`JSON.stringify(value, null, 2)` on the provided value. `JSON.stringify()` does
have limitations regarding circular structures and supported data types. If a
more robust serialization mechanism is required, this function should be used.

### `snapshot.setResolveSnapshotPath(fn)`

Added in: v22.3.0

- `fn` [`<Function>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Function) A function used to compute the location of the snapshot file.
  The function receives the path of the test file as its only argument. If the
  test is not associated with a file (for example in the REPL), the input is
  undefined. `fn()` must return a string specifying the location of the snapshot file.

This function is used to customize the location of the snapshot file used for
snapshot testing. By default, the snapshot filename is the same as the entry
point filename with a `.snapshot` file extension.

## Class: `MockFunctionContext`

Added in: v19.1.0, v18.13.0

The `MockFunctionContext` class is used to inspect or manipulate the behavior of
mocks created via the [`MockTracker`](#class-mocktracker) APIs.

### `ctx.calls`

Added in: v19.1.0, v18.13.0

- Type: [`<Array>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Array)

A getter that returns a copy of the internal array used to track calls to the
mock. Each entry in the array is an object with the following properties.

- `arguments` [`<Array>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Array) An array of the arguments passed to the mock function.
- `error` [`<any>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#Data_types) If the mocked function threw then this property contains the
  thrown value. **Default:** `undefined`.
- `result` [`<any>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#Data_types) The value returned by the mocked function.
- `stack` [`<Error>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Error) An `Error` object whose stack can be used to determine the
  callsite of the mocked function invocation.
- `target` [`<Function>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Function) | [`<undefined>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#undefined_type) If the mocked function is a constructor, this
  field contains the class being constructed. Otherwise this will be `undefined`.
- `this` [`<any>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#Data_types) The mocked function's `this` value.

### `ctx.callCount()`

Added in: v19.1.0, v18.13.0

- Returns: [`<integer>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) The number of times that this mock has been invoked.

This function returns the number of times that this mock has been invoked. This
function is more efficient than checking `ctx.calls.length` because `ctx.calls`
is a getter that creates a copy of the internal call tracking array.

### `ctx.mockImplementation(implementation)`

Added in: v19.1.0, v18.13.0

- `implementation` [`<Function>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Function) | [`<AsyncFunction>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/AsyncFunction) The function to be used as the
  mock's new implementation.

This function is used to change the behavior of an existing mock.

The following example creates a mock function using `t.mock.fn()`, calls the
mock function, and then changes the mock implementation to a different function.

```js
test('changes a mock behavior', (t) => {
  let cnt = 0;

  function addOne() {
    cnt++;
    return cnt;
  }

  function addTwo() {
    cnt += 2;
    return cnt;
  }

  const fn = t.mock.fn(addOne);

  assert.strictEqual(fn(), 1);
  fn.mock.mockImplementation(addTwo);
  assert.strictEqual(fn(), 3);
  assert.strictEqual(fn(), 5);
});
```

### `ctx.mockImplementationOnce(implementation[, onCall])`

Added in: v19.1.0, v18.13.0

- `implementation` [`<Function>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Function) | [`<AsyncFunction>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/AsyncFunction) The function to be used as the
  mock's implementation for the invocation number specified by `onCall`.
- `onCall` [`<integer>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) The invocation number that will use `implementation`. If
  the specified invocation has already occurred then an exception is thrown.
  **Default:** The number of the next invocation.

This function is used to change the behavior of an existing mock for a single
invocation. Once invocation `onCall` has occurred, the mock will revert to
whatever behavior it would have used had `mockImplementationOnce()` not been
called.

The following example creates a mock function using `t.mock.fn()`, calls the
mock function, changes the mock implementation to a different function for the
next invocation, and then resumes its previous behavior.

```js
test('changes a mock behavior once', (t) => {
  let cnt = 0;

  function addOne() {
    cnt++;
    return cnt;
  }

  function addTwo() {
    cnt += 2;
    return cnt;
  }

  const fn = t.mock.fn(addOne);

  assert.strictEqual(fn(), 1);
  fn.mock.mockImplementationOnce(addTwo);
  assert.strictEqual(fn(), 3);
  assert.strictEqual(fn(), 4);
});
```

### `ctx.resetCalls()`

Added in: v19.3.0, v18.13.0

Resets the call history of the mock function.

### `ctx.restore()`

Added in: v19.1.0, v18.13.0

Resets the implementation of the mock function to its original behavior. The
mock can still be used after calling this function.

## Class: `MockModuleContext`

Added in: v22.3.0, v20.18.0

Stability: 1.0 - Early development

The `MockModuleContext` class is used to manipulate the behavior of module mocks
created via the [`MockTracker`](#class-mocktracker) APIs.

### `ctx.restore()`

Added in: v22.3.0, v20.18.0

Resets the implementation of the mock module.

## Class: `MockPropertyContext`

Added in: v24.3.0, v22.20.0

The `MockPropertyContext` class is used to inspect or manipulate the behavior
of property mocks created via the [`MockTracker`](#class-mocktracker) APIs.

### `ctx.accesses`

- Type: [`<Array>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Array)

A getter that returns a copy of the internal array used to track accesses (get/set) to
the mocked property. Each entry in the array is an object with the following properties:

- `type` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) Either `'get'` or `'set'`, indicating the type of access.
- `value` [`<any>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#Data_types) The value that was read (for `'get'`) or written (for `'set'`).
- `stack` [`<Error>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Error) An `Error` object whose stack can be used to determine the
  callsite of the mocked function invocation.

### `ctx.accessCount()`

- Returns: [`<integer>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) The number of times that the property was accessed (read or written).

This function returns the number of times that the property was accessed.
This function is more efficient than checking `ctx.accesses.length` because
`ctx.accesses` is a getter that creates a copy of the internal access tracking array.

### `ctx.mockImplementation(value)`

- `value` [`<any>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#Data_types) The new value to be set as the mocked property value.

This function is used to change the value returned by the mocked property getter.

### `ctx.mockImplementationOnce(value[, onAccess])`

- `value` [`<any>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#Data_types) The value to be used as the mock's
  implementation for the invocation number specified by `onAccess`.
- `onAccess` [`<integer>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) The invocation number that will use `value`. If
  the specified invocation has already occurred then an exception is thrown.
  **Default:** The number of the next invocation.

This function is used to change the behavior of an existing mock for a single
invocation. Once invocation `onAccess` has occurred, the mock will revert to
whatever behavior it would have used had `mockImplementationOnce()` not been
called.

The following example creates a mock function using `t.mock.property()`, calls the
mock property, changes the mock implementation to a different value for the
next invocation, and then resumes its previous behavior.

```js
test('changes a mock behavior once', (t) => {
  const obj = { foo: 1 };

  const prop = t.mock.property(obj, 'foo', 5);

  assert.strictEqual(obj.foo, 5);
  prop.mock.mockImplementationOnce(25);
  assert.strictEqual(obj.foo, 25);
  assert.strictEqual(obj.foo, 5);
});
```

#### Caveat

For consistency with the rest of the mocking API, this function treats both property gets and sets
as accesses. If a property set occurs at the same access index, the "once" value will be consumed
by the set operation, and the mocked property value will be changed to the "once" value. This may
lead to unexpected behavior if you intend the "once" value to only be used for a get operation.

### `ctx.resetAccesses()`

Resets the access history of the mocked property.

### `ctx.restore()`

Resets the implementation of the mock property to its original behavior. The
mock can still be used after calling this function.

## Class: `MockTracker`

Added in: v19.1.0, v18.13.0

The `MockTracker` class is used to manage mocking functionality. The test runner
module provides a top level `mock` export which is a `MockTracker` instance.
Each test also provides its own `MockTracker` instance via the test context's
`mock` property.

### `mock.fn([original[, implementation]][, options])`

Added in: v19.1.0, v18.13.0

- `original` [`<Function>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Function) | [`<AsyncFunction>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/AsyncFunction) An optional function to create a mock on. **Default:** A no-op function.
- `implementation` [`<Function>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Function) | [`<AsyncFunction>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/AsyncFunction) An optional function used as the
  mock implementation for `original`. This is useful for creating mocks that
  exhibit one behavior for a specified number of calls and then restore the
  behavior of `original`. **Default:** The function specified by `original`.
- `options` [`<Object>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Object) Optional configuration options for the mock function. The
  following properties are supported:
  - `times` [`<integer>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) The number of times that the mock will use the behavior of `implementation`. Once the mock function has been called `times` times, it
    will automatically restore the behavior of `original`. This value must be an
    integer greater than zero. **Default:** `Infinity`.
- Returns: [`<Proxy>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Proxy) The mocked function. The mocked function contains a special `mock` property, which is an instance of [`MockFunctionContext`](#class-mockfunctioncontext), and can
  be used for inspecting and changing the behavior of the mocked function.

This function is used to create a mock function.

The following example creates a mock function that increments a counter by one
on each invocation. The `times` option is used to modify the mock behavior such
that the first two invocations add two to the counter instead of one.

```js
test('mocks a counting function', (t) => {
  let cnt = 0;

  function addOne() {
    cnt++;
    return cnt;
  }

  function addTwo() {
    cnt += 2;
    return cnt;
  }

  const fn = t.mock.fn(addOne, addTwo, { times: 2 });

  assert.strictEqual(fn(), 2);
  assert.strictEqual(fn(), 4);
  assert.strictEqual(fn(), 5);
  assert.strictEqual(fn(), 6);
});
```

### `mock.getter(object, methodName[, implementation][, options])`

Added in: v19.3.0, v18.13.0

This function is syntax sugar for [`MockTracker.method`](#mockmethodobject-methodname-implementation-options) with `options.getter`
set to `true`.

### `mock.method(object, methodName[, implementation][, options])`

Added in: v19.1.0, v18.13.0

- `object` [`<Object>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Object) The object whose method is being mocked.
- `methodName` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) | [`<symbol>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#symbol_type) The identifier of the method on `object` to mock.
  If `object[methodName]` is not a function, an error is thrown.
- `implementation` [`<Function>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Function) | [`<AsyncFunction>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/AsyncFunction) An optional function used as the
  mock implementation for `object[methodName]`. **Default:** The original method
  specified by `object[methodName]`.
- `options` [`<Object>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Object) Optional configuration options for the mock method. The
  following properties are supported:
  - `getter` [`<boolean>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#boolean_type) If `true`, `object[methodName]` is treated as a getter.
    This option cannot be used with the `setter` option. **Default:** false.
  - `setter` [`<boolean>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#boolean_type) If `true`, `object[methodName]` is treated as a setter.
    This option cannot be used with the `getter` option. **Default:** false.
  - `times` [`<integer>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) The number of times that the mock will use the behavior of `implementation`. Once the mocked method has been called `times` times, it
    will automatically restore the original behavior. This value must be an
    integer greater than zero. **Default:** `Infinity`.
- Returns: [`<Proxy>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Proxy) The mocked method. The mocked method contains a special `mock` property, which is an instance of [`MockFunctionContext`](#class-mockfunctioncontext), and can
  be used for inspecting and changing the behavior of the mocked method.

This function is used to create a mock on an existing object method. The
following example demonstrates how a mock is created on an existing object
method.

```js
test('spies on an object method', (t) => {
  const number = {
    value: 5,
    subtract(a) {
      return this.value - a;
    },
  };

  t.mock.method(number, 'subtract');
  assert.strictEqual(number.subtract.mock.callCount(), 0);
  assert.strictEqual(number.subtract(3), 2);
  assert.strictEqual(number.subtract.mock.callCount(), 1);

  const call = number.subtract.mock.calls[0];

  assert.deepStrictEqual(call.arguments, [3]);
  assert.strictEqual(call.result, 2);
  assert.strictEqual(call.error, undefined);
  assert.strictEqual(call.target, undefined);
  assert.strictEqual(call.this, number);
});
```

### `mock.module(specifier[, options])`

Added in: v22.3.0, v20.18.0

**History**

| Version | Changes |
| --- | --- |
| v24.0.0, v22.17.0 | Support JSON modules. |

Stability: 1.0 - Early development

- `specifier` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) | [`<URL>`](59-url.md#the-whatwg-url-api) A string identifying the module to mock.
- `options` [`<Object>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Object) Optional configuration options for the mock module. The
  following properties are supported:
  - `cache` [`<boolean>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#boolean_type) If `false`, each call to `require()` or `import()`
    generates a new mock module. If `true`, subsequent calls will return the same
    module mock, and the mock module is inserted into the CommonJS cache.
    **Default:** false.
  - `exports` [`<Object>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Object) Optional mocked exports. The `default` property, if
    provided, is used as the mocked module's default export. All other own
    enumerable properties are used as named exports.
    **This option cannot be used with `defaultExport` or `namedExports`.**
    - If the mock is a CommonJS or builtin module, `exports.default` is used as
      the value of `module.exports`.
    - If `exports.default` is not provided for a CommonJS or builtin mock,
      `module.exports` defaults to an empty object.
    - If named exports are provided with a non-object default export, the mock
      throws an exception when used as a CommonJS or builtin module.
  - `defaultExport` [`<any>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#Data_types) An optional value used as the mocked module's default
    export. If this value is not provided, ESM mocks do not include a default
    export. If the mock is a CommonJS or builtin module, this setting is used as
    the value of `module.exports`. If this value is not provided, CJS and builtin
    mocks use an empty object as the value of `module.exports`.
    **This option cannot be used with `options.exports`.**
    This option is deprecated and will be removed in a later version.
    Prefer `options.exports.default`.
  - `namedExports` [`<Object>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Object) An optional object whose keys and values are used to
    create the named exports of the mock module. If the mock is a CommonJS or
    builtin module, these values are copied onto `module.exports`. Therefore, if a
    mock is created with both named exports and a non-object default export, the
    mock will throw an exception when used as a CJS or builtin module.
    **This option cannot be used with `options.exports`.**
    This option is deprecated and will be removed in a later version.
    Prefer `options.exports`.
- Returns: [`<MockModuleContext>`](53-test-part2.md#class-mockmodulecontext) An object that can be used to manipulate the mock.

This function is used to mock the exports of ECMAScript modules, CommonJS modules, JSON modules, and
Node.js builtin modules. Any references to the original module prior to mocking are not impacted. In
order to enable module mocking, Node.js must be started with the
[`--experimental-test-module-mocks`](13-cli.md#--experimental-test-module-mocks) command-line flag.

**Note**: [module customization hooks](35-module.md#customization-hooks) registered via the **synchronous** API effect resolution of
the `specifier` provided to `mock.module`. Customization hooks registered via the **asynchronous**
API are currently ignored (because the test runner's loader is synchronous, and node does not
support multi-chain / cross-chain loading).

The following example demonstrates how a mock is created for a module.

```js
test('mocks a builtin module in both module systems', async (t) => {
  // Create a mock of 'node:readline' with a named export named 'foo', which
  // does not exist in the original 'node:readline' module.
  const mock = t.mock.module('node:readline', {
    exports: { foo: () => 42 },
  });

  let esmImpl = await import('node:readline');
  let cjsImpl = require('node:readline');

  // cursorTo() is an export of the original 'node:readline' module.
  assert.strictEqual(esmImpl.cursorTo, undefined);
  assert.strictEqual(cjsImpl.cursorTo, undefined);
  assert.strictEqual(esmImpl.foo(), 42);
  assert.strictEqual(cjsImpl.foo(), 42);

  mock.restore();

  // The mock is restored, so the original builtin module is returned.
  esmImpl = await import('node:readline');
  cjsImpl = require('node:readline');

  assert.strictEqual(typeof esmImpl.cursorTo, 'function');
  assert.strictEqual(typeof cjsImpl.cursorTo, 'function');
  assert.strictEqual(esmImpl.foo, undefined);
  assert.strictEqual(cjsImpl.foo, undefined);
});
```

### `mock.property(object, propertyName[, value])`

Added in: v24.3.0, v22.20.0

- `object` [`<Object>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Object) The object whose value is being mocked.
- `propertyName` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) | [`<symbol>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#symbol_type) The identifier of the property on `object` to mock.
- `value` [`<any>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#Data_types) An optional value used as the mock value
  for `object[propertyName]`. **Default:** The original property value.
- Returns: [`<Proxy>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Proxy) A proxy to the mocked object. The mocked object contains a
  special `mock` property, which is an instance of [`MockPropertyContext`](#class-mockpropertycontext), and
  can be used for inspecting and changing the behavior of the mocked property.

Creates a mock for a property value on an object. This allows you to track and control access to a specific property,
including how many times it is read (getter) or written (setter), and to restore the original value after mocking.

```js
test('mocks a property value', (t) => {
  const obj = { foo: 42 };
  const prop = t.mock.property(obj, 'foo', 100);

  assert.strictEqual(obj.foo, 100);
  assert.strictEqual(prop.mock.accessCount(), 1);
  assert.strictEqual(prop.mock.accesses[0].type, 'get');
  assert.strictEqual(prop.mock.accesses[0].value, 100);

  obj.foo = 200;
  assert.strictEqual(prop.mock.accessCount(), 2);
  assert.strictEqual(prop.mock.accesses[1].type, 'set');
  assert.strictEqual(prop.mock.accesses[1].value, 200);

  prop.mock.restore();
  assert.strictEqual(obj.foo, 42);
});
```

### `mock.reset()`

Added in: v19.1.0, v18.13.0

This function restores the default behavior of all mocks that were previously
created by this `MockTracker` and disassociates the mocks from the
`MockTracker` instance. Once disassociated, the mocks can still be used, but the
`MockTracker` instance can no longer be used to reset their behavior or
otherwise interact with them.

After each test completes, this function is called on the test context's
`MockTracker`. If the global `MockTracker` is used extensively, calling this
function manually is recommended.

### `mock.restoreAll()`

Added in: v19.1.0, v18.13.0

This function restores the default behavior of all mocks that were previously
created by this `MockTracker`. Unlike `mock.reset()`, `mock.restoreAll()` does
not disassociate the mocks from the `MockTracker` instance.

### `mock.setter(object, methodName[, implementation][, options])`

Added in: v19.3.0, v18.13.0

This function is syntax sugar for [`MockTracker.method`](#mockmethodobject-methodname-implementation-options) with `options.setter`
set to `true`.

## Class: `MockTimers`

Added in: v20.4.0, v18.19.0

**History**

| Version | Changes |
| --- | --- |
| v23.1.0 | The Mock Timers is now stable. |

Mocking timers is a technique commonly used in software testing to simulate and
control the behavior of timers, such as `setInterval` and `setTimeout`,
without actually waiting for the specified time intervals.

MockTimers is also able to mock the `Date` object.

The [`MockTracker`](#class-mocktracker) provides a top-level `timers` export
which is a `MockTimers` instance.

### `timers.enable([enableOptions])`

Added in: v20.4.0, v18.19.0

**History**

| Version | Changes |
| --- | --- |
| v21.2.0, v20.11.0 | Updated parameters to be an option object with available APIs and the default initial epoch. |

Enables timer mocking for the specified timers.

- `enableOptions` [`<Object>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Object) Optional configuration options for enabling timer
  mocking. The following properties are supported:
  - `apis` [`<Array>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Array) An optional array containing the timers to mock.
    The currently supported timer values are `'setInterval'`, `'setTimeout'`, `'setImmediate'`,
    and `'Date'`. **Default:** `['setInterval', 'setTimeout', 'setImmediate', 'Date']`.
    If no array is provided, all time related APIs (`'setInterval'`, `'clearInterval'`,
    `'setTimeout'`, `'clearTimeout'`, `'setImmediate'`, `'clearImmediate'`, and
    `'Date'`) will be mocked by default.
  - `now` [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) | [`<Date>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Date) An optional number or Date object representing the
    initial time (in milliseconds) to use as the value
    for `Date.now()`. **Default:** `0`.

**Note:** When you enable mocking for a specific timer, its associated
clear function will also be implicitly mocked.

**Note:** Mocking `Date` will affect the behavior of the mocked timers
as they use the same internal clock.

Example usage without setting initial time:

*ES module version:*

```js
import { mock } from 'node:test';
mock.timers.enable({ apis: ['setInterval'] });
```

*CommonJS version:*

```js
const { mock } = require('node:test');
mock.timers.enable({ apis: ['setInterval'] });
```

The above example enables mocking for the `setInterval` timer and
implicitly mocks the `clearInterval` function. Only the `setInterval`
and `clearInterval` functions from [node:timers](54-timers.md),
[node:timers/promises](54-timers.md#timers-promises-api), and
`globalThis` will be mocked.

Example usage with initial time set

*ES module version:*

```js
import { mock } from 'node:test';
mock.timers.enable({ apis: ['Date'], now: 1000 });
```

*CommonJS version:*

```js
const { mock } = require('node:test');
mock.timers.enable({ apis: ['Date'], now: 1000 });
```

Example usage with initial Date object as time set

*ES module version:*

```js
import { mock } from 'node:test';
mock.timers.enable({ apis: ['Date'], now: new Date() });
```

*CommonJS version:*

```js
const { mock } = require('node:test');
mock.timers.enable({ apis: ['Date'], now: new Date() });
```

Alternatively, if you call `mock.timers.enable()` without any parameters:

All timers (`'setInterval'`, `'clearInterval'`, `'setTimeout'`, `'clearTimeout'`,
`'setImmediate'`, and `'clearImmediate'`) will be mocked. The `setInterval`,
`clearInterval`, `setTimeout`, `clearTimeout`, `setImmediate`, and
`clearImmediate` functions from `node:timers`, `node:timers/promises`, and
`globalThis` will be mocked. As well as the global `Date` object.

### `timers.reset()`

Added in: v20.4.0, v18.19.0

This function restores the default behavior of all mocks that were previously
created by this `MockTimers` instance and disassociates the mocks
from the `MockTracker` instance.

**Note:** After each test completes, this function is called on
the test context's `MockTracker`.

*ES module version:*

```js
import { mock } from 'node:test';
mock.timers.reset();
```

*CommonJS version:*

```js
const { mock } = require('node:test');
mock.timers.reset();
```

### `timers[Symbol.dispose]()`

Calls `timers.reset()`.

### `timers.tick([milliseconds])`

Added in: v20.4.0, v18.19.0

Advances time for all mocked timers.

- `milliseconds` [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) The amount of time, in milliseconds,
  to advance the timers. **Default:** `1`.

**Note:** This diverges from how `setTimeout` in Node.js behaves and accepts
only positive numbers. In Node.js, `setTimeout` with negative numbers is
only supported for web compatibility reasons.

The following example mocks a `setTimeout` function and
by using `.tick` advances in
time triggering all pending timers.

*ES module version:*

```js
import assert from 'node:assert';
import { test } from 'node:test';

test('mocks setTimeout to be executed synchronously without having to actually wait for it', (context) => {
  const fn = context.mock.fn();

  context.mock.timers.enable({ apis: ['setTimeout'] });

  setTimeout(fn, 9999);

  assert.strictEqual(fn.mock.callCount(), 0);

  // Advance in time
  context.mock.timers.tick(9999);

  assert.strictEqual(fn.mock.callCount(), 1);
});
```

*CommonJS version:*

```js
const assert = require('node:assert');
const { test } = require('node:test');

test('mocks setTimeout to be executed synchronously without having to actually wait for it', (context) => {
  const fn = context.mock.fn();
  context.mock.timers.enable({ apis: ['setTimeout'] });

  setTimeout(fn, 9999);
  assert.strictEqual(fn.mock.callCount(), 0);

  // Advance in time
  context.mock.timers.tick(9999);

  assert.strictEqual(fn.mock.callCount(), 1);
});
```

Alternatively, the `.tick` function can be called many times

*ES module version:*

```js
import assert from 'node:assert';
import { test } from 'node:test';

test('mocks setTimeout to be executed synchronously without having to actually wait for it', (context) => {
  const fn = context.mock.fn();
  context.mock.timers.enable({ apis: ['setTimeout'] });
  const nineSecs = 9000;
  setTimeout(fn, nineSecs);

  const threeSeconds = 3000;
  context.mock.timers.tick(threeSeconds);
  context.mock.timers.tick(threeSeconds);
  context.mock.timers.tick(threeSeconds);

  assert.strictEqual(fn.mock.callCount(), 1);
});
```

*CommonJS version:*

```js
const assert = require('node:assert');
const { test } = require('node:test');

test('mocks setTimeout to be executed synchronously without having to actually wait for it', (context) => {
  const fn = context.mock.fn();
  context.mock.timers.enable({ apis: ['setTimeout'] });
  const nineSecs = 9000;
  setTimeout(fn, nineSecs);

  const threeSeconds = 3000;
  context.mock.timers.tick(threeSeconds);
  context.mock.timers.tick(threeSeconds);
  context.mock.timers.tick(threeSeconds);

  assert.strictEqual(fn.mock.callCount(), 1);
});
```

Advancing time using `.tick` will also advance the time for any `Date` object
created after the mock was enabled (if `Date` was also set to be mocked).

*ES module version:*

```js
import assert from 'node:assert';
import { test } from 'node:test';

test('mocks setTimeout to be executed synchronously without having to actually wait for it', (context) => {
  const fn = context.mock.fn();

  context.mock.timers.enable({ apis: ['setTimeout', 'Date'] });
  setTimeout(fn, 9999);

  assert.strictEqual(fn.mock.callCount(), 0);
  assert.strictEqual(Date.now(), 0);

  // Advance in time
  context.mock.timers.tick(9999);
  assert.strictEqual(fn.mock.callCount(), 1);
  assert.strictEqual(Date.now(), 9999);
});
```

*CommonJS version:*

```js
const assert = require('node:assert');
const { test } = require('node:test');

test('mocks setTimeout to be executed synchronously without having to actually wait for it', (context) => {
  const fn = context.mock.fn();
  context.mock.timers.enable({ apis: ['setTimeout', 'Date'] });

  setTimeout(fn, 9999);
  assert.strictEqual(fn.mock.callCount(), 0);
  assert.strictEqual(Date.now(), 0);

  // Advance in time
  context.mock.timers.tick(9999);
  assert.strictEqual(fn.mock.callCount(), 1);
  assert.strictEqual(Date.now(), 9999);
});
```

#### Using clear functions

As mentioned, all clear functions from timers (`clearTimeout`, `clearInterval`,and
`clearImmediate`) are implicitly mocked. Take a look at this example using `setTimeout`:

*ES module version:*

```js
import assert from 'node:assert';
import { test } from 'node:test';

test('mocks setTimeout to be executed synchronously without having to actually wait for it', (context) => {
  const fn = context.mock.fn();

  // Optionally choose what to mock
  context.mock.timers.enable({ apis: ['setTimeout'] });
  const id = setTimeout(fn, 9999);

  // Implicitly mocked as well
  clearTimeout(id);
  context.mock.timers.tick(9999);

  // As that setTimeout was cleared the mock function will never be called
  assert.strictEqual(fn.mock.callCount(), 0);
});
```

*CommonJS version:*

```js
const assert = require('node:assert');
const { test } = require('node:test');

test('mocks setTimeout to be executed synchronously without having to actually wait for it', (context) => {
  const fn = context.mock.fn();

  // Optionally choose what to mock
  context.mock.timers.enable({ apis: ['setTimeout'] });
  const id = setTimeout(fn, 9999);

  // Implicitly mocked as well
  clearTimeout(id);
  context.mock.timers.tick(9999);

  // As that setTimeout was cleared the mock function will never be called
  assert.strictEqual(fn.mock.callCount(), 0);
});
```

#### Working with Node.js timers modules

Once you enable mocking timers, [node:timers](54-timers.md),
[node:timers/promises](54-timers.md#timers-promises-api) modules,
and timers from the Node.js global context are enabled:

**Note:** Destructuring functions such as
`import { setTimeout } from 'node:timers'` is currently
not supported by this API.

*ES module version:*

```js
import assert from 'node:assert';
import { test } from 'node:test';
import nodeTimers from 'node:timers';
import nodeTimersPromises from 'node:timers/promises';

test('mocks setTimeout to be executed synchronously without having to actually wait for it', async (context) => {
  const globalTimeoutObjectSpy = context.mock.fn();
  const nodeTimerSpy = context.mock.fn();
  const nodeTimerPromiseSpy = context.mock.fn();

  // Optionally choose what to mock
  context.mock.timers.enable({ apis: ['setTimeout'] });
  setTimeout(globalTimeoutObjectSpy, 9999);
  nodeTimers.setTimeout(nodeTimerSpy, 9999);

  const promise = nodeTimersPromises.setTimeout(9999).then(nodeTimerPromiseSpy);

  // Advance in time
  context.mock.timers.tick(9999);
  assert.strictEqual(globalTimeoutObjectSpy.mock.callCount(), 1);
  assert.strictEqual(nodeTimerSpy.mock.callCount(), 1);
  await promise;
  assert.strictEqual(nodeTimerPromiseSpy.mock.callCount(), 1);
});
```

*CommonJS version:*

```js
const assert = require('node:assert');
const { test } = require('node:test');
const nodeTimers = require('node:timers');
const nodeTimersPromises = require('node:timers/promises');

test('mocks setTimeout to be executed synchronously without having to actually wait for it', async (context) => {
  const globalTimeoutObjectSpy = context.mock.fn();
  const nodeTimerSpy = context.mock.fn();
  const nodeTimerPromiseSpy = context.mock.fn();

  // Optionally choose what to mock
  context.mock.timers.enable({ apis: ['setTimeout'] });
  setTimeout(globalTimeoutObjectSpy, 9999);
  nodeTimers.setTimeout(nodeTimerSpy, 9999);

  const promise = nodeTimersPromises.setTimeout(9999).then(nodeTimerPromiseSpy);

  // Advance in time
  context.mock.timers.tick(9999);
  assert.strictEqual(globalTimeoutObjectSpy.mock.callCount(), 1);
  assert.strictEqual(nodeTimerSpy.mock.callCount(), 1);
  await promise;
  assert.strictEqual(nodeTimerPromiseSpy.mock.callCount(), 1);
});
```

In Node.js, `setInterval` from [node:timers/promises](54-timers.md#timers-promises-api)
is an `AsyncGenerator` and is also supported by this API:

*ES module version:*

```js
import assert from 'node:assert';
import { test } from 'node:test';
import nodeTimersPromises from 'node:timers/promises';
test('should tick five times testing a real use case', async (context) => {
  context.mock.timers.enable({ apis: ['setInterval'] });

  const expectedIterations = 3;
  const interval = 1000;
  const startedAt = Date.now();
  async function run() {
    const times = [];
    for await (const time of nodeTimersPromises.setInterval(interval, startedAt)) {
      times.push(time);
      if (times.length === expectedIterations) break;
    }
    return times;
  }

  const r = run();
  context.mock.timers.tick(interval);
  context.mock.timers.tick(interval);
  context.mock.timers.tick(interval);

  const timeResults = await r;
  assert.strictEqual(timeResults.length, expectedIterations);
  for (let it = 1; it < expectedIterations; it++) {
    assert.strictEqual(timeResults[it - 1], startedAt + (interval * it));
  }
});
```

*CommonJS version:*

```js
const assert = require('node:assert');
const { test } = require('node:test');
const nodeTimersPromises = require('node:timers/promises');
test('should tick five times testing a real use case', async (context) => {
  context.mock.timers.enable({ apis: ['setInterval'] });

  const expectedIterations = 3;
  const interval = 1000;
  const startedAt = Date.now();
  async function run() {
    const times = [];
    for await (const time of nodeTimersPromises.setInterval(interval, startedAt)) {
      times.push(time);
      if (times.length === expectedIterations) break;
    }
    return times;
  }

  const r = run();
  context.mock.timers.tick(interval);
  context.mock.timers.tick(interval);
  context.mock.timers.tick(interval);

  const timeResults = await r;
  assert.strictEqual(timeResults.length, expectedIterations);
  for (let it = 1; it < expectedIterations; it++) {
    assert.strictEqual(timeResults[it - 1], startedAt + (interval * it));
  }
});
```

### `timers.runAll()`

Added in: v20.4.0, v18.19.0

Triggers all pending mocked timers immediately. If the `Date` object is also
mocked, it will also advance the `Date` object to the furthest timer's time.

The example below triggers all pending timers immediately,
causing them to execute without any delay.

*ES module version:*

```js
import assert from 'node:assert';
import { test } from 'node:test';

test('runAll functions following the given order', (context) => {
  context.mock.timers.enable({ apis: ['setTimeout', 'Date'] });
  const results = [];
  setTimeout(() => results.push(1), 9999);

  // Notice that if both timers have the same timeout,
  // the order of execution is guaranteed
  setTimeout(() => results.push(3), 8888);
  setTimeout(() => results.push(2), 8888);

  assert.deepStrictEqual(results, []);

  context.mock.timers.runAll();
  assert.deepStrictEqual(results, [3, 2, 1]);
  // The Date object is also advanced to the furthest timer's time
  assert.strictEqual(Date.now(), 9999);
});
```

*CommonJS version:*

```js
const assert = require('node:assert');
const { test } = require('node:test');

test('runAll functions following the given order', (context) => {
  context.mock.timers.enable({ apis: ['setTimeout', 'Date'] });
  const results = [];
  setTimeout(() => results.push(1), 9999);

  // Notice that if both timers have the same timeout,
  // the order of execution is guaranteed
  setTimeout(() => results.push(3), 8888);
  setTimeout(() => results.push(2), 8888);

  assert.deepStrictEqual(results, []);

  context.mock.timers.runAll();
  assert.deepStrictEqual(results, [3, 2, 1]);
  // The Date object is also advanced to the furthest timer's time
  assert.strictEqual(Date.now(), 9999);
});
```

**Note:** The `runAll()` function is specifically designed for
triggering timers in the context of timer mocking.
It does not have any effect on real-time system
clocks or actual timers outside of the mocking environment.

### `timers.setTime(milliseconds)`

Added in: v21.2.0, v20.11.0

Sets the current Unix timestamp that will be used as reference for any mocked
`Date` objects.

*ES module version:*

```js
import assert from 'node:assert';
import { test } from 'node:test';

test('runAll functions following the given order', (context) => {
  const now = Date.now();
  const setTime = 1000;
  // Date.now is not mocked
  assert.deepStrictEqual(Date.now(), now);

  context.mock.timers.enable({ apis: ['Date'] });
  context.mock.timers.setTime(setTime);
  // Date.now is now 1000
  assert.strictEqual(Date.now(), setTime);
});
```

*CommonJS version:*

```js
const assert = require('node:assert');
const { test } = require('node:test');

test('setTime replaces current time', (context) => {
  const now = Date.now();
  const setTime = 1000;
  // Date.now is not mocked
  assert.deepStrictEqual(Date.now(), now);

  context.mock.timers.enable({ apis: ['Date'] });
  context.mock.timers.setTime(setTime);
  // Date.now is now 1000
  assert.strictEqual(Date.now(), setTime);
});
```

#### Dates and Timers working together

Dates and timer objects are dependent on each other. If you use `setTime()` to
pass the current time to the mocked `Date` object, the set timers with
`setTimeout` and `setInterval` will **not** be affected.

However, the `tick` method **will** advance the mocked `Date` object.

*ES module version:*

```js
import assert from 'node:assert';
import { test } from 'node:test';

test('runAll functions following the given order', (context) => {
  context.mock.timers.enable({ apis: ['setTimeout', 'Date'] });
  const results = [];
  setTimeout(() => results.push(1), 9999);

  assert.deepStrictEqual(results, []);
  context.mock.timers.setTime(12000);
  assert.deepStrictEqual(results, []);
  // The date is advanced but the timers don't tick
  assert.strictEqual(Date.now(), 12000);
});
```

*CommonJS version:*

```js
const assert = require('node:assert');
const { test } = require('node:test');

test('runAll functions following the given order', (context) => {
  context.mock.timers.enable({ apis: ['setTimeout', 'Date'] });
  const results = [];
  setTimeout(() => results.push(1), 9999);

  assert.deepStrictEqual(results, []);
  context.mock.timers.setTime(12000);
  assert.deepStrictEqual(results, []);
  // The date is advanced but the timers don't tick
  assert.strictEqual(Date.now(), 12000);
});
```

## Class: `TestsStream`

Added in: v18.9.0, v16.19.0

**History**

| Version | Changes |
| --- | --- |
| v26.6.0 | Added `entryFile` to events forwarded from child processes when tests run with process isolation. |
| v26.3.0 | Added `parentId` to test events that carry a `testId`. |
| v20.0.0, v19.9.0, v18.17.0 | added type to test:pass and test:fail events for when the test is a suite. |

- Extends [`<Readable>`](51-stream-part1.md#class-streamreadable)

A successful call to [`run()`](53-test-part1.md#runoptions) method will return a new [`<TestsStream>`](53-test-part2.md#class-testsstream)
object, streaming a series of events representing the execution of the tests. `TestsStream` will emit events, in the order of the tests definition

Some of the events are guaranteed to be emitted in the same order as the tests
are defined, while others are emitted in the order that the tests execute.

The following tables summarize all events by scope.

Test scoped events are emitted once per test or suite. Most of them come in
pairs: a declaration ordered event, buffered so that events are emitted in the
same order as the tests are defined, and one or more corresponding execution
ordered events, emitted immediately as the tests execute.

| Declaration ordered (buffered) | Execution ordered (immediate) |
| --- | --- |
| [`'test:start'`](53-test-part3.md#event-teststart) | [`'test:enqueue'`](53-test-part3.md#event-testenqueue) followed by [`'test:dequeue'`](53-test-part3.md#event-testdequeue) |
| [`'test:pass'`](53-test-part3.md#event-testpass) | [`'test:complete'`](53-test-part3.md#event-testcomplete) (`details.passed` is `true`) |
| [`'test:fail'`](53-test-part3.md#event-testfail) | [`'test:complete'`](53-test-part3.md#event-testcomplete) (`details.passed` is `false`) |
| [`'test:plan'`](53-test-part3.md#event-testplan) |  |
| [`'test:diagnostic'`](53-test-part3.md#event-testdiagnostic) |  |
|  | [`'test:log'`](53-test-part3.md#event-testlog) |

[`'test:log'`](53-test-part3.md#event-testlog) is deliberately execution ordered only: it is the live
counterpart of [`'test:diagnostic'`](53-test-part3.md#event-testdiagnostic)'s buffered reporting.

File scoped and global events are always emitted immediately, in execution
order.

File scoped events are emitted once per test file:

| Event | Notes |
| --- | --- |
| [`'test:stderr'`](53-test-part3.md#event-teststderr) | Only emitted if the `--test` flag is passed. |
| [`'test:stdout'`](53-test-part3.md#event-teststdout) | Only emitted if the `--test` flag is passed. |
| [`'test:summary'`](53-test-part3.md#event-testsummary) | Per file, only when process isolation is used. |

Global events are emitted once per test run:

| Event | Notes |
| --- | --- |
| [`'test:summary'`](53-test-part3.md#event-testsummary) | The final cumulative summary. |
| [`'test:coverage'`](53-test-part3.md#event-testcoverage) | Only when code coverage is enabled. |
| [`'test:interrupted'`](53-test-part3.md#event-testinterrupted) | Only when the run receives `SIGINT`. |
| [`'test:watch:drained'`](53-test-part3.md#event-testwatchdrained) | Watch mode only. |
| [`'test:watch:restarted'`](53-test-part3.md#event-testwatchrestarted) | Watch mode only. |

The root test also emits [`'test:plan'`](53-test-part3.md#event-testplan) and [`'test:diagnostic'`](53-test-part3.md#event-testdiagnostic) events
at the end of the run to report run level totals.

[Contents](../README.md) | [Previous: Test runner, part 1 of 4](53-test-part1.md) | [Next: Test runner, part 3 of 4](53-test-part3.md)
