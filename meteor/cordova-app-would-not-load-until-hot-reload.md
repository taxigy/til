# Cordova app that doesn't load until you change something so it hot-reloads?

[I had one](http://stackoverflow.com/q/36595515/1287643), and what was happening
was that the code

```javascript
$(document).ready(() => alert('ready'));
document.addEventListener('deviceready', () => alert('ready'));
Meteor.startup(() => alert('ready'));
```

anywhere on the client would never be executed. But if I opened a console and
ran

```javascript
alert('ready');
```

then it, of course, worked. The same went for this piece of code, which is
exactly what I had in the codebase, on the client:

```javascript
document.addEventListener('deviceready', () => {
  BlazeLayout.render('layout', {
    main: 'somethings'
  });
});
```

But if I did

```javascript
BlazeLayout.render('layout', {
  main: 'somethings'
});
```

on the client, the app immediately started working correctly.

And no viable problems, and no error messages anywhere, be it Safari console,
Xcode console, or any alerts on the simulator.

And then I found out that I was omitting settings, and in the settings, there
was API endpoint root location, so `Meteor.settings.public.api` would evaluate
to `undefined`. Running the app with settings, as

```bash
$ meteor run ios --production --settings ./settings.json
```

resolved the issue. It doesn't look like it could be a common mistake, but it
just gave me an idea about how little friendly Cordova is.

Always control all the refs and check for ReferenceErrors!

Same applies to one more problem that was perfectly handled by browser but not
on a mobile device. Since I used *device* Cordova plugin, at some moment in
time, when the code was loading, `device` was still undefined, hence
ReferenceError. In such situations, having `typeof` as the first item of many in
a confitinal statement helps a lot. So, like

```javascript
if (typeof device === 'object' && something) {
  // do something meaningful with device information
}
```

would work without having an exception thrown even when `device` is undefined.
