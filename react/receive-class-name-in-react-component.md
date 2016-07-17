# Receive a className in your own React component

Subj. Seriously, don't be an ass. Let other web developers use your
component with their CSS properties. It's simple

```javascript
import React, { Component} from 'react';
import styles from './SuperAwesomeComponent.scss';

export default ({className = '', children}) => (
  <div className={`${styles.superAwesomeComponent} ${className}`}>
    {children}
  </div>
);
```

or, in a longer way,

```javascript
import React, { Component } from 'react';
import styles from './SuperAwesomeComponent.scss';

export default class SuperAwesomeComponent extends Component {
  render() {
    const {
      className = '',
      children
    } = this.props;

    return (
      <div className={`${styles.superAwesomeComponent} ${className}`}>
        {children}
      </div>
    );
  }
}
```

Really, do this. Everybody likes using their own classes on top of
predefined component styles to make their shit work.
