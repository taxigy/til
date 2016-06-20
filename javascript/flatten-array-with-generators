# Flattening an array using generators

Think of a generator like it's a recursive function, because

```javascript
function* generator(input) {
  yield* generator(input);
}
```

is just like a recursive call. (which goes wrong in this particular example, don't try at home). So, to flatten an array, it's just this:

- check if the *first* element is an array, and if so, call recursively against it,
- if the first element is not an array, yield it, and
- call recursively against *rest* of the array.

Which turns into

```javascript
function* flatten(e) {
  const [first, ...rest] = e;
  
  if (first instanceof Array && first.length) {
    yield* flatten(first);
  } else if (!(first instanceof Array)) {
    yield first;
  }
  
  if (rest instanceof Array && rest.length) {
    yield* flatten(rest);
  }
}
```

Now, to output the result, let's declare some weird array, like

```javascript
const a = [[1, 2, [3]], 4, [5, [6, []], 7, [], []]];
```

and then `console.log` the generator out:

```javascript
for (const e of flatten(a)) {
  console.log(e);
}
```

The output should be 

```
1
2
3
4
5
6
7
```

----

This has first been published [as a Gist](https://gist.github.com/taxigy/1a3f38f57a6ab91ccdf7e3da39c67760), but I thought it would be a good idea to post it here, too, so that it would be searchable across this certain repo.
