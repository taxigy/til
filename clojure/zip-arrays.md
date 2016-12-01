# Zip arrays

A very simple function that zips two vectors, just as `zip` in
Python does with arrays, so that `[[1 2 3] [4 5 6]]` become `[[1 4] [2 5] [3 6]]`:

```clojure
(defn zip
  ([left right] (zip left right []))
  ([left right result] (if (empty? left)
                           result
                           (zip
                             (rest left)
                             (rest right)
                             (conj result [(first left) (first right)])))))
```

(simply because I forgot the name of a function from STL that
does exactly the same thing)
