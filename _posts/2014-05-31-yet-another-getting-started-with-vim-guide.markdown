---
layout: post
title:  "Yet another “Getting started with Vim” guide"
date:   2014-05-31
---

Coding is more thinking that writing, and the less thought you have to put into the writing, the more you can put into the thinking. [Vim][] has a notoriously steep learning curve, largely because it functions very differently from the type of text editor most people are accustomed to using, but I’ve found that developing even a basic familiarity with the editor is enough to reduce the amount of mental overhead you have to devote to getting the squiggles you want onto the screen, leaving more brainpower for more useful kinds of thinking.

## Opening Vim

1. Open a [CLI][].
2. Type `vim` and hit enter. You’re now in Vim.

## “How do I type things?”

3. Don’t try typing yet; it won’t work and might do crazy things.
4. This is because Vim starts in command mode, which is sometimes referred to as normal mode. In this mode, many single key presses will execute commands rather than inserting the corresponding character.
5. Hit `i` to enter insert mode. (This is the only mode most text editors have.)
6. Type some stuff!
7. When you’re done, hit `esc` to go back to command mode.

## Navigating

8. To quit without saving, type `:q!` and hit enter.
9. Navigate to a directory with a file with some code in it, then type `vim [filename]` to open that file in Vim. Remember that Vim starts in command mode.
10. You don’t use your mouse in Vim. To navigate, use `j` and `k` for up and down, and `h` and `l` for left and right. It’s tempting just to use the arrow keys for navigation, but using these letters keeps your right hand in position as you learn more Vim commands.

Knowing how to open and close files, switch between command and insert modes, and navigate a file with `h`, `j`, `k`, and `l` is all you need to get started with Vim, though using it would be pretty frustrating if you were limited to them alone. There are a crazy, crazy number of possible commands, and much of Vim’s power comes from the ability to chain commands together, though a description of how to do this isn’t really in the purview of a “getting started” tutorial.

Instead, here’s a reference for what are in my opinion the most essential commands for the Vim newbie. (Note that commands prefaced with a **`:`** will only execute once you hit enter, as typing a colon brings up a command bar in the lower left corner. The others will execute immediately, without bringing up this command bar.)

* `i` to enter insert mode, `esc` to get back to command mode
* `:w` to save, `:q!` to quit without saving
* `u`/`ctrl`-`r` to undo/redo

---

* `h`/`j`/`k`/`l` to go left/down/up/right
* `w`/`b` for next/previous word
* `}`/`{` for next/previous paragraph

---

* `x` to delete a character
* `dw` to delete a word
* `dd` to delete a line

---

* `:vs` to split the screen into two vertical splits
* `ctrl`-`w` and a direction key to switch between splits
* `:E` to bring up a directory tree to open a different file

There are also many ways to modify and extend Vim. While I’d suggest using Vim in its unmodified state for at least a little while, here are some of the modifications I consider essential. (In order to use these, create a file called .vimrc in your home directory, then add these to it.)

* `syntax on` for syntax highlighting

---

* `set number` for line numbers

---

* `set tabstop=2`
* `set softtabstop=2`
* `set shiftwidth=2`
* `set expandtab` to set tabs to 2 spaces

Finally, here are two highly recommended, (Mac) operating system-level changes that will make using Vim a lot easier. Go to System Preferences > Keyboard, then do the following:

* Turn “Key Repeat” to the fastest and “Delay Until Repeat” to the shortest.
* Go to “Modifier Keys”, then remap caps lock to control (many Vim commands involve the control key, and the caps lock key is a much bigger target that no one uses anyway).

[Vim]: http://en.wikipedia.org/wiki/Vim_(text_editor)
[CLI]: http://en.wikipedia.org/wiki/Command-line_interface
