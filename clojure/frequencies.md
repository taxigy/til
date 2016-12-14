# Frequencies

Imagine you have a collection of possibly repeating items, and
just a an intermediary step, you need to know how many
occurrences of every element there are in source collection.

You might start with something like

```clojure
(defn occurrences
  ([collection] (occurrences collection {}))
  ([collection occurrences-map]
   (if (empty? collection)
       occurrences-map
       (occurrences
         (rest collection)
         (update
           occurrences-map
           (first collection)
           (fnil inc 0))))))

(occurrences [1 2 2 3 3 3 4 4 4 4])
```

because it's obvious that source is a collection (possibly a
vector) and result is a map, right?

Sure, but you could also do

```clojure
(frequencies [1 2 2 3 3 3 4 4 4 4])
```

because [you should read the
docs](http://realworldclojure.com/one-weird-trick/).
