# Vector into a hash-map

Simply doing `into` won't work:

```clojure
(into {} [1 2 3 4]) ;; throws IllegalArgumentException
```

nor will doing `hash-map`:

```clojure
(hash-map [1 2 3 4]) ;; throws IllegalArgumentException
```

Because `hash-map` is a variadic function, it's better to `apply` it:

```clojure
(apply hash-map [1 2 3 4]) ;; => {1 2 3 4}
```

Works.
