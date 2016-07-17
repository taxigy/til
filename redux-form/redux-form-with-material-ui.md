# Redux Form with Material UI

Two packages:

* [Redux Form](https://github.com/erikras/redux-form) and
* [Material UI](https://github.com/callemall/material-ui)

don't work perfectly together. There is a bunch of hints to make them do
their job together. Work in progress.

# Select Field

The Select Field from material-ui is great but it lacks compatibility with
redux-form. So, simply

```javascript
import React, { Component } from 'react';
import { reduxForm } from 'redux-form';
import {
  SelectField,
  MenuItem
} from 'material-ui';

@reduxForm({
  form: 'whatever',
  fields: 'nevermind'
})
export default class Whatever extends Component {
  render() {
    const {
      fields: {
        nevermind
      }
    } = this.props;

    return (
      <form onSubmit={::this.props.handleSubmit(fields => console.log(fields))}>
        <SelectField {...nevermind}>
          <MenuItem value="YAYAY" primaryText="YAYAY" />
        </SelectField>
      </form>
    );
  }
}
```

won't work. The Select Field's `onChange` works differently, and it's a
function of three parameters, the first is event, the second is index of
selected option, while the third is its value. So, to do a proper change and
get it picked up by Redux Form, you need

```javascript
import React, { Component } from 'react';
import { reduxForm } from 'redux-form';
import {
  SelectField,
  MenuItem
} from 'material-ui';

@reduxForm({
  form: 'whatever',
  fields: 'nevermind'
})
export default class Whatever extends Component {
  render() {
    const {
      fields: {
        nevermind
      }
    } = this.props;

    return (
      <form onSubmit={::this.props.handleSubmit(fields => console.log(fields))}>
        <SelectField {...nevermind} onChange={(event, index, value) => nevermind.onChange(value)}>
          <MenuItem value="YAYAY" primaryText="YAYAY" />
        </SelectField>
      </form>
    );
  }
}
```

and then it works.
