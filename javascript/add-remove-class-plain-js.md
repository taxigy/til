# Add and remove a class name in plain vanilla Javascript

Just recently, I saw [a
Codepen](https://codepen.io/MarcRay/pen/vmJBn?q=sticky+header&limit=all&type=type-pens)
that demonstrated a case of a sticky header controlled by
Javascript code. The problem was that Javascript code looked like
this:

```javascript
$(window).scroll(function() {
  if ($(window).scrollTop() > 100) {
    $('.main_h').addClass('sticky');
  } else {
    $('.main_h').removeClass('sticky');
  }
});
```

which looks sort of okay, but imagine that it's _the only_ code
you have on the page. It means that these seven lines require a
few KB of jQuery lib delivered along with the page.

For those who don't have enough performance budget to allow such
a luxury to drag a whole library just to make sticky header work,
there's actually a way to make it in plain Javascript:

```javascript
document.addEventListener('DOMContentLoaded', () => { // <- to be on the safe side
  const header = document.querySelector('.main_h');

  document.addEventListener('scroll', () => {
    if (window.scrollY > 10) {
      header.classList.add('sticky');
    } else {
      header.classList.remove('sticky');
    }
  });
});
```

No extra dependency, works in all browsers:

- `document.querySelector` is [100%
  supported](https://caniuse.com/#feat=queryselector),
- `DOMContentLoaded` is [100%
  supported](https://caniuse.com/#feat=domcontentloaded),
- `Element#classList` is [97.69%
  supported](https://caniuse.com/#feat=classlist) as of March
  2018, but the features we use (`add` and `remove` with one
  argument) is 100% supported.
