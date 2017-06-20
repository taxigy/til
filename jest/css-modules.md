# CSS modules

By default, Jest would try to parse a CSS module as JS code. If you have

```javascript
import React from 'react';
import styles from './Home.css';

export default function Home(props) {
  return (
    <div className={styles.home}>HOME</div>
  );
}
```

and Home.css is like

```css
@import '../some/variables.css';

.home {
}
```

then Jest test will definitely fail with

```
Unexpected token (1:0)
  > 1 | @import "../some/variables.css";
```

so you need to configure Jest to mock CSS files in a special way. Under "jest"
section in package.json, add

```json
"moduleNameMapper": {
  "\\.css$": "<rootDir>/.jestStyleMock.js"
},
```

and in ./.jestStyleMock.js, export an empty object:

```javascript
module.exports = {};
```

And it will work then.

That's pretty much what the official Jest documentation recommends, see
https://facebook.github.io/jest/docs/webpack.html#handling-static-assets.
