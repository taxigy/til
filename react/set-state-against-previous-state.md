# Set a property of state as reflection of its previous value

Say, you have a checkbox and you toggle its "checked" attribute
on click:

```javascript
class Checkbox extends Component {
  state = {
    checked: false
  }

  handleChange = () => {
    // toggling it!
  }

  render() {
    return (
      <input
        type="checkbox"
        checked={this.state.checked}
        onChange={this.handleChange} />
    );
  }
}
```

Classic way to toggle:

```javascript
handleChange = () => {
  this.setState({
    checked: !this.state.checked
  });
}
```

A slightly better way to do:

```javascript
handleChange = () => {
  this.setState(prevState => ({
    checked: !prevState.checked
  }));
}
```
