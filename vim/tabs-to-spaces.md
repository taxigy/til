# Overriding default indent options in Vim

By default, Vim indents with tabs and displays each tab as 8 character-long
unit. To override this in current window, do

```vim
:set ts=2 sw=2 et
```

Here,

- `ts` stands for "tabstop", an option responsible for "stopping" the cursor
  after a certain number of characters,
- `sw` is for "shiftwidth", which makes each tab "look" like a certain number of
  spaces, and
- `et` is for "expandtab", which will convert newly inserted tabs (literally
  press on Tab button) into "soft tabs", known as spaces.

From Vim docs:

> Set 'tabstop' and 'shiftwidth' to whatever you prefer and use 'expandtab'.
> This way you will always insert spaces. The formatting will never be messed up
> when 'tabstop' is changed.

To change this once and forever, add these lines to .vimrc:

```vim
set tabstop=2
set shiftwidth=2
set expandtab
```

although you'd probably want these settings to be project-specific and taken
from .editorconfig or similar configuration file.
