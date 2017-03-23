# Webpack 2 and PostCSS

Just a quick snippet.

This is what to do:

```bash
yarn add webpack babel-{core,loader} extract-text-webpack-plugin {style,css,postcss}-loader precss autoprefixer
```

This is webpack.config.js:

```javascript
const path = require('path');
const ExtractTextPlugin = require('extract-text-webpack-plugin');

module.exports = {
  entry: './src/index.js',
  output: {
    filename: 'bundle.js',
    path: path.resolve(__dirname, 'build'),
  },
  module: {
    rules: [{
      test: /\.jsx?/,
      use: [
        'babel-loader',
      ],
    }, {
      test: /\.p?css$/,
      use: ExtractTextPlugin.extract({
        fallback: [{
          loader: 'style-loader',
        }],
        use: [{
          loader: 'css-loader',
          options: {
            modules: true,
            localIdentName: '[name]__[local]--[hash:base64:5]',
          },
        }, {
          loader: 'postcss-loader',
        }],
      }),
    }],
  },
  plugins: [
    new ExtractTextPlugin('bundle.css'),
  ],
  resolve: {
    extensions: ['.js', '.jsx'],
  },
};
```

and this is postcss.config.js:

```javascript
const precss = require('precss');
const autoprefixer = require('autoprefixer');

module.exports = {
  plugins: [
    precss(),
    autoprefixer(),
  ],
};
```
