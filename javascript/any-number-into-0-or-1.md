# Convert any number into 0 or 1

I first faced this challenge in [Conway's Game of
Life](https://github.com/taxigy/conways-game-of-life). The
challenge was to convert any Number into a Number that is either
0 or 1, normalize any number into 1 if it's not 0, otherwise keep
it equal to 0. In Javascript, there's a bunch of different
methods to do so. The solution may also be handy to solve other
problems, however I can only guess what these problems are.

## Type cast to Boolean, then to Number

One that [Airbnb would
propose](https://github.com/airbnb/javascript#type-casting--coercion):

```javascript
const a = x => Number(Boolean(x));
```

The underlying idea is simple: first convert the number into
`true` or `false`, then evaluate them back into Number.

## Nasty double-casting into Boolean and then into Number

Like this:

```javascript
const a = x => +!!x;
```

Pretty same as previous one, saves you bytes and kills other
programmer's time. Never do it this way.

## Even nastier double-casting into Boolean and double-unary "not"

This way:

```javascript
const a = x => ~~!!x;
```

First converts into a Boolean that would evaluate into 0 or 1 (`false` or `true`, respectively), then bitwise NOT twice. It works but it questions your faith in humanity.

## Subtract from 1

If you subtract 0 or 1 from 1, you'll get 1 or 0. Easy!

```javascript
const a = x => 1 - !x;
```

All mathematicians be giving you approval nod.

## Divide by self, or 0

If you divide a number by itself, it has to be 1. Exactly, except when you
divide 0 by itself in Javascript, you get NaN. So, simply return the result of
division of number by itself _or_ 0:

```javascript
const a = x => x / x || 0;
```

Not very much arithmetic but still works.

## Divide by self AND 1

Now here comes a little less evident way to extract 0 or 1 from any number:

```javascript
const a = x => x / x & 1;
```

The trick is that NaN AND 1 returns 0, which is precisely what we're
seeking for.

## XORing 1 with NOT-number

The idea is simple: XOR the negation of boolean evaluated into number (which
should be 0 or 1):

```javascript
const a = x => 1 ^ !x;
```

Now this is quite a weirdness!

## React developers would love it: number && 1

This is the core of understanding how Javascript evaluates logical
operators: number AND 1:

```javascript
const a = x => x && 1;
```

When the left part of the logical AND expression is truthy, the
right part is returned as is; otherwise the left part is returned
as is. So, if the number is 0, it is falsy, and therefore is
returned; otherwise the right part returned, and since it is
constant 1, this is what we end up with: 0 or 1.

This approach is used widely in React development. (and I
silently hate it)

## Compare with zero

Now, you thought I forgot that classic elementary programming school
approach every rookie programmer uses? No way!

```javascript
const a = x => x == 0 ? x : 1;
```

Feel old yet?

## Length of a filtered array

Everybody knows that you can filter an array these days. Why not
use it to reflect any number into set of 0 and 1?

```javascript
const a = x => [x].filter(e => e).length;
```

The trick is that the length of the array is guaranteed to be 1,
so it may be filtered into either array of 0 or 1 elements. 0
itself is falsy, which means that it will be filtered out and not
included into the output of `filter` method. Then, `length` is
simply an indicator of presence of truthy values inside the
resulting array.

## Regex to the rescue!

Tired of weirdness yet? If not, here's the example that involves
regular expressions. To be honest, not very impressive. It only
uses the Javascript's intrinsic type casting and literal regex
declaration, but anyway, it's another way to do the job:

```javascript
const a = x => 1 - /^0$/.test(x);
```

What happens here is that `x` is type-casted into a string, and
the string is either "0" or something else, so the test evaluates
into true or false correspondingly.

---

More:

- [Interesting discussion](http://stackoverflow.com/questions/41863620/what-does-mean-in-javascript) on use of "Latin letter 'Dental Click'" Unicode character in form `|ǀ|`, where `ǀ` is most likely undefined.
- Other weird stuff: [checking if something is an element of a collection of somethings](./contains.md).
