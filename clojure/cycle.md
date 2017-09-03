# Cycle a seq

From a vector

```clojure
(def points [x1 x2 x3 x4])
```

I needed

```clojure
[x1 x2 x3 x4 x1]
```

so I did

```clojure
(conj points (first points))
```

however, there is a universal solution: `cycle`.

Cycle takes a seq and returns a lazy seq that repeats itself:

```clojure
(cycle [x1 x2 x3 x4])
;; => (x1 x2 x3 x4 x1 x2 ...)
```

So I could `take` from it:

```clojure
(take (inc (count points)) (cycle points))
;; => (x1 x2 x3 x4 x1)
```
