# Upload Cordova app to TestFlight

Simply running a Meteor mobile app, that is powered by Cordova, is not a big
deal. Simply

```bash
$ meteor run ios
```

or

```bash
$ meteor run ios-device
```

would do the trick. The problem is, these methods build a _debug_ version of the
app. There's a little difference with _release_ version that is when you build
for Release, Xcode tries to trim all the external dependencies, many of them
required to build an app for i386 platform.

Sifting through StackOverflow has shown that, as of 2014, it was okay to just
remove i386 target platform and leave armv7 and armv7s, so that those
dependencies would become irrelevant. But in Xcode 7 these days, and since 2015,
this platform is required to stay in the list.

The solution was rather simple:

* for every project, i.e. MyApp and CordovaLib,
* both for project and for target,
* for both targets, Debug and Release, set *Build Active Architecture Only* to
  _No_.

After that, the app build successfully _and_ is successfully uploaded to iTunes
Connect as archive (see _Archive_ option in, well, _Cmd+Shift+/_).
