---
title: "Subshells and Functions"
teaching: 0
exercises: 0
questions:
- "How can you organise your code into functional blocks"
- "How can you import settings from other files into your scripts"
objectives:
- "Explain how BASH functions operate"
- "Explain the difference between local and global variables"
- "Explain how to import bash files into your environment"
keypoints:
- "functions can be used for easily repeating blocks of code"
- "you can store your study configuration and code separately"
---



## Subshells

In the shell novice course you were introduced to writing and using shell scripts,
http://swcarpentry.github.io/shell-novice/06-script/index.html

Invoking a shell script leads to the launch of a new child process, which processes the
commands within that script. Because a subshell is run in a new process, these can be
used for parallel processing (as we will look at in a future lesson). Here we will introduce
key concepts behind the use of subshells.

more reading: https://www.tldp.org/LDP/abs/html/subshells.html


## Functions

Functions can be used for repeating blocks of code within a script, in a similar manner to
calling a bash script from the command line.


## Sourcing Bash Scripts

We saw earlier how variables defined in a subshell cannot be used by the parent shell.



{% include links.md %}


