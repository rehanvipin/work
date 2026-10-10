---
title: "Access Control"
weight: 4
---

# Access Control

All files are owned by a UID & GID. All processes are owned by a UID & GID.

To decide what rights a process has on a file this algorithm is applied:

if UID(proc) == 0:

apply root access

if UID(proc) == UID(file):

apply owner access

if GID(proc) == GID(file):

apply group access

else:

apply others access

DEBUGGING HELP!

Run set -x to show how the shell interpreted the command you entered, set +x to get it back to the old state.

What all actions a user with some access can do is set through permission bits of the file / directory:

- Read (read contents of file / list contents of dir)
- Write (write contents to file / create or delete in dir)
- Execute (execute the file if it is a program / cd into the dir)
- Set ID / sticky (execute the file with file's UID & GID, not users / finer write access for dir)

If the set ID bits are set on a file and it is copied, the bits are unset.

The sticky bit states that a user can delete a file only if they have write perms on the dir & if the user owns the file.

The permissions of a symlink are never used, the system looks at the actual file instead. When you chmod a symlink explicitly the perms of the file are changed instead.

The sticky bit can be set on the others permission only. Set ID can be set on the user or group only. The `=` in chmod can take ugoa on the LHS and rwxst on the RHS (it unsets the non-mentioned perms). Lower case s or t implies that x is also set, upper case means x is not set.

The octal representation of perms would be:

- [optional] setID user . setID group . sticky bit
- rwx (user)
- rwx (group)
- rwx (others)

The default permissions for a file / dir are obtained from the umask stored by the shell. The umask says what bits should be unset from 777 (i.e., masks them out to get the desired set of perms) for dirs and 666 for files. The default umask is 022.

UID / GID of a file is changed with chown & chgrp. UID / GID of a proc is changed with login & su / newgrp. su is used like so: `su - someonelse` or ksu for kerberos based envs.

The syntax for chown is `chown [OPTION]... [OWNER][:(GROUP)] FILE...`.

A user has one uid and one default gid (the one that's used for file perms and stuff, they could also have 16 other gids but those are used only for access control and stuff not for file perms).
