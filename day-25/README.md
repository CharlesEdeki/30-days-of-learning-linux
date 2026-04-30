# Day 25 - Linux Processes: Resource Management and System Inspection

## Objective
Understand what a process is and how it competes for system
resources, how to inspect running processes using ps and top,
how threads differ from processes, and how to trace what a
process is doing at the system call level using lsof and strace.

---

## What I Learned

### What a Process Actually Is
- A process is a running instance of a program
- Every process competes for three resources: CPU time,
  RAM, and I/O
- The kernel referees this competition, allocating resources
  fairly so no process starves everything else
- When a process needs to do something privileged (open a file,
  allocate memory, talk to hardware) it asks the kernel through
  a system call
- System calls are happening thousands of times per second
  across every running program on the system

### ps and top: Two Ways to Look at the Same Thing
- ps gives a snapshot of the system at one moment
- ps aux dumps every process with CPU usage, memory usage,
  state, and the command that started it
- A process that spiked and settled looks completely normal
  in ps output — it captures one instant only
- top gives a live broadcast, updating every second

Key top shortcuts:
```
M    sort by memory usage
P    sort by CPU usage (default)
T    sort by total cumulative CPU time
u    show only one user's processes
H    toggle thread display on and off
```

- htop and atop are alternatives with better formatting
  and more detail — worth using if available

### Threads: When One Process Is Actually Many
- Some processes split work across multiple threads
- A thread shares memory and resources with other threads
  inside the same process
- Multiple threads can run simultaneously on different CPU
  cores — this is why a single process can saturate all cores
- Every process starts with one thread: the main thread
- Additional threads have their own thread IDs but share
  the same process ID
- By default neither ps nor top shows threads

To see threads:
```bash
ps m                          # show threads under each process
ps m -o pid,tid,command       # show thread IDs explicitly
```

- Lines with a PID number are processes
- Lines with a dash in the PID column are threads belonging
  to the process above them
- For a single-threaded process the thread ID equals the process ID
- Threads start faster than processes and communicate more
  efficiently through shared memory

### lsof: Looking at Open Files
- lsof lists open files — on Linux that means everything:
  regular files, network connections, shared libraries,
  pipes, devices
- If a process has it open, lsof can show it

```bash
lsof | less                   # broad sweep, large output
lsof +D /usr                  # everything open under /usr
lsof -p 1234                  # everything open by process 1234
```

Key output columns:
- PID: the process
- USER: who is running it
- FD: file descriptor number or purpose of the file
- TYPE: regular file, network socket, pipe, or other
- NAME: actual filename or address

- A file descriptor is the number a process uses to identify
  an open file. The kernel hands back a number when a file
  is opened; every read, write, and close uses that number
- FD 0 is standard input, 1 is standard output, 2 is standard
  error. Everything else starts from 3 upward
- lsof is the tool to reach for when a disk will not unmount
  because something is holding a file open

### strace: Watching the Conversation with the Kernel
- strace prints every system call a process makes as it runs
- The tool for programs that crash immediately, fail silently,
  or behave unexpectedly with no visible error message

```bash
strace cat /dev/null          # trace a command from the start
strace -o output.txt cat /dev/null   # save trace to a file
strace -o daemon_trace -ff crummyd   # trace a daemon and all
                                     # child processes it forks
```

- Output starts with execve(), the system call that starts
  the program, followed by memory setup and library loading
- Return value of -1 means the system call failed
- Error codes like ENOENT tell you exactly why it failed
- -ff flag follows every fork and writes each child's trace
  to a separate file named daemon_trace.pid

Example of a successful open:
```
openat(AT_FDCWD, "/dev/null", O_RDONLY) = 3
read(3, "", 131072)                     = 0
close(3)                                = 0
exit_group(0)                           = ?
```

Example of a failed open:
```
openat(AT_FDCWD, "not_a_file", O_RDONLY) = -1 ENOENT (No such file or directory)
```

### ltrace: One Level Below strace
- ltrace does the same thing as strace but at the shared
  library level rather than the kernel level
- Shows calls to functions in shared libraries like libc
  instead of system calls
- Shared library calls happen far more frequently than
  system calls — output needs heavy filtering
- Does not work on statically linked binaries

---

## What I Built / Practiced

```bash
# View all processes
ps aux

# Live process monitor
top

# View threads under each process
ps m
ps m -o pid,tid,command

# List all open files
lsof | less

# Files open under a directory
lsof +D /usr

# Files open by a specific process
lsof -p 1234

# Trace system calls of a command
strace cat /dev/null

# Save strace output to file
strace -o output.txt cat /dev/null

# Trace a daemon and all its children
strace -o daemon_trace -ff crummyd
```

---

## Challenges Faced
- The relationship between processes and threads required
  re-reading. Key insight: the kernel actually schedules
  threads, not processes. For single-threaded processes
  they are identical. For multithreaded processes the kernel
  is managing several concurrent execution paths that happen
  to share the same address space
- strace output is verbose. Learning to read it from the
  bottom up, where the actual work happens, rather than
  top down, where library loading dominates, helped

---

## Key Takeaways
- A process competes for CPU, RAM, and I/O. The kernel
  referees that competition through scheduling
- ps captures one moment. top broadcasts continuously.
  Use ps to find something specific, top to watch something live
- The kernel schedules threads, not processes. A single
  process can use all CPU cores if it runs multiple threads
- lsof answers the question: who has this file open.
  Essential for stuck mounts and locked ports
- strace makes the kernel conversation visible. Every program
  failure is ultimately a system call that returned an
  unexpected value. strace shows exactly which one and why
- ltrace works one level higher than strace, at the shared
  library boundary, but is less commonly needed

### When to Use Which Tool
- ps or top: something is slow or consuming too much resource
  and you need to find what
- lsof: a file is locked, a port is in use, or a disk will
  not unmount
- strace: a program crashes or fails silently and you need
  to see exactly what it was doing at the kernel level
- ltrace: a program fails in a way that looks like a library
  call issue rather than a system call issue

---

## Commands Used

```bash
ps aux                        # snapshot of all processes
ps m                          # show threads under processes
ps m -o pid,tid,command       # show thread IDs explicitly
top                           # live process monitor
lsof | less                   # list all open files
lsof +D /path                 # files open under a path
lsof -p PID                   # files open by a process
strace command                # trace system calls
strace -o file.txt command    # save trace to file
strace -o trace -ff daemon    # trace daemon and children
ltrace command                # trace shared library calls
```

---

## Resources
- How Linux Works by Brian Ward, chapter 8
- [Medium: Day 25 write-up](https://medium.com/@charlesedeki093/day-25-linux-processes-resource-management-and-system-inspection-df2cfee5cfd7)

---

## Output
- Day 25 write-up published on Medium
