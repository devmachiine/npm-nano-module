<p align="center">
  <img src="https://github.com/devmachiine/npm-nano-module/raw/master/play/nanja600.jpg"/>
</p>

## Dynamic package manager for javascript applications

Sometimes you only need a small part of logic that a module provides. Nano modules are just that - modules that only do just about [one thing](https://en.wikipedia.org/wiki/Unix_philosophy#Do_One_Thing_and_Do_It_Well). Like _really_ small modules, a single function _(aka method)_ to be exact.

 You can think of each function as a 'mini-package', and `nano-module` as an additional vitamin supplement to familiar package managers like npm / yarn.

## Example:

Say that you need a function to add two numbers together, but you are not interested in the 20 other things a math package might bring with it. Nor the other 12 packages it will bring to the party.

You find the code you need in `https://example.com/url/math/addition-1992.js`

```javascript
// a single arrow function
(x, y) => x + y
```

Your app could import just that spot of logic:

```javascript
// require nano-module
const ff = require('nano-module')

// url (or local file path) to our dependency
const path = 'https://example.com/url/math/addition-1992.js'

// get the async add function
const add = ff(path)

// fetch the function and run it locally
const result = await add(2, 5)

console.log(`2 plus 5 equals ${result)}`))
```

Outputs:
> `2 plus 5 equals 7`

## A little more

Nano modules can also be imported locally, so you don't have to upload them somewhere before using them:

```javascript
// import same as above..
const rightpad = ff('./utils/string/not-leftpad.js')

// more details on how nano modules can prevent the left-pad like disaster to follow ;)
```

Nano modules can also use `nano-module`, aliased as `ff` _(fetch function)_ themselves to retrieve other functions, and those nested functions can retrieve other functions and so forth, to accomplish more interesting tasks.

Nano modules already find themselves in an `async` context, so in addition to `ff` you have `await` at your disposal:

```javascript
(test, assert) => {

    const test_remote_call_remote = ff('https://gist.githubusercontent.com/devmachiine/44c86b61bd8b98226d7ddbe97b4196ea/raw/66107ef784c8a72bb99fdac2acd23bdd4a8280f9/remote-remote.js')

    const test_add = await test_remote_call_remote(test, assert)

    return test('remote load remote',
        () => {
            assert(test_add.description, 'remote functions can load other remote functions')
            assert(true, !test_add.error)
        })
}
```

Here the funcion wasn't given just any old `x` and `y` as in the first example, but a test framework. These inputs don't go outside of your application over the wire, everything is ran locally.

Nano modules live in the app directory:

```
project-name
 - lib
 - docs
 - nano_modules
   - domain
   - domain
    - subpath
      - nano a.js  <-- in here. similar to the url on the web.
      - nano b.js
 - node_modules
   - module a..
   - module b..
index.js

```

When a nano is requested, `nano-module` fetches it from the web (only once), then caches, and returns it.

The next time your app runs, it'll load it from the `nano_modules` folder on the 1st call, and already in memory for subsequent calls.


## Stable dependencies

Anyone remember the [left\-pad incident](https://www.theregister.co.uk/2016/03/23/npm_left_pad_chaos/) ?

There is a **big** ~~hash~~ hint in the last code snippet how the scenario can be prevented ;)

_(with a minor drawback, for now.. work in progress..)_

## Background

Dependencies often entail breaking changes. Often, especially for small pieces of code, developers prefer to copy or re-invent the wheel to avoid this [dependency problem](https://www.youtube.com/watch?v=oyLBGkS5ICk) all togehter.

How many megabytes of dependencies does a project have ~ and do you really need to download *all* the code, even if you only run a subset ?

[Semantic versioning](https://semver.org/spec/v1.0.0-beta.html) makes dependency management a little easier with this convention:
<br/> \_.\_.x will probably not be a breaking change
<br/> \_.x.\_ also won't be a breaking change, pinky promise ;)
<br/> x.\_.\_ okay changes will possibly break your code, oops!

However the version number only reflects the interactions with a module/package. Sometimes. The behaviour changes from version to version, and a 0.0.x bump could still be a breaking change anyway.

What we want is to re-use shared code, without pushing breaking changes to unknown consumers, and signal updates that people can opt into. *(ex. security, bugfixes and performance improvements)* This project is a step in that direction.

## Next steps

Function-level dependency resolution, especially dynamically, provides it's own set of challenges and concerns to use over a traditional package manager (ex. npm, yarn).

I suspect the dynamic resolution bit will have to be optional *(mainly for security & reliability reasons)*, and to rather/also create a build tool.

Just as any central repository (Github <3, brew.sh, etc..) can evaporate, that problem is exemplified by having thousands of url based dependencies. Instead of creating yet another package manager central, it would be better to have a p2p-mesh network for sharing code.

Thoughts arount *that* project:
- Function identifiers could be a hash of the function, signed by the publisher on a shared ledger.
- A mechanism to enable the mesh to additionaly share optimized javascript, python, and eventually compiled language components.
- Who knows, maybe pure functions could be [memoized](https://en.wikipedia.org/wiki/Memoization) globally..

<!--
## Detail

More: Build tool (bonsai?)

There is no benefit in re-testing and re-building the same things over and over if it's execution path hasn't changed. It slows down the dev/test feedback loop.

### ffetch

Takes single function, that returns source code for a given path or url, and returns a Promise(function)

### ffetch(argument) ~ Directory name, or cache-stack function

If the first argument isn't a directory name, it expects a dependency-resolver-function:

A function that retrieves and builds a function from the web, and caches it both on disk _(eg `./nano_modules` folder)_ and in memory for subsequent requests.

Each function saved on disk is saved in it's own file, exactly like the remote dependency drawn from the web. If multiple remote functions were saved in the same file(s) instead, they would cause many changes in those files over the life of a project (git history), and make remote dependency resolution for those functions substantially more difficult to track and manage effectively.
-->
