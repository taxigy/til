# Passing arguments "as is"

Old school way:

```javascript
function x() {
  y(arguments);
}

function y(a, b) {
  console.log(a, b);
}

x(1, 2);
```

ES6 way:

```javascript
function x(...args) {
  y(...args);
}

function y(a, b) {
  console.log(a, b);
}

x(1, 2);
```
