# window.location.search as an object

Browser API provides the property `window.location.search`, which is a string and contains everything between "?" and "#", including the "?" character. For a very typical URL like

```
https://www.google.de/search?q=library+of+component+for+landing+pages&oq=library+of+component+for+landing+pages&aqs=chrome..69i57.14921j0j7&sourceid=chrome&ie=UTF-8
```

the value of `window.location.search` would be

```
?q=library+of+component+for+landing+pages&oq=library+of+component+for+landing+pages&aqs=chrome..69i57.14921j0j7&sourceid=chrome&ie=UTF-8
```

If we want it to be a plain-object with easily accessible properties that resemble search params, we can do it:

```javascript
window.location.search
  .slice(1)
  .split('&')
  .map(e => e.split('='))
  .reduce(
    (t, c) => Object.assign(t, { [c[0]]: c[1] !== undefined ? c[1] : true }),
    {},
  );
```

which returns a plain-object:

```javascript
{
  aqs: 'chrome..69i57.14921j0j7',
  ie: 'UTF-8',
  oq: 'library+of+component+for+landing+pages',
  q: 'library+of+component+for+landing+pages',
  sourceid: 'chrome',
  xyz: true,
}
```
