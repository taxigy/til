# The handleActions of redux-actions

Typical reducer declaration is a single "export default" function with a monster
`switch` statement, something like

```javascript
import {
  START_DOING_SOMETHING
} from '../constants/something';

const initialState = {
  doingSomething: false
};

export default function (state = initialState, action = {}) {
  switch (action.type) {
    case START_DOING_SOMETHING:
      return Object.assign({}, state, {
        doingSomething: true
      });
  }
}
```

Not only it's unreadable and hard to maintain both in code style and in code
consistency but it also provides a bunch of ways to introduce errors that won't
be easy to debug. What if I miss a `break` between two actions? What if I
overwrite the entire state?

The `redux-actions` package provides a super handy function that wraps actions
into a plain-object of those:

```javascript
import {
  handleActions
} from 'redux-actions';

const initialState = {
  doingSomething: false
};

export default handleActions({
  START_DOING_SOMETHING: (state, action) => ({
    ...state,
    doingSomething: true
  })
}, initialState);
```

Great with object spread and arrow functions.

Also, composing reducers this way totally eliminates the need in constants. Some
people prefer having them to make lives of other programmers easier (harder)
through requiring explicit constants declaration and having ReferenceError
thrown otherwise. It's possible to use constants, too, just add square brackets:

```javascript
import {
  handleActions
} from 'redux-actions';
import {
  START_DOING_SOMETHING
} from '../constants/something';

const initialState = {
  doingSomething: false
};

export default handleActions({
  [START_DOING_SOMETHING]: (state, action) => ({
    ...state,
    doingSomething: true
  })
}, initialState);
```

I see that constants are used almost on every project, in every codebase I'm
introduced to. Not sure why. React devtools and Redux devtools' `DebugPanel` are
okay with just strings provided as action types, not to say constants are just
exactly the same strings themselves. Controlling typos? Throwing ReferenceError
each time an unknown action type is used in code?

With just strings it's as simple as

```javascript
export function anAction() {
  return {
    type: 'START_DOING_SOMETHING'
  };
}
```

Just works.
