# Checking Array Intersection in Mongo

The easiest way to find collection items by intersection of sets is simply using
`$in` for intersection and `$nin` for non-inclusion:

```javascript
Collection.find({
  someArray: {
    $in: [1, 2, 3]
  }
});
```

This way, all collection items will be found that have `someArray` property that
is an array and contains any combination of 1, 2 and 3.

To find only those items that contain neither of 1, 2 nor 3, it's `$nin`:

```javascript
Collection.find({
  someArray: {
    $nin: [1, 2, 3]
  }
});
```

Super handy.
