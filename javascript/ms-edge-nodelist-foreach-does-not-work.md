# One does not simply iterate over a NodeList in MS Edge

Somehow
[`NodeList#forEach`](https://developer.mozilla.org/en-US/docs/Web/API/NodeList/forEach)
is not implemented in Microsoft Edge 16 and below. So this code will break:

```javascript
document.querySelectorAll('section').forEach(fn);
```

To make it work, either use a polyfill, a for/in loop, or `Array#forEach`:

```javascript
Array.prototype.forEach(document.querySelectorAll('section'), fn);
```
