# Dispatching an action on enter per route

Let's pretend you have a react-router setup:

```javascript
import React from 'react';
import {
  Route,
  Router,
  IndexRoute,
  browserHistory
} from 'react-router';
import App from 'containers/App';
import Home from 'containers/Home';
import Login from 'containers/Login';
import Dashboard from 'containers/Dashboard';

export default (
  <Router history={browserHistory}>
    <Route path="/" component={App}>
      <IndexRoute component={Home}>
      <Route path="login" component={Login}>
      <Route path="dashboard" component={Dashboard}>
    </Route>
  </Router>
);
```

Now, you need to check if the user has logged in per every route. Like this:

```javascript
import React from 'react';
import {
  Route,
  Router,
  IndexRoute,
  browserHistory
} from 'react-router';
import axios from 'axios';
import App from 'containers/App';
import Home from 'containers/Home';
import Login from 'containers/Login';
import Dashboard from 'containers/Dashboard';

const requireLogin = requireLogin = (nextState, replace, next) => {
  axios.get('/authentication/check').then(({isAuthenticated}) => {
    if (!isAuthenticated) {
      replace('/login');
    } else {
      // <- I want to dispatch an action here.
    }

    next();
  }).catch(() => next());
};

const redirectIfLoggedIn = (nextState, replace, next) => {
  axios.get('/authentication/check').then(({isAuthenticated}) => {
    if (isAuthenticated) {
      replace('/dashboard');
    }

    next();
  }).catch(() => next());
};

export default (
  <Router history={browserHistory}>
    <Route path="/" component={App} onEnter={requireLogin}>
      <IndexRoute component={Home}>
      <Route path="login" component={Login} onEnter={redirectIfLoggedIn}>
      <Route path="dashboard" component={Dashboard} onEnter={requireLogin}>
    </Route>
  </Router>
);
```

Remember that Store is an object that has `dispatch` property? This property is a function and it accepts objects that represent actions. So, to dispatch an action, you need to get the Store from wherever it's been created. Let's say, you have a file in ./src/reducers/index.js which is like this:

```javascript
import {
  createStore,
  combineReducers
} from 'redux';
import userReducers from './users.js';

export default createStore(combineReducers({
  users: userReducers
}));
```

Now you can import the result of `createStore` function call in any other module:

```javascript
import React from 'react';
import {
  Route,
  Router,
  IndexRoute,
  browserHistory
} from 'react-router';
import axios from 'axios';
import store from './reducers'; // <- here we import the Store
import App from 'containers/App';
import Home from 'containers/Home';
import Login from 'containers/Login';
import Dashboard from 'containers/Dashboard';

const requireLogin = (nextState, replace, next) => {
  axios.get('/authentication/check').then(({isAuthenticated}) => {
    if (!isAuthenticated) {
      replace('/login');
    } else {
      store.dispatch({
        type: 'CONFIRM_USER_AUTHENTICATED' // <- replace it with the action you need
      });
    }

    next();
  }).catch(() => next());
};

const redirectIfLoggedIn = (nextState, replace, next) => {
  axios.get('/authentication/check').then(({isAuthenticated}) => {
    if (isAuthenticated) {
      replace('/dashboard');
    }

    next();
  }).catch(() => next());
};

export default (
  <Router history={browserHistory}>
    <Route path="/" component={App} onEnter={requireLogin}>
      <IndexRoute component={Home}>
      <Route path="login" component={Login} onEnter={redirectIfLoggedIn}>
      <Route path="dashboard" component={Dashboard} onEnter={requireLogin}>
    </Route>
  </Router>
);
```

Now the action of type `CONFIRM_USER_AUTHENTICATED` will be dispatched every time a user enters any route with `onEnter={requireLogin}` prop, if `isAuthenticated` is truthy.
