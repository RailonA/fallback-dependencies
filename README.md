# fallback-dependencies

[![Build Status](https://github.com/rooseveltframework/fallback-dependencies/workflows/CI/badge.svg
)](https://github.com/rooseveltframework/fallback-dependencies/actions?query=workflow%3ACI) [![codecov](https://codecov.io/gh/rooseveltframework/fallback-dependencies/branch/master/graph/badge.svg)](https://codecov.io/gh/rooseveltframework/fallback-dependencies) [![npm](https://img.shields.io/npm/v/fallback-dependencies.svg)](https://www.npmjs.com/package/fallback-dependencies)

A Node.js module that allows you to add git repo dependencies to your Node.js app from a cascading list of fallback locations. This module was built and is maintained by the [Roosevelt web framework](https://github.com/rooseveltframework/roosevelt) [team](https://github.com/orgs/rooseveltframework/people), but it can be used independently of Roosevelt as well.

## Usage

First declare `fallback-dependencies` in `devDependencies` in your app.

Then add a `fallbackDependencies` entry to your `package.json` alongside your `dependencies`, `devDependencies`, etc.

Here's an example:

```js
"fallbackDependencies": {
  "dir": "lib",
  "repos": {
    "some-private-dependency": [
      "https://some.private.git.repo.somewhere",
      "https://some.private.git.repo.somewhere.else",
    ],
    "some-other-private-dependency": [
      "https://some.other.private.git.repo.somewhere",
      "https://some.other.private.git.repo.somewhere.else",
    ]
  },
  "reposFile": "fallback-dependencies.json"
}
```

Lastly, add a `postinstall` script to your npm scripts to execute the `fallback-dependencies` script after you install other dependencies:

```js
  "scripts": {
    "postinstall": "node node_modules/fallback-dependencies/fallback-dependencies.js"
  },
```

To clone a specific git tag, add `-b tag_name` to the URL, e.g. `"https://some.private.git.repo.somewhere -b 1.0.5"`.

To skip installing dependencies for a specific fallback-dependency, add ` -skip-deps` to the end of the URL string, e.g. `"https://some.private.git.repo.somewhere -b 1.0.5 -skip-deps"`

To prevent a fallback-dependency from being installed in a situation where the repo is not a direct dependency of the root project, append the `:directOnly` flag to the end of the dependency name, e.g. `"some-private-dependency:directOnly": [ ... ] `.

### API

- `dir` *[String]*: What directory to deposit fallback dependencies into.
  - Default: `fallback_dependencies`.
- `repos` *[Object]* of *[Arrays]* of *[Strings]*: A list of dependencies similar to the `dependencies` field in package.json, but instead of supplying a string for where to fetch it, you supply an array of strings of possible locations to fetch it from. This script will attempt to fetch it from the first location, then if that fails will fallback to the second possible place to get it from, and so on until it runs out of places to try.
  - Default: `{}`.
- `reposFile` *[String]*: Relative path to a JSON file that contains a list of repos formatted the same as the `repos` entry. If both `repos` and `reposFile` are supplied, the two lists will be merged.
  - Default: `{}`.
  - Example:
    ```js
    // fallback-dependencies.json
    {
      "some-private-dependency": [
        "https://some.private.git.repo.somewhere",
        "https://some.private.git.repo.somewhere.else",
      ],
      "some-other-private-dependency": [
        "https://some.other.private.git.repo.somewhere",
        "https://some.other.private.git.repo.somewhere.else",
      ]
    }
    ```

All params are optional, but the module won't do anything unless you supply at least `repos` or `reposFile`.
