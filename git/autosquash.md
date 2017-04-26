# Autosquash

On a feature branch, you can have a history of "--wip--" and
"fixed typo" stuff. Upon creating a pull-request or merging it
into master branch directly, you may want to tidy up the whole
history so that it's not a dozen of dumb one-liners that fix each
other, produced in haste. There's a nice solution for that.

For every commit that fixes previous commit or generally refers
to the same area, do

```bash
git commit --fixup
```

For the whole branch, once it's ready, use

```bash
git rebase --autosquash <N>
```

where `<N>` is commit hash. Typically, when you know the number
of commits, you can refer to the previous, say, 5th commit as
"HEAD~5"

Then push it. If the history on your local branch and on remote
has diverged, force-push with lease:

```bash
git push --force-with-lease
```

Side-note: since the feature branch has to be one only you are
working on, there shouldn't be a problem when somebody else tries
to push to it and end up with rejected push and diverged history.
