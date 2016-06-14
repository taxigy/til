# Using AVA for automated testing

The [AVA test runner](https://github.com/avajs/ava) boasts a set
of features that go smoothly with your modern ES6 Babel-powered
full-stack JS project. You can easily run tasks that test against
generator functions, or use generators implicitly, or use
async/await, and it's awesome. The config, though, is tricky.

In `package.json`, add the section related to AVA:

```json
"ava": {
  "require": ["babel-register", "babel-polyfill"],
  "babel": "inherit",
  "files": [
    "test/*.js"
  ],
  "source": [
    "**/*.js",
    "!build/**/*"
  ],
  "tap": true
}
```

along with `dependencies`, `devDependencies`, etc.

The `babel-polyfill` will be imported before the first test is
run, so it's really handy because, to test async/await, you
really need that polyfill, at least with Babel 6.

The Babel configuration is inherited, so along with
`dependencies`, `devDependencies` and `ava` properties of the
setup JSON object, there is `babel` property expected (or
`./.babelrc` file that resembles one). That one can be as simple
as

```json
"babel": {
  "stage": 0
}
```

If you store tests in separate files, there's no need to hold a
single entry point. AVA will simply iterate through them and run
sequentially. Every test file has to import default from `ava`
and declare some tests:

```javascript
import test from 'ava';
import _ from 'lodash';
import mongo from 'mongo-async-wrapper';

const {
  DATABASE_URL = 'http://localhost:27017'
} = process.env;

test('all the people are active', async test => {
  try {
    const db = await mongo.MongoClient.connect(DATABASE_URL);
    const people = await db.collection('people').find().toArray();
  
    test.truthy(_.every(people, person => person.active));
  } catch (error) {
    test.fail(error);
  }
});
```

Thoughts after having composed a bunch of tests:

- Automated testing encourages reactivity and immutability, which
  is awesome.
- Never a bad idea to have separate tests that check solely pure
  functions, and tests that check functions that depend on global
  state (which can represent a state stored in the database,
  too).
- The more functions are pure, the better.
- Separating tests per topic or domain is better than per ticket
  or Trello card or whatever related to management.
- Using lodash is way more productive than AVA's `regex`,
  `throws`, `deepEqual`, etc, mostly because relying on the same
  logic in the code and in the tests is a good idea.
- The lesser dependency on external state (aka database), the
  better.
- Separate tests that assert third-party APIs will help avoid
  some API misusage and occasionally discover API outages (like,
  [Slack
  API](http://www.recode.net/2016/6/10/11905384/slack-outage-reaction-twitter)).
