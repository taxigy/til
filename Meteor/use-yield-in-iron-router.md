# Use yield in Iron Router

Iron Router has nice layout system. You can create multiple layout for different
cases, and it will work on a lower level, as compared to responsive web design
techniques. For example, one layout for mobile and one for browser:

```html
<template name="defaultLayout">
  {{#if isMobile}}
    <div class="container">
      {{> mobileHeader}}
      {{> mobileBody}}
      {{> mobileFooter}}
    </div>
  {{else}}
    <div class="container">
      {{> browserHeader}}
      {{> browserSidebar}}
      {{> browserBody}}
      {{> browserFooter}}
    </div>
  {{/if}}
</template>
```

The problem is, what if you need to render a certain template on a place of
`mobileBody`? As usual, in Meteor, it's possible in several ways. One of them
would be to determine behavior inside the `mobileBody` template. But the
idiomatic way is to use `yield` statements to place different helpers on place
of them. And there are few points to keep in mind:

* There may be as many `yield` statements inside a template as you like. None,
  one, many, so
* Each of them has to have a unique identifier that would ensure that certain
  template is rendered on place of certain `yield` statement. More than that,
* You can render one template in many places (not really sure it's useful, but
  still), and this way you use same identifier for two or more `yield`
  statements, and
* There may be _default_ `yield` statement.

To name a `yield`, i.e. to give it an identifier, use any string or helper value
as the first parameter of `yield` template:

```html
<template name="defaultLayout">
  <div class="container">
    <div class="part">
      <!-- This is the default yield -->
      {{> yield}}
    </div>
    <div class="part">
      <!-- This yield has name "something" -->
      {{> yield "something"}}
    </div>
  </div>
</template>
```

So, the corresponding client-side code would look like

```javascript
Router.route('/', function () {
  this.layout('defaultLayout');
  this.render('defaultTemplate');
  this.render('somethingTemplate', {
    to: 'something'
  });
});
```

The `this.layout` part defines the layout that will be rendered at first. This
layout is what will be injected into `body` DOM element. So, the `defaultLayout`
is taken. Next, there is a bunch of `this.render` expressions, each taking the
name of a template as the first argument.

The `this.render` expression can have the second parameter, a so called "options
object". Among other options, there's `to` option which defines what part of the
layout will the template be put into. So,

```javascript
this.render('somethingTemplate', {
  to: 'something'
});
```

will pick `somethingTemplate`, find a `yield` statement with the first parameter
matching the value of `to` option, and render this template there. Which
effectively means that `{{> yield "something"}}` will be used for that.

So, the difference between

```html
{{> somethingTemplate}}
```

and

```html
{{> yield "something"}}
```

is that the former is not controlled by the router. The layout will always end
up with `somethingTemplate` rendered on that place. Which sometimes may save
lines of code, but sometimes does not give enough control. This is where the
latter approach should be used.

----

This tip is based on a 100-minutes live coding session when I _just didn't
consider_ looking at the layout configuration and ended up being close to
desperation and madness trying to debug a route that worked perfectly, when it
came to logging into console, but did not actually render the target template.
Advice: don't neglect checking even evident and "what could go wrong" things.
