# Passing initial values to Redux Form instance

Two ways to do that, one using static values, and another, state-dependent values:

## Static values

Simple add them as `initialValues` property of the object passed as the first argument:

```javascript
import React, { Component } from 'react';
import { reduxForm } from 'redux-form';

@reduxForm({
  form: 'some-form',
  fields: ['fieldA', 'fieldB'],
  initialValues: {
    'fieldA': 123,
    'fieldB': 456
  }
})
class SomeForm extends Component {
  handleSubmit(values) {
    // do some stuff with values
  }

  render() {
    const {
      fields: {
        fieldA,
        fieldB
      }
    } = this.props;

    return (
      <div className="some-form">
        <form onSubmit={::this.props.handleSubmit(::this.handleSubmit)}
          <input
            {...fieldA}
            type="text" />
          <input
            {...fieldB}
            type="text" />
          <button type="submit">Submit</button>
        </form>
      </div>
    );
  }
}
```

## State-dependent values

There's a second argument for that:

```javascript
import React, { Component } from 'react';
import { reduxForm } from 'redux-form';

@reduxForm({
  form: 'some-form',
  fields: ['fieldA', 'fieldB']
}, state => ({
  initialValues: {
    'fieldA': state.someProperty.someValueA,
    'fieldB': state.anotherProperty.anotherValueB
  }
}))
class SomeForm extends Component {
  handleSubmit(values) {
    // do some stuff with values
  }

  render() {
    const {
      fields: {
        fieldA,
        fieldB
      }
    } = this.props;

    return (
      <div className="some-form">
        <form onSubmit={::this.props.handleSubmit(::this.handleSubmit)}
          <input
            {...fieldA}
            type="text" />
          <input
            {...fieldB}
            type="text" />
          <button type="submit">Submit</button>
        </form>
      </div>
    );
  }
}
```

More than that, there's no need to wrap `reduxForm`-ed component into `connect` directly.
Redux Form's higher-order function takes just the same arguments as `connect`, but shifted by one:

```javascript
connect(state => ({
  ...state
}), {
  ...actionCreators
})(SomeComponent)
```

is equivalent to

```javascript
reduxForm({
  form: 'some-form',
  fields: ['fieldA', 'fieldB'],
  // other redux-form-specific properties
}, state => ({ // <- the second argument is mapping state properties to component props
  ...state
}), { // <- the third argument is list of action creators mapped to component props, etc.
  ...actionCreators
})(SomeComponent)
```
