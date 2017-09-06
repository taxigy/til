# Check if a function is a generator

Say, you have a function:


```javascript
function* generateStuff() {
  yield 1;
}
```

and you want to make sure it's a generator function.

Bad ways to do that:

```javascript
typeof generateStuff; // -> 'function', not really helpful
generateStuff instanceof Function; // true, so what?
```

Okay ways to do this:

```javascript
function* sample() {};

generateStuff.constructor === sample.constructor; // -> true
(() => {}).constructor === sample.constructor; // -> false
(function () {}).constructor === sample.constructor; // -> false

// or

generateStuff.constructor.name === 'GeneratorFunction'; // -> true

// or

generateStuff instanceof sample.constructor; // -> true
```

Binding a generator using [`Function#bind`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_objects/Function/bind) breaks it.

To check if an _instance_ of a generator function is what you expect it to be,
refer to its `Symbol.iterator` property:

```javascript
typeof generateStuff()[Symbol.iterator]; // -> 'function';
typeof (function () {})[Symbol.iterator]; // -> 'undefined';
```

None of the methods that work are standard, and the API may vary between
platforms, as well as change over time. Which is kinda sad, but who'd ever
test a function with that precision anyway.
