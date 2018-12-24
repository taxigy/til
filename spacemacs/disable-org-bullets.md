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
`1`. This is done via the setting called "customize-face". To go there, type

```
SPC SPC customize-face RET
```

and look up "org-level-1". In settings, set "Height" property to 1. Then look up
"org-level-2" and "org-level-3" and do the same. When updated, the settings will
be reflected in .spacemacs as a separate call to `custom-set-faces`, like:

```clojure
(custom-set-faces
 ;; custom-set-faces was added by Custom.
 ;; If you edit it by hand, you could mess it up, so be careful.
 ;; Your init file should contain only one such instance.
 ;; If there is more than one, they won't work right.
 '(org-level-1 ((t (:inherit bold :foreground "#4f97d7" :height 1))))
 '(org-level-2 ((t (:inherit bold :foreground "#2d9574" :height 1))))
 '(org-level-3 ((t (:foreground "#67b11d" :weight normal :height 1)))))
```

Hint: to leave the custom mode, do `C-x b` and switch to another buffer or `C-x
k` to kill current buffer.
