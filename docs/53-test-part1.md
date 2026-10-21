[Contents](../README.md) | [Previous: String decoder](52-string-decoder.md) | [Next: Test runner, part 2 of 4](53-test-part2.md)

Source: [https://nodejs.org/docs/latest/api/test.html](https://nodejs.org/docs/latest/api/test.html)

Part 1 of 4 of this section.

# Test runner

Added in: v18.0.0, v16.17.0

**History**

| Version | Changes |
| --- | --- |
| v20.0.0 | The test runner is now stable. |

[Stability: 2](01-documentation.md#stability-index) - Stable

The `node:test` module facilitates the creation of JavaScript tests.
To access it:

*ES module version:*

```js
import test from 'node:test';
```

*CommonJS version:*

```js
const test = require('node:test');
```

This module is only available under the `node:` scheme.

Tests created via the `test` module consist of a single function that is
processed in one of three ways:

1. A synchronous function that is considered failing if it throws an exception,
   and is considered passing otherwise.
2. A function that returns a `Promise` that is considered failing if the
   `Promise` rejects, and is considered passing if the `Promise` fulfills.
3. A function that receives a callback function. If the callback receives any
   truthy value as its first argument, the test is considered failing. If a
   falsy value is passed as the first argument to the callback, the test is
   considered passing. If the test function receives a callback function and
   also returns a `Promise`, the test will fail.

The following example illustrates how tests are written using the
`test` module.

```js
test('synchronous passing test', (t) => {
  // This test passes because it does not throw an exception.
  assert.strictEqual(1, 1);
});

test('synchronous failing test', (t) => {
  // This test fails because it throws an exception.
  assert.strictEqual(1, 2);
});

test('asynchronous passing test', async (t) => {
  // This test passes because the Promise returned by the async
  // function is settled and not rejected.
  assert.strictEqual(1, 1);
});

test('asynchronous failing test', async (t) => {
  // This test fails because the Promise returned by the async
  // function is rejected.
  assert.strictEqual(1, 2);
});

test('failing test using Promises', (t) => {
  // Promises can be used directly as well.
  return new Promise((resolve, reject) => {
    setImmediate(() => {
      reject(new Error('this will cause the test to fail'));
    });
  });
});

test('callback passing test', (t, done) => {
  // done() is the callback function. When the setImmediate() runs, it invokes
  // done() with no arguments.
  setImmediate(done);
});

test('callback failing test', (t, done) => {
  // When the setImmediate() runs, done() is invoked with an Error object and
  // the test fails.
  setImmediate(() => {
    done(new Error('callback failure'));
  });
});
```

If any tests fail, the process exit code is set to `1`.

## Subtests

The test context's `test()` method allows subtests to be created.
It allows you to structure your tests in a hierarchical manner,
where you can create nested tests within a larger test.
This method behaves identically to the top level `test()` function.
The following example demonstrates the creation of a
top level test with two subtests.

```js
test('top level test', async (t) => {
  await t.test('subtest 1', (t) => {
    assert.strictEqual(1, 1);
  });

  await t.test('subtest 2', (t) => {
    assert.strictEqual(2, 2);
  });
});
```

> **Note:** `beforeEach` and `afterEach` hooks are triggered
> between each subtest execution.

In this example, `await` is used to ensure that both subtests have completed.
This is necessary because tests do not wait for their subtests to
complete, unlike tests created within suites.
Any subtests that are still outstanding when their parent finishes
are cancelled and treated as failures. Any subtest failures cause the parent
test to fail.

## Rerunning failed tests

The test runner supports persisting the state of the run to a file, allowing
the test runner to rerun failed tests without having to re-run the entire test suite.
Use the [`--test-rerun-failures`](13-cli.md#--test-rerun-failures) command-line option to specify a file path where the
state of the run is stored. if the state file does not exist, the test runner will
create it.
the state file is a JSON file that contains an array of run attempts.
Each run attempt is an object mapping successful tests to the attempt they have passed in.
The key identifying a test in this map is the test file path, with the line and column where the test is defined.
in a case where a test defined in a specific location is run multiple times,
for example within a function or a loop,
a counter will be appended to the key, to disambiguate the test runs.
note changing the order of test execution or the location of a test can lead the test runner
to consider tests as passed on a previous attempt,
meaning `--test-rerun-failures` should be used when tests run in a deterministic order.

example of a state file:

```json
[
  {
    "test.js:10:5": { "passed_on_attempt": 0, "name": "test 1" }
  },
  {
    "test.js:10:5": { "passed_on_attempt": 0, "name": "test 1" },
    "test.js:20:5": { "passed_on_attempt": 1, "name": "test 2" }
  }
]
```

in this example, there are two run attempts, with two tests defined in `test.js`,
the first test succeeded on the first attempt, and the second test succeeded on the second attempt.

When the `--test-rerun-failures` option is used, the test runner will only run tests that have not yet passed.
A `todo` test that fails counts as not yet passed and is rerun, even though its failure does not
cause the test run to fail.

```bash
node --test-rerun-failures /path/to/state/file
```

## `describe()` and `it()` aliases

Suites and tests can also be written using the `describe()` and `it()`
functions. [`describe()`](53-test-part2.md#describename-options-fn) is an alias for [`suite()`](53-test-part2.md#suitename-options-fn), and [`it()`](53-test-part2.md#itname-options-fn) is an
alias for [`test()`](53-test-part2.md#testname-options-fn).

```js
describe('A thing', () => {
  it('should work', () => {
    assert.strictEqual(1, 1);
  });

  it('should be ok', () => {
    assert.strictEqual(2, 2);
  });

  describe('a nested thing', () => {
    it('should work', () => {
      assert.strictEqual(3, 3);
    });
  });
});
```

`describe()` and `it()` are imported from the `node:test` module.

*ES module version:*

```js
import { describe, it } from 'node:test';
```

*CommonJS version:*

```js
const { describe, it } = require('node:test');
```

## Skipping tests

Individual tests can be skipped by passing the `skip` option to the test, or by
calling the test context's `skip()` method as shown in the
following example.

```js
// The skip option is used, but no message is provided.
test('skip option', { skip: true }, (t) => {
  // This code is never executed.
});

// The skip option is used, and a message is provided.
test('skip option with message', { skip: 'this is skipped' }, (t) => {
  // This code is never executed.
});

test('skip() method', (t) => {
  // Make sure to return here as well if the test contains additional logic.
  t.skip();
});

test('skip() method with message', (t) => {
  // Make sure to return here as well if the test contains additional logic.
  t.skip('this is skipped');
});
```

## TODO tests

Individual tests can be marked as flaky or incomplete by passing the `todo`
option to the test, or by calling the test context's `todo()` method, as shown
in the following example. These tests represent a pending implementation or bug
that needs to be fixed. TODO tests are executed, but are not treated as test
failures, and therefore do not affect the process exit code. If a test is marked
as both TODO and skipped, the TODO option is ignored.

```js
// The todo option is used, but no message is provided.
test('todo option', { todo: true }, (t) => {
  // This code is executed, but not treated as a failure.
  throw new Error('this does not fail the test');
});

// The todo option is used, and a message is provided.
test('todo option with message', { todo: 'this is a todo test' }, (t) => {
  // This code is executed.
});

test('todo() method', (t) => {
  t.todo();
});

test('todo() method with message', (t) => {
  t.todo('this is a todo test and is not treated as a failure');
  throw new Error('this does not fail the test');
});
```

## Expecting tests to fail

Added in: v25.5.0, v24.14.0

This flips the pass/fail reporting for a specific test or suite: a flagged test
case must throw in order to pass, and a flagged test case that does not throw
fails.

In each of the following, `doTheThing()` fails to return `true`, but since the
tests are flagged `expectFailure`, they pass.

```js
it.expectFailure('should do the thing', () => {
  assert.strictEqual(doTheThing(), true);
});

it('should do the thing', { expectFailure: true }, () => {
  assert.strictEqual(doTheThing(), true);
});

it('should do the thing', { expectFailure: 'feature not implemented' }, () => {
  assert.strictEqual(doTheThing(), true);
});
```

If the value of `expectFailure` is a [`<RegExp>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/RegExp) | [`<Function>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Function) | [`<Object>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Object) | [`<Error>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Error)
the tests will pass only if they throw a matching value.
See [`assert.throws`](03-assert.md#assertthrowsfn-error-message) for how each value type is handled.

Each of the following tests fails *despite* being flagged `expectFailure`
because the failure does not match the specific **expected** failure.

```js
it('fails because regex does not match', {
  expectFailure: /expected message/,
}, () => {
  throw new Error('different message');
});

it('fails because object matcher does not match', {
  expectFailure: { code: 'ERR_EXPECTED' },
}, () => {
  const err = new Error('boom');
  err.code = 'ERR_ACTUAL';
  throw err;
});
```

To supply both a reason and specific error for `expectFailure`, use `{ label, match }`.

```js
it('should fail with specific error and reason', {
  expectFailure: {
    label: 'reason for failure',
    match: /error message/,
  },
}, () => {
  assert.strictEqual(doTheThing(), true);
});
```

`skip` and/or `todo` are mutually exclusive to `expectFailure`, and `skip` or `todo`
will "win" when both are applied (`skip` wins against both, and `todo` wins
against `expectFailure`).

These tests will be skipped (and not run):

```js
it.expectFailure('should do the thing', { skip: true }, () => {
  assert.strictEqual(doTheThing(), true);
});

it.skip('should do the thing', { expectFailure: true }, () => {
  assert.strictEqual(doTheThing(), true);
});
```

These tests will be marked "todo" (silencing errors):

```js
it.expectFailure('should do the thing', { todo: true }, () => {
  assert.strictEqual(doTheThing(), true);
});

it.todo('should do the thing', { expectFailure: true }, () => {
  assert.strictEqual(doTheThing(), true);
});
```

## `only` tests

If Node.js is started with the [`--test-only`](13-cli.md#--test-only) command-line option, or test
isolation is disabled, it is possible to skip all tests except for a selected
subset by passing the `only` option to the tests that should run. When a test
with the `only` option is set, all subtests are also run.
If a suite has the `only` option set, all tests within the suite are run,
unless it has descendants with the `only` option set, in which case only those
tests are run.

When using [subtests](#subtests) within a `test()`/`it()`, it is required to mark
all ancestor tests with the `only` option to run only a
selected subset of tests.

The test context's `runOnly()`
method can be used to implement the same behavior at the subtest level. Tests
that are not executed are omitted from the test runner output.

```js
// Assume Node.js is run with the --test-only command-line option.
// The suite's 'only' option is set, so these tests are run.
test('this test is run', { only: true }, async (t) => {
  // Within this test, all subtests are run by default.
  await t.test('running subtest');

  // The test context can be updated to run subtests with the 'only' option.
  t.runOnly(true);
  await t.test('this subtest is now skipped');
  await t.test('this subtest is run', { only: true });

  // Switch the context back to execute all tests.
  t.runOnly(false);
  await t.test('this subtest is now run');

  // Explicitly do not run these tests.
  await t.test('skipped subtest 3', { only: false });
  await t.test('skipped subtest 4', { skip: true });
});

// The 'only' option is not set, so this test is skipped.
test('this test is not run', () => {
  // This code is not run.
  throw new Error('fail');
});

describe('a suite', () => {
  // The 'only' option is set, so this test is run.
  it('this test is run', { only: true }, () => {
    // This code is run.
  });

  it('this test is not run', () => {
    // This code is not run.
    throw new Error('fail');
  });
});

describe.only('a suite', () => {
  // The 'only' option is set, so this test is run.
  it('this test is run', () => {
    // This code is run.
  });

  it('this test is run', () => {
    // This code is run.
  });
});
```

## Filtering tests by name

The [`--test-name-pattern`](13-cli.md#--test-name-pattern) command-line option can be used to only run
tests whose name matches the provided pattern, and the
[`--test-skip-pattern`](13-cli.md#--test-skip-pattern) option can be used to skip tests whose name
matches the provided pattern. Test name patterns are interpreted as
JavaScript regular expressions. The `--test-name-pattern` and
`--test-skip-pattern` options can be specified multiple times in order to run
nested tests. For each test that is executed, any corresponding test hooks,
such as `beforeEach()`, are also run. Tests that are not executed are omitted
from the test runner output.

Given the following test file, starting Node.js with the
`--test-name-pattern="test [1-3]"` option would cause the test runner to execute
`test 1`, `test 2`, and `test 3`. If `test 1` did not match the test name
pattern, then its subtests would not execute, despite matching the pattern. The
same set of tests could also be executed by passing `--test-name-pattern`
multiple times (e.g. `--test-name-pattern="test 1"`,
`--test-name-pattern="test 2"`, etc.).

```js
test('test 1', async (t) => {
  await t.test('test 2');
  await t.test('test 3');
});

test('Test 4', async (t) => {
  await t.test('Test 5');
  await t.test('test 6');
});
```

Test name patterns can also be specified using regular expression literals. This
allows regular expression flags to be used. In the previous example, starting
Node.js with `--test-name-pattern="/test [4-5]/i"` (or `--test-skip-pattern="/test [4-5]/i"`)
would match `Test 4` and `Test 5` because the pattern is case-insensitive.

To match a single test with a pattern, you can prefix it with all its ancestor
test names separated by space, to ensure it is unique.
For example, given the following test file:

```js
describe('test 1', (t) => {
  it('some test');
});

describe('test 2', (t) => {
  it('some test');
});
```

Starting Node.js with `--test-name-pattern="test 1 some test"` would match
only `some test` in `test 1`.

Test name patterns do not change the set of files that the test runner executes.

If both `--test-name-pattern` and `--test-skip-pattern` are supplied,
tests must satisfy **both** requirements in order to be executed.

## Test tags

Added in: v26.2.0

Stability: 1.0 - Early development

Tags annotate tests and suites with arbitrary string labels. The
[`--experimental-test-tag-filter`](13-cli.md#--experimental-test-tag-filtertag) CLI flag (or the `testTagFilters`
option on [`run()`](#runoptions)) selects tests by a boolean expression over those
labels.

Tags are an alternative to encoding metadata into test names. They are
useful for cross-cutting axes such as subsystem, speed bucket, flakiness,
or environment, where a name pattern would be brittle.

### Authoring tagged tests

Pass a `tags` array on any of `test()`, `it()`, `suite()`, or `describe()`.
Tags inherit from a suite to its child tests by union—a test inside a
suite tagged `['db']` that declares its own `tags: ['integration']`
effectively has both tags.

*ES module version:*

```js
import { describe, it } from 'node:test';

describe('database', { tags: ['db'] }, () => {
  it('reads a row');                                            // tags: ['db']
  it('writes a row', { tags: ['integration'] });                // tags: ['db', 'integration']
  it('reconnects after disconnect', { tags: ['flaky'] });       // tags: ['db', 'flaky']
});
```

*CommonJS version:*

```js
const { describe, it } = require('node:test');

describe('database', { tags: ['db'] }, () => {
  it('reads a row');                                            // tags: ['db']
  it('writes a row', { tags: ['integration'] });                // tags: ['db', 'integration']
  it('reconnects after disconnect', { tags: ['flaky'] });       // tags: ['db', 'flaky']
});
```

Tag values must be non-empty strings that contain no whitespace, no
operator characters (`& | ! ( ) *`), and are not the reserved words
`'and'`, `'or'`, or `'not'` in any casing. Tags are matched
case-insensitively; the canonical form is lowercase. Duplicates within a
single `tags` array are collapsed on the lowercased form, preserving the
first-seen declaration order.

Hooks (`before`, `after`, `beforeEach`, `afterEach`) do not declare their
own tags. They run as part of their owning suite, which carries the
suite's tags.

### Filtering syntax

The filter expression supports:

- Identifiers—any non-whitespace, non-operator characters. A literal
  identifier matches a tag of the same value (case-insensitive).
- `*` wildcards inside an identifier match any sequence of characters.
  A bare `*` matches any tagged test.
- Boolean operators with two equivalent forms:
  - `and` / `&&`
  - `or` / `||`
  - `not` / `!`
- Parentheses for grouping.

The word forms (`and`, `or`, `not`) require whitespace separation; the
punctuation forms do not.

#### Operator precedence

The expression is evaluated with the standard precedence
`not > and > or`. Binary operators are left-associative.

| Expression | Equivalent grouping |
| --- | --- |
| `a or b and c` | `a or (b and c)` |
| `not a and b` | `(not a) and b` |

Use parentheses to override:

| Expression | Selects |
| --- | --- |
| `(unit or smoke) and not slow` | unit-or-smoke tests that are not also slow |
| `db && !flaky` | db tests that are not flaky |
| `*` | every tagged test |

#### Untagged tests

Untagged tests behave as if they have an empty tag set. As a result:

| Filter expression | Untagged test | Why |
| --- | --- | --- |
| `db` | excluded | Positive match against an empty tag set is false |
| `*` | excluded | The bare wildcard requires at least one tag |
| `db or unit` | excluded | Both branches are false against an empty tag set |
| `not flaky` | included | Negation against an empty tag set is true |
| `not flaky and not slow` | included | Both negations are true against an empty tag set |
| `db or not flaky` | included | The negated branch is true |

For example, `--experimental-test-tag-filter='not flaky'` runs every test
that is not tagged `flaky`, including all untagged tests.

#### Composing multiple filters

[`--experimental-test-tag-filter`](13-cli.md#--experimental-test-tag-filtertag) may be specified more than once on the
command line. Multiple expressions compose by AND—a test must satisfy
every expression to run. The same applies to passing an array to
`testTagFilters` on [`run()`](#runoptions). The tag filter is also AND'd with
[`--test-name-pattern`](13-cli.md#--test-name-pattern), [`--test-skip-pattern`](13-cli.md#--test-skip-pattern), and `.only`
filtering.

#### Reading tags from inside a test

The [`TestContext`](53-test-part3.md#class-testcontext) object exposes the test's tags as a frozen array
through [`context.tags`](53-test-part4.md#contexttags), so tests can branch on their own metadata.

#### Errors

A tag value that violates the validation rules above throws
`ERR_INVALID_ARG_VALUE` at the registration site, before any test runs.
A non-array `tags` value throws `ERR_INVALID_ARG_TYPE`. A malformed
filter expression on the CLI causes the test runner to exit with a
non-zero status before running any test files.

## Extraneous asynchronous activity

Once a test function finishes executing, the results are reported as quickly
as possible while maintaining the order of the tests. However, it is possible
for the test function to generate asynchronous activity that outlives the test
itself. The test runner handles this type of activity, but does not delay the
reporting of test results in order to accommodate it.

In the following example, a test completes with two `setImmediate()`
operations still outstanding. The first `setImmediate()` attempts to create a
new subtest. Because the parent test has already finished and output its
results, the new subtest is immediately marked as failed, and reported later
to the [`<TestsStream>`](53-test-part2.md#class-testsstream).

The second `setImmediate()` creates an `uncaughtException` event.
`uncaughtException` and `unhandledRejection` events originating from a completed
test are marked as failed by the `test` module and reported as diagnostic
warnings at the top level by the [`<TestsStream>`](53-test-part2.md#class-testsstream).

```js
test('a test that creates asynchronous activity', (t) => {
  setImmediate(() => {
    t.test('subtest that is created too late', (t) => {
      throw new Error('error1');
    });
  });

  setImmediate(() => {
    throw new Error('error2');
  });

  // The test finishes after this line.
});
```

## Watch mode

Added in: v19.2.0, v18.13.0

Stability: 1 - Experimental

The Node.js test runner supports running in watch mode by passing the `--watch` flag:

```bash
node --test --watch
```

In watch mode, the test runner will watch for changes to test files and
their dependencies. When a change is detected, the test runner will
rerun the tests affected by the change.
The test runner will continue to run until the process is terminated.

## Global setup and teardown

Added in: v24.0.0

Stability: 1.0 - Early development

The test runner supports specifying a module that will be evaluated before all tests are executed and
can be used to setup global state or fixtures for tests. This is useful for preparing resources or setting up
shared state that is required by multiple tests.

This module can export any of the following:

- A `globalSetup` function which runs once before all tests start
- A `globalTeardown` function which runs once after all tests complete

The module is specified using the `--test-global-setup` flag when running tests from the command line.

*CommonJS version:*

```js
// setup-module.js
async function globalSetup() {
  // Setup shared resources, state, or environment
  console.log('Global setup executed');
  // Run servers, create files, prepare databases, etc.
}

async function globalTeardown() {
  // Clean up resources, state, or environment
  console.log('Global teardown executed');
  // Close servers, remove files, disconnect from databases, etc.
}

module.exports = { globalSetup, globalTeardown };
```

*ES module version:*

```js
// setup-module.mjs
export async function globalSetup() {
  // Setup shared resources, state, or environment
  console.log('Global setup executed');
  // Run servers, create files, prepare databases, etc.
}

export async function globalTeardown() {
  // Clean up resources, state, or environment
  console.log('Global teardown executed');
  // Close servers, remove files, disconnect from databases, etc.
}
```

If the global setup function throws an error, no tests will be run and the process will exit with a non-zero exit code.
The global teardown function will not be called in this case.

## Running tests from the command line

The Node.js test runner can be invoked from the command line by passing the
[`--test`](13-cli.md#--test) flag:

```bash
node --test
```

By default, Node.js will run all files matching these patterns:

- `**/*.test.{cjs,mjs,js}`
- `**/*-test.{cjs,mjs,js}`
- `**/*_test.{cjs,mjs,js}`
- `**/test-*.{cjs,mjs,js}`
- `**/test.{cjs,mjs,js}`
- `**/test/**/*.{cjs,mjs,js}`

Unless [`--no-strip-types`](13-cli.md#--no-strip-types) is supplied, the following
additional patterns are also matched:

- `**/*.test.{cts,mts,ts}`
- `**/*-test.{cts,mts,ts}`
- `**/*_test.{cts,mts,ts}`
- `**/test-*.{cts,mts,ts}`
- `**/test.{cts,mts,ts}`
- `**/test/**/*.{cts,mts,ts}`

Alternatively, one or more glob patterns can be provided as the
final argument(s) to the Node.js command, as shown below.
Glob patterns follow the behavior of [`glob(7)`](https://man7.org/linux/man-pages/man7/glob.7.html).
The glob patterns should be enclosed in double quotes on the command line to
prevent shell expansion, which can reduce portability across systems.

```bash
node --test "**/*.test.js" "**/*.spec.js"
```

### Randomizing tests execution order

Added in: v26.1.0

Stability: 1.0 - Early development

The test runner can randomize execution order to help detect
order-dependent tests. When enabled, the runner randomizes both discovered
test files and queued tests within each file. Use `--test-randomize` to
enable this mode.

```bash
node --test --test-randomize
```

When randomization is enabled, the test runner prints the seed used for the run
as a diagnostic message:

```text
Randomized test order seed: 12345
```

Use `--test-random-seed=<number>` to replay the same randomized order
deterministically. Supplying `--test-random-seed` also enables randomization,
so `--test-randomize` is optional when a seed is provided:

```bash
node --test --test-random-seed=12345
```

In most test files, randomization works automatically. One important exception
is when subtests are awaited one by one. In that pattern, each subtest starts
only after the previous one finishes, so the runner keeps declaration order
instead of randomizing it.

Example: this runs sequentially and is **not** randomized.

*ES module version:*

```js
import test from 'node:test';

test('math', async (t) => {
  for (const name of ['adds', 'subtracts', 'multiplies']) {
    // Sequentially awaiting each subtest preserves declaration order.
    await t.test(name, async () => {});
  }
});
```

*CommonJS version:*

```js
const test = require('node:test');

test('math', async (t) => {
  for (const name of ['adds', 'subtracts', 'multiplies']) {
    // Sequentially awaiting each subtest preserves declaration order.
    await t.test(name, async () => {});
  }
});
```

Using suite-style APIs such as `describe()`/`it()` or `suite()`/`test()`
still allows randomization, because sibling tests are enqueued together.

Example: this remains eligible for randomization.

*ES module version:*

```js
import { describe, it } from 'node:test';

describe('math', () => {
  it('adds', () => {});
  it('subtracts', () => {});
  it('multiplies', () => {});
});
```

*CommonJS version:*

```js
const { describe, it } = require('node:test');

describe('math', () => {
  it('adds', () => {});
  it('subtracts', () => {});
  it('multiplies', () => {});
});
```

`--test-randomize` and `--test-random-seed` are not supported with `--watch` mode.

Matching files are executed as test files.
More information on the test file execution can be found
in the [test runner execution model](#test-runner-execution-model) section.

### Test runner execution model

When process-level test isolation is enabled, each matching test file is
executed in a separate child process. The maximum number of child processes
running at any time is controlled by the [`--test-concurrency`](13-cli.md#--test-concurrency) flag. If the
child process finishes with an exit code of 0, the test is considered passing.
Otherwise, the test is considered to be a failure. Test files must be executable
by Node.js, but are not required to use the `node:test` module internally.

Each test file is executed as if it was a regular script. That is, if the test
file itself uses `node:test` to define tests, all of those tests will be
executed within a single application thread, regardless of the value of the
`concurrency` option of [`test()`](53-test-part2.md#testname-options-fn).

When process-level test isolation is disabled, each matching test file is
imported into the test runner process. Once all test files have been loaded, the
top level tests are executed with a concurrency of one. Because the test files
are all run within the same context, it is possible for tests to interact with
each other in ways that are not possible when isolation is enabled. For example,
if a test relies on global state, it is possible for that state to be modified
by a test originating from another file.

#### Child process option inheritance

When running tests in process isolation mode (the default), spawned child processes
inherit Node.js options from the parent process, including those specified in
[configuration files](13-cli.md#--config-filepath---config-file). However, certain flags are filtered out to enable proper
test runner functionality:

- `--test` - Prevented to avoid recursive test execution
- `--experimental-test-coverage` - Managed by the test runner
- `--experimental-test-tag-filter` - Filter expressions are validated by the parent
  process and re-emitted to child processes
- `--watch` - Watch mode is handled at the parent level
- `--test-reporter` - Reporting is managed by the parent process
- `--test-reporter-destination` - Output destinations are controlled by the parent
- `--config-file` - Config file paths are managed by the parent
- `--test-randomize` - Randomization is managed by the parent process and
  propagated to child processes
- `--test-random-seed` - Randomization seed is managed by the parent process and
  propagated to child processes

All other Node.js options from command line arguments, environment variables,
and configuration files are inherited by the child processes.

## Collecting code coverage

Stability: 1 - Experimental

When Node.js is started with the [`--experimental-test-coverage`](13-cli.md#--experimental-test-coverage)
command-line flag, code coverage is collected and statistics are reported once
all tests have completed. If the [`NODE_V8_COVERAGE`](13-cli.md#node_v8_coveragedir) environment variable is
used to specify a code coverage directory, the generated V8 coverage files are
written to that directory. Node.js core modules and files within
`node_modules/` directories are, by default, not included in the coverage report.
However, they can be explicitly included via the [`--test-coverage-include`](13-cli.md#--test-coverage-include) flag.
By default all the matching test files are excluded from the coverage report.
Exclusions can be overridden by using the [`--test-coverage-exclude`](13-cli.md#--test-coverage-exclude) flag.
If coverage is enabled, the coverage report is sent to any [test reporters](#test-reporters) via
the `'test:coverage'` event.

Coverage can be disabled on a series of lines using the following
comment syntax:

```js
/* node:coverage disable */
if (anAlwaysFalseCondition) {
  // Code in this branch will never be executed, but the lines are ignored for
  // coverage purposes. All lines following the 'disable' comment are ignored
  // until a corresponding 'enable' comment is encountered.
  console.log('this is never executed');
}
/* node:coverage enable */
```

Coverage can also be disabled for a specified number of lines. After the
specified number of lines, coverage will be automatically reenabled. If the
number of lines is not explicitly provided, a single line is ignored.

```js
/* node:coverage ignore next */
if (anAlwaysFalseCondition) { console.log('this is never executed'); }

/* node:coverage ignore next 3 */
if (anAlwaysFalseCondition) {
  console.log('this is never executed');
}
```

### Coverage reporters

The tap and spec reporters will print a summary of the coverage statistics.
There is also an lcov reporter that will generate an lcov file which can be
used as an in depth coverage report.

```bash
node --test --experimental-test-coverage --test-reporter=lcov --test-reporter-destination=lcov.info
```

- No test results are reported by this reporter.
- This reporter should ideally be used alongside another reporter.

## Mocking

The `node:test` module supports mocking during testing via a top-level `mock`
object. The following example creates a spy on a function that adds two numbers
together. The spy is then used to assert that the function was called as
expected.

*ES module version:*

```js
import assert from 'node:assert';
import { mock, test } from 'node:test';

test('spies on a function', () => {
  const sum = mock.fn((a, b) => {
    return a + b;
  });

  assert.strictEqual(sum.mock.callCount(), 0);
  assert.strictEqual(sum(3, 4), 7);
  assert.strictEqual(sum.mock.callCount(), 1);

  const call = sum.mock.calls[0];
  assert.deepStrictEqual(call.arguments, [3, 4]);
  assert.strictEqual(call.result, 7);
  assert.strictEqual(call.error, undefined);

  // Reset the globally tracked mocks.
  mock.reset();
});
```

*CommonJS version:*

```js
const assert = require('node:assert');
const { mock, test } = require('node:test');

test('spies on a function', () => {
  const sum = mock.fn((a, b) => {
    return a + b;
  });

  assert.strictEqual(sum.mock.callCount(), 0);
  assert.strictEqual(sum(3, 4), 7);
  assert.strictEqual(sum.mock.callCount(), 1);

  const call = sum.mock.calls[0];
  assert.deepStrictEqual(call.arguments, [3, 4]);
  assert.strictEqual(call.result, 7);
  assert.strictEqual(call.error, undefined);

  // Reset the globally tracked mocks.
  mock.reset();
});
```

The same mocking functionality is also exposed on the [`TestContext`](53-test-part3.md#class-testcontext) object
of each test. The following example creates a spy on an object method using the
API exposed on the `TestContext`. The benefit of mocking via the test context is
that the test runner will automatically restore all mocked functionality once
the test finishes.

```js
test('spies on an object method', (t) => {
  const number = {
    value: 5,
    add(a) {
      return this.value + a;
    },
  };

  t.mock.method(number, 'add');
  assert.strictEqual(number.add.mock.callCount(), 0);
  assert.strictEqual(number.add(3), 8);
  assert.strictEqual(number.add.mock.callCount(), 1);

  const call = number.add.mock.calls[0];

  assert.deepStrictEqual(call.arguments, [3]);
  assert.strictEqual(call.result, 8);
  assert.strictEqual(call.target, undefined);
  assert.strictEqual(call.this, number);
});
```

### Timers

Mocking timers is a technique commonly used in software testing to simulate and
control the behavior of timers, such as `setInterval` and `setTimeout`,
without actually waiting for the specified time intervals.

Refer to the [`MockTimers`](53-test-part2.md#class-mocktimers) class for a full list of methods and features.

This allows developers to write more reliable and
predictable tests for time-dependent functionality.

The example below shows how to mock `setTimeout`.
Using `.enable({ apis: ['setTimeout'] });`
it will mock the `setTimeout` functions in the [node:timers](54-timers.md) and
[node:timers/promises](54-timers.md#timers-promises-api) modules,
as well as from the Node.js global context.

**Note:** Destructuring functions such as
`import { setTimeout } from 'node:timers'`
is currently not supported by this API.

*ES module version:*

```js
import assert from 'node:assert';
import { mock, test } from 'node:test';

test('mocks setTimeout to be executed synchronously without having to actually wait for it', () => {
  const fn = mock.fn();

  // Optionally choose what to mock
  mock.timers.enable({ apis: ['setTimeout'] });
  setTimeout(fn, 9999);
  assert.strictEqual(fn.mock.callCount(), 0);

  // Advance in time
  mock.timers.tick(9999);
  assert.strictEqual(fn.mock.callCount(), 1);

  // Reset the globally tracked mocks.
  mock.timers.reset();

  // If you call reset mock instance, it will also reset timers instance
  mock.reset();
});
```

*CommonJS version:*

```js
const assert = require('node:assert');
const { mock, test } = require('node:test');

test('mocks setTimeout to be executed synchronously without having to actually wait for it', () => {
  const fn = mock.fn();

  // Optionally choose what to mock
  mock.timers.enable({ apis: ['setTimeout'] });
  setTimeout(fn, 9999);
  assert.strictEqual(fn.mock.callCount(), 0);

  // Advance in time
  mock.timers.tick(9999);
  assert.strictEqual(fn.mock.callCount(), 1);

  // Reset the globally tracked mocks.
  mock.timers.reset();

  // If you call reset mock instance, it will also reset timers instance
  mock.reset();
});
```

The same mocking functionality is also exposed in the mock property on the [`TestContext`](53-test-part3.md#class-testcontext) object
of each test. The benefit of mocking via the test context is
that the test runner will automatically restore all mocked timers
functionality once the test finishes.

*ES module version:*

```js
import assert from 'node:assert';
import { test } from 'node:test';

test('mocks setTimeout to be executed synchronously without having to actually wait for it', (context) => {
  const fn = context.mock.fn();

  // Optionally choose what to mock
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

  // Optionally choose what to mock
  context.mock.timers.enable({ apis: ['setTimeout'] });
  setTimeout(fn, 9999);
  assert.strictEqual(fn.mock.callCount(), 0);

  // Advance in time
  context.mock.timers.tick(9999);
  assert.strictEqual(fn.mock.callCount(), 1);
});
```

### Dates

The mock timers API also allows the mocking of the `Date` object. This is a
useful feature for testing time-dependent functionality, or to simulate
internal calendar functions such as `Date.now()`.

The dates implementation is also part of the [`MockTimers`](53-test-part2.md#class-mocktimers) class. Refer to it
for a full list of methods and features.

**Note:** Dates and timers are dependent when mocked together. This means that
if you have both the `Date` and `setTimeout` mocked, advancing the time will
also advance the mocked date as they simulate a single internal clock.

The example below show how to mock the `Date` object and obtain the current
`Date.now()` value.

*ES module version:*

```js
import assert from 'node:assert';
import { test } from 'node:test';

test('mocks the Date object', (context) => {
  // Optionally choose what to mock
  context.mock.timers.enable({ apis: ['Date'] });
  // If not specified, the initial date will be based on 0 in the UNIX epoch
  assert.strictEqual(Date.now(), 0);

  // Advance in time will also advance the date
  context.mock.timers.tick(9999);
  assert.strictEqual(Date.now(), 9999);
});
```

*CommonJS version:*

```js
const assert = require('node:assert');
const { test } = require('node:test');

test('mocks the Date object', (context) => {
  // Optionally choose what to mock
  context.mock.timers.enable({ apis: ['Date'] });
  // If not specified, the initial date will be based on 0 in the UNIX epoch
  assert.strictEqual(Date.now(), 0);

  // Advance in time will also advance the date
  context.mock.timers.tick(9999);
  assert.strictEqual(Date.now(), 9999);
});
```

If there is no initial epoch set, the initial date will be based on 0 in the
Unix epoch. This is January 1st, 1970, 00:00:00 UTC. You can set an initial date
by passing a `now` property to the `.enable()` method. This value will be used
as the initial date for the mocked `Date` object. It can either be a positive
integer, or another Date object.

*ES module version:*

```js
import assert from 'node:assert';
import { test } from 'node:test';

test('mocks the Date object with initial time', (context) => {
  // Optionally choose what to mock
  context.mock.timers.enable({ apis: ['Date'], now: 100 });
  assert.strictEqual(Date.now(), 100);

  // Advance in time will also advance the date
  context.mock.timers.tick(200);
  assert.strictEqual(Date.now(), 300);
});
```

*CommonJS version:*

```js
const assert = require('node:assert');
const { test } = require('node:test');

test('mocks the Date object with initial time', (context) => {
  // Optionally choose what to mock
  context.mock.timers.enable({ apis: ['Date'], now: 100 });
  assert.strictEqual(Date.now(), 100);

  // Advance in time will also advance the date
  context.mock.timers.tick(200);
  assert.strictEqual(Date.now(), 300);
});
```

You can use the `.setTime()` method to manually move the mocked date to another
time. This method only accepts a positive integer.

**Note:** This method will **not** execute any mocked timers that are in the past
from the new time.

In the below example we are setting a new time for the mocked date.

*ES module version:*

```js
import assert from 'node:assert';
import { test } from 'node:test';

test('sets the time of a date object', (context) => {
  // Optionally choose what to mock
  context.mock.timers.enable({ apis: ['Date'], now: 100 });
  assert.strictEqual(Date.now(), 100);

  // Advance in time will also advance the date
  context.mock.timers.setTime(1000);
  context.mock.timers.tick(200);
  assert.strictEqual(Date.now(), 1200);
});
```

*CommonJS version:*

```js
const assert = require('node:assert');
const { test } = require('node:test');

test('sets the time of a date object', (context) => {
  // Optionally choose what to mock
  context.mock.timers.enable({ apis: ['Date'], now: 100 });
  assert.strictEqual(Date.now(), 100);

  // Advance in time will also advance the date
  context.mock.timers.setTime(1000);
  context.mock.timers.tick(200);
  assert.strictEqual(Date.now(), 1200);
});
```

Timers scheduled in the past will **not** run when you call `setTime()`. To execute those timers, you can use
the `.tick()` method to move forward from the new time.

*ES module version:*

```js
import assert from 'node:assert';
import { test } from 'node:test';

test('setTime does not execute timers', (context) => {
  // Optionally choose what to mock
  context.mock.timers.enable({ apis: ['setTimeout', 'Date'] });
  const fn = context.mock.fn();
  setTimeout(fn, 1000);

  context.mock.timers.setTime(800);
  // Timer is not executed as the time is not yet reached
  assert.strictEqual(fn.mock.callCount(), 0);
  assert.strictEqual(Date.now(), 800);

  context.mock.timers.setTime(1200);
  // Timer is still not executed
  assert.strictEqual(fn.mock.callCount(), 0);
  // Advance in time to execute the timer
  context.mock.timers.tick(0);
  assert.strictEqual(fn.mock.callCount(), 1);
  assert.strictEqual(Date.now(), 1200);
});
```

*CommonJS version:*

```js
const assert = require('node:assert');
const { test } = require('node:test');

test('setTime does not execute timers', (context) => {
  // Optionally choose what to mock
  context.mock.timers.enable({ apis: ['setTimeout', 'Date'] });
  const fn = context.mock.fn();
  setTimeout(fn, 1000);

  context.mock.timers.setTime(800);
  // Timer is not executed as the time is not yet reached
  assert.strictEqual(fn.mock.callCount(), 0);
  assert.strictEqual(Date.now(), 800);

  context.mock.timers.setTime(1200);
  // Timer is still not executed
  assert.strictEqual(fn.mock.callCount(), 0);
  // Advance in time to execute the timer
  context.mock.timers.tick(0);
  assert.strictEqual(fn.mock.callCount(), 1);
  assert.strictEqual(Date.now(), 1200);
});
```

Using `.runAll()` will execute all timers that are currently in the queue. This
will also advance the mocked date to the time of the last timer that was
executed as if the time has passed.

*ES module version:*

```js
import assert from 'node:assert';
import { test } from 'node:test';

test('runs timers as setTime passes ticks', (context) => {
  // Optionally choose what to mock
  context.mock.timers.enable({ apis: ['setTimeout', 'Date'] });
  const fn = context.mock.fn();
  setTimeout(fn, 1000);
  setTimeout(fn, 2000);
  setTimeout(fn, 3000);

  context.mock.timers.runAll();
  // All timers are executed as the time is now reached
  assert.strictEqual(fn.mock.callCount(), 3);
  assert.strictEqual(Date.now(), 3000);
});
```

*CommonJS version:*

```js
const assert = require('node:assert');
const { test } = require('node:test');

test('runs timers as setTime passes ticks', (context) => {
  // Optionally choose what to mock
  context.mock.timers.enable({ apis: ['setTimeout', 'Date'] });
  const fn = context.mock.fn();
  setTimeout(fn, 1000);
  setTimeout(fn, 2000);
  setTimeout(fn, 3000);

  context.mock.timers.runAll();
  // All timers are executed as the time is now reached
  assert.strictEqual(fn.mock.callCount(), 3);
  assert.strictEqual(Date.now(), 3000);
});
```

## Snapshot testing

Added in: v22.3.0

**History**

| Version | Changes |
| --- | --- |
| v23.4.0 | Snapshot testing is no longer experimental. |

Snapshot tests allow arbitrary values to be serialized into string values and
compared against a set of known good values. The known good values are known as
snapshots, and are stored in a snapshot file. Snapshot files are managed by the
test runner, but are designed to be human readable to aid in debugging. Best
practice is for snapshot files to be checked into source control along with your
test files.

Snapshot files are generated by starting Node.js with the
[`--test-update-snapshots`](13-cli.md#--test-update-snapshots) command-line flag. A separate snapshot file is
generated for each test file. By default, the snapshot file has the same name
as the test file with a `.snapshot` file extension. This behavior can be
configured using the `snapshot.setResolveSnapshotPath()` function. Each
snapshot assertion corresponds to an export in the snapshot file.

An example snapshot test is shown below. The first time this test is executed,
it will fail because the corresponding snapshot file does not exist.

```js
// test.js
suite('suite of snapshot tests', () => {
  test('snapshot test', (t) => {
    t.assert.snapshot({ value1: 1, value2: 2 });
    t.assert.snapshot(5);
  });
});
```

Generate the snapshot file by running the test file with
`--test-update-snapshots`. The test should pass, and a file named
`test.js.snapshot` is created in the same directory as the test file. The
contents of the snapshot file are shown below. Each snapshot is identified by
the full name of test and a counter to differentiate between snapshots in the
same test.

```js
exports[`suite of snapshot tests > snapshot test 1`] = `
{
  "value1": 1,
  "value2": 2
}
`;

exports[`suite of snapshot tests > snapshot test 2`] = `
5
`;
```

Once the snapshot file is created, run the tests again without the
`--test-update-snapshots` flag. The tests should pass now.

## Test reporters

Added in: v19.6.0, v18.15.0

**History**

| Version | Changes |
| --- | --- |
| v23.0.0 | The default reporter on non-TTY stdout is changed from `tap` to `spec`, aligning with TTY stdout. |
| v19.9.0, v18.17.0 | Reporters are now exposed at `node:test/reporters`. |

The `node:test` module supports passing [`--test-reporter`](13-cli.md#--test-reporter)
flags for the test runner to use a specific reporter.

The following built-reporters are supported:

- `spec`
  The `spec` reporter outputs the test results in a human-readable format. This
  is the default reporter.
- `tap`
  The `tap` reporter outputs the test results in the [TAP](https://testanything.org/) format.
- `dot`
  The `dot` reporter outputs the test results in a compact format,
  where each passing test is represented by a `.`,
  and each failing test is represented by a `X`.
- `junit`
  The junit reporter outputs test results in a jUnit XML format
- `lcov`
  The `lcov` reporter outputs test coverage when used with the
  [`--experimental-test-coverage`](13-cli.md#--experimental-test-coverage) flag.

The exact output of these reporters is subject to change between versions of
Node.js, and should not be relied on programmatically. If programmatic access
to the test runner's output is required, use the events emitted by the
[`<TestsStream>`](53-test-part2.md#class-testsstream).

The reporters are available via the `node:test/reporters` module:

*ES module version:*

```js
import { tap, spec, dot, junit, lcov } from 'node:test/reporters';
```

*CommonJS version:*

```js
const { tap, spec, dot, junit, lcov } = require('node:test/reporters');
```

### Custom reporters

[`--test-reporter`](13-cli.md#--test-reporter) can be used to specify a path to custom reporter.
A custom reporter is a module that exports a value
accepted by [stream.compose](51-stream-part2.md#streamcomposestreams).
Reporters should transform events emitted by a [`<TestsStream>`](53-test-part2.md#class-testsstream)

Example of a custom reporter using [`<stream.Transform>`](51-stream-part2.md#class-streamtransform):

*ES module version:*

```js
import { Transform } from 'node:stream';

const customReporter = new Transform({
  writableObjectMode: true,
  transform(event, encoding, callback) {
    switch (event.type) {
      case 'test:dequeue':
        callback(null, `test ${event.data.name} dequeued`);
        break;
      case 'test:enqueue':
        callback(null, `test ${event.data.name} enqueued`);
        break;
      case 'test:watch:drained':
        callback(null, 'test watch queue drained');
        break;
      case 'test:watch:restarted':
        callback(null, 'test watch restarted due to file change');
        break;
      case 'test:start':
        callback(null, `test ${event.data.name} started`);
        break;
      case 'test:pass':
        callback(null, `test ${event.data.name} passed`);
        break;
      case 'test:fail':
        callback(null, `test ${event.data.name} failed`);
        break;
      case 'test:plan':
        callback(null, 'test plan');
        break;
      case 'test:diagnostic':
      case 'test:stderr':
      case 'test:stdout':
        callback(null, event.data.message);
        break;
      case 'test:coverage': {
        const { totalLineCount } = event.data.summary.totals;
        callback(null, `total line count: ${totalLineCount}\n`);
        break;
      }
    }
  },
});

export default customReporter;
```

*CommonJS version:*

```js
const { Transform } = require('node:stream');

const customReporter = new Transform({
  writableObjectMode: true,
  transform(event, encoding, callback) {
    switch (event.type) {
      case 'test:dequeue':
        callback(null, `test ${event.data.name} dequeued`);
        break;
      case 'test:enqueue':
        callback(null, `test ${event.data.name} enqueued`);
        break;
      case 'test:watch:drained':
        callback(null, 'test watch queue drained');
        break;
      case 'test:watch:restarted':
        callback(null, 'test watch restarted due to file change');
        break;
      case 'test:start':
        callback(null, `test ${event.data.name} started`);
        break;
      case 'test:pass':
        callback(null, `test ${event.data.name} passed`);
        break;
      case 'test:fail':
        callback(null, `test ${event.data.name} failed`);
        break;
      case 'test:plan':
        callback(null, 'test plan');
        break;
      case 'test:diagnostic':
      case 'test:stderr':
      case 'test:stdout':
        callback(null, event.data.message);
        break;
      case 'test:coverage': {
        const { totalLineCount } = event.data.summary.totals;
        callback(null, `total line count: ${totalLineCount}\n`);
        break;
      }
    }
  },
});

module.exports = customReporter;
```

Example of a custom reporter using a generator function:

*ES module version:*

```js
export default async function * customReporter(source) {
  for await (const event of source) {
    switch (event.type) {
      case 'test:dequeue':
        yield `test ${event.data.name} dequeued\n`;
        break;
      case 'test:enqueue':
        yield `test ${event.data.name} enqueued\n`;
        break;
      case 'test:watch:drained':
        yield 'test watch queue drained\n';
        break;
      case 'test:watch:restarted':
        yield 'test watch restarted due to file change\n';
        break;
      case 'test:start':
        yield `test ${event.data.name} started\n`;
        break;
      case 'test:pass':
        yield `test ${event.data.name} passed\n`;
        break;
      case 'test:fail':
        yield `test ${event.data.name} failed\n`;
        break;
      case 'test:plan':
        yield 'test plan\n';
        break;
      case 'test:diagnostic':
      case 'test:stderr':
      case 'test:stdout':
        yield `${event.data.message}\n`;
        break;
      case 'test:coverage': {
        const { totalLineCount } = event.data.summary.totals;
        yield `total line count: ${totalLineCount}\n`;
        break;
      }
    }
  }
}
```

*CommonJS version:*

```js
module.exports = async function * customReporter(source) {
  for await (const event of source) {
    switch (event.type) {
      case 'test:dequeue':
        yield `test ${event.data.name} dequeued\n`;
        break;
      case 'test:enqueue':
        yield `test ${event.data.name} enqueued\n`;
        break;
      case 'test:watch:drained':
        yield 'test watch queue drained\n';
        break;
      case 'test:watch:restarted':
        yield 'test watch restarted due to file change\n';
        break;
      case 'test:start':
        yield `test ${event.data.name} started\n`;
        break;
      case 'test:pass':
        yield `test ${event.data.name} passed\n`;
        break;
      case 'test:fail':
        yield `test ${event.data.name} failed\n`;
        break;
      case 'test:plan':
        yield 'test plan\n';
        break;
      case 'test:diagnostic':
      case 'test:stderr':
      case 'test:stdout':
        yield `${event.data.message}\n`;
        break;
      case 'test:coverage': {
        const { totalLineCount } = event.data.summary.totals;
        yield `total line count: ${totalLineCount}\n`;
        break;
      }
    }
  }
};
```

The value provided to `--test-reporter` should be a string like one used in an
`import()` in JavaScript code, or a value provided for [`--import`](13-cli.md#--importmodule).

### Multiple reporters

The [`--test-reporter`](13-cli.md#--test-reporter) flag can be specified multiple times to report test
results in several formats. In this situation
it is required to specify a destination for each reporter
using [`--test-reporter-destination`](13-cli.md#--test-reporter-destination).
Destination can be `stdout`, `stderr`, or a file path.
Reporters and destinations are paired according
to the order they were specified.

In the following example, the `spec` reporter will output to `stdout`,
and the `dot` reporter will output to `file.txt`:

```bash
node --test-reporter=spec --test-reporter=dot --test-reporter-destination=stdout --test-reporter-destination=file.txt
```

When a single reporter is specified, the destination will default to `stdout`,
unless a destination is explicitly provided.

## `run([options])`

Added in: v18.9.0, v16.19.0

**History**

| Version | Changes |
| --- | --- |
| v26.2.0 | Added the `testTagFilters` option. |
| v25.6.0, v24.14.0 | Add the `env` option. |
| v24.7.0 | Added a rerunFailuresFilePath option. |
| v23.0.0 | Added the `cwd` option. |
| v23.0.0, v22.10.0 | Added coverage options. |
| v22.8.0 | Added the `isolation` option. |
| v22.6.0 | Added the `globPatterns` option. |
| v22.0.0, v20.14.0 | Added the `forceExit` option. |
| v20.1.0, v18.17.0 | Add a testNamePatterns option. |

- `options` [`<Object>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Object) Configuration options for running tests. The following
  properties are supported:
  - `concurrency` [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) | [`<boolean>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#boolean_type) If a number is provided,
    then that many test processes would run in parallel, where each process
    corresponds to one test file.
    If `true`, it would run `os.availableParallelism() - 1` test files in
    parallel.
    If `false`, it would only run one test file at a time.
    **Default:** `false`.
  - `cwd` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) Specifies the current working directory to be used by the test runner.
    Serves as the base path for resolving files as if [running tests from the command line](#running-tests-from-the-command-line) from that directory.
    **Default:** `process.cwd()`.
  - `files` [`<Array>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Array) An array containing the list of files to run. **Default:** Same as [running tests from the command line](#running-tests-from-the-command-line).
  - `forceExit` [`<boolean>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#boolean_type) Configures the test runner to exit the process once
    all known tests have finished executing even if the event loop would
    otherwise remain active. **Default:** `false`.
  - `globPatterns` [`<Array>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Array) An array containing the list of glob patterns to
    match test files. This option cannot be used together with `files`.
    **Default:** Same as [running tests from the command line](#running-tests-from-the-command-line).
  - `inspectPort` [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) | [`<Function>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Function) Sets inspector port of test child process.
    This can be a number, or a function that takes no arguments and returns a
    number. If a nullish value is provided, each process gets its own port,
    incremented from the primary's `process.debugPort`. This option is ignored
    if the `isolation` option is set to `'none'` as no child processes are
    spawned. **Default:** `undefined`.
  - `isolation` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) Configures the type of test isolation. If set to `'process'`, each test file is run in a separate child process. If set to
    `'none'`, all test files run in the current process. **Default:**
    `'process'`.
  - `only` [`<boolean>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#boolean_type) If truthy, the test context will only run tests that
    have the `only` option set
  - `setup` [`<Function>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Function) A function that accepts the `TestsStream` instance
    and can be used to setup listeners before any tests are run.
    **Default:** `undefined`.
  - `execArgv` [`<Array>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Array) An array of CLI flags to pass to the `node` executable when
    spawning the subprocesses. This option has no effect when `isolation` is `'none`'.
    **Default:** `[]`
  - `argv` [`<Array>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Array) An array of CLI flags to pass to each test file when spawning the
    subprocesses. This option has no effect when `isolation` is `'none'`.
    **Default:** `[]`.
  - `signal` [`<AbortSignal>`](26-globals.md#class-abortsignal) Allows aborting an in-progress test execution.
  - `testNamePatterns` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) | [`<RegExp>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/RegExp) | [`<Array>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Array) A String, RegExp or a RegExp Array,
    that can be used to only run tests whose name matches the provided pattern.
    Test name patterns are interpreted as JavaScript regular expressions.
    For each test that is executed, any corresponding test hooks, such as `beforeEach()`, are also run.
    **Default:** `undefined`.
  - `testSkipPatterns` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) | [`<RegExp>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/RegExp) | [`<Array>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Array) A String, RegExp or a RegExp Array,
    that can be used to exclude running tests whose name matches the provided pattern.
    Test name patterns are interpreted as JavaScript regular expressions.
    For each test that is executed, any corresponding test hooks, such as `beforeEach()`, are also run.
    **Default:** `undefined`.
  - `testTagFilters` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) | [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type)[] A boolean expression, or an array of
    boolean expressions, used to filter tests by their declared tags.
    Multiple expressions compose by AND. Equivalent to passing [`--experimental-test-tag-filter`](13-cli.md#--experimental-test-tag-filtertag) on the command line. See
    [Test tags](#test-tags). **Default:** `undefined`.
  - `timeout` [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) A number of milliseconds the test execution will
    fail after.
    If unspecified, subtests inherit this value from their parent. **Default:** `Infinity`.
  - `watch` [`<boolean>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#boolean_type) Whether to run in watch mode or not. **Default:** `false`.
  - `shard` [`<Object>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Object) Running tests in a specific shard. **Default:** `undefined`.
    - `index` [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) is a positive integer between 1 and `<total>`
      that specifies the index of the shard to run. This option is *required*.
    - `total` [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) is a positive integer that specifies the total number
      of shards to split the test files to. This option is *required*.
  - `randomize` [`<boolean>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#boolean_type) Randomize execution order for test files and queued tests.
    This option is not supported with `watch: true`.
    **Default:** `false`.
  - `randomSeed` [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) Seed used when randomizing execution order. If this
    option is set, runs can replay the same randomized order deterministically,
    and setting this option also enables randomization. The value must be an
    integer between `0` and `4294967295`.
    **Default:** `undefined`.
  - `rerunFailuresFilePath` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) A file path where the test runner will
    store the state of the tests to allow rerunning only the failed tests on a next run.
    see [Rerunning failed tests][] for more information. **Default:** `undefined`.
  - `coverage` [`<boolean>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#boolean_type) enable [code coverage](#collecting-code-coverage) collection.
    **Default:** `false`.
  - `coverageExcludeGlobs` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) | [`<Array>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Array) Excludes specific files from code coverage
    using a glob pattern, which can match both absolute and relative file paths.
    This property is only applicable when `coverage` was set to `true`.
    If both `coverageExcludeGlobs` and `coverageIncludeGlobs` are provided,
    files must meet **both** criteria to be included in the coverage report.
    **Default:** `undefined`.
  - `coverageIncludeGlobs` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) | [`<Array>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Array) Includes specific files in code coverage
    using a glob pattern, which can match both absolute and relative file paths.
    This property is only applicable when `coverage` was set to `true`.
    If both `coverageExcludeGlobs` and `coverageIncludeGlobs` are provided,
    files must meet **both** criteria to be included in the coverage report.
    **Default:** `undefined`.
  - `coverageIncludeAll` [`<boolean>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#boolean_type) Includes source files that were never loaded by
    the test run in the coverage report, where they are reported as having zero
    coverage. Candidate files are searched for in `cwd`, and are subject to the
    same `coverageIncludeGlobs` and `coverageExcludeGlobs` filtering as the rest
    of the report. This property is only applicable when `coverage` was set to
    `true`.
    **Default:** `false`.
  - `lineCoverage` [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) Require a minimum percent of covered lines. If code
    coverage does not reach the threshold specified, the process will exit with code `1`.
    **Default:** `0`.
  - `branchCoverage` [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) Require a minimum percent of covered branches. If code
    coverage does not reach the threshold specified, the process will exit with code `1`.
    **Default:** `0`.
  - `functionCoverage` [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) Require a minimum percent of covered functions. If code
    coverage does not reach the threshold specified, the process will exit with code `1`.
    **Default:** `0`.
  - `env` [`<Object>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Object) Specify environment variables to be passed along to the test process.
    This option is not compatible with `isolation='none'`. These variables will override
    those from the main process, and are not merged with `process.env`.
    **Default:** `process.env`.
- Returns: [`<TestsStream>`](53-test-part2.md#class-testsstream)

**Note:** `shard` is used to horizontally parallelize test running across
machines or processes, ideal for large-scale executions across varied
environments. It's incompatible with `watch` mode, tailored for rapid
code iteration by automatically rerunning tests on file changes.

*ES module version:*

```js
import { tap } from 'node:test/reporters';
import { run } from 'node:test';
import process from 'node:process';
import path from 'node:path';

run({ files: [path.resolve('./tests/test.js')] })
 .on('test:fail', () => {
   process.exitCode = 1;
 })
 .compose(tap)
 .pipe(process.stdout);
```

*CommonJS version:*

```js
const { tap } = require('node:test/reporters');
const { run } = require('node:test');
const path = require('node:path');

run({ files: [path.resolve('./tests/test.js')] })
 .on('test:fail', () => {
   process.exitCode = 1;
 })
 .compose(tap)
 .pipe(process.stdout);
```

[Contents](../README.md) | [Previous: String decoder](52-string-decoder.md) | [Next: Test runner, part 2 of 4](53-test-part2.md)
