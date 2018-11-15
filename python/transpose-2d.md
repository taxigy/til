# Transpose a two-dimensional array

Given arrays

```python
xs = [1., 2., 3.]
yx = [3., 3., 3.]
```

get an array of pairs:

```python
pairs = [p for p in list(zip(xs, ys))]
# [(1.0, 3.0), (2.0, 3.0), (3.0, 3.0)]
```

The `p` can be destructured, too:

```python
averages = [(x + y) / 2 for x, y in list(zip(xs, ys))]
# [2.0, 2.5, 3.0]
```

Nice.
