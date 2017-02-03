# Elm on Ubuntu machine

Elm is installed via Node, so, for a clean Ubuntu 16.04 LTS
machine, the whole installation process would look like

```bash
sudo apt-get install nodejs # attention: not "node"!
sudo apt-get install npm
sudo ln -s `which nodejs` /usr/bin/node
npm install -g elm
```

After that, all the Elm toolbelt is available on the machine, and
you can run `elm-reactor`, `elm-make` and such.
