-   [https://syedali.net/engineer-interview-questions/](https://syedali.net/engineer-interview-questions/)
    
-   [https://sre.google/sre-book/effective-troubleshooting/](https://sre.google/sre-book/effective-troubleshooting/)
    
-   [https://sre.google/workbook/non-abstract-design/](https://sre.google/workbook/non-abstract-design/)
    
-   [https://github.com/donnemartin/system-design-primer](https://github.com/donnemartin/system-design-primer)
    
-   [http://highscalability.com/blog/2011/1/26/google-pro-tip-use-back-of-the-envelope-calculations-to-choo.html](http://highscalability.com/blog/2011/1/26/google-pro-tip-use-back-of-the-envelope-calculations-to-choo.html)
    
-   [https://www.aosabook.org/en/distsys.html](https://www.aosabook.org/en/distsys.html)
    
-   [https://www.brendangregg.com/Perf/linux_perf_tools_full.png](https://www.brendangregg.com/Perf/linux_perf_tools_full.png)
    
-   [https://netflixtechblog.com/linux-performance-analysis-in-60-000-milliseconds-accc10403c55](https://netflixtechblog.com/linux-performance-analysis-in-60-000-milliseconds-accc10403c55)
    
-   [https://www.brendangregg.com/usemethod.html](https://www.brendangregg.com/usemethod.html)
    
-   [https://github.com/mxssl/sre-interview-prep-guide](https://github.com/mxssl/sre-interview-prep-guide)
    
-   [https://jg.gg/2016/07/31/architecture-and-systems-design-interview/](https://jg.gg/2016/07/31/architecture-and-systems-design-interview/)
    
-   [https://gist.github.com/hellerbarde/2843375](https://gist.github.com/hellerbarde/2843375)

My recommendations for folks who want to interview at Google -

-   Do solid coding prep for 1+ month - actually code, don't just read online solutions. I downloaded IntelliJ and solved a lot of HackerRank/LeetCode problems. I setup a github account and uploaded my solutions there so that I can revisit them for future interviews :-)
    
-   Watch every YouTube video on interview questions, system design, interviewing skills, etc. Learn different techniques and eventually form your own technique for solving problems.
    
-   There are only so many data structures and algorithms out there. At first, it will be very daunting and demoralizing, but eventually you will start seeing a pattern emerging on how to solve these questions. There are only so many different ways to traverse a tree or a graph, dammit! Practice practice practice and eventually you will get better at it.
    
-   Interview at a couple other places before Google (or your dream company) so that you get a few reps under your belt.
    
-   Speak loud and clear during interviews. Every time you speak out your thought process, it demonstrates to your interviewer whats running through your head.
    
-   Read the SRE book!
    
-   Be prepared to ask meaningful questions esp from the SRE book. Its another way to demonstrate to them you are really interested. For example, how do they ensure SREs spend 50% of their time on development or if they follow the guidelines in the book religiously or is there some leeway.
    
-   System design is a toss up. But start off small, design something with "leaps of faith" and then revisit those leaps and fill in the blanks. Of course, mention this to your interviewer. Chances are he/she may not be interested in some of those details and is OK if you skip them.
    
-   Be nice to your Google recruiter. I got a lot of help and advise from mine.
    
-   If you aren't able to solve a question completely during the phone screen or onsite, try completing it at home and send your solution to the recruiter. Not sure if this really helps, but thats what I did when I wasnt able to complete the coding exercise in the 1st phone screen. They gave me another chance. I did the same for the onsite questions.
    
-   If any of the interviewers give you their email/business card, send a thank you note. One of the interviewers sent me a thank you email and I replied back thanking him and re-emphasizing my interest in the role. Again, not sure how much this helps, but it certainly can't hurt.

How deep depends on the level you are interviewing for. The higher the level, the broader is the scope - but a more advanced interview isn't about going deeper, it is about moving faster on the basics and seeing farther implications at the overall software architecture, organization or business level.

Most interviewers won't ask questions that require a super-deep understanding of kernel data structures. Some interviewers might be willing to go deep, if they have the background. In general, you need to understand what are the implications of kernel design and data structures on userspace software.

If you can't explain processes and process groups, capabilities, virtual memory, inodes and filesystems, networking, etc., you are definitely not ready.

If you can't articulate how containers work, or explain how Kubernetes or Docker are implemented, you are not ready.

If you don't know how the top-half or bottom-half parts of device drivers work, how the scheduler works, you are probably borderline.

If you understand and can clearly explain the technical details of ksplice, or live task migrations across VMs and machines, then you are probably more than ready. If you know all that, but can't use this knowledge in a real world situation, you won't pass the interview.

I would focus more on being able to use what you know, rather than knowing a lot.

Interviewers will prepare one or more questions before the interview. These questions are designed to be open ended and clarify how you reason about a problem, rather than if you have a very detailed knowledge about it.

Google SRE interviews look for generalists that can adapt, not specialists whose knowledge is deep but static.


cycle down to syscall signatures, signals, how virtual memory and file systems work etc.


1.  # Linux Notes
    

4.  strace -p $pid # running program
    
5.  strace -c $program # summary
    
6.  strace -e trace=write # filter
    
7.  ptrace()
    

10.  uptime
    

12.  logs:
    
13.  dmesg | tail
    
14.  tail /var/log/messages  ( /var/log/syslog /var/log/kern )
    
15.  journalctl
    

17.  /var/log/audit/audit.log SELinux
    
18.  /var/log/secure
    

20.  vmstat # summary
    
21.  vmstat 1 5 -w # every 1 sec, print 5 . wide .(first line is summary since reboot)
    
22.  vmstat -d # disk stats
    
23.  vmstat -s # summary memory stats
    

25.  r: runnable (running or waiting to run in queue)
    
26.  b: uninterruptible sleep (D in ps)
    

28.  active/inactive: recently used/not
    

30.  cat /proc/meminfo
    

32.  softirqs: network tx/rx, timer
    

34.  dmidecode : hardware info (memory, cpu, board, vendor )
    

36.  yum install sysstat:
    
37.  mpstat -P ALL # cpu balance
    

39.  pidstat
    
40.  pidstat 1 1
    
41.  pidstat -p pid
    

43.  iostat -x 1
    
44.  sar -n DEV 1 # network throughput
    
45.  sar -n TCP,ETCP 1 # TCP stats (also: ss, network interface throughput, retransmits
    

47.  free -m
    
48.  top : P sort by CPU, M sort by memory
    

51.  lscpu
    
52.  cat /proc/cpuinfo
    
53.      flags
    

56.  df -lT # -l local. -T type
    
57.  lsblk -f # filesystem
    
58.  file -s /dev/hda1
    
59.  mount
    

62.  /proc/loadavg : uptime
    
63.  /proc/meminfo : free -m
    
64.  /proc/buddyinfo : memory fragmentation
    
65.  /proc/slabinfo : slabs: cache of freq used objects in kernel. inode, xfs, net.  
    
66.                          vmstat --slabs
    
67.  /proc/version
    
68.  /proc/locks
    
69.  /proc/mounts :  mount
    
70.  /proc/devices : char /(tty, usb) & block
    
71.  /proc/cgroups
    
72.  /proc/cmdline : linux boot cmline args: image, root, lang
    
73.  /proc/interrupts : IRQs per CPU
    
74.  /proc/dma : ISA Direct Memory Access (floppy, cascade)
    
75.  /proc/partitions : major/minor blocks
    
76.  /proc/filesystems : fs types in mount -t  : nodev , xfs
    
77.  /proc/swaps
    
78.  /proc/stat : CPUs, processes
    
79.  /proc/modules : lsmod
    
80.  /proc/sys/* : sysctl -a
    

82.  /proc/net/dev : tx/rx: errs, drop
    
83.  /proc/net/netstat : TCP stats
    

86.  /proc/1/stat
    
87.  /proc/1/status : state, memory, signals caught
    

89.  /proc/1/cmdline
    
90.  /proc/1/comm
    
91.  /proc/1/exe : link to executable
    

93.  /proc/1663/task/*
    
94.  /proc/2753/environ : starting env vars, env | tr '\0' '\n'
    

96.  /proc/1/limits
    
97.  /proc/1/cgroup
    
98.  /proc/1/ns pid, mnt, user
    

100.  /proc/1/fd/* : symbolic links
    
101.  /proc/1/fdinfo/* : info about FDs
    

103.  /proc/1/mounts, mountinfo, mountstats
    
104.  /proc/1/oom_*
    

106.  /proc/1/map_files/* : memory-mapped files
    
107.  /proc/1/maps :  memory: stack, libraries, heap, vdso, vsyscall (accelerate some syscalls) , /smaps
    
108.  /proc/1663/numa_maps     Non-Uniform Memory Access
    
109.  /proc/1/io : io stats
    

111.  /proc/1/syscall
    
112.  /proc/1/timers
    

115.  **Perf numbers**
    
116.  network: ec2 instances (except tiny ones)
    
117.  1-2 Gbit/s Baseline, 10 Gbits/s Burst
    

119.  disk: EBS volumes, general purpose, throughput
    
120.  128 MiB/s - 250 MiB/s (1,000 MiB/s for io optimized)
    
121.                    ~ 2 Gbit/s
    

123.  **Random**
    
124.  store ssh session locally:
    
125.  `ssh <user>@<host> | tee ssh.txt`
    

127.  downloading a file using remote command without shell login, if allowed:
    
128.  ssh hostname tar cvjf - /path/to/folder | tar xjf -
    

131.  **FDs**
    
132.  opaque identifier to an open file
    
133.  integer returned by open() syscall
    
134.  index to an array of open files kept by kernel
    
135.  (0,1,2 reserved stdin, stdout, stderr)
    
136.  per process:
    
137.      ls -l /proc/$pid/fd
    
138.          123 -> /path/to/file
    

140.  multiples FDs can refer to same file, 2 processes fork()
    
141.  open file table: offset, access mode r/w, reference to inode object (in-memory or in-Core inode table)
    

143.  not closing files: too many file descriptors FDs
    
144.  systemd can limit then
    
145.  ulimit can limit them
    

147.  closing file without killin process: gdb
    
148.      gdb -p $PID
    
149.      p close($FD)
    
150.  Bash: http://logan.tw/posts/2016/02/20/open-and-close-files-in-bash/
    
151.          exec $fd>&-
    

153.   overall limit: ulimit -n
    
154.   limit per process: cat /proc/$pid/limits
    
155.   FDs for process: ls /proc/$pid/fd
    

157.  **ulimit**: for current Bash session. setrlimit() . Set at /etc/security/limits.conf
    
158.  control groups, **cgroups**: for user-defined processes
    

160.  ----
    

162.  - Best way to deal with A piece of Hardware that constantly sending interrupt to OS with command buffer of 64 ?
    
163.  - memory leak: Valgrind, mtrace() in C
    
164.  - difference between select, poll, epoll? https://devarea.com/linux-io-multiplexing-select-vs-poll-vs-epoll/#.X2ppb5NKg8N
    
165.    select -> poll > epoll - more efficient , less portable. epoll doesn't work with files.
    
166.  - interrrups. IRQ number, interrupt vector table. DMA. GIC? https://en.wikipedia.org/wiki/Interrupt ,  https://en.wikipedia.org/wiki/Message_Signaled_Interrupts , top-half vs bottom-half
    
167.  - Memory Barrier instructions prevent reordering of CPU instructions beyond or ahead of the barrier.
    

169.  **CPUS**
    

171.  `taskset -pc 1-2 $pid` process binding  
    
172.  `mount -t cpuset ...` exclusive cpu sets
    

174.  **Virtual Memory**
    

176.  - space & temporal locality  
    
177.  - pages & page table  
    
178.   - programs using more (virtual) memory than RAM, loads faster  
    
179.   - no need for programers to be concerned about RAM addresses  
    
180.   - process isolation & protection (r,w,x)  
    
181.   - memory sharing: IPC or shared library  
    
182.  - memory allocation: heap: malloc, brk(). mmap()  
    
183.  - tryign to access address not in page table:  SIGSEGV, segmentation fault  
    
184.  - segments: text, data, stack, heap  
    

186.  Page size: getconf PAGESIZE (4KB)
    

188.  VM is an abstraction that provides each process and the kernel with large (~unlimited), private and linear (contiguous) memory.
    

190.  UNIX: swapping: all memory goes to disk. Linux swapping is paging: pages go to disk.
    
191.  https://www.linuxjournal.com/article/10678  
    

193.  **mblock()** : blocks pages of memory that can't be swapped.
    

195.  Anonymous memory: private to a process; not tied to a file or device, like heap & stack.
    

197.  "dirty" pages are modified ones (not yet written to disk). Flushed to disk:
    
198.      - 30 s
    
199.      - sync()
    
200.      - too many dirty pages dirty_ratio
    

203.  "Fault" does not mean "error" but instead means "unavailable".  
    
204.      - A minor fault means the page is in memory but not allocated to the requesting process or not marked as present in the memory management unit.  
    
205.  A minor page fault is one that does not require loading from disk.
    
206.      - A major fault means the page in no longer in memory.
    
207.  ps maj_flt, min_flt
    

209.  `pmap $pid` command: memory mappings of a process
    
210.  `swapon` create, observe swap `-s` same as `cat /proc/swaps`
    

212.  tunable sysctl:
    
213.    vm.dirty_*
    
214.    vm.overcommit_memory
    
215.      0: reasonable (default)
    
216.      1: always
    
217.      2: never
    
218.    vm.swappiness 60 (favor paging over page cache)
    

221.  The **malloc**() function allocates size bytes and returns a pointer to the allocated memory._The memory is not initialized_.
    

223.  The **free**() function frees the memory space pointed to by the pointer.
    

225.  Normally, **malloc**() allocates memory from the heap, and adjusts the size of the heap as required, using brk(), sbrk()
    

227.  Stack is linear (faster), for function calls and temp vars. Heap is larger, tree (slower), global vars.
    

229.  **mmap** stands for memory-mapped files. It is a way to read and write files without invoking system calls.
    

231.  https://medium.com/@sasha_f/why-mmap-is-faster-than-system-calls-24718e75ab37
    

233.  echo 100000 > /proc/sys/kernel/pid_max
    

235.  `free -m` -> `/proc/meminfo`
    

237.  **Disks**
    

239.  Not all IOPS are the same: sequential vs random access
    

241.  RAID
    
242.      0 : striping (better performance, splitting I/O)
    
243.      1: mirroring 2x Ws
    
244.     10: perf similar to 1, good
    
245.      5: checksums etc, bad perf
    
246.      6: even worse than 5
    

248.  iostat -x
    
249.  iotop
    
250.  sar -d
    
251.  pidstat -d -p pid 1 # default -p ALL
    

253.  **Networking**
    
254.  NAPI: New API: grouping packets together for interrupts
    
255.  IRQ balancer: distribute among CPUs
    

257.  ss -s
    
258.  netstat -s
    
259.  netstat -i
    
260.  ip -s link
    
261.  ifconfig
    
262.  lsof -i
    
263.  sar -n DEV
    

265.  tcpdump, wireshark: promiscuous, libpcap
    
266.  strace
    

269.  /etc/sysconfig/network-scripts/ifcfg-eth0
    
270.  DEVICE="eth0"
    
271.  BOOTPROTO="dhcp"
    

273.  **File system**
    
274.  File system: boot block, superblock, I-node table, data blocks
    

276.  Journaling file systems eliminate the need for lengthy file-system consistency checks after a system crash. A journaling file system logs (journals) all metadata updates to a special on-disk journal file before they are actually carried out.
    

279.  Virtual Memory File System: tmpfs.  
    
280.  `mount -t tmpfs source target`
    
281.  By default, a tmpfs file system is permitted to grow to half the size of RAM
    
282.  A tmpfs file system mounted at /dev/shm is used for the glibc implementation of POSIX shared memory and semaphores.
    

284.  Copy-On-Write COW filesystem: doesn't overwrite blocks but writes new one, update reference, add old blocks to free list.
    

286.  `du -sh <dir>`
    

288.  **Files**
    

290.  - stat(), file metadata, mostly from inode table
    
291.  - access() system call checks the accessibility of the file
    
292.  - sticky bit, for directories, acts as the restricted deletion flag.  This makes it possible to create a directory that is shared by many users, who can each create and delete their own files in the directory but can’t delete files owned by other users. The sticky permission bit is commonly set on the /tmp directory for this reason. `chmod +t file`
    
293.  - umask default 022 (----w--w-)
    

295.  inotify_init() syscall
    

297.  **Signals**
    

299.  - A signal is a notification to a process that an event has occurred. Signals are sometimes described as software interrupts.
    
300.  - A process could send a signal to another process or to itself (raise()) but source is tipically the kernel:
    
301.      - hardware exception (notified the kernel): dividing by zero for ex SIGFPE
    
302.      - user created
    
303.      - software event: input available on FD, timer, limit reached
    
304.  - Small integer defined in signal.h SIGxxxx
    
305.  - standard signals: 1-31
    
306.  - event generated -> pending -> delivered
    
307.  - delivered immediatly to running process or to when process is next scheduled to run. Can add a blocked to signal mask.
    
308.  - A signal handler is a function, written by the programmer, that performs appropriate tasks in response to the delivery of a signal
    
309.  - Instead of accepting the default for a particular signal, a program can change the action: "signal disposition"
    
310.  - two ways of changing the disposition of a signal: signal() and sigaction().
    
311.  - kill():  we can use the null signal to test if a process with a specific process ID exists
    
312.  - The set of pending signals is only a mask; it indicates whether or not a signal has occurred, but not how many times it has occurred. If the same signal is generated multiple times while it is blocked, then it is recorded in the set of pending signals, and later delivered, just once. One of the differences between standard and realtime signals is that realtime signals are queued.
    
313.  - Signal delivery is typically asynchronous, meaning that the point at which the signal interrupts execution of the process is unpredictable. In some cases (e.g., hardware-generated signals), signals are delivered synchronously, meaning that delivery occurs predictably and reproducibly at a certain point in the execution of a program.
    
314.  - By default, a signal either is ignored, terminates a process (with or without a core dump), stops a running process, or restarts a stopped process. The particular default action depends on the signal type. Alternatively, a program can use signal() or better, sigaction() to explicitly ignore a signal or to establish a programmer-defined signal handler function that is invoked when the signal is delivered.
    
315.  - A process (with suitable permissions) can send a signal to another process using kill(). Sending the null signal (0) is a way of determining if a particular process ID is in use.
    
316.  - Each process has a signal mask, which is the set of signals whose delivery is currently blocked.
    
317.  - If a signal is received while it is blocked, then it remains pending until it is unblocked. Standard signals can’t be queued; that is, a signal can be marked as pending (and thus later delivered) only once.
    
318.  - Using pause(), a process can suspend execution until a signal arrives.
    
319.  - Order of delivery of multiple unblocked signals:  the Linux kernel delivers the signals in ascending order. We can’t, however, rely on (standard) signals being delivered in any particular order, since SUSv3 says that the delivery order of multiple signals is implementation-defined.
    

321.  there's about 30 defined signals, 64 total signal.h
    
322.  default action is terminate
    
323.  kill -l
    

325.  SIGHUP  - 1 (restart/reload some apps)
    
326.  SIGINT  - 2 Ctrl-C
    
327.  SIGFPE  - 8
    
328.  SIGKILL - 9 terminate, can't catch or block
    
329.  SIGSEGV - 11 segmentation fault
    
330.  SIGTERM - 15 default
    
331.  SIGSTOP - SIGCONT - Ctrl-Z (can't block)
    

334.   The format string contained in the Linux-specific `/proc/sys/kernel/core_pattern` file controls the naming of all core dump files produced on the system.
    

337.  # Processes
    

339.  Process id PID is 2 bytes (65k values)
    

341.  ``#include <unistd.h>``
    

343.  syscalls: `getpid (), getppid ()`
    

345.  the `ps` utility has a pid,ppid option
    

347.  fork() and exec() family
    
348.  fork() returns child pid to parent or 0 to child
    

350.  exec family:
    

352.  - **P** (path): **execvp(), execlp()** : program name in current path (without p: need to give full path)
    
353.  - **V** : **execv(), execvp(), and execve()** : argument list: array of pointers to strings (L: C-style args)
    
354.  - **E** (env vars): **execve() and execle()** : aditional arg of array of env vars.
    

356.  Because exec replaces the calling program with another one, it never returns unless an error occurs.
    

358.  `execvp (program, arg_list);`
    

360.  ```
    
361.  char* arg_list[] = {"ls", "-l", NULL}
    
362.  ```
    

364.  # Signals
    

366.  `<signal.h>`
    

368.  Signal Disposition and handler.
    

370.  masking = ignoring
    

372.  - Linux Errors: SIGBUS, SIGSEV, SIGFPE (also cause program to terminate)
    
373.  - Signal from a process to another: SIGKILL, SIGTERM
    
374.  - Send a command to a running program: SIGUSR1, SIGUSR2.
    
375.      Also SIGHUP (wakeup/reload)
    

377.   `sigaction(signal, disp, previous disp)` to set signal dispostion, in the disp arg use `sa_handler`:
    

379.   - SIG_DFL : default disp
    
380.   - SIGIGN : ignore signal
    
381.   - pointer to signal-handler function.
    

383.  Signals are asysnchronous. signal-handler function should be minimal, typically they just record the signal happened, main program checks periodically. (A signal can arrive while at signal-handler).
    

385.  Even assigning a value to a global variable can be dangerous because the assignment may actually be carried out in two or more machine instructions, and a second signal may occur between them, leaving the variable in a corrupted state. If you use a global variable to flag a signal from a signal-handler function, it should be of the special type `sig_atomic_t`
    

387.  Process can send itself with abort the SIGABRT signal, which ends process and core dumps.
    

389.  `kill (pid, SIGTERM);` (permissions?)
    

391.  Exit codes convention 0: success, non-zero error.
    
392.  Two bytes. Between 0-127, exit codes above 128: terminated by a signal, 128 + signal number.
    

394.  # Children
    

396.  Parent & child can finish independently. For the parent to wait for the child to terminate and get its information: wait() 4 syscalls:
    

398.  ```
    
399.  wait (&child_status);  
    
400.  if (WIFEXITED (child_status))
    
401.  ```
    

403.  `waitpid` to wait for a particular child
    
404.  `wait3`cpu info, `wait4`more options about what child to wait for
    

406.  Zombie: child terminated without parent waiting, without cleanup (store child's termination status for ex)
    
407.  `ps` The child process is marked as defunct, and its status code is Z, for zombie.
    
408.  The init process automatically cleans up any zombie child processes that it inherits.
    

410.  `wait`  blocks. To clean up children asynchronously: When a child process terminates (or stopped etc), Linux sends the parent process the `SIGCHLD` signal, so parent can handle that. (Default disposition of this signal is do nothing)
    

412.  # Threads
    

414.  A child process gets a copy of memory, FDs etc. A **thread** doesn't get any copy, everything is shared among all threads.
    

416.  A process and all its threads are executing only one program. If a thread calls a exec(), all other threads end.
    

418.  **pthreads** : Linux implementation of the the POSIX standard thread API. `pthread.h`
    
419.  Thread ID. `pthread_t`gcc link library `-lpthread`
    

421.  All threads in a single program share the same address space.
    

423.  Each thread has its own call **stack**, however. This allows each thread to execute different code and to call and return from subroutines in the usual way. As in a single-threaded program, each invocation of a subroutine in each thread has its own set of local variables, which are stored on the stack for that thread.
    

425.  Sometimes it is desirable to duplicate a certain variable so that each thread has a separate copy. GNU/Linux supports this by providing each thread with a `thread-specific data area`.
    

427.  Use **fork** to create new processes or **pthread_create** to create threads.
    

429.  The Linux **clone** system call is a generalized form of *fork* and *pthread_create* that allows the caller to specify which resources are shared between the calling process and the newly created process. Also, clone requires you to specify the memory region for the execution stack that the new process will use. It should not ordinarily be used in programs.
    

431.  ### Synchronization
    

433.  **Synchronization**. Most threaded programs are concurrent programs. In particular, there’s no way to know when the system will schedule one thread to run and when it will run another. the system might even schedule multiple threads to run at literally the same time.
    

435.  The ultimate cause of most bugs involving threads is that the threads are accessing the same data. That’s one of the powerful aspects of threads, but it can also be dangerous.
    
436.  **Race conditions** bugs: the threads are racing one another to change the same data structure.
    

438.  To eliminate race conditions, you need a way to make operations **atomic**. An atomic operation is indivisible and uninterruptible; once the operation starts, it will not be paused or interrupted until it completes, and no other operation will take place mean- while.
    

440.  Linux provides **mutexes**, short for MUTual EXclusion locks. A mutex is a special lock that only one thread may lock at a time. If a thread locks a mutex and then a second thread also tries to lock the same mutex, the second thread is blocked, or put on hold. Only when the first thread unlocks the mutex is the second thread unblocked—allowed to resume execution.
    

442.  Linux guarantees that race conditions do not occur among threads attempting to lock a mutex; only one thread will ever get the lock, and all other threads will be blocked.
    

444.  Mutexes provide a mechanism for allowing one thread to block the execution of another. This opens up the possibility of a new class of bugs, called **deadlocks**. A deadlock occurs when one or more threads are stuck waiting for something that never will occur.
    

446.  A simple type of deadlock may occur when the same thread attempts to lock a mutex twice in a row.The behavior in this case depends on what kind of mutex is being used.Three kinds of mutexes exist:
    

448.  - Locking a **fast mutex** (the default kind) will cause a deadlock to occur.
    
449.  - Locking a **recursive mutex** does not cause a deadlock. The mutex remembers how many times pthread_mutex_lock was called on it by the thread that holds the lock; that thread must make the same number of calls to pthread_mutex_unlock before the mutex is actually unlocked and another thread is allowed to lock it.
    
450.  - Linux will detect and flag a double lock on an **error-checking mutex** that would otherwise cause a deadlock.The second consecutive call to pthread_mutex_lock returns the failure code `EDEADLK`.
    

452.  Occasionally, it is useful to test whether a mutex is locked without actually blocking on it. `pthread_mutex_trylock` for this purpose.
    

454.  A **semaphore** provides a method for blocking the threads while they wait for work to do.
    
455.  A semaphore is a counter that can be used to synchronize multiple threads. As with a mutex, Linux guarantees that checking or modifying the value of a semaphore can be done safely, without creating a race condition.
    

457.  Each semaphore has a counter value.
    
458.  A semaphore supports two basic operations:
    

460.  - A *wait* operation decrements the value of the semaphore by 1. If the value is already zero, the operation blocks until the value of the semaphore becomes positive
    
461.  - A *post* operation increments the value of the semaphore by 1. If the semaphore was previously zero and other threads are blocked in a wait operation on that semaphore, one of those threads is unblocked and its wait operation completes (which brings the semaphore’s value back to zero).
    

464.  **Deadlocks** can occur when two (or more) threads are each blocked, waiting for a condition to occur that only the other one can cause.
    
465.  There's a more general deadlock problem, which can involve not only synchronization objects such as mutexes, but also other resources, such as locks on files or devices. The problem occurs when multiple threads try to lock the *same* set of resources in *different orders*. The solution is to make sure that all threads that lock more than one resource lock them in the same order.
    

467.  **Signals**. In Linux, the behavior is dictated by the fact that threads are implemented as processes. Signals sent from outside the program are sent to the process corresponding to the main thread of the program. (this is not POSIX).
    
468.  Within a multithreaded program, it is possible for one thread to send a signal specifically to another thread: *pthread_kill(thread_ID, signal_number)*
    

470.  Threads should be used for programs that need fine-grained parallelism. For example, if a problem can be broken into multiple, nearly identical tasks, threads may be a good choice.
    

472.  ### IPC
    

474.  Linux command: **ipcs**
    

476.  **Shared memory** segments permit fast bidirectional communication among any number of processes. Each user can both read and write, but a program must establish and follow some protocol for preventing race conditions such as overwriting information before it is read. Unfortunately, Linux does not strictly guarantee exclusive access even if you create a new shared segment with IPC_PRIVATE.
    

478.  Syscalls to manage: **shm**
    
479.   shmget() : allocate
    
480.   shmctl() : control
    
481.   shmat()  : attach
    
482.   shmdt()  : detach
    

484.  Processes must coordinate access to shared memory. **Semaphores** are counters that permit synchronizing multiple threads. Linux provides a distinct alternate implementation of semaphores that can be used for synchronizing processes (called process semaphores or sometimes System V semaphores). Process semaphores are allocated, used, and deallocated like shared memory segments. Although a single semaphore is sufficient for almost all uses, process semaphores come in sets.
    

488.  **Mapped memory** can be used for interprocess communication or as an easy way to access the contents of a file.
    

490.  Mapped memory forms an association between a file and a process’s memory. Linux splits the file into page-sized chunks and then copies them into virtual memory pages so that they can be made available in a process’s address space. Thus, the process can read the file’s contents with ordinary memory access. It can also modify the file’s contents by writing to memory.This permits fast access to files.
    

492.  **mmap**
    
493.  mmap(
    
494.      address/NULL,
    
495.     byte length,
    
496.     protection PROT_READ |PROT_WRITE |PROT_EXEC,
    
497.     extra options flag MAP_FIXED | MAP_PRIVATE | MAP_SHARED,
    
498.     FD of file,
    
499.     offset from the beginning of the file from which to start the map)
    

501.  **munmap()** release. Linux automatically unmaps the file when the program terminates.
    

503.  Specify the MAP_SHARED flag so that any writes to these regions are immediately transferred to the underlying file and made visible to other processes.  
    
504.  If you don’t specify this flag, Linux may buffer writes before transferring them to the file.
    
505.  Alternatively, you can force Linux to incorporate buffered writes into the disk file by calling **msync()**
    

507.  The mmap call can be used for purposes other than interprocess communications. One common use is as a replacement for read and write. For example, rather than explic- itly reading a file’s contents into memory, a program might map the file into memory and scan it using memory reads. For some programs, this is more convenient and may also run faster than explicit file I/O operations.
    

509.  **Pipes**
    
510.  A pipe’s data capacity is limited. If the writer process writes faster than the reader process consumes the data, and if the pipe cannot store more data, the writer process blocks until more capacity becomes available. If the reader tries to read but no data is available, it blocks until data becomes available.Thus, the pipe automatically synchronizes the two processes.
    

512.  A call to pipe creates file descriptors, which are valid only within that process and its children. A process’s file descriptors cannot be passed to unrelated processes; however, when the process calls fork, file descriptors are copied to the new child process. Thus, pipes can connect only related processes.
    

514.  getconf PIPE_BUF path_to_file
    
515.  4096
    

517.  create a pipe:
    

519.  ```
    
520.  int fds[2];
    
521.  pipe (fds);
    
522.  ```
    

524.  duplicate FDs:
    

526.  ```
    
527.  #include <unistd.h>
    
528.  int dup(int oldfd);
    
529.  ```
    

531.  to redirect a process’s standard input to a file descriptor fd, use **dup2**:
    
532.  `dup2 (fd, STDIN_FILENO);`
    
533.  The symbolic constant STDIN_FILENO represents the file descriptor for the standard input, which has the value 0. The call closes standard input and then reopens it as a duplicate of fd so that the two may be used interchangeably.
    

535.  A common use of pipes is to send data to or receive data from a program being run in a subprocess.. Easier with popen() / pclose():
    

537.  `FILE* stream = popen (“sort”, “w”);`
    

540.  **FIFOs / Named Pipes**
    

542.  A first-in, first-out (FIFO) file is a pipe that has a name in the filesystem. Any process can open or close the FIFO; the processes on either end of the pipe need not be related to each other.
    

544.  `mkfifo /tmp/fifo`  
    
545.  ls -l: first char is **p** (named pipe)
    

547.  write `cat > /tmp/fifo` , read `cat < /tmp/fifo`
    

549.  syscall mkfifo(path, PERMISSIONS)
    

551.  Access a FIFO just like an ordinary file. To communicate through a FIFO, one program must open it for writing, and another program must open it for reading.
    

553.  A FIFO can have multiple readers or multiple writers. Bytes from each writer are written atomically up to a maximum size of PIPE_BUF (4KB on Linux). Chunks from simultaneous writers can be interleaved. Similar rules apply to simultaneous reads.
    

555.  **Sockets**
    
556.  A socket is a bidirectional communication device that can be used to communicate with another process on the same machine or with a process running on other machines.
    

558.  **socket(PF_UNIX or PF_INET, SOCK_STREAM or SOCK_DGRAM, 0 protocol)**
    
559.  socket (namespace, type, protocol) /. closes() : create / close
    
560.  connect() : client connects two sockets
    

562.  bind() : server binds address (labels socket to address)
    
563.  listen() : server accepts connections
    
564.  accept(): accepts connections and creates new connection
    

566.  Any technique to write to a file descriptor can be used to write to a socket. The send() function, which is specific to the socket file descriptors, provides an alternative to write with a few additional choices.
    

568.  Because it resides in a file system, a local socket is listed as a file (**s** in ls -l)
    

571.  **sparse files** lots of holes (empty space), so ls shows bigger space than df.
    
572.  fallocate: create empty file faster than filling with zeros
    
573.  dd if=/dev/zeros
    
574.  truncate
    

577.  /proc/loadavg : active , runnable processes
    

579.  ### Syscalls
    

581.  hostname -> **uname**()
    

583.  **access**(file_path, bitwise or of R_OK, W_OK, and X_OK or F_OK)
    
584.      returns: 0 good, -1 bad errno: EACCES, EROFS, ENOENT
    

586.  **fcntl** (FD, operation): access point for several advanced operations on file descriptors.
    
587.  Allows a program to place a read lock or a write lock on a file, somewhat analogous to the mutex locks.
    

589.  More than one process may hold a read lock on the same file at the same time, but only one process may hold a write lock. File may not be both locked for R & W
    

591.  Note that placing a lock does not actually prevent other processes from opening the file, reading from it, or writing to it, unless they acquire locks with fcntl as well.
    

593.  **flock** also.The fcntl version has a major advantage: It works with files on NFS3 file systems
    

595.  **fsync** (FD) flushes buffer to disk. Blocks until finishes. Updates file's modification time, 2 writes. **fdatasync** does not update mod time, so faster 1 write.
    
596.  Also: **open(O_SYNC)** for synchronous I/O, which causes all writes to be committed to disk immediately.
    

598.  Shell ulimit -> **getrlimit** ,  **setrlimit** superuser for hardlimits
    

600.  **getrusage** system call retrieves process statistics from the kernel.
    

602.  **gettimeofday**
    

604.  **mlock**(start memory, length). Any pages in range will be locked. For performance. security reasons.
    
605.  **mlockall** lock a program's entire memory space
    
606.  **munlock** to unlock
    
607.  Have to be superuser for mlock
    

609.  If a program attempts to perform an operation on a memory location that is not allowed by these mmap permissions, it is terminated with a SIGSEGV (segmentation violation) signal.
    
610.  With mmap or **mprotect** and a SIGSEGV handler to monitor memory access
    

613.  **readlink** system call retrieves the target of a symbolic link
    

615.  **sendfile** system call provides an efficient mechanism for copying data from one file descriptor to another (the intermediate buffer can be eliminated).
    

617.  **setitimer** system call is a generalization of the alarm call. It schedules the delivery of a signal at some point in the future after a fixed amount of time has elapsed.
    

619.  **sysinfo** system call fills a structure with system statistics: uptime, totalram, freeram, procs
    

622.  —
    

624.  A directory that has the sticky bit set allows you to delete a file only if you are the owner of the file.
    

626.  whoami command is just like id, except that it shows only the effective user ID,
    
627.  su program is a setuid program
    

629.  —
    
630.  Vilgrind, (gdb)
    

632.  Finding Memory Leaks Using **mtrace**: The mtrace tool helps diagnose the most common error when using dynamic memory: failure to match allocations and deallocations.
    

634.  The **ccmalloc library** diagnoses dynamic memory errors by replacing malloc and free with code tracing their use. Link in gcc.
    

636.  **Electric Fence** halts executing programs on the exact line where a write or a read outside an allocation occurs.
    

638.  —
    

640.  The **stat** call obtains this information about a file. Call stat with the path to the file you’re interested in and a pointer to a variable of type struct stat. If the call to stat is successful, it returns 0 and fills in the fields of the structure with information about that file; otherwise, it returns -1.
    

642.  —
    
643.  pidof
    

645.  1.  Introduction to the Linux Kernel
    

647.  2.  2  Getting Started with the Kernel
    

649.  3.  3  Process Management 23
    

651.  4.  5  System Calls 69
    
652.     ...
    

654.  5.  12  Memory Management 231
    

656.  6.  13  The Virtual Filesystem 261
    

658.  ---
    

660.  There are often times in a kernel when you do not want to do work at this moment. A good example of this is during interrupt processing. When the interrupt was asserted, the processor stopped what it was doing and the operating system delivered the interrupt to the appropriate device driver. Device drivers should not spend too much time handling interrupts as, during this time, nothing else in the system can run. There is often some work that could just as well be done later on. **Linux's bottom half handlers** were invented so that device drivers and other parts of the Linux kernel could queue work to be done later on.
    

662.  The kernel's interrupt handling data structures are set up by the device drivers as they request control of the system's interrupts. To do this the device driver uses a set of Linux kernel services that are used to request an interrupt, enable it and to disable it.
    
663.  The individual device drivers call these routines to register their interrupt handling routine addresses.
    

665.  Some interrupts are fixed by convention for the PC architecture and so the driver simply requests its interrupt when it is initialized. This is what the floppy disk device driver does; it always requests IRQ 6. There may be occassions when a device driver does not know which interrupt the device will use. This is not a problem for PCI device drivers as they always know what their interrupt number is. Unfortunately there is no easy way for ISA device drivers to find their interrupt number. Linux solves this problem by allowing device drivers to probe for their interrupts.
    

667.  First, the device driver does something to the device that causes it to interrupt. Then all of the unassigned interrupts in the system are enabled. This means that the device's pending interrupt will now be delivered via the programmable interrupt controller. Linux reads the interrupt status register and returns its contents to the device driver. A non-zero result means that one or more interrupts occured during the probe. The driver now turns probing off and the unassigned interrupts are all disabled.
    

669.  ----
    

671.  - read() and write() can be used with files, sockets, pipes. They block.  
    

673.  - select(), poll(), epoll() don't block; once called, they will return a list of file descriptors that are ready.  
    

675.  - epoll() doesn't work with files.
    

677.  - For storage I/O classically the blocking problem has been solved with thread pools.
    

680.  - database software may not want to use the Linux page cache. It then became possible to open a file and specify that we want direct access to the device. Direct access, commonly referred to as Direct I/O, or the O_DIRECT flag.
    

682.  - With Linux 2.6, the kernel gained an Asynchronous I/O (linux-aio for short) interface. Asynchronous I/O in Linux is simple at the surface: you can submit I/O with the io_submit system call, and at a later time you can call io_getevents and receive back events that are ready. It allows programmers to make their code fully asynchronous. But due to the way it evolved, it fell short of these expectations.
    

684.  - io_uring:  truly asynchronous, works with any kind of I/O.
    

686.  Alexei (eBPF) and Jens (io_uring, block) work at Facebook
    

688.  ----
    

690.  5 Process States:  (state field in struct **task_struct**)
    

692.  D uninterruptible sleep (usually IO), can't kill with signal TASK\_UNINTERRUPTIBLE  
    
693.   R running or runnable (on run queue) TASK\_RUNNING  
    
694.   S interruptible sleep (waiting for an event/signal to complete) TASK\_INTERRUPTIBLE  
    
695.   T, t stopped, either by a job control signal or because it is being traced. \__TASK\_STOPPED  , \__TASK\_TRACED
    

697.  **System calls** and **exception handlers** are well-defined interfaces into the kernel.A process can begin executing in kernel-space only through one of these interfaces—all access to the kernel is through these interfaces.
    

699.  Most exceptions issued by the CPU are interpreted by Linux as **1) error conditions**. If, for instance, a process performs a division by zero, the CPU raises a “Divide error " exception, and the corresponding exception handler sends a SIGFPE signal to the current process.
    

701.  There are a couple of cases, however, where Linux exploits CPU exceptions to **2) manage hardware resources** more efficiently, like **Page Fault** exception; whenever the kernel tries to access an address that is currently not accessible, the CPU generates a page fault exception and calls the page fault handler.
    

703.  The first, fork(), creates a child process that is a copy of
    
704.  the current task. It differs from the parent only in its PID (which is unique), its PPID (parent’s PID, which is set to the original process), and certain statistics andresources, such as pending signals, which are not inherited.The second function,exec(), loads a new executable into the address space and begins executing it.
    

706.  In Linux, fork() is implemented through the use of copy-on-write pages. **Copy-on-write** (or COW) is a technique to delay or altogether prevent copying of the data. Rather than duplicate the process address space, the parent and the child can share a single copy. For example, if exec() is called immediately after fork()—they never need to be copied.
    

708.  Linux implements fork() via the **clone()** system call.
    

710.  The **vfork()** system call has the same effect as fork(), except that the page table entries
    
711.  of the parent process are not copied. Instead, the child executes as the sole thread in the
    
712.  parent’s address space, and the parent is blocked until the child either calls exec() or exits.  
    

714.  **Threads** provide multiple threads of execution within the same program in a *shared memory address space*. They can also share open files and other resources. Threads enable concurrent programming and, on multiple processor systems, true parallelism. Linux has a unique implementation of threads.To the Linux kernel, there is no concept of a thread. Linux implements all threads as standard processes.  
    

716.  Threads are created the same as normal tasks, with the exception that the clone() system call is passed **flags** corresponding to the specific resources to be shared:
    
717.  `clone(CLONE_VM | CLONE_FS | CLONE_FILES | CLONE_SIGHAND, 0);`  
    

719.  It is often useful for the kernel to perform some operations in the background.The kernel accomplishes this via **kernel threads** — standard processes that exist solely in kernel- space.The significant difference between kernel threads and normal processes is that kernel threads do not have an address space.
    

721.  All new kernel threads are forked off the **kthreadd** kernel process.  
    

723.  When a process **terminates**, the kernel releases the resources owned by the process and notifies the child’s parent of its demise. The bulk of the work is handled by do_exit(), defined in kernel/exit.c, which completes a number of chores. EXIT\_ZOMBIE enables the system to obtain information about a child process after it has terminated. After the parent has obtained information on its terminated child, or signified to the kernel that it does not care, the child’s task_struct is deallocated.  
    

726.  ----
    

728.  The kernel can preempt a task running in the kernel so long as it does not hold a lock.
    

730.  ----
    

732.  The mechanism to signal the kernel is a software interrupt: Incur an exception, and the system will switch to kernel mode and execute the exception (system call) handler.
    

734.  The defined software interrupt on x86 is interrupt number **128**. The system call number must be passed into the kernel. On x86, the syscall number is fed to the kernel via the **eax** register.
    

736.  Recently, x86 processors added a feature known as **sysenter**. This feature provides a faster, more specialized way of trapping into a kernel to execute a system call than using the int interrupt instruction.  
    

739.  Interrupts enable hardware to asynchronously signal to the processor.  
    

741.  The **device driver** that manages a given piece of hardware registers an interrupt handler.
    

743.  These two goals—that an interrupt handler execute quickly and perform a large amount of work—clearly conflict with one another. Because of these competing goals, the processing of interrupts is split into two parts, or halves. The interrupt handler is the **top half**. The top half is run immediately upon receipt of the interrupt and performs only the work that is time-critical, such as acknowledging receipt of the interrupt or resetting the hardware. Work that can be performed later is deferred until the **bottom half**.  
    

745.  Botton half uses three mechanisms: **softirqs**, **tasklets**, and **work queues**.
    

747.  **/proc/interrupts** : statistics related to interrupts on the system.
    

749.  ----
    

751.  The kernel performs all page I/O through the page cache.  
    

753.  Writes are maintained in the page cache through a process called write-back caching, which keeps pages “dirty” in memory and defers writing the data back to disk.The flusher “gang” of kernel threads handles this eventual page writeback.
    

755.  Linux implements a modified version of LRU, called the **two-list strategy**. Instead of maintaining one list, the LRU list, Linux keeps two lists: the **active list** and the **inactive list**. Pages on the active list are considered “hot” and are not available for eviction. Pages on the inactive list are available for cache eviction. Pages are placed on the active list only when they are accessed while already residing on the inactive list. Both lists are maintained in a pseudo-LRU manner: Items are added to the tail and removed from the head, as with a queue. The lists are kept in balance: If the active list becomes much larger than the inactive list, items from the active list’s head are moved back to the inactive list, making them available for eviction.The two-list strategy solves the only-used-once failure in a classic LRU and also enables simpler, pseudo-LRU semantics to perform well.This two-list approach is also known as LRU/2; it can be generalized to n-lists, called LRU/n.
    

757.  ----
    

759.  Magic SysRq key (system request) key: Alt+PrintScreen (PC).  
    
760.  `echo 1 > /proc/sys/kernel/sysrq`
    

762.  Ex: SysRq-i Sends a SIGKILL to all processes except init.
    

764.  ----
    

766.  Anycast: multiple servers, 1 IP address
    

768.  unshare command: namespaces
    

770.  lsb_release: distro specific info
    

772.  nproc: number of CPUs
    

774.  List processes that are consuming most CPU: ps aux| awk '{print $3, $2, $11}' | head -n 15