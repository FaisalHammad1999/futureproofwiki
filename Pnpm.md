[https://www.npmjs.com/package/pnpm](https://www.npmjs.com/package/pnpm)

Pnpm is a newer more efficient version of npm. It stands for "performant npm".

Some of its benefits are:

- **Fast**. Up to 2x faster than the alternatives (see [benchmark](https://www.npmjs.com/package/pnpm#benchmark)).
- **Efficient.** Files inside `node_modules` are linked from a single content-addressable storage.(uses less disk space)
- **[Great for monorepos](https://pnpm.io/workspaces).**
- **Strict.** A package can access only dependencies that are specified in its `package.json`.
- **Deterministic.** Has a lockfile called `pnpm-lock.yaml`.
- **Works everywhere.** Supports Windows, Linux, and macOS.
- **Battle-tested.** Used in production by teams of [all sizes](https://pnpm.io/users) since 2016.
- **Offline mode.** pnpm saves all the downloaded package tarballs in a local registry mirror. It never makes requests when a package is available locally. With the --offline parameter, HTTP requests can be prohibited at all.

INSTALLATION 

On macOS, Linux, or Windows Subsystem for Linux:

```
curl -f https://get.pnpm.io/v6.16.js | node - add --global pnpm

```

On Windows (using PowerShell):

```
(Invoke-WebRequest 'https://get.pnpm.io/v6.16.js' -UseBasicParsing).Content | node - add --global pnpm

```

Using npm:

```
npx pnpm add -g pnpm
```

USAGE

To use it, you will only need to replace npm/yearn and instead type pnpm:

```
pnpm install <something>

```

And in cases where you would use npx (i.e. creating a react-app) you will use pnpm dlx:

```
pnpm dlx create-react-app <app name>
```

How can pnpm be faster? It all comes down to the structure of the dependency tree. Pnpm does not flatten the dependency tree. As a result, the algorithms used by pnpm can be a lot easier! That’s why it is possible that only 1 developer could keep pace with the dozens of contributors of Yarn. 

This is what an actual npm dependency tree looks like: 

```
node_modules
├─ foo
|  ├─ index.js
|  └─ package.json
└─ bar
├─ index.js
└─ package.json
```

And this is what pnpm does: 

```
node_modules
├─ foo -> .registry.npmjs.org/foo/1.0.0/node_modules/foo
└─ .registry.npmjs.org
├─ foo/1.0.0/node_modules
|  ├─ bar -> ../../bar/2.0.0/node_modules/bar
|  └─ foo
|     ├─ index.js
|     └─ package.json
└─ bar/2.0.0/node_modules
└─ bar
├─ index.js
└─ package.json
```

The package in the root of node_modules is just a symlink. This is fine as Node.js ignores symlinks and executes the realpath. So require('foo') will execute the file in node_modules/.registry.npmjs.org/foo/1.0.0/node_modules/foo/index.js.

Also none of the installed packages have their own node_modules. The dependencies of foo (which is just bar) are installed, but one level up in the directory structure. On top of that, both packages are inside a folder called node_modules.

A flat structure will occupy much more memory, will make our tree and our paths look more complex and therefore decrease the speed of our programmes.