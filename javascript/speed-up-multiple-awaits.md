# Speed up multiple async/await function calls

Let's pretend you have something like this:

```javascript
async function doSomeStuff() {
  const reds = await fetch(RED_API_URL);
  const greens = await fetch(GREEN_API_URL);

  return {
    reds,
    greens
  };
}
```

in this very typical pattern, two `await` statements appear. What really happens is that every API call performed with `fetch` is synchronous in this case. When you're requesting reds, the call is blocking. Greens will only start being requested as soon as reds have been totally resolved.

There's [`Promise.all`](https://developer.mozilla.org/en/docs/Web/JavaScript/Reference/Global_Objects/Promise/all) that allows you to combine multiple Promises and resolve them as _all_ of them have been resolved. It's waiting, but it's waiting in a smart way: all the API calls are performed asynchronously, and in parallel.

Let's pretend now that you _really need_  to return from this function. Returning a Promise or performing an old-school callback just won't work. Say no more!

```javascript
async function doSomeStuff() {
  const [reds, greens] = await Promise.all([
    fetch(RED_API_URL),
    fetch(GREEN_API_URL)
  ]);

  return {
    reds,
    greens
  };
}
```

BOOM — performance boost! And not only that, the data will generally be fetched from the API way quicker. The trick is that both `fetch` calls are performed in parallel, just because the first call, `fetch(RED_API_URL)` doesn't block the second one, `fetch(GREEN_API_URL)`. But the function will still return properly, in a typical for async/await function way (hint: a Promise).

-----

There can be a situation when you really need to perform to consecutive `await`s, for example, when you rely on results from the first call when performing a second call:

```javascript
async function doSomeStuff() {
  const reds = await fetch(RED_API_URL);
  const greens = await fetch(GREEN_API_URL, reds);

  return {
    reds,
    greens
  };
}
```

Here, fetching greens requires information about reds, otherwise it would be not guaranteed that the code works correctly. Well, in this case, you need two consecutive `await`s, and `Promise.all` is totally irrelevant.
