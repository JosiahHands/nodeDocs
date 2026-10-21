[Contents](../README.md) | [Previous: V8](61-v8.md) | [Next: VM](63-vm.md)

Source: [https://nodejs.org/docs/latest/api/vfs.html](https://nodejs.org/docs/latest/api/vfs.html)

# Virtual File System

Added in: v26.4.0

[Stability: 1](01-documentation.md#stability-index) - Experimental

The `node:vfs` module provides a virtual file system with a `node:fs`-like API.
It is useful for tests, fixtures, embedded assets, and other scenarios where you
need a self-contained file system without touching the actual file-system.

To access it:

*ES module version:*

```js
import vfs from 'node:vfs';
```

*CommonJS version:*

```js
const vfs = require('node:vfs');
```

This module is only available under the `node:` scheme, and only when Node.js
is started with the `--experimental-vfs` flag.

## Security

The VFS API is not a sandbox, permission system, or access-control mechanism.
It does not isolate untrusted code from the host file system or from other
Node.js capabilities. Code that can access a [`VirtualFileSystem`](#class-virtualfilesystem) instance,
mount it, select its provider, or pass paths to it is trusted application code.

Mounting a VFS only redirects supported [`node:fs`](24-fs-part1.md) calls whose resolved paths
are under the mount point. It does not prevent code from using other paths or
other Node.js APIs to access resources available to the process.
[`RealFSProvider`](#class-realfsprovider) maps VFS paths under its configured root and rejects paths
that resolve outside that root, but that check is not a security boundary.
[`ZipProvider`](#class-zipprovider) has no real file-system paths of its own to escape; its
entries only ever exist within the archive's own namespace. Do not rely on VFS
to run untrusted code; use operating-system-level isolation, such as separate
users, containers, or platform sandboxes, when a security boundary is
required.

## Basic usage

```js
const vfs = require('node:vfs');

const myVfs = vfs.create();
myVfs.mkdirSync('/dir', { recursive: true });
myVfs.writeFileSync('/dir/hello.txt', 'Hello, VFS!');

console.log(myVfs.readFileSync('/dir/hello.txt', 'utf8')); // 'Hello, VFS!'
```

`vfs.create()` returns a [`VirtualFileSystem`](#class-virtualfilesystem) instance backed by a
[`MemoryProvider`](#class-memoryprovider) by default. The instance exposes synchronous,
callback-based, and promise-based file system methods that mirror the
shape of the [`node:fs`](24-fs-part1.md) API. All paths are POSIX-style and absolute
(starting with `/`).

By default, the file tree is private to the VFS instance. To expose
it through the global `node:fs` module, `require()`, and `import`,
call [`vfs.mount()`](#vfsmount); call [`vfs.unmount()`](#vfsunmount) (or rely on a
`using` declaration) to detach again.

## `vfs.create([provider][, options])`

Added in: v26.4.0

- `provider` [`<VirtualProvider>`](62-vfs.md#class-virtualprovider) The provider to use. **Default:**
  `new MemoryProvider()`.
- `options` [`<Object>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Object)
  - `emitExperimentalWarning` [`<boolean>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#boolean_type) Whether to emit the experimental
    warning when the instance is created. **Default:** `true`.
- Returns: [`<VirtualFileSystem>`](62-vfs.md#class-virtualfilesystem)

Convenience factory equivalent to `new VirtualFileSystem(provider, options)`.

```js
const vfs = require('node:vfs');

// Default in-memory provider
const memoryVfs = vfs.create();

// Explicit provider
const realVfs = vfs.create(new vfs.RealFSProvider('/tmp/vfs-root'));
```

## `vfs.registerProvider(entry)`

Added in: v26.10.0

- `entry` [`<Object>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Object)
  - `name` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) A short identifier, used in diagnostics.
  - `canHandle` [`<Function>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Function) Called with the resolved path and its [`fs.Stats`](24-fs-part6.md#class-fsstats). Returns `true` if this provider should back the source.
  - `create` [`<Function>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Function) Called with the resolved path and its [`fs.Stats`](24-fs-part6.md#class-fsstats).
    Returns the [`<VirtualProvider>`](62-vfs.md#class-virtualprovider) backing the source.

Registers a provider that [`--vfs-load`](13-cli.md#--vfs-loadsource) can select for a source it
recognizes, so a file format Node.js has no built-in provider for can still be
mounted.

A source is claimed by the first provider whose `canHandle()` returns `true`.
Registered providers are consulted before the built-in ones, newest
registration first, and are offered directories as well as files, so a
registered provider can back, wrap, or vet any source. If none claims the
source, the built-in providers handle it: a directory with
[`RealFSProvider`](#class-realfsprovider), and a file whose bytes are a ZIP archive with
[`ZipProvider`](#class-zipprovider).

Providers must be registered before the source is mounted. Register from a
module preloaded with [`--require`](13-cli.md#-r---require-module) or [`--import`](13-cli.md#--importmodule):

```js
// provider.js, preloaded with --require
const fs = require('node:fs');
const vfs = require('node:vfs');

const MAGIC = Buffer.from('CUSTOMFMT');

vfs.registerProvider({
  name: 'customfmt',
  canHandle(path, stats) {
    if (!stats.isFile()) return false;
    const head = Buffer.alloc(MAGIC.length);
    const fd = fs.openSync(path, 'r');
    try {
      fs.readSync(fd, head, 0, MAGIC.length, 0);
    } finally {
      fs.closeSync(fd);
    }
    return head.equals(MAGIC);
  },
  create(path) {
    return new MyCustomProvider(path);
  },
});
```

```console
$ node --experimental-vfs --require ./provider.js \
       --vfs-load archive.customfmt
```

## `vfs.vfsBase()`

Added in: v26.11.0

- Returns: [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) The absolute path of the [reserved root directory](#the-reserved-root-directory).

Returns the directory that holds the mount points of every mounted virtual file
system, which is `path.join(os.devNull, 'vfs')`. Reading it lists what is
mounted; see [The reserved root directory](#the-reserved-root-directory).

```js
const vfs = require('node:vfs');
const fs = require('node:fs');

const myVfs = vfs.create();
const mountPoint = myVfs.mount();

fs.readdirSync(vfs.vfsBase()); // The name of every mount point in it
mountPoint.startsWith(vfs.vfsBase()); // true
```

## Class: `VirtualFileSystem`

Added in: v26.4.0

A `VirtualFileSystem` wraps a [`VirtualProvider`](#class-virtualprovider) and exposes a
`node:fs`-like API. Each instance maintains its own file tree.

### `new VirtualFileSystem([provider][, options])`

Added in: v26.4.0

- `provider` [`<VirtualProvider>`](62-vfs.md#class-virtualprovider) The provider to use. **Default:**
  `new MemoryProvider()`.
- `options` [`<Object>`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Object)
  - `emitExperimentalWarning` [`<boolean>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#boolean_type) Whether to emit the experimental
    warning. **Default:** `true`.

### `vfs.mount()`

Added in: v26.9.0

- Returns: [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) The absolute mount point.

Mounts the virtual file system and returns the resulting mount point.
After mounting, files in the VFS can be accessed through the
`node:fs` module and resolved through `require()` and `import`
using paths under the returned mount point.

Mount points always live inside a reserved namespace that cannot have child file system entries,
so virtual paths never conflate with (or shadow) real paths. A mount point is obtained from what
`vfs.mount()` returns or from [`vfs.mountPoint`](#vfsmountpoint), and the mount points of all mounted file
systems can be listed by reading the [reserved root directory](#the-reserved-root-directory), whose path [`vfs.vfsBase()`](#vfsvfsbase)
returns. The name of a mount point within that directory is assigned at runtime, so it is not
something to construct or hard-code.

```js
const vfs = require('node:vfs');
const fs = require('node:fs');

const myVfs = vfs.create();
myVfs.writeFileSync('/data.txt', 'Hello');
const mountPoint = myVfs.mount();
// e.g. '/dev/null/vfs/0'

fs.readFileSync(`${mountPoint}/data.txt`, 'utf8'); // 'Hello'
```

Like any mount point, the mount point cannot be removed or renamed, nor
replaced by renaming something else onto it: [`fs.rmdir()`](24-fs-part4.md#fsrmdirpath-options-callback) and
[`fs.rename()`](24-fs-part4.md#fsrenameoldpath-newpath-callback) fail with `EBUSY`. A recursive [`fs.rm()`](24-fs-part4.md#fsrmpath-options-callback) of the mount
point empties the file system before failing the same way.

Each `VirtualFileSystem` instance may be mounted at most once at a
time. Attempting to mount an already-mounted instance throws
`ERR_INVALID_STATE`. Because each instance mounts inside its own
per-layer namespace, mounts from different instances can never
overlap.

The VFS supports the [Explicit Resource Management](https://github.com/tc39/proposal-explicit-resource-management) proposal. Use
a `using` declaration to unmount automatically when leaving scope:

```js
const vfs = require('node:vfs');
const fs = require('node:fs');

let mountPoint;
{
  using myVfs = vfs.create();
  myVfs.writeFileSync('/data.txt', 'Hello');
  mountPoint = myVfs.mount();

  fs.readFileSync(`${mountPoint}/data.txt`, 'utf8'); // 'Hello'
} // VFS is automatically unmounted here

fs.existsSync(`${mountPoint}/data.txt`); // false
```

### `vfs.unmount()`

Added in: v26.9.0

Unmounts the virtual file system. After unmounting, virtual files
are no longer reachable through `node:fs`, `require()`, or `import`.
The same instance may be mounted again by calling `mount()`.

This method is idempotent: calling `unmount()` on a VFS that is not
currently mounted has no effect.

### `vfs.mounted`

Added in: v26.9.0

- [`<boolean>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#boolean_type)

`true` while the VFS is mounted; `false` otherwise.

### `vfs.mountPoint`

Added in: v26.9.0

- [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) | [`<null>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#null_type)

The current mount point as an absolute string (the value returned by
the last [`vfs.mount()`](#vfsmount) call), or `null` when the VFS is not
mounted.

### `vfs.mountPointURL`

Added in: v26.9.0

- [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) | [`<null>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#null_type)

The current mount point as a `file:` URL string (the [`vfs.mountPoint`](#vfsmountpoint)
path converted with [`url.pathToFileURL()`](59-url.md#urlpathtofileurlpath-options)), or `null` when the VFS
is not mounted.

This is a convenience for addressing mounted files with URL-based
APIs such as dynamic `import()`:

```js
import vfs from 'node:vfs';

const myVfs = vfs.create();
myVfs.writeFileSync('/mod.mjs', 'export const value = 42;');
myVfs.mount();

const { value } = await import(`${myVfs.mountPointURL}/mod.mjs`);
console.log(value); // 42

myVfs.unmount();
```

### `vfs.provider`

Added in: v26.4.0

- [`<VirtualProvider>`](62-vfs.md#class-virtualprovider)

The provider backing this VFS instance.

### `vfs.readonly`

Added in: v26.4.0

- [`<boolean>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#boolean_type)

`true` when the underlying provider is read-only.

### APIs

`VirtualFileSystem` implements the following methods, with the same
signatures as their [`node:fs`](24-fs-part1.md) counterparts:

#### Synchronous API

- `existsSync(path)`
- `statSync(path[, options])`
- `lstatSync(path[, options])`
- `readFileSync(path[, options])`
- `writeFileSync(path, data[, options])`
- `appendFileSync(path, data[, options])`
- `readdirSync(path[, options])`
- `mkdirSync(path[, options])`
- `rmdirSync(path)`
- `unlinkSync(path)`
- `renameSync(oldPath, newPath)`
- `copyFileSync(src, dest[, mode])`
- `realpathSync(path[, options])`
- `readlinkSync(path[, options])`
- `symlinkSync(target, path[, type])`
- `accessSync(path[, mode])`
- `rmSync(path[, options])`
- `truncateSync(path[, len])`
- `ftruncateSync(fd[, len])`
- `linkSync(existingPath, newPath)`
- `chmodSync(path, mode)`
- `chownSync(path, uid, gid)`
- `lchownSync(path, uid, gid)`
- `utimesSync(path, atime, mtime)`
- `lutimesSync(path, atime, mtime)`
- `mkdtempSync(prefix)`
- `opendirSync(path[, options])`
- `openAsBlob(path[, options])`
- File-descriptor ops: `openSync`, `closeSync`, `readSync`, `writeSync`,
  `fstatSync`
- Streams: `createReadStream`, `createWriteStream`
- Watchers: `watch`, `watchFile`, `unwatchFile`

#### Callback API

`readFile`, `writeFile`, `stat`, `lstat`, `readdir`, `realpath`, `readlink`,
`access`, `open`, `close`, `read`, `write`, `rm`, `fstat`, `truncate`,
`ftruncate`, `link`, `mkdtemp`, `opendir`. Each takes a Node.js-style
callback `(err, ...result) => {}`.

#### Promise API

`vfs.promises` exposes the promise-based variants:

```js
const vfs = require('node:vfs');

async function example() {
  const myVfs = vfs.create();
  await myVfs.promises.writeFile('/file.txt', 'hello');
  const data = await myVfs.promises.readFile('/file.txt', 'utf8');
  return data;
}
example();
```

The promise namespace mirrors `fs.promises` and includes `readFile`,
`writeFile`, `appendFile`, `stat`, `lstat`, `readdir`, `mkdir`, `rmdir`,
`unlink`, `rename`, `copyFile`, `realpath`, `readlink`, `symlink`,
`access`, `rm`, `truncate`, `link`, `mkdtemp`, `chmod`, `chown`, `lchown`,
`utimes`, `lutimes`, `open`, `lchmod`, and `watch`.

## The reserved root directory

While any virtual file system is mounted, the directory that holds the mount
points can be read through [`node:fs`](24-fs-part1.md). [`vfs.vfsBase()`](#vfsvfsbase) returns its path,
`path.join(os.devNull, 'vfs')`. It contains a directory for every mounted file
system, named like the last segment of its [`vfs.mountPoint`](#vfsmountpoint).

```js
const vfs = require('node:vfs');
const fs = require('node:fs');
const path = require('node:path');

const root = vfs.vfsBase();
const assets = vfs.create();
assets.writeFileSync('/logo.svg', '<svg/>');
const mountPoint = assets.mount();

const name = path.basename(mountPoint);
fs.readdirSync(root); // [ name ]
fs.readdirSync(root, { recursive: true }); // [ name, `${name}/logo.svg` ]
```

The root directory itself is read-only. Creating, removing, or changing its
entries fails with `EROFS`, while the file systems its entries lead to can be
written to as usual. When nothing is mounted, the root directory does not exist.

## Module loader integration

Once a `VirtualFileSystem` is mounted, paths under the mount point
participate in module resolution and loading. The [CommonJS
resolution algorithm](33-modules.md#all-together) used by [`require()`](33-modules.md#requireid) and
[`require.resolve()`](33-modules.md#requireresolverequest-options) and the [ES modules resolution algorithm](34-esm.md#resolution-algorithm)
used by `import` and [`import.meta.resolve()`](34-esm.md#importmetaresolvespecifier) are unchanged;
instead, every file system operation those algorithms perform is
dispatched on the path being probed: paths under a mount point are
served by the owning VFS, and all other paths are served by the real
file system. Files served from the VFS therefore behave as
first-class modules.

Because mounted paths live in a reserved namespace that cannot exist
on disk, any given path is served either by exactly one VFS or by
the real file system, never both. There is no search order or
fallback between the two: if a path under a mount point does not
exist in the VFS, resolution fails with `ENOENT` without consulting
the disk, and a mounted layer never shadows a real directory.

For resolution purposes the mount point behaves as a file system
root: `package.json` scope lookups and [loading from `node_modules`
folders](33-modules.md#loading-from-node_modules-folders) stop at the mount point. For example, when
`${mountPoint}/foo/bar/main.cjs` calls `require('baz')`, the lookup
goes through:

- `${mountPoint}/foo/bar/node_modules/baz`
- `${mountPoint}/foo/node_modules/baz`
- `${mountPoint}/node_modules/baz`
- If `$NODE_PATH` is set, the folders listed in `$NODE_PATH`
- `$HOME/.node_modules/baz`
- `$HOME/.node_libraries/baz`
- `$PREFIX/lib/node/baz`

The last four entries are [the global folders](33-modules.md#loading-from-the-global-folders), which are legacy
CommonJS behavior and do not apply to `import`. Absolute specifiers
may cross the boundary in either direction: a module on the real
file system can `require()` a mounted path, and a virtual module can
`require()` a real one.

```js
const vfs = require('node:vfs');

const myVfs = vfs.create();
myVfs.mkdirSync('/lib');
myVfs.writeFileSync('/lib/greet.js', 'module.exports = () => "hi";');
myVfs.writeFileSync(
  '/lib/package.json', '{"main": "./greet.js"}');
const mountPoint = myVfs.mount();

const greet = require(`${mountPoint}/lib`);
console.log(greet()); // 'hi'

myVfs.unmount();
```

For ECMAScript modules, use `file:` URLs when passing mounted paths
to dynamic `import()`. [`vfs.mountPointURL`](#vfsmountpointurl) provides the mount
point in that form; this keeps VFS imports portable on Windows,
where mounted paths use Windows path syntax.

```js
import vfs from 'node:vfs';

const myVfs = vfs.create();
myVfs.writeFileSync('/mod.mjs', 'export const value = 42;');
myVfs.mount();

const { value } = await import(`${myVfs.mountPointURL}/mod.mjs`);
console.log(value); // 42

myVfs.unmount();
```

CommonJS modules loaded from a mounted VFS are identified by their VFS paths
that start with the mount point. This is reflected in, for example, `__filename` and
`__dirname` in the module, or the errors stack traces involving functions from
the VFS modules. ES modules in the VFS are similarly identified by the `file:` URL of
their VFS paths and this is reflected in e.g. `import.meta.url`.

Like modules loaded from the real file system, modules loaded from the VFS are
cached on the first load. When `require()` or `import()` is used to load an absolute
path or URL that falls under the mounted VFS multiple times, the module is only loaded
once and subsequent calls return the same instance.

Calling [`vfs.unmount()`](#vfsunmount) invalidates the modules that were loaded
from the mount point: a subsequent `require()` or `import` of a path
under a re-created mount re-reads the file from the newly mounted
VFS rather than returning a stale module. Modules loaded from other
VFS instances or from the real file system are unaffected.

Mounting and unmounting do not stop any module execution that is
already started, or invalidate any objects materialized from VFS
modules that are already executed. As with modules in the real file
system, the callers are responsible for avoiding removal or
invalidation of modules in the virtual file system while they are
being loaded.

Native addons (`.node` files) stored in a mounted VFS can be `require()`d as
well. The operating system's dynamic loader cannot open a virtual path, so the
addon's bytes are read from the VFS and loaded from a private, self-cleaning
temporary image instead. Addons on the real file system are unaffected and
load directly.

Shared libraries opened through [`ffi.dlopen()`](25-ffi.md#ffidlopenpath-definitions) (or
[`new ffi.DynamicLibrary()`](25-ffi.md#new-dynamiclibrarypath)) work the same way: a library path inside a
mounted VFS is detected, its bytes are read from the VFS, and the library is
loaded from a private, self-cleaning image while `library.path` keeps
reporting the virtual path. Libraries on the real file system load directly.

## Use with Single Executable Applications

When running as a [Single Executable Application](49-single-executable-applications.md) built with
`"useVfs": true` in the SEA configuration, the bundled assets are
automatically mounted as a read-only virtual file system and the injected
main script is executed from the root of the mount. No additional setup is
required. Since the mount point is reserved and chosen at runtime, bundled
code accesses the assets through `__dirname`-relative paths and relative
`require()` calls rather than through a fixed path:

```js
// In the SEA main script, __dirname is the root of the mounted assets.
const fs = require('node:fs');
const path = require('node:path');

const config = JSON.parse(
  fs.readFileSync(path.join(__dirname, 'config.json'), 'utf8'));
const template = fs.readFileSync(
  path.join(__dirname, 'templates/index.html'), 'utf8');
```

ESM entry points (`"mainFormat": "module"`) are supported: the main module
is loaded from inside the mount through the ESM loader, and
`import.meta.dirname` points at the mount root.

`"useVfs"` cannot be used together with `"useSnapshot"` or `"useCodeCache"`.
The SEA configuration parser will error if either combination is detected.

Instead of listing individual `"assets"`, the SEA configuration can point
`"vfsArchive"` at a prebuilt ZIP archive; the mount is then backed by a
[`ZipProvider`](#class-zipprovider) over the embedded archive, and each file is inflated when
it is read. See [Serving the assets from a ZIP archive](49-single-executable-applications.md#serving-the-assets-from-a-zip-archive-with-vfsarchive) for details.

See the [Single Executable Application](49-single-executable-applications.md) documentation for more information
on creating SEA builds with assets.

## Class: `VirtualProvider`

Added in: v26.4.0

The base class for all VFS providers. Subclasses implement the essential
primitives (such as `open`, `stat`, `readdir`, `mkdir`, `rmdir`, `unlink`,
`rename`, etc.) and inherit default implementations of the derived
methods (such as `readFile`, `writeFile`, `exists`, `copyFile`, `access`, etc.).

### Capability flags

- `provider.readonly` [`<boolean>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#boolean_type) **Default:** `false`.
- `provider.supportsSymlinks` [`<boolean>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#boolean_type) **Default:** `false`.
- `provider.supportsWatch` [`<boolean>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#boolean_type) **Default:** `false`.

### Creating custom providers

```js
const { VirtualProvider } = require('node:vfs');

class StaticProvider extends VirtualProvider {
  get readonly() { return true; }

  statSync(path) { /* ... */ }
  openSync(path, flags) { /* ... */ }
  readdirSync(path, options) { /* ... */ }
  // ...
}
```

The base class throws `ERR_METHOD_NOT_IMPLEMENTED` for any primitive
that has not been overridden, and rejects writes from a `readonly`
provider with `EROFS`.

## Class: `MemoryProvider`

Added in: v26.4.0

The default in-memory provider. Stores files, directories, and symbolic
links in a `Map`-backed tree, supports symlinks (`supportsSymlinks === true`), and supports watching (`supportsWatch === true`).

### `memoryProvider.setReadOnly()`

Added in: v26.4.0

Locks the provider into read-only mode. Subsequent writes through any
[`VirtualFileSystem`](#class-virtualfilesystem) using this provider throw `EROFS`. There is no
way to revert the provider to writable.

```js
const vfs = require('node:vfs');

const provider = new vfs.MemoryProvider();
const myVfs = vfs.create(provider);
myVfs.writeFileSync('/seed.txt', 'initial');

provider.setReadOnly();

myVfs.writeFileSync('/x.txt', 'fail'); // throws EROFS
```

## Class: `ComposableProvider`

Added in: v26.12.0

[`ComposableProvider`](#class-composableprovider) combines one or more providers in priority order. The first
provider is the writable layer; reads search from first to last. Directories
are merged, with entries in higher-priority layers shadowing entries with the
same name in lower layers. Writes to a lower file copy it to the first provider
before changing it. Removing a file hides lower copies without deleting them.
The first provider must be writable to change the composed file system.

### `new ComposableProvider(providers)`

Added in: v26.12.0

- `providers` [`<VirtualProvider>`](62-vfs.md#class-virtualprovider)[] Non-empty array of providers, ordered from
  highest to lowest priority.

```js
const vfs = require('node:vfs');

const memory = new vfs.MemoryProvider();
const disk = new vfs.RealFSProvider('/tmp/vfs-root');
const combined = vfs.create(new vfs.ComposableProvider([memory, disk]));
combined.writeFileSync('/config.json', '{"debug":true}');
// The file in memory shadows /tmp/vfs-root/config.json.
```

### `composableProvider.providers`

Added in: v26.12.0

- [`<VirtualProvider>`](62-vfs.md#class-virtualprovider)[]

A copy of the ordered provider list. Changes to this array do not affect the
composition. File handles opened before a write continue to refer to the layer
on which they were opened. Watching a path watches only its currently selected
provider, not changes across the entire composition. Symbolic links are
resolved by the provider containing them, not across providers. Traversal
through a symbolic-link directory is not supported by the composition. Renaming
a directory over a directory that exists only in a lower layer is not supported.
Layer selection and copy-up use synchronous provider operations, including
when invoked through the asynchronous VFS API.

## Class: `RealFSProvider`

Added in: v26.4.0

A provider that wraps a directory (i.e. one on the actual file system) and
exposes its contents through the VFS API. All VFS paths are resolved relative to
the root and verified to stay inside it; symbolic links resolving outside the
root are rejected. This path mapping is not a sandbox or access-control
mechanism.

### `new RealFSProvider(rootPath)`

Added in: v26.4.0

- `rootPath` [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type) The absolute file-system path to use as the root.
  Must be a non-empty string.

```js
const vfs = require('node:vfs');

const realVfs = vfs.create(new vfs.RealFSProvider('/tmp/vfs-root'));
realVfs.writeFileSync('/file.txt', 'hello'); // writes /tmp/vfs-root/file.txt
```

### `realFSProvider.rootPath`

Added in: v26.4.0

- [`<string>`](https://developer.mozilla.org/docs/Web/JavaScript/Data_structures#string_type)

The resolved absolute path used as the root.

## Class: `ZipProvider`

Added in: v26.9.0

A provider that exposes the entries of a ZIP archive - either a
[`zlib.ZipBuffer`](68-zlib.md#class-zlibzipbuffer) (in memory) or a [`zlib.ZipFile`](68-zlib.md#class-zlibzipfile) (on disk) - through
the VFS API. `provider.readonly` reflects the archive's own
[`zipFile.writable`](68-zlib.md#zipfilewritable) flag: a `ZipBuffer` is always writable, and a
`ZipFile` is writable only when opened with `{ writable: true }`.

Directories are recognized both explicitly (an entry whose name ends in `/`)
and implicitly (any entry name starting with `"<dir>/"`), and are listed by
`readdir()`, including with `{ recursive: true }`, either way. Because a ZIP
member cannot be edited or read in place - only fully written or fully
decompressed - a file opened for writing only commits its content (as a new
archive entry) when the handle is closed.

Every method has a synchronous counterpart (`openSync()`, `statSync()`,
`readdirSync()`, and so on), backed by the equally complete synchronous
surface [`zlib.ZipBuffer`](68-zlib.md#class-zlibzipbuffer)/[`zlib.ZipFile`](68-zlib.md#class-zlibzipfile) expose. As with those, the
synchronous methods here block the Node.js event loop and further JavaScript
execution until the operation - including any deflate/inflate pass -
completes.

```js
const vfs = require('node:vfs');
const zlib = require('node:zlib');
const { readFileSync } = require('node:fs');

async function main() {
  const zip = new zlib.ZipBuffer(readFileSync('archive.zip'));
  const archiveVfs = vfs.create(new vfs.ZipProvider(zip));

  console.log(await archiveVfs.promises.readdir('/'));
  await archiveVfs.promises.writeFile('/new.txt', 'hello');
}
main();
```

### `new ZipProvider(source)`

Added in: v26.9.0

- `source` [`<zlib.ZipBuffer>`](68-zlib.md#class-zlibzipbuffer) | [`<zlib.ZipFile>`](68-zlib.md#class-zlibzipfile) An already-open archive.

## Implementation details

### `Stats` objects

VFS `Stats` objects are real instances of [`fs.Stats`](24-fs-part6.md#class-fsstats) (or
[`fs.BigIntStats`](24-fs-part6.md#class-fsstats) when `{ bigint: true }` is requested). Their
fields use synthetic but stable values:

- `dev` is `4085` (the VFS device id).
- `ino` is monotonically increasing per process.
- `blksize` is `4096`.
- `blocks` is `Math.ceil(size / 512)`.
- Times default to the moment the entry was created/last modified.

[Contents](../README.md) | [Previous: V8](61-v8.md) | [Next: VM](63-vm.md)
