# fast-safe-stringify

Safely and quickly serialize JavaScript objects.

Detects circular dependencies instead of throwing (as per usual `JSON.stringify`
usage).

## Usage

The same as [JSON.stringify][].

`stringify(value[, replacer[, space]])`

```js
var safeStringify = require('fast-safe-stringify')
var o = { a: 1 }
o.o = o

console.log(safeStringify(o))
// '{"a":1,"o":"[Circular]"}'
console.log(JSON.stringify(o))
// TypeError: Converting circular structure to JSON
```

## Benchmarks

Although not JSON, the Node.js `util.inspect` method can be used for similar
purposes (e.g. logging) and also handles circular references.

Here we compare `fast-safe-stringify` with some alternatives:
(Lenovo T450s with a i7-5600U CPU using Node.js 8.9.4)

```md
inspectBench*10000: 57.636ms
jsonStringifySafeBench*10000: 58.737ms
fastSafeStringifyBench*10000: 25.555ms

inspectCircBench*10000: 137.803ms
jsonStringifyCircSafeBench*10000: 110.460ms
fastSafeStringifyCircBench*10000: 38.039ms

inspectDeepBench*10000: 600.103ms
jsonStringifySafeDeepBench*10000: 1345.514ms
fastSafeStringifyDeepBench*10000: 369.198ms

inspectDeepCircBench*10000: 609.102ms
jsonStringifySafeDeepCircBench*10000: 1361.704ms
fastSafeStringifyDeepCircBench*10000: 383.083ms

```

## JSON.stringify options

[JSON.stringify][]'s `replacer` and `space` options are supported and work the
same as JSON.stringify with one exception: in case a circular structure is
detected the replacer will receive the string `[Circular]` as argument instead
of the circular object itself.

## Protip

Whether `fast-safe-stringify` or alternatives are used: if the use case
consists of deeply nested objects without circular references the following
pattern will give best results.
Shallow or one level nested objects on the other hand will slow down with it.
It is entirely dependant on the use case.

```js
const stringify = require('fast-safe-stringify')

function tryJSONStringify (obj) {
  try { return JSON.stringify(obj) } catch (_) {}
}

const serializedString = tryJSONStringify(deep) || stringify(deep)
```

## Acknowledgements

Sponsored by [nearForm](http://nearform.com)

## License

MIT

[JSON.stringify]: https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/JSON/stringify
