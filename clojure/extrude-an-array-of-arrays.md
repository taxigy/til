# Extrude an array of arrays

Sometimes you need to find all combinations of items in an array
where there can be items that represent many options, like

```clojure
[1 2 [4 5]] -> [[1 2 4] [1 2 5]]
```

A typical input/output in this case would look like:

```
[] -> []
[1] -> [1]
[1 2 3 4 5] -> [1 2 3 4 5]
[[1 2] 3 4 5] -> [[1 3 4 5] [2 3 4 5]]
[[1 2] 3 [4 5]] -> [[1 3 4] [2 3 4] [1 3 5] [2 3 5]]
```

and so on, for any number of items and any number of nested
layers.

This problem can be decomposed into two parts:

- smash current accumulated collection with a new item; if there
  are many items, smash them in a
  [mapcat](https://clojuredocs.org/clojure.core/mapcat) way, and
- go through the input collection to produce output.

The smashing part is simply a function that takes input
collection and either one item or collection of items. In case of
one item, it conjoins it with all items in accumulated result
collection (which are themselves collections); in the latter, it
goes through all items in the accumulated result and conjoins
each item from input collection. The code explains it better:

```clojure
(defn smash [xs ys]
  (if-not (coll? ys)
    (map conj xs (repeat (count xs) ys))
    (mapcat #(smash xs %) ys)))
```

Now, it's a matter of going through the input collection and
accumulating the result each step at a time:

```clojure
(defn extrusion [input]
  (loop [result [[]]
         xs input]
    (if (empty? xs)
      result
      (recur (smash result (first xs))
             (rest xs)))))
```

The result then looks like this:

```clojure
user=> (extrusion [])
[[]]
user=> (extrusion [1])
([1])
user=> (extrusion [1 2 3 4 5])
([1 2 3 4 5])
user=> (extrusion [[1 2] 3 4 5])
([1 3 4 5] [2 3 4 5])
user=> (extrusion [[1 2] 3 [4 5]])
([1 3 4] [2 3 4] [1 3 5] [2 3 5])
```

Note that the result is always a collection of collections and
contains at least one item, which, in turn, can be empty.

The function `extrusion` has a loop with accumulated value; it
looks like that `reduce` might be used instead:

```clojure
(defn extrusion [input]
  (reduce smash [[]] input))
```

which would save us a few lines of code.
