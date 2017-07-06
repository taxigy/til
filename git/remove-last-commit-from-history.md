# Remove last git commit from history (on remote)

Scenario: you created a feature branch, rebased history nicely, and then wanted to deploy a debug version full of prints to stdout; afterwards, you want to remove that last commit so that there's no rubbish in the code and in stdout anymore. And you don't really care about git history: removing one commit from it is okay as long as it happens in a feature branch and not in master.

Solution:

```bash
# make sure that local branch is in sync with its counterpart in remote repo
git reset HEAD^ --hard
git push -f
```

Done. Gone is the HEAD commit.

Use responsibly.
