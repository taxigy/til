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
const a = x => Number(Boolean(x))
```

The underlying idea is simple: first convert the number into
`true` or `false`, then evaluate them back into Number.

## Nasty double-casting into Boolean and then into Number

Like this:

```javascript
const a = x => +!!x
```

Pretty same as previous one, saves you bytes and kills other
programmer's time. Never do it this way.

## Even nastier double-casting into Boolean and double-unary "not"

This way:

```javascript
const a = x => ~~!!x
```

First converts into a Boolean that would evaluate into 0 or 1 (`false` or `true`, respectively), then bitwise NOT twice. It works but it questions your faith in humanity.

## Subtract from 1

If you subtract 0 or 1 from 1, you'll get 1 or 0. Easy!

```javascript
const a = x => 1 - !x
```

All mathematicians be giving you approval nod.

## Divide by self or 0

If you divide a number by itself, it has to be 1. Exactly, except when you
divide 0 by itself in Javascript, you get NaN. So, simply return the result of
division of number by itself _or_ 0:

```
const a = x => x / x || 0;
```

Not very much arithmetic but still works.
