# From \[0, 1\) to \[n, m\)

A classic problem: you have a random number generator that outputs floating-point values from 0 to 1, excluding 1. And you need these values to uniformly reflect into a different range from n to m, excluding m. What are you going to do?

This is a proportion, so a function that reflects a value from one range into a corresponding value within another is called _linear interpolation_ and it would look like this:

```javascript
const lerp = (n, m) => x => x * (m - n) + n;
console.log(lerp(0, 100)(0.5)); // outputs 50
```

If it has to return an integer but retain uniformity, just cut the decimal part:

```javascript
const lerp = (n, m) => x => x * (m - n) + n | 0;
```

Updates:

- a function that reflects range \[0, 1\) to \[n, m\) is commonly referred to as _linear interpolation_, "lerp"; an inverse function that maps range \[n, m\) onto \[0, 1\) is therefore called ["inverse linear interpolation"](https://twitter.com/mattdesl/status/1031629105266126849).
