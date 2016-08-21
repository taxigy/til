# Adding props to children

A basic way to pass props in React is

```javascript
class Parent extends Component {
  render() {
    return (
      <div className="parent">
        <Child foo={'bar'} />
      </div>
    );
  }
}
```

This way, the prop `foo` with value "bar" is passed directly. But what if you want to pass a bunch of props stored in a form of plain-object? Easy:

```javascript
class Parent extends Component {
  render() {
    const props = {
      foo: 'bar',
      baz: false
    };

    return (
      <div className="parent">
        <Child {...props} />
      </div>
    );
  }
}
```

You can see that object spread works well and reflects plain-object, which is a bunch of key-values, into props that are key-values too.

But now, say, you want to make it work with react-router so you place `children` instead of placing components directly. Like this:

```javascript
class Parent extends Component {
  render() {
    return (
      <div className="parent">
        {this.props.children}
      </div>
    );
  }
}
```

To pass props from parent component to `children`, you'll need to

- map through children and
- clone them into new component instances with props you want.

But you can't simply map through children using `this.children.map` or lodash's `_.map(children)`. Instead, you should be using `React.Children.map`:

```javascript
class Parent extends Component {
  render() {
    const props = {
      foo: 'bar',
      baz: false
    };

    return (
      <div className="parent">
        {React.Children.map(this.props.children, child => {
          return React.cloneElement(child, {
            ...props
          })
        })}
      </div>
    );
  }
}
```

The fact you need to do that itself is a red flag. Something isn't right with how you want to make it work. Always stick to direct prop passing and consider [Redux](/redux) when you have multiple components that need to access application state and you don't want to mess with passing props in classic React manner.

----

Inspired by [this question](http://stackoverflow.com/questions/39065374/passing-props-to-generic-children) on StackOverflow.
