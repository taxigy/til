# Deconstructing with default values

The most interesting way to use deconstructing is to use is with default value
of parameters taken from an object provided as the only argument. Something like
if this:

```javascript
function compare(comparables) {
  return comparables.left < comparables.right;
}
```

would turn into this:

```javascript
function compare({left, right}) {
  return left < right;
}
```

And then into this:

```javascript
function compare({left = 0, right = 0}) {
  return left < right;
}
```

But the most interesting thing is that the whole argument can be set with a
default value:

```javascript
function compare({left = 0, right = 0} = {}) {
  return left < right;
}
```

This is the double security, because

1. When there are both `left` and `right` properties of the object, everything
   is fine.
2. When there's either `left` or `right` properties of the object, the missing
   one's value is set to the default one, `0`.
3. When there's neither `left` nor `right` property in the object, both are set
   to default values, `0`.
4. If there's no argument at all, it's taken as an empty object, _and then_ both
   `left` and `right` are set to their default values.

Very useful.
