# Query against an array of objects

Let's pretend the data looks like

```json
{
  "profile": {
    "devices": [{
      "uuid": "123",
      "platform": "iOS"
    }, {
      "uuid": "456",
      "platform": "Android"
    }]
  }
}
```

In Mongo 3.2, there's a handy modifier, `$elemMatch` for that:

```javascript
db.whatevs.find({
  'profile.devices': {
    $elemMatch: {
      uuid: {
        $in: ['123', '456']
      }
    }
  }
});
```

It checks if any of the elements inside `profile.devices` array is valid against
the nested query, which, in turn, checks if `uuis` is either "123" or "456".

## Links

* Mentioned in a 3 years old StackOverflow question,
  [see](http://stackoverflow.com/questions/10298001/mongodb-query-in-array-of-object/36000367#36000367).
