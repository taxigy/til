# Xcode's "command line tools are already installed"

When this happens:

```
xcode-select: error: command line tools are already installed, use "Software Update" to install updates
```

do this:

```bash
sudo xcode-select --switch /Library/Developer/CommandLineTools
```
