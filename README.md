# Storybook submodule repo issue

This repo contains a submodule to replicate an issue with the `getMonorepoType` function used in storybook v6.5.0

## Setup

- Clone the repo and submodule that contains the storybook setup
- Change directory to the submodule that contains the storybook setup
- Install dependencies based on `package-lock.json`

```sh
git clone --recurse-submodules https://github.com/mrkiffie/storybook-parent-repo
cd storybook-parent-repo/storybook-submodule
npm ci
```

## Try build storybook

Trying to build storybook using `npm run build-storybook` results in the following failure:

```console
$ npm run build-storybook

> submodule@0.1.0 build-storybook
> build-storybook -s public

info @storybook/react v6.5.0
info
info => Cleaning outputDir: /path/to/storybook-parent-repo/storybook-submodule/storybook-static
(node:73526) DeprecationWarning: --static-dir CLI flag is deprecated, see:

https://github.com/storybookjs/storybook/blob/next/MIGRATION.md#deprecated---static-dir-cli-flag
(Use `node --trace-deprecation ...` to show where the warning was created)
info => Loading presets
info => Copying static files: ./public => ./
node:fs:585
  handleErrorFromBinding(ctx);
  ^

Error: /path/to/storybook-parent-repo/package.json: ENOENT: no such file or directory, open '/path/to/storybook-parent-repo/package.json'
    at Object.openSync (node:fs:585:3)
    at Object.readFileSync (node:fs:453:35)
    at Object.readFileSync (/path/to/storybook-parent-repo/storybook-submodule/node_modules/jsonfile/index.js:50:22)
    at getMonorepoType (/path/to/storybook-parent-repo/storybook-submodule/node_modules/@storybook/telemetry/dist/cjs/get-monorepo-type.js:46:38)
    at _callee2$ (/path/to/storybook-parent-repo/storybook-submodule/node_modules/@storybook/telemetry/dist/cjs/storybook-metadata.js:210:65)
    at tryCatch (/path/to/storybook-parent-repo/storybook-submodule/node_modules/regenerator-runtime/runtime.js:63:40)
    at Generator.invoke [as _invoke] (/path/to/storybook-parent-repo/storybook-submodule/node_modules/regenerator-runtime/runtime.js:294:22)
    at Generator.next (/path/to/storybook-parent-repo/storybook-submodule/node_modules/regenerator-runtime/runtime.js:119:21)
    at asyncGeneratorStep (/path/to/storybook-parent-repo/storybook-submodule/node_modules/@storybook/telemetry/dist/cjs/storybook-metadata.js:72:103)
    at _next (/path/to/storybook-parent-repo/storybook-submodule/node_modules/@storybook/telemetry/dist/cjs/storybook-metadata.js:74:194) {
  errno: -2,
  syscall: 'open',
  code: 'ENOENT',
  path: '/path/to/storybook-parent-repo/package.json'
}
```

This failure is because the `getMonorepoType` traverses out of the of the repo that contains the storybook configuration and in to the parent directory that doesn't have a `package.json` file.

The `.git` directory of a git submodule is located in the parent repo's `.git` directory. Specifically the contents that would usually be found in the `.git` repo is located in the `.git/modules/<SUBMODULE_NAME>/` directory.
