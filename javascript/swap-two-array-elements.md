# Swap two array elements

Let's pretend we have an array with a few items:

```javascript
const array = [0, 1, 2, 3];
```

and we want a function that would swap first and third items, so
that we get

```javascript
[2, 1, 0, 3]
```

Let's make a function that swaps two items in an array, that

- takes the initial array, and two indices of items to swap,
- indices are both within boundaries of the array, and
- the type of item, as always in Javascript, doesn't matter, so
  no XOR or arithmetics here.

We deliberately omit the case when the two indices are equal, as
in this case it's optimal to just return the initial array.

## Old school with a temporary variable

Destructive way to swap two items using a third variable:

```javascript
function swap(array, i, j) {
  const temp = array[i];

  array[i] = array[j];
  array[j] = temp;
}
```

## Destructuring assignment to assign values of items

Destructive way to swap two elements in one syntactic
construction:

```javascript
function swap(array, i, j) {
  [array[j], array[i]] = [array[i], array[j]];
  return array;
}
```

## Destructuring assignment to clone an array

Assigning by reference is risky; assigning by value is not, and
the way to assign an array by its value in JS is to spread it and
put into a new array:

```javascript
function swap(array, i, j) {
  const result = [...array];
  [result[j], result[i]] = [result[i], result[j]];
  return result;
}
```

## Reduce

Suboptimal immutable way to swap two elements in O(N) using
`Array#reduce`:

```javascript
function swap(array, i, j) {
  return array.reduce((total, current, index) => {
    if (i === index) {
      return total.concat(array[j]);
    } else if (j === index) {
      return total.concat(array[i]);
    }

    return total.concat(current);
  }, []);
}
```

## Map

Suboptimal immutable way to swap two elements in O(N) using
`Array#map`:

```javascript
function swap(array, i, j) {
  return array.map((current, index) => {
    if (i === index) {
      return array[j];
    } else if (j === index) {
      return array[i];
    }

    return current;
  })
}
```

## Slice

Seemingly optimal (if we assume `Array#slice` and `Array#concat`
are both optimal) way to swap two items is by slicing the initial
array into three pieces:

```javascript
function swap(array, i, j) {
  const l = Math.min(i, j);
  const r = Math.max(i, j);
  const left = array.slice(0, l);
  const center = array.slice(l + 1, r);
  const right = array.slice(r + 1);

  return [...left, array[j], ...center, array[i], ...right];
}
```

Note that, for the same values of `i` and `j`, this function will
certainly return a wrong value.

---

This note is a living collection of usable, sometimes weird, ways
to swap two items in an array of items of any type in Javascript
without using any third-party libraries. The cost of maintenance
of a dependency is always higher than of one's own code, given
both solve comparable sets of problems.
