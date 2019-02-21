# Distinguish a predicate type from a generic function type

Consider a case when you filter through a collection of numbers:

```javascript
const xs = [1, 2, 3, 4, 5, 6];
const isEven = x => x % 2 === 0;
const filter = pred => xs => xs.filter(pred);

filter(isEven)(xs);
```

In Flow, this could be typed like:

```javascript
const xs = [1, 2, 3, 4, 5, 6];
const isEven = (x: number): boolean => x % 2 === 0;
const filter = (pred: Function) => (xs: Array<number>) => xs.filter(pred);

filter(isEven)(xs);
```

However, it's better to type the predicate function the way it should be: a function that always returns a boolean:

```javascript
type Predicate = any => boolean;

const xs = [1, 2, 3, 4, 5, 6];
const isEven = (x: number): boolean => x % 2 === 0;
const filter = (pred: Predicate) => (xs: Array<number>) => xs.filter(pred);

filter(isEven)(xs);
```

---

There's some weird issue with this code:

```javascript
filter(x => -x)(xs);
```

Check it out [on StackOverflow](https://stackoverflow.com/questions/54817277/false-positive-predicate-function-type-check-in-flow-for-a-function-that-returns).
