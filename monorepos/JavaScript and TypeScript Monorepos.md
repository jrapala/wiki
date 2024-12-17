**Course:** https://frontendmasters.com/courses/monorepos
**Source:** https://github.com/mike-north/js-ts-monorepos/
**My fork:** https://github.com/jrapala/js-ts-monorepos
## Introduction
---
### What is a Monorepo?

- One repo
- Many related JS/TS packages (many interdependencies) co-located in a git repo

"Many packages, related by a shared purpose and (usually) deep entanglement located in a single git repo."
### Why Use Monorepos?

- A great encapsulation tool
- One commit can modify many packages = single history and no broken builds (e.g. API changing)
- Test a family of packages together before a release
- Dramatically reduced maintenance overhead (one repository instead of many libraries)

### Risks

- More tooling needs
- Duplicate dependencies

### Yarn Workspaces

In `package.json`, add:

```json
"workspaces": [
	"packages/*"
]
```

We are telling yarn:
1. To use the workspaces feature
2. That any subfolders found within `packages` should be regarded as packages that are part of this workspace

This allows us to keep adding items to packages without needing to update `workspaces` (as opposed to listing directories individually)

### Naming Conventions

Adding a scope, e.g. the `@shlack` in `@shlack/types`, it identifies it as a part of a whole.

### Package Dependencies

Workspaces allow yarn to find other `package.json` files.

If a package has dependencies, and you run `yarn` on the root, the package dependencies will be added to the root level `yarn.lock`.


## Composite Project
---

TypeScript allows you to build projects in such a way that allows each part of a project to refer to build outputs of other pieces of the project.

You won't need to first build the lowest level part, then the next, and so on. It's automatic.

### Configuration Files

#### Central `tsconfig.settings.json`

You will want to have one central configuration file and very small configuration files in each package. The main configuration file, `tsconfig.settings.json` can live in `packages`. 

`packages/types tsconfig.json` Before:

```json
{
	"compilerOptions": {
		"module": "CommonJS",
		"types": [],
		"sourceMap": true,
		"target": "ES2018",
		"strict": true,
		"noUnusedLocals": true,
		"noUnusedParameters": true,
		"noImplicitReturns": true,
		"declaration": true,
		"outDir": "dist",
		"rootDir": "src"
	},
	"include": ["src"]
}
```

`packages/types tsconfig.json` After:

```json
{
	"extends": "../tsconfig.settings.json",
	"compilerOptions": {
		"composite": true,
		"outDir": "dist",
		"rootDir": "src"
	},
	"include": ["src"]
}
```

Note the `"composite": true`

`packages tsconfig.settings.json`:

```json
{
	"compilerOptions": {
		"module": "CommonJS",
		"types": [],
		"sourceMap": true,
		"target": "ES2018",
		"strict": true,
		"noUnusedLocals": true,
		"noUnusedParameters": true,
		"noImplicitReturns": true,
		"declaration": true,
	}
}
```

On the next `yarn build` of a package, you will notice a new `tsconfig.tsbuildinfo` file. This keeps track of whether a project needs to be rebuilt via checksums. i.e. "Is the current build in sync with the current source code?"

#### Central `tsconfig.json`

A `tsconfig.json` in `packages` will allow you to build the necessary tools for your various packages.

```json
{
	"files": [],
	"references": [
		{ "path": "utils" },
		{ "path": "types" },
	]
}
```

`references` points to other projects with `"composite": true`. 

`files` is an empty array because this tsconfig should not compile any files. If you do not include this empty array, you may get errors like `Output file '.../deferred.d.ts' has not been built from source file '.../deferred.ts'.`

Run `yarn tsc -b packages` in `/packages` and all of your packages will build.

### Clean Script

Add `rimraf` to the root and a `"clean": "rimraf dist *.tsbuildinfo",` script to each package.

## Tests
---

Use one testing strategy for all tests. (e.g. not jest for one thing, mocha for another, etc..)

jest can use .js, .ts, .jsx, .tsx, etc..

Run `yarn add -D @babel/preset-env @babel/preset-typescript` for each package.

### Babel Config

In `/packages` add a `.babelrc`:

```json
{
"presets": [
	["@babel/preset-env", {
		"targets": {
			"node": 10
		}
	}],
	"@babel/preset-typescript"
	]
}
```

`@babel/preset-env` allows you to set a target that corresponds to browserslist. We're dumbing our code down to Node 10.

`@babel/preset-typescript` removes all typescript symbols from your ts/tsx files.

Now, in each packages, add a `.babelrc`:

```json
{
	"extends": "../.babelrc"
}
```

## Linting
---

ESLInt is a workspace dependency. No editor will allow you to use multiple versions.
Editors like VSCode expect `.eslintrc` files at the root:

```json
{
	"env": {
		"es2021": true
	},
	"extends": [
		"eslint:recommended",
		"plugin:@typescript-eslint/recommended",
		"plugin:@typescript-eslint/recommended-requiring-type-checking"
	],
	"parser": "@typescript-eslint/parser",
	"parserOptions": {
		"ecmaVersion": 12
	},
	"plugins": ["@typescript-eslint"],
	"rules": {
		"prefer-const": "error",
		"@typescript-eslint/no-unsafe-member-access": "off",
		"@typescript-eslint/no-unsafe-call": "off",
		"@typescript-eslint/no-unsafe-assignment": "off"
	}
}
```

Run `yarn add -WD eslint @typescript-eslint/eslint-plugin @typescript-eslint/parser`

For each package, add an `.eslintrc`:

```json
{
	"extends": "../../.eslintrc",
	"parserOptions": {
		"project": "tsconfig.json"
	}
}
```

Add a script to each package as well: `"lint": "eslint src --ext js,ts",`

## Lerna
---

Lerna is a library that provides tooling to manage multi-repository structures inside a single repository.

Lerna is workspace level dependency. `yarn add -DW lerna`

Add a `lerna.json` to the root:

```json
{
	"packages": ["packages/*"],
	"npmClient": "yarn",
	"version": "0.0.1",
	"nohoist": ["parcel-bundler"]
}
```

`packages` is similar to `workspaces` in `package.json`. You need to specify it since `npm`, if we were using it, does not have the concept of workspaces. You may also have a need for lerna to manage things outside of your workspace.

The current `version` strategy is a lock-step version. Every time these is a change, all of your packages are versioned. Setting this to `independent` would mean that only the packages that have changed would cut a new release. This makes sense if everything is talking to a public API. If you have communication between your packages, you should do lockstep versioning. You are signaling "these are all meant to work together".

### Lerna Scripts

`lerna link`: We need some packages discoverable in the `node_modules` of other folder. This creates the symlinks to make that work.

`lerna boostrap`: Operates the same as `lerna link` but also downloads external dependencies

`lerna run`: Like a for loop for a yarn script. e.g. `yarn lerna build` runs `yarn build` in each package while analyzing who depends on who. It will build the lowest level packages first.

`learna exec`: Similar to `lerna run` but for shell commands 

## Scripty
---
Whenever possible, build the same way, test the same way, lint the same way, etc..

Scripty is a tool that lets you take scripts out of individual package.json files

1. Run `yarn add -WD scripty` at the workspace level
2. Add a scripts folder
3. In your root `package.json`, add a new script: `"greet": "scripty"`
4. In your script folder, add a `greet` file:

```js
#!/usr/bin/env node

console.log('Hello, world! 🤣');
```
5. Run `chmod -x greet` to make it an executable (Adds an `-x` to the permissions which means anyone can execute it.)

The top line (the **shebang interpreter directive**) has the file run in node, so it accepts JavaScript.

It all just works because the name of the package.json script matches a file name in a scripts folder. This beats scripts written in package.json because you can look for errors and add linting (e.g. [ShellCheck](https://www.shellcheck.net/) for shell scripts)

### Separating Scripts

You can signify which scripts are for your workspace and which are for packages:

Root `package.json`:

```json
"config": {
	"scripty": {
		"path": "./scripts/workspace"
	}
}
```

Each package `package.json`:

```json
"config": {
	"scripty": {
		"path": "../../scripts/packages"
	}
}
```

`scripts`:
```
- scripts
	- packages
		- build.sh
		- lint.sh
		- test.sh
	- workspace
		- build.sh
		- clean.sh
		- lint.sh
		- test.sh
```

`scripts/packages/build.sh`

```sh
#!/usr/bin/env bash
echo "┏━━━ 📦 Building $(pwd) ━━━━━━━━━━━━━━━━━━━"
yarn tsc -b
```

`scripts/packages/lint.sh`

```sh
#!/usr/bin/env bash
echo "┏━━━ 🕵️‍♀️ LINT: eslint src --ext ts,js,tsx,jsx ━━━━━━━"
yarn eslint src --ext ts,js,tsx,jsx
```

`scripts/packages/test.sh`

```sh
#!/usr/bin/env bash
echo "┏━━━ 🎯 TEST: $(pwd) ━━━━━━━━━━━━━━━━━━━"
yarn jest
```

`scripts/workspace/build.sh`

```sh
#!/usr/bin/env bash
echo "┏━━━ 📦 Building Workspace ━━━━━━━━━━━━━━━━━━━"
yarn tsc -b packages
```

**Note:** No lerna here -- TypeScript does this very well with our predefined references

`scripts/workspace/clean.sh`

```sh
#!/usr/bin/env bash
echo "┏━━━ 🧹 CLEAN ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━"
yarn lerna run clean --concurrency 4
```

`scripts/workspace/lint.sh`

```sh
#!/usr/bin/env bash
echo "┏━━━ 🕵️‍♀️ LINT: eslint src --ext ts,js,tsx,jsx ━━━━━━━"
yarn lerna run lint --stream --concurrency 1
```

`scripts/workspace/test.sh`

```sh
#!/usr/bin/env bash
echo "┏━━━ 🎯 TEST: $(pwd) ━━━━━━━━━━━━━━━━━━━"
yarn lerna run test --stream --concurrency 1
```

**Note:** Concurrency is set to 1 for lint and test scripts because you don't want combined output.

Idea: You can add build scripts to a separate package that any package can consume.
## Changelogs

Let's align with [Conventional Changelog](https://github.com/conventional-changelog/conventional-changelog).

Create a `commitlint.config.js` file in the root:

```js
module.exports = {
	extends: [
		"@commitlint/config-conventional",
		"@commitlint/config-lerna-scopes"
	]
};```

`yarn add -WD @commitlint/cli @commitlint/config-conventional @commitlint/config-lerna-scopes commitlint husky lerna-changelog`

The `@commitlint/config-lerna-scopes` package allows you to specify what packages are affected by a commit.

To your root `package.json`:
```json
"husky": {
	"hooks": {
		"commit-msg": "commitlint -E HUSKY_GIT_PARAMS"
	}
}
```

You can test how this work with something like: `echo "build(api): change something in api's build" | yarn commitlint`. This command would error since we do not have a "api" scope (informed by `config-lerna-scopes`).

## Publishing and Versioning

`lerna version` will prompt you for a release type:

```bash
➜  js-ts-monorepos git:(master) lerna version
lerna notice cli v8.0.1
lerna info current version 0.0.1
lerna info Assuming all packages changed
? Select a new version (currently 0.0.1) (Use arrow keys)
❯ Patch (0.0.2)
  Minor (0.1.0)
  Major (1.0.0)
  Prepatch (0.0.2-alpha.0)
  Preminor (0.1.0-alpha.0)
  Premajor (1.0.0-alpha.0)
  Custom Prerelease
  Custom Version
```

When using conventional commits, you can automate versioning: 

```bash
➜  js-ts-monorepos git:(master) lerna version --conventional-commits
lerna notice cli v8.0.1
lerna info current version 0.0.1
lerna info Assuming all packages changed
lerna info getChangelogConfig Successfully resolved preset "conventional-changelog-angular"

Changes:
 - @shlack/types: 0.0.1 => 0.1.0
 - @shlack/utils: 0.0.1 => 0.1.0
```

After you accept a version, tags are created and CHANGELOGs are created for each package. If no changes occurred in a package, you'll see a "version bump only" message in that package's CHANGELOG.

If you use `independent` to versioning, `lerna changed` will show which packages will be bumped.

**2023 Note:** This doesn't seem to work with the latest version of Lerna

### Mocking Publishing with Verdaccio

Verdaccio is a local npm proxy.

1. `$ yarn global add verdaccio`
2. `$ verdaccio`
3. Go to `http://localhost:4873/`
4. Create an `.npmrc` file with `registry=http://localhost:4873/`. If Verdaccio doesn't see the package cached locally, it will go to NPM

Verdaccio is also useful for create caches of npm packages when you are somewhere without an internet connection.

To clear your Verdaccio cache: `$ rm rf ~/.local/share/verdaccio`

## Internal Dependencies

In a monorepo, it's easy to forget to install a package since everything references the root node_modules.
However it is very important, especially when publishing, that you have all packages that you are importing into files in your app listed as dependencies.

Transitive Dependencies are dependencies that are not imported directly into a project, but are instead imported by a direct dependency or another transitive dependency.

To add dependencies to a package `node_modules`:
1. `$ lerna add @shlack/types --scope '@shlack/{ui,data}'` (note quotes)
2. `$ lerna add @shlack/utils --scope '@shlack/{ui,data}'` (note quotes)
3. `$ lerna add @shlack/data --scope '@shlack/ui'` (note quotes)
4. `$ lerna link`

## API Report

If you document your code well, you can get API extraction for free.

1. `$ yarn add -WD @microsoft/api-extractor` to the root
2. `$ yarn api-extractor init`
3. Move `api-extractor.js`  to `packages` and rename to `api-extractor-base.js` (this is a common file that other packages extend from)

Changes made to `api-extractor-base`:
- Set `docModel` to true, which creates a JSON file that can be used for documentation
- Set `apiJsonFilePath` to `<projectFolder>/../../temp/<unscopedPackageName>.api.json` to create files like `types.api.json` in a new `temp` folder.
- Set `dtsRollup` to true to generate the `.d.ts` rollup file
	- These can be created to only include public types, all types, beta versions, etc..
- Set `untrimmedFilePath` to `<projectFolder>/dist/<unscopedPackageName>-private.d.ts` 
- Set `betaTrimmedFilePath` to `<projectFolder>/dist/<unscopedPackageName>-beta.d.ts` 
- Set `publicTrimmedFilePath` to `<projectFolder>/dist/<unscopedPackageName>.d.ts` 
- Each `package.json` will point to rollups instead of raw type information

Add a new `api-extractor.json` file to root of each package:

```json
{
	"$schema": "https://developer.microsoft.com/json-schemas/api-extractor/v7/api-extractor.schema.json",
	"mainEntryPointFilePath": "<projectFolder>/dist/index.d.ts",
	"extends": "../api-extractor-base.json"
}
```

Add a new `api-report` script to each package:

```sh
#!/usr/bin/env bash
echo "┏━━━ 🧩 API REPORT: $(pwd) ━━━━━━━━━━━━━━━━━━━━━"
yarn api-extractor run --local
```

You must also create an `etc` folder in each package: `$ lerna exec 'mkdir etc'`

**Tip:** [node-jq](https://www.npmjs.com/package/node-jq) is helpful for altering many package.json files.

## API Docs

The API extractor outputs can translate to documentation:

`$ yarn add -WD  @microsoft/api-documenter

Add a script to the workspace folder:

```sh
#!/usr/bin/env bash
echo "┏━━━ 📚 API DOCS: Extracting API surface ━━━━━━━━━━━━━━"
yarn clean
yarn tsc -b packages
yarn lerna run api-report;

echo "┏━━━ 📝 API DOCS: Generating Markdown Docs ━━━━━━━━━━━━"
GH_PAGES_CFG_EXISTS=$(test -f docs/_config.yml)

if [ $GH_PAGES_CFG_EXISTS ]
then
echo "GitHub pages config file DETECTED"
cp docs/_config.yml .
fi
  
yarn api-documenter markdown -i temp -o docs
  
if [ $GH_PAGES_CFG_EXISTS ]
then
cp _config.yml docs/_config.yml
fi
```

This ensures that your API docs are up to date first by running fresh builds.