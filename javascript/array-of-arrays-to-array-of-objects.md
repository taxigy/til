# Convert an array of arrays to an array of objects

Given this CSV-like input:

```javascript
const csv = [
  ["Column 1", "Column 2", "Column 3"],
  [1, 2, 3],
  [4, 5, 6],
  [7, 8, 9],
];
```

how do we get output like:

```javascript
const objs = [
  { "Column 1": 1, "Column 2": 2, "Column 3": 3 },
  { "Column 1": 4, "Column 2": 5, "Column 3": 6 },
  { "Column 1": 7, "Column 2": 8, "Column 3": 9 },
];
```

?

This way:

```javascript
const csvToObjs = data => {
  //          ^ the name of the function could be better though
  const cols = data[0];
  const rows = data.slice(1);

  return rows.map(row =>
    Object.assign(
      {},
      ...cols.map((col, index) => ({
        [col]: row[index],
      })),
    ),
  );
};
```
