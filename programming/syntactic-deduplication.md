# Removing syntactic duplication

In programmin, we have two different types of duplication: syntactic and semantic.

Syntactic duplication is like this:

```javascript
const add = (a, b) => a + b;
const sub = (a, b) => a - b;
```

Semantic duplication is like

```javascript
const add = (a, b) => a + b;
const add2 = a => a + 2;
```

In this case, `add2` is semantically identical to `add` with exception for a very small amount of logic (but not amount of code!), therefore cool guys would never implement `add2`. But as cases grow in complexity, it's becoming easier to fall into that semantic trap and code similar things twice.

What programmers love doing more than hunting semantic duplication is removing syntactic duplication. Think this:

```javascript
const map = (array, f) => array.map(f);
const reduce = (array, f, start = array[0]) => array.reduce(f, start);
```

Let's pretend you have 12 different places in code where you need to map and then reduce. In fact, you may need just 3 of them to start implementing this:

```javascript
const mapreduce = (array, map, reduce) => array.map(map).reduce(reduce);
```

This example looks harmless because it only takes line, but now there are

- an extra layer of abstraction: a new function every newbie to your codebase has to learn what it does,
- potential point of code bloat when, say, you suddenly need to now only map and reduce but also to filter, or maybe tomorrow you need to first reduce and then map, who knows, and of course
- potential (and very likely) source of errors when somebody else uses your function in a different way than expected, say, they're pretending the function first reduces and then maps, although you are completely sure it doesn't work this way.

If you are one of those who are completely certain that nobody else would ever reach their codebase, it's fine. Do whatever you want. But if there's even little chance that somebody else joins your project, please consider doing important stuff. Believe me, removing syntactic duplication isn't one.

When you remove syntactic duplication, you do -1 point to the complexity of your project and then +1 point because now you've added an extra abstraction. There's no duplication anymore but there's one more concept a person needs to learn and know well to improve your codebase and not break it.

----

One of the craziest cases of attempt to remove duplication was, guess what?, automatic form generation. It's like, you have some data that represents your form's metadata (fields, etc.) and you generate a form out of it, like

```javascript
import React, { Component } from 'react';

const form = {
  title: 'Contact',
  className: 'form',
  onSubmit: (event) => {
    event.preventDefault()
    // do smth
  },
  fields: [{
    label: 'First Name',
    name: 'firstname'
    type: 'text',
    className: 'form-control'
  }, {
    label: 'Last Name',
    name: 'lastname'
    type: 'text',
    className: 'form-control'
  }]
};

const Form = () => (
  <div className={form.className}>
    <h2>{form.title}</h2>
    <form
      onSubmit={form.onSubmit}>
      {form.fields.map(field => (
        <label htmlFor={field.name}>{field.label}</label>
        <input
          className={field.className}
          id={field.name}
          name={field.name}
          type={field.type} />
      ))}
    </form>
  </div>
);
```

Looks decent, right? Now imagine that you need to

- add and display non-`input` things like `select` or even custom date picker,
- show fields in a different way, say, some fields go two in a grid row and some take the whole grid row alone,
- change form's title based on whatever it can be,
- show/hide fields based on nobody could ever know what happens, or maybe
- listen to each field's change via `onChange`

What really happens here is the code is about 2x longer than it could be if there was no tempting desire to remove that duplication.

The most annoying thing here is that syntactic deduplication is sometimes considered a mark of skill, as if the programmer would be so experienced they would know for sure it's gonna work. But the truth is, it's more like a mark of incompetence. It's short-sighted. Seriously, stop doing that.

K TNX.
