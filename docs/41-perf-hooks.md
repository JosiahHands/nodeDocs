[Contents](../README.md) | [Previous: Path](40-path.md) | [Next: Permissions](42-permissions.md)

Source: [https://nodejs.org/docs/latest/api/perf_hooks.html](https://nodejs.org/docs/latest/api/perf_hooks.html)

# Performance measurement APIs

[Stability: 2](01-documentation.md#stability-index) - Stable

This module provides an implementation of a subset of the W3C
[Web Performance APIs](https://w3c.github.io/perf-timing-primer/) as well as additional APIs for
Node.js-specific performance measurements.

Node.js supports the following [Web Performance APIs](https://w3c.github.io/perf-timing-primer/):

- [High Resolution Time](https://www.w3.org/TR/hr-time-2)
- [Performance Timeline](https://w3c.github.io/performance-timeline/)
- [User Timing](https://www.w3.org/TR/user-timing/)
- [Resource Timing](https://www.w3.org/TR/resource-timing-2/)

*ES module version:*

```js
import { performance, PerformanceObserver } from 'node:perf_hooks';

const obs = new PerformanceObserver((items) => {
  console.log(items.getEntries()[0].duration);
  performance.clearMarks();
});
obs.observe({ type: 'measure' });
performance.measure('Start to Now');

performance.mark('A');
doSomeLongRunningProcess(() => {
  performance.measure('A to Now', 'A');

  performance.mark('B');
  performance.measure('A to B', 'A', 'B');
});
```

*CommonJS version:*

```js
const { PerformanceObserver, performance } = require('node:perf_hooks');

const obs = new PerformanceObserver((items) => {
  console.log(items.getEntries()[0].duration);
});
obs.observe({ type: 'measure' });
performance.measure('Start to Now');

performance.mark('A');
(async function doSomeLongRunningProcess() {
  await new Promise((r) => setTimeout(r, 5000));
  performance.measure('A to Now', 'A');

  performance.mark('B');
  performance.measure('A to B', 'A', 'B');
})();
```

## `perf_hooks.performance`

Added in: v8.5.0

An object that can be used to collect performance metrics from the current
Node.js instance. It is similar to [`window.performance`](https://developer.mozilla.org/en-US/docs/Web/API/Window/performance) in browsers.

### `performance.clearMarks([name])`

Added in: v8.5.0

**History**

| Version | Changes |
| --- | --- |
| v19.0.0 | This method must be called with the `performance` object as the receiver. |

- `name` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type)

If `name` is not provided, removes all `PerformanceMark` objects from the
Performance Timeline. If `name` is provided, removes only the named mark.

### `performance.clearMeasures([name])`

Added in: v16.7.0

**History**

| Version | Changes |
| --- | --- |
| v19.0.0 | This method must be called with the `performance` object as the receiver. |

- `name` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type)

If `name` is not provided, removes all `PerformanceMeasure` objects from the
Performance Timeline. If `name` is provided, removes only the named measure.

### `performance.clearResourceTimings([name])`

Added in: v18.2.0, v16.17.0

**History**

| Version | Changes |
| --- | --- |
| v19.0.0 | This method must be called with the `performance` object as the receiver. |

- `name` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type)

If `name` is not provided, removes all `PerformanceResourceTiming` objects from
the Resource Timeline. If `name` is provided, removes only the named resource.

### `performance.eventLoopUtilization([utilization1[, utilization2]])`

Added in: v14.10.0, v12.19.0

**History**

| Version | Changes |
| --- | --- |
| v25.2.0, v24.12.0 | Added `perf_hooks.eventLoopUtilization` alias. |

- `utilization1` [`<Object>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Object) The result of a previous call to `eventLoopUtilization()`.
- `utilization2` [`<Object>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Object) The result of a previous call to `eventLoopUtilization()` prior to `utilization1`.
- Returns: [`<Object>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Object)
  - `idle` [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type)
  - `active` [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type)
  - `utilization` [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type)

This is an alias of [`perf_hooks.eventLoopUtilization()`](#perf_hookseventlooputilizationutilization1-utilization2).

*This property is an extension by Node.js. It is not available in Web browsers.*

### `performance.getEntries()`

Added in: v16.7.0

**History**

| Version | Changes |
| --- | --- |
| v19.0.0 | This method must be called with the `performance` object as the receiver. |

- Returns: [`<PerformanceEntry>`](41-perf-hooks.md#class-performanceentry)[]

Returns a list of `PerformanceEntry` objects in chronological order with
respect to `performanceEntry.startTime`. If you are only interested in
performance entries of certain types or that have certain names, see
`performance.getEntriesByType()` and `performance.getEntriesByName()`.

### `performance.getEntriesByName(name[, type])`

Added in: v16.7.0

**History**

| Version | Changes |
| --- | --- |
| v19.0.0 | This method must be called with the `performance` object as the receiver. |

- `name` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type)
- `type` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type)
- Returns: [`<PerformanceEntry>`](41-perf-hooks.md#class-performanceentry)[]

Returns a list of `PerformanceEntry` objects in chronological order
with respect to `performanceEntry.startTime` whose `performanceEntry.name` is
equal to `name`, and optionally, whose `performanceEntry.entryType` is equal to
`type`.

### `performance.getEntriesByType(type)`

Added in: v16.7.0

**History**

| Version | Changes |
| --- | --- |
| v19.0.0 | This method must be called with the `performance` object as the receiver. |

- `type` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type)
- Returns: [`<PerformanceEntry>`](41-perf-hooks.md#class-performanceentry)[]

Returns a list of `PerformanceEntry` objects in chronological order
with respect to `performanceEntry.startTime` whose `performanceEntry.entryType`
is equal to `type`.

### `performance.mark(name[, options])`

Added in: v8.5.0

**History**

| Version | Changes |
| --- | --- |
| v19.0.0 | This method must be called with the `performance` object as the receiver. The name argument is no longer optional. |
| v16.0.0 | Updated to conform to the User Timing Level 3 specification. |

- `name` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type)
- `options` [`<Object>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Object)
  - `detail` [`<any>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#Data_types) Additional optional detail to include with the mark.
  - `startTime` [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) An optional timestamp to be used as the mark time. **Default**: `performance.now()`.

Creates a new `PerformanceMark` entry in the Performance Timeline. A
`PerformanceMark` is a subclass of `PerformanceEntry` whose
`performanceEntry.entryType` is always `'mark'`, and whose
`performanceEntry.duration` is always `0`. Performance marks are used
to mark specific significant moments in the Performance Timeline.

The created `PerformanceMark` entry is put in the global Performance Timeline
and can be queried with `performance.getEntries`,
`performance.getEntriesByName`, and `performance.getEntriesByType`. When the
observation is performed, the entries should be cleared from the global
Performance Timeline manually with `performance.clearMarks`.

### `performance.markResourceTiming(timingInfo, requestedUrl, initiatorType, global, cacheMode, bodyInfo, responseStatus[, deliveryType])`

Added in: v18.2.0, v16.17.0

**History**

| Version | Changes |
| --- | --- |
| v22.2.0 | Added bodyInfo, responseStatus, and deliveryType arguments. |

- `timingInfo` [`<Object>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Object) [Fetch Timing Info](https://fetch.spec.whatwg.org/#fetch-timing-info)
- `requestedUrl` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) The resource url
- `initiatorType` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) The initiator name, e.g: 'fetch'
- `global` [`<Object>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Object)
- `cacheMode` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) The cache mode must be an empty string ('') or 'local'
- `bodyInfo` [`<Object>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Object) [Fetch Response Body Info](https://fetch.spec.whatwg.org/#response-body-info)
- `responseStatus` [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) The response's status code
- `deliveryType` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) The delivery type.  **Default:** `''`.

*This property is an extension by Node.js. It is not available in Web browsers.*

Creates a new `PerformanceResourceTiming` entry in the Resource Timeline. A
`PerformanceResourceTiming` is a subclass of `PerformanceEntry` whose
`performanceEntry.entryType` is always `'resource'`. Performance resources
are used to mark moments in the Resource Timeline.

The created `PerformanceMark` entry is put in the global Resource Timeline
and can be queried with `performance.getEntries`,
`performance.getEntriesByName`, and `performance.getEntriesByType`. When the
observation is performed, the entries should be cleared from the global
Performance Timeline manually with `performance.clearResourceTimings`.

### `performance.measure(name[, startMarkOrOptions[, endMark]])`

Added in: v8.5.0

**History**

| Version | Changes |
| --- | --- |
| v19.0.0 | This method must be called with the `performance` object as the receiver. |
| v16.0.0 | Updated to conform to the User Timing Level 3 specification. |
| v13.13.0, v12.16.3 | Make `startMark` and `endMark` parameters optional. |

- `name` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type)
- `startMarkOrOptions` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) | [`<Object>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Object) Optional.
  - `detail` [`<any>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#Data_types) Additional optional detail to include with the measure.
  - `duration` [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) Duration between start and end times.
  - `end` [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) | [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) Timestamp to be used as the end time, or a string
    identifying a previously recorded mark.
  - `start` [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) | [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) Timestamp to be used as the start time, or a string
    identifying a previously recorded mark.
- `endMark` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) Optional. Must be omitted if `startMarkOrOptions` is an
  [`<Object>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Object).

Creates a new `PerformanceMeasure` entry in the Performance Timeline. A
`PerformanceMeasure` is a subclass of `PerformanceEntry` whose
`performanceEntry.entryType` is always `'measure'`, and whose
`performanceEntry.duration` measures the number of milliseconds elapsed since
`startMark` and `endMark`.

The `startMark` argument may identify any *existing* `PerformanceMark` in the
Performance Timeline, or *may* identify any of the timestamp properties
provided by the `PerformanceNodeTiming` class. If the named `startMark` does
not exist, an error is thrown.

The optional `endMark` argument must identify any *existing* `PerformanceMark`
in the Performance Timeline or any of the timestamp properties provided by the
`PerformanceNodeTiming` class. `endMark` will be `performance.now()`
if no parameter is passed, otherwise if the named `endMark` does not exist, an
error will be thrown.

The created `PerformanceMeasure` entry is put in the global Performance Timeline
and can be queried with `performance.getEntries`,
`performance.getEntriesByName`, and `performance.getEntriesByType`. When the
observation is performed, the entries should be cleared from the global
Performance Timeline manually with `performance.clearMeasures`.

### `performance.nodeTiming`

Added in: v8.5.0

- Type: [`<PerformanceNodeTiming>`](41-perf-hooks.md#class-performancenodetiming)

*This property is an extension by Node.js. It is not available in Web browsers.*

An instance of the `PerformanceNodeTiming` class that provides performance
metrics for specific Node.js operational milestones.

### `performance.now()`

Added in: v8.5.0

**History**

| Version | Changes |
| --- | --- |
| v19.0.0 | This method must be called with the `performance` object as the receiver. |

- Returns: [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type)

Returns the current high resolution millisecond timestamp, where 0 represents
the start of the current `node` process.

### `performance.setResourceTimingBufferSize(maxSize)`

Added in: v18.8.0

**History**

| Version | Changes |
| --- | --- |
| v19.0.0 | This method must be called with the `performance` object as the receiver. |

Sets the global performance resource timing buffer size to the specified number
of "resource" type performance entry objects.

By default the max buffer size is set to 250.

### `performance.timeOrigin`

Added in: v8.5.0

- Type: [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type)

The [`timeOrigin`](https://w3c.github.io/hr-time/#dom-performance-timeorigin) specifies the high resolution millisecond timestamp at
which the current `node` process began, measured in Unix time.

### `performance.timerify(fn[, options])`

Added in: v8.5.0

**History**

| Version | Changes |
| --- | --- |
| v25.2.0, v24.12.0 | Added `perf_hooks.timerify` alias. |
| v16.0.0 | Added the histogram option. |
| v16.0.0 | Re-implemented to use pure-JavaScript and the ability to time async functions. |

- `fn` [`<Function>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Function)
- `options` [`<Object>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Object)
  - `histogram` [`<RecordableHistogram>`](41-perf-hooks.md#class-recordablehistogram-extends-histogram) A histogram object created using `perf_hooks.createHistogram()` that will record runtime durations in
    nanoseconds.

This is an alias of [`perf_hooks.timerify()`](#perf_hookstimerifyfn-options).

*This property is an extension by Node.js. It is not available in Web browsers.*

### `performance.toJSON()`

Added in: v16.1.0

**History**

| Version | Changes |
| --- | --- |
| v19.0.0 | This method must be called with the `performance` object as the receiver. |

An object which is JSON representation of the `performance` object. It
is similar to [`window.performance.toJSON`](https://developer.mozilla.org/en-US/docs/Web/API/Performance/toJSON) in browsers.

#### Event: `'resourcetimingbufferfull'`

Added in: v18.8.0

The `'resourcetimingbufferfull'` event is fired when the global performance
resource timing buffer is full. Adjust resource timing buffer size with
`performance.setResourceTimingBufferSize()` or clear the buffer with
`performance.clearResourceTimings()` in the event listener to allow
more entries to be added to the performance timeline buffer.

## Class: `PerformanceEntry`

Added in: v8.5.0

The constructor of this class is not exposed to users directly.

### `performanceEntry.duration`

Added in: v8.5.0

**History**

| Version | Changes |
| --- | --- |
| v19.0.0 | This property getter must be called with the `PerformanceEntry` object as the receiver. |

- Type: [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type)

The total number of milliseconds elapsed for this entry. This value will not
be meaningful for all Performance Entry types.

### `performanceEntry.entryType`

Added in: v8.5.0

**History**

| Version | Changes |
| --- | --- |
| v19.0.0 | This property getter must be called with the `PerformanceEntry` object as the receiver. |

- Type: [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type)

The type of the performance entry. It may be one of:

- `'dns'` (Node.js only)
- `'function'` (Node.js only)
- `'gc'` (Node.js only)
- `'http2'` (Node.js only)
- `'http'` (Node.js only)
- `'mark'` (available on the Web)
- `'measure'` (available on the Web)
- `'net'` (Node.js only)
- `'node'` (Node.js only)
- `'resource'` (available on the Web)

### `performanceEntry.name`

Added in: v8.5.0

**History**

| Version | Changes |
| --- | --- |
| v19.0.0 | This property getter must be called with the `PerformanceEntry` object as the receiver. |

- Type: [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type)

The name of the performance entry.

### `performanceEntry.startTime`

Added in: v8.5.0

**History**

| Version | Changes |
| --- | --- |
| v19.0.0 | This property getter must be called with the `PerformanceEntry` object as the receiver. |

- Type: [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type)

The high resolution millisecond timestamp marking the starting time of the
Performance Entry.

## Class: `PerformanceMark`

Added in: v18.2.0, v16.17.0

- Extends: [`<PerformanceEntry>`](41-perf-hooks.md#class-performanceentry)

Exposes marks created via the `Performance.mark()` method.

### `performanceMark.detail`

Added in: v16.0.0

**History**

| Version | Changes |
| --- | --- |
| v19.0.0 | This property getter must be called with the `PerformanceMark` object as the receiver. |

- Type: [`<any>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#Data_types)

Additional detail specified when creating with `Performance.mark()` method.

## Class: `PerformanceMeasure`

Added in: v18.2.0, v16.17.0

- Extends: [`<PerformanceEntry>`](41-perf-hooks.md#class-performanceentry)

Exposes measures created via the `Performance.measure()` method.

The constructor of this class is not exposed to users directly.

### `performanceMeasure.detail`

Added in: v16.0.0

**History**

| Version | Changes |
| --- | --- |
| v19.0.0 | This property getter must be called with the `PerformanceMeasure` object as the receiver. |

- Type: [`<any>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#Data_types)

Additional detail specified when creating with `Performance.measure()` method.

## Class: `PerformanceNodeEntry`

Added in: v19.0.0

- Extends: [`<PerformanceEntry>`](41-perf-hooks.md#class-performanceentry)

*This class is an extension by Node.js. It is not available in Web browsers.*

Provides detailed Node.js timing data.

The constructor of this class is not exposed to users directly.

### `performanceNodeEntry.detail`

Added in: v16.0.0

**History**

| Version | Changes |
| --- | --- |
| v19.0.0 | This property getter must be called with the `PerformanceNodeEntry` object as the receiver. |

- Type: [`<any>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#Data_types)

Additional detail specific to the `entryType`.

### `performanceNodeEntry.flags`

Added in: v13.9.0, v12.17.0

**History**

| Version | Changes |
| --- | --- |
| v16.0.0 | Runtime deprecated. Now moved to the detail property when entryType is 'gc'. |

Stability: 0 - Deprecated: Use `performanceNodeEntry.detail` instead.

- Type: [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type)

When `performanceEntry.entryType` is equal to `'gc'`, the `performance.flags`
property contains additional information about garbage collection operation.
The value may be one of:

- `perf_hooks.constants.NODE_PERFORMANCE_GC_FLAGS_NO`
- `perf_hooks.constants.NODE_PERFORMANCE_GC_FLAGS_CONSTRUCT_RETAINED`
- `perf_hooks.constants.NODE_PERFORMANCE_GC_FLAGS_FORCED`
- `perf_hooks.constants.NODE_PERFORMANCE_GC_FLAGS_SYNCHRONOUS_PHANTOM_PROCESSING`
- `perf_hooks.constants.NODE_PERFORMANCE_GC_FLAGS_ALL_AVAILABLE_GARBAGE`
- `perf_hooks.constants.NODE_PERFORMANCE_GC_FLAGS_ALL_EXTERNAL_MEMORY`
- `perf_hooks.constants.NODE_PERFORMANCE_GC_FLAGS_SCHEDULE_IDLE`

### `performanceNodeEntry.kind`

Added in: v8.5.0

**History**

| Version | Changes |
| --- | --- |
| v16.0.0 | Runtime deprecated. Now moved to the detail property when entryType is 'gc'. |

Stability: 0 - Deprecated: Use `performanceNodeEntry.detail` instead.

- Type: [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type)

When `performanceEntry.entryType` is equal to `'gc'`, the `performance.kind`
property identifies the type of garbage collection operation that occurred.
The value may be one of:

- `perf_hooks.constants.NODE_PERFORMANCE_GC_MAJOR`
- `perf_hooks.constants.NODE_PERFORMANCE_GC_MINOR`
- `perf_hooks.constants.NODE_PERFORMANCE_GC_MINOR_MARK_SWEEP`
- `perf_hooks.constants.NODE_PERFORMANCE_GC_INCREMENTAL`
- `perf_hooks.constants.NODE_PERFORMANCE_GC_WEAKCB`

### Garbage Collection ('gc') Details

When `performanceEntry.type` is equal to `'gc'`, the
`performanceNodeEntry.detail` property will be an [`<Object>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Object) with two properties:

- `kind` [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) One of:
  - `perf_hooks.constants.NODE_PERFORMANCE_GC_MAJOR`
  - `perf_hooks.constants.NODE_PERFORMANCE_GC_MINOR`
  - `perf_hooks.constants.NODE_PERFORMANCE_GC_MINOR_MARK_SWEEP`
  - `perf_hooks.constants.NODE_PERFORMANCE_GC_INCREMENTAL`
  - `perf_hooks.constants.NODE_PERFORMANCE_GC_WEAKCB`
- `flags` [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) One of:
  - `perf_hooks.constants.NODE_PERFORMANCE_GC_FLAGS_NO`
  - `perf_hooks.constants.NODE_PERFORMANCE_GC_FLAGS_CONSTRUCT_RETAINED`
  - `perf_hooks.constants.NODE_PERFORMANCE_GC_FLAGS_FORCED`
  - `perf_hooks.constants.NODE_PERFORMANCE_GC_FLAGS_SYNCHRONOUS_PHANTOM_PROCESSING`
  - `perf_hooks.constants.NODE_PERFORMANCE_GC_FLAGS_ALL_AVAILABLE_GARBAGE`
  - `perf_hooks.constants.NODE_PERFORMANCE_GC_FLAGS_ALL_EXTERNAL_MEMORY`
  - `perf_hooks.constants.NODE_PERFORMANCE_GC_FLAGS_SCHEDULE_IDLE`

### HTTP ('http') Details

When `performanceEntry.type` is equal to `'http'`, the
`performanceNodeEntry.detail` property will be an [`<Object>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Object) containing
additional information.

If `performanceEntry.name` is equal to `HttpClient`, the `detail`
will contain the following properties: `req`, `res`. And the `req` property
will be an [`<Object>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Object) containing `method`, `url`, `headers`, the `res` property
will be an [`<Object>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Object) containing `statusCode`, `statusMessage`, `headers`.

If `performanceEntry.name` is equal to `HttpRequest`, the `detail`
will contain the following properties: `req`, `res`. And the `req` property
will be an [`<Object>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Object) containing `method`, `url`, `headers`, the `res` property
will be an [`<Object>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Object) containing `statusCode`, `statusMessage`, `headers`.

This could add additional memory overhead and should only be used for
diagnostic purposes, not left turned on in production by default.

### HTTP/2 ('http2') Details

When `performanceEntry.type` is equal to `'http2'`, the
`performanceNodeEntry.detail` property will be an [`<Object>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Object) containing
additional performance information.

If `performanceEntry.name` is equal to `Http2Stream`, the `detail`
will contain the following properties:

- `bytesRead` [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) The number of `DATA` frame bytes received for this
  `Http2Stream`.
- `bytesWritten` [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) The number of `DATA` frame bytes sent for this
  `Http2Stream`.
- `id` [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) The identifier of the associated `Http2Stream`
- `timeToFirstByte` [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) The number of milliseconds elapsed between the `PerformanceEntry` `startTime` and the reception of the first `DATA` frame.
- `timeToFirstByteSent` [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) The number of milliseconds elapsed between
  the `PerformanceEntry` `startTime` and sending of the first `DATA` frame.
- `timeToFirstHeader` [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) The number of milliseconds elapsed between the `PerformanceEntry` `startTime` and the reception of the first header.

If `performanceEntry.name` is equal to `Http2Session`, the `detail` will
contain the following properties:

- `bytesRead` [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) The number of bytes received for this `Http2Session`.
- `bytesWritten` [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) The number of bytes sent for this `Http2Session`.
- `framesReceived` [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) The number of HTTP/2 frames received by the `Http2Session`.
- `framesSent` [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) The number of HTTP/2 frames sent by the `Http2Session`.
- `maxConcurrentStreams` [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) The maximum number of streams concurrently
  open during the lifetime of the `Http2Session`.
- `pingRTT` [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) The number of milliseconds elapsed since the transmission
  of a `PING` frame and the reception of its acknowledgment. Only present if
  a `PING` frame has been sent on the `Http2Session`.
- `streamAverageDuration` [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) The average duration (in milliseconds) for
  all `Http2Stream` instances.
- `streamCount` [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) The number of `Http2Stream` instances processed by
  the `Http2Session`.
- `type` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) Either `'server'` or `'client'` to identify the type of
  `Http2Session`.

### Timerify ('function') Details

When `performanceEntry.type` is equal to `'function'`, the
`performanceNodeEntry.detail` property will be an [`<Array>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Array) listing
the input arguments to the timed function.

### Net ('net') Details

When `performanceEntry.type` is equal to `'net'`, the
`performanceNodeEntry.detail` property will be an [`<Object>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Object) containing
additional information.

If `performanceEntry.name` is equal to `connect`, the `detail`
will contain the following properties: `host`, `port`.

### DNS ('dns') Details

When `performanceEntry.type` is equal to `'dns'`, the
`performanceNodeEntry.detail` property will be an [`<Object>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Object) containing
additional information.

If `performanceEntry.name` is equal to `lookup`, the `detail`
will contain the following properties: `hostname`, `family`, `hints`, `verbatim`,
`addresses`.

If `performanceEntry.name` is equal to `lookupService`, the `detail` will
contain the following properties: `host`, `port`, `hostname`, `service`.

If `performanceEntry.name` is equal to `queryxxx` or `getHostByAddr`, the `detail` will
contain the following properties: `host`, `ttl`, `result`. The value of `result` is
same as the result of `queryxxx` or `getHostByAddr`.

## Class: `PerformanceNodeTiming`

Added in: v8.5.0

- Extends: [`<PerformanceEntry>`](41-perf-hooks.md#class-performanceentry)

*This property is an extension by Node.js. It is not available in Web browsers.*

Provides timing details for Node.js itself. The constructor of this class
is not exposed to users.

### `performanceNodeTiming.bootstrapComplete`

Added in: v8.5.0

- Type: [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type)

The high resolution millisecond timestamp at which the Node.js process
completed bootstrapping. If bootstrapping has not yet finished, the property
has the value of -1.

### `performanceNodeTiming.environment`

Added in: v8.5.0

- Type: [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type)

The high resolution millisecond timestamp at which the Node.js environment was
initialized.

### `performanceNodeTiming.idleTime`

Added in: v14.10.0, v12.19.0

- Type: [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type)

The high resolution millisecond timestamp of the amount of time the event loop
has been idle within the event loop's event provider (e.g. `epoll_wait`). This
does not take CPU usage into consideration. If the event loop has not yet
started (e.g., in the first tick of the main script), the property has the
value of 0.

### `performanceNodeTiming.loopExit`

Added in: v8.5.0

- Type: [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type)

The high resolution millisecond timestamp at which the Node.js event loop
exited. If the event loop has not yet exited, the property has the value of -1.
It can only have a value of not -1 in a handler of the [`'exit'`](43-process.md#event-exit) event.

### `performanceNodeTiming.loopStart`

Added in: v8.5.0

- Type: [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type)

The high resolution millisecond timestamp at which the Node.js event loop
started. If the event loop has not yet started (e.g., in the first tick of the
main script), the property has the value of -1.

### `performanceNodeTiming.nodeStart`

Added in: v8.5.0

- Type: [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type)

The high resolution millisecond timestamp at which the Node.js process was
initialized.

### `performanceNodeTiming.uvMetricsInfo`

Added in: v22.8.0, v20.18.0

- Type: [`<Object>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Object)
  - `loopCount` [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) Number of event loop iterations.
  - `events` [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) Number of events that have been processed by the event handler.
  - `eventsWaiting` [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) Number of events that were waiting to be processed when the event provider was called.

This is a wrapper to the `uv_metrics_info` function.
It returns the current set of event loop metrics.

The values are exact up to `Number.MAX_SAFE_INTEGER`. Use
[`performanceNodeTiming.uvMetricsInfoBigInt`](#performancenodetiminguvmetricsinfobigint) to obtain the full 64-bit
values reported by libuv.

It is recommended to use this property inside a function whose execution was
scheduled using `setImmediate` to avoid collecting metrics before finishing all
operations scheduled during the current loop iteration.

*CommonJS version:*

```js
const { performance } = require('node:perf_hooks');

setImmediate(() => {
  console.log(performance.nodeTiming.uvMetricsInfo);
});
```

*ES module version:*

```js
import { performance } from 'node:perf_hooks';

setImmediate(() => {
  console.log(performance.nodeTiming.uvMetricsInfo);
});
```

### `performanceNodeTiming.uvMetricsInfoBigInt`

Added in: v26.11.0

- Type: [`<Object>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Object)
  - `loopCount` [`<bigint>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#bigint_type) Number of event loop iterations.
  - `events` [`<bigint>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#bigint_type) Number of events that have been processed by the event handler.
  - `eventsWaiting` [`<bigint>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#bigint_type) Number of events that were waiting to be processed when the event provider was called.

The same as [`performanceNodeTiming.uvMetricsInfo`](#performancenodetiminguvmetricsinfo), except that the values
are [`<bigint>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#bigint_type)s carrying the full 64-bit range reported by libuv.

Because `JSON.stringify()` cannot serialize [`<bigint>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#bigint_type) values, this property is
not enumerable and is not included in the output of `performanceNodeTiming.toJSON()`. Copies of `performance.nodeTiming` made by
spreading its enumerable properties, for example, remain serializable.

*CommonJS version:*

```js
const { performance } = require('node:perf_hooks');

setImmediate(() => {
  console.log(performance.nodeTiming.uvMetricsInfoBigInt);
});
```

*ES module version:*

```js
import { performance } from 'node:perf_hooks';

setImmediate(() => {
  console.log(performance.nodeTiming.uvMetricsInfoBigInt);
});
```

### `performanceNodeTiming.v8Start`

Added in: v8.5.0

- Type: [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type)

The high resolution millisecond timestamp at which the V8 platform was
initialized.

## Class: `PerformanceResourceTiming`

Added in: v18.2.0, v16.17.0

- Extends: [`<PerformanceEntry>`](41-perf-hooks.md#class-performanceentry)

Provides detailed network timing data regarding the loading of an application's
resources.

The constructor of this class is not exposed to users directly.

### `performanceResourceTiming.workerStart`

Added in: v18.2.0, v16.17.0

**History**

| Version | Changes |
| --- | --- |
| v19.0.0 | This property getter must be called with the `PerformanceResourceTiming` object as the receiver. |

- Type: [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type)

The high resolution millisecond timestamp at immediately before dispatching
the `fetch` request. If the resource is not intercepted by a worker the property
will always return 0.

### `performanceResourceTiming.redirectStart`

Added in: v18.2.0, v16.17.0

**History**

| Version | Changes |
| --- | --- |
| v19.0.0 | This property getter must be called with the `PerformanceResourceTiming` object as the receiver. |

- Type: [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type)

The high resolution millisecond timestamp that represents the start time
of the fetch which initiates the redirect.

### `performanceResourceTiming.redirectEnd`

Added in: v18.2.0, v16.17.0

**History**

| Version | Changes |
| --- | --- |
| v19.0.0 | This property getter must be called with the `PerformanceResourceTiming` object as the receiver. |

- Type: [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type)

The high resolution millisecond timestamp that will be created immediately after
receiving the last byte of the response of the last redirect.

### `performanceResourceTiming.fetchStart`

Added in: v18.2.0, v16.17.0

**History**

| Version | Changes |
| --- | --- |
| v19.0.0 | This property getter must be called with the `PerformanceResourceTiming` object as the receiver. |

- Type: [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type)

The high resolution millisecond timestamp immediately before the Node.js starts
to fetch the resource.

### `performanceResourceTiming.domainLookupStart`

Added in: v18.2.0, v16.17.0

**History**

| Version | Changes |
| --- | --- |
| v19.0.0 | This property getter must be called with the `PerformanceResourceTiming` object as the receiver. |

- Type: [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type)

The high resolution millisecond timestamp immediately before the Node.js starts
the domain name lookup for the resource.

### `performanceResourceTiming.domainLookupEnd`

Added in: v18.2.0, v16.17.0

**History**

| Version | Changes |
| --- | --- |
| v19.0.0 | This property getter must be called with the `PerformanceResourceTiming` object as the receiver. |

- Type: [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type)

The high resolution millisecond timestamp representing the time immediately
after the Node.js finished the domain name lookup for the resource.

### `performanceResourceTiming.connectStart`

Added in: v18.2.0, v16.17.0

**History**

| Version | Changes |
| --- | --- |
| v19.0.0 | This property getter must be called with the `PerformanceResourceTiming` object as the receiver. |

- Type: [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type)

The high resolution millisecond timestamp representing the time immediately
before Node.js starts to establish the connection to the server to retrieve
the resource.

### `performanceResourceTiming.connectEnd`

Added in: v18.2.0, v16.17.0

**History**

| Version | Changes |
| --- | --- |
| v19.0.0 | This property getter must be called with the `PerformanceResourceTiming` object as the receiver. |

- Type: [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type)

The high resolution millisecond timestamp representing the time immediately
after Node.js finishes establishing the connection to the server to retrieve
the resource.

### `performanceResourceTiming.secureConnectionStart`

Added in: v18.2.0, v16.17.0

**History**

| Version | Changes |
| --- | --- |
| v19.0.0 | This property getter must be called with the `PerformanceResourceTiming` object as the receiver. |

- Type: [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type)

The high resolution millisecond timestamp representing the time immediately
before Node.js starts the handshake process to secure the current connection.

### `performanceResourceTiming.requestStart`

Added in: v18.2.0, v16.17.0

**History**

| Version | Changes |
| --- | --- |
| v19.0.0 | This property getter must be called with the `PerformanceResourceTiming` object as the receiver. |

- Type: [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type)

The high resolution millisecond timestamp representing the time immediately
before Node.js receives the first byte of the response from the server.

### `performanceResourceTiming.finalResponseHeadersStart`

Added in: v26.9.0

- Type: [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type)

The high resolution millisecond timestamp representing the time immediately
after Node.js receives the first byte of the final response,
as opposed to an interim response.

### `performanceResourceTiming.firstInterimResponseStart`

Added in: v26.9.0

- Type: [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type)

The high resolution millisecond timestamp representing the time immediately
after Node.js receives the first byte of the first interim response, such as
a `103 Early Hints` response.

### `performanceResourceTiming.responseStart`

Added in: v18.2.0, v16.17.0

**History**

| Version | Changes |
| --- | --- |
| v26.9.0 | This property now returns `firstInterimResponseStart` when it is non-zero. |
| v19.0.0 | This property getter must be called with the `PerformanceResourceTiming` object as the receiver. |

- Type: [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type)

The high resolution millisecond timestamp representing the time immediately
after Node.js receives the first byte of the response from the server. This
is `firstInterimResponseStart` when it is non-zero, and
`finalResponseHeadersStart` otherwise.

### `performanceResourceTiming.responseEnd`

Added in: v18.2.0, v16.17.0

**History**

| Version | Changes |
| --- | --- |
| v19.0.0 | This property getter must be called with the `PerformanceResourceTiming` object as the receiver. |

- Type: [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type)

The high resolution millisecond timestamp representing the time immediately
after Node.js receives the last byte of the resource or immediately before
the transport connection is closed, whichever comes first.

### `performanceResourceTiming.transferSize`

Added in: v18.2.0, v16.17.0

**History**

| Version | Changes |
| --- | --- |
| v19.0.0 | This property getter must be called with the `PerformanceResourceTiming` object as the receiver. |

- Type: [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type)

A number representing the size (in octets) of the fetched resource. The size
includes the response header fields plus the response payload body.

### `performanceResourceTiming.encodedBodySize`

Added in: v18.2.0, v16.17.0

**History**

| Version | Changes |
| --- | --- |
| v19.0.0 | This property getter must be called with the `PerformanceResourceTiming` object as the receiver. |

- Type: [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type)

A number representing the size (in octets) received from the fetch
(HTTP or cache), of the payload body, before removing any applied
content-codings.

### `performanceResourceTiming.decodedBodySize`

Added in: v18.2.0, v16.17.0

**History**

| Version | Changes |
| --- | --- |
| v19.0.0 | This property getter must be called with the `PerformanceResourceTiming` object as the receiver. |

- Type: [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type)

A number representing the size (in octets) received from the fetch
(HTTP or cache), of the message body, after removing any applied
content-codings.

### `performanceResourceTiming.renderBlockingStatus`

Added in: v26.9.0

- Type: [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type)

The render blocking status of the resource. It is either `'blocking'` or `'non-blocking'`.

### `performanceResourceTiming.contentType`

Added in: v26.9.0

- Type: [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type)

The minimized MIME type of the content of the fetched resource, or an empty
string if it cannot be determined.

### `performanceResourceTiming.contentEncoding`

Added in: v26.9.0

- Type: [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type)

The content encoding of the fetched resource, such as `'gzip'` or `'br'`.

### `performanceResourceTiming.toJSON()`

Added in: v18.2.0, v16.17.0

**History**

| Version | Changes |
| --- | --- |
| v19.0.0 | This method must be called with the `PerformanceResourceTiming` object as the receiver. |

Returns a `object` that is the JSON representation of the
`PerformanceResourceTiming` object

## Class: `PerformanceObserver`

Added in: v8.5.0

### `PerformanceObserver.supportedEntryTypes`

Added in: v16.0.0

- Type: [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type)[]

Get supported types.

### `new PerformanceObserver(callback)`

Added in: v8.5.0

**History**

| Version | Changes |
| --- | --- |
| v18.0.0 | Passing an invalid callback to the `callback` argument now throws `ERR_INVALID_ARG_TYPE` instead of `ERR_INVALID_CALLBACK`. |

- `callback` [`<Function>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Function)
  - `list` [`<PerformanceObserverEntryList>`](41-perf-hooks.md#class-performanceobserverentrylist)
  - `observer` [`<PerformanceObserver>`](41-perf-hooks.md#class-performanceobserver)

`PerformanceObserver` objects provide notifications when new
`PerformanceEntry` instances have been added to the Performance Timeline.

*ES module version:*

```js
import { performance, PerformanceObserver } from 'node:perf_hooks';

const obs = new PerformanceObserver((list, observer) => {
  console.log(list.getEntries());

  performance.clearMarks();
  performance.clearMeasures();
  observer.disconnect();
});
obs.observe({ entryTypes: ['mark'], buffered: true });

performance.mark('test');
```

*CommonJS version:*

```js
const {
  performance,
  PerformanceObserver,
} = require('node:perf_hooks');

const obs = new PerformanceObserver((list, observer) => {
  console.log(list.getEntries());

  performance.clearMarks();
  performance.clearMeasures();
  observer.disconnect();
});
obs.observe({ entryTypes: ['mark'], buffered: true });

performance.mark('test');
```

Because `PerformanceObserver` instances introduce their own additional
performance overhead, instances should not be left subscribed to notifications
indefinitely. Users should disconnect observers as soon as they are no
longer needed.

The `callback` is invoked when a `PerformanceObserver` is
notified about new `PerformanceEntry` instances. The callback receives a
`PerformanceObserverEntryList` instance and a reference to the
`PerformanceObserver`.

### `performanceObserver.disconnect()`

Added in: v8.5.0

Disconnects the `PerformanceObserver` instance from all notifications.

### `performanceObserver.observe(options)`

Added in: v8.5.0

**History**

| Version | Changes |
| --- | --- |
| v16.7.0 | Updated to conform to Performance Timeline Level 2. The buffered option has been added back. |
| v16.0.0 | Updated to conform to User Timing Level 3. The buffered option has been removed. |

- `options` [`<Object>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Object)
  - `type` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) A single [`<PerformanceEntry>`](41-perf-hooks.md#class-performanceentry) type. Must not be given
    if `entryTypes` is already specified.
  - `entryTypes` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type)[] An array of strings identifying the types of
    [`<PerformanceEntry>`](41-perf-hooks.md#class-performanceentry) instances the observer is interested in. If not
    provided an error will be thrown.
  - `buffered` [`<boolean>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#boolean_type) If true, the observer callback is called with a
    list global `PerformanceEntry` buffered entries. If false, only
    `PerformanceEntry`s created after the time point are sent to the
    observer callback. **Default:** `false`.

Subscribes the [`<PerformanceObserver>`](41-perf-hooks.md#class-performanceobserver) instance to notifications of new
[`<PerformanceEntry>`](41-perf-hooks.md#class-performanceentry) instances identified either by `options.entryTypes`
or `options.type`:

*ES module version:*

```js
import { performance, PerformanceObserver } from 'node:perf_hooks';

const obs = new PerformanceObserver((list, observer) => {
  // Called once asynchronously. `list` contains three items.
});
obs.observe({ type: 'mark' });

for (let n = 0; n < 3; n++)
  performance.mark(`test${n}`);
```

*CommonJS version:*

```js
const {
  performance,
  PerformanceObserver,
} = require('node:perf_hooks');

const obs = new PerformanceObserver((list, observer) => {
  // Called once asynchronously. `list` contains three items.
});
obs.observe({ type: 'mark' });

for (let n = 0; n < 3; n++)
  performance.mark(`test${n}`);
```

### `performanceObserver.takeRecords()`

Added in: v16.0.0

- Returns: [`<PerformanceEntry>`](41-perf-hooks.md#class-performanceentry)[] Current list of entries stored in the performance observer, emptying it out.

## Class: `PerformanceObserverEntryList`

Added in: v8.5.0

The `PerformanceObserverEntryList` class is used to provide access to the
`PerformanceEntry` instances passed to a `PerformanceObserver`.
The constructor of this class is not exposed to users.

### `performanceObserverEntryList.getEntries()`

Added in: v8.5.0

- Returns: [`<PerformanceEntry>`](41-perf-hooks.md#class-performanceentry)[]

Returns a list of `PerformanceEntry` objects in chronological order
with respect to `performanceEntry.startTime`.

*ES module version:*

```js
import { performance, PerformanceObserver } from 'node:perf_hooks';

const obs = new PerformanceObserver((perfObserverList, observer) => {
  console.log(perfObserverList.getEntries());
  /**
   * [
   *   PerformanceEntry {
   *     name: 'test',
   *     entryType: 'mark',
   *     startTime: 81.465639,
   *     duration: 0,
   *     detail: null
   *   },
   *   PerformanceEntry {
   *     name: 'meow',
   *     entryType: 'mark',
   *     startTime: 81.860064,
   *     duration: 0,
   *     detail: null
   *   }
   * ]
   */

  performance.clearMarks();
  performance.clearMeasures();
  observer.disconnect();
});
obs.observe({ type: 'mark' });

performance.mark('test');
performance.mark('meow');
```

*CommonJS version:*

```js
const {
  performance,
  PerformanceObserver,
} = require('node:perf_hooks');

const obs = new PerformanceObserver((perfObserverList, observer) => {
  console.log(perfObserverList.getEntries());
  /**
   * [
   *   PerformanceEntry {
   *     name: 'test',
   *     entryType: 'mark',
   *     startTime: 81.465639,
   *     duration: 0,
   *     detail: null
   *   },
   *   PerformanceEntry {
   *     name: 'meow',
   *     entryType: 'mark',
   *     startTime: 81.860064,
   *     duration: 0,
   *     detail: null
   *   }
   * ]
   */

  performance.clearMarks();
  performance.clearMeasures();
  observer.disconnect();
});
obs.observe({ type: 'mark' });

performance.mark('test');
performance.mark('meow');
```

### `performanceObserverEntryList.getEntriesByName(name[, type])`

Added in: v8.5.0

- `name` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type)
- `type` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type)
- Returns: [`<PerformanceEntry>`](41-perf-hooks.md#class-performanceentry)[]

Returns a list of `PerformanceEntry` objects in chronological order
with respect to `performanceEntry.startTime` whose `performanceEntry.name` is
equal to `name`, and optionally, whose `performanceEntry.entryType` is equal to
`type`.

*ES module version:*

```js
import { performance, PerformanceObserver } from 'node:perf_hooks';

const obs = new PerformanceObserver((perfObserverList, observer) => {
  console.log(perfObserverList.getEntriesByName('meow'));
  /**
   * [
   *   PerformanceEntry {
   *     name: 'meow',
   *     entryType: 'mark',
   *     startTime: 98.545991,
   *     duration: 0,
   *     detail: null
   *   }
   * ]
   */
  console.log(perfObserverList.getEntriesByName('nope')); // []

  console.log(perfObserverList.getEntriesByName('test', 'mark'));
  /**
   * [
   *   PerformanceEntry {
   *     name: 'test',
   *     entryType: 'mark',
   *     startTime: 63.518931,
   *     duration: 0,
   *     detail: null
   *   }
   * ]
   */
  console.log(perfObserverList.getEntriesByName('test', 'measure')); // []

  performance.clearMarks();
  performance.clearMeasures();
  observer.disconnect();
});
obs.observe({ entryTypes: ['mark', 'measure'] });

performance.mark('test');
performance.mark('meow');
```

*CommonJS version:*

```js
const {
  performance,
  PerformanceObserver,
} = require('node:perf_hooks');

const obs = new PerformanceObserver((perfObserverList, observer) => {
  console.log(perfObserverList.getEntriesByName('meow'));
  /**
   * [
   *   PerformanceEntry {
   *     name: 'meow',
   *     entryType: 'mark',
   *     startTime: 98.545991,
   *     duration: 0,
   *     detail: null
   *   }
   * ]
   */
  console.log(perfObserverList.getEntriesByName('nope')); // []

  console.log(perfObserverList.getEntriesByName('test', 'mark'));
  /**
   * [
   *   PerformanceEntry {
   *     name: 'test',
   *     entryType: 'mark',
   *     startTime: 63.518931,
   *     duration: 0,
   *     detail: null
   *   }
   * ]
   */
  console.log(perfObserverList.getEntriesByName('test', 'measure')); // []

  performance.clearMarks();
  performance.clearMeasures();
  observer.disconnect();
});
obs.observe({ entryTypes: ['mark', 'measure'] });

performance.mark('test');
performance.mark('meow');
```

### `performanceObserverEntryList.getEntriesByType(type)`

Added in: v8.5.0

- `type` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type)
- Returns: [`<PerformanceEntry>`](41-perf-hooks.md#class-performanceentry)[]

Returns a list of `PerformanceEntry` objects in chronological order
with respect to `performanceEntry.startTime` whose `performanceEntry.entryType`
is equal to `type`.

*ES module version:*

```js
import { performance, PerformanceObserver } from 'node:perf_hooks';

const obs = new PerformanceObserver((perfObserverList, observer) => {
  console.log(perfObserverList.getEntriesByType('mark'));
  /**
   * [
   *   PerformanceEntry {
   *     name: 'test',
   *     entryType: 'mark',
   *     startTime: 55.897834,
   *     duration: 0,
   *     detail: null
   *   },
   *   PerformanceEntry {
   *     name: 'meow',
   *     entryType: 'mark',
   *     startTime: 56.350146,
   *     duration: 0,
   *     detail: null
   *   }
   * ]
   */
  performance.clearMarks();
  performance.clearMeasures();
  observer.disconnect();
});
obs.observe({ type: 'mark' });

performance.mark('test');
performance.mark('meow');
```

*CommonJS version:*

```js
const {
  performance,
  PerformanceObserver,
} = require('node:perf_hooks');

const obs = new PerformanceObserver((perfObserverList, observer) => {
  console.log(perfObserverList.getEntriesByType('mark'));
  /**
   * [
   *   PerformanceEntry {
   *     name: 'test',
   *     entryType: 'mark',
   *     startTime: 55.897834,
   *     duration: 0,
   *     detail: null
   *   },
   *   PerformanceEntry {
   *     name: 'meow',
   *     entryType: 'mark',
   *     startTime: 56.350146,
   *     duration: 0,
   *     detail: null
   *   }
   * ]
   */
  performance.clearMarks();
  performance.clearMeasures();
  observer.disconnect();
});
obs.observe({ type: 'mark' });

performance.mark('test');
performance.mark('meow');
```

## `perf_hooks.createHistogram([options])`

Added in: v15.9.0, v14.18.0

- `options` [`<Object>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Object)
  - `lowest` [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) | [`<bigint>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#bigint_type) The lowest discernible value. Must be an integer
    value greater than 0. **Default:** `1`.
  - `highest` [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) | [`<bigint>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#bigint_type) The highest recordable value. Must be an integer
    value that is equal to or greater than two times `lowest`.
    **Default:** `Number.MAX_SAFE_INTEGER`.
  - `figures` [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) The number of accuracy digits. Must be a number between `1` and `5`. **Default:** `3`.
  - `halfLife` [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) The EWMA half-life in number of samples. When set to
    a value greater than 0, the histogram tracks an exponentially weighted
    moving average and standard deviation, accessible via `histogram.ewmaMean` and `histogram.ewmaStddev`. After `halfLife`
    recordings, a value's influence has decayed to 50%. **Default:** `0`
    (disabled).
  - `threshold` [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) An SLO threshold value. When set together with `halfLife`, the histogram tracks a smoothed error rate for values
    exceeding this threshold, accessible via `histogram.ewmaErrorRate` and
    `histogram.burnRate()`. **Default:** `0` (disabled).
- Returns: [`<RecordableHistogram>`](41-perf-hooks.md#class-recordablehistogram-extends-histogram)

Returns a [`<RecordableHistogram>`](41-perf-hooks.md#class-recordablehistogram-extends-histogram).

## `perf_hooks.createSlidingWindowHistogram(options)`

Added in: v26.10.0

- `options` [`<Object>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Object)
  - `chunks` [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) The number of histogram chunks retained. Must be an
    integer between `1` and `1024`.
  - `chunkDuration` [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) The duration of each chunk in milliseconds. Must
    be an integer between `1` and `18_446_744_073_709`. Exactly one of
    `chunkDuration` and `recordsPerChunk` must be specified.
  - `recordsPerChunk` [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) The number of calls to `record()` assigned to
    each chunk. Must be an integer between `1` and `Number.MAX_SAFE_INTEGER`.
    Exactly one of `chunkDuration` and `recordsPerChunk` must be specified.
  - `lowest` [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) | [`<bigint>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#bigint_type) The lowest discernible value. Must be an integer
    value greater than `0`. **Default:** `1`.
  - `highest` [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) | [`<bigint>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#bigint_type) The highest recordable value. Must be an integer
    value that is equal to or greater than two times `lowest`.
    **Default:** `Number.MAX_SAFE_INTEGER`.
  - `figures` [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) The number of accuracy digits. Must be an integer between `1` and `5`. **Default:** `3`.
- Returns: {SlidingWindowHistogram}

Creates a {SlidingWindowHistogram} that retains the latest `chunks` histogram
chunks. Rotation is lazy and does not create a timer. Time-based rotation is
evaluated when `record()` or `snapshot()` is called. Count-based rotation is
evaluated when `record()` is called.

One histogram chunk is allocated during construction. Additional chunks are
allocated lazily. The maximum native memory used by the window scales with
`chunks` and with the `lowest`, `highest`, and `figures` histogram options.

The window boundary has chunk-level precision. With `N` chunks of duration
`D`, a recorded value is retained for between `(N - 1) * D` and `N * D`
milliseconds. Once a count-based window is populated, it retains between
`(N - 1) * C + 1` and `N * C` recording attempts, where `C` is
`recordsPerChunk`. Recording attempts which exceed `highest` are included when
determining count-based rotation.

```js
const { createSlidingWindowHistogram } = require('node:perf_hooks');

const window = createSlidingWindowHistogram({
  chunks: 6,
  chunkDuration: 10_000,
});

window.record(20_000_000);

// Materialize the current window as an independent Histogram.
const snapshot = window.snapshot();
console.log(snapshot.percentile(99));
```

## `perf_hooks.importHistogram(data)`

Added in: v26.9.0

**History**

| Version | Changes |
| --- | --- |
| v26.11.0 | Format version 2 is supported. Unknown keys in version 2 data are ignored. |

- `data` [`<Uint8Array>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Uint8Array) A CBOR-encoded histogram previously produced by [`histogram.export()`](#histogramexport).
- Returns: [`<RecordableHistogram>`](41-perf-hooks.md#class-recordablehistogram-extends-histogram)

Reconstructs a histogram from a CBOR-encoded `Uint8Array`. The returned
histogram is a full [`<RecordableHistogram>`](41-perf-hooks.md#class-recordablehistogram-extends-histogram) with all bucket data, configuration,
and EWMA state restored. New values can be recorded into it.

Data in any format version produced by [`histogram.export()`](#histogramexport) can be
imported. See [histogram export format compatibility](#histogram-export-format-compatibility) for details.

```js
const { createHistogram, importHistogram } = require('node:perf_hooks');

const h = createHistogram();
for (let i = 1; i <= 1000; i++) h.record(i);

// Serialize and reconstruct
const data = h.export();
const h2 = importHistogram(data);

console.log(h2.count);          // 1000
console.log(h2.percentile(99)); // Same as h.percentile(99)
```

## `perf_hooks.eventLoopUtilization([utilization1[, utilization2]])`

Added in: v25.2.0, v24.12.0

- `utilization1` [`<Object>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Object) The result of a previous call to `eventLoopUtilization()`.
- `utilization2` [`<Object>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Object) The result of a previous call to `eventLoopUtilization()` prior to `utilization1`.
- Returns: [`<Object>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Object)
  - `idle` [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type)
  - `active` [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type)
  - `utilization` [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type)

The `eventLoopUtilization()` function returns an object that contains the
cumulative duration of time the event loop has been both idle and active as a
high resolution milliseconds timer. The `utilization` value is the calculated
Event Loop Utilization (ELU).

If bootstrapping has not yet finished on the main thread the properties have
the value of `0`. The ELU is immediately available on [Worker threads](67-worker-threads.md#worker-threads) since
bootstrap happens within the event loop.

Both `utilization1` and `utilization2` are optional parameters.

If `utilization1` is passed, then the delta between the current call's `active`
and `idle` times, as well as the corresponding `utilization` value are
calculated and returned (similar to [`process.hrtime()`](43-process.md#processhrtimetime)).

If `utilization1` and `utilization2` are both passed, then the delta is
calculated between the two arguments. This is a convenience option because,
unlike [`process.hrtime()`](43-process.md#processhrtimetime), calculating the ELU is more complex than a
single subtraction.

ELU is similar to CPU utilization, except that it only measures event loop
statistics and not CPU usage. It represents the percentage of time the event
loop has spent outside the event loop's event provider (e.g. `epoll_wait`).
No other CPU idle time is taken into consideration. The following is an example
of how a mostly idle process will have a high ELU.

*ES module version:*

```js
import { eventLoopUtilization } from 'node:perf_hooks';
import { spawnSync } from 'node:child_process';

setImmediate(() => {
  const elu = eventLoopUtilization();
  spawnSync('sleep', ['5']);
  console.log(eventLoopUtilization(elu).utilization);
});
```

*CommonJS version:*

```js
const { eventLoopUtilization } = require('node:perf_hooks');
const { spawnSync } = require('node:child_process');

setImmediate(() => {
  const elu = eventLoopUtilization();
  spawnSync('sleep', ['5']);
  console.log(eventLoopUtilization(elu).utilization);
});
```

Although the CPU is mostly idle while running this script, the value of
`utilization` is `1`. This is because the call to
[`child_process.spawnSync()`](11-child-process.md#child_processspawnsynccommand-args-options) blocks the event loop from proceeding.

Passing in a user-defined object instead of the result of a previous call to
`eventLoopUtilization()` will lead to undefined behavior. The return values
are not guaranteed to reflect any correct state of the event loop.

## `perf_hooks.monitorEventLoopDelay([options])`

Added in: v11.10.0

**History**

| Version | Changes |
| --- | --- |
| v26.11.0 | Added the `lowest`, `highest`, and `figures` options. |
| v26.5.0 | Added the `samplePerIteration` option. |

- `options` [`<Object>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Object)
  - `samplePerIteration` [`<boolean>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#boolean_type) When `true`, samples are taken once per
    event loop iteration. **Default:** `false`.
  - `resolution` [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) The sampling rate in milliseconds for interval-based
    sampling. Must be greater than zero. This option is ignored when `samplePerIteration` is `true`. **Default:** `10`.
  - `lowest` [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) | [`<bigint>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#bigint_type) The lowest discernible delay, in nanoseconds. Must
    be an integer value greater than `0`. **Default:** `1` when
    `samplePerIteration` is `true`, otherwise `1000`.
  - `highest` [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) | [`<bigint>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#bigint_type) The highest recordable delay, in nanoseconds.
    Must be an integer value that is equal to or greater than two times `lowest`. **Default:** `2n ** 63n - 1n`.
  - `figures` [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) The number of accuracy digits. Must be an integer
    between `1` and `5`. **Default:** `3`.
- Returns: [`<ELDHistogram>`](41-perf-hooks.md#class-eldhistogram-extends-histogram)

*This property is an extension by Node.js. It is not available in Web browsers.*

Creates a histogram object that samples and reports the event loop delay over
time. The delays will be reported in nanoseconds.

By default, the histogram is updated by a timer using the configured
`resolution`. When `samplePerIteration` is `true`, samples are taken once per
event loop iteration using `uv_prepare_t` and `uv_check_t` hooks. In that mode,
the histogram does not keep the loop alive or force additional iterations when
the application is idle.
The two sampling modes produce significantly different results and should not
be compared directly.

The `lowest`, `highest`, and `figures` options configure the histogram as they
do for [`perf_hooks.createHistogram()`](#perf_hookscreatehistogramoptions). `lowest` must be greater than `0`
because an event loop delay of zero is not possible: the event loop has a
minimal overhead, and the measurement itself depends on the event loop turning.
Delays greater than `highest` are not recorded, and are counted by
[`histogram.exceeds`](#histogramexceeds) instead. With interval-based sampling, every sample
includes the `resolution`, so `highest` should be well above
`resolution * 1e6`. The histogram's memory use depends on these options, not
on the number of samples.

*ES module version:*

```js
import { monitorEventLoopDelay } from 'node:perf_hooks';

const h = monitorEventLoopDelay({ resolution: 20 });
h.enable();
// Do something.
h.disable();
console.log(h.min);
console.log(h.max);
console.log(h.mean);
console.log(h.stddev);
console.log(h.percentiles);
console.log(h.percentile(50));
console.log(h.percentile(99));
```

*CommonJS version:*

```js
const { monitorEventLoopDelay } = require('node:perf_hooks');
const h = monitorEventLoopDelay({ resolution: 20 });
h.enable();
// Do something.
h.disable();
console.log(h.min);
console.log(h.max);
console.log(h.mean);
console.log(h.stddev);
console.log(h.percentiles);
console.log(h.percentile(50));
console.log(h.percentile(99));
```

## `perf_hooks.timerify(fn[, options])`

Added in: v25.2.0, v24.12.0

- `fn` [`<Function>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Function)
- `options` [`<Object>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Object)
  - `histogram` [`<RecordableHistogram>`](41-perf-hooks.md#class-recordablehistogram-extends-histogram) A histogram object created using `perf_hooks.createHistogram()` that will record runtime durations in
    nanoseconds.

*This property is an extension by Node.js. It is not available in Web browsers.*

Wraps a function within a new function that measures the running time of the
wrapped function. A `PerformanceObserver` must be subscribed to the `'function'`
event type in order for the timing details to be accessed.

*ES module version:*

```js
import { timerify, performance, PerformanceObserver } from 'node:perf_hooks';

function someFunction() {
  console.log('hello world');
}

const wrapped = timerify(someFunction);

const obs = new PerformanceObserver((list) => {
  console.log(list.getEntries()[0].duration);

  performance.clearMarks();
  performance.clearMeasures();
  obs.disconnect();
});
obs.observe({ entryTypes: ['function'] });

// A performance timeline entry will be created
wrapped();
```

*CommonJS version:*

```js
const {
  timerify,
  performance,
  PerformanceObserver,
} = require('node:perf_hooks');

function someFunction() {
  console.log('hello world');
}

const wrapped = timerify(someFunction);

const obs = new PerformanceObserver((list) => {
  console.log(list.getEntries()[0].duration);

  performance.clearMarks();
  performance.clearMeasures();
  obs.disconnect();
});
obs.observe({ entryTypes: ['function'] });

// A performance timeline entry will be created
wrapped();
```

If the wrapped function returns a promise, a finally handler will be attached
to the promise and the duration will be reported once the finally handler is
invoked.

## Class: `Histogram`

Added in: v11.10.0

### `histogram.burnRate(sloTarget)`

Added in: v26.8.0

- `sloTarget` [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) The SLO target as a fraction between 0 and 1
  (exclusive). For example, `0.999` for a 99.9% SLO.
- Returns: [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type)

Returns the SLO burn rate: `ewmaErrorRate / (1 - sloTarget)`. A burn rate
of 1 means the error budget will be exactly exhausted over the SLO window.
A burn rate greater than 1 means it is being consumed faster than allowed.
Requires the histogram to have been created with both `halfLife` and
`threshold` options.

```js
const { createHistogram } = require('node:perf_hooks');

// Track latency with a 200ms SLO threshold, half-life of 100 samples
const h = createHistogram({ halfLife: 100, threshold: 200_000_000 });

// ... record latency values ...

// Check burn rate against a 99.9% SLO
const rate = h.burnRate(0.999);
if (rate > 1) {
  console.log(`SLO burn rate: ${rate.toFixed(2)}x — error budget depleting`);
}
```

### `histogram.count`

Added in: v17.4.0, v16.14.0

- Type: [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type)

The number of samples recorded by the histogram.

### `histogram.countBigInt`

Added in: v17.4.0, v16.14.0

- Type: [`<bigint>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#bigint_type)

The number of samples recorded by the histogram.

### `histogram.ccdf(value)`

Added in: v26.8.0

- `value` [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) The value to query.
- Returns: [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) A probability between 0.0 and 1.0.

Returns the complementary cumulative distribution function (CCDF) value
for the given value, representing the probability that a recorded value
will exceed `value`. Equivalent to `1 - histogram.cdf(value)`.

### `histogram.cdf(value)`

Added in: v26.8.0

- `value` [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) The value to query.
- Returns: [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) A probability between 0.0 and 1.0.

Returns the cumulative distribution function (CDF) value for the given
value, representing the probability that a recorded value will be less
than or equal to `value`. This is the inverse operation of
`histogram.percentile()`.

### `histogram.cliffsD(other)`

Added in: v26.8.0

- `other` [`<Histogram>`](41-perf-hooks.md#class-histogram) The histogram to compare against.
- Returns: [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) A value between -1.0 and 1.0.

Computes [Cliff's delta](https://en.wikipedia.org/wiki/Effect_size#Cliff's_delta), a non-parametric effect size measure. Returns
the probability that a random value from this histogram exceeds a random
value from `other`, minus the reverse probability. A value of 1 means every
value in this histogram exceeds every value in `other`; -1 means the
opposite; 0 means no tendency in either direction.

### `histogram.cohensD(other)`

Added in: v26.8.0

- `other` [`<Histogram>`](41-perf-hooks.md#class-histogram) The histogram to compare against.
- Returns: [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) The effect size.

Computes [Cohen's d](https://en.wikipedia.org/wiki/Effect_size#Cohen's_d) effect size, the standardized difference between the
means of this histogram and `other`, using the pooled standard deviation.
Positive values indicate this histogram has a higher mean. By convention,
|d| \< 0.2 is a small effect, 0.5 is medium, and 0.8 or greater is large.
Both histograms must have at least 2 recorded values; otherwise returns 0.

### `histogram.countAt(value)`

Added in: v26.8.0

- `value` [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) The value to query.
- Returns: [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type)

Returns the number of recorded values that fall within the equivalent
value range of the given value.

### `histogram.diff(other)`

Added in: v26.11.0

- `other` [`<Histogram>`](41-perf-hooks.md#class-histogram) An earlier snapshot of this histogram.
- Returns: [`<Histogram>`](41-perf-hooks.md#class-histogram)

Returns a new [`<Histogram>`](41-perf-hooks.md#class-histogram) containing the values recorded in this histogram after `other` was taken. Neither histogram is changed. To get the values recorded
during each interval without calling `reset()`, compute each difference from a
snapshot and keep that snapshot as the baseline for the next interval:

```js
const { monitorEventLoopDelay } = require('node:perf_hooks');

const histogram = monitorEventLoopDelay();
histogram.enable();
let previous = histogram.snapshot();

setInterval(() => {
  const current = histogram.snapshot();
  // After a reset, use everything recorded since the reset.
  const delta = current.resetCount === previous.resetCount ?
    current.diff(previous) : current;
  console.log(delta.percentile(99));
  previous = current;
}, 10_000);
```

The `count`, `exceeds`, and bucket counts of the returned histogram are the
differences between the two histograms. Its `min` and `max` are computed from
the buckets of the difference, it has no EWMA state, and its `resetCount` is
`0`.

This method throws:

- `ERR_INVALID_ARG_VALUE` if `other` has a different `lowest`, `highest`, or
  `figures` configuration.
- `ERR_INVALID_STATE` if values have been removed from this histogram since
  `other` was taken, which is the case when the `resetCount` of the two
  histograms differs.
- `ERR_INVALID_ARG_VALUE` if `other` contains values that are not in this
  histogram, for example because the histograms were passed in the wrong order.

### `histogram.exceeds`

Added in: v11.10.0

- Type: [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type)

The number of values that were not recorded because they exceeded the
histogram's highest recordable value.

### `histogram.exceedsBigInt`

Added in: v17.4.0, v16.14.0

- Type: [`<bigint>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#bigint_type)

The number of values that were not recorded because they exceeded the
histogram's highest recordable value.

### `histogram.export()`

Added in: v26.9.0

**History**

| Version | Changes |
| --- | --- |
| v26.11.0 | The output uses format version 2. |

- Returns: [`<Uint8Array>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Uint8Array)

Serializes the histogram to a [CBOR](https://www.rfc-editor.org/rfc/rfc8949)-encoded (RFC 8949) `Uint8Array`
suitable for transmission or persistent storage. The encoding uses a
delta-encoded sparse representation of the bucket counts, so the output size
scales with the number of distinct recorded values rather than the total
bucket count.

The output includes all histogram configuration, bucket data, and EWMA
state (when enabled). It can be reconstructed into a new histogram using
[`perf_hooks.importHistogram()`](#perf_hooksimporthistogramdata).

The CBOR payload is a map with integer keys:

| Key | Type | Field |
| --- | --- | --- |
| 0 | uint | Format version (currently 2) |
| 1 | uint | Lowest discernible value |
| 2 | uint | Highest trackable value |
| 3 | uint | Significant figures |
| 4 | uint | Total count |
| 5 | uint | Min value |
| 6 | uint | Max value |
| 7 | uint | Normalizing index offset |
| 8 | float64 | Conversion ratio |
| 9 | uint | Counts array length |
| 10 | array | Delta-encoded sparse counts `[delta, c, ...]` |
| 11 | map | EWMA state (omitted when disabled) |

Any standard CBOR decoder can parse the output.

#### Histogram export format compatibility

[`perf_hooks.importHistogram()`](#perf_hooksimporthistogramdata) accepts every format version that
`histogram.export()` has produced:

- Version 1 was produced by Node.js v26.9.0. Data with a version 1 key, or
  without a version key, is imported with the original semantics: keys
  that are not listed above are rejected.
- Version 2 has the same layout as version 1. Keys that are not recognized
  are ignored, so later versions of Node.js can add fields to version 2
  data without changing the version, and the data remains importable.

Data with any other version is rejected.

When the total count, min, or max value is absent, it is derived from the
bucket counts. A total count that is present must match the bucket counts.

### `histogram.ewmaMean`

Added in: v26.8.0

- Type: [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type)

The exponentially weighted moving average of recorded values. Only active
when the histogram was created with a `halfLife` option greater than 0.
Returns `0` when EWMA is disabled or no values have been recorded.

### `histogram.ewmaStddev`

Added in: v26.8.0

- Type: [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type)

The exponentially weighted moving standard deviation. Only active when the
histogram was created with a `halfLife` option greater than 0. Returns `0`
when EWMA is disabled or no values have been recorded.

### `histogram.ewmaErrorRate`

Added in: v26.8.0

- Type: [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type)

The EWMA-smoothed probability of a recorded value exceeding the configured
`threshold`. Only active when the histogram was created with both `halfLife`
and `threshold` options. Returns `0` when not enabled or no values have been
recorded.

### `histogram.ksTest(other)`

Added in: v26.8.0

- `other` [`<Histogram>`](41-perf-hooks.md#class-histogram) The histogram to compare against.
- Returns: [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) The KS D-statistic, between 0.0 and 1.0.

Computes the Kolmogorov-Smirnov test statistic comparing this histogram's
distribution to `other`. A value of 0 indicates identical distributions;
values close to 1 indicate completely disjoint distributions. Useful for
detecting performance regressions by comparing before/after histograms.

### `histogram.kurtosis`

Added in: v26.8.0

- Type: [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type)

The excess kurtosis of the recorded values. Measures the heaviness of the
distribution's tails relative to a normal distribution. Positive values
indicate heavier tails (more extreme outliers); negative values indicate
lighter tails.

### `histogram.linearBuckets(stepSize)`

Added in: v26.8.0

- `stepSize` [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) The width of each linear bucket.
- Returns: [`<Map>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Map) A map of bucket boundary values to counts.

Returns the histogram data rebucketed into linearly-spaced intervals
of `stepSize`. Useful for visualization and export.

### `histogram.logBuckets(firstBucket, base)`

Added in: v26.8.0

- `firstBucket` [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) The value of the first bucket boundary.
- `base` [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) The logarithmic base for bucket width growth. Must be > 1.
- Returns: [`<Map>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Map) A map of bucket boundary values to counts.

Returns the histogram data rebucketed into logarithmically-spaced
intervals, where each bucket's width is multiplied by `base`.
Useful for visualization and export.

### `histogram.mannWhitneyTest(other)`

Added in: v26.8.0

- `other` [`<Histogram>`](41-perf-hooks.md#class-histogram) The histogram to compare against.
- Returns: [`<Object>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Object)
  - `uStatistic` [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) The Mann-Whitney U statistic.
  - `zScore` [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) The z-score (normal approximation).
  - `pValue` [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) Two-tailed p-value.

Performs a [Mann-Whitney U test](https://en.wikipedia.org/wiki/Mann%E2%80%93Whitney_U_test) comparing whether this histogram tends to
produce larger or smaller values than `other`. Unlike `welchTest()`, this is a
non-parametric test that makes no assumptions about the shape of the
distributions. Uses the normal approximation with tie correction for the
p-value.

### `histogram.max`

Added in: v11.10.0

- Type: [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type)

The maximum recorded event loop delay.

### `histogram.maxBigInt`

Added in: v17.4.0, v16.14.0

- Type: [`<bigint>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#bigint_type)

The maximum recorded event loop delay.

### `histogram.mean`

Added in: v11.10.0

- Type: [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type)

The mean of the recorded event loop delays.

### `histogram.meanCI([options])`

Added in: v26.9.0

- `options` [`<Object>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Object)
  - `confidence` [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) The confidence level for the interval, between
    0 and 1 (exclusive). **Default:** `0.95`.
- Returns: [`<Object>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Object)
  - `mean` [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) The mean estimate, equivalent to `histogram.mean`.
  - `lower` [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) The lower bound of the confidence interval.
  - `upper` [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) The upper bound of the confidence interval.

Returns a two-sided confidence interval for the mean using Student's
t-distribution and the sample standard error. A higher confidence level
produces a wider interval. This interval assumes that samples are independent
and approximately normally distributed, although the approximation is robust
for sufficiently large samples.

The result reflects the histogram's configured precision and is calculated
from the values represented by its buckets. With fewer than two recorded
values, `lower` and `upper` are `NaN`. When all recorded values are equal,
`lower` and `upper` equal `mean`.

```js
const { createHistogram } = require('node:perf_hooks');

const h = createHistogram();
for (let i = 1; i <= 100; i++) h.record(i);

const { mean, lower, upper } = h.meanCI();
console.log(`mean=${mean}, 95% CI=[${lower}, ${upper}]`);
```

### `histogram.min`

Added in: v11.10.0

- Type: [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type)

The minimum recorded event loop delay.

### `histogram.minBigInt`

Added in: v17.4.0, v16.14.0

- Type: [`<bigint>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#bigint_type)

The minimum recorded event loop delay.

### `histogram.percentile(percentile)`

Added in: v11.10.0

- `percentile` [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) A percentile value in the range (0, 100].
- Returns: [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type)

Returns the value at the given percentile.

### `histogram.percentileBigInt(percentile)`

Added in: v17.4.0, v16.14.0

- `percentile` [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) A percentile value in the range (0, 100].
- Returns: [`<bigint>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#bigint_type)

Returns the value at the given percentile.

### `histogram.percentileCI(percentile[, options])`

Added in: v26.8.0

- `percentile` [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) A percentile value in the range (0, 100].
- `options` [`<Object>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Object)
  - `confidence` [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) The confidence level for the interval, between
    0 and 1 (exclusive). **Default:** `0.95`.
- Returns: [`<Object>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Object)
  - `value` [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) The point estimate (same as `histogram.percentile()`).
  - `lower` [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) The lower bound of the confidence interval.
  - `upper` [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) The upper bound of the confidence interval.

Returns a confidence interval for the given percentile using the exact
binomial method. With fewer samples, the interval will be wider, reflecting
the greater uncertainty in the percentile estimate. Requires at least 2
recorded values; with fewer than 2, `lower` and `upper` will equal `value`.

```js
const { createHistogram } = require('node:perf_hooks');

const h = createHistogram();
for (let i = 0; i < 1000; i++) {
  h.record(Math.floor(Math.random() * 100));
}

const ci = h.percentileCI(99);
console.log(ci.value);  // The p99 point estimate
console.log(ci.lower);  // The lower bound (95% confidence)
console.log(ci.upper);  // The upper bound (95% confidence)
```

### `histogram.percentiles`

Added in: v11.10.0

- Type: [`<Map>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Map)

Returns a `Map` object detailing the accumulated percentile distribution.

### `histogram.percentilesBigInt`

Added in: v17.4.0, v16.14.0

- Type: [`<Map>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Map)

Returns a `Map` object detailing the accumulated percentile distribution.

### `histogram.percentilesAt(percentiles)`

Added in: v26.8.0

- `percentiles` [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type)[] An array of percentile values in the range (0, 100].
- Returns: [`<Map>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Map) A map of percentile values to their corresponding histogram
  values.

Returns the values at the specified percentiles, computed in a single
efficient pass over the histogram data. More efficient than calling
`histogram.percentile()` multiple times.

### `histogram.qrde([options])`

Added in: v26.10.0

- `options` [`<Object>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Object)
  - `bins` [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) The number of equal-probability density bins to return.
    Must be between 1 and 1000. Cannot be used with `probabilities`.
    **Default:** `100`.
  - `probabilities` [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type)[] Custom probability boundaries. The array must
    contain between 2 and 1001 strictly increasing values, start with `0`, and
    end with `1`. Cannot be used with `bins`.
  - `dequantize` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) Controls whether repeated bucket values are spread
    deterministically over their equivalent-value ranges. May be `'none'`,
    `'hdr'`, or `'all'`. **Default:** `'hdr'`.
  - `cache` [`<boolean>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#boolean_type) When `true`, retains the expanded histogram snapshot for
    reuse by subsequent calls with `cache: true`. The snapshot is invalidated
    when the histogram is modified. **Default:** `false`.
- Returns: [`<Promise>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Promise) Fulfills with an [`<Object>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Object) containing:
  - `probabilities` [`<Float64Array>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Float64Array) The probability boundaries used by the
    estimate.
  - `quantiles` [`<Float64Array>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Float64Array) The quantiles at the probability boundaries.
  - `densities` [`<Float64Array>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Float64Array) The density within each quantile interval.
  - `count` [`<bigint>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#bigint_type) The number of values in the histogram snapshot.
  - `bucketCount` [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) The number of occupied HDR buckets.
  - `corrections` [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) The number of non-monotonic floating-point results
    that were clamped to the preceding quantile.
  - `dequantize` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) The selected dequantization mode.

Returns a quantile-respectful density estimate based on the Harrell-Davis
quantile estimator. By default, `bins` generates equal probability boundaries.
The `probabilities` option can instead focus the estimate on regions such as
p90, p99, p99.9, and p99.99. The density for interval `i` contains probability
mass `probabilities[i + 1] - probabilities[i]`. The histogram is snapshotted
when the method is called. Snapshot expansion and the estimate are calculated
in the libuv thread pool. Highly concentrated beta weights use a second-order
asymptotic approximation to avoid numerical convergence loss at large sample
counts.

Setting `cache` to `true` avoids repeating snapshot capture and expansion when
several estimates are requested from an unchanged histogram. The retained
snapshot uses memory proportional to the number of occupied HDR buckets and is
released when the histogram is next modified.

QRDE temporarily uses approximately one additional HDR count array plus 32
bytes per occupied bucket. With `cache: true`, the expanded 32-byte-per-bucket
snapshot remains allocated. The following estimates use `lowest: 1` and
`highest: Number.MAX_SAFE_INTEGER` and exclude allocator and JavaScript object
overhead:

| `figures` | Histogram | Maximum expanded snapshot | Peak cache-miss QRDE |
| --- | --- | --- | --- |
| 1 | 6.3 KiB | 25 KiB | 31 KiB |
| 2 | 47 KiB | 188 KiB | 235 KiB |
| 3 | 352 KiB | 1.4 MiB | 1.7 MiB |
| 4 | 5.0 MiB | 20 MiB | 25 MiB |
| 5 | 37 MiB | 148 MiB | 185 MiB |

The maximum snapshot column assumes every representable bucket is occupied.
Lower `highest` values reduce histogram and temporary copy sizes. Concurrent
calls that miss the cache each require their own temporary copy and expanded
snapshot.

HDR histograms aggregate observations into equivalent-value buckets. The
`'hdr'` dequantization mode models repeated values in buckets wider than one
unit as a continuous uniform distribution over the bucket resolution. This
reduces density artifacts introduced by HDR quantization while preserving
repeated unit-resolution values as point masses. The `'all'` mode also
dequantizes repeated unit-resolution values. Use `'none'` to calculate the
grouped Harrell-Davis estimator using bucket midpoints directly.

An empty histogram returns the requested `probabilities` but produces empty
`quantiles` and `densities` arrays. A non-dequantized interval whose quantile
boundaries are equal has an infinite density.

### `histogram.reset()`

Added in: v11.10.0

Resets the collected histogram data and increments `histogram.resetCount`.

### `histogram.resetCount`

Added in: v26.11.0

- Type: [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type)

The number of times values have been removed from this histogram by `reset()` or, for a [`<RecordableHistogram>`](41-perf-hooks.md#class-recordablehistogram-extends-histogram), `subtract()`. A snapshot has the `resetCount`
of its source at the time it was taken, so comparing the `resetCount` of two
snapshots shows whether the source was reset between them. See
[`histogram.diff()`](#histogramdiffother).

### `histogram.skewness`

Added in: v26.8.0

- Type: [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type)

The skewness of the recorded values. Measures the asymmetry of the
distribution. A positive value indicates a right-skewed distribution
(longer right tail, common for latency data); a negative value
indicates a left-skewed distribution.

### `histogram.snapshot()`

Added in: v26.11.0

- Returns: [`<Histogram>`](41-perf-hooks.md#class-histogram)

Returns a new, independent [`<Histogram>`](41-perf-hooks.md#class-histogram) containing a copy of this histogram's
current state: its configuration, recorded values, `exceeds` count, and EWMA
state. Values recorded into this histogram after this method returns, and later
calls to `reset()`, do not change the returned histogram. This provides a stable
view of a histogram that is still recording, such as an enabled [`<ELDHistogram>`](41-perf-hooks.md#class-eldhistogram-extends-histogram).

Values cannot be recorded into the returned histogram. Taking a snapshot copies
every bucket, so both its time and memory cost depend on the histogram's
`lowest`, `highest`, and `figures` configuration rather than on the number of
recorded values.

```js
const { monitorEventLoopDelay } = require('node:perf_hooks');

const histogram = monitorEventLoopDelay();
histogram.enable();

setTimeout(() => {
  const snapshot = histogram.snapshot();
  console.log(snapshot.percentile(99));
  histogram.disable();
}, 1000);
```

### `histogram.stddev`

Added in: v11.10.0

- Type: [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type)

The standard deviation of the recorded event loop delays.

### `histogram.welchTest(other[, options])`

Added in: v26.8.0

- `other` [`<Histogram>`](41-perf-hooks.md#class-histogram) The histogram to compare against.
- `options` [`<Object>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Object)
  - `confidence` [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) Confidence level for the interval, between 0 and 1. **Default:** `0.95`.
- Returns: [`<Object>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Object)
  - `tStatistic` [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) The Welch t-statistic.
  - `degreesOfFreedom` [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) Welch-Satterthwaite degrees of freedom.
  - `pValue` [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) Two-tailed p-value.
  - `confidenceInterval` [`<Object>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Object)
    - `lower` [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) Lower bound of the confidence interval on the
      difference of means.
    - `upper` [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) Upper bound.

Performs [Welch's t-test](https://en.wikipedia.org/wiki/Welch%27s_t-test) comparing the means of this histogram and `other`.
The p-value indicates the probability of observing a difference at least this
extreme under the null hypothesis that the two distributions have the same
mean. Both histograms must have at least 2 recorded values; otherwise the
result has `pValue` 1 and `tStatistic` 0.

## Class: `ELDHistogram extends Histogram`

A `Histogram` that records event loop delay, returned by
[`perf_hooks.monitorEventLoopDelay()`](#perf_hooksmonitoreventloopdelayoptions).

### `histogram.disable()`

Added in: v11.10.0

- Returns: [`<boolean>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#boolean_type)

Disables event loop delay sampling. Returns `true` if sampling was
stopped, `false` if it was already stopped.

### `histogram.enable()`

Added in: v11.10.0

- Returns: [`<boolean>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#boolean_type)

Enables event loop delay sampling. Returns `true` if sampling was
started, `false` if it was already started.

### `histogram[Symbol.dispose]()`

Added in: v24.2.0

Disables event loop delay sampling when the histogram is disposed.

```js
const { monitorEventLoopDelay } = require('node:perf_hooks');
{
  using hist = monitorEventLoopDelay({ resolution: 20 });
  hist.enable();
  // The histogram will be disabled when the block is exited.
}
```

### Cloning an `ELDHistogram`

[`<ELDHistogram>`](41-perf-hooks.md#class-eldhistogram-extends-histogram) instances can be cloned via [`<MessagePort>`](67-worker-threads.md#class-messageport). On the receiving end,
the histogram is cloned as a plain [`<Histogram>`](41-perf-hooks.md#class-histogram) object that does not implement
the `enable()` and `disable()` methods.

## Class: `RecordableHistogram extends Histogram`

Added in: v15.9.0, v14.18.0

### `histogram.add(other)`

Added in: v17.4.0, v16.14.0

- `other` [`<RecordableHistogram>`](41-perf-hooks.md#class-recordablehistogram-extends-histogram)

Adds the values from `other` to this histogram.

### `histogram.record(val)`

Added in: v15.9.0, v14.18.0

**History**

| Version | Changes |
| --- | --- |
| v26.11.0 | Recording `0` is now supported. |

- `val` [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) | [`<bigint>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#bigint_type) The amount to record in the histogram. Must be an
  integer greater than or equal to `0`.

Values smaller than the histogram's `lowest` option, including `0`, might not
be distinguishable from each other.

### `histogram.recordDelta()`

Added in: v15.9.0, v14.18.0

Calculates the amount of time (in nanoseconds) that has passed since the
previous call to `recordDelta()` and records that amount in the histogram.

### `histogram.recordCorrected(val, expectedInterval)`

Added in: v26.8.0

**History**

| Version | Changes |
| --- | --- |
| v26.11.0 | Recording `0` is now supported. |

- `val` [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) | [`<bigint>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#bigint_type) The value to record. Must be an integer greater than or
  equal to `0`.
- `expectedInterval` [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) | [`<bigint>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#bigint_type) The expected recording interval.

Records a value with coordinated omission correction. When a system stall
prevents timely recording, this method backfills intermediate values at
`expectedInterval` steps between the previously recorded value and `val`.
This compensates for measurement gaps that would otherwise underrepresent
latency.

### `histogram.subtract(other)`

Added in: v26.8.0

- `other` [`<RecordableHistogram>`](41-perf-hooks.md#class-recordablehistogram-extends-histogram)

Subtracts the values of `other` from this histogram. Both histograms should
have compatible configurations. Bucket counts that would become negative
are clamped to zero. Increments `histogram.resetCount`.

## Class: `SlidingWindowHistogram`

Added in: v26.10.0

Records values into a lazily rotated ring of histogram chunks. Instances are
created using [`perf_hooks.createSlidingWindowHistogram()`](#perf_hookscreateslidingwindowhistogramoptions) and cannot be
constructed directly. A `SlidingWindowHistogram` does not extend [`<Histogram>`](41-perf-hooks.md#class-histogram);
call `snapshot()` to materialize the current window as a [`<Histogram>`](41-perf-hooks.md#class-histogram).

`SlidingWindowHistogram` instances cannot be cloned or transferred through a
[`<MessagePort>`](67-worker-threads.md#class-messageport).

### `slidingWindowHistogram.record(val)`

Added in: v26.10.0

- `val` [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) | [`<bigint>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#bigint_type) The amount to record. Must be an integer greater than or
  equal to `0`.

Records `val` in the current chunk. For a count-based window, every call that
reaches the native histogram counts toward rotation, including values which
exceed the configured `highest` value.

### `slidingWindowHistogram.reset()`

Added in: v26.10.0

Invalidates all chunks in the current window. Allocated chunks are reset
lazily when reused.

### `slidingWindowHistogram.snapshot()`

Added in: v26.10.0

- Returns: [`<Histogram>`](41-perf-hooks.md#class-histogram)

Materializes the current window as a new, independent [`<Histogram>`](41-perf-hooks.md#class-histogram). Values
recorded or expired after this method returns do not change the returned
histogram. Materialization allocates one histogram and merges every retained
chunk.

## Histogram analysis examples

The `Histogram` class provides statistical analysis methods useful for
performance monitoring, SLO enforcement, and regression detection.

### Distribution shape analysis

```js
const { createHistogram } = require('node:perf_hooks');

const h = createHistogram();

// Simulate a right-skewed latency distribution
for (let i = 0; i < 1000; i++) {
  h.record(Math.ceil(Math.random() * 100));
}
// Add some outliers
for (let i = 0; i < 10; i++) {
  h.record(500 + Math.ceil(Math.random() * 500));
}

console.log('Skewness:', h.skewness.toFixed(4));  // Positive = right-skewed
console.log('Kurtosis:', h.kurtosis.toFixed(4));  // Positive = heavy tails
```

### SLO monitoring with CDF

```js
const { createHistogram } = require('node:perf_hooks');

const latency = createHistogram();

// Record request latencies (in nanoseconds)...

// "What fraction of requests complete within 100ms?"
const withinSLO = latency.cdf(100_000_000);
console.log(`${(withinSLO * 100).toFixed(1)}% of requests within SLO`);

// "What fraction of requests exceed 500ms?"
const violating = latency.ccdf(500_000_000);
console.log(`${(violating * 100).toFixed(1)}% of requests violating SLO`);
```

### SLO burn rate monitoring

```js
const { createHistogram } = require('node:perf_hooks');

// Track latency with EWMA (half-life 100 samples) and a 200ms SLO threshold
const latency = createHistogram({
  halfLife: 100,
  threshold: 200_000_000,  // 200ms in nanoseconds
});

// Record request latencies...

// Smoothed error rate: probability of exceeding the threshold
console.log(`Error rate: ${(latency.ewmaErrorRate * 100).toFixed(2)}%`);

// Burn rate against a 99.9% SLO
// >1 means the error budget is depleting faster than allowed
const rate = latency.burnRate(0.999);
console.log(`Burn rate: ${rate.toFixed(2)}x`);

// EWMA mean and stddev track the smoothed latency
console.log(`EWMA latency: ${latency.ewmaMean.toFixed(0)}ns`);
console.log(`EWMA stddev:  ${latency.ewmaStddev.toFixed(0)}ns`);
```

### Regression detection with KS test

```js
const { createHistogram } = require('node:perf_hooks');

const baseline = createHistogram();
const current = createHistogram();

// Record baseline and current latencies...

// D-statistic: 0 = identical, 1 = completely different
const d = baseline.ksTest(current);
if (d > 0.1) {
  console.log(`Possible regression detected (D=${d.toFixed(4)})`);
}
```

### Batch percentile queries

```js
const { createHistogram } = require('node:perf_hooks');

const h = createHistogram();
// Record values...

// Efficiently query common monitoring percentiles in one pass
const p = h.percentilesAt([50, 75, 90, 95, 99, 99.9]);
console.log('p50:', p.get(50));
console.log('p99:', p.get(99));
```

### Snapshot diffing with subtract

```js
const { createHistogram } = require('node:perf_hooks');

const total = createHistogram();
const snapshot = createHistogram();

// Record values into total...
// Periodically snapshot for "last interval" analysis:
snapshot.add(total);

// Later, take a new snapshot and diff:
const newSnapshot = createHistogram();
newSnapshot.add(total);
newSnapshot.subtract(snapshot);
// newSnapshot now contains only the values recorded since the last snapshot
console.log('Recent p99:', newSnapshot.percentile(99));
```

### Benchmark comparison with Welch's t-test

```js
const { createHistogram } = require('node:perf_hooks');

const baseline = createHistogram();
const candidate = createHistogram();

// Record operation rates from the old and new builds...

const result = baseline.welchTest(candidate);
const improvement = ((candidate.mean - baseline.mean) / baseline.mean * 100);

console.log(`Improvement: ${improvement.toFixed(2)}%`);
console.log(`p-value: ${result.pValue.toFixed(6)}`);
console.log(`95% CI: [${result.confidenceInterval.lower.toFixed(2)}, ` +
            `${result.confidenceInterval.upper.toFixed(2)}]`);

if (result.pValue < 0.05) {
  const d = baseline.cohensD(candidate);
  console.log(`Statistically significant (Cohen's d = ${d.toFixed(4)})`);
}
```

### Effect size with Cliff's delta

```js
const { createHistogram } = require('node:perf_hooks');

const before = createHistogram();
const after = createHistogram();

// Record latencies before and after a change...

const delta = before.cliffsD(after);
// A delta > 0: before tends to produce larger values (improvement)
// A delta < 0: after tends to produce larger values (regression)
console.log(`Cliff's delta: ${delta.toFixed(4)}`);
```

## Examples

### Measuring the duration of async operations

The following example uses the [Async Hooks](05-async-hooks.md) and Performance APIs to measure
the actual duration of a Timeout operation (including the amount of time it took
to execute the callback).

*ES module version:*

```js
import { createHook } from 'node:async_hooks';
import { performance, PerformanceObserver } from 'node:perf_hooks';

const set = new Set();
const hook = createHook({
  init(id, type) {
    if (type === 'Timeout') {
      performance.mark(`Timeout-${id}-Init`);
      set.add(id);
    }
  },
  destroy(id) {
    if (set.has(id)) {
      set.delete(id);
      performance.mark(`Timeout-${id}-Destroy`);
      performance.measure(`Timeout-${id}`,
                          `Timeout-${id}-Init`,
                          `Timeout-${id}-Destroy`);
    }
  },
});
hook.enable();

const obs = new PerformanceObserver((list, observer) => {
  console.log(list.getEntries()[0]);
  performance.clearMarks();
  performance.clearMeasures();
  observer.disconnect();
});
obs.observe({ entryTypes: ['measure'], buffered: true });

setTimeout(() => {}, 1000);
```

*CommonJS version:*

```js
const async_hooks = require('node:async_hooks');
const {
  performance,
  PerformanceObserver,
} = require('node:perf_hooks');

const set = new Set();
const hook = async_hooks.createHook({
  init(id, type) {
    if (type === 'Timeout') {
      performance.mark(`Timeout-${id}-Init`);
      set.add(id);
    }
  },
  destroy(id) {
    if (set.has(id)) {
      set.delete(id);
      performance.mark(`Timeout-${id}-Destroy`);
      performance.measure(`Timeout-${id}`,
                          `Timeout-${id}-Init`,
                          `Timeout-${id}-Destroy`);
    }
  },
});
hook.enable();

const obs = new PerformanceObserver((list, observer) => {
  console.log(list.getEntries()[0]);
  performance.clearMarks();
  performance.clearMeasures();
  observer.disconnect();
});
obs.observe({ entryTypes: ['measure'] });

setTimeout(() => {}, 1000);
```

### Measuring how long it takes to load dependencies

The following example measures the duration of `require()` operations to load
dependencies:

*ES module version:*

```js
import { performance, PerformanceObserver } from 'node:perf_hooks';

// Activate the observer
const obs = new PerformanceObserver((list) => {
  const entries = list.getEntries();
  entries.forEach((entry) => {
    console.log(`import('${entry[0]}')`, entry.duration);
  });
  performance.clearMarks();
  performance.clearMeasures();
  obs.disconnect();
});
obs.observe({ entryTypes: ['function'], buffered: true });

const timedImport = performance.timerify(async (module) => {
  return await import(module);
});

await timedImport('some-module');
```

*CommonJS version:*

```js
const {
  performance,
  PerformanceObserver,
} = require('node:perf_hooks');
const mod = require('node:module');

// Monkey patch the require function
mod.Module.prototype.require =
  performance.timerify(mod.Module.prototype.require);
require = performance.timerify(require);

// Activate the observer
const obs = new PerformanceObserver((list) => {
  const entries = list.getEntries();
  entries.forEach((entry) => {
    console.log(`require('${entry[0]}')`, entry.duration);
  });
  performance.clearMarks();
  performance.clearMeasures();
  obs.disconnect();
});
obs.observe({ entryTypes: ['function'] });

require('some-module');
```

### Measuring how long one HTTP round-trip takes

The following example is used to trace the time spent by HTTP client
(`OutgoingMessage`) and HTTP request (`IncomingMessage`). For HTTP client,
it means the time interval between starting the request and receiving the
response, and for HTTP request, it means the time interval between receiving
the request and sending the response:

*ES module version:*

```js
import { PerformanceObserver } from 'node:perf_hooks';
import { createServer, get } from 'node:http';

const obs = new PerformanceObserver((items) => {
  items.getEntries().forEach((item) => {
    console.log(item);
  });
});

obs.observe({ entryTypes: ['http'] });

const PORT = 8080;

createServer((req, res) => {
  res.end('ok');
}).listen(PORT, () => {
  get(`http://127.0.0.1:${PORT}`);
});
```

*CommonJS version:*

```js
const { PerformanceObserver } = require('node:perf_hooks');
const http = require('node:http');

const obs = new PerformanceObserver((items) => {
  items.getEntries().forEach((item) => {
    console.log(item);
  });
});

obs.observe({ entryTypes: ['http'] });

const PORT = 8080;

http.createServer((req, res) => {
  res.end('ok');
}).listen(PORT, () => {
  http.get(`http://127.0.0.1:${PORT}`);
});
```

### Measuring how long the `net.connect` (only for TCP) takes when the connection is successful

*ES module version:*

```js
import { PerformanceObserver } from 'node:perf_hooks';
import { connect, createServer } from 'node:net';

const obs = new PerformanceObserver((items) => {
  items.getEntries().forEach((item) => {
    console.log(item);
  });
});
obs.observe({ entryTypes: ['net'] });
const PORT = 8080;
createServer((socket) => {
  socket.destroy();
}).listen(PORT, () => {
  connect(PORT);
});
```

*CommonJS version:*

```js
const { PerformanceObserver } = require('node:perf_hooks');
const net = require('node:net');
const obs = new PerformanceObserver((items) => {
  items.getEntries().forEach((item) => {
    console.log(item);
  });
});
obs.observe({ entryTypes: ['net'] });
const PORT = 8080;
net.createServer((socket) => {
  socket.destroy();
}).listen(PORT, () => {
  net.connect(PORT);
});
```

### Measuring how long the DNS takes when the request is successful

*ES module version:*

```js
import { PerformanceObserver } from 'node:perf_hooks';
import { lookup, promises } from 'node:dns';

const obs = new PerformanceObserver((items) => {
  items.getEntries().forEach((item) => {
    console.log(item);
  });
});
obs.observe({ entryTypes: ['dns'] });
lookup('localhost', () => {});
promises.resolve('localhost');
```

*CommonJS version:*

```js
const { PerformanceObserver } = require('node:perf_hooks');
const dns = require('node:dns');
const obs = new PerformanceObserver((items) => {
  items.getEntries().forEach((item) => {
    console.log(item);
  });
});
obs.observe({ entryTypes: ['dns'] });
dns.lookup('localhost', () => {});
dns.promises.resolve('localhost');
```

[Contents](../README.md) | [Previous: Path](40-path.md) | [Next: Permissions](42-permissions.md)
