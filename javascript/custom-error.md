# Custom Error

Say you want to throw Bananas. Here's how:

```javascript
function Bananas(message) {
  const error = Error.call(this, message);
  error.name = 'Bananas';
  return error;
}
```

Use in REPL:

```javascript
throw new Bananas(true);
```

will output

```
Bananas: true
    at new Bananas (repl:2:21)
    at repl:1:7
```

and further stack.
