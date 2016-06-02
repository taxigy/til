# Cordova app and in-app browser

Simply doing

```javascript
window.open('http://google.com', '_system');
```

doesn't work. There's a
[package](https://github.com/meteor/cordova-plugin-inappbrowser) for that, and
its latest version by today is already 1.4.0. Unfortunately, it didn't
work, whatever the reason. A (much) older one did. So. it's simply a matter of a
single package getting added to make `window.open` expressions work properly:

```bash
meteor add cordova:org.apache.cordova.inappbrowser@0.5.4
```

Done.
