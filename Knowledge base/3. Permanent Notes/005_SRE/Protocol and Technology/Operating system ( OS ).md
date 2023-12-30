Tags: #operatingsystem #os



### kernel vs user space
- user space : location in memory where normal process run
- kernel process: location in memory where code and data of kernel is stored
- Process running in user space has access to limited part of memory where as process running on kernel space has access to all the memory.
- user space processes can only access a small part of the kernel via an interface exposed by the kernel - system calls.
- If a process performs a system call, a software interrupt is sent to the kernel, which then dispatches the appropriate interrupt handler and continues its work after the handler has finished


### system calls
- an interface exposed by kernel so that process in user space can interact with the kernel




### fork(), vfork(), exec(), clone()
fork: 

vfork:

exec:

clone:

### Pipe ( Named and Anonymous )
- pipe are used for IPC ( inter process communication)
- two types
	- anonymous: cat "asd" | grep ( here. "|" is anonymous pipe); one-to-one
	- named pipe: It is also called FIFO. It was be used to communication between unrelated process. done using `mkfifo PIPENAME`
```
mkfifo pip1
cat "hello" > pipe1 &
cat < pipe1

```

**named pipe vs anonymous pipe**
- no need to read immediately
- can have multiple readers



### LInux signal
- If a program which uses `kill` signal, it kernel first checks if sending program has permission. If no error is returned
- If it is one of the two special signal SIGKILL and SIGSTOP, kernel acts on them
- 
### Dmesg and Syslog

**dmesg**: It is used to store diagnostic messages from kernel and device drivers, hardware, low level system events.
**syslog**: It allows program to log message in a standardized way. It is used by various daemons and services for logging events.
### Files in linux
- Regular file
- directories
- character device files
- block device file
- local domain socket
- named pipes
- sym links

### RAID
0: block level stripping
1: data mirroring
2: Stripping with parity check
3:  Not used ( same as 2 but in byte level)
4: block level stripping with dedicated parity
5: Block level stripping with distributed parity. Parity information is distributed across devices
6: Mirroring stripping. Sustain loss of multiple drives

Hardware based RAID: RAID controller
Software based: ZFS

### JBOD

### System calls
System calls  is an interface which allows application in user space to communication with kernel to access resources.

Process management: fork(), clone(), wait(), exit() exec()
File system call: open(), read(), close(), write()
network syste,m calls: socket(), bind(), listen(), accept(),connect(),send(),recv()
Device management: getpid(), getppid(),getuid(),getgid(),uname(),sysinfo(),time()


### File descriptor
When we create a file, OS creates an entry in system ( integer ) to represent that file somewhere in kernel. This is called file descriptor.

Information about opened file is stored in that file descriptor. it is a non negative number

Similar if OS creates a socket connection it is called socket descriptor

Some famous one are: STDIN(0),STDOUT(1),STDERR(2) >&2

```
lsof -p 78894
COMMAND   PID      USER   FD   TYPE DEVICE SIZE/OFF                NODE NAME
sleep   78894 pgaijin66  cwd    DIR    1,4      384            16620182 /Users/pgaijin66/src/github/vargant-files
sleep   78894 pgaijin66  txt    REG    1,4   133904 1152921500312422327 /bin/sleep
sleep   78894 pgaijin66    0u   CHR   16,0 0t222268                2203 /dev/ttys000
sleep   78894 pgaijin66    1u   CHR   16,0 0t222268                2203 /dev/ttys000
sleep   78894 pgaijin66    2u   CHR   16,0 0t222268                2203 /dev/ttys000

```

Other than file FD can point to other types of resources like
- files
- stdin, stdout, stderr
- pipes
- sockets
- devices
- special files `/dev/null`
- anonymous pipes
- memory mapped files
### Virtual Memory
- intro
- Address space:
	- user space and kerner space
- Page table
- Page fault
- Demand paging
- Swapping
- Memory mapping
	- types of memory mapping
- Memory allocation strategies
	- Continuous: can lead to fragmentation
	- Non-continuous; When there is no continuous memory available to allocate to process
		- Dynamic
		- best fit
		- worst fit
		- first fit
		- next fit
- OOM
- Tools for memory management
- Hude pages
- Transparent huge pages THP
- Memory ballooning: Automatically adjust memory dynamically


**Thrashing**
It is the situation where system spends more time moving pages from disk to memory and not doing useful work.


**What is `SuperBlock`, `Inode`, `Dentry`, `FIle` ?**

`Block`: Smaller unit of data that can be stored or read in a storage device. For EXT4 default block size in 4KB.

`Superblock`: It is a high level metadata structure for the filesystem. It is very critical to filesystem hence multiple redundant copy is stored.

`Inode`: Inode is a data structure used to represent metadata about a file. One thing to remember is actual filename is not stored in Inode. It contains metadata like ownership, file tpe, size, access modes, atime, ctime, mtime, data blocks

inode are deleted when all the link are deleted

Ext4: fixed size inodes
XFS: inodes on demand

By data block in inode, there are two blocks ( direct and indirect)

direct block: inode contains the block number of a block which contains the actual data

`Dentry`: Also called "directory entry". Used by linux to keep track of the hierarchy of files in a directory. 

Basically maps inode number to a file name and parent directory

`File`: It is an open file associated with a process. It is just a block of logically related arbitrary data. It is used to represent file optned by a process.

`Directory`: It is a special type of file that stores information about the files they contain. They contain filename and their respective inode number

Virtual file system
- Allow client application to access different tpes of concrete file systems in a uniform way.


**How does recovery tools can recover the data deleted ?**

- When file is deleted, its block is not deleted
- unlink() system call removes the inode from dentry ( aka directory entry)
- inode is marked as unlinked
- block space where previous data was is marked available or free for new data.
- Actual data block is not deleted immediately but instead it remains on the disk until it is required for storing new data
- This is why recovery tools can sometime retrive deleted files and data blocks.


**Why no filename are stored in inode?**
- To make use of hardlink



**ZFS Copy on write (COW) filesystem:**
They do deletion differently


**Hardlink vs softlink**
- Hardlink:
	- points to inode
	- Cannot span network ( NFS )
	- inode metadata (link) is increased
	- deleting, renaming, moving does not affect hard link
	- All hard linked file have same permission
	- If original linked file is delete, it does not affect the hard link
- Softlink
	- points to a file
	- can span network
	- Can have different file permission
	- File original linked file is deleted, soft linked file is affected

https://linuxgazette.net/105/pitcher.html


### /proc
It is a special filesystem that holds information about current state of system, memory, processes etc