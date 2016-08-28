# Using react-bootstrap-date-picker in redux-form

The [react-bootstrap-date-picker](https://www.npmjs.com/package/react-bootstrap-date-picker) component is mostly suitable with Redux Form, but there are two things to keep in mind:

- when focus is on it, the date picker appears, and
- when you click any date and the date picker disappears, first `blur` event is returned and dispatched.

Redux Form isn't comfortable with is so you can simply suppress all the `onBlur` events by overriding them:

```javascript
<DatePicker
  {...someDateField}
  onBlur={() => null} />
```

This way, you spread all the properties of `someDateField` object received from `reduxForm` higher-order component, and then override the `onBlur` prop with a function that does nothing and returns null. This way, the `onChange` is reached, and the form happily dispatches a proper action and updates the Store.
