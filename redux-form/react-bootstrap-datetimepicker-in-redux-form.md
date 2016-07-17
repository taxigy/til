# react-datetime-picker in redux-form

There's a [very nice datetime picker React component](http://dev.quri.com/react-bootstrap-datetimepicker/) that just does its job. It's a stateful component that [encapsulates appearance details in its state](https://github.com/quri/react-bootstrap-datetimepicker/blob/master/src/DateTimeField.js). Now let's see how it gets incorporated into a redux-form-enabled controlled component:

```javascript
import React, { Component } from 'react';
import DateTimeField from 'react-bootstrap-datetimepicker';
import { reduxForm } from 'redux-form';

@reduxForm({
  form: 'my-epic-form',
  fields: ['begin', 'end']
})
export default MyEpicForm extends Component {
  handleSubmit(values) {
    console.log(values);
  }

  render() {
    return (
      <div className="my-epic-form">
        <form onSubmit={::this.props.handleSubmit(::this.handleSubmit)}>
          <DateTimeField
            {...this.props.fields.begin} />
          <DateTimeField
            {...this.props.fields.end} />
        </form>
      </div>
    );
  }
}
```

A few things happen here:

1. A new component `MyEpicForm` is declared, obviously.
1. The form renders a form.
1. The form's `onSubmit` event is assigned with a curried function `this.props.handleSubmit`. The latter is taken from props of a component, where it appears after redux-form enriches the component with its own props. See the ["Simple Form" example](http://redux-form.com/5.3.1/#/examples/simple) to see what props redux-form injects into `MyEpicForm` component.
1. The redux-form's `handleSubmit` function, referred to as `this.props.handleSubmit`, takes a single parameter: a user-defined function, which happens to have the same name (just commonly used pattern, not a strict rule). User-defined function `handleSubmit` is an own property of `MyEpicForm` component, therefore it's referred to as `this.handleSubmit`.
1. The double-colon syntax is short-hand for binding a function to current contest (see [StackOverflow Q&A](http://stackoverflow.com/q/31220078/1287643)). So anything that looks like `::whatever` is identical to `whatever.bind(this)`.
1. The `MyEpicForm` component renders two instances of `DateTimeField`.
1. The first `DateTimeField` instance is assigned with props taken from `this.props.fields.begin`. This is not redux-form-specific syntax, it's a [commonly used spread attributes syntax](https://facebook.github.io/react/docs/jsx-spread.html), which should read as "take every property of object `this.props.fields.begin` and assign identically named prop of that component with its value".
1. Same happens to the second `DateTimeField` instance.
1. When a form is submitted, `this.handleSubmit` method will be called with a single argument `values` which is a plain-object where keys are names of all the fields, and values are values taken from those fields. In our case `values` is an object of two properties: `begin` and `end`.

A more common syntax of this is picking certain values from `this.props` via destructuring assignment and using references to constants rather than to properties of `this.props` object:

```javascript
import React, { Component } from 'react';
import DateTimeField from 'react-bootstrap-datetimepicker';
import { reduxForm } from 'redux-form';

@reduxForm({
  form: 'my-epic-form',
  fields: ['begin', 'end']
})
export default MyEpicForm extends Component {
  handleSubmit(values) {
    console.log(values);
  }

  render() {
    const {
      fields: {
        begin,
        end
      },
      handleSubmit
    } = this.props;

    return (
      <div className="my-epic-form">
        <form onSubmit={handleSubmit(::this.handleSubmit)}>
          <DateTimeField
            {...begin} />
          <DateTimeField
            {...end} />
        </form>
      </div>
    );
  }
}
```

There are two interesting features of `DateTimeField` component:

1. It uses [moment](http://momentjs.com/) lib inside of it. It means that if you pass a valid moment instance into it, `DateTimeField` will be happy.
2. On every change, it executes the `onChange` callback with a weird _timestamp as a string._ You expect it to return an [ISO 8601 string](https://en.wikipedia.org/wiki/ISO_8601), or at least a valid timestamp which is a Number, but nope, it's a timestamp wrapped into String.

The weirdness comes from the behavior of `format` method of a moment object:

```javascript
moment().format('x') // -> "1468672000000"
typeof moment().format('x') // -> "string"
```

So as long as you only use this component alone, it's safe. `DateTimeField` operates over moment, no problem whatever the formatted value of it could be. But once you have an API that expects certain format of datetime, there comes trouble.

First, if you take a look at what value is returned on every change, you'll see that it is really a string that contains a timestamp:

```javascript
import React, { Component } from 'react';
import DateTimeField from 'react-bootstrap-datetimepicker';
import { reduxForm } from 'redux-form';

@reduxForm({
  form: 'my-epic-form',
  fields: ['begin', 'end']
})
export default MyEpicForm extends Component {
  handleSubmit(values) {
    console.log(values);
  }

  render() {
    const {
      fields: {
        begin,
        end
      },
      handleSubmit
    } = this.props;

    return (
      <div className="my-epic-form">
        <form onSubmit={handleSubmit(::this.handleSubmit)}>
          <DateTimeField
            {...begin}
            onChange={x => console.log(`The value of new "begin" date is "${x}" while its type is "${typeof x}".`)} />
          <DateTimeField
            {...end} />
        </form>
      </div>
    );
  }
}
```

Now try to change the value of `begin` field and see in the console:

```
The value of new "begin" date is "1468672000000" while its type is "string".
```

That's good!, you think, but it's not. Try to pass exactly the value into `moment`:

```javascript
moment('1468672000000').toJSON() // -> null
```

What if the value was a Number and not a String?

```javascript
moment(1468672000000).toJSON() // -> "2016-07-16T12:26:40.000Z"
```

So let's pretend your API expects values of `begin` and `end` fields to be ISO 8601 strings? If you simply rely on `DateTimeField`'s own perception of this world, you'll end up with `null` values all the time. Not good.

Let's overcome this:

1. Let `DateTimeField` display value as it would like to. After all, it uses moment, and moment is reliable against most representations of datetime.
1. On every `DateTimeField` change, let's convert that weird stringified timestamp into ISO 8601 string.

Like this:

```javascript
import React, { Component } from 'react';
import DateTimeField from 'react-bootstrap-datetimepicker';
import { reduxForm } from 'redux-form';

@reduxForm({
  form: 'my-epic-form',
  fields: ['begin', 'end']
})
export default MyEpicForm extends Component {
  handleSubmit(values) {
    console.log(values);
  }

  render() {
    const {
      fields: {
        begin,
        end
      },
      handleSubmit
    } = this.props;

    return (
      <div className="my-epic-form">
        <form onSubmit={handleSubmit(::this.handleSubmit)}>
          <DateTimeField
            {...begin}
            onChange={x => begin.onChange(moment(x, 'x').toJSON())} />
          <DateTimeField
            {...end}
            onChange={x => end.onChange(moment(x, 'x').toJSON())} />
        </form>
      </div>
    );
  }
}
```

First, how come `begin` and `end` constants are objects that have `onChange` properties? This is what redux-form does: every property of `this.props.fields` is an object that itself contains many properties, and `onChange` among them. Remember how the value of the field changed even though there wasn't any `onChange` prop defined explicitly? It's been among spread attributes. Now it's defined explicitly _after_ the spread attributes, so it _overwrites_ the identically named prop that comes anywhere above. Think this:

```javascript
const object = {
  property: 1,
  property: 2 // <- overwrites previous value of "property" property!
};

console.log(object); // -> {property: 2}
```

or even

```javascript
const props = {
  a: 1,
  b: 2,
  c: 3
};
const object = {
  ...props, // <- take every property of "props" and put them, one by one, into "object".
  c: 0;
};

console.log(object); // -> {a: 1, b: 2, c: 0}
```

This is what happens when you spread props in JSX:

```javascript
const props = {
  a: 1,
  b: 2,
  c: 3
};
const component = (
  <MyComponent
    {...props} />
);
const component2 = (
  <MyComponent
    a={props.a}
    b={props.b}
    c={props.c} />
);
```

In this example `component` and `component2` have identical props, the difference is in how each of them picks values from `props` object.

So, let's get back to the `DateTimeField`. It now has a used-defined `onChange` prop which uses `begin.onChange` or `end.onChange` function. What happens is every time `begin` or `end` field's value changes, the new value is processed by `onChange` and so the newly received value will be not a stringified timestamp but an ISO 8601 string instead.

After that, you can be sure that any time you submit your form, the `values` object contains `begin` and `end` properties, and values of those are _valid ISO 8601 strings:_

```javascript
import React, { Component } from 'react';
import DateTimeField from 'react-bootstrap-datetimepicker';
import { reduxForm } from 'redux-form';
import clean from 'clean-tagged-string';

@reduxForm({
  form: 'my-epic-form',
  fields: ['begin', 'end']
})
export default MyEpicForm extends Component {
  handleSubmit(values) {
    console.log(clean`
      values=${JSON.stringify(values)},
      while values.begin=${values.begin} and it is ${moment(values.begin).isValid() ? 'valid' : 'invalid'},
      and values.end=${values.end} and it is ${moment(values.end).isValid() ? 'valid' : 'invalid'}.
    `);
  }

  render() {
    const {
      fields: {
        begin,
        end
      },
      handleSubmit
    } = this.props;

    return (
      <div className="my-epic-form">
        <form onSubmit={handleSubmit(::this.handleSubmit)}>
          <DateTimeField
            {...begin}
            onChange={x => begin.onChange(moment(x, 'x').toJSON())} />
          <DateTimeField
            {...end}
            onChange={x => end.onChange(moment(x, 'x').toJSON())} />
        </form>
      </div>
    );
  }
}
```

So after you change values in any `DateTimeField` any times, including zero, when you submit, you'll see on the console:

```
values={"begin":"2016-07-16T12:26:40.000Z","end":"2016-07-16T12:26:40.000Z"}, while values.begin=2016-07-16T12:26:40.000Z and it is valid, and values.end=2016-07-16T12:26:40.000Z and it is valid.
```

So now you can be completely sure that any time you perform an API request on form submit, the values of `begin` and `end` are ISO 8601 strings.

If your API expects timestamps as numbers, just change the behavior of the two functions passed into `onChange` props.

----

It's likely there's a shorter path to making the component fire `onChange` callback with something different than `moment(value).format('x')`. TBD.
