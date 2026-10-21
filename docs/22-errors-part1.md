[Contents](../README.md) | [Previous: Environment Variables](21-environment-variables.md) | [Next: Errors, part 2 of 5](22-errors-part2.md)

Source: [https://nodejs.org/docs/latest/api/errors.html](https://nodejs.org/docs/latest/api/errors.html)

Part 1 of 5 of this section.

# Errors

Applications running in Node.js will generally experience the following
categories of errors:

- Standard JavaScript errors such as [`<EvalError>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/EvalError), [`<SyntaxError>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/SyntaxError), [`<RangeError>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/RangeError),
  [`<ReferenceError>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/ReferenceError), [`<TypeError>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/TypeError), and [`<URIError>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/URIError).
- Standard `DOMException`s.
- System errors triggered by underlying operating system constraints such
  as attempting to open a file that does not exist or attempting to send data
  over a closed socket.
- `AssertionError`s are a special class of error that can be triggered when
  Node.js detects an exceptional logic violation that should never occur. These
  are raised typically by the `node:assert` module.
- User-specified errors triggered by application code.

All JavaScript and system errors raised by Node.js inherit from, or are
instances of, the standard JavaScript [`<Error>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Error) class and are guaranteed
to provide *at least* the properties available on that class.

The [`error.message`](#errormessage) property of errors raised by Node.js may be changed in
any versions. Use [`error.code`](#errorcode) to identify an error instead. For a
`DOMException`, use [`domException.name`](https://developer.mozilla.org/en-US/docs/Web/API/DOMException/name) to identify its type.

## Error propagation and interception

Node.js supports several mechanisms for propagating and handling errors that
occur while an application is running. How these errors are reported and
handled depends entirely on the type of `Error` and the style of the API that is
called.

All JavaScript errors are handled as exceptions that *immediately* generate
and throw an error using the standard JavaScript `throw` mechanism. These
are handled using the [`try…catch` construct](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Statements/try...catch) provided by the
JavaScript language.

```js
// Throws with a ReferenceError because z is not defined.
try {
  const m = 1;
  const n = m + z;
} catch (err) {
  // Handle the error here.
}
```

Any use of the JavaScript `throw` mechanism will raise an exception that
*must* be handled or the Node.js process will exit immediately.

With few exceptions, *Synchronous* APIs (any blocking method that does not
return a [`<Promise>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Promise) nor accept a `callback` function, such as
[`fs.readFileSync`](24-fs-part5.md#fsreadfilesyncpath-options)), will use `throw` to report errors.

Errors that occur within *Asynchronous APIs* may be reported in multiple ways:

- Some asynchronous methods returns a [`<Promise>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Promise), you should always take into
  account that it might be rejected. See [`--unhandled-rejections`](13-cli.md#--unhandled-rejectionsmode) flag for
  how the process will react to an unhandled promise rejection.

  ```js
  const fs = require('node:fs/promises');

  (async () => {
    let data;
    try {
      data = await fs.readFile('a file that does not exist');
    } catch (err) {
      console.error('There was an error reading the file!', err);
      return;
    }
    // Otherwise handle the data
  })();
  ```
- Most asynchronous methods that accept a `callback` function will accept an
  `Error` object passed as the first argument to that function. If that first
  argument is not `null` and is an instance of `Error`, then an error occurred
  that should be handled.

  ```js
  const fs = require('node:fs');
  fs.readFile('a file that does not exist', (err, data) => {
    if (err) {
      console.error('There was an error reading the file!', err);
      return;
    }
    // Otherwise handle the data
  });
  ```
- When an asynchronous method is called on an object that is an
  [`EventEmitter`](23-events.md#class-eventemitter), errors can be routed to that object's `'error'` event.

  ```js
  const net = require('node:net');
  const connection = net.connect('localhost');

  // Adding an 'error' event handler to a stream:
  connection.on('error', (err) => {
    // If the connection is reset by the server, or if it can't
    // connect at all, or on any sort of error encountered by
    // the connection, the error will be sent here.
    console.error(err);
  });

  connection.pipe(process.stdout);
  ```
- A handful of typically asynchronous methods in the Node.js API may still
  use the `throw` mechanism to raise exceptions that must be handled using
  `try…catch`. There is no comprehensive list of such methods; please
  refer to the documentation of each method to determine the appropriate
  error handling mechanism required.

The use of the `'error'` event mechanism is most common for [stream-based](51-stream-part1.md)
and [event emitter-based](23-events.md#class-eventemitter) APIs, which themselves represent a series of
asynchronous operations over time (as opposed to a single operation that may
pass or fail).

For *all* [`EventEmitter`](23-events.md#class-eventemitter) objects, if an `'error'` event handler is not
provided, the error will be thrown, causing the Node.js process to report an
uncaught exception and crash unless either: a handler has been registered for
the [`'uncaughtException'`](43-process-part1.md#event-uncaughtexception) event, or the deprecated [`node:domain`](20-domain.md)
module is used.

```js
const EventEmitter = require('node:events');
const ee = new EventEmitter();

setImmediate(() => {
  // This will crash the process because no 'error' event
  // handler has been added.
  ee.emit('error', new Error('This will crash'));
});
```

Errors generated in this way *cannot* be intercepted using `try…catch` as
they are thrown *after* the calling code has already exited.

Developers must refer to the documentation for each method to determine
exactly how errors raised by those methods are propagated.

## Class: `Error`

A generic JavaScript [`<Error>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Error) object that does not denote any specific
circumstance of why the error occurred. `Error` objects capture a "stack trace"
detailing the point in the code at which the `Error` was instantiated, and may
provide a text description of the error.

All errors generated by Node.js, including all system and JavaScript errors,
will either be instances of, or inherit from, the `Error` class.

### `new Error(message[, options])`

- `message` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type)
- `options` [`<Object>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Object)
  - `cause` [`<any>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#Data_types) The error that caused the newly created error.

Creates a new `Error` object and sets the `error.message` property to the
provided text message. If an object is passed as `message`, the text message
is generated by calling `String(message)`. If the `cause` option is provided,
it is assigned to the `error.cause` property. The `error.stack` property will
represent the point in the code at which `new Error()` was called. Stack traces
are dependent on [V8's stack trace API](https://v8.dev/docs/stack-trace-api). Stack traces extend only to either
(a) the beginning of *synchronous code execution*, or (b) the number of frames
given by the property `Error.stackTraceLimit`, whichever is smaller.

### `Error.captureStackTrace(targetObject[, constructorOpt])`

- `targetObject` [`<Object>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Object)
- `constructorOpt` [`<Function>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Function)

Creates a `.stack` property on `targetObject`, which when accessed returns
a string representing the location in the code at which
`Error.captureStackTrace()` was called.

```js
const myObject = {};
Error.captureStackTrace(myObject);
myObject.stack;  // Similar to `new Error().stack`
```

The first line of the trace will be prefixed with
`${myObject.name}: ${myObject.message}`.

The optional `constructorOpt` argument accepts a function. If given, all frames
above `constructorOpt`, including `constructorOpt`, will be omitted from the
generated stack trace.

The `constructorOpt` argument is useful for hiding implementation
details of error generation from the user. For instance:

```js
function a() {
  b();
}

function b() {
  c();
}

function c() {
  // Create an error without stack trace to avoid calculating the stack trace twice.
  const { stackTraceLimit } = Error;
  Error.stackTraceLimit = 0;
  const error = new Error();
  Error.stackTraceLimit = stackTraceLimit;

  // Capture the stack trace above function b
  Error.captureStackTrace(error, b); // Neither function c, nor b is included in the stack trace
  throw error;
}

a();
```

### `Error.stackTraceLimit`

- Type: [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type)

The `Error.stackTraceLimit` property specifies the number of stack frames
collected by a stack trace (whether generated by `new Error().stack` or
`Error.captureStackTrace(obj)`).

The default value is `10` but may be set to any valid JavaScript number. Changes
will affect any stack trace captured *after* the value has been changed.

If set to a non-number value, or set to a negative number, stack traces will
not capture any frames.

### `error.cause`

Added in: v16.9.0

- Type: [`<any>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#Data_types)

If present, the `error.cause` property is the underlying cause of the `Error`.
It is used when catching an error and throwing a new one with a different
message or code in order to still have access to the original error.

The `error.cause` property is typically set by calling
`new Error(message, { cause })`. It is not set by the constructor if the
`cause` option is not provided.

This property allows errors to be chained. When serializing `Error` objects,
[`util.inspect()`](60-util-part1.md#utilinspectobject-options) recursively serializes `error.cause` if it is set.

```js
const cause = new Error('The remote HTTP server responded with a 500 status');
const symptom = new Error('The message failed to send', { cause });

console.log(symptom);
// Prints:
//   Error: The message failed to send
//       at REPL2:1:17
//       at Script.runInThisContext (node:vm:130:12)
//       ... 7 lines matching cause stack trace ...
//       at [_line] [as _line] (node:internal/readline/interface:886:18) {
//     [cause]: Error: The remote HTTP server responded with a 500 status
//         at REPL1:1:15
//         at Script.runInThisContext (node:vm:130:12)
//         at REPLServer.defaultEval (node:repl:574:29)
//         at bound (node:domain:426:15)
//         at REPLServer.runBound [as eval] (node:domain:437:12)
//         at REPLServer.onLine (node:repl:902:10)
//         at REPLServer.emit (node:events:549:35)
//         at REPLServer.emit (node:domain:482:12)
//         at [_onLine] [as _onLine] (node:internal/readline/interface:425:12)
//         at [_line] [as _line] (node:internal/readline/interface:886:18)
```

### `error.code`

- Type: [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type)

The `error.code` property is a string label that identifies the kind of error.
`error.code` is the most stable way to identify an error. It will only change
between major versions of Node.js. In contrast, `error.message` strings may
change between any versions of Node.js. See [Node.js error codes](#nodejs-error-codes) for details
about specific codes.

### `error.message`

- Type: [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type)

The `error.message` property is the string description of the error as set by
calling `new Error(message)`. The `message` passed to the constructor will also
appear in the first line of the stack trace of the `Error`, however changing
this property after the `Error` object is created *may not* change the first
line of the stack trace (for example, when `error.stack` is read before this
property is changed).

```js
const err = new Error('The message');
console.error(err.message);
// Prints: The message
```

### `error.stack`

- Type: [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type)

The `error.stack` property is a string describing the point in the code at which
the `Error` was instantiated.

```console
Error: Things keep happening!
   at /home/gbusey/file.js:525:2
   at Frobnicator.refrobulate (/home/gbusey/business-logic.js:424:21)
   at Actor.<anonymous> (/home/gbusey/actors.js:400:8)
   at increaseSynergy (/home/gbusey/actors.js:701:6)
```

The first line is formatted as `<error class name>: <error message>`, and
is followed by a series of stack frames (each line beginning with "at ").
Each frame describes a call site within the code that lead to the error being
generated. V8 attempts to display a name for each function (by variable name,
function name, or object method name), but occasionally it will not be able to
find a suitable name. If V8 cannot determine a name for the function, only
location information will be displayed for that frame. Otherwise, the
determined function name will be displayed with location information appended
in parentheses.

Frames are only generated for JavaScript functions. If, for example, execution
synchronously passes through a C++ addon function called `cheetahify` which
itself calls a JavaScript function, the frame representing the `cheetahify` call
will not be present in the stack traces:

```js
const cheetahify = require('./native-binding.node');

function makeFaster() {
  // `cheetahify()` *synchronously* calls speedy.
  cheetahify(function speedy() {
    throw new Error('oh no!');
  });
}

makeFaster();
// will throw:
//   /home/gbusey/file.js:6
//       throw new Error('oh no!');
//           ^
//   Error: oh no!
//       at speedy (/home/gbusey/file.js:6:11)
//       at makeFaster (/home/gbusey/file.js:5:3)
//       at Object.<anonymous> (/home/gbusey/file.js:10:1)
//       at Module._compile (module.js:456:26)
//       at Object.Module._extensions..js (module.js:474:10)
//       at Module.load (module.js:356:32)
//       at Function.Module._load (module.js:312:12)
//       at Function.Module.runMain (module.js:497:10)
//       at startup (node.js:119:16)
//       at node.js:906:3
```

The location information will be one of:

- `native`, if the frame represents a call internal to V8 (as in `[].forEach`).
- `plain-filename.js:line:column`, if the frame represents a call internal
  to Node.js.
- `/absolute/path/to/file.js:line:column`, if the frame represents a call in
  a user program (using CommonJS module system), or its dependencies.
- `<transport-protocol>:///url/to/module/file.mjs:line:column`, if the frame
  represents a call in a user program (using ES module system), or
  its dependencies.

The number of frames captured by the stack trace is bounded by the smaller of
`Error.stackTraceLimit` or the number of available frames on the current event
loop tick.

`error.stack` is a getter/setter for a hidden internal property which is only
present on builtin `Error` objects (those for which [`Error.isError`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Error/isError) returns
true). If `error` is not a builtin error object, then the `error.stack` getter
will always return `undefined`, and the setter will do nothing. This can occur
if the accessor is manually invoked with a `this` value that is not a builtin
error object, such as a [`<Proxy>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Proxy).

## Class: `AssertionError`

- Extends: [`<errors.Error>`](22-errors-part1.md#class-errorserror)

Indicates the failure of an assertion. For details, see
[`Class: assert.AssertionError`](03-assert.md#class-assertassertionerror).

## Class: `DOMException`

Added in: v17.0.0

- Extends: [`<errors.Error>`](22-errors-part1.md#class-errorserror)

The Web IDL [`<DOMException>`](https://developer.mozilla.org/docs/Web/API/DOMException) class. These errors are thrown by web-platform APIs
in Node.js such as [`fetch()`](26-globals.md#fetch), [`<AbortController>`](26-globals.md#class-abortcontroller), [`<AbortSignal>`](26-globals.md#class-abortsignal), and Web
Streams. For details, see also [`Class: DOMException`](26-globals.md#class-domexception) on the Globals page.

The [`domException.name`](https://developer.mozilla.org/en-US/docs/Web/API/DOMException/name) property identifies the type of the exception (for
example, `'AbortError'`). Unlike most errors in Node.js, the
[`domException.code`](https://developer.mozilla.org/en-US/docs/Web/API/DOMException/code) property is a number that corresponds to a
[legacy error code name](https://webidl.spec.whatwg.org/#dfn-error-names-table) (for example, `20` for
`ABORT_ERR`).

Node.js-specific APIs that support [`<AbortSignal>`](26-globals.md#class-abortsignal) (such as [`events.once()`](23-events.md#eventsonceemitter-name-options)) throw a Node.js `AbortError` (a native [`<errors.Error>`](22-errors-part1.md#class-errorserror) with `name` of `'AbortError'` and `code` of [`'ABORT_ERR'`](#abort_err)) rather than a
[`<DOMException>`](https://developer.mozilla.org/docs/Web/API/DOMException). To identify abort errors in either case, checking `err?.name === 'AbortError'` is sufficient.

See also [`ABORT_ERR`](#abort_err).

## Class: `RangeError`

- Extends: [`<errors.Error>`](22-errors-part1.md#class-errorserror)

Indicates that a provided argument was not within the set or range of
acceptable values for a function; whether that is a numeric range, or
outside the set of options for a given function parameter.

```js
require('node:net').connect(-1);
// Throws "RangeError: "port" option should be >= 0 and < 65536: -1"
```

Node.js will generate and throw `RangeError` instances *immediately* as a form
of argument validation.

## Class: `ReferenceError`

- Extends: [`<errors.Error>`](22-errors-part1.md#class-errorserror)

Indicates that an attempt is being made to access a variable that is not
defined. Such errors commonly indicate typos in code, or an otherwise broken
program.

While client code may generate and propagate these errors, in practice, only V8
will do so.

```js
doesNotExist;
// Throws ReferenceError, doesNotExist is not a variable in this program.
```

Unless an application is dynamically generating and running code,
`ReferenceError` instances indicate a bug in the code or its dependencies.

## Class: `SyntaxError`

- Extends: [`<errors.Error>`](22-errors-part1.md#class-errorserror)

Indicates that a program is not valid JavaScript. These errors may only be
generated and propagated as a result of code evaluation. Code evaluation may
happen as a result of `eval`, `Function`, `require`, or [vm](63-vm.md). These errors
are almost always indicative of a broken program.

```js
try {
  require('node:vm').runInThisContext('binary ! isNotOk');
} catch (err) {
  // 'err' will be a SyntaxError.
}
```

`SyntaxError` instances are unrecoverable in the context that created them –
they may only be caught by other contexts.

## Class: `SystemError`

- Extends: [`<errors.Error>`](22-errors-part1.md#class-errorserror)

Node.js generates system errors when exceptions occur within its runtime
environment. These usually occur when an application violates an operating
system constraint. For example, a system error will occur if an application
attempts to read a file that does not exist.

- `address` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) If present, the address to which a network connection
  failed
- `code` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) The string error code
- `dest` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) If present, the file path destination when reporting a file
  system error
- `errno` [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) The system-provided error number
- `info` [`<Object>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Object) If present, extra details about the error condition
- `message` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) A system-provided human-readable description of the error
- `path` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) If present, the file path when reporting a file system error
- `port` [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) If present, the network connection port that is not available
- `syscall` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) The name of the system call that triggered the error

### `error.address`

- Type: [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type)

If present, `error.address` is a string describing the address to which a
network connection failed.

### `error.code`

- Type: [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type)

The `error.code` property is a string representing the error code.

### `error.dest`

- Type: [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type)

If present, `error.dest` is the file path destination when reporting a file
system error.

### `error.errno`

- Type: [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type)

The `error.errno` property is a negative number which corresponds
to the error code defined in [`libuv Error handling`](https://docs.libuv.org/en/v1.x/errors.html).

On Windows the error number provided by the system will be normalized by libuv.

To get the string representation of the error code, use
[`util.getSystemErrorName(error.errno)`](60-util-part1.md#utilgetsystemerrornameerr).

### `error.info`

- Type: [`<Object>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Object)

If present, `error.info` is an object with details about the error condition.

### `error.message`

- Type: [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type)

`error.message` is a system-provided human-readable description of the error.

### `error.path`

- Type: [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type)

If present, `error.path` is a string containing a relevant invalid pathname.

### `error.port`

- Type: [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type)

If present, `error.port` is the network connection port that is not available.

### `error.syscall`

- Type: [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type)

The `error.syscall` property is a string describing the [syscall](https://man7.org/linux/man-pages/man2/syscalls.2.html) that failed.

### Common system errors

This is a list of system errors commonly-encountered when writing a Node.js
program. For a comprehensive list, see the [`errno`(3) man page](https://man7.org/linux/man-pages/man3/errno.3.html).

- `EACCES` (Permission denied): An attempt was made to access a file in a way
  forbidden by its file access permissions.
- `EADDRINUSE` (Address already in use): An attempt to bind a server
  ([`net`](38-net.md), [`http`](27-http-part1.md), or [`https`](29-https.md)) to a local address failed due to
  another server on the local system already occupying that address.
- `ECONNREFUSED` (Connection refused): No connection could be made because the
  target machine actively refused it. This usually results from trying to
  connect to a service that is inactive on the foreign host.
- `ECONNRESET` (Connection reset by peer): A connection was forcibly closed by
  a peer. This normally results from a loss of the connection on the remote
  socket due to a timeout or reboot. Commonly encountered via the [`http`](27-http-part1.md)
  and [`net`](38-net.md) modules.
- `EEXIST` (File exists): An existing file was the target of an operation that
  required that the target not exist.
- `EISDIR` (Is a directory): An operation expected a file, but the given
  pathname was a directory.
- `EMFILE` (Too many open files in system): Maximum number of
  [file descriptors](https://en.wikipedia.org/wiki/File_descriptor) allowable on the system has been reached, and
  requests for another descriptor cannot be fulfilled until at least one
  has been closed. This is encountered when opening many files at once in
  parallel, especially on systems (in particular, macOS) where there is a low
  file descriptor limit for processes. To remedy a low limit, run
  `ulimit -n 2048` in the same shell that will run the Node.js process.
- `ENOENT` (No such file or directory): Commonly raised by [`fs`](24-fs-part1.md) operations
  to indicate that a component of the specified pathname does not exist. No
  entity (file or directory) could be found by the given path.
- `ENOTDIR` (Not a directory): A component of the given pathname existed, but
  was not a directory as expected. Commonly raised by [`fs.readdir`](24-fs-part4.md#fsreaddirpath-options-callback).
- `ENOTEMPTY` (Directory not empty): A directory with entries was the target
  of an operation that requires an empty directory, usually [`fs.unlink`](24-fs-part4.md#fsunlinkpath-callback).
- `ENOTFOUND` (DNS lookup failed): Indicates a DNS failure of either
  `EAI_NODATA` or `EAI_NONAME`. This is not a standard POSIX error.
- `EPERM` (Operation not permitted): An attempt was made to perform an
  operation that requires elevated privileges.
- `EPIPE` (Broken pipe): A write on a pipe, socket, or FIFO for which there is
  no process to read the data. Commonly encountered at the [`net`](38-net.md) and
  [`http`](27-http-part1.md) layers, indicative that the remote side of the stream being
  written to has been closed.
- `ETIMEDOUT` (Operation timed out): A connect or send request failed because
  the connected party did not properly respond after a period of time. Usually
  encountered by [`http`](27-http-part1.md) or [`net`](38-net.md). Often a sign that a `socket.end()`
  was not properly called.

## Class: `TypeError`

- Extends [`<errors.Error>`](22-errors-part1.md#class-errorserror)

Indicates that a provided argument is not an allowable type. For example,
passing a function to a parameter which expects a string would be a `TypeError`.

```js
require('node:url').parse(() => { });
// Throws TypeError, since it expected a string.
```

Node.js will generate and throw `TypeError` instances *immediately* as a form
of argument validation.

## Exceptions vs. errors

A JavaScript exception is a value that is thrown as a result of an invalid
operation or as the target of a `throw` statement. While it is not required
that these values are instances of `Error` or classes which inherit from
`Error`, all exceptions thrown by Node.js or the JavaScript runtime *will* be
instances of `Error`.

Some exceptions are *unrecoverable* at the JavaScript layer. Such exceptions
will *always* cause the Node.js process to crash. Examples include `assert()`
checks or `abort()` calls in the C++ layer.

## OpenSSL errors

Errors originating in `crypto` or `tls` are of class `Error`, and in addition to
the standard `.code` and `.message` properties, may have some additional
OpenSSL-specific properties.

### `error.opensslErrorStack`

An array of errors that can give context to where in the OpenSSL library an
error originates from.

### `error.function`

The OpenSSL function the error originates in.

### `error.library`

The OpenSSL library the error originates in.

### `error.reason`

A human-readable string describing the reason for the error.

## Node.js error codes

### `ABORT_ERR`

Added in: v15.0.0

Used when an operation has been aborted (typically using an `AbortController`).

APIs *not* using `AbortSignal`s typically do not raise an error with this code.

This code does not use the regular `ERR_*` convention Node.js errors use in
order to be compatible with the web platform's `AbortError`.

### `ERR_ACCESS_DENIED`

A special type of error that is triggered whenever Node.js tries to get access
to a resource restricted by the [Permission Model](42-permissions.md#permission-model).

### `ERR_AMBIGUOUS_ARGUMENT`

A function argument is being used in a way that suggests that the function
signature may be misunderstood. This is thrown by the `node:assert` module when
the `message` parameter in `assert.throws(block, message)` matches the error
message thrown by `block` because that usage suggests that the user believes
`message` is the expected message rather than the message the `AssertionError`
will display if `block` does not throw.

### `ERR_ARG_NOT_ITERABLE`

An iterable argument (i.e. a value that works with `for...of` loops) was
required, but not provided to a Node.js API.

### `ERR_ASSERTION`

A special type of error that can be triggered whenever Node.js detects an
exceptional logic violation that should never occur. These are raised typically
by the `node:assert` module.

### `ERR_ASYNC_CALLBACK`

An attempt was made to register something that is not a function as an
`AsyncHooks` callback.

### `ERR_ASYNC_LOADER_REQUEST_NEVER_SETTLED`

An operation related to module loading is customized by an asynchronous loader
hook that never settled the promise before the loader thread exits.

### `ERR_ASYNC_TYPE`

The type of an asynchronous resource was invalid. Users are also able
to define their own types if using the public embedder API.

### `ERR_BROTLI_COMPRESSION_FAILED`

Data passed to a Brotli stream was not successfully compressed.

### `ERR_BROTLI_INVALID_PARAM`

An invalid parameter key was passed during construction of a Brotli stream.

### `ERR_BUFFER_CONTEXT_NOT_AVAILABLE`

An attempt was made to create a Node.js `Buffer` instance from addon or embedder
code, while in a JS engine Context that is not associated with a Node.js
instance. The data passed to the `Buffer` method will have been released
by the time the method returns.

When encountering this error, a possible alternative to creating a `Buffer`
instance is to create a normal `Uint8Array`, which only differs in the
prototype of the resulting object. `Uint8Array`s are generally accepted in all
Node.js core APIs where `Buffer`s are; they are available in all Contexts.

### `ERR_BUFFER_OUT_OF_BOUNDS`

An operation outside the bounds of a `Buffer` was attempted.

### `ERR_BUFFER_TOO_LARGE`

An attempt has been made to create a `Buffer` larger than the maximum allowed
size.

### `ERR_CANNOT_WATCH_SIGINT`

Node.js was unable to watch for the `SIGINT` signal.

### `ERR_CHILD_CLOSED_BEFORE_REPLY`

A child process was closed before the parent received a reply.

### `ERR_CHILD_PROCESS_IPC_REQUIRED`

Used when a child process is being forked without specifying an IPC channel.

### `ERR_CHILD_PROCESS_STDIO_MAXBUFFER`

Used when the main process is trying to read data from the child process's
STDERR/STDOUT, and the data's length is longer than the `maxBuffer` option.

### `ERR_CLOSED_MESSAGE_PORT`

Added in: v10.5.0

**History**

| Version | Changes |
| --- | --- |
| v16.2.0, v14.17.1 | The error message was reintroduced. |
| v11.12.0 | The error message was removed. |

There was an attempt to use a `MessagePort` instance in a closed
state, usually after `.close()` has been called.

### `ERR_CONSOLE_WRITABLE_STREAM`

`Console` was instantiated without `stdout` stream, or `Console` has a
non-writable `stdout` or `stderr` stream.

### `ERR_CONSTRUCT_CALL_INVALID`

Added in: v12.5.0

A class constructor was called that is not callable.

### `ERR_CONSTRUCT_CALL_REQUIRED`

A constructor for a class was called without `new`.

### `ERR_CONTEXT_NOT_INITIALIZED`

The vm context passed into the API is not yet initialized. This could happen
when an error occurs (and is caught) during the creation of the
context, for example, when the allocation fails or the maximum call stack
size is reached when the context is created.

### `ERR_CPU_PROFILE_ALREADY_STARTED`

Added in: v24.8.0, v22.20.0

The CPU profile with the given name is already started.

### `ERR_CPU_PROFILE_NOT_STARTED`

Added in: v24.8.0, v22.20.0

The CPU profile with the given name is not started.

### `ERR_CPU_PROFILE_TOO_MANY`

Added in: v24.8.0, v22.20.0

There are too many CPU profiles being collected.

### `ERR_CRYPTO_ARGON2_NOT_SUPPORTED`

Argon2 is not supported by the current version of OpenSSL being used.

### `ERR_CRYPTO_CUSTOM_ENGINE_NOT_SUPPORTED`

An OpenSSL engine was requested (for example, through the `clientCertEngine` or
`privateKeyEngine` TLS options) that is not supported by the version of OpenSSL
being used, likely due to the compile-time flag `OPENSSL_NO_ENGINE`.

### `ERR_CRYPTO_ECDH_INVALID_FORMAT`

An invalid value for the `format` argument was passed to the `crypto.ECDH()`
class `getPublicKey()` method.

### `ERR_CRYPTO_ECDH_INVALID_PUBLIC_KEY`

An invalid value for the `key` argument has been passed to the
`crypto.ECDH()` class `computeSecret()` method. It means that the public
key lies outside of the elliptic curve.

### `ERR_CRYPTO_ENGINE_UNKNOWN`

An invalid crypto engine identifier was passed to
[`require('node:crypto').setEngine()`](15-crypto-part5.md#cryptosetengineengine-flags).

### `ERR_CRYPTO_FIPS_FORCED`

The [`--force-fips`](13-cli.md#--force-fips) command-line argument was used but there was an attempt
to enable or disable FIPS mode in the `node:crypto` module.

### `ERR_CRYPTO_FIPS_UNAVAILABLE`

An attempt was made to enable or disable FIPS mode, but FIPS mode was not
available.

### `ERR_CRYPTO_HASH_FINALIZED`

[`hash.digest()`](15-crypto-part2.md#hashdigestencoding) was called multiple times. The `hash.digest()` method must
be called no more than one time per instance of a `Hash` object.

### `ERR_CRYPTO_HASH_UPDATE_FAILED`

[`hash.update()`](15-crypto-part2.md#hashupdatedata-inputencoding) failed for an unspecified reason.

### `ERR_CRYPTO_INCOMPATIBLE_KEY`

The given crypto keys are incompatible with the attempted operation.

### `ERR_CRYPTO_INCOMPATIBLE_KEY_OPTIONS`

The selected public or private key encoding is incompatible with other options.

### `ERR_CRYPTO_INITIALIZATION_FAILED`

Added in: v15.0.0

Initialization of the crypto subsystem failed.

### `ERR_CRYPTO_INVALID_AUTH_TAG`

Added in: v15.0.0

An invalid authentication tag was provided.

### `ERR_CRYPTO_INVALID_COUNTER`

Added in: v15.0.0

An invalid counter was provided for a counter-mode cipher.

### `ERR_CRYPTO_INVALID_CURVE`

Added in: v15.0.0

An invalid elliptic-curve was provided.

### `ERR_CRYPTO_INVALID_DIGEST`

An invalid [crypto digest algorithm](15-crypto-part4.md#cryptogethashes) was specified.

### `ERR_CRYPTO_INVALID_IV`

Added in: v15.0.0

An invalid initialization vector was provided.

### `ERR_CRYPTO_INVALID_JWK`

Added in: v15.0.0

An invalid JSON Web Key was provided.

### `ERR_CRYPTO_INVALID_KEYLEN`

Added in: v15.0.0

An invalid key length was provided.

### `ERR_CRYPTO_INVALID_KEYPAIR`

Added in: v15.0.0

An invalid key pair was provided.

### `ERR_CRYPTO_INVALID_KEYTYPE`

Added in: v15.0.0

An invalid key type was provided.

### `ERR_CRYPTO_INVALID_KEY_OBJECT_TYPE`

The given crypto key object's type is invalid for the attempted operation.

### `ERR_CRYPTO_INVALID_MAC`

Added in: v26.9.0

An invalid MAC algorithm was specified.

### `ERR_CRYPTO_INVALID_MESSAGELEN`

Added in: v15.0.0

An invalid message length was provided.

### `ERR_CRYPTO_INVALID_SCRYPT_PARAMS`

Added in: v15.0.0

One or more [`crypto.scrypt()`](15-crypto-part5.md#cryptoscryptpassword-salt-keylen-options-callback) or [`crypto.scryptSync()`](15-crypto-part5.md#cryptoscryptsyncpassword-salt-keylen-options) parameters are
outside their legal range.

### `ERR_CRYPTO_INVALID_STATE`

A crypto method was used on an object that was in an invalid state. For
instance, calling [`cipher.getAuthTag()`](15-crypto-part1.md#ciphergetauthtag) before calling `cipher.final()`.

### `ERR_CRYPTO_INVALID_TAG_LENGTH`

Added in: v15.0.0

An invalid authentication tag length was provided.

### `ERR_CRYPTO_JOB_INIT_FAILED`

Added in: v15.0.0

Initialization of an asynchronous crypto operation failed.

### `ERR_CRYPTO_JWK_UNSUPPORTED_CURVE`

Key's Elliptic Curve is not registered for use in the
[JSON Web Key Elliptic Curve Registry](https://www.iana.org/assignments/jose/jose.xhtml#web-key-elliptic-curve).

### `ERR_CRYPTO_JWK_UNSUPPORTED_KEY_TYPE`

Key's Asymmetric Key Type is not registered for use in the
[JSON Web Key Types Registry](https://www.iana.org/assignments/jose/jose.xhtml#web-key-types).

### `ERR_CRYPTO_KEM_NOT_SUPPORTED`

Added in: v24.7.0

Attempted to use KEM operations while Node.js was not compiled with
OpenSSL with KEM support.

### `ERR_CRYPTO_MAC_FINALIZED`

Added in: v26.9.0

An operation was attempted on a `Mac` object after finalization was attempted
or an underlying MAC update failed.

### `ERR_CRYPTO_MAC_NOT_SUPPORTED`

Added in: v26.9.0

Node.js was built without support for the OpenSSL `EVP_MAC` API.

### `ERR_CRYPTO_MAC_UPDATE_FAILED`

Added in: v26.9.0

[`mac.update()`](15-crypto-part2.md#macupdatedata-inputencoding) failed for an unspecified reason.

### `ERR_CRYPTO_OPERATION_FAILED`

Added in: v15.0.0

A crypto operation failed for an otherwise unspecified reason.

### `ERR_CRYPTO_PBKDF2_ERROR`

The PBKDF2 algorithm failed for unspecified reasons. OpenSSL does not provide
more details and therefore neither does Node.js.

### `ERR_CRYPTO_SCRYPT_NOT_SUPPORTED`

Node.js was compiled without `scrypt` support. Not possible with the official
release binaries but can happen with custom builds, including distro builds.

### `ERR_CRYPTO_SIGN_KEY_REQUIRED`

A signing `key` was not provided to the [`sign.sign()`](15-crypto-part2.md#signsignprivatekey-outputencoding) method.

### `ERR_CRYPTO_TIMING_SAFE_EQUAL_LENGTH`

[`crypto.timingSafeEqual()`](15-crypto-part6.md#cryptotimingsafeequala-b) was called with `Buffer`, `TypedArray`, or
`DataView` arguments of different lengths.

### `ERR_CRYPTO_UNKNOWN_CIPHER`

An unknown cipher was specified.

### `ERR_CRYPTO_UNKNOWN_DH_GROUP`

An unknown Diffie-Hellman group name was given. See
[`crypto.getDiffieHellman()`](15-crypto-part4.md#cryptogetdiffiehellmangroupname) for a list of valid group names.

### `ERR_CRYPTO_UNSUPPORTED_OPERATION`

Added in: v15.0.0, v14.18.0

An attempt to invoke an unsupported crypto operation was made.

### `ERR_DEBUGGER_ERROR`

Added in: v16.4.0, v14.17.4

An error occurred with the [debugger](16-debugger.md).

### `ERR_DEBUGGER_STARTUP_ERROR`

Added in: v16.4.0, v14.17.4

The [debugger](16-debugger.md) timed out waiting for the required host/port to be free.

### `ERR_DIR_CLOSED`

The [`fs.Dir`](24-fs-part6.md#class-fsdir) was previously closed.

### `ERR_DIR_CONCURRENT_OPERATION`

Added in: v14.3.0

A synchronous read or close call was attempted on an [`fs.Dir`](24-fs-part6.md#class-fsdir) which has
ongoing asynchronous operations.

### `ERR_DLOPEN_DISABLED`

Added in: v16.10.0, v14.19.0

Loading native addons has been disabled using [`--no-addons`](13-cli.md#--no-addons).

### `ERR_DLOPEN_FAILED`

Added in: v15.0.0

A call to `process.dlopen()` failed.

### `ERR_DNS_SET_SERVERS_FAILED`

`c-ares` failed to set the DNS server.

### `ERR_DOMAIN_CALLBACK_NOT_AVAILABLE`

The `node:domain` module was not usable since it could not establish the
required error handling hooks, because
[`process.setUncaughtExceptionCaptureCallback()`](43-process-part3.md#processsetuncaughtexceptioncapturecallbackfn) had been called at an
earlier point in time.

### `ERR_DOMAIN_CANNOT_SET_UNCAUGHT_EXCEPTION_CAPTURE`

[`process.setUncaughtExceptionCaptureCallback()`](43-process-part3.md#processsetuncaughtexceptioncapturecallbackfn) could not be called
because the `node:domain` module has been loaded at an earlier point in time.

The stack trace is extended to include the point in time at which the
`node:domain` module had been loaded.

### `ERR_DUPLICATE_STARTUP_SNAPSHOT_MAIN_FUNCTION`

[`v8.startupSnapshot.setDeserializeMainFunction()`](61-v8.md#v8startupsnapshotsetdeserializemainfunctioncallback-data) could not be called
because it had already been called before.

### `ERR_ENCODING_INVALID_ENCODED_DATA`

Data provided to `TextDecoder()` API was invalid according to the encoding
provided.

### `ERR_ENCODING_NOT_SUPPORTED`

Encoding provided to `TextDecoder()` API was not one of the
[WHATWG Supported Encodings](60-util-part2.md#whatwg-supported-encodings).

### `ERR_EVAL_ESM_CANNOT_PRINT`

`--print` cannot be used with ESM input.

### `ERR_EVENT_RECURSION`

Thrown when an attempt is made to recursively dispatch an event on `EventTarget`.

### `ERR_EXECUTION_ENVIRONMENT_NOT_AVAILABLE`

The JS execution context is not associated with a Node.js environment.
This may occur when Node.js is used as an embedded library and some hooks
for the JS engine are not set up properly.

### `ERR_FALSY_VALUE_REJECTION`

A `Promise` that was callbackified via `util.callbackify()` was rejected with a
falsy value.

### `ERR_FEATURE_UNAVAILABLE_ON_PLATFORM`

Added in: v14.0.0

Used when a feature that is not available
to the current platform which is running Node.js is used.

### `ERR_FFI_CALL_FAILED`

A low-level FFI call failed.

### `ERR_FFI_INVALID_POINTER`

An invalid pointer was passed to an FFI operation.

### `ERR_FFI_LIBRARY_CLOSED`

An operation was attempted on an FFI dynamic library after it was closed.

[Contents](../README.md) | [Previous: Environment Variables](21-environment-variables.md) | [Next: Errors, part 2 of 5](22-errors-part2.md)
