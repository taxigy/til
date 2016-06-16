# The conflict between Lodash and Underscore

In Meteor 1.3, there's `./imports` folder that works pretty close
to the way Webpack does, so there's no more pain caused by
globals. However, in older codebases, where the whole piece of
client code is placed into `./client` folder, the situation is
different.

Meteor grabs all the content of `./client` folder, bundles it
into a single file in kinda smart way, and the whole idea is that
you don't have imports and instead have globals.

Also, Meteor goes with Underscore (which gets outdated in older
codebases, mostly unnoticed) and so programmers tend to rely on
its features.

But [Lodash](http://lodash.com/docs) has way more interesting and
handy features. So I wanted them in the file I was working on:

```javascript
import React from 'react';
import _ from lodash;

SomeComponent = React.createClass({ // pay attention: it's global, not exported!
  render() {
    const {
      someState
    } = this.state;
    const {
      someProp,
      anotherProp
    } = this.props;
    const veryImportantValue = _.result(someProp, 'path.to.whatevs');
    const anotherImportantValue = _.includes(anotherProp, veryImportantValue);

    return (
      <div className="someComponent">
        ...
      </div>
    );
  }
});
```

See that import line? It will break the whole client side. Once
you import lodash, it will replace underscore way down the
bundle, and there's no way to predict or prevent it effectively.

I tried

```javascript
import {
  result,
  includes
} from 'lodash';

// ...
```

and it broke the global scope again.

I tried

```javascript
// ...

  render() {
    const _ = require('lodash');

    // ...
  }
```

and it broke the global scope again.

And now I don't have an idea how to introduce Lodash to existing
codebase without breaking dependency on Underscore elsewhere in
the client code (tons of it over there!). So, TBD.
