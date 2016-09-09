# Passing custom children

You might have [read the docs about `children` prop](https://facebook.github.io/react/tips/children-props-type.html), but there's a [Q&A about `children` being a function](http://stackoverflow.com/questions/39420515/where-can-i-find-the-api-guide-for-this-usage-of-passing-props-to-children-in-re/39420820#39420820). The question was, how comes `children` prop is a callable function when the official docs say it can only be a plain-object? Let's deconstruct.

A typical value of `children` prop is either `null` or a plain-object that represent children. If you console.log the children like this:

```javascript
const GettingThem = ({children}) => {
  console.log(children);

  return (
    <div>
      {children}
    </div>
  );
};
```

then you'd see it's a plain-object that has a few properties that are very similar to what you usually see in a component: `props`, `children` again, and so on.

And plain-objects are not functions. If you did

```javascript
const a = {};
a();
```

you'd end up with TypeError.

So how can `children` be a function? Just pass a function! Like this:

```javascript
const GettingThem = ({children}) => {
  console.log('So is now this.props.children a function?', children instanceof Function);

  return (
    <div>
      {children()}
    </div>
  )
}

const PuttingThen = () => (
  <div>
    {() => (
      <div>The children!</div>
    )}
  </div>
);
```

You'll see `true` on the console meaning that `children` prop is instance of `Function`, and every function (even an arrow function) is, well, instance of `Function` object, and therefore is callable. The result of calling this function is simply this virtual DOM:

```html
<div>The children!</div>
```

so the `GettingThem` component will return this DOM after performing `children()`.
