# Split a collection against a predicate function

Take a collection:

```clojure
(def xs (1 2 3 4 5 6 7))
```

and a predicate:

```clojure
(def pred odd?)
```

We want to use a predicate function to split this collection into
two, where the first one contains only those elements for which
the predicate is true, and the second collection is where the
predicate is false.

## Group by

The most common way is to use `group-by`:

```clojure
(group-by pred xs)
;; => {true [1 3 5 7], false [2 4 6]}
```

## Juxtapose filter and remove

Another way to split is to use `juxt`:

```clojure
((juxt filter remove) pred xs)
;; => [(1 3 5 7) (2 4 6)]
```
