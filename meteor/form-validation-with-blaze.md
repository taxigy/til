# Form validation in Meteor with Blaze

Since Meteor 1.3, Blaze is now just one of the options for the
view layer in a Meteor app. Still, it's a good option when you
want to launch quickly and the app is simple enough to keep
everyhing in global scope (and Blaze encourages it).

For the form validation with Blaze, there's a lot of possible
techniques, but the approach is pretty much generic:

1. Keep the state of the form: combination of all input values at
   current point of time.
1. Track changes of every input in the form to tell the user if
   it's valid, in real time.
1. Put something into DOM that would actually deliver the
   validation message to the user.
1. Connect validation message with form state.
1. Prevent form's "submit" event until it's perfectly valid.

## Keeping the state of the form

The form itself, represented by DOM, is stateful, because DOM is
inherently stateful. It means that you don't need to keep values
of inputs somewhere else: you can always access them via `value`
property of their element. So let's pretend it's okay to do so
(even if sometimes it's not), because Blaze is perfectly
comfortable with this.

## Tracking changes in fields

To track changes in fields, by design, you need as many different
event listeners as there are fields. The code may not be that
verbose, for you may combine validation functions for similar
fields (say, email and confirmation), but the general idea is you
can't simply perform validation without knowing what's happening
in every single field.

So, let's say, the form is a template:

```html
<template name="loginForm">
  <form>
    <input type="text" id="username">
    <input type="password" id="password">
    <button type="submit">Log in</button>
  </form>
</template>
```

The form contains just two inputs, `#username` and `#password`.
Now you can track changes in them:

```javascript
Template.loginForm.events({
  'change #username': (event) => {
    console.log(event.target.value);
  }
});
```

Every template has `events` property that accepts a plain-object
where keys are jQury-style event declarations and values are
functions of a few arguments, the first one is instance of [Event](https://developer.mozilla.org/en/docs/Web/API/Event).

In case of username, every time you change the field value,
there's a number of different DOM events being dispatched: key
down, key up, and change among of them. So `change #username`
literally binds your function to every "change" event of the DOM
element with id "username".

Every instance of Event object has "target" property which is
reference to DOM element that caused the event. Since, in case of
`#username`, the element is an input field, it has `value`
property that has exactly the same value as the field _after_ the
event.

So, if you type "banana" in the username field, you'll see 6
lines on the browser console:

```
b
ba
ban
bana
banan
banana
```

To validate the value, check it against some logic, presumably a
function. For example, for the username, let's say it must be
longer than 3 characters and shorter than 30, and may only
contain letters and digits:

```javascript
Template.loginForm.events({
  'change #username': (event) => {
    const validateField = value => /[a-zA-Z0-9]{3,30}/.test(value);

    console.log(validateField(value));
  }
});
```

Now, on the console, just as you type "banana", you should see on
the console:

```
false
false
true
true
true
true
```

Which means that "b" and "ba" are invalid usernames because they
are too short.

Do the same for the password, but let the password be no shorter
than 8 characters and contain letters, digits, and some special
characters:

```javascript
Template.loginForm.events({
  'change #username': (event) => {
    const validateField = value => /[a-zA-Z0-9]{3,30}/.test(value);

    console.log(validateField(value));
  },
  'change #password': (event) => {
    const validateField = value => /[a-zA-Z0-9-_=+\s]{8,}/.test(value);

    console.log(validateField(value));
  }
});
```

## Showing the message via DOM

The most convenient way to show validation messages to the user
is via DOM element. To show or hide a DOM element conditionally,
in a Blaze template, you can use a helper:

```html
<template name="loginForm">
  <form>
    <input type="text" id="username">
    {{#unless usernameIsValid}}
      <p>username is invalid</p>
    {{/unless}}
    <input type="password" id="password">
    {{#unless passwordIsValid}}
      <p>password is invalid</p>
    {{/unless}}
    <button type="submit">Log in</button>
  </form>
</template>
```

Here, `usernameIsValid` and `passwordIsValid` are values that are
passed to the template via helpers:

```javascript
Template.helloForm.helpers({
  usernameIsValid: function () {
    return true;
  },
  passwordIsValid: function () {
    return true;
  }
});
```

Since helpers always return true, you'll never see the message
when username or password values are invalid. To make it work,
let's keep the state of the form within the template instance and
set values of these helpers per every change, so that the
template gets updated reactively.

## Template state

The easiest option Meteor promotes for storing template state is
via ReactiveDict:

```javascript
Template.loginForm.onCreated(function () {
  this.formValidationStatus = new ReactiveDict('loginForm');
});
```

and then

```javascript
Template.loginForm.events({
  'change #username': (event, instance) => {
    const validateField = value => /[a-zA-Z0-9]{3,30}/.test(value);

    instance.formValidationStatus.set('usernameIsValid', validateField(value));
  },
  'change #password': (event, instance) => {
    const validateField = value => /[a-zA-Z0-9-_=+\s]{8,}/.test(value);

    instance.formValidationStatus.set('passwordIsValid', validateField(value));
  }
});
```

Every event handler function is called with the second argument
set to current instance of the template. Via this reference, it's
possible to refer to every property that has been set earlier in
`onCreated`. So, `instance.formValidationStatus` is the same
ReactiveDict instance as one created in `onCreated` a few lines
above.

Now, the state is being kept, let's expose it via helpers. It's a
bit tricky here, because helpers are not called with reference to
instance of template as the argument. Instead, we need to refer
to `this` to get what we need:

```javascript
Template.helloForm.helpers({
  usernameIsValid: function () {
    const usernameIsValid = this.formValidationStatus.get('usernameIsValid');

    return usernameIsValid;
  },
  passwordIsValid: function () {
    const passwordIsValid = this.formValidationStatus.get('passwordIsValid');

    return passwordIsValid;
  }
});
```

See that functions here are declared with "function" keyword, as
opposed to arrow functions for event handlers. In case of
helpers, to successfully retrieve values from the ReactiveDict
instance that is set to "formValidationStatus" property of
template, we need to pass context-dependent function. If we used
arrow function, `this` in such a function would refer to
`window`, the topmost browser API context, which would be
incorrect.

## Preventing form form getting submitted

So, now we have all the validation messages, but users still can
submit the form. More generally, we need to add some logic to a
certain event that happens exactly when the user hits "Submit".
It's `submit form`:

```javascript
Template.loginForm.events({
  'change #username': (event, instance) => {
    const validateField = value => /[a-zA-Z0-9]{3,30}/.test(value);

    instance.formValidationStatus.set('usernameIsValid', validateField(value));
  },
  'change #password': (event, instance) => {
    const validateField = value => /[a-zA-Z0-9-_=+\s]{8,}/.test(value);

    instance.formValidationStatus.set('passwordIsValid', validateField(value));
  },
  'submit form': (event, instance) => {
    event.preventDefault();

    const usernameIsValid = instance.formValidationStatus.get('usernameIsValid');
    const passwordIsValid = instance.formValidationStatus.get('passwordIsValid');

    if (usernameIsValid && passwordIdValid) {
      // valid; do something.
    } else {
      // invalid; inform the user about it.
    }
  }
});
```

You can see that, in this case, we no longer refer to event for
values to validate them once again. Instead, we rely on the
ReactiveDict that already stores validation status. Right after
we retrieve the values from ReactiveDict for every field, we can
do something, based on a condition that checks the validation
status.

It's up to you to do something: either inform the user (although
it's already visible via helpers that are bound to every field
separately) or redirect to another page ("Forgot password?").
