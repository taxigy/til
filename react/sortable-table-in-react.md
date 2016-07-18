# Sortable table in React

A table with columns a user can sort by is a classic UI problem. There's [react-bootstrap-table](https://github.com/AllenFang/react-bootstrap-table) that brings a bunch of dependencies along the way, but it doesn't have to be so. React, as opposed to jQuery, is everything but magic. And a table with sortable columns isn't magic, it's just a reflection of a sorted collection. Let's implement it in a form of a stateful component.

```javascript
import React, { Component } from 'react';

export default Table extends Component {
  render() {
    const {
      columns = [],
      items = []
    } = this.props;

    return (
      <table>
        <thead>
          <tr>
            {columns.map((column, index) => (
              <th
                key={index}>
                {column}
              </th>
            ))}
          </tr>
        </thead>
        <tbody>
          {items.map((item, index) => (
            <tr
              key={index}>
              {columns.map((column, index) => (
                <td
                  key={index}>
                  {item[column]}
                </td>
              ))}
            </tr>
          ))}
        </tbody>
      </table>
    );
  }
}
```

Right now, the table receives two props: `columns` and `items`, `columns` prop is an array of strings, each string a column name, while `items` is an array of objects, each having property keys named exactly as column names, like

```javascript
const columns = [
  'First Name',
  'Last Name',
  'Balance'
];
const items = [{
  'First Name': 'John',
  'Last Name': 'Doe',
  'Balance': 1000
}, {
  'First Name': 'Jane',
  'Last Name': 'Doe',
  'Balance': -1000
}];
```

To make it sort, we need to

1. Store the key we're going to sort the collection by in component's own state.
1. Store order: ascending or descending.
1. Sort initial data by selected property in selected order.
1. Never touch DOM, never mutate props, never mutate collection, always sort it in `render` function.

```javascript
import React, { Component } from 'react';

export default Table extends Component {
  constructor() {
    super();
    this.state = {};
  }

  setSorting(column) {
    const {
      sortBy = column,
      order = 1
    } = this.state;

    this.setState({
      sortBy: column,
      order: sortBy === column ? -order : 1 // <- check if user clicked the same column once again, so the order has to flip, otherwise the order is ascending by default
    });
  }

  render() {
    const {
      columns = [],
      items = []
    } = this.props;
    const {
      sortBy = columns[0],
      order = 1
    } = this.state;
    const sortedItems = items.sort((a, b) => a[sortBy] > b[sortBy] ? order : -order);

    return (
      <table>
        <thead>
          <tr>
            {columns.map((column, index) => (
              <th
                key={index}
                onClick={() => ::this.setSorting(column)}>
                {column}
              </th>
            ))}
          </tr>
        </thead>
        <tbody>
          {sortedItems.map((item, index) => (
            <tr
              key={index}>
              {columns.map((column, index) => (
                <td
                  key={index}>
                  {item[column]}
                </td>
              ))}
            </tr>
          ))}
        </tbody>
      </table>
    );
  }
}
```

What happens here is:

1. Every `th` element is now assigned with `onClick` event callback.
1. Every `th` element's `onClick` is called with the `index` of the `column`.
1. The function `setSorting` assigned to `onClick` event is called with the only argument, `column`, which is the value (not index!) of an element in `columns` array.
1. The function `setSorting` changes component state and sets two properties: `sortBy` and `order`.
1. The `sortBy` property is equal to one of the values of `columns` array, based on what was last column a user clicked. For example, it is "First Name" or "Last Name", etc.
1. The `order` property is assigned with either 1 or -1, reflecting ascending and descending order.
1. In `render` function, data is rendered from `sortedItems`, as opposed to just `items` as it was before.
1. The value of `sortedItems` is an array of `items` sorted by key that is equal to the value of `sortBy`.
1. Simply mapping through this sorted array, we can see the table sorted.

There are a few things to think over.

First, the `Array#sort` function. It accepts a function that is applied to every pair of elements of the original array, e.g. it takes two arguments. When this function returns 1, it means that the element passed as first argument is greater than the element passed as second argument. If the function returns -1, it means vice versa, second is greater than first. What we do here is simply returning `order`, which is either 1 or -1, or unary negation of `order`, which is, in turn, either -1 or 1.

Second, how `setSorting` works. It checks if the new key to sort by is the same as the old one. If the previous and the new keys are equal, the order will be flipped, which technically means the value of `order` will be negated. Since its value is always 1 or -1, every operation of negation simply alternates between the two. The value of `order` is used as return value of `Array#sort` function and it makes the function return an array either in ascending or descending sorting.

See:

```javascript
const columns = [
  'First Name',
  'Last Name',
  'Balance'
];
const items = [{
  'First Name': 'John',
  'Last Name': 'Doe',
  'Balance': 1000
}, {
  'First Name': 'Jane',
  'Last Name': 'Doe',
  'Balance': -1000
}];
let order = 1;
let sortBy = columns[0];

console.log(items.sort((a, b) => a[sortBy] > b[sortBy] ? order : -order));
// [{"First Name":"Jane","Last Name":"Doe","Balance":-1000},{"First Name":"John","Last Name":"Doe","Balance":1000}]

order = -1; // <- simply flipping the order
console.log(items.sort((a, b) => a[sortBy] > b[sortBy] ? order : -order));
// [{"First Name":"John","Last Name":"Doe","Balance":1000},{"First Name":"Jane","Last Name":"Doe","Balance":-1000}]

sortBy = columns[2]; // <- now sorting by balance, while order remains -1 = descending
console.log(items.sort((a, b) => a[sortBy] > b[sortBy] ? order : -order));
// [{"First Name":"John","Last Name":"Doe","Balance":1000},{"First Name":"Jane","Last Name":"Doe","Balance":-1000}]

order = 1; // <- flipping the order
console.log(items.sort((a, b) => a[sortBy] > b[sortBy] ? order : -order));
// [{"First Name":"Jane","Last Name":"Doe","Balance":-1000},{"First Name":"John","Last Name":"Doe","Balance":1000}]
```

Open Node REPL and play around with various values of `order`, `sortBy`, `columns` and `items` and see how `Array#sort` works.
