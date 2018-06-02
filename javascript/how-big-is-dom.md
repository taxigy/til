# How big is the DOM?

To get the number of all elements in the document, run

```javascript
document.querySelectorAll('*').length
```

Why? Because pages with greater number of elements run slower
than those with fewer. Reflow gets more expensive. Typically, a
solid modern page is about 2000 elements. No thorough analysis
here but a page with DOM having about 5000 elements lags
noticeably on a MacBook Pro 2015. So, yep, something to consider.

_TODO: do proper analysis, those guessed values are actually quite
upsetting._
