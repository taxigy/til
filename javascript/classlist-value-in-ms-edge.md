# The value of classList in Microsoft Edge

A common API for [class
list](https://developer.mozilla.org/en-US/docs/Web/API/Element/classList)
provides `value` getter that is not supported by MS Edge. The code like this

```javascript
document.body.classList.value;
```

will fail in Edge. To make it work in all browsers, use `toString`:

```javascript
document.body.classList.toString();
```

It's archaic, but it works identically (returns a string that represents the
value of "class" attribute) and doesn't break in Edge.
