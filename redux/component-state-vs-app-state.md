# Component State vs Application State

Application state (aka store) is great when it comes to data that's needed to be
stored across multiple states in the app run-time. For example, a wizard that
depends on current location and so is controlled by an upper-level Router
component. Each step of this wizard is a separate component, so the only way to
preserve everything across the steps is to store data into the store.

Sometimes, though, it's better to store some data on a component level. For
example, state of a drop-down, or any state that only reflects into visual
representation (drawer open, popups shown, etc.).

So, to have both, it's simply

```javascript
import React, { Component } from 'react';
import { connect } from 'react-redux';

@connect(state => ({
  ...state
}))
export default class Whatever extends Component {
  contructor() {
    super();
    this.state = {};
  }

  handleSomething(key, value) {
    this.setState({
      key: value
    });
  }

  render() {
    return (
      <div>
        ...
      </div>
    );
  }
}
```

However, every time such a case bubbles up, it's a signal that something can be
spinned off into an uncontrolled component that encapsulates its state and
allows parent component to change it via "events", in other words, props that
are functions that change component state in context of the uncontrolled
component.
