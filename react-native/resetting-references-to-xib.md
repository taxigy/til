# Resetting references to .xib and .storyboard in existing Xcode project

Not trivial. I got an existing project, with quite mature codebase already, and
couldn't run it in iOS Simulator. The error I was getting, was

```
Interface Builder could not open the document "Whatever.xib" because it does not exist.
```

[The "Interface Builder could not open the document “.xib” because it does not
exist" topic on StackOverflow](http://stackoverflow.com/q/8968715/1287643)
helped a lot, but I had to do some extra maniulations. So, the algorithm is,

```bash
$ cd ~/dev/whatever/ # go to the React Native project folder
$ find . | grep xib # find all .xib documents, there's only one expected
$ find . | grep storyboard # find all .storyboard documents
```

Remove those documents and add again from the file system (details TBD, I hate
UIs).
