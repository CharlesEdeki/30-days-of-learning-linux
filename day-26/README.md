# Day 26 - When the System Cannot Keep Up

## Objective
Learn how to diagnose a slow or struggling Linux system using
time, uptime, vmstat, iostat, iotop, pidstat, and related tools.
Understand demand paging, page faults, and CPU scheduling priority.

---

## What I Learned

### time: Getting a Receipt
- time returns three numbers: user, system, and real time
- User time: CPU time spent running the program's own code
- System time: CPU time the kernel spent doing work on behalf
  of the program (file reads, directory lookups)
- Real time: wall-clock time from start to finish
- The gap between real time and user+sys is waiting time:
  for disk, network, or a turn on the CPU
- A process with 10s real but 0.5s CPU was not slow, it was idle
- /usr/bin/time gives more detail than the shell built-in time

### uptime: Is This a Moment or a Trend
- Shows three load averages: 1 minute, 5 minutes, 15 minutes
- Load average counts processes ready to run or waiting for CPU
- Idle processes waiting for input do not count
- 1-minute much higher than 15-minute: recent spike
- All three high and climbing: sustained problem
- High load does not automatically mean CPU trouble.
  Memory pressure often shows up as high load first

### vmstat: The Full Dashboard
- vmstat 2 updates every 2 seconds
- Ignore the first line, it averages across entire uptime
- CPU columns: us (user), sy (system), id (idle), wa (I/O wait)
- High wa means CPU is waiting for disk reads or writes
- Memory columns: si (pages swapped in), so (pages swapped out)
- Non-zero and climbing si/so means active memory pressure
- b: processes blocked waiting for memory pages
- swpd: total memory swapped to disk
- bi/bo: blocks read and written to disk

### Demand Paging and Page Faults
- The kernel loads pages only as the program reaches for them
- A page is typically 4096 bytes
- Minor page fault: page is in memory but MMU does not know where.
  Normal and frequent, kernel updates records and continues
- Major page fault: page is not in memory, must load from disk.
  A few are unavoidable. Many means memory pressure
- Thrashing: kernel constantly swapping working pages to disk.
  Shown by climbing si/so in vmstat
- /usr/bin/time shows fault counts after running a command
- Re-running same command drops major faults to zero (cached)
- ps -o pid,min_flt,maj_flt pid shows fault counts per process
- In top: press f, select nMaj for total major faults per process

### iostat: Which Disk
- Shows disk activity per device
- tps: transfers per second
- kB_read/s and kB_wrtn/s: live read and write rates
- iostat 2: live updates. iostat -p ALL: includes partitions
- Tells you the disk is busy, not which process is doing it

### iotop: Which Process
- Looks like top but sorted by disk I/O
- IO> column: percentage of time a thread is waiting for I/O
- A process at 90% IO> is frozen waiting on disk
- Identifies the process responsible for disk pressure

### CPU Scheduling and renice
- Priority range: -20 (highest) to 20 (lowest)
- PR column in top: current priority. NI: nice value
- renice 20 pid pushes a background job to lowest priority
- Useful when a heavy job is slowing everything else down

### I/O Priority with ionice
- Three classes: be (best effort), rt (real time), idle
- ionice adjusts I/O scheduling class and priority
- Rarely needed but useful for heavy background I/O jobs

### pidstat: Watching Over Time
- top and iotop overwrite old data on each refresh
- pidstat keeps all history visible on screen
- pidstat -p pid 1: CPU monitoring every second
- pidstat -r -p pid 1: memory monitoring
- pidstat -d -p pid 1: disk monitoring
- Shows patterns over time, not just snapshots

---

## Diagnostic Flow

```
Something feels slow
       ↓
time command        → where is the time going?
       ↓
uptime              → is the whole system feeling it?
       ↓
vmstat 2            → watch wa, si, so
       ↓
if wa is high:
iostat 2            → which disk is busy?
       ↓
iotop               → which process is doing it?
       ↓
renice 20 pid       → push it aside if needed
       ↓
pidstat -p pid 1    → watch it over time
```

---

## Commands Used

```bash
time ls                          # shell built-in time
/usr/bin/time cal > /dev/null    # full time with fault counts
uptime                           # load averages
vmstat 2                         # system stats every 2 seconds
getconf PAGE_SIZE                # confirm page size
ps -o pid,min_flt,maj_flt pid    # page fault counts per process
iostat                           # disk activity snapshot
iostat 2                         # live disk activity
iostat -d 2                      # disk only view
iostat -p ALL                    # include partitions
iotop                            # live I/O by process
renice 20 pid                    # lower process CPU priority
ionice -c 3 -p pid               # set idle I/O class
pidstat -p pid 1                 # CPU stats over time
pidstat -r -p pid 1              # memory stats over time
pidstat -d -p pid 1              # disk stats over time
```

---

## Key Takeaways
- The gap between real time and user+sys is where the problem
  usually hides. That gap is waiting time, not computing time
- High load average often means memory pressure, not CPU trouble
- Major page faults during normal operation means thrashing
- wa in vmstat is the first sign of disk trouble. si and so
  confirm memory pressure is involved
- The diagnostic flow: time → uptime → vmstat → iostat →
  iotop → renice → pidstat
- pidstat keeps history visible. Use it when you need patterns
  over time, not just the current moment

---

## Resources
- How Linux Works by Brian Ward, chapter 8
- [Medium: Day 26 write-up](https://medium.com/@charlesedeki093/day-26-when-the-system-cannot-keep-up-78435b281063)

---

## Output
- Day 26 write-up published on Medium
