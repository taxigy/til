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

Ideas:

1. Use component-level state to prevent the app state update and component
   re-render on every change.
2. Debounce an action, but this way the input field value will get stuck to its
   initial value.

Solution?

See [StackOverflow
Q&A](http://stackoverflow.com/questions/35398115/how-do-i-change-input-field-value-the-way-it-doesnt-run-through-the-whole-redux).
