# Server-side asynchronous operations in Meteor 1.3

Had a weird error on a mentorship session. As it turned out,
until today, I have never had any trouble with running
asynchronous operations on the server side of Meteor application.
But today I finally got stuck into that. The code was

```javascript
Accounts.onCreateUser(function (options, user) {
  Meteor.users.update({
    _id: Meteor.userId()
  }, {
    $set: {
      'profile.whatevs': Math.random() // more meaningful stuff here, actually, but anyway
    }
  });
});
```

Looking quite alright, isn't it? But it didn't work.

Wrapping the whole piece into try/catch statement helped to find
out that it's necessary to use
[fibers](https://www.npmjs.com/package/fibers). So, first,
install it with

```bash
$ npm install --save fibers@^1.0
```

and then import it and use it:

```javascript
import Fiber from 'fibers';

Accounts.onCreateUser(function (options, user) {
  const fiber = Fiber(function () {
    Meteor.users.update({
      _id: Meteor.userId()
    }, {
      $set: {
        'profile.whatevs': Math.random()
      }
    });
  });

  fiber.run();
});
```

But that didn't work again!

Debugging step by step has shown that `Meteor.userId()` execution
fails. Despite the type of `Meteor.userId` is function, calling
it would throw an error. Not really close to what was expected.
But since `user` variable, passed as the second argument into the
`Accounts.onCreateUser` callback function, contains exactly the
same information as `Meteor.user()` would, it wasn't really a big
deal to fix. So:

```javascript
import Fiber from 'fibers';

Accounts.onCreateUser(function (options, user) {
  const fiber = Fiber(function () {
    Meteor.users.update({
      _id: user._id
    }, {
      $set: {
        'profile.whatevs': Math.random()
      }
    });
  });

  fiber.run();
});
```

Done. Now it works, be it a normal piece of code or a callback to
Promise, that doesn't matter. It works with a little delay caused
by the way fibers work, which can be annoying, but figuring out
how to remove it is another topic.

----

There's [discussion about replacing fibers with
async/await](https://github.com/meteor/meteor/issues/5460), not
really productive.
