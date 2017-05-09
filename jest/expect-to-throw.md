# Expect to throw

If there is a function

```javascript
function fn() {
  throw new Error('I always throw.');
}
```

then a simple corresponding Jest test to check if the function
throws an error should have signature

```javascript
expect(fn).toThrow();

// THIS WILL FAIL
expect(fn()).toThrow();

// THIS WILL PASS
expect(fn).toThrow();
```

The value passed to `expect` should be a function, otherwise it
will throw error out of scope of `expect`.
