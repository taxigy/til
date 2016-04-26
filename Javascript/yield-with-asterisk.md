# Yield with asterisk

A good thing about ES6 generators is that you can yield a generator from a
generator, but not as a generator, but instead, as values of the latter
generator. Watch:

```javascript
function* generator() {
  yield [1, 2, 3];
}

for (let next of generator()) {
  console.log(next);
}
```

This will output

```
[1, 2, 3]
```

Now let's use yield with asterisk to _dive into the nested generator_
(since, I should have told earlier, all iterables can behave like generators):

```javascript
function* generator() {
  yield* [1, 2, 3];
}

for (let next of generator()) {
  console.log(next);
}
```

This, in turn, will output

```
1
2
3
```

But there's really weird behaviour in, honestly, weird composition of
yields. Like,

```javascript
function* gen() {
  yield* [1, 2, yield 3];
}

for (let next of gen()) {
  console.log(next);
}
```

Two points:

- yield instructions follow AST, hence "3" as the first output, and
- whatever the reason, and I really don't know right now, the last value of
  yield is undefined:

```
3
1
2
undefined
```

But why? Let's deconstruct. It's going to be awesome.

First, the `yield 3` thing works. This is perceived as a little _lambda
generator_ (please don't kill me) that, attention!, returns nothing.

Next, the yield with asterisk works, returning three values. So why the last
is undefined? Because that lambda generator returned nothing, hence
undefined. Because `yield` inside a `yield` has to `return` something to pass
a value to the outer-scope generator.

So, in theory, everything works fine. But in practice, there's no way to make
the last value (or last many, as many as there are nested yields) yield
something other than undefined.

Please don't use that in production.
