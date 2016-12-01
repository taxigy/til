# Access Git commit hash from Node process via environment variables

Easy!

```bash
COMMIT_HASH=`git rev-parse --short HEAD` node
```

And then, in Node:

```javascript
console.log(process.env.COMMIT_HASH);
```
