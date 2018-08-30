# Bash Cheatsheet and Quick Reference

## Introduction
This guide provides an overview to Bash, and is divided into sections on Introduction, Overview, Advanced, Awk, Sed, and Grep.  Note that there is overlap among the sections, such as the Advanced section describing piping in Bash using grep.  External links are noted by [links](), and bash commands are denoted by *italics*.  Some commands can be copied and pasted, however, caution should be used when copying and pasting from this guide, and **extreme** caution should be used when copying and pasting bash commands from an unknown or unverified source, because there is no "undo" when commands are executed from a Bash terminal window.

Key Concepts:

* Comments in bash are denoted with a hash # symbol:

    echo 'Hello World' # <- everything after here will be ignored by Bash
* a semicolon ";" or a newline denote the end of a command

    echo 'Hello World'

    echo 'Hello World' # no ';' required here as long as nothing else is typed

* The Bash terminal window accepts input and shows output and error.  By default, input is from typed keyboard commands, called STDIN, and output is from two sources: output from commands and error messages from commands.
  * Error messages from commands are called STDERR
  * Output from commands is called STDOUT, but it can be redirected to other places, like files or other Bash sessions.  Redirecting is a very important, but advanced concept.
* Note that unless specifically indicated, white spaces (\s, or ' ') are not allowed in Bash commands, or rather, assume that a white space will not do what you intend it to, so check the command carefully.
* Variables are stored in bash using a dollar sign *without a space*:
  * MYVAR="Hello World" # This stores "Hellow World" in the variable MYVAR
  * MYVAR = "Hello World" # WRONG!! This will produce an error
  * By convention, variable names use ALL CAPS, but this is not strictly required
  * To access a variable, use a dollar sign:

    echo ${MYVAR}

    echo $MYVAR # this is usually acceptable as well as a shorthand
* If you use numeric values, there are several ways to store the variable, and unfortunately there are some differences in Bash:
  * Storing the value usually can always be done by *let MYVALUE=3*
  * Performing arithmetic can be done using:
  *echo $((3-1))* # outputs 2
  * If you wish to make a comparison, you can also use:

    *if ((3 > 2)) ; then echo 'true' ; fi* # will output true

    Comparison will be covered in more detail in the Advanced section; however it should be noted that some versions of Bash do not support '(())', and you must use '-lt' and '-gt' instead:

    *if [ "3" -gt "2" ] ; then echo 'true' ; fi*
* If you have multiple values, you can use an array-this is covered in more detail in the Advanced section



## Overview
Bash, short for Born Again Shell, is a [command-line secure shell](https://www.gnu.org/software/bash/), or, a program that allows useful capabilities like job control, file manipulation, permissions modification, and script creating and execution.  Most Bash commands follow a similar set of rules, and *usually* have accompanying documentation that can be viewed by typing *man somecommand* or "manual somecommand", and if uncertain, available entries can be found by typing *apropos somecommand* or "apropos entries relating to somecommand".  However, some of the built-in documentation can be both extremely verbose and dense.  The Advanced section details a few tips for parsing help.

Bash syntax typically follows the format of

*command OPTION argument*

such as

*ssh -p 22 myname@myhost.com*

where:

*command* is the command to be executed, "ssh" in this case: open a secure shell to another host or computer.  Opening a secure shell to another computer creates a secure (encrypted) tunnel to another computer that no one else can see or eavesdrop into easily.  To execute this command, we must provide 2 additional things to Bash: a port and login information to the other computer.

*OPTION* is the option or options for the command.  Sometimes none are required, but in this case the option -p 22 is provided, directing the ssh command to use port 22 for the SSH login.  However, Bash knows that the default port is 22, so if that has not been changed on the computer that we are connecting to, then we can leave off this option, and Bash will assume that we want to use port 22.  The command would then be:

*ssh myname@host.com*


*argument* is something that we provide to the command.  If we want to open a secure shell connection, then we have to direct Bash exactly what computer we are connecting to, and what login credentials (name, password, etc.) that we are using.  We don't have to provide the password initially (also, providing the password with the command and argument may not be a good idea, because then the password would be saved and visible to anyone, but we must give it a location (host.com) and a login name (myname)).  Bash will then attempt to connect using SSH, and it will ask for a password to provide to the other computer, assuming that SSH is open and running on the computer at host.com

Another example would be

*echo 'Hello World!'*

The command *echo* tells Bash to repeat whatever is followed to the STDOUT output.  In this instance, you could simply type:

*echo Hello World*

which would be correct, because the command to bash would be "repeat anything following to the output".  The difference between the two commands is the first specifies that everything after is one string, while with the second Bash assumes we want the words typed to be a string (this is the case here, but as an exercise, try to think of a few times when this may not be desirable).


## Advanced

Bash can run multiple commands in succession, either as discrete steps:

  *echo 'One' ; echo 'Two' ; echo 'Three'* # outputs one two three

or in a series:

*cat 'file1.txt' | cat file2.txt | cat file3.txt > concatenatedfile.txt *

*# cat is a command short for concatenate, and it can be used to combine multiple files. *

*# the '>' is a redirect operator, and it redirects output from the default, in this case STDOUT, to the file concatenatedfile.txt*

*# the '|' is a pipe operator, and it is used to pipe input or data.  In this case, data is piped from the first command to the second comand, to the third command, then finally redirected to the file concatenatedfile.txt*

Bash binaries and executables usually will describe whether output will be to the screen (also called STDOUT), or whether it will be to a file, but you can alway redirect it somewhere else.


Bash has the capability to read in multiple items and store them in an **array**.  For example, to store the lines of the file myfile.txt into an array, and the values can be retrieved directly, or with a for loop:

*declare -a MYARR # tells Bash you are using an array with the variable name MYARR*
*OLDIFS="$IFS"*

*IFS=$'\n'*

*MYARR=($(<myfile.txt))*

*IFS="$OLDIFS"*

*# The IFS, or Internal Field Separator, tells Bash how to separate fields of text*

*#You can manipulate it, as we are doing here, to recognize newlines, but you should always keep the default value, or unpredictable things can happen*

*#the values are now stored in the array ${MYARR[@]}*

*echo ${MYARR[0]} # will echo the first value/line*

*for i in "${MYARR[@]}" ; do*

  *echo "${i}" ;# will echo all values*

*done*


Grep is a powerful search tool, and a full description of its use is beyonf the scope of this guide.  Very briefly, the syntax for grep is

*grep OPTION pattern string*

Where:

* grep is the command *grep*

* OPTION is one of numerous options for grep, a select few are:

  -Po use perl to search and output

  -v report anything that does NOT match the pattern

  -c report a count only

  -A*n* report *n* matches after the pattern, where *n* is the number; for example, -A5 will report the next 5 matches after the pattern

  -B*n* report *n* matches before the pattern, where *n* is the number, for example, -B5 will report the 5 matches before the pattern
* *pattern* is the search pattern that grep will search in the *string*.  Note that characters like '.', '!', or '()' have special meanings in grep, and if you are searching for a pattern with them, you must escape these using backslash: '\\.', '\\!', '\\)\\('
* string is the string or file that will be searched.  It is also acceptable to pipe something into grep:

  grep 'something' myfile.txt
  echo 'Hello World' | grep 'ello' # will match and output Hello World
