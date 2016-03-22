# Tranforming callback-enabled function into a Promise

[Promise](https://developer.mozilla.org/en/docs/Web/JavaScript/Reference/Global_Objects/Promise) is all new global object proposed and already implemented in ES6. What it is good at is it works with async/await functions, that are a part of ES6 spec, too. The idea is, when you have an asynchronous function, you can do something like

```javascript
async function f() {
  return await anotherAsynchronousFunction();
}
```

It looks like it's synchronous, at least the code appears so, but it works
asynchronously, which is great because it's non-blocking (kind of).

But in the world of Node, there are many functions that are callback enabled,
such as

```javascript
function f(callback) {
  // do something
  callback();
}
```

So those functions don't return anything, they call a callback function instead.
It makes asynchronous execution of those possible, but there's a very thin line
and so called callback hell on the other side of it. So, in ES6 (or ES7, or
ES2015, or whatever), async/await has been proposed, along with promises.

There's nice Promise implementation for ES5 already, the
[bluebird](https://github.com/petkaantonov/bluebird) package. It provides a dead
simple way to turn a callback function into a Promise with
`Promise.promisifyAll` method. But it's always good how things actually work.
With async/await function it's easy.

The idea is that a function returns a new Promise that, in turn, takes a single
parameter, a function of two arguments, both are references to callbacks, the
first one is called when everything is good, the second, when something isn't.
So:

```javascript
export function promising(someParam, ...otherParams) {
  return new Promise((resolve, reject) => {
    callbacking(someParam, otherParams, (error, result) => {
      if (error) {
        reject(error);
      } else {
        resolve(result);
      }
    });
  });
}
```

The arrow function is really a callback function that is required by
`callbacking` classic callback-enabled function. This function, again, in
most classic way, receives (in strict order) two parameters: error and result.
If the `callbacking` function has been executed successfully, the error
will be `null` (but the result can be undefined or null, too, which is not the
case). So, if error is not null, it's time to call `reject`, otherwise it's
`resolve`.

Next, this function can be used with async/await:

```javascript
async function f() {
  try {
    const result = await promising();
  } catch (error) {
    console.error(error);
  }
}
```

So, the `callbacking` function is wrapped into `promising`. The end user will
never know (until they really care) that the former is called behind the latter.
