# Why Meteor?

A very biased dichotomy of why do and why don't choose Meteor for
your new project. I'd personally say, Meteor is a top 1
MVP-friendly framework which takes care of at least half all the
problems every web-app MVP goes through, from signing up users
to storing data securely. So, building an MVP to test an idea?
Spend extra cash on a higher-tier server instead of implementing
the same set of features once again (or, again and again, if you
launch multiple MVPs). Meteor is like a standard practice for
what comes next after scaffolding a brand new web application.

## Why do?

In around 1000 lines of code, you'll likely be having a
full-blown app, highly customized and written from scratch, with
no frameworkds, etc.  Meteor has almost any plug for every
classic hole in it. User accounts? Yes.  Serving data
specifically for every authenticated user? Right out of the box.
A bunch of cool libs that do magic and make your CRM or
multi-user chat app work within 100 lines of custom code? Boom!,
done.

Meteor has database integration built into it. It's Mongo that
can be really considered a part of Meteor. If you go with Mongo,
things are super easy. Fetching data from a collection and
reactively updating whatever is dependent on it in the UI doesn't
even require extra code — literally.

Meteor code looks like it's synchronous. It works mostly that
way. No callbacks and "where did I declare this?" situations.
Fetching from a collection, setting a value on server are, or can
be turned into, synchronous operations. The optimistic and
reactive UI is comfortable with it and only updates when it's
done, not getting blocked at all.

A good portion of code is running on the browser and on the
server instance simultaneously. Having it so, it's possible to
control both UI and server-side computations from withing a
single function. Pretty handy.

ES6 out of the box. Just do

```bash
$ meteor add ecmascript
```

if, for some reason, it doesn't work, and it will.

Combines with React in a blink of an eye. Just use react-mount
lib, and what happens after is a walk in a park. Mount the React
part and use whatever lib you like, organize code as you like,
and such. You can even use Redux (my fave) right withing a Meteor
app.

## Why don't?

One word: globals. In Meteor 1.3, the problem has gotten some
improvement, since the _imports_ thing has been introduced, and
generally, Meteor 1.3 apps look a lot like Webpack-powered ES6
apps.

CPU load is sometimes too high. Meteor apps are, kind of, hard to
scale. Even a "Hello, World" kind of Meteor app requires at least
512 MB RAM to operate at a reasonable speed. It's hardly a
disadvantage, though, since almost every modern full-stack
Javascript framework has the same "feature".

Only has reasonable support for Mongo, not any other database.
True old school coders don't consider Mongo a mature database, so
it's really hard to convince them the opposite. It does have
problems, especially with Mongo, when it comes to 10K+ items in a
single collection, since Meteor, by default, tries to fetch all
at once. Multiply by number of concurrently running instances,
and it is hell.
