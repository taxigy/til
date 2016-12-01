# Populate initial values of the form based on an API response

Let's say there's a pretty big form, and you want to perform a
call to a remote API once the component has been mounted, fetch
some data and put it into fields. To do that, you need

- [redux-thunk](https://github.com/gaearon/redux-thunk) to create
  an asynchronous action creator function,
- form name,
- field name, or better said, path to the field, and of course
- API endpoint URL.

You can use
[fetch](https://developer.mozilla.org/en-US/docs/Web/API/Fetch_API) to query a remote API or use [axios](https://github.com/mzabriskie/axios), but let's pretend the request is performed by `requestApi` function. So, here's the action creator function to put data into fields:

```javascript
export function fetchRecord(id) {
  return async dispatch => {
    try {
      const response = await requestApi();
      const targetValues = {
        'person.first_name': response.data.person.name,
        'person.best_friends': response.data.person.friends.map(e => e.name),
        'person.interests[0].date_started': response.data.person.interests[0].started,
      };

      Object.keys[targetValues].map(key => {
        dispatch({
          type: 'redux-form/CHANGE',
          meta: {
            form: 'person-form',
            field: key
          },
          payload: targetValues[key]
        });
      });
    } catch (error) {
      // do something in case of unsuccessful API call
    }
  }
}
```

Here, `targetValues` is a plain-object that represent a typical
data structure, a map, and it maps values from API response to
fields of an instance of redux-form.

Two downsides here:

- only works with redux-form@6, and
- can easily break if action type `redux-form/CHANGE` is replaced
  with something else in future releases of redux-form.

Generally, this code would make other people question the level
of your professionalism, and when it happens, there surely has to
be a better way. Finding it is TODO.
