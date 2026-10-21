[Contents](../README.md) | [Previous: Test runner, part 3 of 4](53-test-part3.md) | [Next: Timers](54-timers.md)

Source: [https://nodejs.org/docs/latest/api/test.html](https://nodejs.org/docs/latest/api/test.html)

Part 4 of 4 of this section.

# Test runner (part 4 of 4)

#### `context.assert.lastCalledWith(fn[, ...args])`

Added in: v26.12.0

- `fn` [`<Function>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Function) A mock function created by the test runner's mocking API.
- `...args` [`<any>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#Data_types) The expected arguments.

Asserts that the most recent call to the mock function `fn` received arguments
deeply and strictly equal to `args`, using the same comparison as
[`assert.deepStrictEqual()`](03-assert.md#assertdeepstrictequalactual-expected-message).

```js
test('mock was last called with arguments', (t) => {
  const fn = t.mock.fn();
  fn(1);
  fn(2);
  t.assert.lastCalledWith(fn, 2);
});
```

#### `context.assert.nthCalledWith(fn, n[, ...args])`

Added in: v26.12.0

- `fn` [`<Function>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Function) A mock function created by the test runner's mocking API.
- `n` [`<integer>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) The 1-based index of the call to check.
- `...args` [`<any>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#Data_types) The expected arguments.

Asserts that the `n`th call to the mock function `fn` received arguments
deeply and strictly equal to `args`, using the same comparison as
[`assert.deepStrictEqual()`](03-assert.md#assertdeepstrictequalactual-expected-message). `n` starts at `1`, so
`t.assert.nthCalledWith(fn, 1)` checks `fn.mock.calls[0]`.

```js
test('mock was called with arguments on the second call', (t) => {
  const fn = t.mock.fn();
  fn(1);
  fn(2);
  t.assert.nthCalledWith(fn, 2, 2);
});
```

#### `context.assert.snapshot(value[, options])`

Added in: v22.3.0

- `value` [`<any>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#Data_types) A value to serialize to a string. If Node.js was started with
  the [`--test-update-snapshots`](13-cli.md#--test-update-snapshots) flag, the serialized value is written to
  the snapshot file. Otherwise, the serialized value is compared to the
  corresponding value in the existing snapshot file.
- `options` [`<Object>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Object) Optional configuration options. The following properties
  are supported:
  - `serializers` [`<Array>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Array) An array of synchronous functions used to serialize `value` into a string. `value` is passed as the only argument to the first
    serializer function. The return value of each serializer is passed as input
    to the next serializer. Once all serializers have run, the resulting value
    is coerced to a string. **Default:** If no serializers are provided, the
    test runner's default serializers are used.

This function implements assertions for snapshot testing.

```js
test('snapshot test with default serialization', (t) => {
  t.assert.snapshot({ value1: 1, value2: 2 });
});

test('snapshot test with custom serialization', (t) => {
  t.assert.snapshot({ value3: 3, value4: 4 }, {
    serializers: [(value) => JSON.stringify(value)],
  });
});
```

### `context.diagnostic(message)`

Added in: v18.0.0, v16.17.0

- `message` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) Message to be reported.

This function is used to write diagnostics to the output. Any diagnostic
information is included at the end of the test's results. This function does
not return a value.

```js
test('top level test', (t) => {
  t.diagnostic('A diagnostic message');
});
```

### `context.log(message[, data])`

Added in: v26.6.0

- `message` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) Message to be reported.
- `data` [`<any>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#Data_types) Optional structured payload attached to the message. The test
  runner passes it through untouched. When tests run with process isolation,
  this value must be compatible with the [HTML structured clone algorithm](https://developer.mozilla.org/en-US/docs/Web/API/Web_Workers_API/Structured_clone_algorithm).

This function is used to write a log message to the output. Unlike
[`context.diagnostic`](#contextdiagnosticmessage), the resulting [`'test:log'`](53-test-part3.md#event-testlog) event is emitted
immediately, in the order that the tests execute, rather than being buffered
until the test reports its results. This function does not return a value.

```js
test('top level test', (t) => {
  t.log('fetched user', { userId: 42 });
  t.log('retrying flaky endpoint', { attempt: 3 });
});
```

### `context.filePath`

Added in: v22.6.0, v20.16.0

The absolute path of the test file that created the current test. If a test file
imports additional modules that generate tests, the imported tests will return
the path of the root test file.

### `context.fullName`

Added in: v22.3.0, v20.16.0

The name of the test and each of its ancestors, separated by `>`.

### `context.name`

Added in: v18.8.0, v16.18.0

The name of the test.

### `context.passed`

Added in: v21.7.0, v20.12.0

- Type: [`<boolean>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#boolean_type) `false` before the test is executed, e.g. in a `beforeEach` hook.

Indicated whether the test succeeded.

### `context.error`

Added in: v21.7.0, v20.12.0

- Type: [`<Error>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Error) | [`<null>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#null_type)

The failure reason for the test/case; wrapped and available via `context.error.cause`.

### `context.attempt`

Added in: v25.0.0

- Type: [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type)

The attempt number of the test. This value is zero-based, so the first attempt is `0`,
the second attempt is `1`, and so on. This property is useful in conjunction with the
`--test-rerun-failures` option to determine which attempt the test is currently running.

### `context.tags`

Added in: v26.2.0

Stability: 1.0 - Early development

- Type: [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type)[]

A frozen array of the test's flattened lowercased tags, in declaration
order, including any tags inherited from ancestor suites. Empty when the
test has no tags. See [Test tags](53-test-part1.md#test-tags).

### `context.workerId`

Added in: v25.8.0

- Type: [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) | [`<undefined>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#undefined_type)

The unique identifier of the worker running the current test file. This value is
derived from the `NODE_TEST_WORKER_ID` environment variable. When running tests
with `--test-isolation=process` (the default), each test file runs in a separate
child process and is assigned a worker ID from 1 to N, where N is the number of
concurrent workers. A worker ID is never shared by two test files running at the
same time. Once a test file finishes, its worker ID is reused by the next test
file that starts. When running with `--test-isolation=none`, all tests run in
the same process and the worker ID is always 1. This value is `undefined` when
not running in a test context.

This property is useful for splitting resources (like database connections or
server ports) across concurrent test files:

```js
import { test } from 'node:test';
import { process } from 'node:process';

test('database operations', async (t) => {
  // Worker ID is available via context
  console.log(`Running in worker ${t.workerId}`);

  // Or via environment variable (available at import time)
  const workerId = process.env.NODE_TEST_WORKER_ID;
  // Use workerId to allocate separate resources per worker
});
```

### `context.plan(count[,options])`

Added in: v22.2.0, v20.15.0

**History**

| Version | Changes |
| --- | --- |
| v23.9.0, v22.15.0 | Add the `options` parameter. |
| v23.4.0, v22.13.0 | This function is no longer experimental. |

- `count` [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) The number of assertions and subtests that are expected to run.
- `options` [`<Object>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Object) Additional options for the plan.
  - `wait` [`<boolean>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#boolean_type) | [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) The wait time for the plan:
    - If `true`, the plan waits indefinitely for all assertions and subtests to run.
    - If `false`, the plan performs an immediate check after the test function completes,
      without waiting for any pending assertions or subtests.
      Any assertions or subtests that complete after this check will not be counted towards the plan.
    - If a number, it specifies the maximum wait time in milliseconds
      before timing out while waiting for expected assertions and subtests to be matched.
      If the timeout is reached, the test will fail.
      **Default:** `false`.

This function is used to set the number of assertions and subtests that are expected to run
within the test. If the number of assertions and subtests that run does not match the
expected count, the test will fail.

> Note: To make sure assertions are tracked, `t.assert` must be used instead of `assert` directly.

```js
test('top level test', (t) => {
  t.plan(2);
  t.assert.ok('some relevant assertion here');
  t.test('subtest', () => {});
});
```

When working with asynchronous code, the `plan` function can be used to ensure that the
correct number of assertions are run:

```js
test('planning with streams', (t, done) => {
  function* generate() {
    yield 'a';
    yield 'b';
    yield 'c';
  }
  const expected = ['a', 'b', 'c'];
  t.plan(expected.length);
  const stream = Readable.from(generate());
  stream.on('data', (chunk) => {
    t.assert.strictEqual(chunk, expected.shift());
  });

  stream.on('end', () => {
    done();
  });
});
```

When using the `wait` option, you can control how long the test will wait for the expected assertions.
For example, setting a maximum wait time ensures that the test will wait for asynchronous assertions
to complete within the specified timeframe:

```js
test('plan with wait: 2000 waits for async assertions', (t) => {
  t.plan(1, { wait: 2000 }); // Waits for up to 2 seconds for the assertion to complete.

  const asyncActivity = () => {
    setTimeout(() => {
      t.assert.ok(true, 'Async assertion completed within the wait time');
    }, 1000); // Completes after 1 second, within the 2-second wait time.
  };

  asyncActivity(); // The test will pass because the assertion is completed in time.
});
```

Note: If a `wait` timeout is specified, it begins counting down only after the test function finishes executing.

### `context.runOnly(shouldRunOnlyTests)`

Added in: v18.0.0, v16.17.0

- `shouldRunOnlyTests` [`<boolean>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#boolean_type) Whether or not to run `only` tests.

If `shouldRunOnlyTests` is truthy, the test context will only run tests that
have the `only` option set. Otherwise, all tests are run. If Node.js was not
started with the [`--test-only`](13-cli.md#--test-only) command-line option, this function is a
no-op.

```js
test('top level test', (t) => {
  // The test context can be set to run subtests with the 'only' option.
  t.runOnly(true);
  return Promise.all([
    t.test('this subtest is now skipped'),
    t.test('this subtest is run', { only: true }),
  ]);
});
```

### `context.signal`

Added in: v18.7.0, v16.17.0

- Type: [`<AbortSignal>`](26-globals.md#class-abortsignal)

Can be used to abort test subtasks when the test has been aborted.

```js
test('top level test', async (t) => {
  await fetch('some/uri', { signal: t.signal });
});
```

### `context.skip([message])`

Added in: v18.0.0, v16.17.0

- `message` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) Optional skip message.

This function causes the test's output to indicate the test as skipped. If
`message` is provided, it is included in the output. Calling `skip()` does
not terminate execution of the test function. This function does not return a
value.

```js
test('top level test', (t) => {
  // Make sure to return here as well if the test contains additional logic.
  t.skip('this is skipped');
});
```

### `context.todo([message])`

Added in: v18.0.0, v16.17.0

- `message` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) Optional `TODO` message.

This function adds a `TODO` directive to the test's output. If `message` is
provided, it is included in the output. Calling `todo()` does not terminate
execution of the test function. This function does not return a value.

```js
test('top level test', (t) => {
  // This test is marked as `TODO`
  t.todo('this is a todo');
});
```

### `context.test([name][, options][, fn])`

Added in: v18.0.0, v16.17.0

**History**

| Version | Changes |
| --- | --- |
| v26.2.0 | Added the `tags` option. |
| v18.8.0, v16.18.0 | Add a `signal` option. |
| v18.7.0, v16.17.0 | Add a `timeout` option. |

- `name` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) The name of the subtest, which is displayed when reporting
  test results. **Default:** The `name` property of `fn`, or `'<anonymous>'` if
  `fn` does not have a name.
- `options` [`<Object>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Object) Configuration options for the subtest. The following
  properties are supported:
  - `concurrency` [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) | [`<boolean>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#boolean_type) | [`<null>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#null_type) If a number is provided,
    then that many tests would run asynchronously (they are still managed by the single-threaded event loop).
    If `true`, it would run all subtests in parallel.
    If `false`, it would only run one test at a time.
    If unspecified, subtests inherit this value from their parent.
    **Default:** `null`.
  - `only` [`<boolean>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#boolean_type) If truthy, and the test context is configured to run `only` tests, then this test will be run. Otherwise, the test is skipped.
    **Default:** `false`.
  - `signal` [`<AbortSignal>`](26-globals.md#class-abortsignal) Allows aborting an in-progress test.
  - `skip` [`<boolean>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#boolean_type) | [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) If truthy, the test is skipped. If a string is
    provided, that string is displayed in the test results as the reason for
    skipping the test. **Default:** `false`.
  - `tags` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type)[] An array of string labels associated with the subtest.
    Used together with [`--experimental-test-tag-filter`](13-cli.md#--experimental-test-tag-filtertag) to filter which
    tests run. Tags inherit from the parent test or suite by union. See
    [Test tags](53-test-part1.md#test-tags). **Default:** `[]`.
  - `todo` [`<boolean>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#boolean_type) | [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) If truthy, the test marked as `TODO`. If a string
    is provided, that string is displayed in the test results as the reason why
    the test is `TODO`. **Default:** `false`.
  - `timeout` [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) A number of milliseconds the test will fail after.
    If unspecified, subtests inherit this value from their parent. **Default:** `Infinity`.
  - `plan` [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) The number of assertions and subtests expected to be run in the test.
    If the number of assertions run in the test does not match the number
    specified in the plan, the test will fail. **Default:** `undefined`.
- `fn` [`<Function>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Function) | [`<AsyncFunction>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/AsyncFunction) The function under test. The first argument
  to this function is a [`TestContext`](53-test-part3.md#class-testcontext) object. If the test uses callbacks,
  the callback function is passed as the second argument. **Default:** A no-op
  function.
- Returns: [`<Promise>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Promise) Fulfilled with `undefined` once the test completes.

This function is used to create subtests under the current test. This function
behaves in the same fashion as the top level [`test()`](53-test-part2.md#testname-options-fn) function.

```js
test('top level test', async (t) => {
  await t.test(
    'This is a subtest',
    { only: false, skip: false, concurrency: 1, todo: false, plan: 1 },
    (t) => {
      t.assert.ok('some relevant assertion here');
    },
  );
});
```

### `context.waitFor(condition[, options])`

Added in: v23.7.0, v22.14.0

- `condition` [`<Function>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Function) | [`<AsyncFunction>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/AsyncFunction) An assertion function that is invoked
  periodically until it completes successfully or the defined polling timeout
  elapses. Successful completion is defined as not throwing or rejecting. This
  function does not accept any arguments, and is allowed to return any value.
- `options` [`<Object>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Object) An optional configuration object for the polling operation.
  The following properties are supported:
  - `interval` [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) The number of milliseconds to wait after an unsuccessful
    invocation of `condition` before trying again. **Default:** `50`.
  - `timeout` [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) The poll timeout in milliseconds. If `condition` has not
    succeeded by the time this elapses, an error occurs. **Default:** `1000`.
- Returns: [`<Promise>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Promise) Fulfilled with the value returned by `condition`.

This method polls a `condition` function until that function either returns
successfully or the operation times out.

## Class: `SuiteContext`

Added in: v18.7.0, v16.17.0

An instance of `SuiteContext` is passed to each suite function in order to
interact with the test runner. However, the `SuiteContext` constructor is not
exposed as part of the API.

### `context.filePath`

Added in: v22.6.0

The absolute path of the test file that created the current suite. If a test
file imports additional modules that generate suites, the imported suites will
return the path of the root test file.

### `context.fullName`

Added in: v22.3.0, v20.16.0

The name of the suite and each of its ancestors, separated by `>`.

### `context.name`

Added in: v18.8.0, v16.18.0

The name of the suite.

### `context.signal`

Added in: v18.7.0, v16.17.0

- Type: [`<AbortSignal>`](26-globals.md#class-abortsignal)

Can be used to abort test subtasks when the test has been aborted.

### `context.passed`

Added in: v26.1.0

- Type: [`<boolean>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#boolean_type)

Indicates whether the suite and all of its subtests have passed.

### `context.attempt`

Added in: v26.1.0

- Type: [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type)

The attempt number of the suite. This value is zero-based, so the first attempt is `0`,
the second attempt is `1`, and so on. This property is useful in conjunction with the
`--test-rerun-failures` option to determine the attempt number of the current run.

### `context.diagnostic(message)`

Added in: v26.1.0

- `message` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) A diagnostic message to output.

Output a diagnostic message. This is typically used for logging information
about the current suite or its tests.

```js
test.describe('my suite', (suite) => {
  suite.diagnostic('Suite diagnostic message');
});
```

### `context.log(message[, data])`

Added in: v26.6.0

- `message` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) Message to be reported.
- `data` [`<any>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#Data_types) Optional structured payload attached to the message. The test
  runner passes it through untouched.

Write a log message to the output. The resulting [`'test:log'`](53-test-part3.md#event-testlog) event is
emitted immediately, in the order that the tests execute.

```js
test.describe('my suite', (suite) => {
  suite.log('Suite log message');
});
```

[Contents](../README.md) | [Previous: Test runner, part 3 of 4](53-test-part3.md) | [Next: Timers](54-timers.md)
