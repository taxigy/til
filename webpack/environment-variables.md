# Environment variables and Webpack

When you use [environment
variables](https://nodejs.org/api/process.html#process_process_env),
they don't go further the process Webpack is working in. So, you
may do

```bash
NODE_ENV=production npm run build
```

where `build` task runs Webpack to bundle all the things, and
expect that somewhere in your front-end app

```javascript
process.env.NODE_ENV
```

is "production". The problem is that, even though Webpack passes
by references to `process.env`, unless you set [target to
"browser"](https://webpack.github.io/docs/configuration.html#target),
it just doesn't work.

Any reference to `process.env` in the code of your front-end app
will fail with ReferenceError, because there's no variable called
`process` anywhere.

To make Webpack take care of putting the right values into your
app bundle, you can use EnvironmentPlugin. In your Webpack
config, in `plugins`, declare a new call:

```javascript
new webpack.EnvironmentPlugin([
  'NODE_ENV',
  // other environment variables, if necessary
]);
```

This way, you pass these environment variables down to app
bundle, so that the reference

```javascript
process.env.NODE_ENV
```

becomes meaningful, and the value is really "production".

Another way to make it work is via DefinePlugin:

```javascript
new webpack.DefinePlugin({
  'process.env': {
    NODE_ENV: JSON.stringify(process.env.NODE_ENV),
    // other environment variables, if necessary
  }
})
```

It's more verbose, and there's no use to do so. Stick to
EnvironmentPlugin.
