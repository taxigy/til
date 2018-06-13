# From [0, 1) to [n, m)

A classic problem: you have a random number generator that outputs floating-point values from 0 to 1, excluding 1. And you need these values to uniformly reflect into a different range from n to m, excluding m. What are you going to do?

This is a proportion, so the reflection function would look like this:

```javascript
const random = Math.random; // a function that returns a value in the range [0, 1)
const reflection = (n, m) => () => random() * (m - n) + n;
```

If it has to return an integer but retain uniformity, just cut the decimal part:

```javascript
const reflection = (n, m) => () => random() * (m - n) + n | 0;
```
