# Event (instance) is automatically defined in Webkit?

After publishing [a codepen about tilting
image](https://codepen.io/rishatmuhametshin/pen/xYEEGa), I found
that it was working in Chrome and Safari and didn't work in
Firefox. In the latter, there was a ReferenceError on the
console. It was clearly thrown from within a function assigned to
"mouseleave" event on a DOM element. The code looked like this:

```javascript
card.addEventListener('mouseleave', () => {
  event.target.children[0].style.setProperty('--js-hover-rotate-val', '0deg');
});
```

Somehow, I missed the first argument to the function. Still,
`event` was accessible. Perhaps Webkit follows [good old
`window.event` IE
spec](https://developer.mozilla.org/en-US/docs/Web/API/Window/event),
so it made sure that the value of `event` is the actual instance
of currently running event.

In Firefox, on the contrary, it worked _as it should:_ no
argument, no reference, one error.

A little update to the code made it work in Firefox, too, and
made it compliant with modern standard.

```javascript
card.addEventListener('mouseleave', (event) => {
  event.target.children[0].style.setProperty('--js-hover-rotate-val', '0deg');
});
```
