# Redirect to an external URL

In React, it's sometimes necessary to not just open a link by
click on an anchor element, but also perform some operation
before that. So, simple

```jsx
render() {
  const {
    url = 'http://github.com'
  } = this.props;

  return (
    <a
      href={url}
      onClick={() => console.log('Going away')}>
      go away
    </a>
  );
}
```

won't really work when the function that is assigned to `onClick`
event it heavy or even asynchronous.

Instead, the navigation logic can be put right into that
function. To perform the navigation itself, there's
`window.location.assign` method for that:

```jsx
handleClick(event) {
  const {
    url = 'http://github.com'
  } = this.props;

  event.preventDefault();
  console.log('Will go away in 2 seconds');

  setTimeout(() => {
    console.log('Going away');
    window.location.assign(url); // <- this one
  }, 2000);
}

render() {
  return (
    <a
      href="#"
      onClick={::this.handleClick}>
      go away
    </a>
  );
}
```

You can still supply an url via `url` prop (or `href`, to mimic
anchor element's behavior) and have the navigation performed
properly.

But wait, there's more!

You can use this same logic not just with anchor element, but
with buttons or even simple `div` elements, too! Now how cool is
that?
