# Async vs Promise in an Express route handler

tl;dr: both are okay, in best case performance is equivalent.

An async function assigned to a route handler callback might look like this:

```javascript
const app = express();

app.get('/', async (request, response, next) => {
  try {
    const response = await doAsyncStuff();
    res.json(response);
  } catch (error) {
    res.json(null);
  }
});
```

while Promise-based workflow would look like

```javascript
app.get('/', (request, response, next) => {
  doAsyncStuff().then((response) => {
    res.json(response);
  }).catch(() => {
    res.json(null);
  });
});
```

For a test route, performing 10,000 GET requests didn't show any significant
divergence in performance. Both route handlers performed in range between 25ms
and 150ms, with median around 50ms.

But when the async route handler had two or more `await` statements, it would
perform significantly slower, causing increased response time. It might be
obvious but it's good to know that it _really_ is so, according to the same
test of 10k GET requests. Whenever microoptimization matters, use raw Promise
syntax.
