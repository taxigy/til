# Using debugger in an async function in Node

Consider a function that throws an error, and you see an
unhandled Promise rejection on the console. To inspect it, first, use
["try/catch"](../javascript/asynchronous-functions-should-have-try-catch.md).
Then, place `debugger` into the `catch` statement:

```javascript
async function fetchData(url, params) {
  try {
    const response = await fetch(url, params).then(r => r.json());
    console.log(response);
  } catch (error) {
    debugger; // <- here it goes
    console.log(error);
}
```

Then, run

```bash
node inspect ./path/to/the/file.js
```

and `cont` until it hits the breakpoint. Now you can go to REPL
and access values of all the variables in scope of this function
that preceded the breakpoint.

A common mistake is to not use the "try/catch":

```async function fetchData(url, params) {
  const response = await fetch(url, params).then(r => r.json());
  debugger; // <- unreachable
```

Here, the breakpoint simply cannot be reached, as the function
throws (and exits) before that.

Use "try/catch". Place `debugger` within "catch".
