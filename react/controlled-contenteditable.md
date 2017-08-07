# Controlled element with "contenteditable" attribute

Consider a little React app:

```javascript
class App extends React.Component {
  constructor(...args) {
    super(...args);
    this.state = {
      value: 'Start typing',
    };
  }

  render() {
    return (
      <div
        contentEditable
        id="editable"
        onInput={e => this.setState({
          value: e.target.innerText,
          updated: true,
        })}
        children={this.state.value}
      />
    );
  }
}

ReactDOM.render(<App />, document.getElementById('app'));
```

It's just a single `div` element with [`contenteditable` attribute](https://developer.mozilla.org/en/docs/Web/API/HTMLElement/contentEditable). A typical case for WYSIWYG that, when mixed with React, gives very strange behavior.

In this particular case, whatever you type in this contenteditable, the caret will jump to the beginning at every keydown, wherever it was before, so the text will get reversed as you type. See [the Codepen](https://codepen.io/rishatmuhametshin/pen/mMWyRO?editors=0010).

The caret is easy to fix: just make the component uncontrolled (remove `children`) prop, like that:

```javascript
return (
  <div
    contentEditable
    id="editable"
    onInput={e => this.setState({
      value: e.target.innerText,
      updated: true,
    })}
  />
);
```

just like that. Then, whatever is typed, will be put into `value` property of component state anyway.

But what if you want to pass _initial value_? Four things:

- initial value may be passed via props to component constructor, so let's assume `this.state.value` is first set in `constructor(...args)`,
- when component did mount, its refs are ready, and you need ref to the contenteditable DOM element,
- simply set `innerText` _attribute_ to initial value passed to the component, and you can be completely sure `this.state.value`, and
- afterwards, on every input (`onInput` prop) update component state.

Like this:

```javascript
class App extends React.Component {
  constructor(...args) {
    super(...args);
    this.state = {
      value: 'Start typing',
    };
    this.node = null;
  }

  componentDidMount() {
    if (this.node) {
      this.node.innerText = this.state.value;
    }
  }

  render() {
    return (
      <div
        contentEditable
        id="editable"
        ref={node => this.node = node}
        onInput={e => this.setState({
          value: e.target.innerText,
          updated: true,
        })}
      />
    );
  }
}
```

This works (see [Codepen](https://codepen.io/rishatmuhametshin/pen/YxZyLo?editors=0010)), but only when the component would never receive a new value via props after being initially mounted. But if you ever hit this case, just update `innerText` in `componentDidReceiveProps`.
