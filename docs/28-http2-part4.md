[Contents](../README.md) | [Previous: HTTP/2, part 3 of 4](28-http2-part3.md) | [Next: HTTPS](29-https.md)

Source: [https://nodejs.org/docs/latest/api/http2.html](https://nodejs.org/docs/latest/api/http2.html)

Part 4 of 4 of this section.

# HTTP/2 (part 4 of 4)

## Collecting HTTP/2 performance metrics

The [Performance Observer](41-perf-hooks.md) API can be used to collect basic performance
metrics for each `Http2Session` and `Http2Stream` instance.

*ES module version:*

```js
import { PerformanceObserver } from 'node:perf_hooks';

const obs = new PerformanceObserver((items) => {
  const entry = items.getEntries()[0];
  console.log(entry.entryType);  // prints 'http2'
  if (entry.name === 'Http2Session') {
    // Entry contains statistics about the Http2Session
  } else if (entry.name === 'Http2Stream') {
    // Entry contains statistics about the Http2Stream
  }
});
obs.observe({ entryTypes: ['http2'] });
```

*CommonJS version:*

```js
const { PerformanceObserver } = require('node:perf_hooks');

const obs = new PerformanceObserver((items) => {
  const entry = items.getEntries()[0];
  console.log(entry.entryType);  // prints 'http2'
  if (entry.name === 'Http2Session') {
    // Entry contains statistics about the Http2Session
  } else if (entry.name === 'Http2Stream') {
    // Entry contains statistics about the Http2Stream
  }
});
obs.observe({ entryTypes: ['http2'] });
```

The `entryType` property of the `PerformanceEntry` will be equal to `'http2'`.

The `name` property of the `PerformanceEntry` will be equal to either
`'Http2Stream'` or `'Http2Session'`.

If `name` is equal to `Http2Stream`, the `PerformanceEntry` will contain the
following additional properties:

- `bytesRead` [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) The number of `DATA` frame bytes received for this
  `Http2Stream`.
- `bytesWritten` [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) The number of `DATA` frame bytes sent for this
  `Http2Stream`.
- `id` [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) The identifier of the associated `Http2Stream`
- `timeToFirstByte` [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) The number of milliseconds elapsed between the `PerformanceEntry` `startTime` and the reception of the first `DATA` frame.
- `timeToFirstByteSent` [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) The number of milliseconds elapsed between
  the `PerformanceEntry` `startTime` and sending of the first `DATA` frame.
- `timeToFirstHeader` [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) The number of milliseconds elapsed between the `PerformanceEntry` `startTime` and the reception of the first header.

If `name` is equal to `Http2Session`, the `PerformanceEntry` will contain the
following additional properties:

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

## Note on `:authority` and `host`

HTTP/2 requires requests to have either the `:authority` pseudo-header
or the `host` header. Prefer `:authority` when constructing an HTTP/2
request directly, and `host` when converting from HTTP/1 (in proxies,
for instance).

The compatibility API falls back to `host` if `:authority` is not
present. See [`request.authority`](28-http2-part3.md#requestauthority) for more information. However,
if you don't use the compatibility API (or use `req.headers` directly),
you need to implement any fall-back behavior yourself.

[Contents](../README.md) | [Previous: HTTP/2, part 3 of 4](28-http2-part3.md) | [Next: HTTPS](29-https.md)
