# Using CSS modules in React app

The idea behind [CSS modules](https://medium.com/seek-ui-engineering/the-end-of-global-css-90d2a4a06284#.cwk7t8j88)
is not new and everybody knows that it's somewhat modern stuff every cool engineer uses. When you start digging for more
information, it looks like you need

* Webpack,
* [css-loader](https://github.com/webpack/css-loader),
* and a nice Webpack config that would define a loader in a way like `css?modules`.

That's not enough, as it turned out, and so I'm here, in this "Today I Learned" repo, with a new gotcha.

A quick look at my Webpack config answers the question I had in mind for about an hour, "Why the hell it's not working?!":

```javascript
const ExtractTextPlugin = require('extract-text-webpack-plugin');

module.exports = {
  entry: ['babel-polyfill', './src/App.js'],
  output: {
    path: __dirname + '/build/',
    filename: 'bundle.js'
  },
  module: {
    loaders: [{
      test: /\.js$/,
      exclude: /node_modules/,
      loader: 'babel-loader'
    }, {
      test: /\.scss$/,
      loader: ExtractTextPlugin.extract('style', 'css?modules&localIdentName=[local]__[hash:base64:5]', 'sass')
    }]
  },
  plugins: [
    new ExtractTextPlugin('styles.css')
  ]
};
```

So, just Webpack is not enough. The full list is not very much longer though:

* Webpack,
* [css-loader](https://github.com/webpack/css-loader),
* in my case, also [sass-loader](https://github.com/jtangelder/sass-loader) (be careful, it requires extra packages), and
* [extract-text-webpack-plugin](https://github.com/webpack/extract-text-webpack-plugin).

My `package.json` looks like

```json
  ...
  "devDependencies": {
    "babel-core": "^6.10.4",
    "babel-loader": "^6.2.4",
    "babel-polyfill": "^6.9.1",
    "babel-preset-es2015": "^6.9.0",
    "babel-preset-react": "^6.11.1",
    "babel-preset-stage-0": "^6.5.0",
    "css-loader": "^0.23.1",
    "extract-text-webpack-plugin": "^1.0.1",
    "node-sass": "^3.8.0",
    "sass-loader": "^4.0.0",
    "style-loader": "^0.13.1",
    "webpack": "^1.13.1"
  },
  "dependencies": {
    "react": "^15.2.0",
    "react-dom": "^15.2.1",
    "react-redux": "^4.4.5",
    "react-router": "^2.5.2",
    "redux": "^3.5.2"
  },
  ...
```

After that, I'm able to use inline styles like

```javascript
import React from 'react';
import styles from './Components.scss';

const Component = () => (
  <div className={styles.component}>The Component</div>
);
```

That expands into something like

```html
  <div class="component__3Hbli">The Component</div>
```

given that all the styles are now bundled into `/build/styles.css`.
