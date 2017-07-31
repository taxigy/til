# Update a component if and only if its parent component is updated

Consider a composition of a text editor and a button:

```javascript
function Button() {
  return (
    <button />
  );
}

function Form(props) {
  return (
    <Editor>
      <Button />
    </Editor>
  );
}
```

Should Button update? It has to decide whenever Editor is updated.

A less costly version that works in most cases when Button should get updated
whenever Editor is updated:

```javascript
function button {
  return (
    <button />
  );
}

function Form(props) {
  return (
    <Editor>
      {button()}
    </Editor>
  );
}
```

Pros:

- not a component,
- therefore doesn't have lifecycle event handler functions,
- therefore no overhead,
- greater performance.

At scale, you should probably use `shouldComponentUpdate`, but it can be even a
costlier operation than rendering it like that.
