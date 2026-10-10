---
title: "Parallelism"
weight: 6
# bookFlatSection: false
# bookCollapseSection: false
# bookComments: true
---

## Parallelism

Concurrent system - more than one task makes progress within a given time interval.
Parallel system - more than one task can execute at the same instant.

The CPU schedules threads.

How often does the scheduler schedule tasks? After every time quantum (usually a clock cycle) it looks for tasks that have same / higher priority to schedule. This is only for a timesharing OS (aka preemptive scheduler). For a non-preemptive scheduler (aka cooperative multitasking OS) it doesn't actively try to preempt a task. The scheduler will also kick out a task only if a higher priority task comes in (priority of task increase over time to avoid starvation) or the task has done a blocking call. Unix & newer Linux kernels are preemptive while windows & mac are non-preemptive ones (lower turnaround time but limited choice in scheduling algorithms).

Sleeping state vs blocked state? Sleeping is something the thread decides to do, it also has a fixed duration. Blocking is something the OS forces the thread to do because it asked for a resource.

It's possible to set an interrupt flag on a thread (in java), you can also check if the thread has the flag on it, or even check the interrupted status of itself. If that thread is blocked (e.g., because of sleep, wait, or join), then an exception is thrown inside that thread (which clears the flag). A thread can only interact with other threads in the same group.

An exception that's not handled in a thread will kill the thread. It's possible to set a handler for unhandled exceptions in a thread (or even for all threads).

In java, every object has a mutex lock, only one thread can hold it at a time. It's possible to tag methods of a class as synchronized and then only one thread can call that method at a time on an object (it basically holds the lock of that object and releases it when the method gets over). What if you didn't mark a method as synchronized during class definition? You can create a synchronized block over an object e.g., synchronized (objkt) ( /* do whatever. This basically holds the lock of that object */ ). It has also the wait, notify, and notifyAll methods. The wait method blocks the calling thread till some notification is sent. NotifyAll resumes the threads that are waiting. These methods can only be called within synchronization blocks of that object. It's a good idea for a thread that has resumed after waiting to recheck any condition that had sent it to wait in the first place (since there could be many threads that have resumed after waiting).

To generalize the above idea, you can also do this with a lock object and `conditions` on the lock object, you can have multiple conditions on a lock. How to use it in a similar manner? First acquire the lock using the lock object, then for any condition you can do await / signal / signalAll (equivalent to wait / notify / notifyAll). Then sometime later release the lock whenever you want.

The Java memory model defines some specs for synchronization (properly defined in Java 5 and onwards):

Relation b/w actions in program and its interactions with memory. If an action A "happens before" action B, then the results of action A will be visible to action B. this relation doesn't exist everywhere. Synchronization introduces a "happens before" relation and so does the use of "volatile".

"partial order" means that code could be reordered by the CPU to make it more efficient but there is still a partial order maintained in the sense that the synchronization blocks won't be reordered, their relative order will still be maintained.

Marking a variable as volatile means it won't be cahced, won't be optimized away in places by the compiler and read / writes to the variable will not be reordered with operations around it.

Java also has atomic variables which make use of composite OS level / chip level instructions that let you atomically compare & set a variable, etc. without the use of locks.

Producer consumer problem - there is a shared storage in which multiple producers can add values, and multiple consuers can remove values. You cannot add values to a full storage and remove values from an empty storage. Synchronization is required to make sure that rule is followed.

A semaphore is a resource counter. It doesn't have a concept of ownership, one thread could increase the count while another could decrease it.

How to ensure that all threads have reached a certain point before continuing program execution? Use a latch, set the limit and each thread can add to the counter when it's reached its point. If the limit is reached, the threads can continue with execution. An example is a countdownlatch (beware that once it's reached its limit and lets threads through, this particular latch object cannot be used again). Like a latch there's also a barrier e.g., cyclicbarrier which doesn't require the threads to signal that it's reached the point where it should've and the object can be used multiple times. A phaser is a newer version of the barrier and doesn't need to set the limit at construction, it can be adjusted dynamically.


Locking adds a performance penalty because it requires system calls (for blocking and waking a thread) and requires the scheduler to be used. Spinning until a resource is available could be more efficient if that resource is expected to be available fairly quickly. Why spin and not sleep? To prevent the thread from being scheduled out of the processor. Thus it really only makes sense on a multicore machine (or any machine which lets multiple threads run parallelly).

Deciphering the output of time:

Real - wall clock time for execution of process from start to end

User - time spent executing threads in user mode (doesn't count time when it's not running)

Sys - time spent executing threads in kernel mode (also doesn't count time where it's not running)

User + sys is the amount of time the process spent running on the CPU (sum of time spent in all cores). In case of a multicore system, this value could be more than real.

Issues in concurrency are caused by "mutable shared state", if that's not there, then there's no issue.

Threads themselves are heavyweight objects. If you're gonna use them to just block later on, don't bother, use async instead (which adds concurrency, doesn't create any new threads). How to do async in JVM languages?

