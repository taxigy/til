# IE-friendly dialogue

It could be a bit late, but there's still [about
2%](https://caniuse.com/usage-table) of people who use IE 11. This is series of
posts about IE-friendly UI patterns with fallbacks on old styling techniques.
This one is about _dialogue_, a chat-like box with messages appearing on the
left and on the right.

---

A dialogue is a container with elements each representing a message. There are
two types of messages, "mine" and "theirs". Should look like this:

```html
<div class="messages">
  <div class="message">Sup</div>
  <div class="message mine">Sup</div>
  <div class="message">Sup</div>
  <div class="message">Sup</div>
  <div class="message mine">Sup</div>
  <div class="message">Sup</div>
  <div class="message mine">Sup</div>
  <div class="message mine">Sup</div>
  <div class="message mine">Sup</div>
  <div class="message">Sup</div>
</div>
```

In browsers that support Flexbox, let's use Flexbox; in browsers that don't,
let's use `float`:

```less
.messages {
  width: 100vw;
  max-width: 660px;
  padding: 8px 0;
  background-color: lighten(lightyellow, 2.25);
  overflow: auto;

  @supports (display: flex) {
    display: flex;
    flex-flow: column nowrap;
  }
}

.message {
  max-width: 450px;
  margin: 4px 12px;
  border-radius: 4px;
  padding: 8px 12px;
  background-color: lightgray;
  float: left;
  clear: both;

  @supports (display: flex) {
    align-self: flex-start;
  }

  &.mine {
    float: right;
    background-color: lightgreen;

    @supports (display: flex) {
      align-self: flex-end;
    }
  }
}
```

See [the
Codepen](https://codepen.io/rishatmuhametshin/pen/KobYLj?editors=1100).
