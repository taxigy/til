# Generator Contexts

In ES6, there's a nice thing called
[generators](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Statements/function*).
Those are pretty weird because they

* preserve state,
* cannot be "just called",
* return an object that you always need to remember structure of, and
* can be nested in a composition of any complexity.

Hint: composition of generators is really handy when you make a dialogue engine.

A basic example:

```javascript
export function* conversation({user, message, ...data} = {}) {
  const answer = yield 'Hello, what is going on?';
  yield 'Okay.';
}
```

The workflow is simple and linear.

Now, time to create some branches:

```javascript
export function* conversation({user, message, ...data} = {}) {
  const answer = yield 'Hello, what is going on?';
  
  if (answer.match(/nothing/i)) {
    yield 'Okay.';
  } else {
    yield* inquire({user, message});
  }
}

function* inquire({user, message} = {}) {
  const details = yield 'Okay, let’s go deeper. What _exactly_ is going on?';
  const happy = yield 'Are you happy about that?';
}
```

A branch appears. After the first `yield`, the workflow is transferred to
`inquire` generator function. Still, it's comprehensible. But we can go deeper.

```javascript
export function* conversation({user, message, ...data} = {}) {
  const answer = yield 'Hello, what is going on?';
  
  if (answer.match(/nothing/i)) {
    yield 'Okay.';
  } else {
    yield* inquire({user, message});
  }
}

function* inquire({user, message} = {}) {
  const details = yield 'Okay, let’s go deeper. What _exactly_ is going on?';
  const happy = yield 'Are you happy about that?';

  if (details.match(/lmao|laugh|chill/i) && happy.match(/y[ae]+s*/i)) {
    yield* congrat({user, message});
  } else {
    yield* listen({user, message});
  }
}

function* congrat({user, message} = {}) {
  yield 'MAN U AWESOME';
}

function* listen({user, message} = {}) {
  const details = yield `Okay, ${user}, you said ${message}. Are you sad then?`;
  console.log(user, message, details);
}
```

And this will work. The whole workflow will be as branchy and have as many
layers, as there are conditional statements _and_ generator compositions.
Insane.

Quite useful when it's necessary to preserve state, whatever the number of
layers of state is, and stay asynchronous at the same time. As long as the
process is running, the state will be preserved. Values are assigned to
variables from `yield` statements through `next` function parameters, so there's
no need in an additional state management engine.
