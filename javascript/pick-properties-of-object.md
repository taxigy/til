# Pick properties of object

The problem isn't very common, but sometimes you need to do
something like [`_.pick`](https://lodash.com/docs/4.16.6#pick)
would do. Generally, you have a list of properties you want to
get from an object (or a list of properties you want to remove
from that object), and so you want to do so.

Let's say, the properties you want to get is a list expressed in
form of an array:

```javascript
const targetProperties = ['a', 'b', 'c'];
```

Next what you can do is
[`Array#reduce`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/Reduce)
this array against the source object:

```javascript
const sourceObject = {
  a: 1,
  b: 2,
  c: 3,
  d: 4,
  e: 5
};
const targetProperties = ['a', 'b', 'c'];

const result = targetProperties.reduce((total, current) => {
  if (current in sourceObject) {
    return {
      ...total,
      [current]: obj[current]
    };
  } else {
    return total;
  }
}, {});
```

which basically means, starting from an empty object, run through
`targetProperties` array, and if a property of `sourceObject`
with key identical to current item of an array exists, put it
into the result object, otherwise keep going until the end.

The result will be an object of only three properties: "a", "b"
and "c". Other properties will be omitted.

----

The point in doing

```javascript
current in sourceObject
```

as opposed to

```javascript
sourceObject[current]
```

is in checking if property is present, and not the value of this
property. In the latter case, falsy values would be omitted,
which is likely not expected.
