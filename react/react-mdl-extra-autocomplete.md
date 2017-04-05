# AutoComplete from React MDL Extra

In [react-mdl-extra](https://github.com/HriBB/react-mdl-extra), there is the [AutoComplete](https://github.com/HriBB/react-mdl-extra/blob/master/src/AutoComplete/AutoComplete.js) component. It implements, obviously, auto-complete text field, where you can type and you can select items. If you put it into Redux loop, it looks like a working solution:

```javascript
import React from 'react';
import { connect } from 'react-redux';
import { AutoComplete } from 'react-mdl-extra';
import { searchThingsBySubstring } from './actions/thingsActions';

function ThingsSearch(props) {
  return (
    <AutoComplete
      label="Find things"
      items={props.foundThings}
      valueIndex="id"
      dataIndex="name"
      onChange={props.searchThingsBySubstring}
    />
  )
}

export default connect(state => ({
  foundThings: state.things.found,
}), {
  searchThingsBySubstring,
})(ThingsSearch);
```

Having each thing represented by an object like

```javascript
{
  id: 1,
  name: 'A little thing',
}
```

and `foundThings` as an array of these objects, you can see it working.

However!

There is an interesting problem: the function passed to `onChange` will be executed on both _typing_ and _item select_. It makes it a bit uncomfortable when you want to distinguish between the two:

```javascript
<AutoComplete
  label="Find things"
  items={props.foundThings}
  valueIndex="id"
  dataIndex="name"
  onChange={props.searchThingsBySubstring}
  onItemSelectOrHowDoIDoIt={props.iHaveNoIdea}
/>
```

Guess what? There is no separate prop that would accept a function to execute on item select! Nope. See the sources: [one for item select ("item click")](https://github.com/HriBB/react-mdl-extra/blob/master/src/AutoComplete/AutoComplete.js#L47) and [another for type ("field change")](https://github.com/HriBB/react-mdl-extra/blob/master/src/AutoComplete/AutoComplete.js#L55). Both call `onChange`.

The only way to distinguish is to keep the type of `id` non-string. This way, you'll be able to do something like

```javascript
function onChangeAutoComplete(value, onType, onSelect) {
  if (typeof value === 'string') {
    // it is presumably typing
    onType(value);
  } else if (typeof value === 'number') {
    // it is presumably item select
    onSelect(value);
  }
}

function ThingsSearch(props) {
  return (
    <AutoComplete
      label="Find things"
      items={props.foundThings}
      valueIndex="id"
      dataIndex="name"
      onChange={value => onChangeAutoComplete(value, props.searchThingsBySubstring, props.goToThing)}
    />
  )
}
```

It's a hack but it should work in most cases. If your identifiers are unordered random strings, try to use another property instead. For example, a date property. Otherwise, you can always submit a PR to react-mdl-extra.
