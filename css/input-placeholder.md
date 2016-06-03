# Input field's placeholder

So far, I've found several interesting techniques that enable
interesting ways for `input[type=text]` field's `placeholder`
pseudo-element to get styled.

Let's pretend, there's a simple component

```jsx
export default const theInput = ({className, onChange, onBlur}) => (
  <input
    className={`${className} theInput`}
    type="text"
    onChange={onChange}
    onBlur={onBlur} />
);
```

and so the stylesheet is

```scss
.theInput {
  padding: 2px 16px;
  font-size: 10pt;
  line-height: 2.25;
  color: rgba(white, 0.9);
  background-color: rgba(black, 0.15);
  transition: all 150ms ease;

  &:placeholder-shown {
    color: rgba(white, 0.25);
  }

  &::placeholder {
    color: rgba(white, 0.25);
    text-align: center;
    transition: all 330ms ease;
  }

  &:focus {
    &::placeholder {
      color: rgba(white, 0.5);
    }
  }
}
```

The `:placeholder-shown` is a pseudo-class applied to the `input`
field whenever the placeholder is visible. And `::placeholder`
(which is converted neatly into a bunch of vendor-prefixed rules)
is a pseudo-element. Combination of `:focus` and `::placeholder`
is applied to a case when the input field is focused but the
placeholder is still visible (or invisible, but that's not as
interesting). As the result, everything is animated in a nice
way:

1. By default, placeholder element has white text with 0.25
   opacity.
2. Once focused, the input field has `:focus` pseudo-class
   applied, so the placeholder has now white text with 0.5
   opacity.
3. After a user starts typing, default input field text turns,
   with transition, from white with 0.25 opacity to white with
   0.9 opacity, while placeholder turns hidden.

## Todo

- find a way to animate placeholder's appearance and
  disappearance (see
  [Q&A](http://stackoverflow.com/q/37617300/1287643)).
