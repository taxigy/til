# Factorial

A good example of how `reduce` can be used to get a factorial of a number
without recursion:

```clojure
(defn factorial [n]
  (reduce * (range 1 (inc n))))
```

Neat.
