---
title: "CPU"
weight: 5
---

# CPU
CPUs understand machine code. It's just binary stuff. It's a representation of assembly language. RISC computers need simpler CPUs than CISC ones. Pipelines improve throughput not latency. Works well with RISC as the instructions are easy to fetch/decode etc.

## Cache coherency:

- Write through - whatever is being written to cache, write to main as well
- Write back - the line is marked as dirty, when the line is being kicked out, the change is written to main
- Multi-cores (MESI protocol - line is tagged M or E or so on)
- Memory barrier instructions block all IO ops (useful for synchronization stuff).

Level 3 is closest to cache and is the smallest.

## Syscalls:

The only way for a user to invoke kernel functionality is through system calls (they are C functions because the kernel is written in C). The syscall function stores the syscall number and args in some memory loc, calls a trap instruction which causes the kernel code to run. The code checks the number and args, does the functionality and stores results in the user area memory. Then it transfers control back to the user code.

`strace` can be used to log the system calls (including the args) that a process makes and of the signals it receives. There's also `ltrace` for library calls.
The options it has are: (`-p pid` to monitor running procs, `-r` / `-T` for timing info, `-c` for a columnar summary, `-f` to see info of forked procs)

## IO:

Most of it can be done with `read`, `write`, `lseek`, `open`, `close`. Some meta ops can be done with `fcntl`, `ioctl` etc. There's also `pread`, `pwrite` for IO from particular locations in a file. For each process there's an fd table. Each entry points to a data structure in the global space which holds info like current file offset, pointer to its inode etc. If you write beyond EOF, the file's size grows. If you `lseek` and write some location much farther than EOF the file is called a sparse file. The gap appears to be full of null characters may not actually occupy much disk space! (an optimization for sparse files). `du` or `ls -s` will show the actual size on disk.

`dup2(a, b)` will make fd b point to the same data structure as what 'a' points to.

IO can also be done via a block of memory that's mapped to a file's contents. This is faster, in fact the kernel may already be doing this behind the scenes. It's done via `mmap`.