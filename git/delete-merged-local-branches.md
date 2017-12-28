# Delete all local branches that have been merged

First, get all the branches that have been merged:

```bash
$ git branch --merged
  feature-xyz
  feature-def
* feature-abc
  master
```

then remove the asterisk:

```bash
$ git branch --merged | grep -v \*
  feature-xyz
  feature-def
  master
```

then also remove the master branch from the list:

```bash
$ git branch --merged | grep -v \* | grep -v master
  feature-xyz
  feature-def
```

then join them into a single string:

```bash
$ git branch --merged | grep -v \* | grep -v master | xargs
feature-xyz feature-def
```

then pass it to `git branch -d` as args:

```bash
$ git branch -d `git branch --merged | grep -v \* | grep -v master | xargs`
```

But because `xargs` can apply the arguments to the command that
follows it, it all can be reduced into plain

```bash
$ git branch --merged | grep -v \* | grep -v master | xargs git branch -d
```
