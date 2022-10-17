---
layout: work
worktitle: Creating Shell Commands Using Rust
tags:
- Operating Systems
- Easy
---

Implement the following shell commands as Rust programs. 
You might find the [fs crate](https://doc.rust-lang.org/std/fs/index.html) useful in 
writing many of these programs:

* `dir`: Prints out all of the names of the files and directories in the current directory. It will not employ any command-line arguments.
* `destroy`: Delete every file in the list of command-line arguments.
* `newname`: This program expects two command-line arguments. It will give a "usage" message if it does not receive them. It will change the name of the file given by the first argument to be the name given by the second argument.
* `duplicate`: This program also expects two command-line arguments. It will give a "usage" message if it does not receive them. It will make a copy of the file given by the first argument with the name given by the second argument.
* `start`: Prints out the first ten lines of each file listed in the command-line arguments. If the first argument begins with a dash, use the number immediately following the dash instead of ten.
* `counter`: Prints out the number of words, lines, and characters for each file listed in its command-line arguments. If the first argument begins with a dash, the letters "w", "l", and "c" immediately following the dash indicate which of words, lines, and characters get displayed.
* `findtext`: Output every line that contains a specified pattern. The first command-line argument is the fixed-string pattern. Remaining arguments are the names of the files to inspect.
* `order`: Works like `cat`, except the output lines must be sorted before being output. All lines from all files will be mixed together and then sorted. If the "-r" command-line argument is provided, they should be sorted in reverse order.

------------------------------------------------------------------------
