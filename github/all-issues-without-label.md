# All issues without label

Sometimes you want to select all issues with a particular label,
say, "Important". You then would use the query:

```
is:open is:issue label:Important
```

The opposite would be when you want to query issues and find all
of them that don't have that label. Use "-label" in this case:

```
is:open is:issue -label:Important
```

Good luck with all the unimportant issues!
