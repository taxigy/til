# Using async/await in Redux actions

Imagine an action that fetches data from a server and changes the app state
then. With [axios](https://github.com/mzabriskie/axios) it would look like

```javascript
export function anAction() {
  const resource = '/resource';
  const promise = axios.get(resource);

  promise.then((result) => {
    return {
      type: 'AN_ACTION',
      ...result.data
    }
  });
}
```

The problem is in `return` statement. The `anAction` function returns nothing, a
lambda that is passed into `then` method as the only parameter does. An action
creator has to return a plain-object with at least a single property, `type`.
Currently, it doesn't.

Okay,

So there's a workaround for that, or, more precisely, a middleware nicely packed
into a [redux-thunk](https://github.com/gaearon/redux-thunk) package, that
allows an action creator to return a function that accepts a callback function
as the first argument, that, in turn, dispatches the app state change:

```javascript
export function anAction() {
  return (dispatch) => {
    const resource = '/resource';
    const promise = axios.get(resource);

    promise.then((result) => {
      dispatch({
        type: 'AN_ACTION',
        ...result.data
      });
    });
  };
}
```

It's nice and works asynchronously, but the code can be wrapped into async/await
to run synchronously (which is doubtfully better, but still useful in some cases), like:

```javascript
export function anAction() {
  return async (dispatch) => {
    try {
      const resource = '/resource';
      const result = await axios.get(resource);

      dispatch({
        type: 'AN_ACTION',
        ...result.data
      });
    } catch (error) {
      console.error(`Error fetching ${resource}`, error);
      dispatch({
        type: 'AN_ACTION' // or, better, 'FAILED_ACTION' or something like that
      });
    }
  };
}
```

Now, there's no callbacks. The code is written as if it was synchronous and, in
fact, it does mimic synchronous execution using an infinite loop that breaks as
soon as response from server has been retrieved.

But an action can be called multiple times, and we want to make sure that only
one request is running at every moment. To do so, we can add `loading` property
of the state object with default value `false` (set in the initial state inside
reducer declaration) and manipulate this property when performing async calls.
The second argument of the function accepted by redux-thunk allows accessing the
app state, so we can reach that `loading` property:

```javascript
export function anAction() {
  return async (dispatch, getState) => {
    const {
      someReducer: {
        loading
      }
    } = getState();

    if (loading) {
      console.log('Already loading something. Cannot proceed.');
      return;
    }

    dispatch({
      type: 'PERFORMING_AN_ACTION',
      loading: true // or better, let reducer take care of it
    });

    try {
      const resource = '/resource';
      const result = await axios.get(resource);

      dispatch({
        type: 'PERFORMED_AN_ACTION',
        loading: false,
        ...result.data
      });
    } catch (error) {
      console.error(`Error fetching ${resource}`, error);
      dispatch({
        type: 'FAILED_AN_ACTION',
        loading: false
      });
    }
  };
}
```

Now, the whole action is secure from multiple calls. Make every action creator
respect the `loading` app state property, and the whole HTTP API consummation
will become more reliable.
