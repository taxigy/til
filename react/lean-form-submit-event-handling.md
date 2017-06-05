# Lean form submit event handling

Huge solution: redux-form or react-redux-form or whatever that
contains "redux" and "form". Downside: the JS bundle is getting
bigger for only a fraction of features you use.

Standard solution: use component state!

```javascript
class LoginForm extends React.Component {
  constructor(...args) {
    super(...args);
    this.state = {
      username: '',
      password: '',
    };
  }

  handleChangeUsername(event) {
    this.setState(() => ({ username: event.target.value }));
  }

  handleChangePassword(event) {
    this.setState(() => ({ password: event.target.value }));
  }

  handleSubmitLoginForm(event) {
    event.preventDefault();

    authenticateUser(this.state.username, this.state.password);
  }

  render() {
    return (
      <form onSubmit={this.handleSubmitLoginForm.bind(this)}>
        <input type="text" onChange={this.handleChangeUsername.bind(this)} />
        <input type="password" onChange={this.handleChangePassword.bind(this)} />
      </form>
    );
  }
}
```

Lean solution: no state (and uncontrolled inputs)!

```javascript
class LoginForm extends React.Component {
  handleSubmitLoginForm(event) {
    event.preventDefault();

    const username = event.target.username.value;
    const password = event.target.password.value;

    authenticateUser(this.state.username, this.state.password);
  }

  render() {
    return (
      <form onSubmit={this.handleSubmitLoginForm.bind(this)}>
        <input type="text" name="username" />
        <input type="password" name="password" />
      </form>
    );
  }
}
```

Reference via element's `name` attribute from submit handler has
been in browser API for ages. There's no need to substitute it
with extra layer of abstraction, putting extra work into it.

Of course it only works for browser-specific elements like text
inputs, checkboxes, and file pickers. It doesn't work for highly
customized input components like 2D-sliders or date range
pickers. I wouldn't even recommend trying to substitute state
management with hidden fields in this case. Just because it's
easier in the beginning doesn't mean it's the right way.
