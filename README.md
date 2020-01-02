# Hashy

[![Node compatibility](https://badgen.net/npm/node/hashy)](https://npmjs.org/package/hashy) [![PackagePhobia](https://badgen.net/packagephobia/install/hashy)](https://packagephobia.now.sh/result?p=hashy)

[![Package Version](https://badgen.net/npm/v/hashy)](https://npmjs.org/package/hashy) [![Build Status](https://travis-ci.org/JsCommunity/hashy.png?branch=master)](https://travis-ci.org/JsCommunity/hashy) [![Latest Commit](https://badgen.net/github/last-commit/JsCommunity/hashy)](https://github.com/JsCommunity/hashy/commits/master)

> Hash passwords the right way (Argon2 & bcrypt support)

Hashy is small [Node.js](http://nodejs.org/) library which aims to do
passwords hashing _[the correct
way](https://wiki.php.net/rfc/password_hash)_.

It has been heavily inspired by the new [PHP password hashing
API](http://www.php.net/manual/en/book.password.php) but, following
the Node.js philosophy, hashing is done asynchronously.

Furthermore, to make the interfaces as easy to use as possible, async
functions can either be used with callbacks or they return
[promises](https://en.wikipedia.org/wiki/Promise_%28programming%29)
which will make them super easy to work with [async functions](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Statements/async_function)!

Supported algorithms:

- [Argon2](https://en.wikipedia.org/wiki/Argon2)
- [bcrypt](https://en.wikipedia.org/wiki/Bcrypt)

## Why a new library?

The other ones I found were too complicated and/or were missing
important features.

The main missing feature is the `needRehash()` function: cryptography
is a fast-moving science and algorithms can quickly become obsolete or
their parameters needs to be adjusted to compensate the performance
increase of recent computers (e.g. [bcrypt cost
factor](http://phpmaster.com/why-you-should-use-bcrypt-to-hash-stored-passwords/)).

This is exactly what this function is for: checking whether a hash
uses the correct algorithm (and options) to see if we need to compute
a new hash for this password.

## Install

Installation of the npm package:

```
> npm install --save hashy
```

Hashy requires promises support, for Node versions prior to 0.12 [see
this page](https://github.com/JsCommunity/promise-toolbox#usage) to
enable them.

## How to use it?

First, you may take a look at examples: using [callbacks](https://github.com/JsCommunity/hashy/blob/master/examples/callbacks.js), [promises](https://github.com/JsCommunity/hashy/blob/master/examples/promises.js) or [async functions](https://github.com/JsCommunity/hashy/blob/master/examples/async.js) (requires Node >= 7.6).

### Creating a hash

```js
hashy.hash(password, function(error, hash) {
  if (error) {
    return console.log(error);
  }

  console.log("generated hash: ", hash);
});
```

`hash()` handles additionaly two parameters which may be passed before the callback:

1. `algo`: which algorithm to use, it defaults to `'bcrypt'`;
2. `options`: additional options for the current algorithm, for bcrypt
   it defaults to `{cost: 10}.`.

### Checking a password against a hash

```js
hashy.verify(password, hash, function(error, success) {
  if (error) {
    return console.error(err);
  }

  if (success) {
    console.log("you are now authenticated!");
  } else {
    console.warn("invalid password!");
  }
});
```

### Getting information about a hash

```js
const info = hashy.getInfo(hash);
```

### Checking whether a hash is up to date

As I said [earlier](#why-a-new-library), we must be able to check
whether the hash is up to date, i.e. if it has been generated by the
last algorithm available with the last set of options.

```js
if (hashy.needsRehash(hash)) {
  // Rehash.
}
```

It handles the optional `algo` and `options` parameters like
[`hash()`](#creating-a-hash).

### Changing default options.

The default options for a given algorithm is available at `hashy.options[&gt;algo&lt;]`.

```js
// Sets the default cost for bcrypt to 12.
hashy.options.bcrypt.cost = 12;
```

## Using promises

Same interface as above but without the callbacks!

```javascript
// Hashing.
hashy.hash(password).then(function (hash) {
  console.log('generated hash:' hash)
})

// Checking.
hashy.verify(password, hash).then(function (success) {
  if (success) {
    console.log('you are now authenticated!')
  } else {
    console.warn('invalid password!')
  }
})

```

As you can see, you don't even have to handle errors if you don't want
to!

## Using async functions

**Note:** only available since Node.js 7.6.

Same interface as promises but much more similar to a synchronous
code!

```javascript
// Hashing.
(async function() {
  const hash = await hashy.hash(password);
  console.log("generated hash:", hash);
})()(
  // Checking.
  async function() {
    if (await hashy.verify(password, hash)) {
      console.log("you are now authenticated!");
    } else {
      console.warn("invalid password!");
    }
  }
)();
```

## Contributing

Contributions are _very_ welcome, either on the documentation or on
the code.

You may:

- report any [issue](https://github.com/JsCommunity/hashy/issues)
  you've encountered;
- fork and create a pull request.

## License

Hashy is released under the [MIT
license](https://en.wikipedia.org/wiki/MIT_License).
