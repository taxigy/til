# Resetting the value of select field in redux-form

With redux-form, it's very easy to reset values in a form:

```javascript
import React, { Component } from 'react';
import { reduxForm } from 'redux-form';

@reduxForm({
  form: 'my-epic-form',
  fields: []
})
export default class MyEpicComponent extends Component {
  render() {
    return (
      <div className="my-epic-component">
        <form>
          <button onClick={::this.props.resetForm}>Reset</button>
        </form>
      </div>
    );
  }
}
```

There's even no need to declare a function (in a form of class method) to reset form because redux-form _injects the prop_ to do it, the `resetForm` prop that is a function. When called, it cleans values of all the fields in this particular form.

But it doesn't work with `select` fields. After you chose a value in select field, and try to reset the form, the value will remain. It's annoying, and there's a way to manually overcome that: just call `onChange` of every field that is a select element:

```javascript
import React, { Component } from 'react';
import { reduxForm } from 'redux-form';
import _ from 'lodash';

@reduxForm({
  form: 'my-epic-form',
  fields: ['whatevs']
})
export default class MyEpicComponent extends Component {
  resetForm() {
    this.props.resetForm();
    this.props.fields.whatevs.onChange(''); // <- set the value of "whatevs" field to an empty string
  }

  render() {
    const {
      fields: {
        whatevs
      }
    } = this.props;

    return (
      <div className="my-epic-component">
        <form>
          <select
            {...whatevs}>
            {_.map([1, 2, 3, 4], (value, index) => (
              <option
                key={index}
                value={value}>
                {value}
              </option>
            ))}
          </select>
          <button onClick={::this.resetForm}>Reset</button>
        </form>
      </div>
    );
  }
}
```

Setting value of the select field with an empty string erases it. It doesn't work with `null` or `undefined`, and only works with an empty string.
