# Day 04 - Commands That Read, Search, and Tell the Truth

## Objective
Get hands-on with Linux commands used to search, inspect, compare, and sort file content. Understand how the shell reports results honestly and what to do when a tool is not available on a managed server.

---

## What I Learned

### Commands Covered
- `grep` searches a file line by line for a pattern and prints matching lines. Case-sensitive by default
- `grep -i` makes the search case-insensitive
- `grep -v` inverts the match, printing every line that does NOT contain the pattern
- `less` displays file content one screenful at a time. Spacebar moves forward, `b` moves backward, `q` quits
- `sort` puts lines of a file in alphanumeric order and prints to standard output. Does not modify the source file
- `diff` compares two text files and reports the differences. `<` marks lines from the left file, `>` marks lines from the right file
- `head` shows the first 10 lines of a file by default. `-n` specifies the number of lines
- `tail` shows the last 10 lines of a file by default. `-n` specifies the number of lines
- `find` searches the filesystem in real time for files matching given criteria
- `locate` searches a pre-built index (`mlocate.db`) rather than the live filesystem. Much faster than find but only as current as the last index update
- `pwd` prints the current working directory
- `file` identifies the format of a file. Not available on this VM, requires admin installation

### Key Concepts
- grep is case-sensitive by default. A search for "so" will not match "So"
- A result that looks surprising is not always an error. `grep -v so` returning both lines is correct because neither line contains lowercase "so"
- `sort` reads and outputs. It does not write back to the source file unless redirected with `>`
- `locate` vs `find` is the same trade-off as a pivot table vs a raw Excel formula. locate is fast but uses cached data. find is slower but always reflects current reality
- On managed servers you do not control what is installed. Always check with `which` or `type` before assuming a command is available

---

## What I Built / Practiced

Created `sample_file` in nano with two lines of content, then used it as a test subject for every command covered today.

```bash
$ grep see sample_file
So let's see how it goes

$ grep so sample_file

$ grep -i so sample_file
So let's see how it goes

$ grep -v So sample_file
This file will be used to test maybe all the commands I have learnt today

$ grep -v so sample_file
This file will be used to test maybe all the commands I have learnt today
So let's see how it goes

$ sort sample_file
So let's see how it goes
This file will be used to test maybe all the commands I have learnt today

$ cat sample_file
This file will be used to test maybe all the commands I have learnt today

So let's see how it goes

$ sort sample_file > sorted_file
$ cat sorted_file
So let's see how it goes
This file will be used to test maybe all the commands I have learnt today

$ diff sample_file sorted_file
1d0
< This file will be used to test maybe all the commands I have learnt today
3a3
> This file will be used to test maybe all the commands I have learnt today

$ head -n 1 sample_file
This file will be used to test maybe all the commands I have learnt today

$ head -n 1 sorted_file
So let's see how it goes

$ which file
$ type file
-bash: type: file: not found
```

---

## Challenges Faced
- `grep so` returned no output even though "So" visibly appears in the file. Resolved by understanding grep is case-sensitive and adding the `-i` flag
- `grep -v so` returned both lines, which looked wrong at first. Resolved by understanding that neither line contains lowercase "so", so nothing was excluded
- `file` command was not installed on the VM. Requires admin access to install. Used `which` and `type` to confirm it was unavailable

---

## Key Takeaways
- grep is one of the most used commands in Linux. Understanding its flags, especially `-i` and `-v`, makes it dramatically more powerful
- sort does not modify source files. Always redirect output if you want to save it
- A surprising result is not always an error. Read what the command actually did before assuming something went wrong
- locate trades freshness for speed. find trades speed for accuracy. Know which one to reach for depending on the situation
- On real servers, not every tool is available. Check before assuming

---

## Commands Used

```bash
grep pattern file          # search for pattern in file
grep -i pattern file       # case-insensitive search
grep -v pattern file       # show lines that do NOT match
sort file                  # sort lines alphanumerically
sort file > newfile        # sort and save output to new file
diff file1 file2           # compare two files
head -n N file             # show first N lines
tail -n N file             # show last N lines
find . -name "pattern"     # real-time file search
locate filename            # fast index-based file search
which command              # check if a command exists
type command               # another way to check command availability
```

---

## Resources
- How Linux Works by Brian Ward, chapter 2
- [Medium: Day 4 write-up](https://medium.com/@charlesedeki093/day-4-commands-that-read-search-and-tell-the-truth-bcd69c816c2d)

---

## Output
- Created and used sample_file and sorted_file as test subjects
- Demonstrated all four grep flag variations with actual output
- Proved sort does not modify source files using cat before and after
- Compared two files using diff and decoded the output notation
- Day 4 write-up published on Medium
