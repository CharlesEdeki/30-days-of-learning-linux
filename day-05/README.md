# Day 05 - More about the Linux Shell

## Objective

The goal was to learn more about the Linux Shell

---

## What I Learned

- Environment variables can be accessed by child processes like running programs or scripts.
- Shell variables are limited to where they are defined, and are not automatically accessible by other child processes unless explicitly made so.
- Shell outputs can be sent as an input to a file using `>` which will either create a new file if the file doesn't exist or erase the existing one and fill in the new content.
- Using the `>>` symbol helps to apppend new inputs into an existing file instead of erasing it.
- Standard Error (stderr) is an additional output for dianostics and debugging.

---

## What I Built / Practiced

- I formulated a non existing folder and tried to perform an operation with it to see an example of the standard error on the standard output.
- After that I went ahead to redirect the standard error into a file instead of having it in the standard output.

---

## Challenges Faced

- Took me a while to understand the environment and shell variables, but researching helped me.

---

## Key Takeaways

- Understanding this part of the shell is important for debugging and logging
- 

---

## Resources

- How Linux Works by Brian Ward, chapter 2
- [Medium Day 5 Article](https://medium.com/@charlesedeki093/day-5-more-about-the-linux-shell-02b67329eae4)

---

## Output

- updated my README
- redirected an error into a file to log it.
