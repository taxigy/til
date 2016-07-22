# Using react-komposer with publication and subscriptions in Meteor

The [react-komposer](https://github.com/kadirahq/react-komposer) is a pretty popular library among Meteor developers who user React on the front-end. It is recommended by [Discover Meteor](https://www.discovermeteor.com/blog/data-loading-react/), a pretty reliable source of knowledge about latest Meteor stuff.

The most common problem is what comes after doing

```bash
$ meteor remove insecure autopublish
```

because now you need to deal with pub/sub all on your own. There are a few surprises:

1. Meteor's publications and subscriptions are really hard to understand because they involve collection cursors.
1. While `Meteor.publish` is synchronous, `Meteor.subscribe` is not, and it's not evident what to do in this case.
1. Derived from the previous point, simply using `MyCollection.find()` won't work React component because the component won't know it should update after the subscription is ready.

The latter is fixed perfectly with react-komposer because it uses Meteor's native Tracker and injects new props on every autorun event. It's like using

```javascript
Meteor.autorun(() => {
  // how do I make the component re-render from within this scope?!
});
```

but bound to React component:

```javascript
import React, { Component } from 'react';
import { composeWithTracker } from 'react-komposer';
import MyCollection from '/import/collections/MyCollection';

class MyComponent extends Component {
  render() {
    const {
      MyCollection
    } = this.props;

    return (
      <div className="container">
        {MyCollection.find().fetch().map(item => (
          <div className="row">
            <div className="col-xs-12">{item}</div>
          </div>
        ))}
      </div>
    );
  }
}

export default composeWithTracker((props, onData) => {
  Meteor.subscribe('myCollection', {
    onStop: (error) => console.log('The subscription has stopped:', error),
    onReady: () => onData(null, {
      MyCollection
    })
  })
})(MyComponent);
```

The `onData` is what will inject new props and cause the component's update. The `onReady` property of the subscription options object takes a function that will run when the subscription is ready, which may take a few `Meteor.autorun` runs prior to that.
