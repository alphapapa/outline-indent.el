# outline-indent.el - Emacs Package to Outline and Fold Text Using Indentation Levels

The `outline-indent.el` Emacs package provides a minor mode that enables code folding and outlining based on indentation levels for various indentation-based text files, such as YAML, Python, and other indented text files.

In addition to code folding, `outline-indent.el` allows moving indented subtrees up and down, promoting and demoting sections to adjust indentation levels, customizing the ellipsis, and inserting a new line with the same indentation level as the current line, among other features.

The `outline-indent.el` package leverages the built-in *outline-minor-mode*, which is maintained by the Emacs developers and has less chance of being abandoned like *origami.el*.

![](https://raw.githubusercontent.com/jamescherti/outline-indent.el/main/.screenshot.png)

![](https://raw.githubusercontent.com/jamescherti/outline-yaml.el/main/.screenshot.png)

The `outline-indent.el` Emacs package offers a similar functionality to Vim's `set foldmethod=indent` setting. Just as in Vim, it allows to fold and unfold code sections based on their indentation levels.

<!-- markdown-toc start - Don't edit this section. Run M-x markdown-toc-refresh-toc -->
## Table of Contents

- [Installation](#installation)
    - [Install using straight](#install-using-straight)
- [Activation](#activation)
- [Usage](#usage)
    - [Functions specific to outline-indent-minor-mode](#functions-specific-to-outline-indent-minor-mode)
        - [outline-indent-promote and outline-indent-demote](#outline-indent-promote-and-outline-indent-demote)
        - [outline-indent-move-subtree-up and outline-indent-move-subtree-down](#outline-indent-move-subtree-up-and-outline-indent-move-subtree-down)
        - [outline-indent-insert-heading](#outline-indent-insert-heading)
    - [Vanilla Emacs](#vanilla-emacs)
    - [Evil mode](#evil-mode)
- [Frequently asked questions](#frequently-asked-questions)
    - [Maintaining blank lines between folded sections](#maintaining-blank-lines-between-folded-sections)
    - [Why not use origami.el?](#why-not-use-origamiel)
- [License](#license)
- [Links](#links)

<!-- markdown-toc end -->

## Installation

### Install using straight

To install the `outline-indent` using `straight.el`:

1. If you haven't already done so, [add the straight.el bootstrap code](https://github.com/radian-software/straight.el?tab=readme-ov-file#getting-started) to your init file.

2. Add the following code to your Emacs init file:
```
(use-package outline-indent
  :ensure t
  :straight (outline-indent
             :type git
             :host github
             :repo "jamescherti/outline-indent.el")
  :custom
  (outline-indent-ellipsis " ▼ "))
```

## Activation

### Manual activation

Once installed, the minor mode can be activated using:
```
(outline-indent-minor-mode)
```

### Auto activation

The minor mode can also be automatically activated for a certain mode. For example for Python and YAML:
```
;; Python
(add-hook 'python-mode-hook #'outline-indent-minor-mode)
(add-hook 'python-ts-mode-hook #'outline-indent-minor-mode)

;; YAML
(add-hook 'yaml-mode-hook #'outline-indent-minor-mode)
(add-hook 'yaml-ts-mode-hook #'outline-indent-minor-mode)
```

### Adjusting the shift width

You can adjust the `outline-indent-shift-width` according to your preferences. While the default value of 1 is adequate for most modes, setting the appropriate value ensures that the promote and demote functions correctly adjust the indentation of blocks. For example:
```
;; Python
(dolist (hook '(python-mode python-ts-mode-hook))
  (add-hook hook #'(lambda()
                     (setq-local outline-indent-shift-width 4))))

;; YAML
(dolist (hook '(yaml-mode yaml-ts-mode-hook))
  (add-hook hook #'(lambda()
                     (setq-local outline-indent-shift-width 2)))
```

This configuration sets different shift widths for Python and YAML modes, allowing for more precise control over the indentation of indented blocks during promotion and demotion.

(By default, `outline-indent-default-shift-width` is nil, which means it uses the same value as `outline-indent-default-offset`, which is 1 by default.)

## Usage

### Functions specific to outline-indent-minor-mode

#### outline-indent-promote and outline-indent-demote

*(By default, `outline-indent-advise-outline-functions` is set to t, which means that you can also use the built-in outline functions `(outline-promote)` and `(outline-demote)` as an alternative to `(outline-indent-promote)` and `(outline-indent-demote)`)*

These functions can be used to decrease and increase the indentation level of indented blocks.

To increase indentation:
```
(outline-indent-demote)
```

To decrease indentation:
```
(outline-indent-promote)
```

The global variable `outline-indent-default-offset` is used to determine the number of spaces to indent or unindent the subtree.

#### outline-indent-move-subtree-up and outline-indent-move-subtree-down

*(By default, `outline-indent-advise-outline-functions` is set to t, which means that you can also use the built-in outline functions `(outline-move-subtree-up)` and `(outline-move-subtree-down)`, as an alternative to `(outline-indent-move-subtree-up)` and `(outline-indent-move-subtree-down)`)*

These functions can be used to move the current subtree down past ARGS headlines of the same level.

To move the subtree down, use:
```
(outline-indent-move-subtree-down)
```

To move the subtree up, use:
```
(outline-indent-move-subtree-up)
```

#### outline-indent-insert-heading

*(By default, `outline-indent-advise-outline-functions` is set to t, which means that you can also use the built-in outline function `outline-insert-heading` as an alternative to `outline-indent-insert-heading`)*

The `(outline-indent-insert-heading)` function inserts a new line with the same indentation level/depth as the current line just before the next heading that shares the same or less indentation level. It finds the nearest non-empty line with the same or less indentation as the current line and inserts a new line before it.

In `outline-indent-minor-mode`, where most lines are treated as headings, this function is suitable for maintaining consistent indentation within the outline structure. It can be used as an alternative to `outline-insert-heading` to insert content at the same indentation level after the current fold.

Example usage:
```
(outline-indent-insert-heading)
```

### Vanilla Emacs

Use the standard `outline-mode`/`outline-minor-mode` commands to fold and unfold sections of your indented file:
- `outline-hide-body`: Hide all body lines in buffer, leaving all headings visible.
- `outline-hide-other`: Hide everything except current body and parent and top-level headings.
- `outline-hide-entry`: Hide the body directly following this heading.
- `outline-hide-leaves`: Hide the body after this heading and at deeper levels.
- `outline-hide-subtree`: Hide everything after this heading at deeper levels.
- `outline-show-children`: Show all direct subheadings of this heading.
- `outline-hide-sublevels`: Hide everything but the top LEVELS levels of headers, in whole buffer.
- `outline-show-all`: Show all of the text in the buffer.
- `outline-show-entry`: Show the body directly following this heading.
- `outline-show-subtree`: Show everything after this heading at deeper levels.
- `outline-show-branches`: Show all subheadings of this heading, but not their bodies.
- `outline-show-children`: Show all direct subheadings of this heading.

You can also indent/unindent and move subtree up and down using:

- `(outline-indent-demote)` and `(outline-indent-promote)`: Indent or unindent the entire subtree.
- `(outline-indent-move-subtree-down)` and `(outline-indent-move-subtree-up)` to move the current subtree up or down.
- `(outline-insert-heading)` to insert a new line with the same indentation level/depth as the current line just before the next heading that shares the same or less indentation level.

### Evil mode

In Evil mode, `outline-indent` works out of the box, and you can use the Evil keyboard mappings: zo, zc, zO, zC, za, zr, and zm to manage folds.

You may want to set a few additional key mappings:
```emacs-lisp
(with-eval-after-load "evil"
  (defun my-evil-define-key-outline-indent-minor-mode ()
    ;; Set `M-h` and `M-l` to decrease and increase the indentation level of
    ;; indented blocks
    (evil-define-key 'normal 'local (kbd "M-h") #'outline-indent-promote)
    (evil-define-key 'normal 'local (kbd "M-l") #'outline-indent-demote)

    ;; Set `M-k` and `M-j` to move indented blocks up and down
    (evil-define-key 'normal 'local (kbd "M-k") #'outline-indent-move-subtree-up)
    (evil-define-key 'normal 'local (kbd "M-j") #'outline-indent-move-subtree-down)

    ;; Set C-<return> to insert a new line with the same indentation
    ;; level/depth as the current line just before the next heading
    (evil-define-key '(normal insert) 'local (kbd "C-<return>")
      (defun my-evil-outline-indent-insert-heading ()
        (interactive)
        (outline-indent-insert-heading)
        (evil-insert-state))))

  (add-hook 'outline-indent-minor-mode-hook #'my-evil-define-key-outline-indent-minor-mode))
```

## Frequently asked questions

### Maintaining blank lines between folded sections

The `outline-blank-line` variable can be set to `t` (true) to maintain blank lines between folded sections, making it easier to distinguish between folds:

```
(setq outline-blank-line t)
```

### Why not use origami.el?

The `origami.el` package is no longer actively maintained and has known bugs that can affect its reliability and performance.

On the other hand, `outline-indent.el` leverages the built-in `outline-minor-mode`, which is actively maintained by the Emacs developers.

## License

Copyright (C) 2024 [James Cherti](https://www.jamescherti.com)

This program is free software: you can redistribute it and/or modify it under the terms of the GNU General Public License as published by the Free Software Foundation, either version 3 of the License, or (at your option) any later version.

This program is distributed in the hope that it will be useful, but WITHOUT ANY WARRANTY; without even the implied warranty of MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE. See the GNU General Public License for more details.

You should have received a copy of the GNU General Public License along with this program.

## Links

- [outline-indent.el @GitHub](https://github.com/jamescherti/outline-indent.el)
