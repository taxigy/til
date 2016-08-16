# Generators 101

In ES2015, there's a nice feature called [generators](https://developer.mozilla.org/en/docs/Web/JavaScript/Reference/Statements/function*). It's like a typical function but way more improved and suitable in specific cases when you need to preserve context and keep it in form of a single function.

A generator is declared as function-asterisk, like this:

```javascript
function* generator() {

}
```

It's okay for a generator to

- have no body at all or some body,
- return nothing at all or return something, and
- yield nothing at all or yield something, as many times as you need.

Wait, yield? That's right. Yield.

Yield is an operation that can only happen in a generator. A generator can yield values as many times as there are reachable yield statements in it. Sometimes none is reachable, and sometimes all, however you'll find out both are only cases and none of them is guaranteed.

Every generator can be instantiated:

```javascript
function* generator() { // <- the generator is declared
  return 1;
}

const g = generator(); // <- the generator is instantiated
```

Here, `g` is a reference to the instance of our generator. You can see that, when a generator is instantiated, you don't use `new` keyword as it typically happens when you instantiate new objects.

Next, a generator can yield. To do that, we use `yield` keyword:

```javascript
function* generator() {
  yield 1;
  yield 2;
  yield 3;
}
```

You can see that this generator has three `yield` statements going one after another. Let's make it a little more complex right away:

```javascript
function* generator(initialValue) {
  if (initialValue) {
    yield 0;
  }

  yield 1;
  yield 2;
  yield 3;
}

const g = generator('I am a truthy initial value');
```

Not only you can see that `yield 0` only happens when you provide truthy `initialValue` but also you can see that you can pass any arguments into generator when you instantiate it. This is why the same generator can yield different number of times based on the data that flows into it.

Now, how to get data from the generator? Use `next` property of its instance:

```javascript
function* generator(initialValue) {
  if (initialValue) {
    yield 0;
  }

  yield 1;
  yield 2;
  yield 3;
}

const g = generator('I am a truthy initial value');

console.log(g.next());
```

A few important things happen here.

When you call `next` function that's a property of generator instance, the generator starts working from the very first statement, and it is `if` statement. Next statement is either `yield 0` or `yield 1`, based on the truthiness of `initialValue`. In our case, it is truthy, so `yield 0` is executed.

After a `yield` is executed, the generator _pauses_. This is what I found to be the hardest thing to understand for most people who learn ES6. This is why you can safely use infinite loops in generators: they won't really loop, the execution will be paused every time you call `next`.

So if we do

```javascript
console.log(g.next());
```

again, since we went through `yield 0`, the next statement is `yield 1`. This is what `next` function will return and this is where the generator will pause next time.

The value `next` returns is an object of two properties: `done` and `value`. This is an implementation feature that is built into language. You can't change the names of the properties and you can't make `next` return anything else. While `done` tells you whether the generator has reached the end, `value` is the value that the generator yields. So:

```javascript
console.log(g.next()); // -> {done: false, value: 0}
console.log(g.next()); // -> {done: false, value: 1}
console.log(g.next()); // -> {done: false, value: 2}
console.log(g.next()); // -> {done: false, value: 3}
console.log(g.next()); // -> {done: true}
```

You can see that when there's no value, the key is not present in the object. It's a minor nuance and you should always rely on `done` property to know whether the generator has or hasn't stopped yet.

So, speaking of infinite loops, take a look at this:

```javascript
function* infinite() {
  while (true) {
    yield 'still working';
  }
}

const g = infinite();

console.log(g.next()); // -> {done: false, value: 'still working'}
console.log(g.next()); // -> {done: false, value: 'still working'}
console.log(g.next()); // -> {done: false, value: 'still working'}
console.log(g.next()); // -> {done: false, value: 'still working'}
console.log(g.next()); // -> {done: false, value: 'still working'}
console.log(g.next()); // -> {done: false, value: 'still working'}
// don't try to reach the end, it's impossible
```

One last basic thing about generators is that you can use `yield` to pass values to your generator. Like this:

```javascript
function* infinite() {
  while (true) {
    const stop = yield 'still working';

    if (stop) {
      return yield 'okay, done';
    }
  }
}

const g = infinite();

console.log(g.next()); // -> {done: false, value: 'still working'}
console.log(g.next()); // -> {done: false, value: 'still working'}
console.log(g.next(true)); // -> {done: false, value: 'okay, done'}
console.log(g.next()); // -> {done: true}
```

You'd expect it to stop at the very moment `true` has been passed to yield, right? Nope, and abstract syntax tree is the answer. Let's deconstruct it step by step:

1. `next` property is called without arguments.
1. In generator, `while` condition is checked, and it's `true`.
1. `yield` statement yields the value "still working", `g.next()` returns value, the generator pauses.
1. `next` property is called without arguments.
1. In generator, `stop` is assigned with the value passed to `next` and it's `undefined`.
1. `if` condition checks if `stop` is truthy; it's undefined, therefore not.
1. Next iteration of `while` loop, condition `true` is truthy.
1. `yield` statement yields the value "still working", `g.next()` returns value, the generator pauses.
1. `next` property is called with an argument and it's `true`.
1. In generator, `stop` is assigned with the value passed to `next` and it's `true`.
1. `if` condition checks if `stop` is truthy; it is, therefore the execution follows the body of `if` statement.
1. Next operation is `yield 'okay, done'`, the generator yields this value and `g.next()` returns it.
1. `next` property is called with an argument and it's `true`.
1. One more statement is left in generator, and it's `return`.
1. The generator returns, `g.next()` returns, and it's done.

Keep in mind that

```
return yield •;
```

is not a single operation, those are two operations!

What if we remove the last `yield`? It will work, obviously, in a different way (spoiler: in a way you expect):

```javascript
function* infinite() {
  while (true) {
    const stop = yield 'still working';

    if (stop) {
      return 'okay, done';
    }
  }
}

const g = infinite();

console.log(g.next()); // -> {done: false, value: 'still working'}
console.log(g.next()); // -> {done: false, value: 'still working'}
console.log(g.next(true)); // -> {done: true, value: 'okay, done'}
console.log(g.next()); // -> {done: true}
```

You can see that, in this case, the generator states it's done and returns value as well, and next time it simply tells you it's done and won't ever return any value. That's because `return •` is a single operation and `next` property of generator instance perceives it as a point to return something. Since generator returns, it's never going to continue execution from that point, therefore it's done.

----

Take a look at more advanced (and weird) [example of flattening an array using generators](flatten-array-with-generators.md) to know how to yield a generator from generator instead of yielding just a value.
