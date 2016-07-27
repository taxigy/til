# Resolve paths to modules

By default, if you have a project and two files, ./src/components/TheComponent.js and ./src/containers/TheContainer.js, you have references like

```javascript
import TheComponent from '../components/TheContainer';
```

With Webpack, it's possible to point at multiple folders so that absolute paths to modules would be searched there. The `webpack.config.js` may look like

```javascript
module.exports = {
  // ... entry, output, other settings
  resolve: {
    modulesDirectories: [
      './node_modules',
      './src'
    ]
  }
};
```

See [reference to `modulesDirectories` setting](https://webpack.github.io/docs/configuration.html#resolve-modulesdirectories). After that, whatever is in ./src, you can point at it via "absolute" path:

```javascript
import TheComponent from 'components/TheContainer';
```

See that including `'./node_modules'` is critical once you explicitly define the `modulesDirectories` setting in Webpack config.
