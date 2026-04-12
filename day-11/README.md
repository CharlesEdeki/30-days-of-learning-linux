# Day 11 - Everything is a File: Linux Device Files

## Objective
Understand how Linux represents hardware devices as files, what device
files actually are, and how the four types of device files differ in
how they move data.

---

## What I Learned
- Linux represents hardware devices as files stored in the `/dev`
  directory. This is what "everything is a file" actually means in
  practice
- When a user writes to a regular file, data goes to disk. When a
  user writes to a device file, the kernel intercepts it and passes
  it to a device driver
- Device files are identified by the first character in `ls -l`
  output: `b` for block, `c` for character, `p` for pipe, `s` for
  socket
- Block devices store data in fixed chunks and support random access.
  All disks fall into this category
- Character devices work with data streams. No fixed size, no random
  access. Keyboards, mice, printers, and serial ports are examples
- Pipe devices (named pipes / FIFOs) pass data between processes
  rather than to hardware. A write blocks until a reader is present
  and a read blocks until a writer arrives
- Socket devices are like named pipes but bidirectional. Processes
  can send and receive through the same socket simultaneously
- The major and minor device numbers visible in `ls -l` output are
  how the kernel identifies specific devices internally

---

## What I Built / Practiced
- Ran `ls /dev` to explore the device file directory
- Ran `ls -l` to read device type characters and permissions
- Demonstrated named pipe behaviour across two terminals:

```bash
# Terminal 1 — reader blocks and waits
cat < sample_pipe

# Terminal 2 — writer unblocks the reader
echo "hello world" > sample_pipe
```

Data appeared in Terminal 1 the moment Terminal 2 wrote. No network,
no shared memory, just a file in the filesystem acting as a conduit
between two processes.

---

## Challenges Faced
- Understanding that `/dev/null` discards data rather than storing it.
  Resolved by understanding that device files are doorways to kernel
  behaviour, not storage locations

---

## Key Takeaways
- Device files are not storage. They are interfaces. Writing to one
  triggers kernel behaviour, not a disk write
- The four device types are four different answers to the same
  question: how should data move? Block devices store it. Character
  devices stream it to hardware. Pipes stream it between processes.
  Sockets stream it both ways between processes
- `/dev/null` is one of the most useful devices on the system. It
  accepts anything written to it and discards it silently, useful for
  suppressing unwanted output in scripts

---

## Commands Used

```bash
ls /dev                        # list all device files
ls -l /dev                     # list with type characters and permissions
echo blah > /dev/null          # send output to null device, discards it
cat < sample_pipe              # read from a named pipe, blocks until writer
echo "hello world" > sample_pipe  # write to named pipe, unblocks reader
```

---

## Resources
- How Linux Works by Brian Ward, chapter 3
- [Medium: Day 11 write-up](https://medium.com/@charlesedeki093/day-11-everything-is-a-file-linux-device-files-4bf28a2764cd)

---

## Output
- Named pipe practical demonstration completed across two terminals
- Day 11 write-up published on Medium
