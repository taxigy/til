# Disable org-bullets (and normalize heading font size)

The [org-bullets package](https://github.com/sabof/org-bullets) makes headings'
bullets look a little better than just asterisks. It's on by default in
Spacemacs and cannot be turned off by just removing it from the list of the
packages. We need to explicitly disable it by adding it to
`dotspacemacs-excluded-packages` collection:

```clojure
(defun dotspacemacs/layers ()
  ;; ...
  dotspacemacs-excluded-packages '(org-bullets)
  ;; ...
)
```

This way, Spacemacs won't load org-bullets.

You also might notice that first-level headers and second-level headers are
displayed in bigger font. To normalize them, we need to bring them to height of
1. This is done via the setting called "customize-face". To go there, type

```
SPC SPC customize-face RET
```

and look up "org-level-1". In settings, set "Height" property to 1. Then look up
"org-level-2" and "org-level-3" and do the same.

Hint: to leave the custom mode, do `C-x b` and switch to another buffer.
