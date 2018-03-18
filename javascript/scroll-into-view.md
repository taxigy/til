# Scrolling an element into view

There's an amazing API in all modern browsers (according to Can I Use, all
except mobile Opera),
[`Element#scrollIntoView`](https://developer.mozilla.org/en-US/docs/Web/API/Element/scrollIntoView).
Before this, we must have used jQuery or smooth-scroll libs, but now it's
natively supported. The variety of ways to scroll into view isn't as fascinating
as with custom implementation, but it solves the problem and fits nicely into
your [performance
budget](http://bradfrost.com/blog/post/performance-budget-builder/).

```javascript
document.querySelector('section#chapter2').scrollIntoView();
```

According to MDN, to scroll smoothly, you have to provide the first argument an
options plain-object with "behavior" property set to "smooth":

```javascript
document.querySelector('section#chapter2').scrollIntoView({ behavior: smooth });
```

Now, if you want all anchor elements on the page that point at other elements
within this page to provide this smooth scrolling experience, you may want to do
like this:

```javascript
document.querySelectorAll('a[href*="#"]').forEach(element => {
  const { href } = element.attributes; // -> for example, "#chapter2"

  if (href) {
    element.addEventListener('click', (event) => {
      event.preventDefault();

      const id = href.value.slice(1); // -> becomes "chapter2"

      document.getElementById(id).scrollIntoView({ behavior: 'smooth' });
    });
  }
});
```

Done.
