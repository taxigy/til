# Empty keys in a map

Say, there's a map:

```clojure
(def m {:a [1 2 3]
        :b [4 5 6]
        :c [7 8 9]})
```

and we want to empty all values and get this:

```clojure
{:a []
 :b []
 :c []}
```

How?

## Map through keys and update them

One obvious option is to go through all the keys of the map and update with `empty` by each key:

```clojure
(defn empty-keys [m]
  (loop [ks (keys m)
         r m]
    (if (empty? ks)
      r
      (recur (rest ks)
             (update r (first ks) empty)))))

(empty-keys m)
;; => {:a [] :b [] :c []}
```

## Interleave with empty values and put back into a map

A shorter, but somewhat less efficient (?) version of such a function would look like

```clojure
(defn empty-keys [m]
  (apply assoc {} (interleave (keys m) (map empty (vals m)))))
```

## Interleave with nil

Because Clojure treats nil as an empty collection in many cases (for example, you can `conj` or `assoc` to a nil), it's probably fine to lose the information about type of the collection at source. This way, 

```clojure
(defn empty-keys [m]
  (apply assoc {} (interleave (keys m) (repeat nil))))

(empty-keys m)
;; => {:a nil :b nil :c nil}
```

---

The search for ways to empty vals never ends.
