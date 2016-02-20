# Changing Input Field Values Bound to App State

A classic way to keep input fields' values in perfect sync with app state is
using actions to update app state on every input field change, like

```jsx
import {updateTitle} from '../actions/sales';

class Sale extends Component {
  onTitleChange(event) {
    const {value} = event.target;
    const {id} = this.props;

    this.props.updateTitle(id, value);
  }

  render() {
    return (
      <input
        placeholder="The Title"
        value={this.props.title}
        onChange={::this.onTitleChange} />
    );
  }
}

export default connect(({sales}) => ({
  title: sales.title
}), {
  updateTitle
}))(Sale);
```

The problem is that, for every change occuring in the input field, a new history
entry will be created and a new action reduced. Over time, the app will be
getting slower and more laggy. The action history that's typically shown in the
debug panel will be stuffed with same actions with difference in just a single
character of one of the properties of the state.

To make it better, there's `onBlur` prop that listens to blur event. It won't
work with `value` prop, which means if you try, the value of the field will be
preserved. So instead, the `defaultValue` prop should be used:

```javascript
render() {
  return (
    <input
      placeholder="The Title"
      defaultValue={this.props.title}
      onBlur={::this.onTitleChange} />
  );
}
```

This way, only getting cursor away from the input field will fire an action. In
most cases it's enough. The most common case when it's it _not_ is a search
field with autocomplete. To make it work, use `defaultValue` and `onBlur` props
and arrange the component-level state changes on every change.

See also [StackOverflow
Q&A](http://stackoverflow.com/questions/35398115/how-do-i-change-input-field-value-the-way-it-doesnt-run-through-the-whole-redux).
