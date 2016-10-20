# Render lag in stateful components

If you use Redux and use connected stateful components, you might
have seen a strange behavior in components when, on some event,
both an action is dispatched and component's state gets updated.
Like this:

```javascript
handleChange(event) {
  const { value } = event.target;

  this.setState({
    error: this.props.value !== value
  });

  this.props.dispatch({
    type: 'SOME_ACTION_WHEN_IT_CHANGES',
    value
  });
}
```

What happens then is the race between the two asynchronous
operations, each resulting in component update aka re-render. If
you rely both on `this.props.value` and `this.state.error` to
display some error message, you may see it appear for a very
short amount of time every time `handleChange` is executed (e.g.
an event occurs).

How to fix?

IDK LOL (TBD)
