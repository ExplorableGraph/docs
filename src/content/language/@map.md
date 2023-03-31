---
title: "@map"
---

This is a collection of functions for transforming graphs.

<a name="keys"></a>

## @map/keys(graph, [keyFn])

Returns a new graph that replaces the original keys with mapped keys, obtained by invoking the `keyFn` function for each value in the original graph. If the `keyFn` is omitted, the value itself becomes its own key.

```console assert: true, path: files
$ ori team.yaml
- name: Alice
- name: Bob
- name: Carol
$ ori mapKeys team.yaml, =name
Alice:
  name: Alice
Bob:
  name: Bob
Carol:
  name: Carol
```

<a name="values"></a>

## @map/values(graph, mapFn, [options])

Returns a new graph whose values are the result of invoking the `mapFn` function for each value in the original graph.

```console assert: true, path: files
$ ori greetings.yaml
Alice: Hello, Alice.
Bob: Hello, Bob.
Carol: Hello, Carol.
$ ori uppercase.js
export default (x) => x.toString().toUpperCase();
$ ori "map(greetings.yaml, uppercase)"
Alice: HELLO, ALICE.
Bob: HELLO, BOB.
Carol: HELLO, CAROL.
```

Unlike a JavaScript [Array map](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/map), the `map` function does not do any mapping work upon invocation — it only does the work when someone requests the mapped graph's keys or values.

The `mapFn` mapping function is typically a JavaScript function, but can be any [graph variant](/core/variants.html). For example, you can use a second [graph as a map](/cli/intro.html#use-a-graph-as-a-map).

Available `options` include:

- `deep`: If `false` (the default), this only maps the top-level values in the graph. If `true`, this maps values at all levels of the graph.
- `getValue`: If `true` (the default), this gets values from the inner graph before passing them to the `mapFn`. If `false`, the `mapFn` is invoked with an `undefined` argument. In cases where you want to map all the values to, say, a constant value, this avoids the unnecessary cost of getting the values from the inner graph.
- `sourceExtension`: If supplied, this only maps values whose keys end in the indicated extension.
- `targetExtension`: If supplied, change the extension of the mapped keys to the indicated extension.