# String as an element name

This code works:

```jsx
const X = () => {
  const Tag = "a";
  return <Tag href="#">click</Tag>;
};
```

because this code works:

```jsx
const X = () => {
  const Tag = "a";
  return React.createElement(Tag, { href: "#" }, "click");
};
```
