---
title: Messaging
---

TODO
How good is the microcopy in a CLI tool...

<!--more-->

## Progress bars

If your tasks takes too long, maybe because its complexity or because it depends on the Network, it’s reasonable to show progress with a Progress bar, that won’t be included in the final output, or won’t be outputted in an non-interactive (non-tty) terminal.

See How to make a progress bar?

## Error messages

...
See: debugging

## Success message

Unless the result of the CLI command should be the output, like in case with `ls`, consider _no output_. If no errors or unexpected behaviors happened during the CLI’s execution, exiting your program with a 0 exit code might be enough. It’s tempting to print out a success message, or a message describing a new state after command finished, but maybe it’s not needed. Consider `cp` command - [EXAMPLE OF CP WITH EXTRA OUTPUT](#)

[https://www.linuxtopia.org/online_books/programming_books/art_of_unix_programming/ch11s09.html](https://www.linuxtopia.org/online_books/programming_books/art_of_unix_programming/ch11s09.html)
