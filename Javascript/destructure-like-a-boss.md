# Destructure Like a Boss (not any longer)

There's no better thing in ES6 for a destructuring-aligned programmer and
nothing worse for those who will maintain their code. Consider this:

```javascript
const purchase = {
  item: {
    id: 100500,
    title: 'Nimbus 3000',
    price: {
      initial: '500',
      discounted: '315',
      discountedOn: 'Tue Apr 05 2016 23:26:49 GMT+0400 (SAMT)',
      discountedDue: {
        coupons: [{
          code: 'ABCXYZ',
          amountOff: 150
        }, {
          code: 'WHATEVS',
          percentageOff: 10
        }]
      }
    }
  }
};
```

Now, let's pretend we only need the title, the discounted price and all the
coupons out of this item, but there's already a `title` variable, so it should
be `itemTitle` instead. Also, `discounted` is not a nice name for a variable
since it's not evident what it represents, so let it be `discountedPrice`. Now, it's

```javascript
const {
  item: {
    title: itemTitle
  },
  price: {
    discounted: discountedPrice,
    discountedDue: {
      coupons
    }
  }
} = purchase;
```

Now let's make it type-safe to avoid TypeError being thrown when a typical
"cannot ready property • of undefined" happens:

```javascript
const {
  item: {
    title: itemTitle = '(no title)'
  } = {},
  price: {
    discounted: discountedPrice = 0,
    discountedDue: {
      coupons = []
    } = {}
  } = {}
} = purchase || {};
```

Never do that in your codebase.
