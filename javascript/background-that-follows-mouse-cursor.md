# A background that follows the mouse cursor

For some visual interactivity, let's make a Hero image background follow the
mouse cursor a little. For simplicity, let's assume the Hero is always 100vw
wide and 100vh tall, and the background image moves towards the cursor linearly
by 7.5% of its size max.

First, let's put the image there:

```html
<head>
  <style>
    body {
      margin: 0;
      padding: 0;
      height: 100vh;
      width: 100vw;
    }

    .c {
      height: 100%;
      width: 100%;
      background: url(https://lorempixel.com/500/200) center/120% no-repeat;
    }
  </style>
</head>
<body>
  <div class="c"></div>
</body>
```

And now let's

- establish a "mousemove" listener, where
- calculate the shift based on cursor position on screen, and
- reset the value of "background-position" based on that.

```javascript
const GRAVITY = 7.5;

document.addEventListener('DOMContentLoaded', () => {
  const c = document.querySelector('.c');
  
  c.addEventListener('mousemove', (event) => {
    const { clientX, clientY } = event;
    const { innerWidth, innerHeight } = window;
    const newX = 50 + GRAVITY * (innerWidth / 2 - clientX) / innerWidth;
    const newY = 50 + GRAVITY * (innerHeight / 2 - clientY) / innerHeight;
    
    c.style.setProperty('background-position', `${newX}% ${newY}%`);
  })
});
```

See [the Codepen](https://codepen.io/rishatmuhametshin/pen/rdMEjd?editors=0110).

More info:

- [`MouseEvent#clientX`](https://developer.mozilla.org/en-US/docs/Web/API/MouseEvent/clientX)
  and
  [`MouseEvent#clientY`](https://developer.mozilla.org/en-US/docs/Web/API/MouseEvent/clientY).
- [`window.innerHeight`](https://developer.mozilla.org/en-US/docs/Web/API/Window/innerHeight)
  and
  [`window.innerWidth`](https://developer.mozilla.org/en-US/docs/Web/API/Window/innerWidth).
- [`CSSStyleDeclaration#setProperty`](https://developer.mozilla.org/en-US/docs/Web/API/CSSStyleDeclaration/setProperty)
  (`Element.style` points at an instance of `CSSStyleDeclaration`).
