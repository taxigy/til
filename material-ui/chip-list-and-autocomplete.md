# I want a component that would be an auto-complete with chips. How?

In [material-ui](https://material-ui.com), there are two separate components
that implement different parts of it:

- [`Chip`](http://www.material-ui.com/#/components/chip), a rounded rectangle
  that represents an entity, one of many.
- [`AutoComplete`](http://www.material-ui.com/#/components/auto-complete), an
  enhanced text field that accepts `dataSource` prop and drops down a menu with
  items based on user input and filter function.

It's easy to combine them into an input that would show chips but also would
show the whole list of items, that haven't been selected, below. Because it's
half-done by [`ChipInput`](https://www.npmjs.com/package/material-ui-chip-input)
component.

So,

```javascript
import React, { Component } from 'react';
import ChipInput from 'material-ui-chip-input';
import {
  Chip
} from 'material-ui';
import styles from './styles.css';

export default class ChipSuggest extends Component {
  constructor(...args) {
    super(...args);
    this.state = {
      all: ['abc', 'def', 'xyz'],
      selected: []
    };
  }

  handleAddChip(value) {
    const {
      selected
    } = this.state;

    this.setState({
      selected: selected.some(e => e === value) ? selected : [...selected, value]
    });
  }

  handleRemoveChip(value) {
    this.setState({
      selected: this.state.selected.filter(e => e !== value)
    });
  }

  handleAddUnselectedChip(event) {
    this.setState({
      selected: [...this.state.selected, event.target.innerText] // <- relying on innerText directly is unstable, use Array#find to deal with it
    });
  }

  render() {
    const {
      all,
      selected
    } = this.state;

    return (
      <div className={styles.chipSuggest}>
        <ChipInput
          style={{ width: '100%' }}
          id="regions"
          name="regions"
          hintText="Click to search and add countries and regions"
          floatingLabelText="Countries"
          dataSource={allCountries.map(v => v.name)}
          value={props.regions}
          onRequestAdd={::this.handleAddChip}
          onRequestDelete={::this.handleRemoveChip} />
        <div className={styles.unselectedChips}>
          {all.filter(e => !selected.some(s => s === e)).map(item => (
            <Chip
              style={{ marginRight: 8, marginBottom: 8 }}
              key={item}
              children={item}
              onTouchTap={::this.handleAddUnselectedChip} />
          ))}
        </div>
      </div>
    );
  }
}
```

Don't forget to add some vertical margins, and it's done.

Improve as you like. Pass any data source, be it a Map, a plain-object, an
array, a Set, whatever. You control display and adding/removing items!
