# Keep yielding from top to bottom of the function

Imagine a generator:

```javascript
function* some() {
  yield {
    a: 1,
    b: yield {
      a: 2,
      b: yield {
        a: 3
      }
    }
  };
}
```

(which nobody would ever even think to implement, but nothing is
guaranteed when it's about programming).

Now let's iterate through the generator:

```javascript
for (let {a} of some()) {
  console.log(a);
}
```

What will be the output?

A:

```
1
2
3
```

B:

```
3
2
1
```

C: okay, I admit I don't understand generators, oh wait, I just
checked that on [Babel REPL](https://goo.gl/ENPd4N), now I see.

Try to always keep `yield` statements as in natural order as
possible. It's definitely not a good thing to show off your super
programmer power at.
