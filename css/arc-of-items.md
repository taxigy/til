# Arc of items

I'm not sure if it is a frequent enough case to cover, but it's still good to
look at the CSS (and SCSS) to see what happens. So, there's SCSS and there's
"for" loop in SCSS that allows to create as many classnames, pseudo-classes or
whatever else as necessary. Let's say, we want to put 11 dots on an arc, such
that it makes a dome of these dots. Each dot is a "div" and arc itself is just a
single containing "div". No pseudo-elements!

```html
<div class='circle-container'>
  <div class="circle"></div>
  <div class="circle"></div>
  <div class="circle"></div>
  <div class="circle"></div>
  <div class="circle"></div>
  <div class="circle"></div>
  <div class="circle"></div>
  <div class="circle"></div>
  <div class="circle"></div>
  <div class="circle"></div>
  <div class="circle"></div>
</div>
```

That's pretty much the whole HTML code we need. Now, SCSS:

```scss
body {
  background-color: rgba(green, 0.25);
}

@mixin on-circle($item-count, $arc-size, $item-size) {
  position: relative;
  display: flex;
  justify-content: center;
  width:  $arc-size;
  height: ($arc-size + $item-size) / 2;

  .circle {
    position: absolute;
    bottom: 0;
    left: calc(50% - $item-size);
    width: $item-size;
    height: $item-size;
    border-radius: 50%;
    background-color: white;

    $angle: ceil(180 / ($item-count - 1));
    $rot: 0;

    @for $i from 0 through $item-count - 1 {
      &:nth-of-type(#{$i + 1}) {
        $angleDeg: 180 / ($arc-size - $item-size) * (($arc-size - $item-size) * $i / ($item-count - 1));
        transform: rotate($rot * 1deg - 180deg) translate(($arc-size - $item-size) / 2) rotate(-$rot * 1deg + 180deg);
      }

      $rot: $rot + $angle;
    }
  }
}

.circle-container {
  @include on-circle($item-count: 11, $arc-size: 20em,
$item-size: 1em);
  margin: 0 auto;
  background-color: rgba(green, 0.15);
}
```

The logic is represented in form of a mixin, and I'm not sure it's a good idea
to do so, as it requires certain kind of (and classname of) child elements, but
probably it's fine for just a single case.

See the result [on
Codepen](http://codepen.io/rishatmuhametshin/pen/EZzWyN?editors=1100).
