# "No such file ..." problem with Yarn

There's [yarn](https://github.com/yarnpkg/yarn), a nice tool to keep the dependencies flat and clean, and node_modules/ under strict control. But sometimes undesirable happens:

```
ERROR in ./~/css-loader?{"zindex":false,"autoprefixer":false}!./~/postcss-loader!./~/sass-loader/lib/loader.js?config=sass!./src/styles/application.sass
Module build failed: Error: ENOENT: no such file or directory, open '~/coolest-app-ever/node_modules/svgo/lib/svgo/../../.svgo.yml'
```

That problem with [svgo](https://github.com/svg/svgo) is very specific and has a heavily upvoted and actually working solution:

```bash
rm .yarnclean
rm -rf node_modules/
yarn
```

See [the original message](https://github.com/svg/svgo/issues/622#issuecomment-261064895).
