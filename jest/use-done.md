# Use done

The `it`'s and `test`'s second argument is a function where the test is performed. To test Promises, use `done`, first argument to this function that marks the test as done.

For example, this one will never work:

```javascript
describe('test', () => {
  it('should wait until a Promise resolves', () => {
    const p = new Promise((resolve) => {
      setTimeout(() => resolve(false), 3000); // <- resolve with a falsy value
    });

    p.then((result) => {
      expect(result).toBeTruthy(); // <- gets executed after Jest marks it passed = not tested
    });
  });
});
```

While this one will:

```javascript
describe('test', () => {
  it('should wait until a Promise resolves', (done) => { // <- first argument is a callback function to mark the test as done
    const p = new Promise((resolve) => {
      setTimeout(() => resolve(false), 1000); // <- resolve with a falsy value
    });

    p.then((result) => {
      expect(result).toBeTruthy(); // <- gets executed after Jest marks it passed = not tested
      done();
    });
  });
});
```
