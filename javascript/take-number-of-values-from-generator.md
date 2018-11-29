# Take a limited number of values from an infinite generator

If we have a generator that yields an arithmetic progression, like this:

```javascript
function* progression() {
  let n = 0;

  while (true) {
    yield n++;
  }
}
```

then we can take an arbitrary number of items from the head of generated collection like this:

```javascript
function* take(n, generator) {
  let i = 0;
  let values = generator();

  for (let v of values) {
    if (++i > n) {
      return;
    }

    yield v;
  }
}
```

so that we get a generator that ends after a given number of values, thus:

```javascript
> for (let o of take(10, progression)) { console.log(o) }
< 0
< 1
< 2
< 3
< 4
< 5
< 6
< 7
< 8
< 9
```
