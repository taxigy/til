# SVG background mask

tl;dr: see [Codepen](http://codepen.io/rishatmuhametshin/pen/EyPxLO) of that.

----

There's a common challenge when you have an SVG icon or image and
you need to change its whole color based on its state, most
often, on hover.

To do that, use `-webkit-mask`, which is a vendor-specific
version of `mask` CSS property (todo: find a way to autoprefix
it).

Let's say, we have a rating that shows up to five stars, and as
many stars have to be shown (or, better said, be _not
transparent_), as the rating reflects to.

The DOM is simple:

```html
<div class="stars"></div>
```

The CSS is quite tricky:

```scss
.stars {
  $value: 0.3; // rating, in percentage, valid values in [0, 1] range
  height: 50px;
  width: 5 * 50px;
  background-image: linear-gradient(90deg, green 0%, green $value * 100%, gray $value * 100%, gray 100%);
  -webkit-mask: url(data:image/svg+xml;base64,PD94bWwgdmVyc2lvbj0iMS4wIiBlbmNvZGluZz0idXRmLTgiPz48c3ZnIHZlcnNpb249IjEuMSIgaWQ9IkxheWVyXzEiIHhtbG5zPSJodHRwOi8vd3d3LnczLm9yZy8yMDAwL3N2ZyIgeG1sbnM6eGxpbms9Imh0dHA6Ly93d3cudzMub3JnLzE5OTkveGxpbmsiIHg9IjBweCIgeT0iMHB4IiB2aWV3Qm94PSIwIDAgNTYuMiA1NS4yIiBzdHlsZT0iZW5hYmxlLWJhY2tncm91bmQ6bmV3IDAgMCA1Ni4yIDU1LjI7IiB4bWw6c3BhY2U9InByZXNlcnZlIj48c3R5bGUgdHlwZT0idGV4dC9jc3MiPi5zdDB7ZmlsbDojNTM2RTdCO308L3N0eWxlPjxwb2x5Z29uIGNsYXNzPSJzdDAiIHBvaW50cz0iMjcuNSw0My4yIDExLjcsNTEuNSAxNC44LDM0IDIsMjEuNSAxOS42LDE5IDI3LjUsMyAzNS40LDE5IDUzLDIxLjUgNDAuMywzNCA0My4zLDUxLjUgIi8+PC9zdmc+) repeat-x 50% 50%;
}
```

The image is base64-encoded and inlined, but you can use URL
reference instead, no problem about that.

What happens there is that an SVG image, which has transparent
background by design, is a mask to an element filled with
gradient background. As a result, only that part of the whole
element is shown that is masked by the image.

Coolest thing is that the image itself can repeat!

You can imagine how many opportunities lay there, as the
custom-shaped element can now be applied with any CSS property.
Transitions, any colors you need, etc. Just amazing.
