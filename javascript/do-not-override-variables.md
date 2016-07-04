# Do not override variables

This gotcha cost me a few hours of debugging. You see, since `let` and
`const` respect scope of every code block, it is very tempting to use them like

```javascript
const a = 1;

if (a === 1) {
  const a = a + 1;
  console.log(a);
}
```

That last `a` should be a reference to externally defined `a` while the
in-block `a` should be a new `a` that is not an old `a`, right? Nope.

This code transpiles to

```javascript
"use strict";

var a = 1;

if (a === 1) {
  var _a = _a + 2;
  console.log(_a);
}
```

as opposed to expected

```javascript
"use strict";

var a = 1;

if (a === 1) {
  var _a = a + 2; // <- HAHA NOPE
  console.log(_a);
}
```

While this code:

```javascript
const a = 1;

if (a === 1) {
  const a = 2;
  console.log(a);
}
```

is totally correct and transpiles into

```javascript
"use strict";

var a = 1;

if (a === 1) {
  var _a = 2;
  console.log(_a);
}
```

Be cautious with redefenition of variables in ES6!

## Update: do not reassign somewhere below

Consider this code:

```javascript
function* something() {
  const request = 123;
  const response = yield request;

  if (property) {
    const response = yield request;
  }
}
```

Everything is okay: the `response` at function scope level and
`response` at condition block scope level are different
variables. Now take a look at this:

```javascript
function* something() {
  const request = 123;
  const response = yield request;

  if (response) {
    const response = yield request;
    const request = 456;
  }
}
```

The problem here is that `request` is redefined, but it's used to
`yield` prior to that. In terms of ES6, everything is okay. But
the

```javascript
const response = yield request;
const request = 456;
```

is transpiled in a way similar to

```javascript
var _response;
var _request;
yield request;
request = 456;
```

So, instead of having `request` equal to 123 on first yield and
to 456 on second, it's 123 on first and undefined on second, and
will only be defined _after_ the second yield. Beware!
