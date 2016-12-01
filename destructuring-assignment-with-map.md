# Destructuring assignment with map

Map declaration is a number of pairs of key and value:

```clojure
{:key "value"}
```

so destructuring assignment goes in the opposite direction, first
variable then key:

```clojure
(def values {:key-for-a "value-of-a" :key-for-b "value-of-b"})

(defn print value-of-a [{value-of-a :key-for-a}]
 (print value-of-a))
```
