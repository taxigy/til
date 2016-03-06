# Asynchronous functions should have try/catch

In ES7, there's a great (and not yet confirmed to be included into official
specification) feature that allows to write _kind of_ synchronous code using
`async` and `await`. Like this:

```javascript
// before:
function fetchUsers(regex, callback) {
  db.collection('users').find({
    firstName: regex
  }, function (error, result) {
    if (error) {
      callback(error);
    } else {
      callback(null, result);
    }
  });
}

// after:
async function fetchUsers(regex) {
  const users = await db.collection('users').find({
    firstName: regex
  });

  return users;
}
```

Here, the new function is very optimistic. It expects users to be successfully
fetched in 100% cases. But what if something goes wrong? The `users` variable
will just stay `undefined`, right?

Wrong.

What will exactly happen is that what's after the `await` function call will
never be executed. The transpiled ES5 code will not do anything. The Promise
will not get resolved. As a result, no matter what goes after the first line of
the new function, if error is thrown, it will never be reached.

The solution is, _always have try/catch_, which mimics `then` and `catch`
methods of Promise:

```javascript
// after, and 100% reliable:
async function fetchUsers(regex) {
  try {
    const users = db.collection('users').find({
      firstName: regex
    });

    return users
  } catch (error) {
    console.error(`Something is wrong with the regex "${regex}".`, error);
    return [];
  }
}
```

This will ensure that the Promise that stays behind this asynchronous function
is guaranteed to be resolved, whether it's error or result has been received.
