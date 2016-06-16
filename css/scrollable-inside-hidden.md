# Scrollable element inside hidden overflow element

A classic situation is when you need an element with `overflow:
scroll` inside an element with `overflow: hidden`. For example,
for arrangement

```html
<div class="parent">
  <div class="child">
    ...
  </div>
</div>
```

simply setting

```scss
.parent {
  overflow: hidden;
}

.child {
  overflow: auto;
}
```

would not be working. There are two things to keep in mind:

- parent has to have height greater than 0, hence not auto.
- child element has to be of parent's height, not 0 or auto.

So, the working setup would be

```scss
.parent {
  height: 100vh;
  width: 100vw;
  overflow: hidden;
}

.child {
  height: 100%;
  width: 100%;
  overflow: auto;
}
```

Combine this with different kinds of layout, for example,
Slack-like scrollable messages feed and independably scrollable
roster.
