---
title: "Filesystems"
weight: 2
---

# Filesystems

To view details about a directory instead of listing its contents use "-d" i.e. "ls -ld adirectory"

In Linux, the shell interprets wildcard expansion "*" and passes the result to the program.

To copy the contents of a directory to a new one use "cp -r dir1 dir2"

In case dir2 does not exist it will create it and directly put all of dir1's contents in it (only works when there is exactly one source to copy from and that source is a dir). If it does exist, dir1 itself will be copied into it.

To preserve mod time and perm bits, use the -p flag (preserve). Use -a to preserve ownership (preserve=all).

If a wildcard expansion does not match any file it is considered a file name itself.

Links:

Use "ln" to create a new name for the same inode. Both names (i.e., files) are equal since they represent the same file on disk, just with different names. When you use rm to delete one, only that will be deleted. Actually, rm just deletes names (i.e., links) of a file and only deletes the file when there are no more names for it.

Therefore it can be said that file names in Linux are just hard links to files on disk.

A hard link cannot be created for a file on a different partition or FS. That's where soft links (symbolic links) come in. Create one with 'ln -s og sym' and delete with 'unlink sym' or just 'rm sym' (rm internally calls unlink). A symlink is a separate file which contains a pointer to the og file. Since it is a new file on its own it has its own inode.

Try doing 'ln sym symcopy', it creates a new link for the symlink, not the og file.

This means a sym link can become invalid when the original file is deleted (it's called a broken link).

You can find broken symlinks like so: 'find . -xtype l' and delete them by passing the '-delete' flag.

'ls -li' shows the inode numbers as well

A disk usually has multiple "virtual disks" (partitions) which have their own file systems. One filesystem is chosen to be the root filesystem, the others may be "mounted" on different parts of the root. If the mount point is a directory that already contains some files then those files will be hidden while the other FS is mounted
It's not possible to unmount an FS while any of its files are in use.

## Network Files:

NFS is an extension to the unix file system. 
NAS devices are accessed through NFS on Unix and CIFS on Windows.


## Virtual memory:

A virtual memory address is made up of:

- Index to page directory - 10 bits
- Index to page table specified by PD - 10 bits
- Offset into physical page specified by PT - 12 bits

Each process has its own PD and PT. The location of the PD is stored in a control register. When the OS switches processes, it changes the value in the control register to point to the correct PD.


## inode

It doesn't contain the name of the file because that's file system dependent. A file is just a name for an inode. What it does contain is:

- id - some ids in the FS are reserved
- mode - basically the perms (rw-r--rwx). That's why it's called chmod
- uid and gid
- Timestamp
- Size
- Pointers to the actual data.
- The pointers could be multi-level (i.e., pointers to pointers to data and so on)

The kernel itself is not a process but a process manager. Unix has only one logical FS which could have mounts of multiple physical FSes.
