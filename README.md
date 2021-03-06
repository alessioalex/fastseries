# fastseries [![Build Status](https://travis-ci.org/mcollina/fastseries.svg?branch=master)](https://travis-ci.org/mcollina/fastseries)

Zero-overhead series function call for node.js. Also supports each
and map!

Benchmark for doing 3 calls `setImmediate` 1 million times:

* non-reusable `setImmediate`: 3887ms
* `async.series`: 5981ms
* `async.eachSeries`: 5087ms
* `async.mapSeries`: 5540ms
* `neoAsync.series`: 4338ms
* `neoAsync.eachSeries`: 4195ms
* `neoAsync.mapSeries`: 4237ms
* `fastseries` with results: 4096ms
* `fastseries` without results: 4063ms
* `fastseries` map: 4032ms
* `fastseries` each: 4168ms

These benchmarks where taken via `bench.js` on node 4.2.2, on a MacBook
Pro Retina 2014.

If you need zero-overhead parallel function call, check out
[fastparallel](http://npm.im/fastparallel).

[![js-standard-style](https://raw.githubusercontent.com/feross/standard/master/badge.png)](https://github.com/feross/standard)

## Example for series call

```js
var series = require('fastseries')({
  // this is a function that will be called
  // when a series completes
  released: completed,

  // if you want the results, then here you are
  results: true
})

series(
  {}, // what will be this in the functions
  [something, something, something], // functions to call
  42, // the first argument of the functions
  done // the function to be called when the series ends
)

function late (arg, cb) {
  console.log('finishing', arg)
  cb(null, 'myresult-' + arg)
}

function something (arg, cb) {
  setTimeout(late, 1000, arg, cb)
}

function done (err, results) {
  console.log('series completed, results:', results)
}

function completed () {
  console.log('series completed!')
}
```

## Example for each and map calls

```js
var series = require('fastseries')({
  // this is a function that will be called
  // when a series completes
  released: completed,

  // if you want the results, then here you are
  // passing false disables map
  results: true
})

series(
  {}, // what will be this in the functions
  something, // functions to call
  [1, 2, 3], // the first argument of the functions
  done // the function to be called when the series ends
)

function late (arg, cb) {
  console.log('finishing', arg)
  cb(null, 'myresult-' + arg)
}

function something (arg, cb) {
  setTimeout(late, 1000, arg, cb)
}

function done (err, results) {
  console.log('series completed, results:', results)
}

function completed () {
  console.log('series completed!')
}

```

## Caveats

The `done` function will be called only once, even if more than one error happen.

This library works by caching the latest used function, so that running a new series
does not cause **any memory allocations**.

## License

ISC
