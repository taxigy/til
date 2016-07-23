# Turn a bunch of images of different sizes into a grid of strictly sized items

It's a pretty common challenge, when you have images you don't really know heights and widths of. Using a simple technique that involves `background-image` property, it's easy to turn them into a solid grid:

```html
<div class="grid">
  <div
    class="image"
    style="background-image: url(http://lorempixel.com/300/500)">
  </div>
  <div
    class="image"
    style="background-image: url(http://lorempixel.com/400/300)">
  </div>
  <div
    class="image"
    style="background-image: url(http://lorempixel.com/200/150)">
  </div>
  <div
    class="image"
    style="background-image: url(http://lorempixel.com/100/100)">
  </div>
  <div
    class="image"
    style="background-image: url(http://lorempixel.com/1000/1000)">
  </div>
  <div
    class="image"
    style="background-image: url(http://lorempixel.com/250/150)">
  </div>
</div>
```

See that the images have different sizes. The only thing that makes this solution ugly is that it's necessary to use `style` attribute. But it's worth it. See the css:

```scss
.grid {
  display: flex;
  flex-direction: row;
  flex-wrap: wrap;
  justify-content: center;
  align-items: center;
}

.image {
  height: 200px;
  width: 200px;
  margin: 10px;
  background-size: cover;
  background-position: center center;
  background-repeat: no-repeat;
}
```

Now [see the result on Codepen](http://codepen.io/rishatmuhametshin/pen/GqdkqK?editors=2100).
