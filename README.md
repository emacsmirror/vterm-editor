# vterm-editor.el

Compose text in a full Emacs buffer and send it to vterm with a single keystroke.

![License](https://img.shields.io/badge/license-GPL--3.0-blue.svg)
![Emacs Version](https://img.shields.io/badge/Emacs-25.1+-purple.svg)

## Overview

When working inside a vterm terminal, editing multi-line text can be awkward: you lose access to Emacs keybindings, navigation, and editing commands. `vterm-editor` solves this by opening a temporary buffer where you can write comfortably using all of Emacs, then send the result back to vterm.

### Without vterm-editor

You type directly in the terminal, limited to basic line editing.

### With vterm-editor

```
┌─────────────────────────────────────────────────┐
│ Edit, then C-c C-c to send or C-c C-k to cancel│  ← header line
├─────────────────────────────────────────────────┤
│                                                 │
│  Write freely here with full Emacs power:       │
│  - Multiple cursors, search & replace, etc.     │
│  - Spell checking, auto-complete, snippets      │
│  - Any major/minor mode you want                │
│                                                 │
└─────────────────────────────────────────────────┘
        │ C-c C-c                │ C-c C-k
        ▼                        ▼
  Text sent to vterm        Buffer discarded
```

## Features

- **Full Emacs editing**: Write in a regular buffer with all your keybindings and modes available
- **Bracketed paste**: Sends text using vterm's bracketed paste mode, safe for multi-line content
- **Simple workflow**: `C-c C-c` to send, `C-c C-k` to cancel
- **Clean UI**: Header line shows available actions, no clutter in the buffer
- **Lightweight**: Less than 80 lines of code

## Installation

### Using `use-package` (Emacs 29+)

```elisp
(use-package vterm-editor
  :ensure t
  :vc (:url "https://git.andros.dev/andros/vterm-editor.el"))
```

Or from a local path:

```elisp
(use-package vterm-editor
  :load-path "~/path/to/vterm-editor")
```

### Manual installation

1. Download `vterm-editor.el`
2. Place it in your Emacs load path
3. Add to your init file:

```elisp
(require 'vterm-editor)
```

### With a keybinding in vterm

```elisp
(use-package vterm-editor
  :after vterm
  :bind (:map vterm-mode-map
         ("C-c e" . vterm-editor-open)))
```

## Usage

From any vterm buffer, run:

```
M-x vterm-editor-open
```

A new buffer `*vterm-editor*` opens in `text-mode` with `vterm-editor-mode` enabled. Write your text, then:

- **`C-c C-c`**: Send the text to the original vterm buffer and close the editor
- **`C-c C-k`**: Discard the text and close the editor without sending anything

The editor buffer is killed after either action, and focus returns to vterm.

## How it works

1. `vterm-editor-open` saves a reference to the current vterm buffer
2. A temporary `*vterm-editor*` buffer opens via `pop-to-buffer`
3. On `C-c C-c`, the buffer content is sent using `vterm-send-string` with the `paste-p` flag enabled (bracketed paste mode), which wraps the text in terminal escape sequences (`\e[200~`...`\e[201~`) so the shell treats it as pasted content rather than executed commands
4. `quit-window` kills the editor buffer and restores the previous window layout

## Requirements

- Emacs 25.1 or higher
- [emacs-libvterm](https://github.com/akermu/emacs-libvterm) (vterm)

## Contributing

Contributions are welcome! Please see the [contribution guidelines](https://git.andros.dev/andros/contribute) for instructions on how to submit issues or pull requests.

## License

This program is free software: you can redistribute it and/or modify it under the terms of the GNU General Public License as published by the Free Software Foundation, either version 3 of the License, or (at your option) any later version.
