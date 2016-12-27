# Return value of a Promise

Sometimes you have a function and this function uses some async
code in it, such as
[Promise](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise).
I bet once you encounter it, you're instantly urged to make your
code _wait_ until the Promise has been resolved. Something like
this:

```javascript
function queryApiAndDoThings() {
  let response;

  queryApi().then(data => {
    response = data;
  });

  pleaseWait(10000000);

  return response;
}
```

Think you outsmarted Javascript? Hell nope!

The truth is, there is no way to turn asynchronous code, if even
appearing partially, into a piece of truly synchronous code. At
least, not in Javascript.

What you can do instead is to dive into that deep
multi-time-dimensional hole of Promises. And do it this way:

```javascript
function queryApiAndDoThings() {
  return new Promise((resolve, reject) => {
    queryApi().then(data => {
      resolve(data);
    }).catch(error => {
      reject(error);
    });
}
```

This example isn't very smart. It introduces +1 layer of
asynchronous absraction without providing any value. But let's
say you want to do some other things. For example, perform a
sequence of API calls:

```javascript
function queryApiAndDoThings() {
  return new Promise((resolve, reject) => {
    queryApi().then(data => {
      // now you have data from the first response
      queryApiAgain(data).then(moreData => {
        resolve(moreData);
      }).catch(error => {
        reject(error);
      });
    }).catch(error => {
      reject(error);
    });
}
```

Here, you call `queryApi`, wait until immediately returned
Promise gets resolved, then call `queryApiAgain`, wait until this
one gets resolved as well, and then, all of a sudden, you're able
to resolve the Promise you return from `queryApiAndDoThings` and
access data from the second API call (think of value of
`moreData`).

Now you can do this:

```javascript
queryApiAndDoThings().then(moreDataWillBeHere => {
  // do something with the value - remember moreData?
}).catch(error => {
  // do something with error message, which is actually usually undefined
});
```

Now, there's a less efficient but cleaner way to express these
nested async operations with [ES6's
async/await](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Statements/async_function):

```javascript
async function queryApiAndDoThings() {
  try {
    const data = await queryApi();
    const moreData = await queryApiAgain(data);

    return moreData;
  } catch (error) {
    return error;
  }
}
```

This code is equivalent to the previous one semantically, but you
can see that async/await thing. To make it work, you should use
[babel-polyfill](https://babeljs.io/docs/usage/polyfill/), which
actually works pretty nicely with Webpack.

The only downside is that async/await is _slower_. So be careful
with them, especially when many async calls are not
interdependent. In this case, use
[Promise.all](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise/all).

Also, take note that, in an async function, try/catch block is
important, [see
why](asynchronous-functions-should-have-try-catch.md).
