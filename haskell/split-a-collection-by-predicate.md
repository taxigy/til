# Split a collection by predicate

_see also [the same stuff, but for Clojure](../clojure/ways-to-split-a-collection-by-predicate.md)_

Just learning Haskell, and while there should be a built-in way
to split (or group) a collection by an arbitrary predicate, I
found it entertaining to implement this function myself. So far
it's like this:

```haskell
split pred xs = ([x | x <- xs, pred x == True], [x | x <- xs, pred x /= True])
```

so that

```haskell
split (\x -> x > 3) [1, 2, 3, 4, 5, 6, 7]
-- ([4,5,6,7],[1,2,3])
```
