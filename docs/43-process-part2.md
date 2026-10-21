[Contents](../README.md) | [Previous: Process, part 1 of 3](43-process-part1.md) | [Next: Process, part 3 of 3](43-process-part3.md)

Source: [https://nodejs.org/docs/latest/api/process.html](https://nodejs.org/docs/latest/api/process.html)

Part 2 of 3 of this section.

# Process (part 2 of 3)

## `process.exitCode`

Added in: v0.11.8

**History**

| Version | Changes |
| --- | --- |
| v20.0.0 | Only accepts a code of type number, or of type string if it represents an integer. |

- Type: [`<integer>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) | [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) | [`<null>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#null_type) | [`<undefined>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#undefined_type) The exit code. For string type, only
  integer strings (e.g.,'1') are allowed. **Default:** `undefined`.

A number which will be the process exit code, when the process either
exits gracefully, or is exited via [`process.exit()`](43-process-part1.md#processexitcode) without specifying
a code.

The value of `process.exitCode` can be updated by either assigning a value to
`process.exitCode` or by passing an argument to [`process.exit()`](43-process-part1.md#processexitcode):

```console
$ node -e 'process.exitCode = 9'; echo $?
9
$ node -e 'process.exit(42)'; echo $?
42
$ node -e 'process.exitCode = 9; process.exit(42)'; echo $?
42
```

The value can also be set implicitly by Node.js when unrecoverable errors occur (e.g.
such as the encountering of an unsettled top-level await). However explicit
manipulations of the exit code always take precedence over implicit ones:

```console
$ node --input-type=module -e 'await new Promise(() => {})'; echo $?
13
$ node --input-type=module -e 'process.exitCode = 9; await new Promise(() => {})'; echo $?
9
```

## `process.features.cached_builtins`

Added in: v12.0.0

- Type: [`<boolean>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#boolean_type)

A boolean value that is `true` if the current Node.js build is caching builtin modules.

## `process.features.debug`

Added in: v0.5.5

- Type: [`<boolean>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#boolean_type)

A boolean value that is `true` if the current Node.js build is a debug build.

## `process.features.inspector`

Added in: v11.10.0

- Type: [`<boolean>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#boolean_type)

A boolean value that is `true` if the current Node.js build includes the inspector.

## `process.features.ipv6`

Added in: v0.5.3Deprecated in: v23.4.0, v22.13.0

Stability: 0 - Deprecated. This property is always true, and any checks based on it are
redundant.

- Type: [`<boolean>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#boolean_type)

A boolean value that is `true` if the current Node.js build includes support for IPv6.

Since all Node.js builds have IPv6 support, this value is always `true`.

## `process.features.require_module`

Added in: v23.0.0, v22.10.0, v20.19.0

- Type: [`<boolean>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#boolean_type)

A boolean value that is `true` if the current Node.js build supports
[loading ECMAScript modules using `require()`](33-modules.md#loading-ecmascript-modules-using-require).

## `process.features.tls`

Added in: v0.5.3

- Type: [`<boolean>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#boolean_type)

A boolean value that is `true` if the current Node.js build includes support for TLS.

## `process.features.tls_alpn`

Added in: v4.8.0Deprecated in: v23.4.0, v22.13.0

Stability: 0 - Deprecated. Use `process.features.tls` instead.

- Type: [`<boolean>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#boolean_type)

A boolean value that is `true` if the current Node.js build includes support for ALPN in TLS.

In Node.js 11.0.0 and later versions, the OpenSSL dependencies feature unconditional ALPN support.
This value is therefore identical to that of `process.features.tls`.

## `process.features.tls_ocsp`

Added in: v0.11.13Deprecated in: v23.4.0, v22.13.0

Stability: 0 - Deprecated. Use `process.features.tls` instead.

- Type: [`<boolean>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#boolean_type)

A boolean value that is `true` if the current Node.js build includes support for OCSP in TLS.

In Node.js 11.0.0 and later versions, the OpenSSL dependencies feature unconditional OCSP support.
This value is therefore identical to that of `process.features.tls`.

## `process.features.tls_sni`

Added in: v0.5.3Deprecated in: v23.4.0, v22.13.0

Stability: 0 - Deprecated. Use `process.features.tls` instead.

- Type: [`<boolean>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#boolean_type)

A boolean value that is `true` if the current Node.js build includes support for SNI in TLS.

In Node.js 11.0.0 and later versions, the OpenSSL dependencies feature unconditional SNI support.
This value is therefore identical to that of `process.features.tls`.

## `process.features.typescript`

Added in: v23.0.0, v22.10.0

**History**

| Version | Changes |
| --- | --- |
| v26.0.0 | Removed `transform` value. |
| v25.2.0, v24.12.0 | Type stripping is now stable. |

Stability: 1.2 - Release candidate

- Type: [`<boolean>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#boolean_type) | [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type)

A value that is `"strip"` by default, and `false` if
Node.js is run with `--no-strip-types`.

## `process.features.uv`

Added in: v0.5.3Deprecated in: v23.4.0, v22.13.0

Stability: 0 - Deprecated. This property is always true, and any checks based on it are
redundant.

- Type: [`<boolean>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#boolean_type)

A boolean value that is `true` if the current Node.js build includes support for libuv.

Since it's not possible to build Node.js without libuv, this value is always `true`.

## `process.finalization.register(ref, callback)`

Added in: v22.5.0

**History**

| Version | Changes |
| --- | --- |
| v26.12.0 | Marked as release candidate. |

Stability: 1.2 - Release candidate

- `ref` [`<Object>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Object) | [`<Function>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Function) The reference to the resource that is being tracked.
- `callback` [`<Function>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Function) The callback function to be called when the resource
  is finalized.
  - `ref` [`<Object>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Object) | [`<Function>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Function) The reference to the resource that is being tracked.
  - `event` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) The event that triggered the finalization. Defaults to 'exit'.

This function registers a callback to be called when the process emits the `exit`
event if the `ref` object was not garbage collected. If the object `ref` was garbage collected
before the `exit` event is emitted, the callback will be removed from the finalization registry,
and it will not be called on process exit.

Inside the callback you can release the resources allocated by the `ref` object.
Be aware that all limitations applied to the `beforeExit` event are also applied to the `callback` function,
this means that there is a possibility that the callback will not be called under special circumstances.

The idea of ​​this function is to help you free up resources when the starts process exiting,
but also let the object be garbage collected if it is no longer being used.

Eg: you can register an object that contains a buffer, you want to make sure that buffer is released
when the process exit, but if the object is garbage collected before the process exit, we no longer
need to release the buffer, so in this case we just remove the callback from the finalization registry.

*CommonJS version:*

```js
const { finalization } = require('node:process');

// Please make sure that the function passed to finalization.register()
// does not create a closure around unnecessary objects.
function onFinalize(obj, event) {
  // You can do whatever you want with the object
  obj.dispose();
}

function setup() {
  // This object can be safely garbage collected,
  // and the resulting shutdown function will not be called.
  // There are no leaks.
  const myDisposableObject = {
    dispose() {
      // Free your resources synchronously
    },
  };

  finalization.register(myDisposableObject, onFinalize);
}

setup();
```

*ES module version:*

```js
import { finalization } from 'node:process';

// Please make sure that the function passed to finalization.register()
// does not create a closure around unnecessary objects.
function onFinalize(obj, event) {
  // You can do whatever you want with the object
  obj.dispose();
}

function setup() {
  // This object can be safely garbage collected,
  // and the resulting shutdown function will not be called.
  // There are no leaks.
  const myDisposableObject = {
    dispose() {
      // Free your resources synchronously
    },
  };

  finalization.register(myDisposableObject, onFinalize);
}

setup();
```

The code above relies on the following assumptions:

- arrow functions are avoided
- regular functions are recommended to be within the global context (root)

Regular functions *could* reference the context where the `obj` lives, making the `obj` not garbage collectible.

Arrow functions will hold the previous context. Consider, for example:

```js
class Test {
  constructor() {
    finalization.register(this, (ref) => ref.dispose());

    // Even something like this is highly discouraged
    // finalization.register(this, () => this.dispose());
  }
  dispose() {}
}
```

It is very unlikely (not impossible) that this object will be garbage collected,
but if it is not, `dispose` will be called when `process.exit` is called.

Be careful and avoid relying on this feature for the disposal of critical resources,
as it is not guaranteed that the callback will be called under all circumstances.

## `process.finalization.registerBeforeExit(ref, callback)`

Added in: v22.5.0

**History**

| Version | Changes |
| --- | --- |
| v26.12.0 | Marked as release candidate. |

Stability: 1.2 - Release candidate

- `ref` [`<Object>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Object) | [`<Function>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Function) The reference
  to the resource that is being tracked.
- `callback` [`<Function>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Function) The callback function to be called when the resource
  is finalized.
  - `ref` [`<Object>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Object) | [`<Function>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Function) The reference to the resource that is being tracked.
  - `event` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) The event that triggered the finalization. Defaults to 'beforeExit'.

This function behaves exactly like the `register`, except that the callback will be called
when the process emits the `beforeExit` event if `ref` object was not garbage collected.

Be aware that all limitations applied to the `beforeExit` event are also applied to the `callback` function,
this means that there is a possibility that the callback will not be called under special circumstances.

## `process.finalization.unregister(ref)`

Added in: v22.5.0

**History**

| Version | Changes |
| --- | --- |
| v26.12.0 | Marked as release candidate. |

Stability: 1.2 - Release candidate

- `ref` [`<Object>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Object) | [`<Function>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Function) The reference
  to the resource that was registered previously.

This function remove the register of the object from the finalization
registry, so the callback will not be called anymore.

*CommonJS version:*

```js
const { finalization } = require('node:process');

// Please make sure that the function passed to finalization.register()
// does not create a closure around unnecessary objects.
function onFinalize(obj, event) {
  // You can do whatever you want with the object
  obj.dispose();
}

function setup() {
  // This object can be safely garbage collected,
  // and the resulting shutdown function will not be called.
  // There are no leaks.
  const myDisposableObject = {
    dispose() {
      // Free your resources synchronously
    },
  };

  finalization.register(myDisposableObject, onFinalize);

  // Do something

  myDisposableObject.dispose();
  finalization.unregister(myDisposableObject);
}

setup();
```

*ES module version:*

```js
import { finalization } from 'node:process';

// Please make sure that the function passed to finalization.register()
// does not create a closure around unnecessary objects.
function onFinalize(obj, event) {
  // You can do whatever you want with the object
  obj.dispose();
}

function setup() {
  // This object can be safely garbage collected,
  // and the resulting shutdown function will not be called.
  // There are no leaks.
  const myDisposableObject = {
    dispose() {
      // Free your resources synchronously
    },
  };

  // Please make sure that the function passed to finalization.register()
  // does not create a closure around unnecessary objects.
  function onFinalize(obj, event) {
    // You can do whatever you want with the object
    obj.dispose();
  }

  finalization.register(myDisposableObject, onFinalize);

  // Do something

  myDisposableObject.dispose();
  finalization.unregister(myDisposableObject);
}

setup();
```

## `process.getActiveResourcesInfo()`

Added in: v17.3.0, v16.14.0

**History**

| Version | Changes |
| --- | --- |
| v24.0.0, v22.16.0 | Change stability index for this feature from Experimental to Stable. |

- Returns: [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type)[]

The `process.getActiveResourcesInfo()` method returns an array of strings
containing the types of the active resources that are currently keeping the
event loop alive.

*ES module version:*

```js
import { getActiveResourcesInfo } from 'node:process';
import { setTimeout } from 'node:timers';

console.log('Before:', getActiveResourcesInfo());
setTimeout(() => {}, 1000);
console.log('After:', getActiveResourcesInfo());
// Prints:
//   Before: [ 'CloseReq', 'TTYWrap', 'TTYWrap', 'TTYWrap' ]
//   After: [ 'CloseReq', 'TTYWrap', 'TTYWrap', 'TTYWrap', 'Timeout' ]
```

*CommonJS version:*

```js
const { getActiveResourcesInfo } = require('node:process');
const { setTimeout } = require('node:timers');

console.log('Before:', getActiveResourcesInfo());
setTimeout(() => {}, 1000);
console.log('After:', getActiveResourcesInfo());
// Prints:
//   Before: [ 'TTYWrap', 'TTYWrap', 'TTYWrap' ]
//   After: [ 'TTYWrap', 'TTYWrap', 'TTYWrap', 'Timeout' ]
```

## `process.getBuiltinModule(id)`

Added in: v22.3.0, v20.16.0

- `id` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) ID of the built-in module being requested.
- Returns: [`<Object>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Object) | [`<undefined>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#undefined_type)

`process.getBuiltinModule(id)` provides a way to load built-in modules
in a globally available function. ES Modules that need to support
other environments can use it to conditionally load a Node.js built-in
when it is run in Node.js, without having to deal with the resolution
error that can be thrown by `import` in a non-Node.js environment or
having to use dynamic `import()` which either turns the module into
an asynchronous module, or turns a synchronous API into an asynchronous one.

```js
if (globalThis.process?.getBuiltinModule) {
  // Run in Node.js, use the Node.js fs module.
  const fs = globalThis.process.getBuiltinModule('fs');
  // If `require()` is needed to load user-modules, use createRequire()
  const module = globalThis.process.getBuiltinModule('module');
  const require = module.createRequire(import.meta.url);
  const foo = require('foo');
}
```

If `id` specifies a built-in module available in the current Node.js process,
`process.getBuiltinModule(id)` method returns the corresponding built-in
module. If `id` does not correspond to any built-in module, `undefined`
is returned.

`process.getBuiltinModule(id)` accepts built-in module IDs that are recognized
by [`module.isBuiltin(id)`](35-module.md#moduleisbuiltinmodulename). Some built-in modules must be loaded with the
`node:` prefix, see [built-in modules with mandatory `node:` prefix](33-modules.md#built-in-modules-with-mandatory-node-prefix).
The references returned by `process.getBuiltinModule(id)` always point to
the built-in module corresponding to `id` even if users modify
[`require.cache`](33-modules.md#requirecache) so that `require(id)` returns something else.

## `process.getegid()`

Added in: v2.0.0

The `process.getegid()` method returns the numerical effective group identity
of the Node.js process. (See [`getegid(2)`](http://man7.org/linux/man-pages/man2/getegid.2.html).)

*ES module version:*

```js
import process from 'node:process';

if (process.getegid) {
  console.log(`Current gid: ${process.getegid()}`);
}
```

*CommonJS version:*

```js
if (process.getegid) {
  console.log(`Current gid: ${process.getegid()}`);
}
```

This function is only available on POSIX platforms (i.e. not Windows or
Android).

## `process.geteuid()`

Added in: v2.0.0

- Returns: [`<Object>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Object)

The `process.geteuid()` method returns the numerical effective user identity of
the process. (See [`geteuid(2)`](http://man7.org/linux/man-pages/man2/geteuid.2.html).)

*ES module version:*

```js
import process from 'node:process';

if (process.geteuid) {
  console.log(`Current uid: ${process.geteuid()}`);
}
```

*CommonJS version:*

```js
if (process.geteuid) {
  console.log(`Current uid: ${process.geteuid()}`);
}
```

This function is only available on POSIX platforms (i.e. not Windows or
Android).

## `process.getgid()`

Added in: v0.1.31

- Returns: [`<Object>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Object)

The `process.getgid()` method returns the numerical group identity of the
process. (See [`getgid(2)`](http://man7.org/linux/man-pages/man2/getgid.2.html).)

*ES module version:*

```js
import process from 'node:process';

if (process.getgid) {
  console.log(`Current gid: ${process.getgid()}`);
}
```

*CommonJS version:*

```js
if (process.getgid) {
  console.log(`Current gid: ${process.getgid()}`);
}
```

This function is only available on POSIX platforms (i.e. not Windows or
Android).

## `process.getgroups()`

Added in: v0.9.4

- Returns: [`<integer>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type)[]

The `process.getgroups()` method returns an array with the supplementary group
IDs. POSIX leaves it unspecified if the effective group ID is included but
Node.js ensures it always is.

*ES module version:*

```js
import process from 'node:process';

if (process.getgroups) {
  console.log(process.getgroups()); // [ 16, 21, 297 ]
}
```

*CommonJS version:*

```js
if (process.getgroups) {
  console.log(process.getgroups()); // [ 16, 21, 297 ]
}
```

This function is only available on POSIX platforms (i.e. not Windows or
Android).

## `process.getuid()`

Added in: v0.1.28

- Returns: [`<integer>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type)

The `process.getuid()` method returns the numeric user identity of the process.
(See [`getuid(2)`](http://man7.org/linux/man-pages/man2/getuid.2.html).)

*ES module version:*

```js
import process from 'node:process';

if (process.getuid) {
  console.log(`Current uid: ${process.getuid()}`);
}
```

*CommonJS version:*

```js
if (process.getuid) {
  console.log(`Current uid: ${process.getuid()}`);
}
```

This function not available on Windows.

## `process.hasUncaughtExceptionCaptureCallback()`

Added in: v9.3.0

- Returns: [`<boolean>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#boolean_type)

Indicates whether a callback has been set using
[`process.setUncaughtExceptionCaptureCallback()`](43-process-part3.md#processsetuncaughtexceptioncapturecallbackfn).

## `process.hrtime([time])`

Added in: v0.7.6

Stability: 3 - Legacy. Use [`process.hrtime.bigint()`](#processhrtimebigint) instead.

- `time` [`<integer>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type)[] The result of a previous call to `process.hrtime()`
- Returns: [`<integer>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type)[]

This is the legacy version of [`process.hrtime.bigint()`](#processhrtimebigint)
before `bigint` was introduced in JavaScript.

The `process.hrtime()` method returns the current high-resolution real time
in a `[seconds, nanoseconds]` tuple `Array`, where `nanoseconds` is the
remaining part of the real time that can't be represented in second precision.

`time` is an optional parameter that must be the result of a previous
`process.hrtime()` call to diff with the current time. If the parameter
passed in is not a tuple `Array`, a `TypeError` will be thrown. Passing in a
user-defined array instead of the result of a previous call to
`process.hrtime()` will lead to undefined behavior.

These times are relative to an arbitrary time in the
past, and not related to the time of day and therefore not subject to clock
drift. The primary use is for measuring performance between intervals:

*ES module version:*

```js
import { hrtime } from 'node:process';

const NS_PER_SEC = 1e9;
const time = hrtime();
// [ 1800216, 25 ]

setTimeout(() => {
  const diff = hrtime(time);
  // [ 1, 552 ]

  console.log(`Benchmark took ${diff[0] * NS_PER_SEC + diff[1]} nanoseconds`);
  // Benchmark took 1000000552 nanoseconds
}, 1000);
```

*CommonJS version:*

```js
const { hrtime } = require('node:process');

const NS_PER_SEC = 1e9;
const time = hrtime();
// [ 1800216, 25 ]

setTimeout(() => {
  const diff = hrtime(time);
  // [ 1, 552 ]

  console.log(`Benchmark took ${diff[0] * NS_PER_SEC + diff[1]} nanoseconds`);
  // Benchmark took 1000000552 nanoseconds
}, 1000);
```

## `process.hrtime.bigint()`

Added in: v10.7.0

- Returns: [`<bigint>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#bigint_type)

The `bigint` version of the [`process.hrtime()`](#processhrtimetime) method returning the
current high-resolution real time in nanoseconds as a `bigint`.

Unlike [`process.hrtime()`](#processhrtimetime), it does not support an additional `time`
argument since the difference can just be computed directly
by subtraction of the two `bigint`s.

*ES module version:*

```js
import { hrtime } from 'node:process';

const start = hrtime.bigint();
// 191051479007711n

setTimeout(() => {
  const end = hrtime.bigint();
  // 191052633396993n

  console.log(`Benchmark took ${end - start} nanoseconds`);
  // Benchmark took 1154389282 nanoseconds
}, 1000);
```

*CommonJS version:*

```js
const { hrtime } = require('node:process');

const start = hrtime.bigint();
// 191051479007711n

setTimeout(() => {
  const end = hrtime.bigint();
  // 191052633396993n

  console.log(`Benchmark took ${end - start} nanoseconds`);
  // Benchmark took 1154389282 nanoseconds
}, 1000);
```

## `process.initgroups(user, extraGroup)`

Added in: v0.9.4

- `user` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) | [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) The user name or numeric identifier.
- `extraGroup` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) | [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) A group name or numeric identifier.

The `process.initgroups()` method reads the `/etc/group` file and initializes
the group access list, using all groups of which the user is a member. This is
a privileged operation that requires that the Node.js process either have `root`
access or the `CAP_SETGID` capability.

Use care when dropping privileges:

*ES module version:*

```js
import { getgroups, initgroups, setgid } from 'node:process';

console.log(getgroups());         // [ 0 ]
initgroups('nodeuser', 1000);     // switch user
console.log(getgroups());         // [ 27, 30, 46, 1000, 0 ]
setgid(1000);                     // drop root gid
console.log(getgroups());         // [ 27, 30, 46, 1000 ]
```

*CommonJS version:*

```js
const { getgroups, initgroups, setgid } = require('node:process');

console.log(getgroups());         // [ 0 ]
initgroups('nodeuser', 1000);     // switch user
console.log(getgroups());         // [ 27, 30, 46, 1000, 0 ]
setgid(1000);                     // drop root gid
console.log(getgroups());         // [ 27, 30, 46, 1000 ]
```

This function is only available on POSIX platforms (i.e. not Windows or
Android).
This feature is not available in [`Worker`](67-worker-threads.md#class-worker) threads.

## `process.kill(pid[, signal])`

Added in: v0.0.6

- `pid` [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) A process ID
- `signal` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) | [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) The signal to send, either as a string or number. **Default:** `'SIGTERM'`.

The `process.kill()` method sends the `signal` to the process identified by
`pid`.

Signal names are strings such as `'SIGINT'` or `'SIGHUP'`. See [Signal Events](43-process-part1.md#signal-events) and [`kill(2)`](http://man7.org/linux/man-pages/man2/kill.2.html) for more information.

This method will throw an error if the target `pid` does not exist. As a special
case, a signal of `0` can be used to test for the existence of a process.
Windows platforms will throw an error if the `pid` is used to kill a process
group.

Even though the name of this function is `process.kill()`, it is really just a
signal sender, like the `kill` system call. The signal sent may do something
other than kill the target process.

*ES module version:*

```js
import process, { kill } from 'node:process';

process.on('SIGHUP', () => {
  console.log('Got SIGHUP signal.');
});

setTimeout(() => {
  console.log('Exiting.');
  process.exit(0);
}, 100);

kill(process.pid, 'SIGHUP');
```

*CommonJS version:*

```js
process.on('SIGHUP', () => {
  console.log('Got SIGHUP signal.');
});

setTimeout(() => {
  console.log('Exiting.');
  process.exit(0);
}, 100);

process.kill(process.pid, 'SIGHUP');
```

When `SIGUSR1` is received by a Node.js process, Node.js will start the
debugger. See [Signal Events](43-process-part1.md#signal-events).

## `process.loadEnvFile(path)`

Added in: v21.7.0, v20.12.0

**History**

| Version | Changes |
| --- | --- |
| v24.10.0, v22.21.0 | This API is no longer experimental. |

- `path` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) | [`<URL>`](59-url.md#the-whatwg-url-api) | [`<Buffer>`](07-buffer-part1.md#class-buffer) | [`<undefined>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#undefined_type). **Default:** `'./.env'`

Loads the `.env` file into `process.env`. Usage of `NODE_OPTIONS`
in the `.env` file will not have any effect on Node.js.

*CommonJS version:*

```js
const { loadEnvFile } = require('node:process');
loadEnvFile();
```

*ES module version:*

```js
import { loadEnvFile } from 'node:process';
loadEnvFile();
```

## `process.mainModule`

Added in: v0.1.17Deprecated in: v14.0.0

Stability: 0 - Deprecated: Use [`require.main`](33-modules.md#accessing-the-main-module) instead.

- Type: [`<Object>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Object)

The `process.mainModule` property provides an alternative way of retrieving
[`require.main`](33-modules.md#accessing-the-main-module). The difference is that if the main module changes at
runtime, [`require.main`](33-modules.md#accessing-the-main-module) may still refer to the original main module in
modules that were required before the change occurred. Generally, it's
safe to assume that the two refer to the same module.

As with [`require.main`](33-modules.md#accessing-the-main-module), `process.mainModule` will be `undefined` if there
is no entry script.

## `process.memoryUsage()`

Added in: v0.1.16

**History**

| Version | Changes |
| --- | --- |
| v13.9.0, v12.17.0 | Added `arrayBuffers` to the returned object. |
| v7.2.0 | Added `external` to the returned object. |

- Returns: [`<Object>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Object)
  - `rss` [`<integer>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type)
  - `heapTotal` [`<integer>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type)
  - `heapUsed` [`<integer>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type)
  - `external` [`<integer>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type)
  - `arrayBuffers` [`<integer>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type)

Returns an object describing the memory usage of the Node.js process measured in
bytes.

*ES module version:*

```js
import { memoryUsage } from 'node:process';

console.log(memoryUsage());
// Prints:
// {
//  rss: 4935680,
//  heapTotal: 1826816,
//  heapUsed: 650472,
//  external: 49879,
//  arrayBuffers: 9386
// }
```

*CommonJS version:*

```js
const { memoryUsage } = require('node:process');

console.log(memoryUsage());
// Prints:
// {
//  rss: 4935680,
//  heapTotal: 1826816,
//  heapUsed: 650472,
//  external: 49879,
//  arrayBuffers: 9386
// }
```

- `heapTotal` and `heapUsed` refer to V8's memory usage.
- `external` refers to the memory usage of C++ objects bound to JavaScript
  objects managed by V8.
- `rss`, Resident Set Size, is the amount of space occupied in the main
  memory device (that is a subset of the total allocated memory) for the
  process, including all C++ and JavaScript objects and code.
- `arrayBuffers` refers to memory allocated for `ArrayBuffer`s and
  `SharedArrayBuffer`s, including all Node.js [`Buffer`](07-buffer-part1.md)s.
  This is also included in the `external` value. When Node.js is used as an
  embedded library, this value may be `0` because allocations for `ArrayBuffer`s
  may not be tracked in that case.

When using [`Worker`](67-worker-threads.md#class-worker) threads, `rss` will be a value that is valid for the
entire process, while the other fields will only refer to the current thread.

The `process.memoryUsage()` method iterates over each page to gather
information about memory usage which might be slow depending on the
program memory allocations.

### A note on process memoryUsage

On Linux or other systems where glibc is commonly used, an application may have sustained
`rss` growth despite stable `heapTotal` due to fragmentation caused by the glibc `malloc`
implementation. See [nodejs/node#21973](https://github.com/nodejs/node/issues/21973) on how to switch to an alternative `malloc`
implementation to address the performance issue.

## `process.memoryUsage.rss()`

Added in: v15.6.0, v14.18.0

- Returns: [`<integer>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type)

The `process.memoryUsage.rss()` method returns an integer representing the
Resident Set Size (RSS) in bytes.

The Resident Set Size, is the amount of space occupied in the main
memory device (that is a subset of the total allocated memory) for the
process, including all C++ and JavaScript objects and code.

This is the same value as the `rss` property provided by `process.memoryUsage()`
but `process.memoryUsage.rss()` is faster.

*ES module version:*

```js
import { memoryUsage } from 'node:process';

console.log(memoryUsage.rss());
// 35655680
```

*CommonJS version:*

```js
const { memoryUsage } = require('node:process');

console.log(memoryUsage.rss());
// 35655680
```

## `process.nextTick(callback[, ...args])`

Added in: v0.1.26

**History**

| Version | Changes |
| --- | --- |
| v22.7.0, v20.18.0 | Changed stability to Legacy. |
| v18.0.0 | Passing an invalid callback to the `callback` argument now throws `ERR_INVALID_ARG_TYPE` instead of `ERR_INVALID_CALLBACK`. |
| v1.8.1 | Additional arguments after `callback` are now supported. |

Stability: 3 - Legacy: Use [`queueMicrotask()`](26-globals.md#queuemicrotaskcallback) instead.

- `callback` [`<Function>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Function)
- `...args` [`<any>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#Data_types) Additional arguments to pass when invoking the `callback`

`process.nextTick()` adds `callback` to the "next tick queue". This queue is
fully drained after the current operation on the JavaScript stack runs to
completion and before the event loop is allowed to continue. It's possible to
create an infinite loop if one were to recursively call `process.nextTick()`.
See the [Event Loop](https://nodejs.org/en/learn/asynchronous-work/event-loop-timers-and-nexttick#understanding-processnexttick) guide for more background.

*ES module version:*

```js
import { nextTick } from 'node:process';

console.log('start');
nextTick(() => {
  console.log('nextTick callback');
});
console.log('scheduled');
// Output:
// start
// scheduled
// nextTick callback
```

*CommonJS version:*

```js
const { nextTick } = require('node:process');

console.log('start');
nextTick(() => {
  console.log('nextTick callback');
});
console.log('scheduled');
// Output:
// start
// scheduled
// nextTick callback
```

This is important when developing APIs in order to give users the opportunity
to assign event handlers *after* an object has been constructed but before any
I/O has occurred:

*ES module version:*

```js
import { nextTick } from 'node:process';

function MyThing(options) {
  this.setupOptions(options);

  nextTick(() => {
    this.startDoingStuff();
  });
}

const thing = new MyThing();
thing.getReadyForStuff();

// thing.startDoingStuff() gets called now, not before.
```

*CommonJS version:*

```js
const { nextTick } = require('node:process');

function MyThing(options) {
  this.setupOptions(options);

  nextTick(() => {
    this.startDoingStuff();
  });
}

const thing = new MyThing();
thing.getReadyForStuff();

// thing.startDoingStuff() gets called now, not before.
```

It is very important for APIs to be either 100% synchronous or 100%
asynchronous. Consider this example:

```js
// WARNING!  DO NOT USE!  BAD UNSAFE HAZARD!
function maybeSync(arg, cb) {
  if (arg) {
    cb();
    return;
  }

  fs.stat('file', cb);
}
```

This API is hazardous because in the following case:

```js
const maybeTrue = Math.random() > 0.5;

maybeSync(maybeTrue, () => {
  foo();
});

bar();
```

It is not clear whether `foo()` or `bar()` will be called first.

The following approach is much better:

*ES module version:*

```js
import { nextTick } from 'node:process';

function definitelyAsync(arg, cb) {
  if (arg) {
    nextTick(cb);
    return;
  }

  fs.stat('file', cb);
}
```

*CommonJS version:*

```js
const { nextTick } = require('node:process');

function definitelyAsync(arg, cb) {
  if (arg) {
    nextTick(cb);
    return;
  }

  fs.stat('file', cb);
}
```

### When to use `queueMicrotask()` vs. `process.nextTick()`

The [`queueMicrotask()`](26-globals.md#queuemicrotaskcallback) API is an alternative to `process.nextTick()` that instead of using the
"next tick queue" defers execution of a function using the same microtask queue used to execute the
then, catch, and finally handlers of resolved promises.

Within Node.js, every time the "next tick queue" is drained, the microtask queue
is drained immediately after.

So in CJS modules `process.nextTick()` callbacks are always run before `queueMicrotask()` ones.
However since ESM modules are processed already as part of the microtask queue, there
`queueMicrotask()` callbacks are always executed before `process.nextTick()` ones since Node.js
is already in the process of draining the microtask queue.

*ES module version:*

```js
import { nextTick } from 'node:process';

Promise.resolve().then(() => console.log('resolve'));
queueMicrotask(() => console.log('microtask'));
nextTick(() => console.log('nextTick'));
// Output:
// resolve
// microtask
// nextTick
```

*CommonJS version:*

```js
const { nextTick } = require('node:process');

Promise.resolve().then(() => console.log('resolve'));
queueMicrotask(() => console.log('microtask'));
nextTick(() => console.log('nextTick'));
// Output:
// nextTick
// resolve
// microtask
```

For *most* userland use cases, the `queueMicrotask()` API provides a portable
and reliable mechanism for deferring execution that works across multiple
JavaScript platform environments and should be favored over `process.nextTick()`.
In simple scenarios, `queueMicrotask()` can be a drop-in replacement for
`process.nextTick()`.

```js
console.log('start');
queueMicrotask(() => {
  console.log('microtask callback');
});
console.log('scheduled');
// Output:
// start
// scheduled
// microtask callback
```

One note-worthy difference between the two APIs is that `process.nextTick()`
allows specifying additional values that will be passed as arguments to the
deferred function when it is called. Achieving the same result with
`queueMicrotask()` requires using either a closure or a bound function:

```js
function deferred(a, b) {
  console.log('microtask', a + b);
}

console.log('start');
queueMicrotask(deferred.bind(undefined, 1, 2));
console.log('scheduled');
// Output:
// start
// scheduled
// microtask 3
```

There are minor differences in the way errors raised from within the next tick
queue and microtask queue are handled. Errors thrown within a queued microtask
callback should be handled within the queued callback when possible. If they are
not, the `process.on('uncaughtException')` event handler can be used to capture
and handle the errors.

When in doubt, unless the specific capabilities of `process.nextTick()` are
needed, use `queueMicrotask()`.

## `process.noDeprecation`

Added in: v0.8.0

- Type: [`<boolean>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#boolean_type)

The `process.noDeprecation` property indicates whether the `--no-deprecation`
flag is set on the current Node.js process. See the documentation for
the [`'warning'` event](43-process-part1.md#event-warning) and the
[`emitWarning()` method](43-process-part1.md#processemitwarningwarning-type-code-ctor) for more information about this
flag's behavior.

## `process.permission`

Added in: v20.0.0

- Type: [`<Object>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Object)

This API is available through the [`--permission`](13-cli.md#--permission) or
[`--permission-audit`](13-cli.md#--permission-audit) flags.

`process.permission` is an object whose methods are used to manage permissions
for the current process. Additional documentation is available in the
[Permission Model](42-permissions.md#permission-model).

### `process.permission.has(scope[, reference])`

Added in: v20.0.0

**History**

| Version | Changes |
| --- | --- |
| v26.12.0 | The `reference` argument can be a `URL` or a `Uint8Array`. |

- `scope` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type)
- `reference` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) | [`<URL>`](59-url.md#the-whatwg-url-api) | [`<Uint8Array>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Uint8Array)
- Returns: [`<boolean>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#boolean_type)

Verifies that the process is able to access the given scope and reference.
If no reference is provided, a global scope is assumed, for instance,
`process.permission.has('fs.read')` will check if the process has ALL
file system read permissions.

In audit mode ([`--permission-audit`](13-cli.md#--permission-audit)), this method still returns the actual
permission status, but denied operations will not throw `ERR_ACCESS_DENIED`.

The reference has a meaning based on the provided scope. For example,
the reference when the scope is File System means files and folders.

For File System scopes, a `URL` reference must use the `file:` protocol and is
converted to a path, while a `Uint8Array` reference is treated as the raw bytes
of a path. Other scopes ignore the reference.

The available scopes are:

- `fs` - All File System
- `fs.read` - File System read operations
- `fs.write` - File System write operations
- `child` - Child process spawning operations
- `openssl.store` - Loading keys through OpenSSL STORE loaders
- `worker` - Worker thread spawning operation
- `net` - Network operations
- `inspector` - Inspector operations
- `wasi` - WASI operations
- `addon` - Native addon operations
- `ffi` - Foreign function interface operations

```js
// Check if the process has permission to read the README file
process.permission.has('fs.read', './README.md');
// Check if the process has read permission operations
process.permission.has('fs.read');
```

### `process.permission.drop(scope[, reference])`

Added in: v26.3.0, v24.20.0

**History**

| Version | Changes |
| --- | --- |
| v26.12.0 | The `reference` argument can be a `URL` or a `Uint8Array`. |

Stability: 1.1 - Active Development

- `scope` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type)
- `reference` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) | [`<URL>`](59-url.md#the-whatwg-url-api) | [`<Uint8Array>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Uint8Array)

Drops the specified permission from the current process. This operation is
**irreversible** — once a permission is dropped, it cannot be restored through
any Node.js API.

In audit mode ([`--permission-audit`](13-cli.md#--permission-audit)), dropping a permission takes effect,
but since denied operations do not throw, the impact is limited to changing the
return value of `permission.has()`.

If no reference is provided, the entire scope is dropped. For example,
`process.permission.drop('fs.read')` will revoke ALL file system read
permissions.

When a reference is provided, only the permission for that specific resource
is dropped. For example, `process.permission.drop('fs.read', '/etc/myapp')`
will revoke read access to that directory while keeping other read
permissions intact.

**Important:** You can only drop the exact resource that was explicitly
granted. The reference passed to `drop()` must match the original grant:

- If a permission was granted using a wildcard (`*`), such as
  `--allow-fs-read=*`, individual paths cannot be dropped - only the entire
  scope can be dropped (by calling `drop()` without a reference).
- If a directory was granted (e.g. `--allow-fs-read=/my/folder`), you cannot
  drop access to individual files inside it. You must drop the same directory
  that was granted. Any remaining grants continue to apply.

The available scopes are the same as [`process.permission.has()`](#processpermissionhasscope-reference):

- `fs` - All File System (drops both read and write)
- `fs.read` - File System read operations
- `fs.write` - File System write operations
- `child` - Child process spawning operations
- `openssl.store` - Loading keys through OpenSSL STORE loaders
- `worker` - Worker thread spawning operation
- `net` - Network operations
- `inspector` - Inspector operations
- `wasi` - WASI operations
- `addon` - Native addon operations
- `ffi` - Foreign function interface operations

```js
const fs = require('node:fs');

// Read configuration during startup
const config = fs.readFileSync('/etc/myapp/config.json', 'utf8');

// Drop read access to the config directory after initialization
process.permission.drop('fs.read', '/etc/myapp');

// This will now throw ERR_ACCESS_DENIED
fs.readFileSync('/etc/myapp/config.json');
```

## `process.pid`

Added in: v0.1.15

- Type: [`<integer>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type)

The `process.pid` property returns the PID of the process.

*ES module version:*

```js
import { pid } from 'node:process';

console.log(`This process is pid ${pid}`);
```

*CommonJS version:*

```js
const { pid } = require('node:process');

console.log(`This process is pid ${pid}`);
```

## `process.platform`

Added in: v0.1.16

- Type: [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type)

The `process.platform` property returns a string identifying the operating
system platform for which the Node.js binary was compiled.

Currently possible values are:

- `'aix'`
- `'darwin'`
- `'freebsd'`
- `'linux'`
- `'openbsd'`
- `'sunos'`
- `'win32'`

*ES module version:*

```js
import { platform } from 'node:process';

console.log(`This platform is ${platform}`);
```

*CommonJS version:*

```js
const { platform } = require('node:process');

console.log(`This platform is ${platform}`);
```

The value `'android'` may also be returned if Node.js is built on the
Android operating system. However, Android support in Node.js
[is experimental](https://github.com/nodejs/node/blob/HEAD/BUILDING.md#android).

## `process.ppid`

Added in: v9.2.0, v8.10.0, v6.13.0

- Type: [`<integer>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type)

The `process.ppid` property returns the PID of the parent of the
current process.

*ES module version:*

```js
import { ppid } from 'node:process';

console.log(`The parent process is pid ${ppid}`);
```

*CommonJS version:*

```js
const { ppid } = require('node:process');

console.log(`The parent process is pid ${ppid}`);
```

## `process.ref(maybeRefable)`

Added in: v23.6.0, v22.14.0

**History**

| Version | Changes |
| --- | --- |
| v26.11.0 | This API is no longer experimental. |

- `maybeRefable` [`<any>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#Data_types) An object that may be "refable".

An object is "refable" if it implements the Node.js "Refable protocol".
Specifically, this means that the object implements the `Symbol.for('nodejs.ref')`
and `Symbol.for('nodejs.unref')` methods. "Ref'd" objects will keep the Node.js
event loop alive, while "unref'd" objects will not. Historically, this was
implemented by using `ref()` and `unref()` methods directly on the objects.
This pattern, however, is being deprecated in favor of the "Refable protocol"
in order to better support Web Platform API types whose APIs cannot be modified
to add `ref()` and `unref()` methods but still need to support that behavior.

## `process.release`

Added in: v3.0.0

**History**

| Version | Changes |
| --- | --- |
| v4.2.0 | The `lts` property is now supported. |

- Type: [`<Object>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Object)

The `process.release` property returns an `Object` containing metadata related
to the current release, including URLs for the source tarball and headers-only
tarball.

`process.release` contains the following properties:

- `name` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) A value that will always be `'node'`.
- `sourceUrl` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) an absolute URL pointing to a *`.tar.gz`* file containing
  the source code of the current release.
- `headersUrl`[`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) an absolute URL pointing to a *`.tar.gz`* file containing
  only the source header files for the current release. This file is
  significantly smaller than the full source file and can be used for compiling
  Node.js native add-ons.
- `libUrl` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) | [`<undefined>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#undefined_type) an absolute URL pointing to a *`node.lib`* file
  matching the architecture and version of the current release. This file is
  used for compiling Node.js native add-ons. *This property is only present on
  Windows builds of Node.js and will be missing on all other platforms.*
- `lts` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) | [`<undefined>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#undefined_type) a string label identifying the [LTS](https://github.com/nodejs/Release) label for this
  release. This property only exists for LTS releases and is `undefined` for all
  other release types, including *Current* releases. Valid values include the
  LTS Release code names (including those that are no longer supported).
  - `'Fermium'` for the 14.x LTS line beginning with 14.15.0.
  - `'Gallium'` for the 16.x LTS line beginning with 16.13.0.
  - `'Hydrogen'` for the 18.x LTS line beginning with 18.12.0.
    For other LTS Release code names, see [Node.js Changelog Archive](https://github.com/nodejs/node/blob/HEAD/doc/changelogs/CHANGELOG_ARCHIVE.md)

```json
{
  "name": "node",
  "lts": "Hydrogen",
  "sourceUrl": "https://nodejs.org/download/release/v18.12.0/node-v18.12.0.tar.gz",
  "headersUrl": "https://nodejs.org/download/release/v18.12.0/node-v18.12.0-headers.tar.gz",
  "libUrl": "https://nodejs.org/download/release/v18.12.0/win-x64/node.lib"
}
```

In custom builds from non-release versions of the source tree, only the
`name` property may be present. The additional properties should not be
relied upon to exist.

## `process.report`

Added in: v11.8.0

**History**

| Version | Changes |
| --- | --- |
| v13.12.0, v12.17.0 | This API is no longer experimental. |

- Type: [`<Object>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Object)

`process.report` is an object whose methods are used to generate diagnostic
reports for the current process. Additional documentation is available in the
[report documentation](48-report.md).

### `process.report.compact`

Added in: v13.12.0, v12.17.0

- Type: [`<boolean>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#boolean_type)

Write reports in a compact format, single-line JSON, more easily consumable
by log processing systems than the default multi-line format designed for
human consumption.

*ES module version:*

```js
import { report } from 'node:process';

console.log(`Reports are compact? ${report.compact}`);
```

*CommonJS version:*

```js
const { report } = require('node:process');

console.log(`Reports are compact? ${report.compact}`);
```

### `process.report.directory`

Added in: v11.12.0

**History**

| Version | Changes |
| --- | --- |
| v13.12.0, v12.17.0 | This API is no longer experimental. |

- Type: [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type)

Directory where the report is written. The default value is the empty string,
indicating that reports are written to the current working directory of the
Node.js process.

*ES module version:*

```js
import { report } from 'node:process';

console.log(`Report directory is ${report.directory}`);
```

*CommonJS version:*

```js
const { report } = require('node:process');

console.log(`Report directory is ${report.directory}`);
```

### `process.report.filename`

Added in: v11.12.0

**History**

| Version | Changes |
| --- | --- |
| v13.12.0, v12.17.0 | This API is no longer experimental. |

- Type: [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type)

Filename where the report is written. If set to the empty string, the output
filename will be comprised of a timestamp, PID, and sequence number. The default
value is the empty string.

If the value of `process.report.filename` is set to `'stdout'` or `'stderr'`,
the report is written to the stdout or stderr of the process respectively.

*ES module version:*

```js
import { report } from 'node:process';

console.log(`Report filename is ${report.filename}`);
```

*CommonJS version:*

```js
const { report } = require('node:process');

console.log(`Report filename is ${report.filename}`);
```

### `process.report.getReport([err])`

Added in: v11.8.0

**History**

| Version | Changes |
| --- | --- |
| v13.12.0, v12.17.0 | This API is no longer experimental. |

- `err` [`<Error>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Error) A custom error used for reporting the JavaScript stack.
- Returns: [`<Object>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Object)

Returns a JavaScript Object representation of a diagnostic report for the
running process. The report's JavaScript stack trace is taken from `err`, if
present.

*ES module version:*

```js
import { report } from 'node:process';
import util from 'node:util';

const data = report.getReport();
console.log(data.header.nodejsVersion);

// Similar to process.report.writeReport()
import fs from 'node:fs';
fs.writeFileSync('my-report.log', util.inspect(data), 'utf8');
```

*CommonJS version:*

```js
const { report } = require('node:process');
const util = require('node:util');

const data = report.getReport();
console.log(data.header.nodejsVersion);

// Similar to process.report.writeReport()
const fs = require('node:fs');
fs.writeFileSync('my-report.log', util.inspect(data), 'utf8');
```

Additional documentation is available in the [report documentation](48-report.md).

### `process.report.reportOnFatalError`

Added in: v11.12.0

**History**

| Version | Changes |
| --- | --- |
| v15.0.0, v14.17.0 | This API is no longer experimental. |

- Type: [`<boolean>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#boolean_type)

If `true`, a diagnostic report is generated on fatal errors, such as out of
memory errors or failed C++ assertions.

*ES module version:*

```js
import { report } from 'node:process';

console.log(`Report on fatal error: ${report.reportOnFatalError}`);
```

*CommonJS version:*

```js
const { report } = require('node:process');

console.log(`Report on fatal error: ${report.reportOnFatalError}`);
```

### `process.report.reportOnSignal`

Added in: v11.12.0

**History**

| Version | Changes |
| --- | --- |
| v13.12.0, v12.17.0 | This API is no longer experimental. |

- Type: [`<boolean>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#boolean_type)

If `true`, a diagnostic report is generated when the process receives the
signal specified by `process.report.signal`.

*ES module version:*

```js
import { report } from 'node:process';

console.log(`Report on signal: ${report.reportOnSignal}`);
```

*CommonJS version:*

```js
const { report } = require('node:process');

console.log(`Report on signal: ${report.reportOnSignal}`);
```

### `process.report.reportOnUncaughtException`

Added in: v11.12.0

**History**

| Version | Changes |
| --- | --- |
| v13.12.0, v12.17.0 | This API is no longer experimental. |

- Type: [`<boolean>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#boolean_type)

If `true`, a diagnostic report is generated on uncaught exception.

*ES module version:*

```js
import { report } from 'node:process';

console.log(`Report on exception: ${report.reportOnUncaughtException}`);
```

*CommonJS version:*

```js
const { report } = require('node:process');

console.log(`Report on exception: ${report.reportOnUncaughtException}`);
```

### `process.report.excludeEnv`

Added in: v23.3.0, v22.13.0

- Type: [`<boolean>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#boolean_type)

If `true`, a diagnostic report is generated without the environment variables.

### `process.report.signal`

Added in: v11.12.0

**History**

| Version | Changes |
| --- | --- |
| v13.12.0, v12.17.0 | This API is no longer experimental. |

- Type: [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type)

The signal used to trigger the creation of a diagnostic report. Defaults to
`'SIGUSR2'`.

*ES module version:*

```js
import { report } from 'node:process';

console.log(`Report signal: ${report.signal}`);
```

*CommonJS version:*

```js
const { report } = require('node:process');

console.log(`Report signal: ${report.signal}`);
```

### `process.report.writeReport([filename][, err])`

Added in: v11.8.0

**History**

| Version | Changes |
| --- | --- |
| v13.12.0, v12.17.0 | This API is no longer experimental. |

- `filename` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) Name of the file where the report is written. This
  should be a relative path, that will be appended to the directory specified in `process.report.directory`, or the current working directory of the Node.js
  process, if unspecified.
- `err` [`<Error>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Error) A custom error used for reporting the JavaScript stack.
- Returns: [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) Returns the filename of the generated report.

Writes a diagnostic report to a file. If `filename` is not provided, the default
filename includes the date, time, PID, and a sequence number. The report's
JavaScript stack trace is taken from `err`, if present.

If the value of `filename` is set to `'stdout'` or `'stderr'`, the report is
written to the stdout or stderr of the process respectively.

*ES module version:*

```js
import { report } from 'node:process';

report.writeReport();
```

*CommonJS version:*

```js
const { report } = require('node:process');

report.writeReport();
```

Additional documentation is available in the [report documentation](48-report.md).

## `process.resourceUsage()`

Added in: v12.6.0

- Returns: [`<Object>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Object) the resource usage for the current process. All of these
  values come from the `uv_getrusage` call which returns
  a [`uv_rusage_t` struct](https://docs.libuv.org/en/v1.x/misc.html#c.uv_rusage_t).
  - `userCPUTime` [`<integer>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) maps to `ru_utime` computed in microseconds.
    It is the same value as [`process.cpuUsage().user`](43-process-part1.md#processcpuusagepreviousvalue).
  - `systemCPUTime` [`<integer>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) maps to `ru_stime` computed in microseconds.
    It is the same value as [`process.cpuUsage().system`](43-process-part1.md#processcpuusagepreviousvalue).
  - `maxRSS` [`<integer>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) maps to `ru_maxrss` which is the maximum resident set
    size used in kibibytes (1024 bytes).
  - `sharedMemorySize` [`<integer>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) maps to `ru_ixrss` but is not supported by
    any platform.
  - `unsharedDataSize` [`<integer>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) maps to `ru_idrss` but is not supported by
    any platform.
  - `unsharedStackSize` [`<integer>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) maps to `ru_isrss` but is not supported by
    any platform.
  - `minorPageFault` [`<integer>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) maps to `ru_minflt` which is the number of
    minor page faults for the process, see
    [this article for more details](https://en.wikipedia.org/wiki/Page_fault#Minor).
  - `majorPageFault` [`<integer>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) maps to `ru_majflt` which is the number of
    major page faults for the process, see
    [this article for more details](https://en.wikipedia.org/wiki/Page_fault#Major). This field is not
    supported on Windows.
  - `swappedOut` [`<integer>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) maps to `ru_nswap` but is not supported by any
    platform.
  - `fsRead` [`<integer>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) maps to `ru_inblock` which is the number of times the
    file system had to perform input.
  - `fsWrite` [`<integer>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) maps to `ru_oublock` which is the number of times the
    file system had to perform output.
  - `ipcSent` [`<integer>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) maps to `ru_msgsnd` but is not supported by any
    platform.
  - `ipcReceived` [`<integer>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) maps to `ru_msgrcv` but is not supported by any
    platform.
  - `signalsCount` [`<integer>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) maps to `ru_nsignals` but is not supported by any
    platform.
  - `voluntaryContextSwitches` [`<integer>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) maps to `ru_nvcsw` which is the
    number of times a CPU context switch resulted due to a process voluntarily
    giving up the processor before its time slice was completed (usually to
    await availability of a resource). This field is not supported on Windows.
  - `involuntaryContextSwitches` [`<integer>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) maps to `ru_nivcsw` which is the
    number of times a CPU context switch resulted due to a higher priority
    process becoming runnable or because the current process exceeded its
    time slice. This field is not supported on Windows.

*ES module version:*

```js
import { resourceUsage } from 'node:process';

console.log(resourceUsage());
/*
  Will output:
  {
    userCPUTime: 82872,
    systemCPUTime: 4143,
    maxRSS: 33164,
    sharedMemorySize: 0,
    unsharedDataSize: 0,
    unsharedStackSize: 0,
    minorPageFault: 2469,
    majorPageFault: 0,
    swappedOut: 0,
    fsRead: 0,
    fsWrite: 8,
    ipcSent: 0,
    ipcReceived: 0,
    signalsCount: 0,
    voluntaryContextSwitches: 79,
    involuntaryContextSwitches: 1
  }
*/
```

*CommonJS version:*

```js
const { resourceUsage } = require('node:process');

console.log(resourceUsage());
/*
  Will output:
  {
    userCPUTime: 82872,
    systemCPUTime: 4143,
    maxRSS: 33164,
    sharedMemorySize: 0,
    unsharedDataSize: 0,
    unsharedStackSize: 0,
    minorPageFault: 2469,
    majorPageFault: 0,
    swappedOut: 0,
    fsRead: 0,
    fsWrite: 8,
    ipcSent: 0,
    ipcReceived: 0,
    signalsCount: 0,
    voluntaryContextSwitches: 79,
    involuntaryContextSwitches: 1
  }
*/
```

[Contents](../README.md) | [Previous: Process, part 1 of 3](43-process-part1.md) | [Next: Process, part 3 of 3](43-process-part3.md)
