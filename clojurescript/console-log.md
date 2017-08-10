# How to do `console.log`?

```clojure
(def log (.-log js/console))
```

so that

```clojure
(log "it" "also" "is" "variadic" "by" "default")
```
