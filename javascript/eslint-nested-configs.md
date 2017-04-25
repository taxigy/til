# Nested ESLint configurations

For a universal Javascript application that uses Express for the back-end, you
may want to use a separate ESLint config for the server-side source because
Express has some features that may be annoying when run through modern ESLint
default config (like [the one Airbnb
authored](https://www.npmjs.com/package/eslint-config-airbnb)):

```javascript
function whateverMiddleware(req, res, next) {
  req.runThroughWhateverMiddleware = true;
  next();
}
```

This code will make ESLint spit out ["no-param-reassign"
violation](http://eslint.org/docs/rules/no-param-reassign). But this way is
natural for Express! Same with console logging:

```javascript
function whateverMiddleware(req, res, next) {
  console.log('Applying whatever!'); // ! no-console
  req.runThroughWhateverMiddleware = true; // ! no-param-reassign
  next();
}
```

The ["no-console"](http://eslint.org/docs/rules/no-console) is a problem here.

To make it work, just use _nested ESLint configs_:

1. In project root folder, use a default .eslintrc, and
1. In ./server folder, use an updated .eslintrc that extends its parent.

There's no need to refer to parent config (even though [you
could](http://eslint.org/docs/user-guide/configuring#using-a-configuration-file)),
just define certain rules and they will be combined with rules declared in
parent config (aka one located in parent folder, if exists).
