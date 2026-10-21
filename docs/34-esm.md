[Contents](../README.md) | [Previous: Modules: CommonJS modules](33-modules.md) | [Next: Modules: node:module API](35-module.md)

Source: [https://nodejs.org/docs/latest/api/esm.html](https://nodejs.org/docs/latest/api/esm.html)

# Modules: ECMAScript modules

Added in: v8.5.0

**History**

| Version | Changes |
| --- | --- |
| v23.1.0, v22.12.0, v20.18.3, v18.20.5 | Import attributes are no longer experimental. |
| v22.0.0 | Drop support for import assertions. |
| v21.0.0, v20.10.0, v18.20.0 | Add experimental support for import attributes. |
| v20.0.0, v18.19.0 | Module customization hooks are executed off the main thread. |
| v18.6.0, v16.17.0 | Add support for chaining module customization hooks. |
| v17.1.0, v16.14.0 | Add experimental support for import assertions. |
| v17.0.0, v16.12.0 | Consolidate customization hooks, removed `getFormat`, `getSource`, `transformSource`, and `getGlobalPreloadCode` hooks added `load` and `globalPreload` hooks allowed returning `format` from either `resolve` or `load` hooks. |
| v15.3.0, v14.17.0, v12.22.0 | Stabilize modules implementation. |
| v14.13.0, v12.20.0 | Support for detection of CommonJS named exports. |
| v14.8.0 | Unflag Top-Level Await. |
| v14.0.0, v13.14.0, v12.20.0 | Remove experimental modules warning. |
| v13.2.0, v12.17.0 | Loading ECMAScript modules no longer requires a command-line flag. |
| v12.0.0 | Add support for ES modules using `.js` file extension via `package.json` `"type"` field. |

[Stability: 2](01-documentation.md#stability-index) - Stable

## Introduction

ECMAScript modules are [the official standard format](https://tc39.github.io/ecma262/#sec-modules) to package JavaScript
code for reuse. Modules are defined using a variety of [`import`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Statements/import) and
[`export`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Statements/export) statements.

The following example of an ES module exports a function:

```js
// addTwo.mjs
function addTwo(num) {
  return num + 2;
}

export { addTwo };
```

The following example of an ES module imports the function from `addTwo.mjs`:

```js
// app.mjs
import { addTwo } from './addTwo.mjs';

// Prints: 6
console.log(addTwo(4));
```

Node.js fully supports ECMAScript modules as they are currently specified and
provides interoperability between them and its original module format,
[CommonJS](33-modules.md).

## Enabling

Node.js has two module systems: [CommonJS](33-modules.md) modules and ECMAScript modules.

Authors can tell Node.js to interpret JavaScript as an ES module via the `.mjs`
file extension, the `package.json` [`"type"`](36-packages.md#type) field with a value `"module"`,
or the [`--input-type`](13-cli.md#--input-typetype) flag with a value of `"module"`. These are explicit
markers of code being intended to run as an ES module.

Inversely, authors can explicitly tell Node.js to interpret JavaScript as
CommonJS via the `.cjs` file extension, the `package.json` [`"type"`](36-packages.md#type) field
with a value `"commonjs"`, or the [`--input-type`](13-cli.md#--input-typetype) flag with a value of
`"commonjs"`.

When code lacks explicit markers for either module system, Node.js will inspect
the source code of a module to look for ES module syntax. If such syntax is
found, Node.js will run the code as an ES module; otherwise it will run the
module as CommonJS. See [Determining module system](36-packages.md#determining-module-system) for more details.

## Packages

This section was moved to [Modules: Packages](36-packages.md).

## `import` Specifiers

### Terminology

The *specifier* of an `import` statement is the string after the `from` keyword,
e.g. `'node:path'` in `import { sep } from 'node:path'`. Specifiers are also
used in `export from` statements, and as the argument to an `import()`
expression.

There are three types of specifiers:

- *Relative specifiers* like `'./startup.js'` or `'../config.mjs'`. They refer
  to a path relative to the location of the importing file. *The file extension
  is always necessary for these.*
- *Bare specifiers* like `'some-package'` or `'some-package/shuffle'`. They can
  refer to the main entry point of a package by the package name, or a
  specific feature module within a package prefixed by the package name as per
  the examples respectively. *Including the file extension is only necessary
  for packages without an [`"exports"`](36-packages.md#exports) field.*
- *Absolute specifiers* like `'file:///opt/nodejs/config.js'`. They refer
  directly and explicitly to a full path.

Bare specifier resolutions are handled by the [Node.js module
resolution and loading algorithm](#resolution-algorithm-specification).
All other specifier resolutions are always only resolved with
the standard relative [URL](https://url.spec.whatwg.org/) resolution semantics.

Like in CommonJS, module files within packages can be accessed by appending a
path to the package name unless the package's [`package.json`](36-packages.md#nodejs-packagejson-field-definitions) contains an
[`"exports"`](36-packages.md#exports) field, in which case files within packages can only be accessed
via the paths defined in [`"exports"`](36-packages.md#exports).

For details on these package resolution rules that apply to bare specifiers in
the Node.js module resolution, see the [packages documentation](36-packages.md).

### Mandatory file extensions

A file extension must be provided when using the `import` keyword to resolve
relative or absolute specifiers. Directory indexes (e.g. `'./startup/index.js'`)
must also be fully specified.

This behavior matches how `import` behaves in browser environments, assuming a
typically configured server.

### URLs

ES modules are resolved and cached as URLs. This means that special characters
must be [percent-encoded](59-url.md#percent-encoding-in-urls), such as `#` with `%23` and `?` with `%3F`.

`file:`, `node:`, and `data:` URL schemes are supported. A specifier like
`'https://example.com/app.js'` is not supported natively in Node.js unless using
a [custom HTTPS loader](35-module.md#import-from-https).

#### `file:` URLs

Modules are loaded multiple times if the `import` specifier used to resolve
them has a different query or fragment.

```js
import './foo.mjs?query=1'; // loads ./foo.mjs with query of "?query=1"
import './foo.mjs?query=2'; // loads ./foo.mjs with query of "?query=2"
```

The volume root may be referenced via `/`, `//`, or `file:///`. Given the
differences between [URL](https://url.spec.whatwg.org/) and path resolution (such as percent encoding
details), it is recommended to use [url.pathToFileURL](59-url.md#urlpathtofileurlpath-options) when importing a path.

#### `data:` imports

Added in: v12.10.0

[`data:` URLs](https://developer.mozilla.org/en-US/docs/Web/URI/Reference/Schemes/data) are supported for importing with the following MIME types:

- `text/javascript` for ES modules
- `application/json` for JSON
- `application/wasm` for Wasm

```js
import 'data:text/javascript,console.log("hello!");';
import _ from 'data:application/json,"world!"' with { type: 'json' };
```

`data:` URLs only resolve [bare specifiers](#terminology) for builtin modules
and [absolute specifiers](#terminology). Resolving
[relative specifiers](#terminology) does not work because `data:` is not a
[special scheme](https://url.spec.whatwg.org/#special-scheme). For example, attempting to load `./foo`
from `data:text/javascript,import "./foo";` fails to resolve because there
is no concept of relative resolution for `data:` URLs.

#### `node:` imports

Added in: v14.13.1, v12.20.0

**History**

| Version | Changes |
| --- | --- |
| v16.0.0, v14.18.0 | Added `node:` import support to `require(...)`. |

`node:` URLs are supported as an alternative means to load Node.js builtin
modules. This URL scheme allows for builtin modules to be referenced by valid
absolute URL strings.

```js
import fs from 'node:fs/promises';
```

## Import attributes

Added in: v17.1.0, v16.14.0

**History**

| Version | Changes |
| --- | --- |
| v21.0.0, v20.10.0, v18.20.0 | Switch from Import Assertions to Import Attributes. |

[Import attributes](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Statements/import/with) are an inline syntax for module import
statements to pass on more information alongside the module specifier.

```js
import fooData from './foo.json' with { type: 'json' };

const { default: barData } =
  await import('./bar.json', { with: { type: 'json' } });
```

Node.js only supports the `type` attribute, for which it supports the following values:

| Attribute `type` | Needed for |
| --- | --- |
| `'json'` | [JSON modules](#json-modules) |
| `'text'` | [Text modules](#text-modules) |

The `type: 'json'` attribute is mandatory when importing JSON modules.
The `type: 'text'` attribute is mandatory when importing text modules.

## Built-in modules

[Built-in modules](33-modules.md#built-in-modules) provide named exports of their public API. A
default export is also provided which is the value of the CommonJS exports.
The default export can be used for, among other things, modifying the named
exports. Named exports of built-in modules are updated only by calling
[`module.syncBuiltinESMExports()`](35-module.md#modulesyncbuiltinesmexports).

```js
import EventEmitter from 'node:events';
const e = new EventEmitter();
```

```js
import { readFile } from 'node:fs';
readFile('./foo.txt', (err, source) => {
  if (err) {
    console.error(err);
  } else {
    console.log(source);
  }
});
```

```js
import fs, { readFileSync } from 'node:fs';
import { syncBuiltinESMExports } from 'node:module';
import { Buffer } from 'node:buffer';

fs.readFileSync = () => Buffer.from('Hello, ESM');
syncBuiltinESMExports();

fs.readFileSync === readFileSync;
```

> When importing built-in modules, all the named exports (i.e. properties of the module exports object)
> are populated even if they are not individually accessed.
> This can make initial imports of built-in modules slightly slower compared to loading them with
> `require()` or `process.getBuiltinModule()`, where the module exports object is evaluated immediately,
> but some of its properties may only be initialized when first accessed individually.

## `import()` expressions

[Dynamic `import()`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/import) provides an asynchronous way to import modules. It is
supported in both CommonJS and ES modules, and can be used to load both CommonJS
and ES modules.

## `import.meta`

- Type: [`<Object>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Object)

The `import.meta` meta property is an `Object` that contains the following
properties. It is only supported in ES modules.

### `import.meta.dirname`

Added in: v21.2.0, v20.11.0

**History**

| Version | Changes |
| --- | --- |
| v24.0.0, v22.16.0 | This property is no longer experimental. |

- Type: [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) The directory name of the current module.

This is the same as the [`path.dirname()`](40-path.md#pathdirnamepath) of the [`import.meta.filename`](#importmetafilename).

> **Caveat**: only present on `file:` modules.

### `import.meta.filename`

Added in: v21.2.0, v20.11.0

**History**

| Version | Changes |
| --- | --- |
| v24.0.0, v22.16.0 | This property is no longer experimental. |

- Type: [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) The full absolute path and filename of the current module, with
  symlinks resolved.

This is the same as the [`url.fileURLToPath()`](59-url.md#urlfileurltopathurl-options) of the [`import.meta.url`](#importmetaurl).

> **Caveat** only local modules support this property. Modules not using the
> `file:` protocol will not provide it.

### `import.meta.url`

- Type: [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) The absolute `file:` URL of the module.

This is defined exactly the same as it is in browsers providing the URL of the
current module file.

This enables useful patterns such as relative file loading:

```js
import { readFileSync } from 'node:fs';
const buffer = readFileSync(new URL('./data.proto', import.meta.url));
```

### `import.meta.main`

Added in: v24.2.0, v22.18.0

Stability: 1.0 - Early development

- Type: [`<boolean>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#boolean_type) `true` when the current module is the entry point of the current process; `false` otherwise.

Equivalent to `require.main === module` in CommonJS.

Analogous to Python's `__name__ == "__main__"`.

```js
export function foo() {
  return 'Hello, world';
}

function main() {
  const message = foo();
  console.log(message);
}

if (import.meta.main) main();
// `foo` can be imported from another module without possible side-effects from `main`
```

### `import.meta.resolve(specifier)`

Added in: v13.9.0, v12.16.2

**History**

| Version | Changes |
| --- | --- |
| v20.6.0, v18.19.0 | No longer behind `--experimental-import-meta-resolve` CLI flag, except for the non-standard `parentURL` parameter. |
| v20.6.0, v18.19.0 | This API no longer throws when targeting `file:` URLs that do not map to an existing file on the local FS. |
| v20.0.0, v18.19.0 | This API now returns a string synchronously instead of a Promise. |
| v16.2.0, v14.18.0 | Add support for WHATWG `URL` object to `parentURL` parameter. |

Stability: 1.2 - Release candidate

- `specifier` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) The module specifier to resolve relative to the
  current module.
- Returns: [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) The absolute URL string that the specifier would resolve to.

[`import.meta.resolve`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/import.meta/resolve) is a module-relative resolution function scoped to
each module, returning the URL string.

```js
const dependencyAsset = import.meta.resolve('component-lib/asset.css');
// file:///app/node_modules/component-lib/asset.css
import.meta.resolve('./dep.js');
// file:///app/dep.js
```

All features of the Node.js module resolution are supported. Dependency
resolutions are subject to the permitted exports resolutions within the package.

**Caveats**:

- This can result in synchronous file-system operations, which
  can impact performance similarly to `require.resolve`.
- This feature is not available within custom loaders (it would
  create a deadlock).

**Non-standard API**:

When using the `--experimental-import-meta-resolve` flag, that function accepts
a second argument:

- `parent` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) | [`<URL>`](59-url.md#the-whatwg-url-api) An optional absolute parent module URL to resolve from. **Default:** `import.meta.url`

## Interoperability with CommonJS

### `import` statements

An `import` statement can reference an ES module or a CommonJS module.
`import` statements are permitted only in ES modules, but dynamic [`import()`](#import-expressions)
expressions are supported in CommonJS for loading ES modules.

When importing [CommonJS modules](#commonjs-namespaces), the
`module.exports` object is provided as the default export. Named exports may be
available, provided by static analysis as a convenience for better ecosystem
compatibility.

### `require`

The CommonJS module `require` currently only supports loading synchronous ES
modules (that is, ES modules that do not use top-level `await`).

See [Loading ECMAScript modules using `require()`](33-modules.md#loading-ecmascript-modules-using-require) for details.

### CommonJS Namespaces

Added in: v14.13.0

**History**

| Version | Changes |
| --- | --- |
| v23.0.0 | Added `'module.exports'` export marker to CJS namespaces. |

CommonJS modules consist of a `module.exports` object which can be of any type.

To support this, when importing CommonJS from an ECMAScript module, a namespace
wrapper for the CommonJS module is constructed, which always provides a
`default` export key pointing to the CommonJS `module.exports` value.

In addition, a heuristic static analysis is performed against the source text of
the CommonJS module to get a best-effort static list of exports to provide on
the namespace from values on `module.exports`. This is necessary since these
namespaces must be constructed prior to the evaluation of the CJS module.

These CommonJS namespace objects also provide the `default` export as a
`'module.exports'` named export, in order to unambiguously indicate that their
representation in CommonJS uses this value, and not the namespace value. This
mirrors the semantics of the handling of the `'module.exports'` export name in
[`require(esm)`](33-modules.md#loading-ecmascript-modules-using-require) interop support.

When importing a CommonJS module, it can be reliably imported using the ES
module default import or its corresponding sugar syntax:

```js
import { default as cjs } from 'cjs';
// Identical to the above
import cjsSugar from 'cjs';

console.log(cjs);
console.log(cjs === cjsSugar);
// Prints:
//   <module.exports>
//   true
```

This Module Namespace Exotic Object can be directly observed either when using
`import * as m from 'cjs'` or a dynamic import:

```js
import * as m from 'cjs';
console.log(m);
console.log(m === await import('cjs'));
// Prints:
//   [Module] { default: <module.exports>, 'module.exports': <module.exports> }
//   true
```

For better compatibility with existing usage in the JS ecosystem, Node.js
in addition attempts to determine the CommonJS named exports of every imported
CommonJS module to provide them as separate ES module exports using a static
analysis process.

For example, consider a CommonJS module written:

```js
// cjs.cjs
exports.name = 'exported';
```

The preceding module supports named imports in ES modules:

```js
import { name } from './cjs.cjs';
console.log(name);
// Prints: 'exported'

import cjs from './cjs.cjs';
console.log(cjs);
// Prints: { name: 'exported' }

import * as m from './cjs.cjs';
console.log(m);
// Prints:
//   [Module] {
//     default: { name: 'exported' },
//     'module.exports': { name: 'exported' },
//     name: 'exported'
//   }
```

As can be seen from the last example of the Module Namespace Exotic Object being
logged, the `name` export is copied off of the `module.exports` object and set
directly on the ES module namespace when the module is imported.

Live binding updates or new exports added to `module.exports` are not detected
for these named exports.

The detection of named exports is based on common syntax patterns but does not
always correctly detect named exports. In these cases, using the default
import form described above can be a better option.

Named exports detection covers many common export patterns, reexport patterns
and build tool and transpiler outputs. See [merve](https://github.com/anonrig/merve/tree/v1.0.0) for the exact
semantics implemented.

### Differences between ES modules and CommonJS

#### No `require`, `exports`, or `module.exports`

In most cases, the ES module `import` can be used to load CommonJS modules.

If needed, a `require` function can be constructed within an ES module using
[`module.createRequire()`](35-module.md#modulecreaterequirefilename).

#### No `__filename` or `__dirname`

These CommonJS variables are not available in ES modules.

`__filename` and `__dirname` use cases can be replicated via
[`import.meta.filename`](#importmetafilename) and [`import.meta.dirname`](#importmetadirname).

#### No Addon Loading

[Addons](08-addons.md) are not currently supported with ES module imports.

They can instead be loaded with [`module.createRequire()`](35-module.md#modulecreaterequirefilename) or
[`process.dlopen`](43-process.md#processdlopenmodule-filename-flags).

#### No `require.main`

To replace `require.main === module`, there is the [`import.meta.main`](#importmetamain) API.

#### No `require.resolve`

Relative resolution can be handled via `new URL('./local', import.meta.url)`.

For a complete `require.resolve` replacement, there is the
[import.meta.resolve](#importmetaresolvespecifier) API.

Alternatively `module.createRequire()` can be used.

#### No `NODE_PATH`

`NODE_PATH` is not part of resolving `import` specifiers. Please use symlinks
if this behavior is desired.

#### No `require.extensions`

`require.extensions` is not used by `import`. Module customization hooks can
provide a replacement.

#### No `require.cache`

`require.cache` is not used by `import` as the ES module loader has its own
separate cache.

## JSON modules

**History**

| Version | Changes |
| --- | --- |
| v23.1.0, v22.12.0, v20.18.3, v18.20.5 | JSON modules are no longer experimental. |

JSON files can be referenced by `import`:

```js
import packageConfig from './package.json' with { type: 'json' };
```

The `with { type: 'json' }` syntax is mandatory; see [Import Attributes](#import-attributes).

The imported JSON only exposes a `default` export. There is no support for named
exports. A cache entry is created in the CommonJS cache to avoid duplication.
The same object is returned in CommonJS if the JSON module has already been
imported from the same path.

## Text modules

Stability: 1.0 - Early development

Text modules are available behind the `--experimental-import-text` flag.

Text files can be referenced by `import`:

```js
import message from './message.txt' with { type: 'text' };
```

The `with { type: 'text' }` syntax is mandatory; see [Import Attributes](#import-attributes).

The imported text only exposes a `default` export whose value is the module
source as a string.

## Wasm modules

**History**

| Version | Changes |
| --- | --- |
| v24.5.0, v22.19.0 | Wasm modules no longer require the `--experimental-wasm-modules` flag. |

Importing both WebAssembly module instances and WebAssembly source phase
imports is supported.

Both of these integrations are in line with the
[ES Module Integration Proposal for WebAssembly](https://github.com/webassembly/esm-integration).

When WebAssembly is unavailable in the current environment, importing a Wasm
module throws [`ERR_WEBASSEMBLY_NOT_SUPPORTED`](22-errors.md#err_webassembly_not_supported).
This also applies to source phase imports. Node.js does not provide an
alternative implementation when WebAssembly is unavailable.

### Wasm Source Phase Imports

Added in: v24.0.0

Stability: 1.2 - Release candidate

The [Source Phase Imports](https://github.com/tc39/proposal-source-phase-imports) proposal allows the `import source` keyword
combination to import a `WebAssembly.Module` object directly, instead of getting
a module instance already instantiated with its dependencies.

This is useful when needing custom instantiations for Wasm, while still
resolving and loading it through the ES module integration.

For example, to create multiple instances of a module, or to pass custom imports
into a new instance of `library.wasm`:

```js
import source libraryModule from './library.wasm';

const instance1 = await WebAssembly.instantiate(libraryModule, importObject1);

const instance2 = await WebAssembly.instantiate(libraryModule, importObject2);
```

In addition to the static source phase, there is also a dynamic variant of the
source phase via the `import.source` dynamic phase import syntax:

```js
const dynamicLibrary = await import.source('./library.wasm');

const instance = await WebAssembly.instantiate(dynamicLibrary, importObject);
```

### JavaScript String Builtins

Added in: v24.5.0, v22.19.0

Stability: 1.2 - Release candidate

When importing WebAssembly modules, the
[WebAssembly JS String Builtins Proposal](https://github.com/WebAssembly/js-string-builtins) is automatically enabled through the
ESM Integration. This allows WebAssembly modules to directly use efficient
compile-time string builtins from the `wasm:js-string` namespace.

For example, the following Wasm module exports a string `getLength` function using
the `wasm:js-string` `length` builtin:

```text
(module
  ;; Compile-time import of the string length builtin.
  (import "wasm:js-string" "length" (func $string_length (param externref) (result i32)))

  ;; Define getLength, taking a JS value parameter assumed to be a string,
  ;; calling string length on it and returning the result.
  (func $getLength (param $str externref) (result i32)
    local.get $str
    call $string_length
  )

  ;; Export the getLength function.
  (export "getLength" (func $get_length))
)
```

```js
import { getLength } from './string-len.wasm';
getLength('foo'); // Returns 3.
```

Wasm builtins are compile-time imports that are linked during module compilation
rather than during instantiation. They do not behave like normal module graph
imports and they cannot be inspected via `WebAssembly.Module.imports(mod)`
or virtualized unless recompiling the module using the direct
`WebAssembly.compile` API with string builtins disabled.

String constants may also be imported from the `wasm:js/string-constants` builtin
import URL, allowing static JS string globals to be defined:

```text
(module
  (import "wasm:js/string-constants" "hello" (global $hello externref))
)
```

Importing a module in the source phase before it has been instantiated will also
use the compile-time builtins automatically:

```js
import source mod from './string-len.wasm';
const { exports: { getLength } } = await WebAssembly.instantiate(mod, {});
getLength('foo'); // Also returns 3.
```

### Wasm Instance Phase Imports

Stability: 1.1 - Active development

Instance imports allow any `.wasm` files to be imported as normal modules,
supporting their module imports in turn.

For example, an `index.js` containing:

```js
import * as M from './library.wasm';
console.log(M);
```

executed under:

```bash
node index.mjs
```

would provide the exports interface for the instantiation of `library.wasm`.

### Reserved Wasm Namespaces

Added in: v24.5.0, v22.19.0

When importing WebAssembly module instances, they cannot use import module
names or import/export names that start with reserved prefixes:

- `wasm-js:` - reserved in all module import names, module names and export
  names.
- `wasm:` - reserved in module import names and export names (imported module
  names are allowed in order to support future builtin polyfills).

Importing a module using the above reserved names will throw a
`WebAssembly.LinkError`.

## Top-level `await`

Added in: v14.8.0

The `await` keyword may be used in the top level body of an ECMAScript module.

Assuming an `a.mjs` with

```js
export const five = await Promise.resolve(5);
```

And a `b.mjs` with

```js
import { five } from './a.mjs';

console.log(five); // Logs `5`
```

```bash
node b.mjs # works
```

If a top level `await` expression never resolves, the `node` process will exit
with a `13` [status code](43-process.md#exit-codes).

```js
import { spawn } from 'node:child_process';
import { execPath } from 'node:process';

spawn(execPath, [
  '--input-type=module',
  '--eval',
  // Never-resolving Promise:
  'await new Promise(() => {})',
]).once('exit', (code) => {
  console.log(code); // Logs `13`
});
```

## Loaders

The former Loaders documentation is now at
[Modules: Customization hooks](35-module.md#customization-hooks).

## Resolution and loading algorithm

### Features

The default resolver has the following properties:

- FileURL-based resolution as is used by ES modules
- Relative and absolute URL resolution
- No default extensions
- No folder mains
- Bare specifier package resolution lookup through node\_modules
- Does not fail on unknown extensions or protocols
- Can optionally provide a hint of the format to the loading phase

The default loader has the following properties

- Support for builtin module loading via `node:` URLs
- Support for "inline" module loading via `data:` URLs
- Support for `file:` module loading
- Fails on any other URL protocol
- Fails on unknown extensions for `file:` loading
  (supports only `.cjs`, `.js`, and `.mjs`)

When the [`--experimental-package-map`](13-cli.md#--experimental-package-mappath) flag is enabled, bare specifier
resolution first consults the package map configuration. If the importing
module is within a mapped package and the specifier matches a declared
dependency, the package map resolution takes precedence. See [Package maps](36-packages.md#package-maps)
for details.

### Resolution algorithm

The algorithm to load an ES module specifier is given through the
**ESM\_RESOLVE** method below. It returns the resolved URL for a
module specifier relative to a parentURL.

The resolution algorithm determines the full resolved URL for a module
load, along with its suggested module format. The resolution algorithm
does not determine whether the resolved URL protocol can be loaded,
or whether the file extensions are permitted, instead these validations
are applied by Node.js during the load phase
(for example, if it was asked to load a URL that has a protocol that is
not `file:`, `data:` or `node:`.

The algorithm also tries to determine the format of the file based
on the extension (see `ESM_FILE_FORMAT` algorithm below). If it does
not recognize the file extension (eg if it is not `.mjs`, `.cjs`, or
`.json`), then a format of `undefined` is returned,
which will throw during the load phase.

The algorithm to determine the module format of a resolved URL is
provided by **ESM\_FILE\_FORMAT**, which returns the unique module
format for any file. The *"module"* format is returned for an ECMAScript
Module, while the *"commonjs"* format is used to indicate loading through the
legacy CommonJS loader. Additional formats such as *"addon"* can be extended in
future updates.

In the following algorithms, all subroutine errors are propagated as errors
of these top-level routines unless stated otherwise.

*defaultConditions* is the conditional environment name array,
`["node", "import"]`.

The resolver can throw the following errors:

- *Invalid Module Specifier*: Module specifier is an invalid URL, package name
  or package subpath specifier.
- *Invalid Package Configuration*: package.json configuration is invalid or
  contains an invalid configuration.
- *Invalid Package Target*: Package exports or imports define a target module
  for the package that is an invalid type or string target.
- *Package Path Not Exported*: Package exports do not define or permit a target
  subpath in the package for the given module.
- *Package Import Not Defined*: Package imports do not define the specifier.
- *Module Not Found*: The package or module requested does not exist.
- *Unsupported Directory Import*: The resolved path corresponds to a directory,
  which is not a supported target for module imports.

### Resolution Algorithm Specification

**ESM\_RESOLVE**(*specifier*, *parentURL*)

> 1. Let *resolved* be **undefined**.
> 2. If *specifier* is a valid URL, then
>    1. Set *resolved* to the result of parsing and reserializing
>       *specifier* as a URL.
> 3. Otherwise, if *specifier* starts with *"/"*, *"./"*, or *"../"*, then
>    1. Set *resolved* to the URL resolution of *specifier* relative to
>       *parentURL*.
> 4. Otherwise, if *specifier* starts with *"#"*, then
>    1. Set *resolved* to the result of
>       **PACKAGE\_IMPORTS\_RESOLVE**(*specifier*,
>       *parentURL*, *defaultConditions*).
> 5. Otherwise,
>    1. Note: *specifier* is now a bare specifier.
>    2. Set *resolved* the result of
>       **PACKAGE\_RESOLVE**(*specifier*, *parentURL*).
> 6. Let *format* be **undefined**.
> 7. If *resolved* is a *"file:"* URL, then
>    1. If *resolved* contains any percent encodings of *"/"* or *"\"* (*"%2F"*
>       and *"%5C"* respectively), then
>       1. Throw an *Invalid Module Specifier* error.
>    2. If the file at *resolved* is a directory, then
>       1. Throw an *Unsupported Directory Import* error.
>    3. If the file at *resolved* does not exist, then
>       1. Throw a *Module Not Found* error.
>    4. Set *resolved* to the real path of *resolved*, maintaining the
>       same URL querystring and fragment components.
>    5. Set *format* to the result of **ESM\_FILE\_FORMAT**(*resolved*).
> 8. Otherwise,
>    1. Set *format* the module format of the content type associated with the
>       URL *resolved*.
> 9. Return *format* and *resolved* to the loading phase

**PACKAGE\_RESOLVE**(*packageSpecifier*, *parentURL*)

> 1. Let *packageName* be **undefined**.
> 2. If *packageSpecifier* is an empty string, then
>    1. Throw an *Invalid Module Specifier* error.
> 3. If *packageSpecifier* is a Node.js builtin module name, then
>    1. Return the string *"node:"* concatenated with *packageSpecifier*.
> 4. If *packageSpecifier* does not start with *"@"*, then
>    1. Set *packageName* to the substring of *packageSpecifier* until the first
>       *"/"* separator or the end of the string.
> 5. Otherwise,
>    1. If *packageSpecifier* does not contain a *"/"* separator, then
>       1. Throw an *Invalid Module Specifier* error.
>    2. Set *packageName* to the substring of *packageSpecifier*
>       until the second *"/"* separator or the end of the string.
> 6. If *packageName* starts with *"."* or contains *"\"* or *"%"*, then
>    1. Throw an *Invalid Module Specifier* error.
> 7. Let *packageSubpath* be *"."* concatenated with the substring of
>    *packageSpecifier* from the position at the length of *packageName*.
> 8. Let *selfUrl* be the result of
>    **PACKAGE\_SELF\_RESOLVE**(*packageName*, *packageSubpath*, *parentURL*).
> 9. If *selfUrl* is not **undefined**, return *selfUrl*.
> 10. While *parentURL* is not the file system root,
>     1. Let *packageURL* be the URL resolution of *"node\_modules/"*
>        concatenated with *packageName*, relative to *parentURL*.
>     2. Set *parentURL* to the parent folder URL of *parentURL*.
>     3. If the folder at *packageURL* does not exist, then
>        1. Continue the next loop iteration.
>     4. Let *pjson* be the result of **READ\_PACKAGE\_JSON**(*packageURL*).
>     5. If *pjson* is not **null** and *pjson*.*exports* is not **null** or
>        **undefined**, then
>        1. Return the result of **PACKAGE\_EXPORTS\_RESOLVE**(*packageURL*,
>           *packageSubpath*, *pjson.exports*, *defaultConditions*).
>     6. Otherwise, if *packageSubpath* is equal to *"."*, then
>        1. If *pjson.main* is a string, then
>           1. Return the URL resolution of *main* in *packageURL*.
>     7. Otherwise,
>        1. Return the URL resolution of *packageSubpath* in *packageURL*.
> 11. Throw a *Module Not Found* error.

**PACKAGE\_SELF\_RESOLVE**(*packageName*, *packageSubpath*, *parentURL*)

> 1. Let *packageURL* be the result of **LOOKUP\_PACKAGE\_SCOPE**(*parentURL*).
> 2. If *packageURL* is **null**, then
>    1. Return **undefined**.
> 3. Let *pjson* be the result of **READ\_PACKAGE\_JSON**(*packageURL*).
> 4. If *pjson* is **null** or if *pjson*.*exports* is **null** or
>    **undefined**, then
>    1. Return **undefined**.
> 5. If *pjson.name* is equal to *packageName*, then
>    1. Return the result of **PACKAGE\_EXPORTS\_RESOLVE**(*packageURL*,
>       *packageSubpath*, *pjson.exports*, *defaultConditions*).
> 6. Otherwise, return **undefined**.

**PACKAGE\_EXPORTS\_RESOLVE**(*packageURL*, *subpath*, *exports*, *conditions*)

Note: This function is directly invoked by the CommonJS resolution algorithm.

> 1. If *exports* is an Object with both a key starting with *"."* and a key not
>    starting with *"."*, throw an *Invalid Package Configuration* error.
> 2. If *subpath* is equal to *"."*, then
>    1. Let *mainExport* be **undefined**.
>    2. If *exports* is a String or Array, or an Object containing no keys
>       starting with *"."*, then
>       1. Set *mainExport* to *exports*.
>    3. Otherwise if *exports* is an Object containing a *"."* property, then
>       1. Set *mainExport* to *exports*[*"."*].
>    4. If *mainExport* is not **undefined**, then
>       1. Let *resolved* be the result of **PACKAGE\_TARGET\_RESOLVE**(
>          *packageURL*, *mainExport*, **null**, **false**, *conditions*).
>       2. If *resolved* is not **null** or **undefined**, return *resolved*.
> 3. Otherwise, if *exports* is an Object and all keys of *exports* start with
>    *"."*, then
>    1. Assert: *subpath* begins with *"./"*.
>    2. Let *resolved* be the result of **PACKAGE\_IMPORTS\_EXPORTS\_RESOLVE**(
>       *subpath*, *exports*, *packageURL*, **false**, *conditions*).
>    3. If *resolved* is not **null** or **undefined**, return *resolved*.
> 4. Throw a *Package Path Not Exported* error.

**PACKAGE\_IMPORTS\_RESOLVE**(*specifier*, *parentURL*, *conditions*)

Note: This function is directly invoked by the CommonJS resolution algorithm.

> 1. Assert: *specifier* begins with *"#"*.
> 2. If *specifier* is exactly equal to *"#"*, then
>    1. Throw an *Invalid Module Specifier* error.
> 3. Let *packageURL* be the result of **LOOKUP\_PACKAGE\_SCOPE**(*parentURL*).
> 4. If *packageURL* is not **null**, then
>    1. Let *pjson* be the result of **READ\_PACKAGE\_JSON**(*packageURL*).
>    2. If *pjson.imports* is a non-null Object, then
>       1. Let *resolved* be the result of
>          **PACKAGE\_IMPORTS\_EXPORTS\_RESOLVE**(
>          *specifier*, *pjson.imports*, *packageURL*, **true**, *conditions*).
>       2. If *resolved* is not **null** or **undefined**, return *resolved*.
> 5. Throw a *Package Import Not Defined* error.

**PACKAGE\_IMPORTS\_EXPORTS\_RESOLVE**(*matchKey*, *matchObj*, *packageURL*,
*isImports*, *conditions*)

> 1. If *matchKey* ends in *"/"*, then
>    1. Throw an *Invalid Module Specifier* error.
> 2. If *matchKey* is a key of *matchObj* and does not contain *"\*"*, then
>    1. Let *target* be the value of *matchObj*[*matchKey*].
>    2. Return the result of **PACKAGE\_TARGET\_RESOLVE**(*packageURL*,
>       *target*, **null**, *isImports*, *conditions*).
> 3. Let *expansionKeys* be the list of keys of *matchObj* containing only a
>    single *"\*"*, sorted by the sorting function **PATTERN\_KEY\_COMPARE**
>    which orders in descending order of specificity.
> 4. For each key *expansionKey* in *expansionKeys*, do
>    1. Let *patternBase* be the substring of *expansionKey* up to but excluding
>       the first *"\*"* character.
>    2. If *matchKey* starts with but is not equal to *patternBase*, then
>       1. Let *patternTrailer* be the substring of *expansionKey* from the
>          index after the first *"\*"* character.
>       2. If *patternTrailer* has zero length, or if *matchKey* ends with
>          *patternTrailer* and the length of *matchKey* is greater than or
>          equal to the length of *expansionKey*, then
>          1. Let *target* be the value of *matchObj*[*expansionKey*].
>          2. Let *patternMatch* be the substring of *matchKey* starting at the
>             index of the length of *patternBase* up to the length of
>             *matchKey* minus the length of *patternTrailer*.
>          3. Return the result of **PACKAGE\_TARGET\_RESOLVE**(*packageURL*,
>             *target*, *patternMatch*, *isImports*, *conditions*).
> 5. Return **null**.

**PATTERN\_KEY\_COMPARE**(*keyA*, *keyB*)

> 1. Assert: *keyA* contains only a single *"\*"*.
> 2. Assert: *keyB* contains only a single *"\*"*.
> 3. Let *baseLengthA* be the index of *"\*"* in *keyA*.
> 4. Let *baseLengthB* be the index of *"\*"* in *keyB*.
> 5. If *baseLengthA* is greater than *baseLengthB*, return -1.
> 6. If *baseLengthB* is greater than *baseLengthA*, return 1.
> 7. If the length of *keyA* is greater than the length of *keyB*, return -1.
> 8. If the length of *keyB* is greater than the length of *keyA*, return 1.
> 9. Return 0.

**PACKAGE\_TARGET\_RESOLVE**(*packageURL*, *target*, *patternMatch*,
*isImports*, *conditions*)

> 1. If *target* is a String, then
>    1. If *target* does not start with *"./"*, then
>       1. If *isImports* is **false**, or if *target* starts with *"../"* or
>          *"/"*, or if *target* is a valid URL, then
>          1. Throw an *Invalid Package Target* error.
>       2. If *patternMatch* is a String, then
>          1. Return **PACKAGE\_RESOLVE**(*target* with every instance of *"\*"*
>             replaced by *patternMatch*, *packageURL* + *"/"*).
>       3. Return **PACKAGE\_RESOLVE**(*target*, *packageURL* + *"/"*).
>    2. If *target* split on *"/"* or *"\"* contains any *""*, *"."*, *".."*,
>       or *"node\_modules"* segments after the first *"."* segment, case
>       insensitive and including percent encoded variants, throw an *Invalid
>       Package Target* error.
>    3. Let *resolvedTarget* be the URL resolution of the concatenation of
>       *packageURL* and *target*.
>    4. Assert: *packageURL* is contained in *resolvedTarget*.
>    5. If *patternMatch* is **null**, then
>       1. Return *resolvedTarget*.
>    6. If *patternMatch* split on *"/"* or *"\"* contains any *""*, *"."*,
>       *".."*, or *"node\_modules"* segments, case insensitive and including
>       percent encoded variants, throw an *Invalid Module Specifier* error.
>    7. Return the URL resolution of *resolvedTarget* with every instance of
>       *"\*"* replaced with *patternMatch*.
> 2. Otherwise, if *target* is a non-null Object, then
>    1. If *target* contains any index property keys, as defined in ECMA-262
>       [6.1.7 Array Index](https://tc39.es/ecma262/#integer-index), throw an *Invalid Package Configuration* error.
>    2. For each property *p* of *target*, in object insertion order as,
>       1. If *p* equals *"default"* or *conditions* contains an entry for *p*,
>          then
>          1. Let *targetValue* be the value of the *p* property in *target*.
>          2. Let *resolved* be the result of **PACKAGE\_TARGET\_RESOLVE**(
>             *packageURL*, *targetValue*, *patternMatch*, *isImports*,
>             *conditions*).
>          3. If *resolved* is equal to **undefined**, continue the loop.
>          4. Return *resolved*.
>    3. Return **undefined**.
> 3. Otherwise, if *target* is an Array, then
>    1. If \_target.length is zero, return **null**.
>    2. For each item *targetValue* in *target*, do
>       1. Let *resolved* be the result of **PACKAGE\_TARGET\_RESOLVE**(
>          *packageURL*, *targetValue*, *patternMatch*, *isImports*,
>          *conditions*), continuing the loop on any *Invalid Package Target*
>          error.
>       2. If *resolved* is **undefined**, continue the loop.
>       3. Return *resolved*.
>    3. Return or throw the last fallback resolution **null** return or error.
> 4. Otherwise, if *target* is *null*, return **null**.
> 5. Otherwise throw an *Invalid Package Target* error.

**ESM\_FILE\_FORMAT**(*url*)

> 1. Assert: *url* corresponds to an existing file.
> 2. If *url* ends in *".mjs"*, then
>    1. Return *"module"*.
> 3. If *url* ends in *".cjs"*, then
>    1. Return *"commonjs"*.
> 4. If *url* ends in *".json"*, then
>    1. Return *"json"*.
> 5. If *url* ends in
>    *".wasm"*, then
>    1. Return *"wasm"*.
> 6. If `--experimental-addon-modules` is enabled and *url* ends in
>    *".node"*, then
>    1. Return *"addon"*.
> 7. Let *packageURL* be the result of **LOOKUP\_PACKAGE\_SCOPE**(*url*).
> 8. Let *pjson* be the result of **READ\_PACKAGE\_JSON**(*packageURL*).
> 9. Let *packageType* be **null**.
> 10. If *pjson?.type* is *"module"* or *"commonjs"*, then
>     1. Set *packageType* to *pjson.type*.
> 11. If *url* ends in *".js"*, then
>     1. If *packageType* is not **null**, then
>        1. Return *packageType*.
>     2. If the result of **DETECT\_MODULE\_SYNTAX**(*source*) is true, then
>        1. Return *"module"*.
>     3. Return *"commonjs"*.
> 12. If *url* does not have any extension, then
>     1. If *packageType* is *"module"* and the file at *url* contains the
>        "application/wasm" content type header for a WebAssembly module, then
>        1. Return *"wasm"*.
>     2. If *packageType* is not **null**, then
>        1. Return *packageType*.
>     3. If the result of **DETECT\_MODULE\_SYNTAX**(*source*) is true, then
>        1. Return *"module"*.
>     4. Return *"commonjs"*.
> 13. Return **undefined** (will throw during load phase).

**LOOKUP\_PACKAGE\_SCOPE**(*url*)

> 1. Let *scopeURL* be *url*.
> 2. While *scopeURL* is not the file system root,
>    1. Set *scopeURL* to the parent URL of *scopeURL*.
>    2. If *scopeURL* ends in a *"node\_modules"* path segment, return **null**.
>    3. Let *pjsonURL* be the resolution of *"package.json"* within
>       *scopeURL*.
>    4. if the file at *pjsonURL* exists, then
>       1. Return *scopeURL*.
> 3. Return **null**.

**READ\_PACKAGE\_JSON**(*packageURL*)

> 1. Let *pjsonURL* be the resolution of *"package.json"* within *packageURL*.
> 2. If the file at *pjsonURL* does not exist, then
>    1. Return **null**.
> 3. If the file at *packageURL* does not parse as valid JSON, then
>    1. Throw an *Invalid Package Configuration* error.
> 4. Return the parsed JSON source of the file at *pjsonURL*.

**DETECT\_MODULE\_SYNTAX**(*source*)

> 1. Parse *source* as an ECMAScript module.
> 2. If the parse is successful, then
>    1. If *source* contains top-level `await`, static `import` or `export`
>       statements, or `import.meta`, return **true**.
>    2. If *source* contains a top-level lexical declaration (`const`, `let`,
>       or `class`) of any of the CommonJS wrapper variables (`require`,
>       `exports`, `module`, `__filename`, or `__dirname`) then return **true**.
> 3. Return **false**.

### Customizing ESM specifier resolution algorithm

[Module customization hooks](35-module.md#customization-hooks) provide a mechanism for customizing the ESM
specifier resolution algorithm. An example that provides CommonJS-style
resolution for ESM specifiers is [commonjs-extension-resolution-loader](https://github.com/nodejs/loaders-test/tree/main/commonjs-extension-resolution-loader).

[Contents](../README.md) | [Previous: Modules: CommonJS modules](33-modules.md) | [Next: Modules: node:module API](35-module.md)
