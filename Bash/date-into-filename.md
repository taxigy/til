# Date into filename

The great thing about Github Pages is that you can use its built-in Jekyll
processor to create a blog from, simply said, a bund of files. Those files,
however, should have certain names for Jekyll to pick them up, like
`2016-01-01-happy-new-year.markdown`. One day, typing current date may have
become too annoying. There's a solution for that:

```bash
$ touch `date "+%Y-%m-%d"`-happy-new-year.markdown
```

Will produce, if today is January 1, exactly the same filename. Which is super
handy. At long last, there's no need for me to remember what day is today.
