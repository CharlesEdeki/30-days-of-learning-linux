# Day 02 - How Linux Actually Works: The Kernel, Memory, and Processes

## Objective
Build a foundational understanding of how Linux works internally, specifically how the kernel manages main memory, user processes, and system resources, and what the boundaries between kernel space and user space actually mean.

---

## What I Learned
- Main memory is a collection of bits, and everything running on a Linux system, including the kernel and all processes, lives in memory as arrangements of those bits
- The kernel sits between hardware and user processes, managing four core areas: process scheduling, memory management, device drivers, and system calls
- Processes on a single-core CPU do not actually run simultaneously. The kernel switches between them in tiny time slices, so fast that humans perceive it as multitasking
- The handoff between processes is called a context switch. The kernel saves the current process state, picks the next process, and hands over control
- Virtual memory gives each process the illusion that it has the entire machine to itself. The kernel handles the translation to actual physical memory via the MMU
- The superuser (root) cannot access kernel space directly. The kernel maintains a private memory area no user process can touch
- The kernel enforces permissions, it does not make judgments. If root has permission to run a command, the kernel executes it regardless of consequences
- Users are identified by numeric user IDs internally. Usernames are a human-readable layer on top

---

## What I Built / Practiced
- Read and studied chapters 1.2 through 1.5 of How Linux Works by Brian Ward
- Mapped the kernel's four management responsibilities to real system behaviours
- Traced the lifecycle of a context switch through all seven kernel steps
- Worked through the question of why the kernel executes root commands even when they are destructive

---

## Challenges Faced
- No commands run today, this was a reading and comprehension day
- The concept of virtual memory required re-reading. The key insight: the process sees a fake address space, the MMU translates to real physical addresses, the kernel manages the map

---

## Key Takeaways
- The kernel is not intelligent, it is precise. It executes instructions without judgment and enforces rules without understanding why they matter
- Root's power is not unlimited, it cannot touch kernel space. But within user space, the kernel will carry out any root instruction faithfully, including destructive ones
- This is why the principle of least privilege exists: never run as root unless necessary, because the system will do exactly what you tell it, no questions asked
- Everything that feels simultaneous on Linux is actually the kernel orchestrating incredibly fast switches between processes

---

## The Question I Asked
If the kernel protects itself from the superuser, why does it still carry out root instructions even when those instructions are destructive?

Answer: the kernel enforces permissions, not wisdom. Root has permission to do almost anything in user space. The kernel's job is to check permissions and execute, not to evaluate intent. Its protections are designed to prevent accidental interference from unprivileged users, not to save root from deliberate decisions.

---

## Commands Used
None today. Day 3 returns to the terminal.

---

## Resources
- How Linux Works by Brian Ward, chapters 1
- [Medium: Day 2 write-up](https://medium.com/@charlesedeki093/day-2-how-linux-actually-works-the-kernel-memory-and-a-question-that-kept-me-up-388fa911fc74)

---

## Output
- Reading and comprehension session completed
- Day 2 write-up published on Medium
- Key concepts mapped and documented in this README

