# Vertically aligned inline elements

Let's pretend there's a container element, which is a block element (say,
`div`), and inside it, there are two elements that should fit into a single line
(spoiler: a single row) and also be vertically aligned by middle. There are many
ways to do that, each having its own features and limitations.

Let's pretend, the markup is as simple as

```html
<div class="wrapper">
  <span class="tall">X</span>
  <span class="short">Y</span>
</div>
```

So, there is a `.tall` element, and there's a `.short` element. Let's style
them.

## Translating a smaller element

There are two elements, and I know both their heights. So, I simply translate
the shorter one by half of the difference of their heights on Y axis:

```less
.wrapper {
  background-color: rgba(50, 50, 50, 0.25);
  height: 40px;
  width: 200px;
  padding: 2em;
}

.tall {
  display: inline-block;
  height: 40px;
  width: 40px;
  background-color: rgba(120, 50, 50, 0.5);
}

.short {
  display: inline-block;
  height: 20px;
  width: 100px;
  background-color: rgba(50, 120, 50, 0.5);
  transform: translateY((40px - 20px) / 2);
}
```

See [codepen](http://codepen.io/rishatmuhametshin/pen/greRab).

## Setting line height of a bigger element

Simply setting the `line-height` property of a taller element works nice both
for aligning elements _and_ aligning text!

```less
.wrapper {
  background-color: rgba(50, 50, 50, 0.25);
  height: 40px;
  width: 200px;
  padding: 2em;
}

.tall {
  display: inline-block;
  height: 40px;
  width: 40px;
  background-color: rgba(120, 50, 50, 0.5);
  line-height: 40px;
}

.short {
  display: inline-block;
  height: 20px;
  width: 100px;
  background-color: rgba(50, 120, 50, 0.5);
}
```

Limitation? Text in the `.tall` element should fit into a single line, otherwise
it won't be a nice surprise.

See [codepen](http://codepen.io/rishatmuhametshin/pen/GZxEZR).

## Applying Flexbox

My fave. Flexbox rules. Simply use a container that has row direction:

```less
.wrapper {
  background-color: rgba(50, 50, 50, 0.25);
  height: 40px;
  width: 200px;
  padding: 2em;
  display: flex;
  flex-direction: row;
  align-items: center;
}

.tall {
  display: inline-block;
  height: 40px;
  width: 40px;
  background-color: rgba(120, 50, 50, 0.5);
}

.short {
  display: inline-block;
  height: 20px;
  width: 100px;
  background-color: rgba(50, 120, 50, 0.5);
}
```

See [codepen](http://codepen.io/rishatmuhametshin/pen/QNmgNd).

## Pretending it's a table

That's an ol' good practice. Back in grampy's days, frontenders tended to use
`display: table` to alter the behavior of elements in flow dramatically.

```less
.wrapper {
  background-color: rgba(50, 50, 50, 0.25);
  height: 40px;
  width: 200px;
  display: table-row;
}

.tall {
  display: table-cell;
  height: 40px;
  width: 40px;
  background-color: rgba(120, 50, 50, 0.5);
  vertical-align: middle;
}

.short {
  display: table-cell;
  height: 20px;
  width: 100px;
  background-color: rgba(50, 120, 50, 0.5);
  vertical-align: middle;
}
```

See [codepen](http://codepen.io/rishatmuhametshin/pen/vGRZGo).
