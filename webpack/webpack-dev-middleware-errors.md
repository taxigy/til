# Uneven errors that Webpack Dev Server would throw

Sometimes it just happens. I collect these cases here.

## Error in NormalModuleMixin

The issue is about Webpack Dev Server being unable to resolve the output path. Appears in a way like this:

```
/home/username/project/node_modules/webpack/node_modules/webpack-core/lib/NormalModuleMixin.js:161
            if(isError) throw e;
                              ^
Error: Invalid path
    at pathToArray (/home/username/project/node_modules/webpack-dev-server/node_modules/webpack-dev-middleware/node_modules/memory-fs/lib/MemoryFileSystem.js:23:38)
    at MemoryFileSystem.mkdirpSync (/home/username/project/node_modules/webpack-dev-server/node_modules/webpack-dev-middleware/node_modules/memory-fs/lib/MemoryFileSystem.js:107:13)
    at MemoryFileSystem.(anonymous function) [as mkdirp] (/home/username/project/node_modules/webpack-dev-server/node_modules/webpack-dev-middleware/node_modules/memory-fs/lib/MemoryFileSystem.js:187:34)
    at Tapable.<anonymous> (/home/username/project/node_modules/webpack/lib/Compiler.js:225:25)
    at Tapable.applyPluginsAsync (/home/username/project/node_modules/webpack/node_modules/tapable/lib/Tapable.js:60:69)
    at Tapable.Compiler.emitAssets (/home/username/project/node_modules/webpack/lib/Compiler.js:222:7)
    at Watching.<anonymous> (/home/username/project/node_modules/webpack/lib/Compiler.js:53:18)
    at /home/username/project/node_modules/webpack/lib/Compiler.js:397:12
    at Tapable.next (/home/username/project/node_modules/webpack/node_modules/tapable/lib/Tapable.js:69:11)
    at CachePlugin.<anonymous> (/home/username/project/node_modules/webpack/lib/CachePlugin.js:40:4)
```

See the [issue](https://github.com/webpack/webpack-dev-server/issues/88). The problem is in Webpack configuration:

```javascript
module.exports = {
  // webpack config options
  output: {
    // other output options
    path: './build'
  }
}
```

Webpack itself is okay with this value of `output.path` config property, but not the Webpack Dev Server. To make it work, you need to provide _absolute path_:

```javascript
const path = require('path');

module.exports = {
  // webpack config options
  output: {
    // other output options
    path: path.resolve(process.env.PWD, 'build')
  }
}
```
