# Curried "add" function

Imagine you need a curried function to add two or many numbers. One could look
like this:

```javascript
const add = (...args) => {
  switch (args.length) {
    case 0: return add;
    case 1: return (...more) => add(...args, ...more);
    default: return args.reduce((t, c) => t + c);
  }
}
```

So that

```javascript
console.log(add(1, 2, 3, 4)); // -> 10
console.log(add(1)(2, 3, 4)); // -> 10
console.log(add(1)()()()()()()()()(2, 3, 4)); // -> 10
console.log(add()()()()()(1, 2, 3, 4)); // -> 10
```
