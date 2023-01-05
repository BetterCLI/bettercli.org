---
title: Colors and formatting in the output
---

Learn how you can make your CLI application output colors and how to use them to your advantage. And when to avoid formatting in the terminal.

<!--more-->

You can make information stand out by applying text formatting like bold, italics, or text/background colors. However, you should be careful with colors in terminal applications. And there are many situations where you shouldn’t use colors at all.

See some notes on accessibility in the “How does CLI text formatting interact with color schemes?” below.

## Technical implementation

Colors are supported by most modern shells and terminals that you might encounter. There are some platform-specific gotchas and limitations. If you are planning to use text styles a lot, it’s a good idea to use a library, that will handle these edge cases.

The best way to color your terminal output is to use ASCII escape sequence. Those are _hidden_ control characters that looks like this `\033[31m` This will print following text in red.

1. start with an [ASCII escape character](https://en.wikipedia.org/wiki/Escape_character) `\033`, `\u001b` or `^[` depending on a language of your choice
2. continue with `[` and a [formatting code](https://en.wikipedia.org/wiki/ANSI_escape_code#Description)
3. close with a character `m`

Everything after this sequence will have the set formatting. You can combine formatting sequences e.g., bold`\033[1m` red`\033[31m` text for fatal error.

Also see: [https://tldp.org/HOWTO/Bash-Prompt-HOWTO/x329.html](https://tldp.org/HOWTO/Bash-Prompt-HOWTO/x329.html)

### How does CLI text formatting interact with color schemes?

Because modern terminals support user-defined themes, colors and contrast are highly unpredictable. Basic colors are often defined by the user/system theme. You’ll never get this right. It’s safer to keep style formatting as simple as possible.

That said, if you use extended [ASCII 8bit colors scheme](https://en.wikipedia.org/wiki/ANSI_escape_code#8-bit), that looks like this `\033[38;5;`character between 0-255`m`, these colors are fixed in place, and should render the same on all terminals and systems.

When defining a background color also define the text color. You’ll prevent incompatibilities with light/dark themes.

### Notes on backspace escaping aka overstriking[^1]

Dated practice, is not recommended as terminals often ignore it. Still supported by Pager programs. Works by simulating a typewriter behavior, using a Backspace Escape character to move the cursor one character back and overtype it to make it bold. Still used by manpages.

This will lead to a silly rendering in some text editors, e.g., the bolded word “**OPTIONS**” will show up as `OOPPTTIIOONNSS` with unprintable backspace characters scattered between the letters.

## When not to use colors or formatting?

There are situations, where you should strip colors from your terminal output.

### When the output is not terminal

When the output is being parsed or searched with grep, and it has formatting in the output it’s harder to search/parse it.

```bash
 ~/ echo "\e[1mhell\e[0mo"
hello
 ~/ echo "\e[1mhell\e[0mo" | grep hell
hello
 ~/ echo "\e[1mhell\e[0mo" | grep hello
 ✘  ~/


```

Good way to account for this, is by detecting if program output is being piped somewhere and disabling color output if output is not directed to a tty.

```bash
$ multipush | grep string
```

### When NO_COLOR is set

If `NO_COLOR` environment variable is present, your application shouldn’t output any colors. Per [no-color.org](https://no-color.org).

[^1]: [https://unix.stackexchange.com/questions/118707/what-is-the-name-of-the-formatting-output-from-man](https://unix.stackexchange.com/questions/118707/what-is-the-name-of-the-formatting-output-from-man)
