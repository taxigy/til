# CSS variables in SVG

It is possible to use CSS variables in SVG. For this to work, the target SVG has
to be part of DOM. So instead of

```html
<p>
  <img src="abc.svg" />
</p>
```

the content of SVG to be in place:

```html
<p>
  <svg width="500" height="500" viewBox="0 0 500 500" fill="none" xmlns="http://www.w3.org/2000/svg">
    <rect width="500" height="500" fill="var(--background-color)"/>
  </svg>
</p>
```

Bonus: in Jekyll, for this to work, SVG files need to be in the \_includes
folder and then need to be included, like

```html
{% include abc.svg %}
```

where \_includes/abc.svg is the target image.

