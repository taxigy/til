# Supplying parameters to helpers in Spacebars

In Spacebars, each template and helper is a function that is evaluated by the
templating engine. If you need to supply a parameter to a helper, think of it as
of a function.

For example, if you need to check if a helper's value is equal to a certain
string, the bad way to do that would be

```html
<templte name="whatever">
  {{someHelper == 'abcd'}}
</template>
```

because Spacebars expect function parameters, not a JS expession. So a good way
to do that would be

```html
<templte name="whatever">
  {{someHelper 'abcd'}}
</template>
```

and make the helper accept a single parameter:

```javascript
Template.whatever.helpers({
  someHelper(string, template) {
    return template.someInternalValue === string;
  }
});
```

Then, there's a way to supply a template instance with parameters:

```html
<template name="whatever">
  {{> anotherTemplate someKey="someString" anotherKey=anotherHelper}}
</template>
```

Now, Spacebars engine is okay with that the value supplied to `someKey`
parameter is simply a string. What it expects as a value of `anotherKey` is a
function, aka helper. So the helper is something like

```javascript
Template.whatever.helpers({
  anotherHelper() {
    return 'anotherString';
  }
});
```

What if we want to pass a value of evaluation of the helper with some parameters
into `anotherKey`? Like,

```
{{> anotherTemplate someKey="someString" anotherKey=anotherHelper('Hello')}}
```

or

```
{{> anotherTemplate someKey="someString" anotherKey={{anotherHelper 'Hello'}} }}
```

will this work? Nope.

The only way to pass a result of expression into `anotherKey` is to wrap
everything into `{{#with}}...{{/with}}`, such that

```html
<template name="whatever">
  {{#with 'Hello'}}
    {{> anotherTemplate someKey="someString" anotherKey=anotherHelper}}
  {{/with}}
</template>
```

and then refer to newly created context from `anotherHelper`:

```javascript
Template.whatever.helpers({
  anotherHelper() {
    return this; // this === • in Spacebars' {{#with •}} block, hence 'Hello'
  }
});
```

This may cause some trouble if you already rely on current context, so make sure
you use "go to upper context" syntax (double dot).
