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
