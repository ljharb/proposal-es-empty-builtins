# Proposal for ECMAScript empty builtin values

## Status

This proposal is officially in stage 0 (strawman) of [the TC39 process](https://docs.google.com/document/d/1QbEE0BsO4lvl7NFTn5WXWeiEIBfaVUF7Dk0hpPpPDzU).

## Motivation
Often, developers use a "no-op" function (`function () {}`) as an initial or placeholder value for a callback, or for a function that a subclass must implement, etc. Some developers have used `Function.prototype` for this purpose, which is a fast and simple builtin no-op function (one example [JSPerf](http://jsperf.com/noop-method-approaches/2)).
Similarly, developers may use `Array.prototype` as a placeholder for an empty Array (that they don't intend to mutate) - or, simply will use `[]` as an initial/placeholder value - or `RegExp.prototype`.

In ES6, TC39 has attempted to change builtin prototype objects to normal objects, as opposed to the pre-ES6 behavior where `X.prototype` is also an `X` - however, for `Function.prototype` and `Array.prototype`, at least, this turned out to be [problematic](https://esdiscuss.org/topic/array-prototype-change-was-tostringtag-spoofing-for-null-and-undefined).

This has been discussed on [es-discuss](https://esdiscuss.org/topic/empty-idea) [multiple](https://esdiscuss.org/topic/my-ecmascript-7-wishlist) times.

## Proposed Solution
We propose creating a nonconfigurable, non-writable, non-enumerable `empty` property on the following builtins:
 - `Function.empty`, polyfilled as `(function () { var f = function empty() {}; f.prototype = Object.empty; Object.defineProperty(f, 'length', { configurable: false }); Object.defineProperty(f, 'name', { configurable: false }); return Object.freeze(f); }())`: a function that is empty of operations, ie, a "no-op".
 - `String.empty` as `''` - an empty string
 - `RegExp.empty` as `Object.freeze(/(?:)/)` - a regular expression empty of matches
 - `Object.empty` as `Object.freeze(Object.create(null))` - an empty object
 - `Promise.empty` as `Object.freeze(new Promise(Function.empty))` - an empty promise :-)
 - `Date.empty` as `Object.freeze(new Date(0))` - a date empty of seconds since the epoch
 - `Map.empty` as `Object.freeze(new Map())` - a map empty of entries (*note*: this does not make an immutable Map: revisit)
 - `Set.empty` as `Object.freeze(new Set())` - a set empty of values (*note*: this does not make an immutable Set: revisit)
 - `WeakMap.empty` as `Object.freeze(new WeakMap())` - a weak map empty of entries (*note*: this does not make an immutable WeakMap: revisit)
 - `WeakSet.empty` as `Object.freeze(new WeakSet())` - a weak set empty of values (*note*: this does not make an immutable WeakSet: revisit)
 - `Array.empty` as `Object.freeze([])`, an array that is empty of items.
 - `Int8Array.empty` as `Object.freeze(new Int8Array(0))`, an Int8Array that is empty of items
 - `Int16Array.empty` as `Object.freeze(new Int16Array(0))`, an Int16Array that is empty of items
 - `Int32Array.empty` as `Object.freeze(new Int32Array(0))`, an Int32Array that is empty of items
 - `Uint8Array.empty` as `Object.freeze(new Uint8Array(0))`, a Uint8Array that is empty of items
 - `Uint8ClampedArray.empty` as `Object.freeze(new Uint8ClampedArray(0))`, a Uint8ClampedArray that is empty of items
 - `Uint16Array.empty` as `Object.freeze(new Uint16Array(0))`, a Uint16Array that is empty of items
 - `Uint32Array.empty` as `Object.freeze(new Uint32Array(0))`, a Uint32Array that is empty of items
 - `Float32Array.empty` as `Object.freeze(new Float32Array(0))`, a Float32Array that is empty of items
 - `Float64Array.empty` as `Object.freeze(new Float64Array(0))`, a Float64Array that is empty of items

Builtins intentionally missing: `Symbol`, `Number`, `Boolean`, `JSON`, `Math`, `Error`, error subtypes, `Proxy`, `Reflect`
Investigate: `ArrayBuffer`, `DataView`
