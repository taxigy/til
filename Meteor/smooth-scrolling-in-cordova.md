# Smooth scrolling in Cordova app

A typical Meteor with Cordova mobile application is a vertically scrollable list
of something. An average user would, most likely, expect this list to scroll
smoothly. By default, it doesn't.

You could use some multi-part solution, with Javascript and complex transition
computation involved, or you can simply use a single CSS property:

```less
.whateverScrollable {
  // some other styles
  -webkit-overflow-scrolling: touch;
}
```

Let's pretend the whole layout consists of three blocks:

* header,
* footer, and
* content

and the _content_ part is the only scrollable part, while _header_ and _footer_
are static, i.e. absolutely positioned relative to document body or a wrapping
DOM element.

So, the markup would look like

```html
<template name="layout">
  <div class="header">{{header}}</div>
  <div class="content">{{content}}</div>
  <div class="footer">{{footer}}</div>
</template>
```

and styles look like

```less
@header-height: 96px;
@footer-height: 72px;

.header {
  position: fixed;
  top: 0;
  left: 0;
  height: @header-height;
}

.content {
  position: fixed;
  top: 0;
  left: 0;
  height: ~"calc(100vh - @{header-height})";
  margin-top: @header-height;
  padding-bottom: @footer-height;
  overflow-x: hidden;
  overflow-y: auto;
  -webkit-overflow-scrolling: touch; // <- this one
}

.footer {
  position: fixed;
  left: 0;
  bottom: 0;
  height: @footer-height;
}
```

And with just this single CSS property, `.content` element now has smooth
scrolling on mobile devices (and some weird, but still smooth, in simulators).
