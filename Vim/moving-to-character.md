# Moving to a certain character

There's a powerful search engine in Vim that's invoked with `/`, but sometimes
it's useful to move to a certain character to the right or to the left in line.
To do that, there are commands `f` and `t`, the former walking right at the
position of searched character, and the latter walking _before_ it, i.e. at the
position to the left of it. So, for a line

```javascript
function* whatever() {
  yield 'LMAO IDC';
}
```

* to go to the position of "O", it's `fO`, and to the previous character ("A"),
  it's `tO`,
* therefore, to remove everything from current position up to "O", inclusive,
  it's `dfO`, and exclusive, `dtO`, and
* to select in visual mode, it's `vfO` or `vtO`.

A common pattern is to rename `doSomething` into, say, `handleSomething`, so I
it's simply `ctShandle`.
