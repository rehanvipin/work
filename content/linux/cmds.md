---
title: "Commands"
weight: 1
---

# Commands

For `tail` or `head` you can use the -c option to select bytes instead of lines. Tail has the option -f (follow) which lets you watch changes to the file.

diff has the -e option which outputs commands you can pass to ed to make one file like the other.

The default delim for cut is TAB. If multiple chars are specified for DELIM, the last one will be taken.

paste takes lines from each file and pastes them together with a delimiter (TAB by default). In case a file doesn't have lines but the other does paste uses an empty string in place for that file.

uniq has the option -c (count dupes), -d (show only lines which have dupes), and -u (show only unique). With no options matching lines are merged with the first.

wc has -l, -w, and -c

grep can only indicate there is a match on the line, not how many.

who has the option "am i" "is that" "was that" and so on.

When performing redirection to a file that doesn't exist, first the file will be created then the command run.

Ctrl+C kills a process while Ctrl+Z stops a process (allowing it to be resumed later on)

Change date: https://unix.stackexchange.com/a/392807 e.g.: date -s "-4 hours - 30 minutes"

Determine who you are: "who am i"

List files that have numbers in their name: "ls | grep [0-9]"

Some points to note here. Ls prints its output depending on what the stdout is. In case of printing to the terminal it does some formatting and stuff but if you are piping it to another command it will print one file name per line. Cool stuff!

It is not recommended to parse the output of "ls -i" and stuff http://mywiki.wooleidge.org/ParsingLs

Display "processes of interest" : ps

Show full details about procs: ps -f

Display all processes: ps -e

Display all processes created by a user: ps -u $USER (full details? Add -f after this)

Less is a pager (hard to define that). It's commands are similar to more and vi's commands.

Cut is pretty cool. A common way to use it would be: cut -d "somedelimiter" -f whichfieldtotake.

Find all files which have the word "Lion" -> grep -l Lion ./*

When you exit a shell, it also kills its jobs except if those were prefixed with nohup while running. (it's description says "Run COMMAND, ignoring hangup signals.")

Noclobber prevents you from overwriting files with '>', they can be overwritten with '>|'.

Set FCEDIT=vim then check out fc which lets you edit commands in history and run them again. Fc -l to show old commands and their numbers, fc <command-num or="" command-name=""> to edit and run one of those old ones.</command-num>

## Editing

ed is always available and can be used in scripts too! It has a command mode and insert mode. It's interactive, buffered and line-oriented. There's a nice cheat sheet: https://raymii.org/s/tutorials/ed_cheatsheet.html

sed is non-interactive, non-buffered and stream-oriented (read from STDIN, write to STDOUT). It still works on lines of text. Some differences from ed are that without an address, a command applies to the whole file. It also prints everything by default and stuff has to be deleted not to be printed. If it's run with -n nothing is printed by default and stuff has to be printed via commands. https://quickref.me/sed or http://eriklievaart.com/cheat/linux/shell/sed.html .

Vi reads the ~/.vimrc file for configs.

Using ed:

Open with prompt: ed -p '>'

Exit: q or Q (for no save)

Start editing into buffer: i (insert before current line), a (insert after current line)

Go back to command mode: enter '.' on a new line

Save: w filename (or just w if the filename was a CLA).

Navigate: enter line number (or $ to go to last). pn will print line and tell line number. +/-shift will move that much up or down.

Printing: ,p will print entire buffer.

Regex: g/regex/whattodowithit (the last part can be omitted as it will print by default, else p for print the found line, d for delete). Replace g with v to print lines that don't match.

Substitute: 1,$s/regextomatch/stufftoreplace/g will replace every occurrence in the buffer.

Copy: <startline>,<endline>t<line-number-to-insert-after>

Sed stuff: sed [options] command [filename]

Replace every occurrence on every line: sed 's/old/new/g' filename (use -i for in-place edit and -E for extended regex)

Delete some line: 'line-num d' (it could also be a range)

Print some lines: 'line-num p' (same as above) or '/stufftomatch/p'

Sed prints everything, use the -n to not do that.

Output a tab between ttyp and the corresponding number in Userlist, if ttyp is not followed by a number then there should not be a tab: sed -E 's/(ttyp)[0-9]+)/\1\t\2/' userlist
