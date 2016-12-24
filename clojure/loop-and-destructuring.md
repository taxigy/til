# Loop and destructuring

A typical function that runs through a collection would contain a
`loop` in its body. Let's say, it's just another kind of `map`
but it requires first two elements of the collection at a time:

```clojure
(loop [collection source-collection]
      ; do something with
      ; (nth collection 0)
      ; and
      ; (nth collection 1)
      (recur (drop 2 collection)))
```

With destructuring in `loop`, it would look way cleaner:

```clojure
(loop [[first-item second-item & remaining] source-collection]
      ; do something with
      ; first-item
      ; and
      ; second-item
      (recur remaining))
```
