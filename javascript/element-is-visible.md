# Is a DOM element visible?

A very common problem is to determine whether a DOM element is
visible in the viewport.
[`Element#getBoundingClientRect`](https://developer.mozilla.org/en-US/docs/Web/API/Element/getBoundingClientRect) to the rescue!

```javascript
function isElementVisible(element) {
  if (element instanceof Element) {
    const {
      top,
      right,
      bottom,
      left
    } = element.getBoundingClientRect();
    const {
      clientHeight,
      clientWidth
    } = document.documentElement;

    return top >= 0 && right <= clientWidth && bottom <= clientHeight && left >= 0;
  }
}
```
