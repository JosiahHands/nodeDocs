[Contents](../README.md) | [Previous: Test runner, part 2 of 4](53-test-part2.md) | [Next: Test runner, part 4 of 4](53-test-part4.md)

Source: [https://nodejs.org/docs/latest/api/test.html](https://nodejs.org/docs/latest/api/test.html)

Part 3 of 4 of this section.

# Test runner (part 3 of 4)

Continues: Class: TestsStream.

### Event lifecycle

The tables above group the events; the diagram below places them on a
timeline. The declaration ordered events form the main spine, buffered so that
a reporter sees them in source order, while each execution ordered twin is
emitted immediately, when the work actually happens. In particular,
[`'test:start'`](#event-teststart) marks when a test begins *reporting* its own and its
subtests' status, not when its body begins executing; that moment is
[`'test:dequeue'`](#event-testdequeue).

```text
                     node:test reporter event lifecycle
   main spine = DECLARATION order (buffered; matches source order)
   right side = EXECUTION order (emitted immediately); ◄ marks each twin

  LEAF TEST
  ─────────
   ┌──────────────┐                   test:enqueue
   │ test:start   │ ◄──── twins ────  (queued for execution;
   └──────────────┘                    type: 'suite' | 'test')
        │  begins REPORTING           test:dequeue
        │  (not the start of          (about to run; emitted right
        │   the test body)             before the test body runs)
        │
        │     [ between the twins, on the execution timeline, the test
        │       body runs: context.log() emits test:log live, and
        │       test:stdout / test:stderr stream with --test ]
        │
        ▼
   ┌───────────────────────┐
   │ test:pass │ test:fail │ ◄──── twin ────  test:complete
   └───────────────────────┘   result         (details.passed says which)
        │
        ▼
   test:diagnostic    the test's own context.diagnostic() messages,
                      buffered while it runs, flushed after its result

  SUITE / PARENT TEST   (each subtest is the whole LEAF flow above)
  ───────────────────
   test:start ─► [ full flow of each subtest ... ] ─►
        test:plan (count = subtests) ─► test:pass │ test:fail ─►
        test:diagnostic

  RUN-LEVEL FINALE   (root, after all top-level tests)
  ────────────────
   test:plan         top-level count
        │
        ▼
   test:diagnostic   x N   tests, suites, pass, fail, cancelled,
        │                  skipped, todo, duration_ms (+ coverage errors)
        ▼
   test:coverage     only if coverage is enabled
        │
        ▼
   test:summary  ─►  stream ends

  INTERRUPTION   (SIGINT, e.g. Ctrl+C, while tests are still running)
  ────────────
   test:interrupted   the innermost tests still running at that moment
        │             (not emitted if none were running)
        ▼
   the run exits immediately — the buffered spine never flushes, so
   neither the finale above nor those tests' own results are emitted
```

### Event: `'test:coverage'`

- `data` [`<Object>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Object)
  - `summary` [`<Object>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Object) An object containing the coverage report.
    - `files` [`<Array>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Array) An array of coverage reports for individual files. Each
      report is an object with the following schema:
      - `path` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) The absolute path of the file.
      - `totalLineCount` [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) The total number of lines.
      - `totalBranchCount` [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) The total number of branches.
      - `totalFunctionCount` [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) The total number of functions.
      - `coveredLineCount` [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) The number of covered lines.
      - `coveredBranchCount` [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) The number of covered branches.
      - `coveredFunctionCount` [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) The number of covered functions.
      - `coveredLinePercent` [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) The percentage of lines covered.
      - `coveredBranchPercent` [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) The percentage of branches covered.
      - `coveredFunctionPercent` [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) The percentage of functions covered.
      - `functions` [`<Array>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Array) An array of functions representing function
        coverage.
        - `name` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) The name of the function.
        - `line` [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) The line number where the function is defined.
        - `count` [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) The number of times the function was called.
      - `branches` [`<Array>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Array) An array of branches representing branch coverage.
        - `line` [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) The line number where the branch is defined.
        - `count` [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) The number of times the branch was taken.
      - `lines` [`<Array>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Array) An array of lines representing line
        numbers and the number of times they were covered.
        - `line` [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) The line number.
        - `count` [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) The number of times the line was covered.
    - `thresholds` [`<Object>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Object) An object containing whether or not the coverage for
      each coverage type.
      - `function` [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) The function coverage threshold.
      - `branch` [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) The branch coverage threshold.
      - `line` [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) The line coverage threshold.
    - `totals` [`<Object>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Object) An object containing a summary of coverage for all
      files.
      - `totalLineCount` [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) The total number of lines.
      - `totalBranchCount` [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) The total number of branches.
      - `totalFunctionCount` [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) The total number of functions.
      - `coveredLineCount` [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) The number of covered lines.
      - `coveredBranchCount` [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) The number of covered branches.
      - `coveredFunctionCount` [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) The number of covered functions.
      - `coveredLinePercent` [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) The percentage of lines covered.
      - `coveredBranchPercent` [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) The percentage of branches covered.
      - `coveredFunctionPercent` [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) The percentage of functions covered.
    - `workingDirectory` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) The working directory when code coverage
      began. This is useful for displaying relative path names in case the tests
      changed the working directory of the Node.js process.
  - `nesting` [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) The nesting level of the test.

Emitted when code coverage is enabled and all tests have completed.

### Event: `'test:complete'`

- `data` [`<Object>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Object)
  - `column` [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) | [`<undefined>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#undefined_type) The column number where the test is defined, or `undefined` if the test was run through the REPL.
  - `details` [`<Object>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Object) Additional execution metadata.
    - `passed` [`<boolean>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#boolean_type) Whether the test passed or not.
    - `duration_ms` [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) The duration of the test in milliseconds.
    - `error` [`<Error>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Error) | [`<undefined>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#undefined_type) An error wrapping the error thrown by the test
      if it did not pass.
      - `cause` [`<Error>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Error) The actual error thrown by the test.
    - `type` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) | [`<undefined>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#undefined_type) The type of the test, used to denote whether
      this is a suite.
  - `entryFile` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) | [`<undefined>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#undefined_type) The path of the test file that was
    executed as the entry point of the child process that emitted this event.
    Only present when tests run with process isolation. May differ from `file` when the test is defined in a module imported by the entry file.
  - `file` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) | [`<undefined>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#undefined_type) The path of the test file, `undefined` if test was run through the REPL.
  - `line` [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) | [`<undefined>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#undefined_type) The line number where the test is defined, or `undefined` if the test was run through the REPL.
  - `name` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) The test name.
  - `nesting` [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) The nesting level of the test.
  - `parentId` [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) | [`<undefined>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#undefined_type) The `testId` of the enclosing test, or
    `undefined` for top-level tests. Lets custom reporters track lineage
    when concurrent siblings at the same nesting level interleave.
  - `tags` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type)[] The flattened lowercased tags declared on the test
    and its ancestor suites, in declaration order. Empty for untagged tests.
    See [Test tags](53-test-part1.md#test-tags).
  - `testId` [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) A numeric identifier for this test instance, unique
    within the test file's process. Consistent across all events for the same
    test instance, enabling reliable correlation in custom reporters.
  - `testNumber` [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) The ordinal number of the test.
  - `todo` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) | [`<boolean>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#boolean_type) | [`<undefined>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#undefined_type) Present if [`context.todo`](53-test-part4.md#contexttodomessage) is called
  - `skip` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) | [`<boolean>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#boolean_type) | [`<undefined>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#undefined_type) Present if [`context.skip`](53-test-part4.md#contextskipmessage) is called

Emitted when a test completes its execution.
This event is not emitted in the same order as the tests are
defined.
The corresponding declaration ordered events are `'test:pass'` and `'test:fail'`.

### Event: `'test:dequeue'`

- `data` [`<Object>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Object)
  - `column` [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) | [`<undefined>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#undefined_type) The column number where the test is defined, or `undefined` if the test was run through the REPL.
  - `entryFile` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) | [`<undefined>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#undefined_type) The path of the test file that was
    executed as the entry point of the child process that emitted this event.
    Only present when tests run with process isolation. May differ from `file` when the test is defined in a module imported by the entry file.
  - `file` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) | [`<undefined>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#undefined_type) The path of the test file, `undefined` if test was run through the REPL.
  - `line` [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) | [`<undefined>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#undefined_type) The line number where the test is defined, or `undefined` if the test was run through the REPL.
  - `name` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) The test name.
  - `nesting` [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) The nesting level of the test.
  - `parentId` [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) | [`<undefined>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#undefined_type) The `testId` of the enclosing test, or
    `undefined` for top-level tests. Lets custom reporters track lineage
    when concurrent siblings at the same nesting level interleave.
  - `tags` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type)[] The flattened lowercased tags declared on the test
    and its ancestor suites, in declaration order. Empty for untagged tests.
    See [Test tags](53-test-part1.md#test-tags).
  - `testId` [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) A numeric identifier for this test instance, unique
    within the test file's process. Consistent across all events for the same
    test instance, enabling reliable correlation in custom reporters.
  - `type` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) The test type. Either `'suite'` or `'test'`.

Emitted when a test is dequeued, right before it is executed.
This event is not guaranteed to be emitted in the same order as the tests are
defined. The corresponding declaration ordered event is `'test:start'`.

### Event: `'test:diagnostic'`

- `data` [`<Object>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Object)
  - `column` [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) | [`<undefined>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#undefined_type) The column number where the test is defined, or `undefined` if the test was run through the REPL.
  - `entryFile` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) | [`<undefined>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#undefined_type) The path of the test file that was
    executed as the entry point of the child process that emitted this event.
    Only present when tests run with process isolation. May differ from `file` when the test is defined in a module imported by the entry file.
  - `file` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) | [`<undefined>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#undefined_type) The path of the test file, `undefined` if test was run through the REPL.
  - `line` [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) | [`<undefined>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#undefined_type) The line number where the test is defined, or `undefined` if the test was run through the REPL.
  - `message` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) The diagnostic message.
  - `nesting` [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) The nesting level of the test.
  - `level` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) The severity level of the diagnostic message.
    Possible values are:
    - `'info'`: Informational messages.
    - `'warn'`: Warnings.
    - `'error'`: Errors.

Emitted when [`context.diagnostic`](53-test-part4.md#contextdiagnosticmessage) is called.
This event is guaranteed to be emitted in the same order as the tests are
defined.

### Event: `'test:enqueue'`

- `data` [`<Object>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Object)
  - `column` [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) | [`<undefined>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#undefined_type) The column number where the test is defined, or `undefined` if the test was run through the REPL.
  - `entryFile` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) | [`<undefined>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#undefined_type) The path of the test file that was
    executed as the entry point of the child process that emitted this event.
    Only present when tests run with process isolation. May differ from `file` when the test is defined in a module imported by the entry file.
  - `file` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) | [`<undefined>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#undefined_type) The path of the test file, `undefined` if test was run through the REPL.
  - `line` [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) | [`<undefined>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#undefined_type) The line number where the test is defined, or `undefined` if the test was run through the REPL.
  - `name` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) The test name.
  - `nesting` [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) The nesting level of the test.
  - `parentId` [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) | [`<undefined>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#undefined_type) The `testId` of the enclosing test, or
    `undefined` for top-level tests. Lets custom reporters track lineage
    when concurrent siblings at the same nesting level interleave.
  - `tags` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type)[] The flattened lowercased tags declared on the test
    and its ancestor suites, in declaration order. Empty for untagged tests.
    See [Test tags](53-test-part1.md#test-tags).
  - `testId` [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) A numeric identifier for this test instance, unique
    within the test file's process. Consistent across all events for the same
    test instance, enabling reliable correlation in custom reporters.
  - `type` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) The test type. Either `'suite'` or `'test'`.

Emitted when a test is enqueued for execution.

### Event: `'test:fail'`

- `data` [`<Object>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Object)
  - `column` [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) | [`<undefined>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#undefined_type) The column number where the test is defined, or `undefined` if the test was run through the REPL.
  - `details` [`<Object>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Object) Additional execution metadata.
    - `duration_ms` [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) The duration of the test in milliseconds.
    - `error` [`<Error>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Error) An error wrapping the error thrown by the test.
      - `cause` [`<Error>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Error) The actual error thrown by the test.
    - `type` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) | [`<undefined>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#undefined_type) The type of the test, used to denote whether
      this is a suite.
    - `attempt` [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) | [`<undefined>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#undefined_type) The attempt number of the test run,
      present only when using the [`--test-rerun-failures`](13-cli.md#--test-rerun-failures) flag.
  - `entryFile` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) | [`<undefined>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#undefined_type) The path of the test file that was
    executed as the entry point of the child process that emitted this event.
    Only present when tests run with process isolation. May differ from `file` when the test is defined in a module imported by the entry file.
  - `file` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) | [`<undefined>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#undefined_type) The path of the test file, `undefined` if test was run through the REPL.
  - `line` [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) | [`<undefined>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#undefined_type) The line number where the test is defined, or `undefined` if the test was run through the REPL.
  - `name` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) The test name.
  - `nesting` [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) The nesting level of the test.
  - `parentId` [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) | [`<undefined>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#undefined_type) The `testId` of the enclosing test, or
    `undefined` for top-level tests. Lets custom reporters track lineage
    when concurrent siblings at the same nesting level interleave.
  - `tags` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type)[] The flattened lowercased tags declared on the test
    and its ancestor suites, in declaration order. Empty for untagged tests.
    See [Test tags](53-test-part1.md#test-tags).
  - `testId` [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) A numeric identifier for this test instance, unique
    within the test file's process. Consistent across all events for the same
    test instance, enabling reliable correlation in custom reporters.
  - `testNumber` [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) The ordinal number of the test.
  - `todo` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) | [`<boolean>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#boolean_type) | [`<undefined>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#undefined_type) Present if [`context.todo`](53-test-part4.md#contexttodomessage) is called
  - `skip` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) | [`<boolean>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#boolean_type) | [`<undefined>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#undefined_type) Present if [`context.skip`](53-test-part4.md#contextskipmessage) is called

Emitted when a test fails.
This event is guaranteed to be emitted in the same order as the tests are
defined.
The corresponding execution ordered event is `'test:complete'`.

### Event: `'test:interrupted'`

Added in: v25.7.0

- `data` [`<Object>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Object)
  - `tests` [`<Array>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Array) An array of objects containing information about the
    interrupted tests.
    - `column` [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) | [`<undefined>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#undefined_type) The column number where the test is defined,
      or `undefined` if the test was run through the REPL.
    - `file` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) | [`<undefined>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#undefined_type) The path of the test file, `undefined` if test was run through the REPL.
    - `line` [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) | [`<undefined>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#undefined_type) The line number where the test is defined, or `undefined` if the test was run through the REPL.
    - `name` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) The test name.
    - `nesting` [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) The nesting level of the test.

Emitted when the test runner is interrupted by a `SIGINT` signal (e.g., when
pressing `Ctrl`+`C`). The event contains information about
the tests that were running at the time of interruption.

When using process isolation (the default), the test name will be the file path
since the parent runner only knows about file-level tests. When using
`--test-isolation=none`, the actual test name is shown.

### Event: `'test:log'`

Added in: v26.6.0

- `data` [`<Object>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Object)
  - `column` [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) | [`<undefined>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#undefined_type) The column number where the test is defined, or `undefined` if the test was run through the REPL.
  - `data` [`<any>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#Data_types) The structured payload passed to [`context.log`](53-test-part4.md#contextlogmessage-data), or
    `undefined` if none was provided. The test runner does not interpret this
    value.
  - `entryFile` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) | [`<undefined>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#undefined_type) The path of the test file that was
    executed as the entry point of the child process that emitted this event.
    Only present when tests run with process isolation. May differ from `file` when the test is defined in a module imported by the entry file.
  - `file` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) | [`<undefined>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#undefined_type) The path of the test file, `undefined` if test was run through the REPL.
  - `line` [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) | [`<undefined>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#undefined_type) The line number where the test is defined, or `undefined` if the test was run through the REPL.
  - `message` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) The log message.
  - `name` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) The test name.
  - `nesting` [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) The nesting level of the test.
  - `parentId` [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) | [`<undefined>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#undefined_type) The `testId` of the enclosing test, or
    `undefined` for top-level tests.
  - `testId` [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) A numeric identifier for the test instance that emitted
    the log message.

Emitted when [`context.log`](53-test-part4.md#contextlogmessage-data) is called. Unlike [`'test:diagnostic'`](#event-testdiagnostic),
this event is emitted immediately, in the order that the tests execute,
making it suitable for reporters that render test output unbuffered.

### Event: `'test:pass'`

- `data` [`<Object>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Object)
  - `column` [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) | [`<undefined>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#undefined_type) The column number where the test is defined, or `undefined` if the test was run through the REPL.
  - `details` [`<Object>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Object) Additional execution metadata.
    - `duration_ms` [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) The duration of the test in milliseconds.
    - `type` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) | [`<undefined>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#undefined_type) The type of the test, used to denote whether
      this is a suite.
    - `attempt` [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) | [`<undefined>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#undefined_type) The attempt number of the test run,
      present only when using the [`--test-rerun-failures`](13-cli.md#--test-rerun-failures) flag.
    - `passed_on_attempt` [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) | [`<undefined>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#undefined_type) The attempt number the test passed on,
      present only when using the [`--test-rerun-failures`](13-cli.md#--test-rerun-failures) flag.
  - `entryFile` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) | [`<undefined>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#undefined_type) The path of the test file that was
    executed as the entry point of the child process that emitted this event.
    Only present when tests run with process isolation. May differ from `file` when the test is defined in a module imported by the entry file.
  - `file` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) | [`<undefined>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#undefined_type) The path of the test file, `undefined` if test was run through the REPL.
  - `line` [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) | [`<undefined>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#undefined_type) The line number where the test is defined, or `undefined` if the test was run through the REPL.
  - `name` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) The test name.
  - `nesting` [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) The nesting level of the test.
  - `parentId` [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) | [`<undefined>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#undefined_type) The `testId` of the enclosing test, or
    `undefined` for top-level tests. Lets custom reporters track lineage
    when concurrent siblings at the same nesting level interleave.
  - `tags` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type)[] The flattened lowercased tags declared on the test
    and its ancestor suites, in declaration order. Empty for untagged tests.
    See [Test tags](53-test-part1.md#test-tags).
  - `testId` [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) A numeric identifier for this test instance, unique
    within the test file's process. Consistent across all events for the same
    test instance, enabling reliable correlation in custom reporters.
  - `testNumber` [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) The ordinal number of the test.
  - `todo` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) | [`<boolean>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#boolean_type) | [`<undefined>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#undefined_type) Present if [`context.todo`](53-test-part4.md#contexttodomessage) is called
  - `skip` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) | [`<boolean>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#boolean_type) | [`<undefined>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#undefined_type) Present if [`context.skip`](53-test-part4.md#contextskipmessage) is called

Emitted when a test passes.
This event is guaranteed to be emitted in the same order as the tests are
defined.
The corresponding execution ordered event is `'test:complete'`.

### Event: `'test:plan'`

- `data` [`<Object>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Object)
  - `column` [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) | [`<undefined>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#undefined_type) The column number where the test is defined, or `undefined` if the test was run through the REPL.
  - `entryFile` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) | [`<undefined>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#undefined_type) The path of the test file that was
    executed as the entry point of the child process that emitted this event.
    Only present when tests run with process isolation. May differ from `file` when the test is defined in a module imported by the entry file.
  - `file` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) | [`<undefined>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#undefined_type) The path of the test file, `undefined` if test was run through the REPL.
  - `line` [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) | [`<undefined>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#undefined_type) The line number where the test is defined, or `undefined` if the test was run through the REPL.
  - `nesting` [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) The nesting level of the test.
  - `count` [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) The number of subtests that have ran.

Emitted when all subtests have completed for a given test.
This event is guaranteed to be emitted in the same order as the tests are
defined.

### Event: `'test:start'`

- `data` [`<Object>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Object)
  - `column` [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) | [`<undefined>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#undefined_type) The column number where the test is defined, or `undefined` if the test was run through the REPL.
  - `entryFile` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) | [`<undefined>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#undefined_type) The path of the test file that was
    executed as the entry point of the child process that emitted this event.
    Only present when tests run with process isolation. May differ from `file` when the test is defined in a module imported by the entry file.
  - `file` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) | [`<undefined>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#undefined_type) The path of the test file, `undefined` if test was run through the REPL.
  - `line` [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) | [`<undefined>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#undefined_type) The line number where the test is defined, or `undefined` if the test was run through the REPL.
  - `name` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) The test name.
  - `nesting` [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) The nesting level of the test.
  - `parentId` [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) | [`<undefined>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#undefined_type) The `testId` of the enclosing test, or
    `undefined` for top-level tests. Lets custom reporters track lineage
    when concurrent siblings at the same nesting level interleave.
  - `tags` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type)[] The flattened lowercased tags declared on the test
    and its ancestor suites, in declaration order. Empty for untagged tests.
    See [Test tags](53-test-part1.md#test-tags).
  - `testId` [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) A numeric identifier for this test instance, unique
    within the test file's process. Consistent across all events for the same
    test instance, enabling reliable correlation in custom reporters.

Emitted when a test starts reporting its own and its subtests status.
This event is guaranteed to be emitted in the same order as the tests are
defined.
The corresponding execution ordered event is `'test:dequeue'`.

### Event: `'test:stderr'`

- `data` [`<Object>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Object)
  - `entryFile` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) | [`<undefined>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#undefined_type) The path of the test file that was
    executed as the entry point of the child process that emitted this event.
    Only present when tests run with process isolation.
  - `file` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) The path of the test file.
  - `message` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) The message written to `stderr`.

Emitted when a running test writes to `stderr`.
This event is only emitted if `--test` flag is passed.
This event is not guaranteed to be emitted in the same order as the tests are
defined.

### Event: `'test:stdout'`

- `data` [`<Object>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Object)
  - `entryFile` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) | [`<undefined>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#undefined_type) The path of the test file that was
    executed as the entry point of the child process that emitted this event.
    Only present when tests run with process isolation.
  - `file` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) The path of the test file.
  - `message` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) The message written to `stdout`.

Emitted when a running test writes to `stdout`.
This event is only emitted if `--test` flag is passed.
This event is not guaranteed to be emitted in the same order as the tests are
defined.

### Event: `'test:summary'`

- `data` [`<Object>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Object)
  - `counts` [`<Object>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Object) An object containing the counts of various test results.
    - `cancelled` [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) The total number of cancelled tests.
    - `failed` [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) The total number of failed tests.
    - `passed` [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) The total number of passed tests.
    - `skipped` [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) The total number of skipped tests.
    - `suites` [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) The total number of suites run.
    - `tests` [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) The total number of tests run, excluding suites.
    - `todo` [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) The total number of TODO tests.
    - `topLevel` [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) The total number of top level tests and suites.
  - `duration_ms` [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) The duration of the test run in milliseconds.
  - `file` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) | [`<undefined>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#undefined_type) The path of the test file that generated the
    summary. If the summary corresponds to multiple files, this value is `undefined`.
  - `success` [`<boolean>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#boolean_type) Indicates whether or not the test run is considered
    successful or not. If any error condition occurs, such as a failing test or
    unmet coverage threshold, this value will be set to `false`.

Emitted when a test run completes. This event contains metrics pertaining to
the completed test run, and is useful for determining if a test run passed or
failed. If process-level test isolation is used, a `'test:summary'` event is
generated for each test file in addition to a final cumulative summary.

### Event: `'test:watch:drained'`

Emitted when no more tests are queued for execution in watch mode.

### Event: `'test:watch:restarted'`

Emitted when one or more tests are restarted due to a file change in watch mode.

## `getTestContext()`

Added in: v26.1.0

- Returns: [`<TestContext>`](53-test-part3.md#class-testcontext) | [`<SuiteContext>`](53-test-part4.md#class-suitecontext) | [`<undefined>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#undefined_type)

Returns the [`TestContext`](#class-testcontext) or [`SuiteContext`](53-test-part4.md#class-suitecontext) object associated with the
currently executing test or suite, or `undefined` if called outside of a test or
suite. This function can be used to access context information from within the
test or suite function or any async operations within them.

```js
import { getTestContext } from 'node:test';

test('example test', async () => {
  const ctx = getTestContext();
  console.log(`Running test: ${ctx.name}`);
});

describe('example suite', () => {
  const ctx = getTestContext();
  console.log(`Running suite: ${ctx.name}`);
});
```

When called from a test, returns a [`TestContext`](#class-testcontext).
When called from a suite, returns a [`SuiteContext`](53-test-part4.md#class-suitecontext).

If called from outside a test or suite (e.g., at the top level of a module or in
a setTimeout callback after execution has completed), this function returns
`undefined`.

When called from within a hook (before, beforeEach, after, afterEach), this
function returns the context of the test or suite that the hook is associated
with.

## Test instrumentation and OpenTelemetry

Added in: v26.1.0

The test runner publishes test execution events through the Node.js
[`diagnostics_channel`](18-diagnostics-channel.md) module, enabling integration with observability tools
like OpenTelemetry without requiring changes to the test runner itself.

### Tracing events

The test runner publishes events to the `'node.test'` tracing channel. Subscribers
can use the [`TracingChannel`](18-diagnostics-channel.md#class-tracingchannel) API to bind context or perform custom
instrumentation.

#### Channel: `'tracing:node.test:start'`

- `data` [`<Object>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Object)
  - `name` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) The name of the test.
  - `nesting` [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) The nesting level of the test.
  - `file` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) | [`<undefined>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#undefined_type) The path to the test file, or `undefined` when
    running in the REPL.
  - `type` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) The type of test. Either `'test'` or `'suite'`.

Emitted when a test or suite starts execution. The test's span encompasses all
of its before, beforeEach, and afterEach hooks, as well as the test body.

#### Channel: `'tracing:node.test:end'`

- `data` [`<Object>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Object)
  - `name` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) The name of the test.
  - `nesting` [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) The nesting level of the test.
  - `file` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) | [`<undefined>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#undefined_type) The path to the test file, or `undefined` when
    running in the REPL.
  - `type` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) The type of test. Either `'test'` or `'suite'`.

Emitted when a test or suite finishes execution.

#### Channel: `'tracing:node.test:error'`

- `data` [`<Object>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Object)
  - `name` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) The name of the test.
  - `nesting` [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) The nesting level of the test.
  - `file` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) | [`<undefined>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#undefined_type) The path to the test file, or `undefined` when
    running in the REPL.
  - `type` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) The type of test. Either `'test'` or `'suite'`.
  - `error` [`<Error>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Error) The error that was thrown.

Emitted when a test or suite throws an error.

### Context propagation with `bindStore()`

The tracing channel can be used to propagate context through test execution by
binding an `AsyncLocalStorage` instance. This allows context to be automatically
available in the test function and all async operations within the test.

```js
import dc from 'node:diagnostics_channel';
import { AsyncLocalStorage } from 'node:async_hooks';

const testStorage = new AsyncLocalStorage();
const testChannel = dc.tracingChannel('node.test');

// Bind context to test execution — the returned value becomes the store
testChannel.start.bindStore(testStorage, (data) => {
  return { testName: data.name, startTime: Date.now() };
});

// Optionally handle errors and cleanup
testChannel.error.subscribe((data) => {
  const store = testStorage.getStore();
  console.log(`Test "${data.name}" failed after ${Date.now() - store.startTime}ms`);
});

testChannel.end.subscribe((data) => {
  const store = testStorage.getStore();
  console.log(`Test "${data.name}" completed in ${Date.now() - store.startTime}ms`);
});
```

When using `bindStore()`, the context provided will be automatically propagated
to the test function and all async operations within the test, without requiring
any additional instrumentation in the test code.

## Class: `TestContext`

Added in: v18.0.0, v16.17.0

**History**

| Version | Changes |
| --- | --- |
| v20.1.0, v18.17.0 | The `before` function was added to TestContext. |

An instance of `TestContext` is passed to each test function in order to
interact with the test runner. However, the `TestContext` constructor is not
exposed as part of the API.

### `context.before([fn][, options])`

Added in: v20.1.0, v18.17.0

- `fn` [`<Function>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Function) | [`<AsyncFunction>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/AsyncFunction) The hook function. The first argument
  to this function is a [`TestContext`](#class-testcontext) object. If the hook uses callbacks,
  the callback function is passed as the second argument. **Default:** A no-op
  function.
- `options` [`<Object>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Object) Configuration options for the hook. The following
  properties are supported:
  - `signal` [`<AbortSignal>`](26-globals.md#class-abortsignal) Allows aborting an in-progress hook.
  - `timeout` [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) A number of milliseconds the hook will fail after.
    If unspecified, subtests inherit this value from their parent. **Default:** `Infinity`.

This function registers a hook that runs before any subtests of the current
test.

### `context.beforeEach([fn][, options])`

Added in: v18.8.0, v16.18.0

- `fn` [`<Function>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Function) | [`<AsyncFunction>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/AsyncFunction) The hook function. The first argument
  to this function is a [`TestContext`](#class-testcontext) object. If the hook uses callbacks,
  the callback function is passed as the second argument. **Default:** A no-op
  function.
- `options` [`<Object>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Object) Configuration options for the hook. The following
  properties are supported:
  - `signal` [`<AbortSignal>`](26-globals.md#class-abortsignal) Allows aborting an in-progress hook.
  - `timeout` [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) A number of milliseconds the hook will fail after.
    If unspecified, subtests inherit this value from their parent. **Default:** `Infinity`.

This function registers a hook that runs before each subtest of the current
test.

```js
test('top level test', async (t) => {
  t.beforeEach((t) => t.diagnostic(`about to run ${t.name}`));
  await t.test(
    'This is a subtest',
    (t) => {
      // Some relevant assertion here
    },
  );
});
```

### `context.after([fn][, options])`

Added in: v19.3.0, v18.13.0

- `fn` [`<Function>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Function) | [`<AsyncFunction>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/AsyncFunction) The hook function. The first argument
  to this function is a [`TestContext`](#class-testcontext) object. If the hook uses callbacks,
  the callback function is passed as the second argument. **Default:** A no-op
  function.
- `options` [`<Object>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Object) Configuration options for the hook. The following
  properties are supported:
  - `signal` [`<AbortSignal>`](26-globals.md#class-abortsignal) Allows aborting an in-progress hook.
  - `timeout` [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) A number of milliseconds the hook will fail after.
    If unspecified, subtests inherit this value from their parent. **Default:** `Infinity`.

This function registers a hook that runs after the current test finishes.

```js
test('top level test', async (t) => {
  t.after((t) => t.diagnostic(`finished running ${t.name}`));
  // Some relevant assertion here
});
```

### `context.afterEach([fn][, options])`

Added in: v18.8.0, v16.18.0

- `fn` [`<Function>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Function) | [`<AsyncFunction>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/AsyncFunction) The hook function. The first argument
  to this function is a [`TestContext`](#class-testcontext) object. If the hook uses callbacks,
  the callback function is passed as the second argument. **Default:** A no-op
  function.
- `options` [`<Object>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Object) Configuration options for the hook. The following
  properties are supported:
  - `signal` [`<AbortSignal>`](26-globals.md#class-abortsignal) Allows aborting an in-progress hook.
  - `timeout` [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) A number of milliseconds the hook will fail after.
    If unspecified, subtests inherit this value from their parent. **Default:** `Infinity`.

This function registers a hook that runs after each subtest of the current
test.

```js
test('top level test', async (t) => {
  t.afterEach((t) => t.diagnostic(`finished running ${t.name}`));
  await t.test(
    'This is a subtest',
    (t) => {
      // Some relevant assertion here
    },
  );
});
```

### `context.assert`

Added in: v22.2.0, v20.15.0

An object containing assertion methods bound to `context`. The top-level
functions from the `node:assert` module are exposed here for the purpose of
creating test plans.

```js
test('test', (t) => {
  t.plan(1);
  t.assert.strictEqual(true, true);
});
```

#### `context.assert.callCount(fn, times[, message])`

Added in: v26.12.0

- `fn` [`<Function>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Function) A mock function created by the test runner's mocking API.
- `times` [`<integer>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) The expected number of calls.
- `message` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) Optional error message.

Asserts that the mock function `fn` has been called exactly `times` times.

```js
test('mock was called twice', (t) => {
  const fn = t.mock.fn();
  fn();
  fn();
  t.assert.callCount(fn, 2);
});
```

#### `context.assert.called(fn[, message])`

Added in: v26.12.0

- `fn` [`<Function>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Function) A mock function created by the test runner's mocking API.
- `message` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) Optional error message.

Asserts that the mock function `fn` has been called at least once.

```js
test('mock was called', (t) => {
  const fn = t.mock.fn();
  fn();
  t.assert.called(fn);
});
```

#### `context.assert.fileSnapshot(value, path[, options])`

Added in: v23.7.0, v22.14.0

- `value` [`<any>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#Data_types) A value to serialize to a string. If Node.js was started with
  the [`--test-update-snapshots`](13-cli.md#--test-update-snapshots) flag, the serialized value is written to
  `path`. Otherwise, the serialized value is compared to the contents of the
  existing snapshot file.
- `path` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) The file where the serialized `value` is written.
- `options` [`<Object>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Object) Optional configuration options. The following properties
  are supported:
  - `serializers` [`<Array>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Array) An array of synchronous functions used to serialize `value` into a string. `value` is passed as the only argument to the first
    serializer function. The return value of each serializer is passed as input
    to the next serializer. Once all serializers have run, the resulting value
    is coerced to a string. **Default:** If no serializers are provided, the
    test runner's default serializers are used.

This function serializes `value` and writes it to the file specified by `path`.

```js
test('snapshot test with default serialization', (t) => {
  t.assert.fileSnapshot({ value1: 1, value2: 2 }, './snapshots/snapshot.json');
});
```

This function differs from `context.assert.snapshot()` in the following ways:

- The snapshot file path is explicitly provided by the user.
- Each snapshot file is limited to a single snapshot value.
- No additional escaping is performed by the test runner.

These differences allow snapshot files to better support features such as syntax
highlighting.

[Contents](../README.md) | [Previous: Test runner, part 2 of 4](53-test-part2.md) | [Next: Test runner, part 4 of 4](53-test-part4.md)
