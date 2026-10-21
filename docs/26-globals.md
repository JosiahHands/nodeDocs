[Contents](../README.md) | [Previous: FFI](25-ffi.md) | [Next: HTTP, part 1 of 3](27-http-part1.md)

Source: [https://nodejs.org/docs/latest/api/globals.html](https://nodejs.org/docs/latest/api/globals.html)

# Global objects

[Stability: 2](01-documentation.md#stability-index) - Stable

These objects are available in all modules.

The following variables may appear to be global but are not. They exist only in
the scope of [CommonJS modules](33-modules.md):

- [`__dirname`](33-modules.md#__dirname)
- [`__filename`](33-modules.md#__filename)
- [`exports`](33-modules.md#exports)
- [`module`](33-modules.md#module)
- [`require()`](33-modules.md#requireid)

The objects listed here are specific to Node.js. There are [built-in objects](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects)
that are part of the JavaScript language itself, which are also globally
accessible.

## `__dirname`

This variable may appear to be global but is not. See [`__dirname`](33-modules.md#__dirname).

## `__filename`

This variable may appear to be global but is not. See [`__filename`](33-modules.md#__filename).

## Class: `AbortController`

Added in: v15.0.0, v14.17.0

**History**

| Version | Changes |
| --- | --- |
| v15.4.0 | No longer experimental. |

A utility class used to signal cancelation in selected `Promise`-based APIs.
The API is based on the Web API [`<AbortController>`](26-globals.md#class-abortcontroller).

```js
const ac = new AbortController();

ac.signal.addEventListener('abort', () => console.log('Aborted!'),
                           { once: true });

ac.abort();

console.log(ac.signal.aborted);  // Prints true
```

### `abortController.abort([reason])`

Added in: v15.0.0, v14.17.0

**History**

| Version | Changes |
| --- | --- |
| v17.2.0, v16.14.0 | Added the new optional reason argument. |

- `reason` [`<any>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#Data_types) An optional reason, retrievable on the `AbortSignal`'s
  `reason` property.

Triggers the abort signal, causing the `abortController.signal` to emit
the `'abort'` event.

### `abortController.signal`

Added in: v15.0.0, v14.17.0

- Type: [`<AbortSignal>`](26-globals.md#class-abortsignal)

## Class: `AbortSignal`

Added in: v15.0.0, v14.17.0

- Extends: [`<EventTarget>`](23-events.md#class-eventtarget)

The `AbortSignal` is used to notify observers when the
`abortController.abort()` method is called.

### Static method: `AbortSignal.abort([reason])`

Added in: v15.12.0, v14.17.0

**History**

| Version | Changes |
| --- | --- |
| v17.2.0, v16.14.0 | Added the new optional reason argument. |

- `reason` [`<any>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#Data_types)
- Returns: [`<AbortSignal>`](26-globals.md#class-abortsignal)

Returns a new already aborted `AbortSignal`.

### Static method: `AbortSignal.timeout(delay)`

Added in: v17.3.0, v16.14.0

- `delay` [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) The number of milliseconds to wait before triggering
  the AbortSignal.

Returns a new `AbortSignal` which will be aborted in `delay` milliseconds.

### Static method: `AbortSignal.any(signals)`

Added in: v20.3.0, v18.17.0

- `signals` [`<Iterable>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Iteration_protocols#the_iterable_protocol) An iterable of [`<AbortSignal>`](26-globals.md#class-abortsignal)s from which to compose a new
  [`<AbortSignal>`](26-globals.md#class-abortsignal).

Returns a new `AbortSignal` which will be aborted if any of the provided
signals are aborted. Its [`abortSignal.reason`](#abortsignalreason) will be set to whichever
one of the `signals` caused it to be aborted.

### Event: `'abort'`

Added in: v15.0.0, v14.17.0

The `'abort'` event is emitted when the `abortController.abort()` method
is called. The callback is invoked with a single object argument with a
single `type` property set to `'abort'`:

```js
const ac = new AbortController();

// Use either the onabort property...
ac.signal.onabort = () => console.log('aborted!');

// Or the EventTarget API...
ac.signal.addEventListener('abort', (event) => {
  console.log(event.type);  // Prints 'abort'
}, { once: true });

ac.abort();
```

The `AbortController` with which the `AbortSignal` is associated will only
ever trigger the `'abort'` event once. We recommended that code check
that the `abortSignal.aborted` attribute is `false` before adding an `'abort'`
event listener.

Any event listeners attached to the `AbortSignal` should use the
`{ once: true }` option (or, if using the `EventEmitter` APIs to attach a
listener, use the `once()` method) to ensure that the event listener is
removed as soon as the `'abort'` event is handled. Failure to do so may
result in memory leaks.

### `abortSignal.aborted`

Added in: v15.0.0, v14.17.0

- Type: [`<boolean>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#boolean_type)

True after the `AbortController` has been aborted.

### `abortSignal.onabort`

Added in: v15.0.0, v14.17.0

- Type: [`<Function>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Function)

An optional callback function that may be set by user code to be notified
when the `abortController.abort()` function has been called.

### `abortSignal.reason`

Added in: v17.2.0, v16.14.0

- Type: [`<any>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#Data_types)

An optional reason specified when the `AbortSignal` was triggered.

```js
const ac = new AbortController();
ac.abort(new Error('boom!'));
console.log(ac.signal.reason);  // Error: boom!
```

### `abortSignal.throwIfAborted()`

Added in: v17.3.0, v16.17.0

If `abortSignal.aborted` is `true`, throws `abortSignal.reason`.

## `atob(data)`

Added in: v16.0.0

Stability: 3 - Legacy. Use `Buffer.from(data, 'base64')` instead.

Global alias for [`buffer.atob()`](07-buffer-part3.md#bufferatobdata).

An automated migration is available ([source](https://github.com/nodejs/userland-migrations/tree/main/recipes/buffer-atob-btoa)):

```bash
npx codemod@latest @nodejs/buffer-atob-btoa
```

## Class: `Blob`

Added in: v18.0.0

See [`<Blob>`](07-buffer-part1.md#class-blob).

## Class: `BroadcastChannel`

Added in: v18.0.0

See [`<BroadcastChannel>`](67-worker-threads.md#class-broadcastchannel-extends-eventtarget).

## `btoa(data)`

Added in: v16.0.0

Stability: 3 - Legacy. Use `buf.toString('base64')` instead.

Global alias for [`buffer.btoa()`](07-buffer-part3.md#bufferbtoadata).

An automated migration is available ([source](https://github.com/nodejs/userland-migrations/tree/main/recipes/buffer-atob-btoa)):

```bash
npx codemod@latest @nodejs/buffer-atob-btoa
```

## Class: `Buffer`

Added in: v0.1.103

- Type: [`<Function>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Function)

Used to handle binary data. See the [buffer section](07-buffer-part1.md).

## Class: `ByteLengthQueuingStrategy`

Added in: v18.0.0

**History**

| Version | Changes |
| --- | --- |
| v23.11.0, v22.15.0 | Marking the API stable. |

A browser-compatible implementation of [`ByteLengthQueuingStrategy`](66-webstreams.md#class-bytelengthqueuingstrategy).

## `clearImmediate(immediateObject)`

Added in: v0.9.1

[`clearImmediate`](54-timers.md#clearimmediateimmediate) is described in the [timers](54-timers.md) section.

## `clearInterval(intervalObject)`

Added in: v0.0.1

[`clearInterval`](54-timers.md#clearintervaltimeout) is described in the [timers](54-timers.md) section.

## `clearTimeout(timeoutObject)`

Added in: v0.0.1

[`clearTimeout`](54-timers.md#cleartimeouttimeout) is described in the [timers](54-timers.md) section.

## Class: `CloseEvent`

Added in: v23.0.0

A browser-compatible implementation of [`<CloseEvent>`](https://developer.mozilla.org/docs/Web/API/CloseEvent). Disable this API
with the [`--no-experimental-websocket`](13-cli.md#--no-experimental-websocket) CLI flag.

## Class: `CompressionStream`

Added in: v18.0.0

**History**

| Version | Changes |
| --- | --- |
| v24.7.0, v22.20.0 | format now accepts `brotli` value. |
| v23.11.0, v22.15.0 | Marking the API stable. |

A browser-compatible implementation of [`CompressionStream`](66-webstreams.md#class-compressionstream).

## `console`

Added in: v0.1.100

- Type: [`<Object>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Object)

Used to print to stdout and stderr. See the [`console`](14-console.md) section.

## Class: `CountQueuingStrategy`

Added in: v18.0.0

**History**

| Version | Changes |
| --- | --- |
| v23.11.0, v22.15.0 | Marking the API stable. |

A browser-compatible implementation of [`CountQueuingStrategy`](66-webstreams.md#class-countqueuingstrategy).

## Class: `Crypto`

Added in: v17.6.0, v16.15.0

**History**

| Version | Changes |
| --- | --- |
| v23.0.0 | No longer experimental. |
| v19.0.0 | No longer behind `--experimental-global-webcrypto` CLI flag. |

A browser-compatible implementation of [`<Crypto>`](65-webcrypto.md#class-crypto). This global is available
only if the Node.js binary was compiled with including support for the `node:crypto` module.

## `crypto`

Added in: v17.6.0, v16.15.0

**History**

| Version | Changes |
| --- | --- |
| v23.0.0 | No longer experimental. |
| v19.0.0 | No longer behind `--experimental-global-webcrypto` CLI flag. |

A browser-compatible implementation of the [Web Crypto API](65-webcrypto.md).

## Class: `CryptoKey`

Added in: v17.6.0, v16.15.0

**History**

| Version | Changes |
| --- | --- |
| v23.0.0 | No longer experimental. |
| v19.0.0 | No longer behind `--experimental-global-webcrypto` CLI flag. |

A browser-compatible implementation of [`<CryptoKey>`](65-webcrypto.md#class-cryptokey). This global is available
only if the Node.js binary was compiled with including support for the `node:crypto` module.

## Class: `CustomEvent`

Added in: v18.7.0, v16.17.0

**History**

| Version | Changes |
| --- | --- |
| v23.0.0 | No longer experimental. |
| v22.1.0, v20.13.0 | CustomEvent is now stable. |
| v19.0.0 | No longer behind `--experimental-global-customevent` CLI flag. |

A browser-compatible implementation of [`<CustomEvent>`](23-events.md#class-customevent).

## Class: `DecompressionStream`

Added in: v18.0.0

**History**

| Version | Changes |
| --- | --- |
| v24.7.0, v22.20.0 | format now accepts `brotli` value. |
| v23.11.0, v22.15.0 | Marking the API stable. |

A browser-compatible implementation of [`DecompressionStream`](66-webstreams.md#class-decompressionstream).

## Class: `DOMException`

Added in: v17.0.0

The WHATWG [`<DOMException>`](https://developer.mozilla.org/docs/Web/API/DOMException) class.

## `ErrorEvent`

Added in: v25.0.0

A browser-compatible implementation of [`<ErrorEvent>`](https://developer.mozilla.org/docs/Web/API/ErrorEvent).

## Class: `Event`

Added in: v15.0.0

**History**

| Version | Changes |
| --- | --- |
| v15.4.0 | No longer experimental. |

A browser-compatible implementation of the `Event` class. See
[`EventTarget` and `Event` API](23-events.md#eventtarget-and-event-api) for more details.

## Class: `EventSource`

Added in: v22.3.0, v20.18.0

Stability: 1 - Experimental. Enable this API with the [`--experimental-eventsource`](13-cli.md#--experimental-eventsource)
CLI flag.

A browser-compatible implementation of [`<EventSource>`](https://developer.mozilla.org/docs/Web/API/EventSource).

## Class: `EventTarget`

Added in: v15.0.0

**History**

| Version | Changes |
| --- | --- |
| v15.4.0 | No longer experimental. |

A browser-compatible implementation of the `EventTarget` class. See
[`EventTarget` and `Event` API](23-events.md#eventtarget-and-event-api) for more details.

## `exports`

This variable may appear to be global but is not. See [`exports`](33-modules.md#exports).

## `fetch`

Added in: v17.5.0, v16.15.0

**History**

| Version | Changes |
| --- | --- |
| v21.0.0 | No longer experimental. |
| v18.0.0 | No longer behind `--experimental-fetch` CLI flag. |

A browser-compatible implementation of the [`fetch()`](https://developer.mozilla.org/en-US/docs/Web/API/Window/fetch) function.

```js
const res = await fetch('https://nodejs.org/api/documentation.json');
if (res.ok) {
  const data = await res.json();
  console.log(data);
}
```

The implementation is based upon [undici](https://undici.nodejs.org), an HTTP/1.1 client
written from scratch for Node.js. You can figure out which version of `undici` is bundled
in your Node.js process reading the `process.versions.undici` property.

### Custom dispatcher

You can use a custom dispatcher to dispatch requests passing it in fetch's options object.
The dispatcher must be compatible with `undici`'s
[`Dispatcher` class](https://undici.nodejs.org/api/Dispatcher).

```js
fetch(url, { dispatcher: new MyAgent() });
```

It is possible to change the global dispatcher in Node.js by installing `undici` and using
the `setGlobalDispatcher()` method. Calling this method will affect both `undici` and
Node.js.

```js
import { setGlobalDispatcher } from 'undici';
setGlobalDispatcher(new MyAgent());
```

### Related classes

The following globals are available to use with `fetch`:

- [`FormData`](#class-formdata)
- [`Headers`](#class-headers)
- [`Request`](#class-request)
- [`Response`](#class-response)

## Class: `File`

Added in: v20.0.0

See [`<File>`](07-buffer-part3.md#class-file).

## Class: `FormData`

Added in: v17.6.0, v16.15.0

**History**

| Version | Changes |
| --- | --- |
| v21.0.0 | No longer experimental. |
| v18.0.0 | No longer behind `--experimental-fetch` CLI flag. |

A browser-compatible implementation of [`<FormData>`](https://developer.mozilla.org/docs/Web/API/FormData).

## `global`

Added in: v0.1.27

Stability: 3 - Legacy. Use [`globalThis`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/globalThis) instead.

- Type: [`<Object>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Object) The global namespace object.

In browsers, the top-level scope has traditionally been the global scope. This
means that `var something` will define a new global variable, except within
ECMAScript modules. In Node.js, this is different. The top-level scope is not
the global scope; `var something` inside a Node.js module will be local to that
module, regardless of whether it is a [CommonJS module](33-modules.md) or an
[ECMAScript module](34-esm.md).

## Class: `Headers`

Added in: v17.5.0, v16.15.0

**History**

| Version | Changes |
| --- | --- |
| v21.0.0 | No longer experimental. |
| v18.0.0 | No longer behind `--experimental-fetch` CLI flag. |

A browser-compatible implementation of [`<Headers>`](https://developer.mozilla.org/docs/Web/API/Headers).

## `localStorage`

Added in: v22.4.0

**History**

| Version | Changes |
| --- | --- |
| v26.0.0 | Accessing the `localStorage` global without providing `--localstorage-file` now throws a `DOMException`, for compliance with the Web Storage specification. |
| v25.0.0 | When webstorage is enabled and `--localstorage-file` is not provided, accessing the `localStorage` global now returns an empty object. |
| v25.0.0 | This API is no longer behind `--experimental-webstorage` runtime flag. |

Stability: 1.2 - Release candidate. Disable this API with [`--no-experimental-webstorage`](13-cli.md#--no-experimental-webstorage).

A browser-compatible implementation of [`localStorage`](https://developer.mozilla.org/en-US/docs/Web/API/Window/localStorage). Data is stored
unencrypted in the file specified by the [`--localstorage-file`](13-cli.md#--localstorage-filefile) CLI flag.
The maximum amount of data that can be stored is 10 MB.
Any modification of this data outside of the Web Storage API is not supported.
`localStorage` data is not stored per user or per request when used in the context
of a server, it is shared across all users and requests.

## Class: `MessageChannel`

Added in: v15.0.0

The `MessageChannel` class. See [`MessageChannel`](67-worker-threads.md#class-messagechannel) for more details.

## Class: `MessageEvent`

Added in: v15.0.0

A browser-compatible implementation of [`<MessageEvent>`](https://developer.mozilla.org/docs/Web/API/MessageEvent).

## Class: `MessagePort`

Added in: v15.0.0

The `MessagePort` class. See [`MessagePort`](67-worker-threads.md#class-messageport) for more details.

## `module`

This variable may appear to be global but is not. See [`module`](33-modules.md#module).

## Class: `Navigator`

Added in: v21.0.0

Stability: 1.1 - Active development. Disable this API with the
[`--no-experimental-global-navigator`](13-cli.md#--no-experimental-global-navigator) CLI flag.

A partial implementation of the [Navigator API](https://html.spec.whatwg.org/multipage/system-state.html#the-navigator-object).

## `navigator`

Added in: v21.0.0

Stability: 1.1 - Active development. Disable this API with the
[`--no-experimental-global-navigator`](13-cli.md#--no-experimental-global-navigator) CLI flag.

A partial implementation of [`window.navigator`](https://developer.mozilla.org/en-US/docs/Web/API/Window/navigator).

### `navigator.hardwareConcurrency`

Added in: v21.0.0

- Type: [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type)

The `navigator.hardwareConcurrency` read-only property returns the number of
logical processors available to the current Node.js instance.

```js
console.log(`This process is running on ${navigator.hardwareConcurrency} logical processors`);
```

### `navigator.language`

Added in: v21.2.0

- Type: [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type)

The `navigator.language` read-only property returns a string representing the
preferred language of the Node.js instance. The language will be determined by
the ICU library used by Node.js at runtime based on the
default language of the operating system.

The value is representing the language version as defined in [RFC 5646](https://www.rfc-editor.org/rfc/rfc5646.txt).

The fallback value on builds without ICU is `'en-US'`.

```js
console.log(`The preferred language of the Node.js instance has the tag '${navigator.language}'`);
```

### `navigator.languages`

Added in: v21.2.0

- Type: [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type)[]

The `navigator.languages` read-only property returns an array of strings
representing the preferred languages of the Node.js instance.
By default `navigator.languages` contains only the value of
`navigator.language`, which will be determined by the ICU library used by
Node.js at runtime based on the default language of the operating system.

The fallback value on builds without ICU is `['en-US']`.

```js
console.log(`The preferred languages are '${navigator.languages}'`);
```

### `navigator.locks`

Added in: v24.5.0

Stability: 1 - Experimental

The `navigator.locks` read-only property returns a [`LockManager`](67-worker-threads.md#class-lockmanager) instance that
can be used to coordinate access to resources that may be shared across multiple
threads within the same process. This global implementation matches the semantics
of the [browser `LockManager`](https://developer.mozilla.org/en-US/docs/Web/API/LockManager) API.

*ES module version:*

```js
// Request an exclusive lock
await navigator.locks.request('my_resource', async (lock) => {
  // The lock has been acquired.
  console.log(`Lock acquired: ${lock.name}`);
  // Lock is automatically released when the function returns
});

// Request a shared lock
await navigator.locks.request('shared_resource', { mode: 'shared' }, async (lock) => {
  // Multiple shared locks can be held simultaneously
  console.log(`Shared lock acquired: ${lock.name}`);
});
```

*CommonJS version:*

```js
// Request an exclusive lock
navigator.locks.request('my_resource', async (lock) => {
  // The lock has been acquired.
  console.log(`Lock acquired: ${lock.name}`);
  // Lock is automatically released when the function returns
}).then(() => {
  console.log('Lock released');
});

// Request a shared lock
navigator.locks.request('shared_resource', { mode: 'shared' }, async (lock) => {
  // Multiple shared locks can be held simultaneously
  console.log(`Shared lock acquired: ${lock.name}`);
}).then(() => {
  console.log('Shared lock released');
});
```

See [`worker_threads.locks`](67-worker-threads.md#worker_threadslocks) for detailed API documentation.

### `navigator.platform`

Added in: v21.2.0

- Type: [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type)

The `navigator.platform` read-only property returns a string identifying the
platform on which the Node.js instance is running.

```js
console.log(`This process is running on ${navigator.platform}`);
```

### `navigator.userAgent`

Added in: v21.1.0

- Type: [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type)

The `navigator.userAgent` read-only property returns user agent
consisting of the runtime name and major version number.

```js
console.log(`The user-agent is ${navigator.userAgent}`); // Prints "Node.js/21"
```

## `performance`

Added in: v16.0.0

The [`perf_hooks.performance`](41-perf-hooks.md#perf_hooksperformance) object.

## Class: `PerformanceEntry`

Added in: v19.0.0

The `PerformanceEntry` class. See [`PerformanceEntry`](41-perf-hooks.md#class-performanceentry) for more details.

## Class: `PerformanceMark`

Added in: v19.0.0

The `PerformanceMark` class. See [`PerformanceMark`](41-perf-hooks.md#class-performancemark) for more details.

## Class: `PerformanceMeasure`

Added in: v19.0.0

The `PerformanceMeasure` class. See [`PerformanceMeasure`](41-perf-hooks.md#class-performancemeasure) for more details.

## Class: `PerformanceObserver`

Added in: v19.0.0

The `PerformanceObserver` class. See [`PerformanceObserver`](41-perf-hooks.md#class-performanceobserver) for more details.

## Class: `PerformanceObserverEntryList`

Added in: v19.0.0

The `PerformanceObserverEntryList` class. See
[`PerformanceObserverEntryList`](41-perf-hooks.md#class-performanceobserverentrylist) for more details.

## Class: `PerformanceResourceTiming`

Added in: v19.0.0

The `PerformanceResourceTiming` class. See [`PerformanceResourceTiming`](41-perf-hooks.md#class-performanceresourcetiming) for
more details.

## `process`

Added in: v0.1.7

- Type: [`<Object>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Object)

The process object. See the [`process` object](43-process-part1.md#process) section.

## `queueMicrotask(callback)`

Added in: v11.0.0

- `callback` [`<Function>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Function) Function to be queued.

The `queueMicrotask()` method queues a microtask to invoke `callback`. If
`callback` throws an exception, the [`process` object](43-process-part1.md#process) `'uncaughtException'`
event will be emitted.

The microtask queue is managed by V8 and may be used in a similar manner to
the [`process.nextTick()`](43-process-part2.md#processnexttickcallback-args) queue, which is managed by Node.js. The
`process.nextTick()` queue is always processed before the microtask queue
within each turn of the Node.js event loop.

```js
// Here, `queueMicrotask()` is used to ensure the 'load' event is always
// emitted asynchronously, and therefore consistently. Using
// `process.nextTick()` here would result in the 'load' event always emitting
// before any other promise jobs.

DataHandler.prototype.load = async function load(key) {
  const hit = this._cache.get(key);
  if (hit !== undefined) {
    queueMicrotask(() => {
      this.emit('load', hit);
    });
    return;
  }

  const data = await fetchData(key);
  this._cache.set(key, data);
  this.emit('load', data);
};
```

## Class: `QuotaExceededError`

Added in: v26.0.0

The WHATWG [`<QuotaExceededError>`](https://developer.mozilla.org/docs/Web/API/QuotaExceededError) class. Extends [`<DOMException>`](https://developer.mozilla.org/docs/Web/API/DOMException).

## Class: `ReadableByteStreamController`

Added in: v18.0.0

**History**

| Version | Changes |
| --- | --- |
| v23.11.0, v22.15.0 | Marking the API stable. |

A browser-compatible implementation of [`ReadableByteStreamController`](66-webstreams.md#class-readablebytestreamcontroller).

## Class: `ReadableStream`

Added in: v18.0.0

**History**

| Version | Changes |
| --- | --- |
| v23.11.0, v22.15.0 | Marking the API stable. |

A browser-compatible implementation of [`ReadableStream`](66-webstreams.md#class-readablestream).

## Class: `ReadableStreamBYOBReader`

Added in: v18.0.0

**History**

| Version | Changes |
| --- | --- |
| v23.11.0, v22.15.0 | Marking the API stable. |

A browser-compatible implementation of [`ReadableStreamBYOBReader`](66-webstreams.md#class-readablestreambyobreader).

## Class: `ReadableStreamBYOBRequest`

Added in: v18.0.0

**History**

| Version | Changes |
| --- | --- |
| v23.11.0, v22.15.0 | Marking the API stable. |

A browser-compatible implementation of [`ReadableStreamBYOBRequest`](66-webstreams.md#class-readablestreambyobrequest).

## Class: `ReadableStreamDefaultController`

Added in: v18.0.0

**History**

| Version | Changes |
| --- | --- |
| v23.11.0, v22.15.0 | Marking the API stable. |

A browser-compatible implementation of [`ReadableStreamDefaultController`](66-webstreams.md#class-readablestreamdefaultcontroller).

## Class: `ReadableStreamDefaultReader`

Added in: v18.0.0

**History**

| Version | Changes |
| --- | --- |
| v23.11.0, v22.15.0 | Marking the API stable. |

A browser-compatible implementation of [`ReadableStreamDefaultReader`](66-webstreams.md#class-readablestreamdefaultreader).

## Class: `Request`

Added in: v17.5.0, v16.15.0

**History**

| Version | Changes |
| --- | --- |
| v21.0.0 | No longer experimental. |
| v18.0.0 | No longer behind `--experimental-fetch` CLI flag. |

A browser-compatible implementation of [`<Request>`](https://developer.mozilla.org/docs/Web/API/Request).

## `require()`

This variable may appear to be global but is not. See [`require()`](33-modules.md#requireid).

## Class: `Response`

Added in: v17.5.0, v16.15.0

**History**

| Version | Changes |
| --- | --- |
| v21.0.0 | No longer experimental. |
| v18.0.0 | No longer behind `--experimental-fetch` CLI flag. |

A browser-compatible implementation of [`<Response>`](https://developer.mozilla.org/docs/Web/API/Response).

## `sessionStorage`

Added in: v22.4.0

**History**

| Version | Changes |
| --- | --- |
| v25.0.0 | This API is no longer behind `--experimental-webstorage` runtime flag. |

Stability: 1.2 - Release candidate. Disable this API with [`--no-experimental-webstorage`](13-cli.md#--no-experimental-webstorage).

A browser-compatible implementation of [`sessionStorage`](https://developer.mozilla.org/en-US/docs/Web/API/Window/sessionStorage). Data is stored in
memory, with a storage quota of 10 MB. `sessionStorage` data persists only within
the currently running process, and is not shared between workers.

## `setImmediate(callback[, ...args])`

Added in: v0.9.1

[`setImmediate`](54-timers.md#setimmediatecallback-args) is described in the [timers](54-timers.md) section.

## `setInterval(callback, delay[, ...args])`

Added in: v0.0.1

[`setInterval`](54-timers.md#setintervalcallback-delay-args) is described in the [timers](54-timers.md) section.

## `setTimeout(callback, delay[, ...args])`

Added in: v0.0.1

[`setTimeout`](54-timers.md#settimeoutcallback-delay-args) is described in the [timers](54-timers.md) section.

## Class: `Storage`

Added in: v22.4.0

Stability: 1.2 - Release candidate. Disable this API with [`--no-experimental-webstorage`](13-cli.md#--no-experimental-webstorage).

A browser-compatible implementation of [`<Storage>`](https://developer.mozilla.org/docs/Web/API/Storage).

## `structuredClone(value[, options])`

Added in: v17.0.0

The WHATWG [`structuredClone`](https://developer.mozilla.org/en-US/docs/Web/API/Window/structuredClone) method.

## Class: `SubtleCrypto`

Added in: v17.6.0, v16.15.0

**History**

| Version | Changes |
| --- | --- |
| v19.0.0 | No longer behind `--experimental-global-webcrypto` CLI flag. |

A browser-compatible implementation of [`<SubtleCrypto>`](65-webcrypto.md#class-subtlecrypto). This global is available
only if the Node.js binary was compiled with including support for the `node:crypto` module.

## Class: `TextDecoder`

Added in: v11.0.0

The WHATWG `TextDecoder` class. See the [`TextDecoder`](60-util-part2.md#class-utiltextdecoder) section.

## Class: `TextDecoderStream`

Added in: v18.0.0

**History**

| Version | Changes |
| --- | --- |
| v23.11.0, v22.15.0 | Marking the API stable. |

A browser-compatible implementation of [`TextDecoderStream`](66-webstreams.md#class-textdecoderstream).

## Class: `TextEncoder`

Added in: v11.0.0

The WHATWG `TextEncoder` class. See the [`TextEncoder`](60-util-part2.md#class-utiltextencoder) section.

## Class: `TextEncoderStream`

Added in: v18.0.0

**History**

| Version | Changes |
| --- | --- |
| v23.11.0, v22.15.0 | Marking the API stable. |

A browser-compatible implementation of [`TextEncoderStream`](66-webstreams.md#class-textencoderstream).

## Class: `TransformStream`

Added in: v18.0.0

**History**

| Version | Changes |
| --- | --- |
| v23.11.0, v22.15.0 | Marking the API stable. |

A browser-compatible implementation of [`TransformStream`](66-webstreams.md#class-transformstream).

## Class: `TransformStreamDefaultController`

Added in: v18.0.0

**History**

| Version | Changes |
| --- | --- |
| v23.11.0, v22.15.0 | Marking the API stable. |

A browser-compatible implementation of [`TransformStreamDefaultController`](66-webstreams.md#class-transformstreamdefaultcontroller).

## Class: `URL`

Added in: v10.0.0

The WHATWG `URL` class. See the [`URL`](59-url.md#class-url) section.

## Class: `URLPattern`

Added in: v24.0.0

Stability: 1 - Experimental

The WHATWG `URLPattern` class. See the [`URLPattern`](59-url.md#class-urlpattern) section.

## Class: `URLSearchParams`

Added in: v10.0.0

The WHATWG `URLSearchParams` class. See the [`URLSearchParams`](59-url.md#class-urlsearchparams) section.

## Class: `WebAssembly`

Added in: v8.0.0

- Type: [`<Object>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Object)

The object that acts as the namespace for all W3C
[WebAssembly](https://webassembly.org) related functionality. See the
[Mozilla Developer Network](https://developer.mozilla.org/en-US/docs/WebAssembly) for usage and compatibility.

## Class: `WebSocket`

Added in: v21.0.0, v20.10.0

**History**

| Version | Changes |
| --- | --- |
| v22.4.0 | No longer experimental. |
| v22.0.0 | No longer behind `--experimental-websocket` CLI flag. |

A browser-compatible implementation of [`<WebSocket>`](https://developer.mozilla.org/docs/Web/API/WebSocket). Disable this API
with the [`--no-experimental-websocket`](13-cli.md#--no-experimental-websocket) CLI flag.

## Class: `Worker`

Added in: v26.9.0

Stability: 1 - Experimental. Enable this API with the
[`--experimental-web-worker`](13-cli.md#--experimental-web-worker) CLI flag.

A mostly browser-compatible implementation of Web Workers of the [HTML Standard](https://html.spec.whatwg.org/multipage/workers.html),
implemented on top of [`node:worker_threads`](67-worker-threads.md). Threads created with it
are given the [`<DedicatedWorkerGlobalScope>`](https://developer.mozilla.org/docs/Web/API/DedicatedWorkerGlobalScope) API (`self`,
`name`, `location`, `navigator`, `postMessage()`, `close()`, and
`importScripts()`), in addition to the usual Node.js globals, such as `process`.

```js
// worker.js
addEventListener('message', (event) => {
  postMessage(`${event.data} from ${name}!`);
});
```

```js
// main.js
const worker = new Worker('./worker.js', { name: 'greeter' });

worker.addEventListener('message', (event) => {
  console.log(event.data); // Prints: Hello from greeter!
  worker.terminate();
});

worker.postMessage('Hello');
```

Because their lifetime and sharing model depend on origins and
browsing contexts, Node.js does not currently implement `SharedWorker`.

### Loading worker scripts

Worker scripts are read synchronously from the local file system or from
memory rather than fetched over the network, which changes which URLs are
accepted and how failures are reported:

- `new Worker()` and `importScripts()` accept only `file:`, `data:`, and
  `blob:` URLs. Any other scheme makes `new Worker()` throw a
  `NotSupportedError` and `importScripts()` throw a `NetworkError`.
- A script that cannot be read makes `importScripts()` throw a `NetworkError`;
  for `new Worker()` it fires an `error` event at the `Worker` object.
- Redirects, the `nosniff` check, and HTTP MIME type validation do not apply.
  MIME types are validated only for `data:` and `blob:` URLs. The
  `credentials` option is validated for API compatibility but has no effect,
  since no network request is made.
- On the main thread, relative script URLs are resolved against the current
  working directory, because there is no document base URL. Within a worker
  they are resolved against the worker's own URL (as is done in the spec).
- For `blob:` URLs, the script must be held in memory, so blobs backed by a file,
  such as those returned by [`fs.openAsBlob()`](24-fs-part4.md#fsopenasblobpath-options), cannot be used.

[Type stripping](37-typescript.md#type-stripping) only applies to module workers loaded from
`file:` URLs. The `type` option, not the file extension, decides how an entry is
run, so a `.cts` entry is still evaluated as an ES module.

### Differences from the HTML Standard

Besides script loading, mentioned above:

- Node.js has no origin model, so same-origin and cross-origin distinctions do
  not exist and `location.origin` is `'null'` for every supported scheme.
- `close()` terminates the worker immediately instead of following the
  specification's "closing flag" algorithm, so code remaining in the current
  task after `close()` is not executed.
- The worker global is the normal Node.js global object with
  `DedicatedWorkerGlobalScope` inserted into its prototype chain, rather than
  a fresh global created from the interface. Node.js globals such as
  `process`, `Buffer`, and `require()` remain available to worker scripts.
- `ErrorEvent`s dispatched at `Worker` instances include `message` and
  `error`, but `filename`, `lineno`, and `colno` are always `''`, `0`, and
  `0`. An uncaught exception terminates the worker thread, and an unhandled
  `error` event is not propagated further: it neither reaches the parent's
  global scope nor affects the exit code of the process.
- The following [`<WorkerGlobalScope>`](https://developer.mozilla.org/docs/Web/API/WorkerGlobalScope) events are never dispatched, although
  their handler properties exist: `languagechange`, `online`, and `offline`,
  since these concepts do not exist in Node.js; `rejectionhandled` and
  `unhandledrejection`, since Node.js exposes the equivalent does not
  implement the `PromiseRejectionEvent` interface or the per-rejection
  `preventDefault()` behavior required by the HTML Standard.
- Module workers loaded from `file:` URLs support [type stripping](37-typescript.md#type-stripping).

### Web Workers and `node:worker_threads`

Every Web Worker is backed by a [`node:worker_threads`](67-worker-threads.md) [`<Worker>`](67-worker-threads.md#class-worker), so the
two APIs share their threading, structured clone, and transfer semantics.
Inside a worker, [`worker_threads.parentPort`][] is the port behind
`self.postMessage()` and the worker's `message` events, `isMainThread` is
`false`, and `workerData` is `undefined`.

Web Workers, like `node:worker_threads` workers, keep the event loop alive by
default. In Node.js, Web Workers implement the [Refable protocol](43-process-part2.md#processrefmayberefable), and can be
ref'd and unref'd using `process.ref(worker)` and `process.unref(worker)`.

As a rule of thumb, use [`node:worker_threads`](67-worker-threads.md) directly when a program
needs `workerData`, a custom `env` or `execArgv`, resource limits, stdio
redirection, the `'online'` and `'exit'` events, or `worker.threadId`;
`Worker` accepts only the `name`, `type`, and `credentials` options and,
per the specification, its `terminate()` returns `undefined`, rather than
a promise. Threads started through [`node:worker_threads`](67-worker-threads.md) are ordinary
Node.js threads and do not get the worker global scope APIs.

## Class: `WritableStream`

Added in: v18.0.0

**History**

| Version | Changes |
| --- | --- |
| v23.11.0, v22.15.0 | Marking the API stable. |

A browser-compatible implementation of [`WritableStream`](66-webstreams.md#class-writablestream).

## Class: `WritableStreamDefaultController`

Added in: v18.0.0

**History**

| Version | Changes |
| --- | --- |
| v23.11.0, v22.15.0 | Marking the API stable. |

A browser-compatible implementation of [`WritableStreamDefaultController`](66-webstreams.md#class-writablestreamdefaultcontroller).

## Class: `WritableStreamDefaultWriter`

Added in: v18.0.0

**History**

| Version | Changes |
| --- | --- |
| v23.11.0, v22.15.0 | Marking the API stable. |

A browser-compatible implementation of [`WritableStreamDefaultWriter`](66-webstreams.md#class-writablestreamdefaultwriter).

[Contents](../README.md) | [Previous: FFI](25-ffi.md) | [Next: HTTP, part 1 of 3](27-http-part1.md)
