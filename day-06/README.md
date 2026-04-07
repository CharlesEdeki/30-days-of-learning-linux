# Day 06 - Listing and Manipulating Processes

## Objective

Get a deeper understanding of the Linux shell.

---

## What I Learned

- `ps` gives the list of current running processes
- `ps $$` provides detail of the shell process
- `kill pid` terminates the process of the specified pid
- `kill -STOP pid` pauses the process of the specified pid
- `kill -CONT pid` resumes the process of tthe specified pid
- `kill -KILL pid` bypasses the process clean up and brutally terminates the process

---

## What I Built / Practiced

- used the `ps` command to check the running processes.
- used `ps $$` and `ps $PPID` to see my shell process and the process that spawned my shell
- used the `top` command to have a live version of the `ps` command

---

## Challenges Faced

- articulating what I have learnt was a challenge but I eventually did it.
- writing this README file is also a big one, but I am glad I am learning.

---

## Key Takeaways

- The commands I learnt about seem to be maintenance level commands or a kind of systems admin level commands
- These commands give the user the privlege to monitor and manipulate their processes. It is like an advanced level Windows task manager

---

## Resources

- How Linux Works by Brian Ward, chapter 2
- [Medium: Day 6 Article](https://medium.com/@charlesedeki093/day-5-listing-and-manipulating-process-26f8c10706dc)

---

## Output

- Day 6 learning posted on Medium
- used the above commands to monitor current processes and parent processes.
