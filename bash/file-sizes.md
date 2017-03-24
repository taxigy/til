# File sized

Sometimes, I'm in a folder and I want to know the size of some files there. In
most cases, these are ".js" files that are located along with ".js.map". So,
it's about finding paths to these files against a regex and then outputting the
size. It's this:

```bash
du -h `find ./ | grep ".js$"`
```

However, I'm pretty sure there is a better, shorter, cleaner version of it.
