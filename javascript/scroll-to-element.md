# Scroll to an element

To scroll the page to a certain element,

- use [`Element.prototype.scrollIntoView`](https://developer.mozilla.org/en-US/docs/Web/API/Element/scrollIntoView),
- when the method is not available, use [`window.scrollTo`](https://developer.mozilla.org/en-US/docs/Web/API/Window/scrollTo).

Like this:

```javascript
function scrollIntoView(element) {
  if (Element.prototype.scrollIntoView) {
    element.scrollIntoView({ behavior: 'smooth', block: 'end' });
  } else {
    window.scrollTo(
      window.pageXOffset,
      window.pageYOffset -
        window.innerHeight +
        element.getBoundingClientRect().bottom
    );
  }
}
```