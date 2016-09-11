# API liaison in form of an Express app

Imagine that you have a single-page application that needs to perform API requests against a certain API, and that API is remote, its hostname is different, and it doesn't allow you to perform any requests against it from the browser directly. To make it work, it might need to enable CORS, but it doesn't. A typical way to deal with it is to make your own server that would bypass requests to that API. Mix it with React and [react-router's `browserHistory`](https://github.com/reactjs/react-router/blob/master/docs/guides/Histories.md#browserhistory), and we're having something more complex that's [been already described earlier](/express/many-handlers-per-route.md):

```javascript
const path = require('path');
const express = require('express');
const bodyParser = require('body-parser');
const axios = require('axios');
const {
  REMOTE_API_ENDPOINT = 'https://whatevs.com/api/v1',
  PORT = 3000
} = process.env;
const headers = {
  'X-Api-Client': 'replace me with client_id if necessary',
  'X-Api-Secret': 'replace me with client_secret if necessary'
};

app.get('/api/*', (request, response) => {
  const token = 'replace me with a valid OAuth token';

  axios.get(`${REMOTE_API_ENDPOINT}/${request.params[0]}`, {
    headers: {
      ...headers,
      'Authorization': `Bearer ${token}` // <- use your own headers just as your remote API requires
    }
  }).then(({data}) => response.json(data)).catch(error => response.json(error));
});

app.post('/api/*', bodyParser.json(), (request, response) => {
  axios.post(`${REMOTE_API_ENDPOINT}/${request.params[0]}`, {
    ...request.body
  }, {
    ...headers // <- add more headers if necessary
  }).then(({data}) => response.json(data)).catch(error => response.json(error));
});

// add app.put, app.delete, etc. if necessary

app.get('/:folder/:file', (request, response) => {
  // send files like /build/styles.css or /build/bundle.js
  response.sendFile(request.params.file, {
    root: path.resolve(__dirname, request.params.folder);
  });
});

app.get('*', (request, response) => {
  response.sendFile('index.html', {
    root: __dirname
  });
});

app.listen(PORT, () => console.log(`App is running on port ${PORT}.`));
```

What's happening here:

1. Whatever the route you send to the server, if it doesn't match ones declared before `app.get('*')`, the `index.html` will be returned.
1. In browser, as `index.html` has been received, its own `script` and `link` tags are being resolved. In case of this example, these are links to resources inside `/build` folder, like `./build/styles.css`. Note that if you have everything on root location, the setup will be different.
1. Per any `/api/*` request, Express puts whatever is matched by `*` into `0` property of `request.params` object. You can refer to this property by `request.params[0]`. In case of `/api/users/all`, `request.params[0]` will be equal to `users/all`.
1. What passed into `axios.get` or `axios.post` is a template string composed from three parts: value of `REMOTE_API_ENDPOINT` (replace with the base URL of your target API), a slash, and then the value of `request.params[0]`. The result might look like `https://whatevs.com/api/v1/users/all`.
1. After the request has been resolved, either result or error are handled by corresponding methods of Promise returned by `axios.get` or `axios.post`.

Piping a response like

```javascript
axios.get(•).then(res => response.json(res));
```

won't work. You need to pass directly the data from the response, i.e. `res.data`:

```javascript
axios.get(•).then(res => response.json(res.data));
```

while the `res` may be just destructured like this:

```javascript
axios.get(•).then(({data}) => response.json(data));
```

The only thing that is missing here is Hot Module Reload. (TBD)
