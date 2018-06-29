# Sticky side-headers

One of the common patterns for a long list of something is to have group headers
on one of the sides where headers are also sticky. With "position: sticky", it
can be done with semantically reasonable HTML structure:

```html
<div class="article">
  <div class="section">
    <div class="title"></div>
    <div class="paragraphs">
      <div class="p"></div>
      <div class="p"></div>
    </div>
  </div>
  <div class="section">
    <div class="title"></div>
    <div class="paragraphs">
      <div class="p"></div>
      <div class="p"></div>
    </div>
  </div>
  <div class="section">
    <div class="title"></div>
    <div class="paragraphs">
      <div class="p"></div>
      <div class="p"></div>
    </div>
  </div>
</div>
```

using the styles

```scss
.section {
  display: flex;
}

.title {
  align-self: flex-start;
  position: sticky;
  top: 0;
  flex: 0 1 250px; // necessary to maintain columns
}

.p {
  margin: 0 0 10px;
}
```

See [Codepen](https://codepen.io/rishatmuhametshin/full/bKOzNv/) for that.

If we use Flexgrid, the HTML gets one level shallower:

```html
<div class="article">
  <div class="title"></div>
  <div class="paragraphs">
    <div class="p"></div>
    <div class="p"></div>
  </div>
  <div class="title"></div>
  <div class="paragraphs">
    <div class="p"></div>
    <div class="p"></div>
  </div>
  <div class="title"></div>
  <div class="paragraphs">
    <div class="p"></div>
    <div class="p"></div>
  </div>
</div>
```

and the styles become different:

```scss
.article {
  display: grid;
  grid-template-columns: auto 1fr;
  grid-gap: 20px;
}

.title {
  align-self: flex-start;
  position: sticky;
  top: 0;
}

.p {
  margin: 0 0 10px;
}
```

but it does not make titles on the left to stick according to their counterpart
cells on the right. As a result, titles stack one on the other and make a mess.
A quick solution for that is to just add solid opaque background to them. See
[the other Codepen](https://codepen.io/rishatmuhametshin/pen/bKOzNv).
