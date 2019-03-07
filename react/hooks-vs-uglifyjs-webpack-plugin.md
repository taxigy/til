# Hooks vs. uglifyjs-webpack-plugin

If you use [uglifyjs-webpack-plugin](https://webpack.js.org/plugins/uglifyjs-webpack-plugin/) and want to use [hooks](https://reactjs.org/blog/2019/02/06/react-v16.8.0.html) in React, here's the bad news: the two don't play well together. Once there's an attempt to render a component that uses hooks in minified build, the error looks like this:

```
TypeError: Cannot set property 'lastEffect' of null
     at or (react-dom.production.min.js:154)
     at ir (react-dom.production.min.js:154)
     at Object.useEffect (react-dom.production.min.js:158)
     at Object.useEffect (react.production.min.js:22)
     at q (index.jsx:44)
     at Ji (react-dom.production.min.js:150)
     at Rr (react-dom.production.min.js:178)
     at Ua (react-dom.production.min.js:232)
     at Va (react-dom.production.min.js:233)
     at xs (react-dom.production.min.js:249)
```

As you probably correctly guessed, and thanks for source maps for pointing at it clearly, line 44 of index.jsx has a reference to a hook (in this case, `useEffect`).

It's a known issue:

- https://github.com/facebook/react/issues/14014
- https://github.com/webpack-contrib/uglifyjs-webpack-plugin/issues/374
- https://github.com/mishoo/UglifyJS2/issues/3291

Same with Enzyme, by the way:

- https://github.com/reactjs/rfcs/issues/71

There are two options to go with: either stick to components declared as classes if you need lifecycle methods, or replace uglifyjs-webpack-plugin with [terser-webpack-plugin](https://webpack.js.org/plugins/terser-webpack-plugin/) (proof of working TBD).
