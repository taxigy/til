# Undefined view engine after applying Express app as middleware

It's possible to use Express apps as middlewares in other Express
apps. Looks like it breaks outer app's settings (set with
`app.set`).

This one will break:

```javascript
const path = require('path');
const express = require('express');
const middleware = express();
const app = express();

middleware.get('*', (req, res, next) => {
  req.runThroughMiddleware = true;
  next();
});

app.set('view engine', 'ejs');
app.set('views', path.join(__dirname, 'views'));

app.get('*', middleware, (req, res) => {
  res.render('index', {
    runThroughMiddleware: req.runThroughMiddleware,
  });
  // will throw and respond with 500
});
```

Surprisingly, this one will work:

```javascript
const path = require('path');
const express = require('express');
const middleware = express();
const app = express();

middleware.set('view engine', 'ejs');
middleware.set('views', path.join(__dirname, 'views'));

middleware.get('*', (req, res, next) => {
  req.runThroughMiddleware = true;
  next();
});

// no view engine and views settings for app!

app.get('*', middleware, (req, res) => {
  res.render('index', {
    runThroughMiddleware: req.runThroughMiddleware,
  });
  // will respond with 200 and properly rendered page
});
```

Be careful with this kind of middlewares.
