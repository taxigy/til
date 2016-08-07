# Fetching data directly in the browser from a remote API

If you try to fetch data from a remote API in the browser, avoiding server-to-server request, it's likely you'll bump into [CORS issue](https://developer.mozilla.org/en-US/docs/Web/HTTP/Access_control_CORS). To make it really work, you need JSONP. In modern browsers, we already have `window.fetch` that implements XHR but we can't use it to fetch from remote APIs.

Good thing there's [fetch-jsonp](https://github.com/camsong/fetch-jsonp) lib that make JSONP requests work. The strangest thing is that it generates a callback function but not all remote APIs support it out of the box. You need to provide _both_ `jsonpCallback` and `jsonpCallbackFunction`:

```javascript
import fj from 'fetch-jsonp';

const url = 'http://www.colourlovers.com/api/colors/random?format=json';

fj(`${url}?jsonpCallback=callback`, {
  jsonpCallbackFunction: 'callback'
}).then(r => r.json()).then(data => console.log(data)).catch(e => console.error(e));
```

So, don't forget to

- modify URL such that it has `jsonpCallback` query parameter set to anything, and
- provide parameter `jsonpCallbackFunction` that has the same value.
