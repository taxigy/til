# Error handler middleware in Express

As official docs say, an error handler is a function that you pass to
`Express#use` with a very special signature: four arguments. It looks like this:

```javascript
import express from 'express';

const app = express();

app.use((error, request, response, next) => {
  // do stuff
});
```

Now, that's actually enough. Make sure that route handler
functions use `next` properly, and that's it:

```javascript
app.get('/:fruit', (request, response, next) => {
  if (request.params.fruit === 'bananas') {
    next('nooooo');
  }
});

app.use((error, request, response, next) => {
  console.log(error); // will print "nooooo"
});
```

A few gotchas here:

1. You may want real errors (aka instance of `Error`) to be
   passed to error handler, just because then you'll have proper
   error stack.
1. When calling `next`, it's historically considered that falsy
   value passed as the first argument means "no error" (because
   `!error` will evaluate to true).
1. You can use as many error handlers as you want, given that you
   pass execution from current to next one using `next`. But why
   though.

So, regarding the first point, a better configuration of a route
handler and an error handler would look like

```javascript
app.get('/:fruit', (request, response, next) => {
  if (request.params.fruit === 'bananas') {
    next(Error('nooooo'));
  }
});

app.use((error, request, response, next) => {
  console.log(error); // will print "Error: nooooo" and the stack trace
});
```
