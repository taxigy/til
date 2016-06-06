# Flexbox in Safari

There are some interesting implementation details about Flexbox
in Safari. I'll be listing them here over time.

## Column direction

The

```scss
flex-direction: column
```

doesn't work identically in Chrome and Safari, see [example on
Codepen](http://codepen.io/rishatmuhametshin/pen/bedmXw). The
problem is, when there's an element that is a column, and two
elements inside it have default `height: auto`, in Safari, parent
element is rendered with height computed to 0.

So, for the element arrangement

```html
<div class="e">
  <div class="c">1</div>
  <div class="c">2</div>
</div>
```

the CSS

```scss
.e {
  display: flex;
  flex-direction: column;
}

.c {
  flex: 0 1 50%;
  background-color: rgba(black, 0.25);
}
```

just won't work in Safari.

There would be an obvious solution to add `height` property to every
child of flex element, i.e.

```scss
.c {
  flex: 0 1 50%;
  height: 50px;
  background-color: rgba(black, 0.25);
}
```

but that wouldn't work! Safari requires _parent element_ to have
height explicitly set so that its children would fit using flex
properties. Therefore, this CSS would work:

```scss
.e {
  display: flex;
  height: 100px;
  flex-direction: column;
}

.c {
  flex: 0 1 50%;
  background-color: rgba(black, 0.25);
}
```

But if you don't know exact height of every child, you're busted.
Unless you're not. There's a way to make flex element work
properly in Safari. To do that,

1. Start using `flex-wrap: wrap`,
2. Stick to `flex-basis: auto` and only manipulate widths, and
3. Forget about `flex-direction: column`. (unless you really need
   it)

The actual solution to this particular challenge looks like this:

```scss
.e {
  display: flex;
  flex-direction: row;
  flex-wrap: wrap;
}

.c {
  flex: 0 1 auto;
  width: 100%;
  background-color: rgba(black, 0.25);
}
```

The only limitation is that you no longer can safely use
horizontal margins in case you need more than one column.
Instead, use `calc` in `width` property, like:

```scss
.e {
  display: flex;
  flex-direction: row;
  flex-wrap: wrap;
  justify-content: space-between;
}

.c {
  flex: 0 1 auto;
  width: calc(50% - 10px);
  background-color: rgba(black, 0.25);
}
```

For three-column, it's 33%, and so on.
