# What is faster: a class or a plain-object?

Consider a class:

```javascript
class A {
  constructor(a, b, c) {
    this.a = a;
    this.b = b;
    this.c = c;
  }
}
```

Then a pure function with equivalent purpose that operates on plain-objects would be

```javascript
const a = (a, b, c) => ({ a, b, c });
```

Consider a collection that we push these objects into. Which of the two objects is less CPU-consuming?

Given the code

```javascript
const ITERATIONS = 10000000;
const msg = `class+new, ${ITERATIONS}x`;

console.time(msg);

const xs = [];

class A {
  constructor( a, b, c ) {
    this.a = a;
    this.b = b;
    this.c = c;
  }
}

for (let i = 0; i < ITERATIONS; i++) {
  const x = new A(i, i * 2, i ** 2)
  xs.push(x);
}

console.timeEnd(msg);
```

If we run it on an average low-tier container, we get results like

```bash
$ for x in {1..21}; do node a.js; done
class+new, 10000000x: 4510.280ms
class+new, 10000000x: 4167.457ms
class+new, 10000000x: 4069.037ms
class+new, 10000000x: 4167.504ms
class+new, 10000000x: 4186.628ms
class+new, 10000000x: 4176.723ms
class+new, 10000000x: 4219.366ms
class+new, 10000000x: 4114.468ms
class+new, 10000000x: 4163.553ms
class+new, 10000000x: 4190.509ms
class+new, 10000000x: 4053.873ms
class+new, 10000000x: 4109.081ms
class+new, 10000000x: 4071.563ms
class+new, 10000000x: 4135.235ms
class+new, 10000000x: 4123.231ms
class+new, 10000000x: 4043.924ms
class+new, 10000000x: 3974.904ms
class+new, 10000000x: 4143.446ms
class+new, 10000000x: 4114.738ms
class+new, 10000000x: 4164.355ms
class+new, 10000000x: 4251.598ms
```

Let's adjust the code so that it runs on plain-objects:

```javascript
const ITERATIONS = 10000000;
const msg = `plain-obj, ${ITERATIONS}x`;

console.time(msg);

const xs = [];

const a = ({ a, b, c }) => ({ a, b, c });

for (let i = 0; i < ITERATIONS; i++) {
  const x = a({ a: i, b: i * 2, c: i ** 2})
  xs.push(x);
}

console.timeEnd(msg);
```

And run the benchmark again:

```bash
$ for x in {1..21}; do node b.js; done
plain-obj, 10000000x: 1603.891ms
plain-obj, 10000000x: 1579.470ms
plain-obj, 10000000x: 1603.102ms
plain-obj, 10000000x: 1679.208ms
plain-obj, 10000000x: 2408.277ms
plain-obj, 10000000x: 1959.455ms
plain-obj, 10000000x: 2442.324ms
plain-obj, 10000000x: 1979.892ms
plain-obj, 10000000x: 2605.148ms
plain-obj, 10000000x: 2006.987ms
plain-obj, 10000000x: 2780.927ms
plain-obj, 10000000x: 2061.610ms
plain-obj, 10000000x: 1725.224ms
plain-obj, 10000000x: 1610.541ms
plain-obj, 10000000x: 1606.645ms
plain-obj, 10000000x: 1557.968ms
plain-obj, 10000000x: 1948.077ms
plain-obj, 10000000x: 1943.276ms
plain-obj, 10000000x: 1989.754ms
plain-obj, 10000000x: 2152.500ms
plain-obj, 10000000x: 1584.155ms
```

Almost twice as fast.

Maybe this is engine-specific, maybe on engines other than V8 results would be different. It also might be that the real performance may not maintain the ratio in real-life code where nothing is as isolated and leaks are everywhere. But so far the second option wins by both the amount of code and performance.
