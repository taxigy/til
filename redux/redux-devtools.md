# The easiest way to enable Redux DevTools

Firebase and Redux Form included!

```javascript
import {
  applyMiddleware,
  combineReducers,
  createStore
} from 'redux';
import thunk from 'redux-thunk';
import {
  reducer as form
} from 'redux-form';
import reducers from './reducers'; // replace with actual reducers
import firebaseConnection from './firebase-connection'; // replace with actual Firebase connection config

function createFirebaseMiddleware(firebase) {
  return store => next => action => typeof action.firebase === 'function' ?
    action.firebase(firebase, store.dispatch, store.getState) :
    next(action);
}

const reducers = combineReducers({
  ...reducers,
  form
});

const store = createStore(
  reducers,
  window.devToolsExtension && window.devToolsExtension(), // <- enables DevTools in Chrome
  applyMiddleware(
    thunk,
    createFirebaseMiddleware(firebaseConnection)
  )
);

export default store;
```
