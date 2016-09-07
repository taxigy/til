# Mapping against newly created array

This

```javascript
new Array(45).map((x, index) => (
  <div key={index}>45 times!</div>
));
```

won't work. The instance of Array created isn't an array of 45 undefined element, it's an empty array of length 45. To make it work, [fill](https://developer.mozilla.org/en/docs/Web/JavaScript/Reference/Global_Objects/Array/fill) it:

```javascript
new Array(45).fill().map((x, index) => (
  <div key={index}>45 times!</div>
));
```

Now it will output 45 `div` elements (completely unrelated to the array content).
