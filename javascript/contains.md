# Contains

A typical pattern in Javascript is checking if a certain value is an element of
a collection. Today, we're going to do some weird stuff (just like a while ago
with [zeroes and ones](./any-number-into-0-or-1.md)) and check if a string is an
element of an array, or something else, of strings.

## An amateur Javascript way

No collection, just hardcoded "big 'if'":

```javascript
if (x === 'banana' || x === 'papaya') {
  // ...
}
```

Extending this means extending the "if" statement. Good thing we have
[Prettier](https://github.com/prettier/prettier) to format it nicely.

## A reliable and somewhat maintainable way

Use [ESLint](https://github.com/eslint/eslint) and use constant so that whenever
there's a typo, ESLint throws
[no-unused-vars](https://eslint.org/docs/rules/no-unused-vars):

```javascript
const BANANA = 'banana';
const PAPAYA = 'papaya';

if (x === BANANA || x === PAPAYA) {
  // ...
}
```

A little verbose but more maintainable.

## A C language way bit mask

There isn't any better thing in this life than to see good old C code, is there?

```javascript
const BANANA = 0x1;
const PAPAYA = 0x2;

if (x & (BANANA | PAPAYA)) {
  // ...
}
```

A good way to confuse ESLint when you have
[no-bitwise](https://eslint.org/docs/rules/no-bitwise) rule turned on.

## A universal, super ancient Javascript way

Now we're getting closer to collections. Collections are good because you can
always extend one and make the change git-happy: just one line changed, awesome!
A naïve approach to this is to use arrays and good old
[`Array.prototype.indexOf`](https://developer.mozilla.org/en/docs/Web/JavaScript/Reference/Global_Objects/Array/indexOf):

```javascript
const FRUIT = ['banana', 'papaya'];

if (FRUIT.indexOf(x) >= 0) {
  // ...
}
```

Remember: `indexOf` returns -1 if the there's no matching value.

## A universal, quite modern Javascript way

In ES6, we have
[`Array.prototype.includes`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/includes).
Let's use it!

```javascript
const FRUIT = ['banana', 'papaya'];

if (FRUIT.includes(x)) {
  // ...
}
```

Saved a few bytes, made it incompatible with Opera and IE.

## A universal, screwed up a bit Javascript way

Here, we forget (or maybe don't know) that `Array.prototype.includes` exists. So
instead, we use
[`Array.prototype.find`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/find):

```javascript
const FRUIT = ['banana', 'papaya'];

if (FRUIT.find(e => e === x)) {
  // ...
}
```

Didn't save a few bytes, still incompatible with IE.

## A O(1) way with a plain-object

Plain-objects are the most commonly used data structure in Javascript that
allows O(1)-ish lookup by key. Using it as an enumerator may feel weird until
you see this beauty:

```javascript
const FRUIT = {
  banana: true,
  papaya: true,
};

if (x in FRUIT) {
  // ...
}
```

Lovely, right? And completely not misleading or confusing to anyone who's going
to maintain this code in future.

## A decent ES6 way using Set

We have an amazing native data structure in ES6,
[Set](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Set).
An instance of Set stores a collection of unique values and provides `has`
method to check if any value is in this set:

```javascript
const FRUIT = new Set(['banana', 'papaya']);

if (FRUIT.has(x)) {
  // ...
}
```

Be careful with reference types though.

## A regular expression way, of course!

This list wouldn't be complete without a regular expression. Here's one:

```javascript
const FRUIT = /^(banana|papaya)$/;

if (x.match(FRUIT)) {
  // ...
}
```

Add `i` to make it case-insensitive.

---

Remember: there's no better code than no code, and when it's impossible, the
second best alternative is code that anyone familiar with basic concepts of data
structures may read, understand, adjust and avoid introducing disproportionate
number of bugs into it. Code responsibly!
