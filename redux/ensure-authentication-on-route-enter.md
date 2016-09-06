# How to check if the user is authenticated on every route enter

In [react-router](https://github.com/reactjs/react-router), every `Route` component can receive `onEnter` prop, the value a function that may be used to dispatch actions and, generally, redirect. A typical use case is to check if the user is authenticated, by checking whether an access token is present in `localStorage` and hasn't yet expired. With [redux-auth-wrapper](https://github.com/mjrussell/redux-auth-wrapper), a higher-order component, it's easier than before:

```javascript
import React from 'react';
import {
  Router,
  Route,
  IndexRoute,
  browserHistory
} from 'react-router';
import { UserAuthWrapper } from 'redux-auth-wrapper';
import {
  App,
  Home,
  Login,
  Dashboard
} from './containers';

const UserIsAuthenticated = UserAuthWrapper({
  authSelector: () => localStorage,
  predicate: () => localStorage.getItem('token') && new Date() > localStorage.getItem('token_expire'),
  failureRedirectPath: '/login',
  allowRedirectBack: false
});

export default (
  <Router history={browserHistory}>
    <Route path="/" component={App}>
      <IndexRoute component={Home} />
      <Route path="/login" component={Login} />
      <Route
        path="/dashboard"
        component={UserIsAuthenticated(Dashboard)} />
      </Route>
    </Route>
  </Router>
);
```

See that for `Route`, responsible for path `/dashboard`, is receiving a component wrapped into result of calling `UserAuthWrapper`. The component, whenever mounted, will now execute the `UserIsAuthenticated` function, ending up with unauthenticated users getting redirected to `/login` path, with all proper react-router's actions dispatched. Clean!
