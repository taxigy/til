# React Router v4 Route component: render vs component

The Route component in react-router@4 has two props: `render` and
`component`. In the [source
code](https://github.com/ReactTraining/react-router/blob/59a4fbc184f3a3cf4c25ee39a7e97f72efed7849/packages/react-router/modules/Route.js#L112-L116),
it's pretty clear what's happening when either of those are used with
a component declared as a class or with a component declared as a
function:

```javascript
if (component)
  return match ? React.createElement(component, props) : null

if (render)
  return match ? render(props) : null
```

Using `component` prop is equivalent to creating an element:

```javascript
<Route path="•" component={MyComponent} />
```

works as if it was

```javascript
<Route path="•">
  <MyComponent />
</Route>
```

while using `render` prop is equivalent to executing a component
function:

```javascript
<Route path="•" render={MyComponent} />
```

works as if it was

```javascript
<Route path="•">
  {MyComponent()}
</Route>
```

Of course it doesn't work exactly like that. The component passed to
Route will actually receive props. But the lifecycle is similar.

So, if we smash together class and function components against
`component` and `render` props, we get four different options:

| | `component` prop | `render` prop |
| --- | --- | --- |
| **Component as a function** | Runs as is, no lifecycle methods are executed. | Runs as is, no lifecycle methods are executed. |
| **Component as a class** | Runs with all lifecycle methods. | _Throws an error_ because classes are not callable. |

---

Inspired by [Q&A on
StackOverflow](https://stackoverflow.com/questions/48150567/react-router-difference-between-component-and-render/48152635).

There's also [a Codepen about
that](https://codepen.io/rishatmuhametshin/pen/wppyEg?editors=1010)
(don't forget to open browser console).
