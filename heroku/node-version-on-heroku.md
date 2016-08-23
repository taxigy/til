# Node version on Heroku dyno

By default, Heroku instance is running Node 5. To change it and make Heroku install a specific version of Node, define it in `package.json`:

```json
"engines": {
  "node": "6.3.1"
}
```
