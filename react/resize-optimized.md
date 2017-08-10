# Window resize, optimized

Request animation frame, and only dispatch an event on its
callback. It's more efficient.

Consider a function that will take one argument, a new event
name, and return another name for the new event that will be
dispatched only with fraction of likelihood of "resize" event:

```javascript
const optimizedResize = (eventName = 'optimizedResize') => {
  let isResizing = false;

  window.addEventListener('resize', () => {
    if (!isResizing) {
      isResizing = true;

      window.requestAnimationFrame(() => {
        window.dispatchEvent(new window.CustomEvent(eventName));
        isResizing = false;
      });
    }
  });

  return eventName;
};
```

In a component, on "did mount", attach this event:

```javascript
componentDidMount() {
  window.addEventListener(optimizedResize(), () => {
    // Do something, for example,
    this.setState({
      screenHeight: window.innerHeight,
      screenWidth: window.innerWidth,
    });
  });
}
```

Should work better.
