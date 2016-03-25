# Publish an npm package

It's simple. First, `npm init` to have a `package.json` file in the folder that
soon will become an npm package.

Next, add some dependencies. Probably, `babel` would be one of the most useful.

Then, in `package.json` do some preparation stuff, with emphasis on two
important settings:

```json
{
  "name": "pancakes",
  "version": "0.0.1-b1",
  "main": "build/index.js"
}
```

Those will be used by `npm` to publish stuff.

Build, `git add .`, commit, push and

```bash
$ npm publish
```

This will make `npm` run a publishing routine. If there's no package name
collision and the version is formatted properly, the package will be published
and accessible via the URL http://npmjs.com/packages/pancakes. And when people
do `npm install --save pancakes`, the version `0.0.1` will be installed, causing
them some pain because, to install beta versions, they will need to mention the
exact version, like

```bash
$ npm install --save pancakes@0.0.1-b1
```

Which is okay for the first few releases.
