---
title: "Shell"
weight: 3
---

# Shell

A shell needs to do three main things - be a command line interpreter, provide a programming languages and have capabilities for job control.

The shell prepares and gets the file handles for the procs before they are executed. That's why sort file > file leaves the file empty. tee filename writes STDIN to the file and to STDOUT. Programs running the bg need to have their STDIN & STDOUT redirected. Composition:

- Serial: a; b; c
- Parallel: a & b & c

Grouping commands can set all their STDOUTs at once or make them all run in the bg or something.

cd is a shell builtin otherwise if it ran as a child process and changed the pwd, the parent's wouldn't change. Therefore it doesn't create a new process for the command. Fun fact! This is valid: `>datafile <datafile sort`.

All the processes in a command are together known as a job. Jobs can have job numbers (when they are run in the background). The process id displayed on running `jobs` is the process id of the latest process in the job (or the last in the command if they're running in parallel).

To send a signal to the job, use `kill -s SIGNAME %jobID` (instead of SIGNAME, SIGNUMBER can also be used). Common signals are 9 for hard kill and 15 for request for termination.

Kill can also be used with processes in which case replace `%jobID with PID`. Kill -0 will kill all processes in the same process group as the shell.

Job scheduling can be done with `cron` (for repetitive jobs) and `at` (for one-off jobs). Cron runs according to a timetable. It can be set through crontab. Cron is a system daemon, when it dies it loses its timetable. A persistent timetable could be stored in the `/usr/spool/cron/crontabs/` dir through crontab if wished. Crontab could be used to add schedules to the current timetable interactively or could be used to edit the file for persistence.

Crontab entries are of this format: `min hour day-of-month month-of-year day-of-week command-for-job`. Everything there is 0 indexed except for month-of-year, a value could also be set to `*` in case of a don't-care. It could also be a range (via `-`) or a set of values (via `,`)

Job numbers are only for that shell. They are not unique system-wide.

## Variables:

Local vars are only available in that shell process. Env vars are visible to the current process and it's children. If the child creates or modifies an env var it won't be visible to the parent though (it's children can access it). There are many pre-defined ones for each shell, there are some set by the system that available to all shells. `set` displays local and env vars, `env` only displays env vars. Do `lol=abc` to create a local var and `export lol` to make it an env var.

MS has the "module" system which adds programs to your PATH when you run the commands. It has avail, list, display, unload, load.

Double quotes will quote everything but $, single will quote everything. Back quotes allows output of a command to be substituted in its place e.g. `pwd` will be equivalent to "/home/singssong/wow", a modern alternative is $(pwd) which does the same thing. REMEMBER THAT THE COMMAND IS EXECUTED FROM A SUBSHELL.

Instead of editing $HOME/.profile and stuff (which is managed by the system), edit $HOME/.custom/.profile.

More on shell expansions : https://ss64.com/bash/syntax-expand.html

There's also the curly brace syntax which can be used for:

- Variable expansion like so: "wowoww$(somevar)" if somevar is lol becomes wowowwlol

## Unix utilities:

- Zgrep to grep through compressed file
- Nroff for text formatting (it's used to format the man pages which are stored as roff files). Does justification by default.
- Spell goes through a file to see if there are any words which aren't in the dictionary. To see if a single word is in the dictionary use the look command
- Tr
- Whereis (pre-defined set of dirs), which (searches in PATH), whence (ksh only) do the same thing
- "find [places to start looking] [what to look for] [what to do with the found items]"

- In the what to look for, you can use -name, -user, -size, -type and so on (if you want to use multiple options you need to separate them with the -o flag and for negate of an option place a ! Before it)
  - In the what to do, you can -ls, -print, -exec and so on (in exec, the current file is represented by {})
  - The found items may be relative or absolute path names (based on how the path of places to start looking is specified)

Variable expansion happens after IO redirection.

eval evaluates the stuff after it as if it was a normal command execution.

$$ is a special variable which stores the PID of the current proc.

The directory file is very simple. It only holds the mapping of inode numbers to file names. When a file is deleted in that directory, the file name still exists in the dir file but the inode is set to 0. That file name may be overwritten when a new file comes in later on. That's one reason dir files don't get smaller.

Process substitution is a cool trick. It's not present in every shell though. It allows you to put the output of a command into a temporary file for use in a larger command. It works like this:

ls dir1 > files1

ls dir2 > files2

diff files1 files2

Can be done as

diff <(ls dir1) <(ls dir2)

It could also be done for STDIN like so: >(abs)

They're mainly used for places where the pipeline is insufficient and when subshells or temporary files need to be avoided. For fun try out vim <(ls).

To create / delete / do stuff with a file called "-t", make sure to use -- e.g., touch -- -t, rm -- -t and so on.

There are some pre-defined regular expression character classes like [[:alnum:]] [[:upper:]] and so on. They can be used in sed too and they are found here: https://www.gnu.org/software/grep/manual/html_node/Character-Classes-and-Bracket-Expressions.html. Here is an example with sed replacing every non-alphabet with empty char: sed 's/[':\alpha:]]\v//'

CHECK OUT AWK!!!

Awk syntax: awk <awk program> <file> (instead of a file, stdin will also do)</file>

The awk program is (simplified form) just a sequence of one or more '[optional cond] { action };' all of them together have to be quoted with a single quote. The action will be run only if the condition is met and is print $0 be default.

Some special variables are there:

$0 (current line), $1 $2 .. (fields of current line), NF (num of fields), NR (current line num), FS (input field sep, a regexp, matches spaces and tabs by default), OFS (output field sep). Print can be like "print item1, item2, ..." (without the commas the items will be concat).

Conditions can be stuff like /regex/ or comparison, boolean operations and so on. e.g. 'NF > 6', '$2 ~ /regex/' and so on.

Double quotes only preserve $, ', \, and !(optionally, when history option is enabled)

Double hyphens can be used in some commands to denote the end of options, e.g., in this case
git diff --stat -- --file1 --file2

'--file1' and '-file2' are both read as file names, the file names could also have been specified as ./--file and ./--file2 .

Globbing uses a different set of chars from RE. It allows *, ?, char sets within square brackets, range/list expansion with {}, and ^ for inverse matching.