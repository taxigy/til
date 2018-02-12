# Element's classList

[`Element#classList`](https://developer.mozilla.org/de/docs/Web/API/Element/classList) is a property of a DOM element that expose a nice set of methods to manipulate its classnames (CSS classes). Because Element is mutable, its classList is also mutable.

Was:

```javascript
document.getElementById('highlight-on-mouseenter').addEventListener('mouseenter', (event) => {
  event.target.className += ' highlighted';
});

document.getElementById('highlight-on-mouseenter').addEventListener('mouseleave', (event) => {
  event.target.className = event.target.className.replace(' highlighted', '');
});
```

Now:

```javascript
document.getElementById('highlight-on-mouseenter').addEventListener('mouseenter', (event) => {
  event.target.classList.add('highlighted');
});

document.getElementById('highlight-on-mouseenter').addEventListener('mouseleave', (event) => {
  event.target.classList.remove('highlighted');
});
```
