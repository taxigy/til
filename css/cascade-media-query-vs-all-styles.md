# Cascade rule for selectors with and without media queries

In SASS, consider this:

```sass
.container
  display: flex
  flex-flow: column nowrap
  align-items: center

  &.container--reverse
    flex-direction: column--reverse

  @media (min-width: 1024px)
    flex-flow: row nowrap

    &.container--reverse
      flex-direction: row--reverse
```

This one works fine: `.container.container--reverse` element on large screens will have flex-direction: row-reverse.

Now this:

```sass
.container
  display: flex
  flex-flow: column nowrap
  align-items: center

  @media (min-width: 1024px)
    flex-flow: row nowrap

    &.container--reverse
      flex-direction: row--reverse

  &.container--reverse
    flex-direction: column--reverse
```

Because media query has been declared _before_ the additional classname, it will have the same specificity, but lesser priority because it will be coming _before_ the style declaration for `.container.container--reverse`. It will have flex-direction: column-reverse.

Be careful with order.
