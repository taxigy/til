# Destructuring assignment with generators

Yes:

```javascript
function* generator() {
  while (true) {
    yield 1;
  }
}

const [first, second] = generator();

console.log(first, second); // -> 1 1
```

But why?
