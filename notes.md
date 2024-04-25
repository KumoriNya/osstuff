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

3. Application multi-threading provided by kernel-provided threads can have higher overheads (i.e. the time it takes to execute individual operations) than user-level threads as thread management (such as creation, and blocking/unblocking) requires a system call into the operating system.

The answer is true and self explanatory.

4. Applications can benefit from running on multi-processors when they are implemented to use multi-threading provided by kernel-level threads, but not user-level threads.

The is true.

Only the in-kernel scheduler is aware of the number of CPUs (processors) in the machine. It assigns ready threads to available CPUs to keep the machine busy.

Thus kernel-provided threads can be spread across available CPUs to keep the machine busy.

The kernel is unaware of any user-level threads, and thus can only schedule processes across available CPUs. It can't take advantage of any parallelism (ready user-level threads). 

5. Application multi-threading supported by user-level threads features a per-thread user-level stack and a 1-to-1 corresponding per-thread in-kernel stack.

The answer is false.

Yes, each user-level thread would execute using a per-thread user-level stack. However, since the kernel is unaware of user-level threads (and their stacks), it only manages processes. Each process has its own in-kernel stack.

So the relationship is many user-level stacks to 1 in-kernel stack.

6. A context switch between user-level threads (also termed a thread switch), can be implemented by an assembly language function called from C that pushes a subset of the registers onto the current stack, saves the current stack pointer, and then sets the stack pointer to the target thread, and restores the target's registers.

The answer is true.

A thread switch can be implemented by preserving the C compiler calling convention and saving all saved registers (see the MIPS calling convention in lectures) on the current stack, then changing stacks, then restoring the registers such that we return to (continue) a different thread after the switch.  

7. Application multi-threading implemented entirely at user-level (i.e. user-level threads) can be used to take advantage of multiple processors to increase performance, even in the situation of a single runnable process.

The answer is false. 

See Question 4 for an answer.

8. Application multi-threading implemented entirely at user-level (i.e. user-level threads) does not expose the concurrency available in the application to the operating system.

The answer is true.

See Question 4 for an answer.

9. Applications running as the root user under UNIX (or administrator under Windows XP/Vista/7/8/10) all run in the processor's privileged mode.

The answer is false.

All applications run in user-mode in the same way. 

The operating system keeps track of the user associated with each process. The user may be root or administrator or some other user. The operating system allows those user-level applications to request certain operation based on the user. A root or administrator application is allowed to perform more operations than a normal user. It is not related to the privileged mode of the microprocessor.

10. A system call processor instruction is one way to trigger the transition between the application running in user-mode to the Operating System running in privileged mode.

- True

11. A process and the operating system kernel share the same stack so they both can execute a high-level language like the C programming language.

The answer is false.

For each process, the operating system has 1 (or more in the case of kernel threads) stacks private to the operating system.

The OS can't securely share a user-level stack as user-level can freely corrupt or compromise its own stack.

12. The arguments to system calls are placed in registers (or on the application's stack) based on a mutually defined convention between the operating system and user-level applications.

The answer is true.

The OS defines the convention for how to request an operation when performing a system call. Applications adhere to the convention in order to correctly request OS services.

13. What is a branch delay?

The pipeline structure of the MIPS CPU means that when a jump instruction reaches the "execute" phase and a new program counter is generated, the instruction after the jump will already have been decoded. Rather than discard this potentially useful work, the architecture rules state that the instruction after a branch is always executed before the instruction at the target of the branch.

14. The goal of this question is to have you reverse engineer some of the C compiler function calling convention (instead of reading it from a manual). The following code contains 6 functions that take 1 to 6 integer arguments. Each function sums its arguments and returns the sum as a the result. (Code)
- Q1 arg1 (and functions in general) returns its return value in what register?
    - A1 v0
- Q2 Why is there no stack references in arg2?
    - There are no local variables inside the function, so the compiler does not need space on the stack to store them.
- Q3 What does jr ra do?
    - It jumps (changes the program counter) to the address in the ra register. The ra register is set by a jal instruction to the address of the instruction after jal. Thus function calls can be implemented with jal and jr ra instructions.
- Q4 Which register contains the first argument to the function?
    - a0
- Q5 Why is the move instruction in arg1 after the jr instruction.  
    - The instruction after a jump (i.e. the instruction in the branch delay slot is executed prior to arriving at the destination of the jump. Thus, logically, the move instruction is executed before arg1 returns.
- Q6 Why does arg5 and arg6 reference the stack?
    - Up to 4 arguments can be passed to a function in registers. Arguments beyond the fourth are passed on the stack?

15. The following code provides an example to illustrate stack management by the C compiler. Firstly, examine the C code in the provided example to understand how the recursive function works. (Code)

16. Why is recursion or large arrays of local variables avoided by kernel programmers?

- The kernel stack is usually a limited resource. A stack overflow crashes the entire machine.

17. Compare cooperative versus preemptive multithreading?

Cooperative multithreading is where the running thread must explicitly yield() the CPU so that the dispatcher can select a ready thread to run next. Preemptive multithreading is where an external event (e.g. a regular timer interrupt) causes the dispatcher to be invoked and thus preempt the running thread, and select a ready thread to run next.

Cooperative multithreading relies on the cooperation of the threads to ensure each thread receives regular CPU time. Preemptive multithreading enforces a regular (at least systematic) allocation of CPU time to each thread, even when a thread is uncooperative or malicious.

18. Describe user-level threads and kernel-level threads. What are the advantages or disadvantages of each approach?

User-level threads are implemented in the application (usually in a "thread library"). The thread management structures (Thread Control Blocks) and scheduler are contained withing the application. The kernel has no knowledge of the user-level threads.

Kernel threads are implemented in the kernel. The TCBs are managed by the kernel, the thread scheduler is the normal in-kernel scheduler.

User threads are generally faster to create, destroy, manage, block and activate (no kernel entry and exit required).
If a single user-level thread blocks in the kernel, the whole process is blocked. However, some libraries (e.g., most UNIX pthreads libraries) avoid blocking in the kernel by using non-blocking system call variants to emulate the blocking calls.
User threads don't take advantage of parallelism available on multi-CPU machines.
Kernel threads are usually preemptive, user-level threads are usually cooperative (Note: some user-level threads use alarms or timeouts to provide a tick for preemption).
User-level threads can be implemented on OSes without support for kernel threads.

19. A web server is constructed such that it is multithreaded. If the only way to read from a file is a normal blocking read system call, do you think user-level threads or kernel-level threads are being used for the web server? Why?


A worker thread within the web server will block when it has to read a Web page from the disk. If user-level threads are being used, this action will block the entire process, destroying the value of multithreading. Thus it is essential that kernel threads are used to permit some threads to block without affecting the others.

20. Assume a multi-process operating system with single-threaded applications. The OS manages the concurrent application requests by having a thread of control within the kernel for each process. Such a OS would have an in-kernel stack assocaited with each process.
Switching between each process (in-kernel thread) is performed by the function switch_thread(cur_tcb,dst_tcb). What does this function do?

The function saves the registers required to preserve the compiler calling convention (and registers to return to the caller) onto the current stack.

The function saves the resulting stack pointer into the thread control block associated with cur_tcb, and sets the stack pointer to the stack pointer stored in destination tcb.

The function then restores the registers that were stored previously on the destination (now current) stack, and returns to the destination thread to continue where is left off.

21. What is the EPC register? What is it used for?

This is a 32-bit register containing the 32-bit address of the return point for the last exception. The instruction causing (or suffering) the exception is at EPC, unless BD is set in Cause, in which case EPC points to the previous (branch) instruction.

It is used by the exception handler to restart execution at the at the point where execution was interrupted.

22. What happens to the KUc and IEc bits in the STATUS register when an exception occurs? Why? How are they restored?

The 'c' (current) bits are shifted into the corresponding 'p' (previous) bits, after which KUc = 0, IEc = 0 (kernel mode with interrupts disabled). They are shifted in order to preserve the current state at the point of the exception in order to restore that exact state when returning from the exception.

They are restored via a rfe instruction (restore from exception).

23. What is the value of ExcCode in the Cause register immediately after a system call exception occurs?

The value of ExcCode is 8.

24. Why must kernel programmers be especially careful when implementing system calls?

System calls with poor argument checking or implementation can result in a malicious or buggy program crashing, or compromising the operating system.

25. The following questions are focused on the case study of the system call convention used by OS/161 on the MIPS R3000 from the lecture slides.
- How does the 'C' function calling convention relate to the system call interface between the application and the kernel?
- What does the most work to preserve the compiler calling convention, the system call wrapper, or the OS/161 kernel.
- At minimum, what additional information is required beyond that passed to the system-call wrapper function?

The 'C' function calling convention must always appear to be adhered to after any system-call wrapper function completes. This invovles saving and restoring of the preserved registers (e.g., s0-s8, ra). The system call convention also uses the calling convention of the C-compiler to pass arguments to OS/161. Having the same covention as the compiler means the system call wrapper can avoid moving arguments around and the compiler has already placed them where the OS expects to find them.

The OS/161 kernel code does the saving and restoring of preserved registers. The system call wrapper function does very little.

The interface between the system-call wrapper function and the kernel can be defined to provide additional information beyond that passed to the wrapper function. At minimum, the wrapper function must add the system call number to the arguments passed to the wrapper function. It's usually added by setting an agreed-to register to the value of the system call number.

26. In the example given in lectures, the library function read invoked the read system call. Is it essential that both have the same name? If not, which name is important?

System calls do not really have names, other than in a documentation sense. When the library function read() traps in to the kernel, it puts the number of the system call into a register or on the stack. This number is used to index into a jump table (e.g. a C switch statement). There is really no name used anywhere. On the other hand, the name of the library function is very important, since that is what appears in the program.

Note: The kernel programmer may can the code in the operating system that implements read a similar name, e.g., sys_read. This is purely a convention for the sake of code clarity.

27. To a programmer, a system call looks like any other call to a library function. Is it important that a programmer know which library function result in system calls? Under what circumstances and why?

As far as program logic is concerned it does not matter whether a call to a library function results in a system call. But if performance is an issue, if a task can be accomplished without a system call the program will run faster. Every system call involves overhead in switching from the user context to the kernel context. Furthermore, on a multiuser system the operating system may schedule another process to run when a system call completes, further slowing the progress in real time of a calling process.

28. Describe a plausible sequence of activities that occur when a timer interrupt results in a context switch.

In the table below, almost everything that is not the timer device or CPU is actually just code executing within the kernel. The distinction of "who" is there to clarify which kernel subsystem is notionally responsible.

## Quiz 4
1. File systems based on contiguous allocation are suitable for read-only media such as optical disks (DVDs and CDs).

The answer is true.

Read-only media is created from a set of files of size known in advance, allowing efficient allocation planning.

Contiguous allocation is an efficient layout minimising head seeking of the device.

2. A FAT file system gets its name as it is designed for large disks.

False

It is actually the opposite. FAT = file allocation table. Typically it only supports smaller disks. 

3. Using a bitmap to manage free blocks on a disk reduces the usable disk capacity compared to the approach of storing the addresses of free blocks in the free blocks themselves.

True

A bitmap approach reserves space on the disk to contain the bitmap independent of the current level of utilisation of the disk.

The list-of-blocks of block numbers approach temporarily repurposes some of the free blocks to keep track of the free blocks. It dynamically sizes the number of block used based on the number of free blocks needing recording. When no blocks are free, no blocks need recording, and no free blocks are repurposed effective taking no space when the disk is full. 

4. File systems based on indexed allocation (i.e. i-node-based file systems) have good random access performance compared to linked-list allocation.

True

Link-list based allocation requires the FS to read all blocks prior to the target block in order to locate the target block.

Inode-based allocation can use the i-node structure to find the target directly. 

5. A file system using linked-list disk allocation (also termed chained allocation) provides poor random access performance.

True

Link-list based allocation requires the FS to read all blocks prior to the target block in order to locate the target block.

6. A file system uses contiguous allocation. The file system can save disk storage for sparse files by not storing the blocks of the file that are not written to.

False

Contiguous allocation records only the initial block on the disk and the file size. There is no metadata available to record any unused blocks.

7. Consider a file currently consisting of 100 records of 400 bytes. The filesystem uses fixed blocking, i.e. one 400 byte record is stored per 512 byte block. Assume that the file control block (and the index block, in the case of indexed allocation) is already in memory. Calculate how many disk I/O operations are required for contiguous, linked, and indexed (single-level) allocation strategies, if, for one record, the following conditions hold. In the contiguous-allocation case, assume that there is no room to grow at the beginning, but there is room to grow at the end of the file. Assume that the record information to be added is stored in memory.

8. In the previous example, only 400 bytes is stored in each 512 byte block. Is this wasted space due to internal or external fragmentation?

Internal fragmentation

9. Old versions of UNIX allowed you to write to directories. Newer ones do not even allow the superuser to write to them? Why? Note that many unices allow you read directories.

To prevent total corruption of the fs. eg cat /dev/zero > /

10. Given a file which varies in size from 4KiB to 4MiB, which of the three allocation schemes (contiguous, linked-list, or i-node based) would be suitable to store such a file? If the file is access randomly, how would that influence the suitability of the three schemes?

Contiguous is not really suitable for a variable size file as it would require 4MiB to be pre-allocated, which would waste a lot of space if the file is generally mush smaller. Either linked-list of i-node-based allocation would be preferred. Adding random access to the situation (supported well by contiguous or i-node based), which further motivate i-node-based allocation to be the most appropriate.


11. Why is there VFS Layer in Unix?

- It provides a framework to support multiple file system types concurrently without requiring each file system to be aware of other file system types.
- Provides transparent access to all supported file systems including network file systems (e.g. NFS, CODA)
- It provides a clean interface between the file system independent kernel code and the file system specific kernel code.
- Provide support for special file system types like /proc.

12. How does choice of block size affect file system performance. You should consider both sequential and random access.

- Sequential Access
    - The larger the block size, the fewer I/O operations required and the more contiguous the disk accesses. Compare loading a single 16K block with loading 32 512-byte blocks.

- Random Access
    - The larger the block size, the more unrelated data loaded. Spatial locality of access can improve the situation.

13. Is the open() system call in UNIX essential? What would be the consequence of not having it?

It is not absolutely essential. The read and write system calls would have to be modified such that:
The filename is passed in on each call to identify the file to operate on
With a file descriptor to identify the open session that is returned by open, the sycalls would also need to specify the offset into the file that the syscall would need to use.
Effectively opening and closing the file on each read or write would reduce performance.

14. Some operating system provide a rename system call to give a file a new name. What would be different compared to the approach of simply copying the file to a new name and then deleting the original file?

The rename system call would just change the string of characters stored in the directory entry. A copy operation would result in a new directory entry, and (more importantly) much more I/O as each block of the original file is copied into a newly allocated block in the new file. Additionally, the original file blocks need de-allocating after the copy finishes, and the original name removed from the directory. A rename is much less work, and thus way more efficient than the copy approach.

15. In both UNIX and Windows, random file access is performed by having a special system call that moves the current position in the file so the subsequent read or write is performed from the new position. What would be the consequence of not having such a call. How could random access be supported by alternative means?

Without being able to move the file pointer, random access is either extremely inefficient as one would have to read sequentially from the start each time until the appropriate offset is arrived at, or the an extra argument would need to be added to read or write to specify the offset for each operation.

16. The following code accesses an existing file in a file system that is formatted with a disk block size of 1024 bytes. If offset is initialised to 7169, how many disk accesses are required to read the 1 byte for each of the following underlying file system types.

Contiguous 1
Linked 8
FAT 1
Inode 1

## Quiz 5 FileManagement? (wk7)

## Quiz 6 FileManagement & MemoryManagement

## Quiz 7 VM?

## Quiz 8 VM2, MultiProcessor Sys, Scheduling (Wk 10)
### VM2
1. What effect does increasing the page size have?

- Increases internal fragmentation, and the unit of allocation is now greater, hence greater potential for un-used memory when allocation is rounded up. Practically speaking, this is not usually an issue, and round up an application to the next page boundary usually results in a relative small amount wasted compared to the application size itself.
- Decreases number of pages. For a given amount of memory, larger pages result in fewer page table entries, thus smaller (and potentially faster to lookup) page table.
- Increases TLB coverage as the TLB has a fixed number of entries, thus larger entries cover more memory, reducing miss rate.
- Increases page fault latency as a page fault must load the whole page into memory before allowing the process to continue. Large pages have to wait for a longer loading time per fault.
- Increases swapping I/O throughput. Given a certain amount of memory, larger pages have higher I/O throughput as the content of a page is stored contiguously on disk, giving sequential access.
- Increases the working set size. Work set is defined as the size of memory associates with all pages accessed within a window of time. With large the pages, the more potential for pages to include significant amounts of unused data, thus working set size generally increases with page size.

2. Why is demand paging generally more prevalent than pre-paging?

Pre-paging requires predicting the future (which is hard) and the penalty for making a mistake may be expensive (may page out a needed page for an unneeded page).

3. Describe four replacement policies and compare them.
- Optimal, FIFO, LRU, Clock

4. What is thrashing? How can it be detected? What can be done to combat it?

Thrashing is where the sum of the working set sizes of all processes exceeds available physical memory and the computer spends more and more time waiting for pages to transfer in and out.

It can be detected by monitoring page fault frequency.

Some processes can be suspended and swapped out to relieve pressure on memory.
### Multiprocessor Systems
5. What are the advantages and disadvantages of using a global scheduling queue over per-CPU queues? Under which circumstances would you use the one or the other? What features of a system would influence this decision?

Global queue is simple and provides automatic load balancing, but it can suffer from contention on the global scheduling queue in the presence of many scheduling events or many CPUs or both. Another disadvantage is that all CPUs are treated equally, so it does not take advantage of hot caches present on the CPU the process was last scheduled on.
Per-CPU queues provide CPU affinity, avoid contention on the scheduling queue, however they require more complex schemes to implement load balancing.

6. When does spinning on a lock (busy waiting, as opposed to blocking on the lock, and being woken up when it's free) make sense in a multiprocessor environment?

Spinning makes sense when the average spin time spent spinning is less than the time spent blocking the lock requester, switching to another process, switching back, and unblocking the lock requester.

7. Why is preemption an issue with spinlocks?

Spinning wastes CPU time and indirectly consumes bus bandwidth. When acquiring a lock, an overall system design should minimise time spent spinning, which implies minimising the time a lock holder holds the lock. Preemption of the lock holder extends lock holding time across potentially many time slices, increasing the spin time of lock acquirers.

8. How does a read-before-test-and-set lock work and why does it improve scalability?

See the lecture notes for code. It improves scalability as the spinning is on a read instruction (not a test-and-set instruction), which allows the spinning to occur on a local-to-the-CPU cached copy of the lock's memory location. Only when the lock changes state is cache coherency traffic required across the bus as a result of invalidating the local copy of the lock's memory location, and the test-and-set instruction which require exclusive access to the memory across all CPUs.

9. On a uniprocessor machine, it is more efficient to block and context switch than to spin (busy-wait) on a lock, even if the context switching overhead is very high.

True.

One a uniprocessor, the context switch to the lock holder is always required (for it to release the lock). Thus any spinning simply wastes CPU time and delays the inevitable context switch to the lock holder.

10. A read before test and set implementation of a spinlock (also called a test and test and set) just adds extra overhead to the lock implementation by adding a superfluous read.

False:

The read allows the lock_acquire() to spin until the lock is available. Spinning, reading the lock variable results in the variable being cached, and no additional traffic on the memory bus. When the lock is available, the lock_acquire() then attempts to acquire the lock with a test_and_set instruction.

Thus the read avoid repeating the test_and_set instruction in a tight loop when there is little chance to acquire the lock.

11. On a multiprocessor machine, a single ready queue in the operating system provides automatic load balancing across CPUs.

True.

When a CPU chooses the next thread to run, it takes the head of the global queue shared between all CPUs. Thus all CPUs shared the load contained in the ready queue.

12. Spinlocks can never be more efficient to use than blocking locks, even on a multiprocessor machine.

False.

Spinlocks can be more efficient when the time spent spinning is less than the cost of context switching.

### Scheduling
13. What do the terms I/O bound and CPU bound mean when used to describe a process (or thread)?

The time to completion of a CPU-bound process is largely determined by the amount of CPU time it receives.

The time to completion of a I/O-bound process is largely determined by the time taken to service its I/O requests. CPU time plays little part in the completion time of I/O-bound processes.

14. What is the difference between cooperative and pre-emptive multitasking?

In cooperative scheduling, threads explicitly release the CPU, while with pre-emptive the thread has no choice, a timer interrupt triggers the invocation of the scheduler and potentially a switch away from the currently running thread.

15. Consider the multilevel feedback queue scheduling algorithm used in traditional Unix systems. It is designed to favour IO bound over CPU bound processes. How is this achieved? How does it make sure that low priority, CPU bound background jobs do not suffer starvation?

Note: Unix uses low values to denote high priority and vice versa, 'high' and 'low' in the above text does not refer to the Unix priority value.

I/O bound processes typically have many short CPU bursts. If such a process is scheduled, it will typically not use up it's time slice. Priorities are recomputed taking into account the consumed CPU, and hence an I/O-bound process will end up having a higher priority than a process that started at the same priority level and which used more CPU cycles.

Even a process with low priority will be scheduled eventually, since the priority of processes that are continually scheduled eventually also receive a low or lower priority.

Also note that the recorded amount of CPU consumed (that is used to calculate priority) is aged (reduced) over time, and hence CPU-bound processes also increase in priority is they are not scheduled.

16. Why would a hypothetical OS always schedule a thread in the same address space over a thread in a different address space? Is this a good idea?

Context switch is faster. Better locality. If done too often (e.g., always) it starves other tasks.

17. Why would a round robin scheduler NOT use a very short time slice to provide good responsive application behaviour?

CPU is consumed when switching from one task to another. This switching does not contribute to application progress. If done very frequently (a very short time slice), a significant portion of available CPU will be wasted on scheduler overhead.

18. General purpose schedulers aim to improve interactivity and I/O performance by favouring I/O-bound processes (and threads) over CPU-bound processes (and threads). This improvement for I/O-bound processes comes with the disadvantage of dramatically reducing the CPU available to CPU-bound processes (and threads).

False: I/O-bound processes spend a significant amount of their time waiting for I/O and using little CPU to make the I/O requests. Thus favouring I/O-bound processes still allows CPU-bound processes to be overlapped with I/O waiting 

19. Round-robin scheduling gives each ready process (or thread) a turn to execute for a timeslice length of time. A short timeslice provides good interactive response times at the expense of more overhead due to more frequent context switching.

True

20. The traditional UNIX scheduler uses priorities to favour I/O-bound processes over CPU-bound processes. It fails to adjust when CPU-bound processes become I/O-bound and vice versa.

False. 

The UNIX scheduler adjusts the priority when a process is preempted by the scheduler. Thus is if an I/O bound process become CPU bound, the process will be preempted more often and thus have its priority adjusted. 