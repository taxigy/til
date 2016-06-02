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

## Destructuring in for/of

Easy:

```javascript
const bodies = {
  small: {
    head: 'small',
    feet: 'small'
  },
  mid: {
    head: 'big',
    feet: 'small'
  },
  big: {
    head: 'big',
    feet: 'big'
  }
};

for (let {head, feet} of _.map(bodies, ({body, key}) => ({body: {head, feet}, key}))) {
  console.log(`${key}: ${head}, ${feet}`);
}
```

And it's a perfect way to shoot in the foot, just take a look at the longest
line. Well, it's a little worse than could be because of lodash's `map`. But
anyway. Never do that in your codebase.

# Get value by a random key and store into a constant

What if you have username stored as a string and also have an object where
usernames are keys and values are users' favourite food? Easily:

```javascript
const favs = {
  hans: 'wurst',
  pete: 'bier'
};
const username = 'hans';
const {
  [username]: hansLikes = '(none)'
} = favs;

console.log(hansLikes); // -> wurst
```

Purely functional, perfectly readable, considerably unreliable.
