# Keep vs filter

There are two functions that seem similar: `keep` and `filter`,
the difference is that `keep` returns the value of a predicate
applied to each value in collection, while `filter` returns the
item itself if the predicate returns truthy value:

```clojure
user=> (keep even? (range 10))
(true false true false true false true false true false)
user=> (filter even? (range 10))
(0 2 4 6 8)
```

and that `keep` keeps false, too, while `filter` omits it:

```clojure
user=> (keep identity '(nil true false))
(true false)
user=> (filter identity '(nil true false))
(true)
```
