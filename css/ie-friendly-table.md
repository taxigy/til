# IE-friendly table

It could be a bit late, but there's still [about
2%](https://caniuse.com/usage-table) of people who use IE 11. This is series of
posts about IE-friendly UI patterns with fallbacks on old styling techniques.
This one is about _table_, but without "table", "tr" or "td" elements.

---

Let's represent a table as a flat collection of elements. This organization is
easier on DOM and also a little bit easier to navigate whenever you need to
debug something. And it fits perfectly into the concept of Flexgrid.

```html
<div class="table">
  <div class="cell head">Number</div>
  <div class="cell head">Letter</div>
  <div class="cell head">Together</div>
  <div class="cell">1</div>
  <div class="cell">A</div>
  <div class="cell">1A</div>
  <div class="cell">2</div>
  <div class="cell">B</div>
  <div class="cell">2B</div>
  <div class="cell">3</div>
  <div class="cell">C</div>
  <div class="cell">3C</div>
</div>
```

Now, the table layout:

```less
.table {
  padding: 8px 0;
  background-color: lighten(lightyellow, 2.25);

  @supports (display: grid) {
    display: grid;
    grid-template-columns: 1fr 1fr 1fr;
    grid-gap: 8px;
  }
}

.cell {
  width: calc(100% / 3);
  border-radius: 4px;
  padding: 8px 12px;
  background-color: lightgreen;
  float: left;

  &:nth-child(3n + 2) {
    clear: both;
  }

  @supports (display: grid) {
    width: auto;
  }
}

.head {
  background-color: lightgray;

  @supports (display: grid) {
    grid-column-start: 1;
    grid-column-end: 4;
  }
}
```

See [the Codepen](https://codepen.io/rishatmuhametshin/pen/oqJRjG?editors=1100).
