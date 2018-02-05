# Transposed matrix from source matrix

Consider a matrix:

```python
import numpy as np

m = np.array([[1, 2, 3], [4, 5, 6], [7, 8, 9]])
```

It can be transposed as

```python
>>> np.transpose(m);
array([[1, 4, 7],
       [2, 5, 8],
       [3, 6, 9]])
```

but it also has the property `T` that returns the same value:

```python
>>> m.T
array([[1, 4, 7],
       [2, 5, 8],
       [3, 6, 9]])
```
