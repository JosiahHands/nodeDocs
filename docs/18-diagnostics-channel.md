[Contents](../README.md) | [Previous: Deprecated APIs](17-deprecations.md) | [Next: DNS](19-dns.md)

Source: [https://nodejs.org/docs/latest/api/diagnostics_channel.html](https://nodejs.org/docs/latest/api/diagnostics_channel.html)

# Diagnostics Channel

Added in: v15.1.0, v14.17.0

**History**

| Version | Changes |
| --- | --- |
| v19.2.0, v18.13.0 | diagnostics\_channel is now Stable. |

[Stability: 2](01-documentation.md#stability-index) - Stable

The `node:diagnostics_channel` module provides an API to create named channels
to report arbitrary message data for diagnostics purposes.

It can be accessed using:

*ES module version:*

```js
import diagnostics_channel from 'node:diagnostics_channel';
```

*CommonJS version:*

```js
const diagnostics_channel = require('node:diagnostics_channel');
```

It is intended that a module writer wanting to report diagnostics messages
will create one or many top-level channels to report messages through.
Channels may also be acquired at runtime but it is not encouraged
due to the additional overhead of doing so. Channels may be exported for
convenience, but as long as the name is known it can be acquired anywhere.

If you intend for your module to produce diagnostics data for others to
consume it is recommended that you include documentation of what named
channels are used along with the shape of the message data. Channel names
should generally include the module name to avoid collisions with data from
other modules.

## Public API

### Overview

Following is a simple overview of the public API.

*ES module version:*

```js
import diagnostics_channel from 'node:diagnostics_channel';

// Get a reusable channel object
const channel = diagnostics_channel.channel('my-channel');

function onMessage(message, name) {
  // Received data
}

// Subscribe to the channel
diagnostics_channel.subscribe('my-channel', onMessage);

// Check if the channel has an active subscriber
if (channel.hasSubscribers) {
  // Publish data to the channel
  channel.publish({
    some: 'data',
  });
}

// Unsubscribe from the channel
diagnostics_channel.unsubscribe('my-channel', onMessage);
```

*CommonJS version:*

```js
const diagnostics_channel = require('node:diagnostics_channel');

// Get a reusable channel object
const channel = diagnostics_channel.channel('my-channel');

function onMessage(message, name) {
  // Received data
}

// Subscribe to the channel
diagnostics_channel.subscribe('my-channel', onMessage);

// Check if the channel has an active subscriber
if (channel.hasSubscribers) {
  // Publish data to the channel
  channel.publish({
    some: 'data',
  });
}

// Unsubscribe from the channel
diagnostics_channel.unsubscribe('my-channel', onMessage);
```

#### `diagnostics_channel.hasSubscribers(name)`

Added in: v15.1.0, v14.17.0

- `name` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) | [`<symbol>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#symbol_type) The channel name
- Returns: [`<boolean>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#boolean_type) If there are active subscribers

Check if there are active subscribers to the named channel. This is helpful if
the message you want to send might be expensive to prepare.

This API is optional but helpful when trying to publish messages from very
performance-sensitive code.

*ES module version:*

```js
import diagnostics_channel from 'node:diagnostics_channel';

if (diagnostics_channel.hasSubscribers('my-channel')) {
  // There are subscribers, prepare and publish message
}
```

*CommonJS version:*

```js
const diagnostics_channel = require('node:diagnostics_channel');

if (diagnostics_channel.hasSubscribers('my-channel')) {
  // There are subscribers, prepare and publish message
}
```

#### `diagnostics_channel.channel(name)`

Added in: v15.1.0, v14.17.0

- `name` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) | [`<symbol>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#symbol_type) The channel name
- Returns: [`<Channel>`](18-diagnostics-channel.md#class-channel) The named channel object

This is the primary entry-point for anyone wanting to publish to a named
channel. It produces a channel object which is optimized to reduce overhead at
publish time as much as possible.

*ES module version:*

```js
import diagnostics_channel from 'node:diagnostics_channel';

const channel = diagnostics_channel.channel('my-channel');
```

*CommonJS version:*

```js
const diagnostics_channel = require('node:diagnostics_channel');

const channel = diagnostics_channel.channel('my-channel');
```

#### `diagnostics_channel.subscribe(name, onMessage)`

Added in: v18.7.0, v16.17.0

- `name` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) | [`<symbol>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#symbol_type) The channel name
- `onMessage` [`<Function>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Function) The handler to receive channel messages
  - `message` [`<any>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#Data_types) The message data
  - `name` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) | [`<symbol>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#symbol_type) The name of the channel

Register a message handler to subscribe to this channel. This message handler
will be run synchronously whenever a message is published to the channel. Any
errors thrown in the message handler will trigger an [`'uncaughtException'`](43-process-part1.md#event-uncaughtexception).

*ES module version:*

```js
import diagnostics_channel from 'node:diagnostics_channel';

diagnostics_channel.subscribe('my-channel', (message, name) => {
  // Received data
});
```

*CommonJS version:*

```js
const diagnostics_channel = require('node:diagnostics_channel');

diagnostics_channel.subscribe('my-channel', (message, name) => {
  // Received data
});
```

#### `diagnostics_channel.unsubscribe(name, onMessage)`

Added in: v18.7.0, v16.17.0

- `name` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) | [`<symbol>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#symbol_type) The channel name
- `onMessage` [`<Function>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Function) The previous subscribed handler to remove
- Returns: [`<boolean>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#boolean_type) `true` if the handler was found, `false` otherwise.

Remove a message handler previously registered to this channel with
[`diagnostics_channel.subscribe(name, onMessage)`](#diagnostics_channelsubscribename-onmessage).

*ES module version:*

```js
import diagnostics_channel from 'node:diagnostics_channel';

function onMessage(message, name) {
  // Received data
}

diagnostics_channel.subscribe('my-channel', onMessage);

diagnostics_channel.unsubscribe('my-channel', onMessage);
```

*CommonJS version:*

```js
const diagnostics_channel = require('node:diagnostics_channel');

function onMessage(message, name) {
  // Received data
}

diagnostics_channel.subscribe('my-channel', onMessage);

diagnostics_channel.unsubscribe('my-channel', onMessage);
```

#### `diagnostics_channel.tracingChannel(nameOrChannels)`

Added in: v19.9.0, v18.19.0

**History**

| Version | Changes |
| --- | --- |
| v26.8.0 | Marked as stable. |

Stability: 2 - Stable

- `nameOrChannels` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) | [`<TracingChannel>`](18-diagnostics-channel.md#class-tracingchannel) Channel name or
  object containing all the [TracingChannel Channels](#tracingchannel-channels)
- Returns: [`<TracingChannel>`](18-diagnostics-channel.md#class-tracingchannel) Collection of channels to trace with

Creates a [`TracingChannel`](#class-tracingchannel) wrapper for the given
[TracingChannel Channels](#tracingchannel-channels). If a name is given, the corresponding tracing
channels will be created in the form of `tracing:${name}:${eventType}` where
`eventType` corresponds to the types of [TracingChannel Channels](#tracingchannel-channels).

*ES module version:*

```js
import diagnostics_channel from 'node:diagnostics_channel';

const channelsByName = diagnostics_channel.tracingChannel('my-channel');

// or...

const channelsByCollection = diagnostics_channel.tracingChannel({
  start: diagnostics_channel.channel('tracing:my-channel:start'),
  end: diagnostics_channel.channel('tracing:my-channel:end'),
  asyncStart: diagnostics_channel.channel('tracing:my-channel:asyncStart'),
  asyncEnd: diagnostics_channel.channel('tracing:my-channel:asyncEnd'),
  error: diagnostics_channel.channel('tracing:my-channel:error'),
});
```

*CommonJS version:*

```js
const diagnostics_channel = require('node:diagnostics_channel');

const channelsByName = diagnostics_channel.tracingChannel('my-channel');

// or...

const channelsByCollection = diagnostics_channel.tracingChannel({
  start: diagnostics_channel.channel('tracing:my-channel:start'),
  end: diagnostics_channel.channel('tracing:my-channel:end'),
  asyncStart: diagnostics_channel.channel('tracing:my-channel:asyncStart'),
  asyncEnd: diagnostics_channel.channel('tracing:my-channel:asyncEnd'),
  error: diagnostics_channel.channel('tracing:my-channel:error'),
});
```

#### `diagnostics_channel.boundedChannel(nameOrChannels)`

Added in: v26.1.0

Stability: 1 - Experimental

- `nameOrChannels` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) | [`<BoundedChannel>`](18-diagnostics-channel.md#class-boundedchannel) Channel name or
  object containing all the [BoundedChannel Channels](#boundedchannel-channels)
- Returns: [`<BoundedChannel>`](18-diagnostics-channel.md#class-boundedchannel) Collection of channels to trace with

Creates a [`BoundedChannel`](#class-boundedchannel) wrapper for the given channels. If a name is
given, the corresponding channels will be created in the form of
`tracing:${name}:${eventType}` where `eventType` is `start` or `end`.

A `BoundedChannel` is a simplified version of [`TracingChannel`](#class-tracingchannel) that only
traces synchronous operations. It only has `start` and `end` events, without
`asyncStart`, `asyncEnd`, or `error` events, making it suitable for tracing
operations that don't involve asynchronous continuations or error handling.

*ES module version:*

```js
import { boundedChannel, channel } from 'node:diagnostics_channel';

const wc = boundedChannel('my-operation');

// or...

const wc2 = boundedChannel({
  start: channel('tracing:my-operation:start'),
  end: channel('tracing:my-operation:end'),
});
```

*CommonJS version:*

```js
const { boundedChannel, channel } = require('node:diagnostics_channel');

const wc = boundedChannel('my-operation');

// or...

const wc2 = boundedChannel({
  start: channel('tracing:my-operation:start'),
  end: channel('tracing:my-operation:end'),
});
```

### Class: `Channel`

Added in: v15.1.0, v14.17.0

The class `Channel` represents an individual named channel within the data
pipeline. It is used to track subscribers and to publish messages when there
are subscribers present. It exists as a separate object to avoid channel
lookups at publish time, enabling very fast publish speeds and allowing
for heavy use while incurring very minimal cost. Channels are created with
[`diagnostics_channel.channel(name)`](#diagnostics_channelchannelname), constructing a channel directly
with `new Channel(name)` is not supported.

#### `channel.hasSubscribers`

Added in: v15.1.0, v14.17.0

- Returns: [`<boolean>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#boolean_type) If there are active subscribers

Check if there are active subscribers to this channel. This is helpful if
the message you want to send might be expensive to prepare.

This API is optional but helpful when trying to publish messages from very
performance-sensitive code.

*ES module version:*

```js
import diagnostics_channel from 'node:diagnostics_channel';

const channel = diagnostics_channel.channel('my-channel');

if (channel.hasSubscribers) {
  // There are subscribers, prepare and publish message
}
```

*CommonJS version:*

```js
const diagnostics_channel = require('node:diagnostics_channel');

const channel = diagnostics_channel.channel('my-channel');

if (channel.hasSubscribers) {
  // There are subscribers, prepare and publish message
}
```

#### `channel.publish(message)`

Added in: v15.1.0, v14.17.0

- `message` [`<any>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#Data_types) The message to send to the channel subscribers

Publish a message to any subscribers to the channel. This will trigger
message handlers synchronously so they will execute within the same context.

*ES module version:*

```js
import diagnostics_channel from 'node:diagnostics_channel';

const channel = diagnostics_channel.channel('my-channel');

channel.publish({
  some: 'message',
});
```

*CommonJS version:*

```js
const diagnostics_channel = require('node:diagnostics_channel');

const channel = diagnostics_channel.channel('my-channel');

channel.publish({
  some: 'message',
});
```

#### `channel.subscribe(onMessage)`

Added in: v15.1.0, v14.17.0

**History**

| Version | Changes |
| --- | --- |
| v24.8.0, v22.20.0 | Deprecation revoked. |
| v18.7.0, v16.17.0 | Documentation-only deprecation. |

- `onMessage` [`<Function>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Function) The handler to receive channel messages
  - `message` [`<any>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#Data_types) The message data
  - `name` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) | [`<symbol>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#symbol_type) The name of the channel

Register a message handler to subscribe to this channel. This message handler
will be run synchronously whenever a message is published to the channel. Any
errors thrown in the message handler will trigger an [`'uncaughtException'`](43-process-part1.md#event-uncaughtexception).

*ES module version:*

```js
import diagnostics_channel from 'node:diagnostics_channel';

const channel = diagnostics_channel.channel('my-channel');

channel.subscribe((message, name) => {
  // Received data
});
```

*CommonJS version:*

```js
const diagnostics_channel = require('node:diagnostics_channel');

const channel = diagnostics_channel.channel('my-channel');

channel.subscribe((message, name) => {
  // Received data
});
```

#### `channel.unsubscribe(onMessage)`

Added in: v15.1.0, v14.17.0

**History**

| Version | Changes |
| --- | --- |
| v24.8.0, v22.20.0 | Deprecation revoked. |
| v18.7.0, v16.17.0 | Documentation-only deprecation. |
| v17.1.0, v16.14.0, v14.19.0 | Added return value. Added to channels without subscribers. |

- `onMessage` [`<Function>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Function) The previous subscribed handler to remove
- Returns: [`<boolean>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#boolean_type) `true` if the handler was found, `false` otherwise.

Remove a message handler previously registered to this channel with
[`channel.subscribe(onMessage)`](#channelsubscribeonmessage).

*ES module version:*

```js
import diagnostics_channel from 'node:diagnostics_channel';

const channel = diagnostics_channel.channel('my-channel');

function onMessage(message, name) {
  // Received data
}

channel.subscribe(onMessage);

channel.unsubscribe(onMessage);
```

*CommonJS version:*

```js
const diagnostics_channel = require('node:diagnostics_channel');

const channel = diagnostics_channel.channel('my-channel');

function onMessage(message, name) {
  // Received data
}

channel.subscribe(onMessage);

channel.unsubscribe(onMessage);
```

#### `channel.bindStore(store[, transform])`

Added in: v19.9.0, v18.19.0

Stability: 1 - Experimental

- `store` [`<AsyncLocalStorage>`](04-async-context.md#class-asynclocalstorage) The store to which to bind the context data
- `transform` [`<Function>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Function) Transform context data before setting the store context

When [`channel.runStores(context, ...)`](#channelrunstorescontext-fn-thisarg-args) is called, the given context data
will be applied to any store bound to the channel. If the store has already been
bound the previous `transform` function will be replaced with the new one.
The `transform` function may be omitted to set the given context data as the
context directly.

*ES module version:*

```js
import diagnostics_channel from 'node:diagnostics_channel';
import { AsyncLocalStorage } from 'node:async_hooks';

const store = new AsyncLocalStorage();

const channel = diagnostics_channel.channel('my-channel');

channel.bindStore(store, (data) => {
  return { data };
});
```

*CommonJS version:*

```js
const diagnostics_channel = require('node:diagnostics_channel');
const { AsyncLocalStorage } = require('node:async_hooks');

const store = new AsyncLocalStorage();

const channel = diagnostics_channel.channel('my-channel');

channel.bindStore(store, (data) => {
  return { data };
});
```

#### `channel.unbindStore(store)`

Added in: v19.9.0, v18.19.0

Stability: 1 - Experimental

- `store` [`<AsyncLocalStorage>`](04-async-context.md#class-asynclocalstorage) The store to unbind from the channel.
- Returns: [`<boolean>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#boolean_type) `true` if the store was found, `false` otherwise.

Remove a message handler previously registered to this channel with
[`channel.bindStore(store)`](#channelbindstorestore-transform).

*ES module version:*

```js
import diagnostics_channel from 'node:diagnostics_channel';
import { AsyncLocalStorage } from 'node:async_hooks';

const store = new AsyncLocalStorage();

const channel = diagnostics_channel.channel('my-channel');

channel.bindStore(store);
channel.unbindStore(store);
```

*CommonJS version:*

```js
const diagnostics_channel = require('node:diagnostics_channel');
const { AsyncLocalStorage } = require('node:async_hooks');

const store = new AsyncLocalStorage();

const channel = diagnostics_channel.channel('my-channel');

channel.bindStore(store);
channel.unbindStore(store);
```

#### `channel.runStores(context, fn[, thisArg[, ...args]])`

Added in: v19.9.0, v18.19.0

Stability: 1 - Experimental

- `context` [`<any>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#Data_types) Message to send to subscribers and bind to stores
- `fn` [`<Function>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Function) Handler to run within the entered storage context
- `thisArg` [`<any>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#Data_types) The receiver to be used for the function call.
- `...args` [`<any>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#Data_types) Optional arguments to pass to the function.

Applies the given data to any AsyncLocalStorage instances bound to the channel
for the duration of the given function, then publishes to the channel within
the scope of that data is applied to the stores.

If a transform function was given to [`channel.bindStore(store)`](#channelbindstorestore-transform) it will be
applied to transform the message data before it becomes the context value for
the store. The prior storage context is accessible from within the transform
function in cases where context linking is required.

The context applied to the store should be accessible in any async code which
continues from execution which began during the given function, however
there are some situations in which [context loss](04-async-context.md#troubleshooting-context-loss) may occur.

*ES module version:*

```js
import diagnostics_channel from 'node:diagnostics_channel';
import { AsyncLocalStorage } from 'node:async_hooks';

const store = new AsyncLocalStorage();

const channel = diagnostics_channel.channel('my-channel');

channel.bindStore(store, (message) => {
  const parent = store.getStore();
  return new Span(message, parent);
});
channel.runStores({ some: 'message' }, () => {
  store.getStore(); // Span({ some: 'message' })
});
```

*CommonJS version:*

```js
const diagnostics_channel = require('node:diagnostics_channel');
const { AsyncLocalStorage } = require('node:async_hooks');

const store = new AsyncLocalStorage();

const channel = diagnostics_channel.channel('my-channel');

channel.bindStore(store, (message) => {
  const parent = store.getStore();
  return new Span(message, parent);
});
channel.runStores({ some: 'message' }, () => {
  store.getStore(); // Span({ some: 'message' })
});
```

#### `channel.withStoreScope(data)`

Added in: v26.1.0

Stability: 1 - Experimental

- `data` [`<any>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#Data_types) Message to bind to stores
- Returns: [`<RunStoresScope>`](18-diagnostics-channel.md#class-runstoresscope) Disposable scope object

Creates a disposable scope that binds the given data to any AsyncLocalStorage
instances bound to the channel and publishes it to subscribers. The scope
automatically restores the previous storage contexts when disposed.

This method enables the use of JavaScript's explicit resource management
(`using` syntax with `Symbol.dispose`) to manage store contexts without
closure wrapping.

*ES module version:*

```js
import { channel } from 'node:diagnostics_channel';
import { AsyncLocalStorage } from 'node:async_hooks';

const store = new AsyncLocalStorage();
const ch = channel('my-channel');

ch.bindStore(store, (message) => {
  return { ...message, timestamp: Date.now() };
});

{
  using scope = ch.withStoreScope({ request: 'data' });
  // Store is entered, data is published
  console.log(store.getStore()); // { request: 'data', timestamp: ... }
}
// Store is automatically restored on scope exit
```

*CommonJS version:*

```js
const { channel } = require('node:diagnostics_channel');
const { AsyncLocalStorage } = require('node:async_hooks');

const store = new AsyncLocalStorage();
const ch = channel('my-channel');

ch.bindStore(store, (message) => {
  return { ...message, timestamp: Date.now() };
});

{
  using scope = ch.withStoreScope({ request: 'data' });
  // Store is entered, data is published
  console.log(store.getStore()); // { request: 'data', timestamp: ... }
}
// Store is automatically restored on scope exit
```

### Class: `RunStoresScope`

Added in: v26.1.0

Stability: 1 - Experimental

The class `RunStoresScope` represents a disposable scope created by
[`channel.withStoreScope(data)`](#channelwithstorescopedata). It manages the lifecycle of store
contexts and ensures they are properly restored when the scope exits.

The scope must be used with the `using` syntax to ensure proper disposal.

### Class: `TracingChannel`

Added in: v19.9.0, v18.19.0

**History**

| Version | Changes |
| --- | --- |
| v26.8.0 | Marked as stable. |

Stability: 2 - Stable

The class `TracingChannel` is a collection of [TracingChannel Channels](#tracingchannel-channels) which
together express a single traceable action. It is used to formalize and
simplify the process of producing events for tracing application flow.
[`diagnostics_channel.tracingChannel()`](#diagnostics_channeltracingchannelnameorchannels) is used to construct a
`TracingChannel`. As with `Channel` it is recommended to create and reuse a
single `TracingChannel` at the top-level of the file rather than creating them
dynamically.

#### `tracingChannel.subscribe(subscribers)`

Added in: v19.9.0, v18.19.0

- `subscribers` [`<Object>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Object) Set of [TracingChannel Channels](#tracingchannel-channels) subscribers
  - `start` [`<Function>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Function) The [`start` event](#startevent) subscriber
  - `end` [`<Function>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Function) The [`end` event](#endevent) subscriber
  - `asyncStart` [`<Function>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Function) The [`asyncStart` event](#asyncstartevent) subscriber
  - `asyncEnd` [`<Function>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Function) The [`asyncEnd` event](#asyncendevent) subscriber
  - `error` [`<Function>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Function) The [`error` event](#errorevent) subscriber

Helper to subscribe a collection of functions to the corresponding channels.
This is the same as calling [`channel.subscribe(onMessage)`](#channelsubscribeonmessage) on each channel
individually.

*ES module version:*

```js
import diagnostics_channel from 'node:diagnostics_channel';

const channels = diagnostics_channel.tracingChannel('my-channel');

channels.subscribe({
  start(message) {
    // Handle start message
  },
  end(message) {
    // Handle end message
  },
  asyncStart(message) {
    // Handle asyncStart message
  },
  asyncEnd(message) {
    // Handle asyncEnd message
  },
  error(message) {
    // Handle error message
  },
});
```

*CommonJS version:*

```js
const diagnostics_channel = require('node:diagnostics_channel');

const channels = diagnostics_channel.tracingChannel('my-channel');

channels.subscribe({
  start(message) {
    // Handle start message
  },
  end(message) {
    // Handle end message
  },
  asyncStart(message) {
    // Handle asyncStart message
  },
  asyncEnd(message) {
    // Handle asyncEnd message
  },
  error(message) {
    // Handle error message
  },
});
```

#### `tracingChannel.unsubscribe(subscribers)`

Added in: v19.9.0, v18.19.0

- `subscribers` [`<Object>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Object) Set of [TracingChannel Channels](#tracingchannel-channels) subscribers
  - `start` [`<Function>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Function) The [`start` event](#startevent) subscriber
  - `end` [`<Function>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Function) The [`end` event](#endevent) subscriber
  - `asyncStart` [`<Function>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Function) The [`asyncStart` event](#asyncstartevent) subscriber
  - `asyncEnd` [`<Function>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Function) The [`asyncEnd` event](#asyncendevent) subscriber
  - `error` [`<Function>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Function) The [`error` event](#errorevent) subscriber
- Returns: [`<boolean>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#boolean_type) `true` if all handlers were successfully unsubscribed,
  and `false` otherwise.

Helper to unsubscribe a collection of functions from the corresponding channels.
This is the same as calling [`channel.unsubscribe(onMessage)`](#channelunsubscribeonmessage) on each channel
individually.

*ES module version:*

```js
import diagnostics_channel from 'node:diagnostics_channel';

const channels = diagnostics_channel.tracingChannel('my-channel');

channels.unsubscribe({
  start(message) {
    // Handle start message
  },
  end(message) {
    // Handle end message
  },
  asyncStart(message) {
    // Handle asyncStart message
  },
  asyncEnd(message) {
    // Handle asyncEnd message
  },
  error(message) {
    // Handle error message
  },
});
```

*CommonJS version:*

```js
const diagnostics_channel = require('node:diagnostics_channel');

const channels = diagnostics_channel.tracingChannel('my-channel');

channels.unsubscribe({
  start(message) {
    // Handle start message
  },
  end(message) {
    // Handle end message
  },
  asyncStart(message) {
    // Handle asyncStart message
  },
  asyncEnd(message) {
    // Handle asyncEnd message
  },
  error(message) {
    // Handle error message
  },
});
```

#### `tracingChannel.traceSync(fn[, context[, thisArg[, ...args]]])`

Added in: v19.9.0, v18.19.0

- `fn` [`<Function>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Function) Function to wrap a trace around
- `context` [`<Object>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Object) Shared object to correlate events through
- `thisArg` [`<any>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#Data_types) The receiver to be used for the function call
- `...args` [`<any>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#Data_types) Optional arguments to pass to the function
- Returns: [`<any>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#Data_types) The return value of the given function

Trace a synchronous function call. This will always produce a [`start` event](#startevent)
and [`end` event](#endevent) around the execution and may produce an [`error` event](#errorevent)
if the given function throws an error. This will run the given function using
[`channel.runStores(context, ...)`](#channelrunstorescontext-fn-thisarg-args) on the `start` channel which ensures all
events should have any bound stores set to match this trace context.

To ensure only correct trace graphs are formed, events will only be published
if subscribers are present prior to starting the trace. Subscriptions which are
added after the trace begins will not receive future events from that trace,
only future traces will be seen.

*ES module version:*

```js
import diagnostics_channel from 'node:diagnostics_channel';

const channels = diagnostics_channel.tracingChannel('my-channel');

channels.traceSync(() => {
  // Do something
}, {
  some: 'thing',
});
```

*CommonJS version:*

```js
const diagnostics_channel = require('node:diagnostics_channel');

const channels = diagnostics_channel.tracingChannel('my-channel');

channels.traceSync(() => {
  // Do something
}, {
  some: 'thing',
});
```

#### `tracingChannel.tracePromise(fn[, context[, thisArg[, ...args]]])`

Added in: v19.9.0, v18.19.0

**History**

| Version | Changes |
| --- | --- |
| v26.5.0 | Non-native-Promise thenables are now returned as-is, preserving their original type and methods. |
| v26.0.0 | Non-thenables will be returned with a warning. |

- `fn` [`<Function>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Function) Function to wrap a trace around
- `context` [`<Object>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Object) Shared object to correlate trace events through
- `thisArg` [`<any>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#Data_types) The receiver to be used for the function call
- `...args` [`<any>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#Data_types) Optional arguments to pass to the function
- Returns: [`<any>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#Data_types) The return value of the given function. If the return value
  is a Promise or thenable, tracing events will be published when it settles.
  If the return value is not a Promise or thenable, it is returned as-is and
  a warning is emitted.

Trace an asynchronous function call which returns a [`<Promise>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Promise) or [thenable object](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise#thenables). This will always produce a [`start` event](#startevent) and
[`end` event](#endevent) around the synchronous portion of the function execution, and
will produce an [`asyncStart` event](#asyncstartevent) and [`asyncEnd` event](#asyncendevent) when the
returned promise is resolved or rejected. It may also produce an
[`error` event](#errorevent) if the given function throws an error or the returned promise
is rejected. This will run the given function using
[`channel.runStores(context, ...)`](#channelrunstorescontext-fn-thisarg-args) on the `start` channel which ensures all
events should have any bound stores set to match this trace context.

If the value returned by `fn` is not a Promise or thenable, then it will be
returned with a warning, and no `asyncStart` or `asyncEnd` events will be
produced.

To ensure only correct trace graphs are formed, events will only be published
if subscribers are present prior to starting the trace. Subscriptions which are
added after the trace begins will not receive future events from that trace,
only future traces will be seen.

*ES module version:*

```js
import diagnostics_channel from 'node:diagnostics_channel';

const channels = diagnostics_channel.tracingChannel('my-channel');

channels.tracePromise(async () => {
  // Do something
}, {
  some: 'thing',
});
```

*CommonJS version:*

```js
const diagnostics_channel = require('node:diagnostics_channel');

const channels = diagnostics_channel.tracingChannel('my-channel');

channels.tracePromise(async () => {
  // Do something
}, {
  some: 'thing',
});
```

#### `tracingChannel.traceCallback(fn[, position[, context[, thisArg[, ...args]]]])`

Added in: v19.9.0, v18.19.0

- `fn` [`<Function>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Function) callback using function to wrap a trace around
- `position` [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) Zero-indexed argument position of expected callback
  (defaults to last argument if `undefined` is passed)
- `context` [`<Object>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Object) Shared object to correlate trace events through (defaults
  to `{}` if `undefined` is passed)
- `thisArg` [`<any>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#Data_types) The receiver to be used for the function call
- `...args` [`<any>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#Data_types) arguments to pass to the function (must include the callback)
- Returns: [`<any>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#Data_types) The return value of the given function

Trace a callback-receiving function call. The callback is expected to follow
the error as first arg convention typically used. This will always produce a
[`start` event](#startevent) and [`end` event](#endevent) around the synchronous portion of the
function execution, and will produce a [`asyncStart` event](#asyncstartevent) and
[`asyncEnd` event](#asyncendevent) around the callback execution. It may also produce an
[`error` event](#errorevent) if the given function throws or the first argument passed to
the callback is set. This will run the given function using
[`channel.runStores(context, ...)`](#channelrunstorescontext-fn-thisarg-args) on the `start` channel which ensures all
events should have any bound stores set to match this trace context.

To ensure only correct trace graphs are formed, events will only be published
if subscribers are present prior to starting the trace. Subscriptions which are
added after the trace begins will not receive future events from that trace,
only future traces will be seen.

*ES module version:*

```js
import diagnostics_channel from 'node:diagnostics_channel';

const channels = diagnostics_channel.tracingChannel('my-channel');

channels.traceCallback((arg1, callback) => {
  // Do something
  callback(null, 'result');
}, 1, {
  some: 'thing',
}, thisArg, arg1, callback);
```

*CommonJS version:*

```js
const diagnostics_channel = require('node:diagnostics_channel');

const channels = diagnostics_channel.tracingChannel('my-channel');

channels.traceCallback((arg1, callback) => {
  // Do something
  callback(null, 'result');
}, 1, {
  some: 'thing',
}, thisArg, arg1, callback);
```

The callback will also be run with [`channel.runStores(context, ...)`](#channelrunstorescontext-fn-thisarg-args) which
enables context loss recovery in some cases.

*ES module version:*

```js
import diagnostics_channel from 'node:diagnostics_channel';
import { AsyncLocalStorage } from 'node:async_hooks';

const channels = diagnostics_channel.tracingChannel('my-channel');
const myStore = new AsyncLocalStorage();

// The start channel sets the initial store data to something
// and stores that store data value on the trace context object
channels.start.bindStore(myStore, (data) => {
  const span = new Span(data);
  data.span = span;
  return span;
});

// Then asyncStart can restore from that data it stored previously
channels.asyncStart.bindStore(myStore, (data) => {
  return data.span;
});
```

*CommonJS version:*

```js
const diagnostics_channel = require('node:diagnostics_channel');
const { AsyncLocalStorage } = require('node:async_hooks');

const channels = diagnostics_channel.tracingChannel('my-channel');
const myStore = new AsyncLocalStorage();

// The start channel sets the initial store data to something
// and stores that store data value on the trace context object
channels.start.bindStore(myStore, (data) => {
  const span = new Span(data);
  data.span = span;
  return span;
});

// Then asyncStart can restore from that data it stored previously
channels.asyncStart.bindStore(myStore, (data) => {
  return data.span;
});
```

#### `tracingChannel.hasSubscribers`

Added in: v22.0.0, v20.13.0

- Returns: [`<boolean>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#boolean_type) `true` if any of the individual channels has a subscriber,
  `false` if not.

This is a helper method available on a [`TracingChannel`](#class-tracingchannel) instance to check if
any of the [TracingChannel Channels](#tracingchannel-channels) have subscribers. A `true` is returned if
any of them have at least one subscriber, a `false` is returned otherwise.

*ES module version:*

```js
import diagnostics_channel from 'node:diagnostics_channel';

const channels = diagnostics_channel.tracingChannel('my-channel');

if (channels.hasSubscribers) {
  // Do something
}
```

*CommonJS version:*

```js
const diagnostics_channel = require('node:diagnostics_channel');

const channels = diagnostics_channel.tracingChannel('my-channel');

if (channels.hasSubscribers) {
  // Do something
}
```

### Class: `BoundedChannel`

Added in: v26.1.0

Stability: 1 - Experimental

The class `BoundedChannel` is a simplified version of [`TracingChannel`](#class-tracingchannel) that
only traces synchronous operations. It consists of two channels (`start` and
`end`) instead of five, omitting the `asyncStart`, `asyncEnd`, and `error`
events. This makes it suitable for tracing operations that don't involve
asynchronous continuations or error handling.

Like `TracingChannel`, it is recommended to create and reuse a single
`BoundedChannel` at the top-level of the file rather than creating them
dynamically.

#### `boundedChannel.hasSubscribers`

Added in: v26.1.0

- Returns: [`<boolean>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#boolean_type) `true` if any of the individual channels has a subscriber,
  `false` if not.

Check if any of the `start` or `end` channels have subscribers.

*ES module version:*

```js
import { boundedChannel } from 'node:diagnostics_channel';

const wc = boundedChannel('my-operation');

if (wc.hasSubscribers) {
  // There are subscribers, perform traced operation
}
```

*CommonJS version:*

```js
const { boundedChannel } = require('node:diagnostics_channel');

const wc = boundedChannel('my-operation');

if (wc.hasSubscribers) {
  // There are subscribers, perform traced operation
}
```

#### `boundedChannel.subscribe(handlers)`

Added in: v26.1.0

- `handlers` [`<Object>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Object) Set of channel subscribers
  - `start` [`<Function>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Function) The start event subscriber
  - `end` [`<Function>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Function) The end event subscriber

Subscribe to the bounded channel events. This is equivalent to calling
[`channel.subscribe(onMessage)`](#channelsubscribeonmessage) on each channel individually.

*ES module version:*

```js
import { boundedChannel } from 'node:diagnostics_channel';

const wc = boundedChannel('my-operation');

wc.subscribe({
  start(message) {
    // Handle start
  },
  end(message) {
    // Handle end
  },
});
```

*CommonJS version:*

```js
const { boundedChannel } = require('node:diagnostics_channel');

const wc = boundedChannel('my-operation');

wc.subscribe({
  start(message) {
    // Handle start
  },
  end(message) {
    // Handle end
  },
});
```

#### `boundedChannel.unsubscribe(handlers)`

Added in: v26.1.0

- `handlers` [`<Object>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Object) Set of channel subscribers
  - `start` [`<Function>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Function) The start event subscriber
  - `end` [`<Function>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Function) The end event subscriber
- Returns: [`<boolean>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#boolean_type) `true` if all handlers were successfully unsubscribed,
  `false` otherwise.

Unsubscribe from the bounded channel events. This is equivalent to calling
[`channel.unsubscribe(onMessage)`](#channelunsubscribeonmessage) on each channel individually.

*ES module version:*

```js
import { boundedChannel } from 'node:diagnostics_channel';

const wc = boundedChannel('my-operation');

const handlers = {
  start(message) {},
  end(message) {},
};

wc.subscribe(handlers);
wc.unsubscribe(handlers);
```

*CommonJS version:*

```js
const { boundedChannel } = require('node:diagnostics_channel');

const wc = boundedChannel('my-operation');

const handlers = {
  start(message) {},
  end(message) {},
};

wc.subscribe(handlers);
wc.unsubscribe(handlers);
```

#### `boundedChannel.run(context, fn[, thisArg[, ...args]])`

Added in: v26.1.0

- `context` [`<Object>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Object) Shared object to correlate events through
- `fn` [`<Function>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Function) Function to wrap a trace around
- `thisArg` [`<any>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#Data_types) The receiver to be used for the function call
- `...args` [`<any>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#Data_types) Optional arguments to pass to the function
- Returns: [`<any>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#Data_types) The return value of the given function

Trace a synchronous function call. This will produce a `start` event and `end`
event around the execution. This runs the given function using
[`channel.runStores(context, ...)`](#channelrunstorescontext-fn-thisarg-args) on the `start` channel which ensures all
events have any bound stores set to match this trace context.

*ES module version:*

```js
import { boundedChannel } from 'node:diagnostics_channel';

const wc = boundedChannel('my-operation');

const result = wc.run({ operationId: '123' }, () => {
  // Perform operation
  return 42;
});
```

*CommonJS version:*

```js
const { boundedChannel } = require('node:diagnostics_channel');

const wc = boundedChannel('my-operation');

const result = wc.run({ operationId: '123' }, () => {
  // Perform operation
  return 42;
});
```

#### `boundedChannel.withScope([context])`

Added in: v26.1.0

- `context` [`<Object>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Object) Shared object to correlate events through
- Returns: [`<BoundedChannelScope>`](18-diagnostics-channel.md#class-boundedchannelscope) Disposable scope object

Create a disposable scope for tracing a synchronous operation using JavaScript's
explicit resource management (`using` syntax). The scope automatically publishes
`start` and `end` events, enters bound stores, and handles cleanup when disposed.

*ES module version:*

```js
import { boundedChannel } from 'node:diagnostics_channel';

const wc = boundedChannel('my-operation');

const context = { operationId: '123' };
{
  using scope = wc.withScope(context);
  // Stores are entered, start event is published

  // Perform work and set result on context
  context.result = 42;
}
// End event is published, stores are restored automatically
```

*CommonJS version:*

```js
const { boundedChannel } = require('node:diagnostics_channel');

const wc = boundedChannel('my-operation');

const context = { operationId: '123' };
{
  using scope = wc.withScope(context);
  // Stores are entered, start event is published

  // Perform work and set result on context
  context.result = 42;
}
// End event is published, stores are restored automatically
```

### Class: `BoundedChannelScope`

Added in: v26.1.0

Stability: 1 - Experimental

The class `BoundedChannelScope` represents a disposable scope created by
[`boundedChannel.withScope(context)`](#boundedchannelwithscopecontext). It manages the lifecycle of a traced
operation, automatically publishing events and managing store contexts.

The scope must be used with the `using` syntax to ensure proper disposal.

*ES module version:*

```js
import { boundedChannel } from 'node:diagnostics_channel';

const wc = boundedChannel('my-operation');

const context = {};
{
  using scope = wc.withScope(context);
  // Start event is published, stores are entered
  context.result = performOperation();
  // End event is automatically published at end of block
}
```

*CommonJS version:*

```js
const { boundedChannel } = require('node:diagnostics_channel');

const wc = boundedChannel('my-operation');

const context = {};
{
  using scope = wc.withScope(context);
  // Start event is published, stores are entered
  context.result = performOperation();
  // End event is automatically published at end of block
}
```

### BoundedChannel Channels

A `BoundedChannel` consists of two diagnostics channels representing the
lifecycle of a scope created with the `using` syntax:

- `tracing:${name}:start` - Published when the `using` statement executes (scope creation)
- `tracing:${name}:end` - Published when exiting the block (scope disposal)

When using the `using` syntax with [`boundedChannel.withScope([context])`][], the `start`
event is published immediately when the statement executes, and the `end` event
is automatically published when disposal occurs at the end of the block. All
events share the same context object, which can be extended with additional
properties like `result` during scope execution.

### TracingChannel Channels

A TracingChannel is a collection of several diagnostics\_channels representing
specific points in the execution lifecycle of a single traceable action. The
behavior is split into five diagnostics\_channels consisting of `start`,
`end`, `asyncStart`, `asyncEnd`, and `error`. A single traceable action will
share the same event object between all events, this can be helpful for
managing correlation through a weakmap.

These event objects will be extended with `result` or `error` values when
the task "completes". In the case of a synchronous task the `result` will be
the return value and the `error` will be anything thrown from the function.
With callback-based async functions the `result` will be the second argument
of the callback while the `error` will either be a thrown error visible in the
`end` event or the first callback argument in either of the `asyncStart` or
`asyncEnd` events.

To ensure only correct trace graphs are formed, events should only be published
if subscribers are present prior to starting the trace. Subscriptions which are
added after the trace begins should not receive future events from that trace,
only future traces will be seen.

Tracing channels should follow a naming pattern of:

- `tracing:module.class.method:start` or `tracing:module.function:start`
- `tracing:module.class.method:end` or `tracing:module.function:end`
- `tracing:module.class.method:asyncStart` or `tracing:module.function:asyncStart`
- `tracing:module.class.method:asyncEnd` or `tracing:module.function:asyncEnd`
- `tracing:module.class.method:error` or `tracing:module.function:error`

#### `start(event)`

- Name: `tracing:${name}:start`

The `start` event represents the point at which a function is called. At this
point the event data may contain function arguments or anything else available
at the very start of the execution of the function.

#### `end(event)`

- Name: `tracing:${name}:end`

The `end` event represents the point at which a function call returns a value.
In the case of an async function this is when the promise returned not when the
function itself makes a return statement internally. At this point, if the
traced function was synchronous the `result` field will be set to the return
value of the function. Alternatively, the `error` field may be present to
represent any thrown errors.

It is recommended to listen specifically to the `error` event to track errors
as it may be possible for a traceable action to produce multiple errors. For
example, an async task which fails may be started internally before the sync
part of the task then throws an error.

#### `asyncStart(event)`

- Name: `tracing:${name}:asyncStart`

The `asyncStart` event represents the callback or continuation of a traceable
function being reached. At this point things like callback arguments may be
available, or anything else expressing the "result" of the action.

For callbacks-based functions, the first argument of the callback will be
assigned to the `error` field, if not `undefined` or `null`, and the second
argument will be assigned to the `result` field.

For promises, the argument to the `resolve` path will be assigned to `result`
or the argument to the `reject` path will be assign to `error`.

It is recommended to listen specifically to the `error` event to track errors
as it may be possible for a traceable action to produce multiple errors. For
example, an async task which fails may be started internally before the sync
part of the task then throws an error.

#### `asyncEnd(event)`

- Name: `tracing:${name}:asyncEnd`

The `asyncEnd` event represents the callback of an asynchronous function
returning. It's not likely event data will change after the `asyncStart` event,
however it may be useful to see the point where the callback completes.

#### `error(event)`

- Name: `tracing:${name}:error`

The `error` event represents any error produced by the traceable function
either synchronously or asynchronously. If an error is thrown in the
synchronous portion of the traced function the error will be assigned to the
`error` field of the event and the `error` event will be triggered. If an error
is received asynchronously through a callback or promise rejection it will also
be assigned to the `error` field of the event and trigger the `error` event.

It is possible for a single traceable function call to produce errors multiple
times so this should be considered when consuming this event. For example, if
another async task is triggered internally which fails and then the sync part
of the function then throws and error two `error` events will be emitted, one
for the sync error and one for the async error.

### Built-in Channels

#### Console

Stability: 1 - Experimental

##### Event: `'console.log'`

- `args` [`<any>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#Data_types)[]

Emitted when `console.log()` is called. Receives and array of the arguments
passed to `console.log()`.

##### Event: `'console.info'`

- `args` [`<any>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#Data_types)[]

Emitted when `console.info()` is called. Receives and array of the arguments
passed to `console.info()`.

##### Event: `'console.debug'`

- `args` [`<any>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#Data_types)[]

Emitted when `console.debug()` is called. Receives and array of the arguments
passed to `console.debug()`.

##### Event: `'console.warn'`

- `args` [`<any>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#Data_types)[]

Emitted when `console.warn()` is called. Receives and array of the arguments
passed to `console.warn()`.

##### Event: `'console.error'`

- `args` [`<any>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#Data_types)[]

Emitted when `console.error()` is called. Receives and array of the arguments
passed to `console.error()`.

#### Crypto

Added in: v26.9.0

Stability: 1 - Experimental

##### Event: `'crypto.fips.indicator'`

- `operation` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) The provider-defined operation type.
- `reason` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) The provider-defined description of why the operation is
  not approved.
- `blocked` [`<boolean>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#boolean_type) Whether an indicator callback blocked the operation.
- `count` [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) The number of matching pending indicator invocations
  represented by this message.
- `dropped` [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) The number of additional indicator invocations dropped
  before this message was delivered.

Emitted when the OpenSSL FIPS provider used by Node.js detects an operation that
is not FIPS approved after the corresponding provider check was relaxed. Such
operations are possible when the provider is configured for backwards
compatibility. The `operation` and `reason` values come from the provider and
should be treated as opaque strings rather than stable enumerations.

Start Node.js with [`--enable-fips-indicator-events`](13-cli.md#--enable-fips-indicator-events) to enable this channel.
Without the option, subscribing does not install the OpenSSL callback and no
messages are published.

Subscribing to the channel is observation-only and never changes the result of
an operation. Node.js preserves the result from any native indicator callback
installed before Node.js initializes its crypto support. When
[`--force-fips=strict`](13-cli.md#--force-fips) is used, Node.js rejects callback-indicated
non-approved operations whether or not indicator events are enabled or the
channel has subscribers.

Messages are published asynchronously on the main thread because OpenSSL
indicators can originate from Workers or other threads. Only subscriptions on
the main thread receive messages. Delivery order relative to the originating
operation is not defined, and a message cannot be correlated with a particular
call or Worker.

One cryptographic operation can invoke the OpenSSL indicator more than once.
Matching pending invocations are coalesced and reflected in `count`, which does
not necessarily represent a number of cryptographic operations. At most 256
distinct messages are queued. Additional invocations are reported in `dropped`
on the first queued message. Queued messages do not keep the event loop active,
so this channel is best-effort diagnostics rather than an authoritative audit
log.

This channel observes the default OpenSSL library context used by Node.js. It
does not observe native addons or other code that uses another `OSSL_LIB_CTX`
or another copy of `libcrypto`. It is active with OpenSSL 3.4 and later and is
not available with BoringSSL. A provider configured to reject a check directly,
including a pedantic OpenSSL FIPS provider, can reject an operation without
emitting an indicator. Receiving or not receiving a message does not establish
that Node.js or a cryptographic operation is FIPS validated.

```js
import diagnosticsChannel from 'node:diagnostics_channel';

diagnosticsChannel.subscribe('crypto.fips.indicator', (message) => {
  console.error('Non-approved cryptographic operation', message);
});
```

#### HTTP

Stability: 1 - Experimental

##### Event: `'http.client.request.created'`

- `request` [`<http.ClientRequest>`](27-http-part1.md#class-httpclientrequest)

Emitted when client creates a request object.
Unlike `http.client.request.start`, this event is emitted before the request has been sent.

##### Event: `'http.client.request.start'`

- `request` [`<http.ClientRequest>`](27-http-part1.md#class-httpclientrequest)

Emitted when client starts a request.

##### Event: `'http.client.request.error'`

- `request` [`<http.ClientRequest>`](27-http-part1.md#class-httpclientrequest)
- `error` [`<Error>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Error)

Emitted when an error occurs during a client request.

##### Event: `'http.client.response.finish'`

- `request` [`<http.ClientRequest>`](27-http-part1.md#class-httpclientrequest)
- `response` [`<http.IncomingMessage>`](27-http-part2.md#class-httpincomingmessage)

Emitted when client receives a response.

##### Event: `'http.server.request.start'`

- `request` [`<http.IncomingMessage>`](27-http-part2.md#class-httpincomingmessage)
- `response` [`<http.ServerResponse>`](27-http-part2.md#class-httpserverresponse)
- `socket` [`<net.Socket>`](38-net.md#class-netsocket)
- `server` [`<http.Server>`](27-http-part1.md#class-httpserver)

Emitted when server receives a request.

##### Event: `'http.server.response.created'`

- `request` [`<http.IncomingMessage>`](27-http-part2.md#class-httpincomingmessage)
- `response` [`<http.ServerResponse>`](27-http-part2.md#class-httpserverresponse)

Emitted when server creates a response.
The event is emitted before the response is sent.

##### Event: `'http.server.response.finish'`

- `request` [`<http.IncomingMessage>`](27-http-part2.md#class-httpincomingmessage)
- `response` [`<http.ServerResponse>`](27-http-part2.md#class-httpserverresponse)
- `socket` [`<net.Socket>`](38-net.md#class-netsocket)
- `server` [`<http.Server>`](27-http-part1.md#class-httpserver)

Emitted when server sends a response.

#### HTTP/2

Stability: 1 - Experimental

##### Event: `'http2.client.stream.created'`

- `stream` [`<ClientHttp2Stream>`](28-http2-part1.md#class-clienthttp2stream)
- `headers` [`<HTTP/2 Headers Object>`](28-http2-part3.md#headers-object)

Emitted when a stream is created on the client.

##### Event: `'http2.client.stream.start'`

- `stream` [`<ClientHttp2Stream>`](28-http2-part1.md#class-clienthttp2stream)
- `headers` [`<HTTP/2 Headers Object>`](28-http2-part3.md#headers-object)

Emitted when a stream is started on the client.

##### Event: `'http2.client.stream.error'`

- `stream` [`<ClientHttp2Stream>`](28-http2-part1.md#class-clienthttp2stream)
- `error` [`<Error>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Error)

Emitted when an error occurs during the processing of a stream on the client.

##### Event: `'http2.client.stream.finish'`

- `stream` [`<ClientHttp2Stream>`](28-http2-part1.md#class-clienthttp2stream)
- `headers` [`<HTTP/2 Headers Object>`](28-http2-part3.md#headers-object)
- `flags` [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type)

Emitted when a stream is received on the client.

##### Event: `'http2.client.stream.bodyChunkSent'`

- `stream` [`<ClientHttp2Stream>`](28-http2-part1.md#class-clienthttp2stream)
- `writev` [`<boolean>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#boolean_type)
- `data` [`<Buffer>`](07-buffer-part1.md#class-buffer) | [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) | [`<Buffer>`](07-buffer-part1.md#class-buffer)[] | [`<Object>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Object)[]
  - `chunk` [`<Buffer>`](07-buffer-part1.md#class-buffer) | [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type)
  - `encoding` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type)
- `encoding` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type)

Emitted when a chunk of the client stream body is being sent.

##### Event: `'http2.client.stream.bodySent'`

- `stream` [`<ClientHttp2Stream>`](28-http2-part1.md#class-clienthttp2stream)

Emitted after the client stream body has been fully sent.

##### Event: `'http2.client.stream.close'`

- `stream` [`<ClientHttp2Stream>`](28-http2-part1.md#class-clienthttp2stream)

Emitted when a stream is closed on the client. The HTTP/2 error code used when
closing the stream can be retrieved using the `stream.rstCode` property.

##### Event: `'http2.server.stream.created'`

- `stream` [`<ServerHttp2Stream>`](28-http2-part1.md#class-serverhttp2stream)
- `headers` [`<HTTP/2 Headers Object>`](28-http2-part3.md#headers-object)

Emitted when a stream is created on the server.

##### Event: `'http2.server.stream.start'`

- `stream` [`<ServerHttp2Stream>`](28-http2-part1.md#class-serverhttp2stream)
- `headers` [`<HTTP/2 Headers Object>`](28-http2-part3.md#headers-object)

Emitted when a stream is started on the server.

##### Event: `'http2.server.stream.error'`

- `stream` [`<ServerHttp2Stream>`](28-http2-part1.md#class-serverhttp2stream)
- `error` [`<Error>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Error)

Emitted when an error occurs during the processing of a stream on the server.

##### Event: `'http2.server.stream.finish'`

- `stream` [`<ServerHttp2Stream>`](28-http2-part1.md#class-serverhttp2stream)
- `headers` [`<HTTP/2 Headers Object>`](28-http2-part3.md#headers-object)
- `flags` [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type)

Emitted when a stream is sent on the server.

##### Event: `'http2.server.stream.close'`

- `stream` [`<ServerHttp2Stream>`](28-http2-part1.md#class-serverhttp2stream)

Emitted when a stream is closed on the server. The HTTP/2 error code used when
closing the stream can be retrieved using the `stream.rstCode` property.

#### Modules

Stability: 1 - Experimental

##### Event: `'tracing:module.require:start'`

- `event` [`<Object>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Object) containing the following properties
  - `id` Argument passed to `require()`. Module name.
  - `parentFilename` Name of the module that attempted to require(id).

Emitted when `require()` is executed. See [`start` event](#startevent).

##### Event: `'tracing:module.require:end'`

- `event` [`<Object>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Object) containing the following properties
  - `id` Argument passed to `require()`. Module name.
  - `parentFilename` Name of the module that attempted to require(id).

Emitted when a `require()` call returns. See [`end` event](#endevent).

##### Event: `'tracing:module.require:error'`

- `event` [`<Object>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Object) containing the following properties
  - `id` Argument passed to `require()`. Module name.
  - `parentFilename` Name of the module that attempted to require(id).
- `error` [`<Error>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Error)

Emitted when a `require()` throws an error. See [`error` event](#errorevent).

##### Event: `'tracing:module.import:asyncStart'`

- `event` [`<Object>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Object) containing the following properties
  - `id` Argument passed to `import()`. Module name.
  - `parentURL` URL object of the module that attempted to import(id).

Emitted when `import()` is invoked. See [`asyncStart` event](#asyncstartevent).

##### Event: `'tracing:module.import:asyncEnd'`

- `event` [`<Object>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Object) containing the following properties
  - `id` Argument passed to `import()`. Module name.
  - `parentURL` URL object of the module that attempted to import(id).

Emitted when `import()` has completed. See [`asyncEnd` event](#asyncendevent).

##### Event: `'tracing:module.import:error'`

- `event` [`<Object>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Object) containing the following properties
  - `id` Argument passed to `import()`. Module name.
  - `parentURL` URL object of the module that attempted to import(id).
- `error` [`<Error>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Error)

Emitted when a `import()` throws an error. See [`error` event](#errorevent).

#### NET

Stability: 1 - Experimental

##### Event: `'net.client.socket'`

- `socket` [`<net.Socket>`](38-net.md#class-netsocket) | [`<tls.TLSSocket>`](55-tls.md#tlstlssocket)

Emitted when a new TCP or pipe client socket connection is created.

##### Event: `'net.server.socket'`

- `socket` [`<net.Socket>`](38-net.md#class-netsocket)

Emitted when a new TCP or pipe connection is received.

##### Event: `'tracing:net.server.listen:asyncStart'`

- `server` [`<net.Server>`](38-net.md#class-netserver)
- `options` [`<Object>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Object)

Emitted when [`net.Server.listen()`](38-net.md#serverlisten) is invoked, before the port or pipe is actually setup.

##### Event: `'tracing:net.server.listen:asyncEnd'`

- `server` [`<net.Server>`](38-net.md#class-netserver)

Emitted when [`net.Server.listen()`](38-net.md#serverlisten) has completed and thus the server is ready to accept connection.

##### Event: `'tracing:net.server.listen:error'`

- `server` [`<net.Server>`](38-net.md#class-netserver)
- `error` [`<Error>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Error)

Emitted when [`net.Server.listen()`](38-net.md#serverlisten) is returning an error.

#### UDP

Stability: 1 - Experimental

##### Event: `'udp.socket'`

- `socket` [`<dgram.Socket>`](58-dgram.md#class-dgramsocket)

Emitted when a new UDP socket is created.

#### Process

Added in: v16.18.0

Stability: 1 - Experimental

##### Event: `'child_process'`

- `process` [`<ChildProcess>`](11-child-process.md#class-childprocess)

Emitted when a new process is created.

`tracing:child_process.spawn:start`

- `process` [`<ChildProcess>`](11-child-process.md#class-childprocess)
- `options` [`<Object>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Object)

Emitted when [`child_process.spawn()`](11-child-process.md#child_processspawncommand-args-options) is invoked, before the process is
actually spawned.

`tracing:child_process.spawn:end`

- `process` [`<ChildProcess>`](11-child-process.md#class-childprocess)

Emitted when [`child_process.spawn()`](11-child-process.md#child_processspawncommand-args-options) has completed successfully and the
process has been created.

`tracing:child_process.spawn:error`

- `process` [`<ChildProcess>`](11-child-process.md#class-childprocess)
- `error` [`<Error>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Error)

Emitted when [`child_process.spawn()`](11-child-process.md#child_processspawncommand-args-options) encounters an error.

##### Event: `'process.execve'`

- `execPath` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type)
- `args` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type)[]
- `env` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type)[]

Emitted when [`process.execve()`](43-process-part1.md#processexecvefile-args-env) is invoked.

#### Test Runner

Added in: v26.1.0, v24.16.0

See [Test instrumentation and OpenTelemetry](53-test-part3.md#test-instrumentation-and-opentelemetry) for details.

#### Web Locks

Added in: v25.9.0

Stability: 1 - Experimental

These channels are emitted for each [`locks.request()`](67-worker-threads.md#locksrequestname-options-callback) call. See
[`worker_threads.locks`](67-worker-threads.md#worker_threadslocks) for details on Web Locks.

##### Event: `'locks.request.start'`

- `name` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) The name of the requested lock resource.
- `mode` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) The lock mode: `'exclusive'` or `'shared'`.

Emitted when a lock request is initiated, before the lock is granted.

##### Event: `'locks.request.grant'`

- `name` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) The name of the requested lock resource.
- `mode` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) The lock mode: `'exclusive'` or `'shared'`.

Emitted when a lock is successfully granted and the callback is about to run.

##### Event: `'locks.request.miss'`

- `name` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) The name of the requested lock resource.
- `mode` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) The lock mode: `'exclusive'` or `'shared'`.

Emitted when `ifAvailable` is `true` and the lock is not immediately available,
and the request callback is invoked with `null` instead of a `Lock` object.

##### Event: `'locks.request.end'`

- `name` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) The name of the requested lock resource.
- `mode` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) The lock mode: `'exclusive'` or `'shared'`.
- `steal` [`<boolean>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#boolean_type) Whether the request uses steal semantics.
- `ifAvailable` [`<boolean>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#boolean_type) Whether the request uses ifAvailable semantics.
- `error` [`<Error>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Error) | [`<undefined>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#undefined_type) The error thrown by the callback, if any.

Emitted when a lock request has finished, whether the callback succeeded,
threw an error, or the lock was stolen.

#### Worker Thread

Added in: v16.18.0

Stability: 1 - Experimental

##### Event: `'worker_threads'`

- `worker` [`<Worker>`](67-worker-threads.md#class-worker)

Emitted when a new thread is created.

#### SQLite

Added in: v26.8.0

Stability: 1 - Experimental

##### Event: `'sqlite.db.query'`

- `sql` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) The expanded SQL with bound parameter values substituted.
  If expansion fails, the source SQL with unsubstituted placeholders is used
  instead.
- `database` [`<Database>`](50-sqlite.md#class-database) The [`Database`](50-sqlite.md#class-database) instance that executed the
  statement.
- `duration` [`<number>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#number_type) SQLite's internal estimate of the statement run time in
  nanoseconds. This reflects C-layer execution time only and does not include
  JavaScript binding overhead such as argument marshaling or result-row
  construction.

Emitted after a SQL statement finishes executing against a [`Database`](50-sqlite.md#class-database)
instance. This is a **profiling** event: it fires once per statement upon
completion and reports an estimated duration from SQLite's internal profiler.
It is not a distributed-tracing span. There is no corresponding start event,
no async context propagation, and no parent-span linkage. If you need
OpenTelemetry-compatible spans or async context propagation, wrap your SQLite
calls with a [`TracingChannel`](#class-tracingchannel) at the JavaScript layer instead.

Publishing is zero-overhead when there are no subscribers.

No event is emitted for a statement that is abandoned mid-iteration and later
finalized, either explicitly through [`statement.close()`](50-sqlite.md#statementclose) or when the
statement is garbage collected. Subscribers must not close the database or the
statement, since both are still in use while the event is being delivered; see
[`database.close()`](50-sqlite.md#databaseclose) and [`statement.close()`](50-sqlite.md#statementclose).

[Contents](../README.md) | [Previous: Deprecated APIs](17-deprecations.md) | [Next: DNS](19-dns.md)
