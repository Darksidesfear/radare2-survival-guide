# learn-radare2
A small, basic guide on how to get started on radare2.

## Glossary
[Introduction](#introduction)

[Installation](#installation)

[Seeking](#seeking)

[Basic Operations](#basic-operations)

[Analyzing](#analyzing)

[Information gathering](#information)

[Seeking](#seeking)

[Printing](#printing)

[Visual Mode](#visual-mode)

[Debugging](#debugging)

[Visual Panel Mode](#visual-panel-mode---)

[Graph Mode](#graph-mode)

[Special Utilities](#special-utilities)

[Further Help](#further-help)

## Introduction
The first thing you might need to know is that radare2 project is a framework. It's different tools : radare2, rabin2, rasm2 et cetera together. Hence, all commands are grouped together in different modules. There's a module for debugging, one for analyzing, et cetera.

And secondly, help is already built-in into radare. You don't have to memorize all the commands, you just have to know about the modules. You can add a '?' to whatever command you are running to see information about it.

I've displayed the module name in monospace like `this` on the titles, which is the word you can append the `?` to. 
I'll try to explain everything in the most easy way possible, and obviously, PRs and improvements are welcome.

---

## Installation
Installing it from the official GitHub repository is highly recommended as the development happens quite quickly and sometimes the other packages get left behind.
```
https://github.com/radareorg/radare2
sys/install.sh
```

---

## Basic operations
To open a file, just pass the file as an argument.

	`$ r2 <file_name>`

That'll load the file into radare2, you'll find yourself in the radare2 shell. But if you have to debug the program or patch it, you'll have to specify that as a command line argument.

To debug, add a `-d` before the name of the file.

	`$ r2 -d <file_name>`

And a `-w` for the writing mode, if you want to patch.

So, if you want to open it for debugging and writing, run :

	`$ r2 -d -w <file_name>`

---

After opening a file, you'll find yourself in the radare2 shell. That is also the memory address, where the seek is pointing to now.

	`[0x080488e0]>`

---

## `a`nalyzing

After loading up a file, radare2 doesn't know anything about file yet, and for that, we have to analyze, and the family name is `a`. And like I've said before, append a '?' to see the help.

	`[0x080488e0]> a?`

You'll see a trove different methods.

Beginners, you probably just need `aaa`. That'll get you everything you are looking for.

	`[0x080488e0]> aaa`

This includes a variety of methods including `af`, which analyses the functions.

Which means, you can `a`nalyse `f`unction `l`ist, to see the list of functions radare2 has discovered for you

	`[0x080488e0]> afl`

---

## `i`nformation
This one's used for extracting information about the file. Just like the `file` command in UNIX-like systems, but with more information.

	`[0x080488e0]> i`

Running that will get file information.

Most useful commands from this modules are `iS` and `ii`, which will display information about the sections in the file, along with the virtual memory address with it and `ii`, the imports.

`izz` prints all the ASCII strings in the binary. 

---

## `s`eeking
As, yo have the name of functions within your knowledge, you can seek to that function or any address of your choice.
	`[0x080488e0]> s <function_name> / <addr> `

You'll notice that the seek has changed to the address you've moved to:

You can also seek n bytes, undo a seek, and seek to a specific line.

More on `s?`

---

## `p`rinting
There are so many different, useful options available in the `p` module.

The `pd` submodule is about printing the disassembly. You can run just `pd` to print the disassembly from current address the seek is pointing to, to the end of the function, and `pdf` for printing the whole function.

The impressive one, in my opinion is the `pdc` command which prints the pseudocode of the disassembler output in C like format.

There's `pds`, too, which prints references of the string and call functions, which I seldom find useful.

---

## `V`isual mode
This is the most useful tool of radare2.

Simply, run `V` on the radare2 shell, to enter the Visual mode. You'll be in the Visual mode, with the seek at the same address you were in when you entered it.

Here, you can navigate using the arrow keys or `h,j,k,l`
You can see the seek changing, as you scroll up or down.

You can press `Tab` to cycle through the some of different flavors. In each of those flavors, what it's essentially doing it fiddling with the Visual Configurations.

And if you want to see the disassembly for a particular function, simply type in the string that is displayed as a comment.

```
           0x55de36c005c9      e87bfcffff     call sym.Random         ;[2]                                                                           
```
For example, press `2` to go inside Random. You can press `u` to come back to where you were before, after you are satisfied with the disassembly of the function. Let's call this the ID.

---

### Visual Configurations - `e`
By pressing `e` inside the visual mode, you'll enter the EvalSpace. You can change your configurations here. So, if you want to edit some settings regarding the disassembly, say the syntax, go inside asm, and likewise.

If you already know the name of the setting, you can set that from the shell inside Visual mode, too. To open the shell, type in `:`, just like in vim.

You can add comments to the address the seek is pointing to, by pressing `;`.

You can highlight a specific string, by typing that string after pressing `/`.

There are also different types of Visual modes, you can press `p` to cycle through them.

And if you're new to an architecture, run `e asm.describe` on the shell, which will describe each instruction in common english

---

### `d`ebugging
For debugging, you should the file up in debugging mode, using the -d flag.

	`$ r2 -d <file_name>`

You can place breakpoints with:

	`[0x080488e0] db <addr> `

And then run `dc` to start the debugging process and to continue after hitting a breakpoint.

You can write values using `dr [register]=[value]`

Check out `d?` and `db?`.

Although there is a certain type of mode in Visual mode, that displays the registers, it doesn't show you the register dereferences or stack.

For that, the Visual Panel Mode might help.

---

## Visual Panel Mode - `!`
Press `!` while in Visual mode to access the Visual Panel mode. You'll probably encounter three panels.

You can cycle through each tabs by pressing `Tab`. And press `"` to change the content of the selected panel.

There you'll find Register References, Imports and lots of other cool panels, which might suit your needs.

The shell can be summoned in the same way that you did in Visual Mode.

---

## Graph Mode

Graph Mode is probably why most people use radare2.

You can access, by running `VV` from the r2 shell.

Here, you can use your arrow keys or the letters to navigate and go inside functions in the same method like we did in Visual Mode.

You can change the default layout of the graph, the different boxes, by pressing `c` after selecting the branch by typing in the ID, and then use the arrow keys to place the box wherever you want.

---

## Special utilities
radare2 has a built in grep option, and the command is `~`. If you want to get a list of all the comparisons. You can:

	`:> pdf~cmp`

which will grep for 'cmp' in the disassembly of the function.

You can also print to STDOUT in JSON format, which is easier for scripiting, by appending j to the specefic commands. Like this:

	`pdj~{}`

---

## Further help
radare2 has a wonderful community online, and if you need any help, you can get on the [Telegram channel](t.me/radare).

I usually find my answers just be searching the chat history.
