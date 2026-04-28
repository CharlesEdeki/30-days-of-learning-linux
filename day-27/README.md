# Day 27 - Putting Processes in a Box: Control Groups

## Objective
Understand how Linux control groups (cgroups) work, the difference
between cgroup v1 and v2, how to read existing cgroups on a running
system, and how to create and manipulate cgroups to enforce resource
limits.

---

## What I Learned

### The Problem cgroups Solve
- renice asks the scheduler to deprioritise a process but does
  not stop it from eventually consuming whatever it wants
- cgroups build a hard box around processes and enforce limits
  at the kernel level, not as preferences but as ceilings
- Create a group, put processes into it, attach controllers,
  set limits. The kernel enforces them.

### What cgroups Are
- Control groups is a kernel feature, lives in kernel space
- You create a group, put one or more processes into it, and
  attach controllers to govern specific resource types
- CPU controller: limits processor time the group can consume
- Memory controller: limits how much RAM the group can use
- pids controller: limits how many processes the group can spawn
- systemd makes heavy use of cgroups but cgroups exist
  independently of systemd

### cgroup v1 vs v2
- Both versions can be active on the same system simultaneously
- v1: each controller type has its own independent hierarchy.
  A process can belong to multiple cgroups, one per controller.
  CPU hierarchy is separate from memory hierarchy
- v2: unified structure. A process belongs to exactly one cgroup.
  Multiple controllers attach to that single cgroup
- v1 was flexible in theory but complicated in practice.
  Separate hierarchies per controller made things hard to reason about
- v2 simplified to: one process, one cgroup, multiple controllers
- v1 is being phased out. Current discussion focuses on v2

### Reading Existing cgroups
- Every process on Linux is already inside a cgroup
- cat /proc/self/cgroup shows which cgroup the current process
  belongs to
- Lines numbered 1-12 are v1 entries with controller names
- Line 0 is v2. On a v2-only system this is the only line
- cgroups are accessed through the filesystem at /sys/fs/cgroup
- The name in /proc/self/cgroup is a path relative to that mount

### Key Interface Files
- cgroup.procs: lists PIDs of all processes in the cgroup
- cgroup.threads: lists individual thread IDs
- cgroup.controllers: shows which controllers are active
- pids.current: number of threads currently running in the cgroup
- memory.max: memory ceiling. max means no specific limit set
- cpu.stat: accumulated CPU time for the entire cgroup lifetime
  - usage_usec: total CPU microseconds
  - user_usec: user space CPU time
  - system_usec: kernel CPU time
- memory.current: live memory usage (if memory controller enabled)
- memory.stat: full memory breakdown

### Why cpu.stat Is Useful
- A service that spawns and reaps worker processes makes per-process
  CPU tracking difficult
- cpu.stat for the service's cgroup gives total CPU across
  everything it has ever spawned across its entire lifetime

### Manipulating cgroups
- Move a process: echo pid > /cgroup/path/cgroup.procs (as root)
- Set a PID limit: echo 3000 > /cgroup/path/pids.max
- Create a new cgroup: mkdir /sys/fs/cgroup/my-cgroup
  The kernel detects the new directory and creates all interface
  files automatically
- Remove an empty cgroup: rmdir /sys/fs/cgroup/my-cgroup
  Works even though directory contains files. Kernel handles cleanup

### Rules for cgroups
- Processes can only be placed in leaf cgroups (no child cgroups
  beneath them)
- A cgroup cannot use a controller its parent does not also use
- To give a child cgroup access to a controller, write to the
  parent's cgroup.subtree_control file:
  echo +cpu +pids > /sys/fs/cgroup/cgroup.subtree_control
- The root cgroup is an exception: processes can be placed
  directly into it to detach them from systemd management

---

## What I Built / Practiced

```bash
# Find which cgroup the current shell belongs to
cat /proc/self/cgroup

# Navigate to the cgroup in the filesystem
cd /sys/fs/cgroup/user.slice/user-1000.slice/session-2.scope

# List interface files
ls

# Check active controllers
cat cgroup.controllers

# Check current PID count
cat pids.current

# Check memory ceiling
cat memory.max

# Check CPU usage for the cgroup
cat cpu.stat

# Move a process into a cgroup (as root)
echo pid > cgroup.procs

# Set a PID limit
echo 3000 > pids.max

# Create a new cgroup
mkdir /sys/fs/cgroup/my-cgroup

# Enable controllers for child cgroups
echo +cpu +pids > /sys/fs/cgroup/cgroup.subtree_control

# Remove empty cgroup
rmdir /sys/fs/cgroup/my-cgroup
```

---

## Challenges Faced
- Reading day focused on concepts and filesystem navigation
- The v1 vs v2 distinction required careful attention.
  Key insight: v1 has separate hierarchies per controller.
  v2 has one hierarchy where multiple controllers attach to
  one cgroup. A process in v2 is in exactly one place

---

## Key Takeaways
- renice is a polite request. cgroups are a hard rule
- The kernel enforces cgroup limits as constraints, not suggestions
- cgroup v2 is the current standard: one process, one cgroup,
  multiple controllers. v1 is being phased out
- cgroups are accessed entirely through the filesystem at
  /sys/fs/cgroup. Reading and writing files is the interface
- cpu.stat tracks total CPU across all processes ever spawned
  in a cgroup, including those that have already exited
- memory.max showing max means no limit on that cgroup but
  a parent cgroup higher up may still impose one
- Containers use cgroups heavily. Understanding them now makes
  Docker and container concepts easier when they arrive
- systemd manages most cgroups on a running system but cgroups
  exist independently of systemd

---

## Commands Used

```bash
cat /proc/self/cgroup                    # current process cgroup
ls /sys/fs/cgroup                        # cgroup filesystem root
cat /sys/fs/cgroup/cgroup.controllers    # available controllers
cat cgroup.procs                         # PIDs in this cgroup
cat cgroup.threads                       # threads in this cgroup
cat cgroup.controllers                   # active controllers
cat pids.current                         # current thread count
cat pids.max                             # PID limit
cat memory.max                           # memory ceiling
cat memory.current                       # live memory usage
cat cpu.stat                             # CPU usage statistics
echo pid > cgroup.procs                  # move process into cgroup
echo 3000 > pids.max                     # set PID limit
mkdir /sys/fs/cgroup/my-cgroup           # create new cgroup
rmdir /sys/fs/cgroup/my-cgroup           # remove empty cgroup
echo +cpu +pids > cgroup.subtree_control # enable controllers for children
```

---

## Resources
- How Linux Works by Brian Ward, chapter 8
- [Medium: Day 27 write-up](https://medium.com/@charlesedeki093/day-27-putting-processes-in-a-box-ce7d78c42845)

---

## Output
- Day 27 write-up published on Medium
