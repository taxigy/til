# Hard reset to remote branch

Consider a case when you and your friend are working on one branch, whatever the
reason. You both have the branch on your local machines. Now it's time to rebase
against master. Your friend does the rebase, squashes all the things, and the
history is a mess. All you need is to make git forget everything, take the
remote branch and completely overwrite your local one with it.

If you do

```bash
git pull the-branch
```

it will raise merging conflicts. You need to step back in history to a common
ancestor and then pull:

```bash
git reset --hard HEAD~50 # the number may vary
git pull the-branch
```

However, it turned out, resetting against remote branch works, too:

```bash
git reset --hard origin/the-branch
```

You may need to fetch from remote first, so the full command is

```bash
git fetch
git reset --hard origin/the-branch
```

which is easier because you don't need to guess the number of steps to step back
to find a common ancestor in history of commits.
