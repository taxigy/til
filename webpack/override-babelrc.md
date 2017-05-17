# Override .babelrc for babel-loader

Set `use.options.babelrc` to false:

```javascript
module: {
  rules: [{
    test: /\.jsx?/,
    use: {
      loader: 'babel-loader',
      options: {
        babelrc: false, // <- this one
        presets: ['es2015', 'react'],
        plugins: ['transform-object-rest-spread'],
        cacheDirectory: true,
      },
    },
    include: pathToSources,
  }],
},
```
