# One route, many params

Say, your app expects URLs to be either of those:

- /:locale/:page
- /:locale/:space/:page

Easy!

```javascript
const app = require('express')();

app.get('/:locale/(:page|:space/:page)', (request, response) => {
  const {
    locale,
    space = 'default value of space',
    page,
  } = request.params;

  // do stuff, respond with data
});
```
