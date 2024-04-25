# Indexes
## Week 1 OS Overview, Processes and Threads, Concurrency and Synchronisation

## Quiz 1
1. Because the operating system runs in privileged mode, the operating system can safely use the memory pointers provided by applications (via the arguments to system calls) just like in-kernel memory pointers.
- False
2. All application calls to the standard C library result in a transition into kernel mode to process the call, after which the processor returns to the application in user mode.
- False
3. An operating system can enforce security by putting security checks in the standard C-library.
- False
4. An operating system provides a high-level programming interface to the low-level computer hardware for applications.
- True
5. On a microprocessor with no privileged mode, an operating system can implement resource allocation policies, but not enforce them.
- True
6. An operating system is not responsible for arbitrating between processes competing for system resources.
- False
7. In the user mode of the microprocessor, only the application's virtual memory is accessible to the processor.
- True
8. What are some of the differences between a processor running in privileged mode (also called kernel mode) and user mode? Why are the two modes needed?  
In user-mode:
- CPU control registers are inaccessible.
- CPU management instructions are inaccessible.
- Part's of the address space (containing kernel code and data) are inaccessible.
- Some device memory and registers (or ports) are inaccessible.  
The two modes of operation are required to ensure that applications (running in user-mode) cannot bypass, circumvent, or take control of the operating system.

9. Given a high-level understanding of file systems, explain how a file system fulfills the two roles of an operating system?  
At the level of the hardware, storage involves low-level controller hardware and storage devices that store blocks of data at many locations in the store. The OS filesystem abstracts above all these details and provides an interface to store, name and organise arbitrary unstructured data.

The filesystem also arbitrates between competing processor by managing allocated and free space on the storage device, in addition to enforcing limits on storage consumption (e.g. quotas).

10. What are the two main roles of an Operating System?
- It provides a high-level abstract machine for programmers (hides the details of the hardware)
- It is a resource manager that divides resources amongst competing programs or users according to some system policy.

11. Which of the following instructions (or instruction sequences) should only be allowed in kernel mode?
- Disable all interrupts → Kernel-mode only,
- Read the time of day clock. → Kernel-mode and user-mode,
- Set the time of day clock → Kernel-mode only,
- Change the memory map → Kernel-mode only,
- Write to the hard disk controller register → Kernel-mode only,
- Trigger the write of all buffered blocks associated with a file back to disk (fsync). → Kernel-mode and user-mode

17. On UNIX, which of the following are considered system calls? Why?
- read() - syscall
- printf() - C lib calls write()
- memcpy() - lib func
- open() - syscall
- strncpy() - lib func

18. Compare reading a file using a single-threaded file server and a multithreaded file server. Within the file server, it takes 15 msec to get a request for work and do all the necessary processing, assuming the required block is in the main memory disk block cache. A disk operation is required for one third of the requests, which takes an additional 75 msec during which the thread sleeps. How many requests/sec can a server handled if it is single threaded? If it is multithreaded?
- In the single-threaded case, the cache hits take 15 msec and cache misses take 90 msec. The weighted average is 2/3 × 15 + 1/3 × 90. Thus the mean request takes 40 msec and the server can do 25 per second. For a multithreaded server, all the waiting for the disk is overlapped, so every request takes 15 msec, and the server can handle 66 2/3 requests per second.

19. Given N threads in a uniprocessor system. How many threads can be running at the same point in time? How many threads can be ready at the same time? How many threads can be blocked at the same time?
- Running threads = 0 or 1.
- Blocked = N - Running - Ready
- Ready = N - Running - Blocked

20. In the three-state process model, what do each of the three states signify? What transitions are possible between each of the states, and what causes a process (or thread) to undertake such a transition?  

"
The three states are: Running , the process is currently being executed on the CPU; Ready, the process is ready to execute, but has not yet been selected for execution by the dispatcher; and Blocked where the process is not runnable as it is waiting for some event prior to continuing execution.

Possible transitions are Running to Ready, Ready to Running, Running to Blocked, and Blocked to Ready.

Events that cause transitions:

Running to Ready: timeslice expired, yield, or higher priority process becomes ready.
Ready to Running: Dispatcher chose the next thread to run.
Running to Blocked: A requested resource (file, disk block, printer, mutex) is unavailable, so the process is blocked waiting for the resource to become available.
Blocked to Ready: a resource has become available, so all processes blocked waiting for the resource now become ready to continue execution.
"

22. Multi-tasking can improve system utilisation for I/O-bound workloads by overlapping I/O with execution of other ready applications.
- False

23. In a multi-threaded environment, local variables declared within a function have to be synchronised as they are allocated on a stack shared between all the threads.
- False

24. In a multi-threaded process, every thread has its own virtual address space.
- False

25. In the three-state process model, a common state transition is from the ready state to the blocked state.
- False

26. A single-threaded process runs twice as fast on a two CPU machine.
- False

## Quiz 2 Locks?
1. Multi-threaded applications (i.e. user-level code) can avoid race conditions by disabling and enabling interrupts.

The answer is false.

It is unsafe for a hardware architecture to allow user-mode code to have access to interrupt disabling. Similarly, it is unsafe for the operating system to indirectly give access to interrupt disabling by providing a system call that disables interrupts.

The security threat is that an application disables interrupts and enters an endless loop thus preventing the operating system from ever regaining control of the system again.

2. Within OS/161, condition variables are used to manage blocking and waking within critical sections protected by  semaphores.

The answer is false.

Condition variables manage blocking and waking within locks. 

More traditionally, condition variable provide block and waking within a programming language construct known as a Monitor.

3. On a uniprocessor machine, disabling interrupts prior to entering short critical sections, and re-enabling interrupts on exiting is a reasonable approach to achieving mutual exclusion within critical sections inside the operating system.

The answer is true.

Disabling interrupts postpones timer interrupts while interrupts are disabled, thus preventing the potential concurrency that might exist when timer interrupts trigger switching between threads of control within the operating system.

4. Semaphores can be used to implement mutually exclusive execution in critical sections in a multi-threaded program by initialising the semaphore count to zero.

The answer if false:

Semaphores can be used to implement mutual exclusion with the count is initialised to 1. This implies only 1 thread can complete the P() at a time, thus providing mutually exclusive access for that thread.

6. Implementing a spinlock with an atomic test-and-set instruction avoids the issue of busy waiting.

The answer if false.

The test-and-set instruction avoids race conditions involve in determining which thread has acquired the lock. However, a spinlock() with test-and-set still busy-waits by repeated executing test-and-set until the thread obtains the lock.

7. What synchronisation mechanism or approach might one take to have one thread wait for another thread to update some state? (Code)

8. A particular abstraction only allows a maximum of 10 threads to enter the "room" at any point in time. Further threads attempting to enter the room have to wait at the door for another thread to exit the room. How could one implement a synchronisation approach to enforce the above restriction? (Code)

9. Multiple threads are waiting for the same thing to happen (e.g. a disk block to arrive from disk). Write pseudo-code for a synchronising and waking the multiple threads waiting for the same event. (Code)

10. Banker's algorithm can be used to avoid competing processes deadlocking without requiring any knowledge of their resource requirements in advance.

The answer is false.

Bankers algorithm requires the maximum required resources to be known in advance.

11. Deadlock prevention can be achieved by mitigating one of the four conditions required for deadlock. Each one of the four conditions are as practical a candidate to mitigate as any of the others.

The answer is false.

While deadlock prevent aims to mitigate one of the four conditions required for deadlock, the  condition most practical to mitigate is the circular wait. The mutual exclusion and no preemption conditions are not prractical to mitigate at all. The hold and wait condition is feasible but not as practical as mitigating the circular weight.

12. Hold and wait is a practical deadlock prevention strategy.

The answer is false.

Hold and wait is one of the four conditions required for deadlock to occur. 

13. One deadlock prevention method is to release all locks held by a thread when the thread finds a lock that it requires is held by another thread. This method prevents deadlock, but not necessarily livelock.

The answer is true.

This approach mitigates hold and wait as resources are not held if waiting is required. However, the approach may livelock in that resource are continually partially acquired, but never completely.

14. An operating system implements deadlock detection. Upon detecting deadlock, it notifies the system administrator of the set of deadlocked processes that it has detected. A practical method of deadlock recovery is for the administrator to kill one of the processes in the set, thus allowing the others to continue.

The answer is true.

Killing a process in the set of deadlocked processes will release the resources help by that process, thus allowing the remaining processes in the set to potentially continue.

15. Dining philosophers can avoid deadlock using a single lock for the entire table.

The answer is true.

Using a single lock for the entire table would guarantee only a single philosopher could eat at a time. This prevents deadlock as circular wait is not possible and hold and wait is not possible. However, it is less than optimal as only a single philosopher can eat at a time even though many forks may be available.

16. A group of processes are livelocked if they are all in the blocked state, and are making no effective progress.

The answer is false.

Processes that are livelocked are 'live', i.e. they are in the running or ready state. They make to progress as they never receive the resources they require despite repeated attempts.

17. A system uses a resource management policy that is potentially prone to starvation (e.g. shortest job first scheduling). When starvation occurs, the whole system makes no progress.

The answer is false.

A single process can starve in a system as described for instance if it was a long job. The system can make continued progress process the shorter jobs, but the longer jobs starve as they are neve chosen.

18. Here are code samples for two threads that use semaphores (count initialised to 1). Give a sequence of execution and context switches in which these two threads can deadlock.
Propose a change to one or both of them that makes deadlock impossible. What general principle do the original threads violate that causes them to deadlock? (Code)

19. Here are two more threads. Can they deadlock? If so, give a concurrent execution in which they do and propose a change to one or both that makes them deadlock free. (Code on deadlock)

20. Describe (and give pseudocode for) a synchronised linked list structure based on thread list code in the OS/161 codebase (kern/thread/threadlist.c). You may use semaphores, locks, and condition variables as you see fit. You must describe (a proof is not necessary) why your algorithm will not deadlock. (Code on deadlock)

21. For each of the following scenarios, one or more dining philosophers are going hungry. What is the condition the philosophers are suffering from?

- a. Each philosopher at the table has picked up his left fork, and is waiting for his right fork - Deadlock

- b. Only one philosopher is allowed to eat at a time. When more than one philosophy is hungry, the youngest one goes first. The oldest philosopher never gets to eat. - Starvation

- c. Each philosopher, after picking up his left fork, puts it back down if he can't immediately pick up the right fork to give others a chance to eat. No philosopher is managing to eat despite lots of left fork activity. - Livelock

22. What is starvation, give an example?

Starvation is where the system allocates resources according to some policy such that progress is being made, however one or more processes never receive the resources they require as a result of that policy.

Example, a printer that is allocated based on "smallest print job first" in order to improve the response for small jobs. A large job on a busy system may never print and thus starve

23. Two processes are attempting to read independent blocks from a disk, which involves issuing a seek command and a read command. Each process is interrupted by the other in between its seek and read. When a process discovers the other process has moved the disk head, it re-issues the original seek to re-position the head for itself, which is again interrupted prior to the read. This alternate seeking continues indefinitely, with neither process able to read their data from disk. Is this deadlock, starvation, or livelock? How would you change the system to prevent the problem?

It is livelock. Allow each process to lock the disk and issue both commands together mutually exclusively and then release the lock.

24. Describe four ways to prevent deadlock by attacking the conditions required for deadlock.

- Mutual exclusion condition
    * Make the resource sharable, i.e. allow concurrent access to read-only files. However, in general most resources are not shareable and require mutual exclusion.
- Hold and wait condition
    * Dictate only a single resource can be held at any time. Not really practical.
    Require that all required * resource be obtained initially. If a resource is not available, all held resources must be releases before trying again - prone to livelock.
- No preemption condition
    * Preempt the resource (take it away from the holder). Not always possible.
- Circular wait condition
    * Order the resources numerically and request them in numerical order.


## Quiz 3
1. An I/O-bound process spends a significant fraction of its execution time blocked waiting for I/O. One can reduce the overall execution time of the application by using user-level threads to overlap the application's blocking I/O with its execution.

The answer is false

The kernel itself is unaware an any user-level threads. Thus any blocking system call (i.e. any system call involving I/O) would block the whole process.

One could attempt to reduce the overall execution time by attempting to overlap execution with I/O by using kernel-provided (kernel-level) threads.

2. Co-operative (non-preemptive) multitasking can result in a non-responsive system if an application has an endless loop.

The answer is true.

Non-preemptive multitasking relies on applications calling yield regularly to allow the scheduler to chose another thread to run. If a process/thread does not perform a system call and does not yield, a process can run forever without the OS (scheduler) ever regaining control.