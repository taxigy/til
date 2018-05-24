# Format a string in Python 2 and 3

In Python 2, there's
[`string.format()`](https://docs.python.org/3.4/library/stdtypes.html#str.format)
that is a method of a string class which immutably places values given as args
into the initial string:

```python
a = 1
b = 2
'a is {a}, b is {b}'.format(a=a, b=b)
```

In Python 3, there are
[f-strings](https://docs.python.org/3/reference/lexical_analysis.html#f-strings):

```python
a = 1
b = 2
f'a is {a}, b is {b}'
```

