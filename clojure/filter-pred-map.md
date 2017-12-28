# Remove keys from map by predicate

Say, you have a map:

```clojure
{0 0 1 1 2 2 3 3 4 4 5 5}
```

and you only need to keep the items where values are odd numbers.
In case of a map, `remove` isn't really helpful:

```clojure
user=> (remove even? {0 0 1 1 2 2 3 3 4 4 5 5})

IllegalArgumentException Argument must be an integer: [0 0]  clojure.core/even?
```

Ideally, we would want a similar function that takes a predicate
and a map, but the one that works with a map and returns a new
map. Something like this:

```clojure
(defn remove-vals [pred? m]
  (into {} (filter (comp not pred? second) m)))
```

So that

```clojure
user=> (remove-vals even? {0 0 1 1 2 2 3 3 4 4 5 5})
{1 1, 3 3, 5 5}
```
