# OAuth2 in React and Express app

I believe there's a package for that (see [react-oauth](https://www.npmjs.com/package/react-oauth) or [redux-oauth](https://github.com/lynndylanhurley/redux-auth)) but it's never a bad idea to implement such a little thing yourself and learn something.

To add OAuth support to your React app, you need a server because OAuth requires three parts:

- OAuth provider's server,
- your application's front-end where a user clicks that "Login with X" button, and
- your server that the user will be redirected to after successful or unsuccessful authentication in X.

[Express](https://expressjs.com/en/4x/api.html) is the easiest option here. In future, you may want to replace it with [something more reliable](https://www.youtube.com/watch?v=1e1zzna-dNw). Let's first add things. Run

```bash
npm install --save express react react-router
touch index.html
mkdir server; touch server/index.js
mkdir client; touch client/index.js
```

Now we're having the tree structure like

```
.
|- index.html
|- client
|  |- index.js
|- server
   |- index.js
```

which is obviously not enough for the app to work since you have to run Webpack and such, but let's pretend all the Webpack, babel-loader and other things are done already and so you always end up with `build/` folder containing `client.js` and `server.js`:

```
.
|- index.html
|- client
|  |- index.js
|- build
|  |- client.js
|  |- server.js
|- server
   |- index.js
```

Now, let's add things to the `client/index.js`:

```javascript
import React, { Component } from 'react';
import { render } from 'react-dom';
import { Router, Route, browserHistory } from 'react-router';

export default render((
  <Router history={browserHistory}>
    <Route path="/" component={App} />
    <Route path="/authenticated" component={Authenticated} />
  </Router>
), document.getElementById('app'));
```

And `server/index.js`:

```javascript
import express from 'express';

const app = express();
const {
  PWD = __dirname,
  PORT = 3000
} = process.env;

app.get('/', (req, res) => {
  res.sendFile('index.html', {
    root: PWD
  });
});

app.get('/auth', (req, res) => {
  const {
    auth_code
  } = req.query;

  res.sendFile('index.html', {
    root: PWD
  });
});

app.listen(PORT);
```

See that for both locations `/` and `/auth` the server sends the same file. In browser, react-router will take care of rendering the right component, see the router configuration above.

Don't forget to add `build/client.js` to your HTML file:

```html
<!DOCTYPE html>
<html>
<head>
  <meta name="description" content="OAuth stub" />
  <meta name="viewport" content="width=device-width, initial-scale=1.0" />
</head>
<body>
  <div id="app"></div>
  <script src="./build/client.js"></script>
</body>
</html>
```

And run the server with

```bash
node build/server.js
```

Now go to http://localhost:3000 (or another port specified) and see that both `/` and `/auth` work, even though the page is being re-rendered.

----

TBC:

- [ ] describe `App` and `Authenticated` components, and
- [ ] describe the process of server-to-server communication (third step of OAuth), exchanging `auth_code` to `access_token`.
