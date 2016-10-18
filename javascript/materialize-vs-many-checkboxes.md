# Materialize and many checkboxes case

Imagine that you use [Materialize](http://materializecss.com/) lib and you want to put a bunch of checkboxes on your page. So you look for checkbox example on the official doc and quickly paste the first things you see onto your page:

```html
<form id="theForm" name="theForm" action="">
  <input type="checkbox" id="test5" />
  <label for="test5">Red</label>
  <input type="checkbox" id="test5" />
  <label for="test5">Red</label>
  <input type="checkbox" id="test5" />
  <label for="test5">Red</label>
</form>
```

And then you see that nothing is working. It's because [the value of `id` attribute has to be unique](http://www.w3schools.com/tags/att_global_id.asp). If it's not, you'll see weirdest stuff, like this. Stick to the rules, use unique `id`s and be happy.

-----

See [this Codepen](http://codepen.io/rishatmuhametshin/pen/GjYykQ?editors=1010) for a quick example. Try to click on the checkboxes.
