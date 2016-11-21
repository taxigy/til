# Find item inside an array

I saw a very straightforward implementation of this:

```javascript
const sourceArray = ['a', 'b', 'c'];
const target = 'a';
let theItem = null;

for (let sourceItem of sourceArray) {
  if (target === sourceItem) {
    theItem = source;
  }
}

// do something with theItem
```

But there's
[Array#find](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/find) for that, so you can do it in a functional way:

```javascript
const sourceArray = ['a', 'b', 'c'];
const target = 'a';
const theItem = sourceArray.find(sourceItem => sourceItem === target);

// do something with theItem
```

The `find` function is a very common pattern, using it is safe in both Node
and browsers, and while worst case time complexity is identical to a `for`
loop, code readability wins!
