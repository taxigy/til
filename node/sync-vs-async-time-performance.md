# Synchronous and asynchronous functions: time performance

It's a very stupid empirical test that I found useful simply because when a Node server is loaded with 10k+ requests per minute, every little part is becoming critical. So, here's the experiment:

```javascript
let CYCLES = 1000 * 1000;

function sf() {
  return Promise.resolve(true);
}

async function af() {
  return Promise.resolve(true);
}

// Test
function testSync() {
  console.time('Sync');

  for (let i = 0; i < CYCLES; i++) {
    sf();
  }

  console.timeEnd('Sync');
}

function testAsync() {
  console.time('Async');
  
  for (let i = 0; i < CYCLES; i++) {
    af();
  }
  
  console.timeEnd('Async');  
}
```

Because `Promise.resolve` doesn't require a function, and the latter does not require instantiation time and memory, it works pretty fast. Still, on a million cycles, the difference between _equivalent_ functions `sf` and `af` is interesting:

```
> testSync()
Sync: 149.407ms
undefined
> testSync()
Sync: 140.346ms
undefined
> testSync()
Sync: 142.676ms
undefined
> testAsync()
Async: 394.417ms
undefined
> testAsync()
Async: 393.648ms
undefined
> testAsync()
Async: 386.898ms
undefined
```

Now, what about a real Promise that takes a function that resolves it at some point in future, even though it happens immediately?

```javascript
let CYCLES = 1000 * 1000;

function sfc() {
  return new Promise(resolve => resolve(true));
}

async function afc() {
  return new Promise(resolve => resolve(true));
}

// Test
function testSyncConstructor() {
  console.time('Sync with constructor');

  for (let i = 0; i < CYCLES; i++) {
    sfc();
  }

  console.timeEnd('Sync with constructor');
}

function testAsyncConstructor() {
  console.time('Async with constructor');

  for (let i = 0; i < CYCLES; i++) {
    afc();
  }

  console.timeEnd('Async with constructor');
}
```

The result:

```
> testSyncConstructor()
Sync with constructor: 445.782ms
undefined
> testSyncConstructor()
Sync with constructor: 628.467ms
undefined
> testSyncConstructor()
Sync with constructor: 476.739ms
undefined
> testAsyncConstructor()
Async with constructor: 762.107ms
undefined
> testAsyncConstructor()
Async with constructor: 753.442ms
undefined
> testAsyncConstructor()
Async with constructor: 762.238ms
undefined
```

By adding `async` keyword to a function that returns a Promise anyway and does not have `await` statement, we can increase its execution time by almost 50%. Totally a thing to always take care of.
