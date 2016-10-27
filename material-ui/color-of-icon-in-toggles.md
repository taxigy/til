# Color of the icon in toggle components

In material-ui, there is a few toggle components, `Checkbox` and
`RadioButton` among them. If you're all in with customization of your
project UI based on material-ui, you might find it difficult to change the
color of that round element of radio button or square one of checkbox.
Neither `color` nor `background` color would work. The trick is that the icon
is an SVG element so it has `fill` property. And it's precisely what is
responsible for its color! So,

```javascript
import React, { PropTypes } from 'react';
import { Checkbox } from 'material-ui';

export default function (props) {
  const styles = {
    iconStyle: {
      fill: 'blue', // <- your color here
      marginRight: 10,
    }
  };

  return (
    <Checkbox
      {...props}
      {...styles}
    />
  );
}
```
