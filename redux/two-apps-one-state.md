# Share state between two isolated React apps

Consider a case when you're migrating an old Django or Symfony app to an API-first, single page application app. You want to keep views and gradually introduce React, one piece of the UI at a time. Like this:

```html
<body>
  <main>
    <nav id="navigation"></nav>
    <section id="hero"></section>
    <section id="userinfo"></section>
  </main>
</body>
```

In this case, you can mount different React apps to different DOM nodes, perhaps even defer the loading until a certain route is being visited. The entry point of the Javascript bundle that mounts the apps would look Like

```javascript
import React from 'react';
import { render } from 'react-dom';
import Navigation from './components/Navigation';
import Hero from './components/Hero';
import UserInfo from './components/UserInfo';

render(<Navigation />, document.getElementById('navigation'));
render(<Hero />, document.getElementById('hero'));
render(<UserInfo />, document.getElementById('userinfo'));
```

The three apps can by design share the same Redux store:

```javascript
import React from 'react';
import { render } from 'react-dom';
import { Provider } from 'react-redux';
import Navigation from './components/Navigation';
import Hero from './components/Hero';
import UserInfo from './components/UserInfo';
import store from './store';

const useStore = Component => (
  <Provider store={store}>
    <Component />
  </Provider>
);

render(useStore(Navigation), document.getElementById('navigation'));
render(useStore(Hero), document.getElementById('hero'));
render(useStore(UserInfo), document.getElementById('userinfo'));
```

Done. The three React apps are mounted to different DOM nodes that don't intersect with each other, yet they share the same state via a Redux store and can use it at its fullest.

For proof of concept, see [the codesandbox](https://codesandbox.io/s/2xy634926y).
