# Validation in Redux Form version 5 vs 6

How it was done back in redux-form@^5 days:

```javascript
@reduxForm({
  form: 'whatever-form'
})
class WhateverForm extends Component {
  const {
    fields: {
      title
    }
  } = this.props;

  render() {
    return (
      <div>
        <input
          {...fields.title}
          name="title"
          component={Input} />
        {field.error}
      </div>
    )
  }
}
```

How it's done now in redux-form@^6

```javascript
@reduxForm({
  form: 'whatever-form'
})
class WhateverForm extends Component {
  render() {
    return (
      <div>
        <Field
          name="title"
          component={Input} />
      </div>
    )
  }
}
```

and

```javascript
export const Input = field => (
  <div>
    <input
      {...field}
      type="text" />
    {field.meta.error}
  </div>
);
```

So,

- `field.error` is now `field.meta.error`,
- `field` is now not a prop but a prop injected into the component `Input` that is passed as `component` prop value into `Field`, which is crazy genius, and
- no need to pick up values from `this.props` directly.
