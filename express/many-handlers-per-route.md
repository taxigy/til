# Many route handlers per a single route in Express app

There was [a question about that](http://stackoverflow.com/questions/39000904). So let's discover a basic case:


```javascript
import express from 'express';

const app = express();

app.get('/', (req, res, next) => {
  console.log('First route handler');
  // ...
});

app.get('/', (req, res, next) => {
  console.log('Second route handler');
  // ...
});
```

First thing is, the execution will follow the order of declaration of route handlers. It means that, if both are executed, the console output is

```
First route handler
Second route handler
```

It's not guaranteed because

1. Execution may stop somewhere inside the first route handler.
1. Execution may follow one of the branches of conditional statement, either `if` or `switch`.
1. Execution may never reach the next route handler even though you expect it to do so.

When you declare route handlers in Express, they are simply declarations. They are used by Express to build up the workflow but it won't follow exactly the order you put into your code.

After one or more route handlers are registered, the app needs to know that it needs to jump from current to the next one. How would it know? There's the third argument to route handler callback function, `next`. When you call it, the next matching route handler is executed. So if you don't, it never will.

Therefore, we need to have it in the first route handler, like

```javascript
import express from 'express';

const app = express();

app.get('/', (req, res, next) => {
  console.log('First route handler');
  // ...
  next();
});

app.get('/', (req, res, next) => {
  console.log('Second route handler');
  // ...
});
```

Imagine that the first route handler does nothing but calling `next`. It's a valid route handler that passes the execution even though it is totally ineffectual. So you may need to respond with some data, say, with JSON:

```javascript
import express from 'express';

const app = express();

app.get('/', (req, res, next) => {
  console.log('First route handler');
  res.json({
    error: null,
    ok: true
  });
  next();
});

app.get('/', (req, res, next) => {
  console.log('Second route handler');
  // ...
});
```

Here, `res.json` both sends response data _and_ ends the response. It means, Express stops execution exactly after this function has been executed. Therefore, `next` will never be called, and "Second route handler" will never appear on the console.

It's different, though, when you only use `res` to build up response without sending it, for example, setting response headers or body. This way, it's very likely that you'll see "Second route handler" on the console.

This is precisely how any Express middleware works. It integrates into the workflow and is executed _before_ all your route handler and guess why? Because it is usually declared _above_ custom route handlers in the code. Think this:

```javascript
import express from 'express';
import myMiddleware from './middleware';

const app = express();

app.use(myMiddleware); // <- here myMiddleware injects its route handlers and so they will be executed before what comes after

app.get('/', (req, res, next) => {
  console.log('First route handler');
  res.json({
    error: null,
    ok: true
  });
  next();
});

app.get('/', (req, res, next) => {
  console.log('Second route handler');
  // ...
});
```

So it's possible to have as many handlers per a particular route as possible. Sometimes you may want to use it, for example, in your single-page applications where you need to

- return `bundle.js` and `bundle.css` when exactly these files are requested, and
- return `index.html` per _any_ route otherwise.

To do that, just declare two routes and use `next` to pass execution to the next route handler if applicable:

```javascript
import express from 'express';

const app = express();

app.get(':file', (req, res, next) => {
  const {
    file
  } = req.params;

  if (/^bundle.(js|css)$/.test(file)) { // <- check if the requested route looks like file name and it's bundle.js or bundle.css
    res.sendFile(file, { // <- returns corresponding file, either bundle.js or bundle.css
      root: __dirname
    });
  }

  next(); // <- only reachable when response hasn't been sent.
});

app.get('*', (req, res, next) => {
  res.sendFile('index.html', {
    root: __dirname
  });
});
```

Express will actually try to execute `next()` but `res.sendFile` states that execution shall be finished, and so whatever synchronous code that is executed afterwards doesn't take effect. However, I'd recommend placing `next` inside `else` conditional statement branch just to not make Express uncomfortable with synchronous code execution (which may sometimes happen).
