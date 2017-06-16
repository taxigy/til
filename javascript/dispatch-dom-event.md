# Dispatch a DOM event

Consider a button:

```html
<button id="b">•</button>
```

then old school DOM way would be to create and event and dispatch it
through this DOM element.

```javascript
const button = document.getElementById('b');
const click = new MouseEvent('click');

button.dispatchEvent(click);
```

See also:

- [Event](https://developer.mozilla.org/en/docs/Web/API/Event)
- [`document.createEvent`](https://developer.mozilla.org/en-US/docs/Web/API/Document/createEvent)
- [`EventTarget#dispatchEvent`](https://developer.mozilla.org/en-US/docs/Web/API/EventTarget/dispatchEvent)
