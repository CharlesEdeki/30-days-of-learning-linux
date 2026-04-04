# Day 03 - The Shell, Core Commands, and How Linux Really Stores Files

## Objective
Get hands-on with foundational Linux commands, understand how the shell processes input before commands ever run, and dig into what actually happens at the filesystem level when files are moved, copied, and deleted.

---

## What I Learned

### The Shell
- The shell is the interpreter between the user and the kernel. It takes typed commands and translates them into system instructions
- The original Bourne shell (`/bin/sh`) evolved into Bash (Bourne Again Shell), the default shell on most Linux and Unix systems today
- The shell processes arguments before passing them to commands. Globbing, variable expansion, and quoting all happen at the shell level, not inside the command itself

### Commands Covered
- `cat` concatenates and prints file contents to standard output. With no filename argument, it reads from standard input (keyboard). Terminate with `Ctrl+D`
- `ls` lists directory contents. `ls -l` shows detailed long format. `ls -F` appends type indicators (slash for directories, asterisk for executables)
- `cp` copies files. Silently overwrites the destination if it already exists. When the last argument is a directory, all preceding files are copied into it
- `mv` moves or renames files. If the destination file already exists, it is silently overwritten with no warning
- `touch` creates a new empty file. If the file already exists, it updates the timestamp without changing content
- `rm` removes a file name from the directory. The data remains on disk until overwritten by something else
- `echo` prints arguments to standard output. Used to create file content quickly and to observe shell behaviour with wildcards
- `mkdir` creates a new directory
- `rmdir` deletes an empty directory. `rm -r` deletes a directory and all its contents recursively
- `cd` changes the working directory. No argument returns to the home directory

### Paths
- Absolute paths start with `/` and are measured from the filesystem root
- Relative paths start with `./` and are measured from the current working directory

### Shell Globbing (Wildcards)
- `*` matches any sequence of characters. The shell expands it before the command runs
- `?` matches exactly one arbitrary character. `b?at` matches `beat`, `boat`, `brat`
- Single quotes prevent glob expansion. `echo '*'` prints a literal asterisk. `echo *` prints all files in the current directory
- `echo *` and `ls` are not the same. `echo *` is shell glob expansion passed to echo. `ls` reads the directory directly and supports metadata, sorting, and flags

---

## What I Built / Practiced

```bash
echo hello > file1
touch file2
cp file1 file2
mkdir test
cp file1 file2 test
cd test
mv file1 file2
```

Observed that `mv file1 file2` inside `test` resulted in only `file2` remaining. Investigated why.

---

## The Deep Dive: What Actually Happens When mv Overwrites a File

Running `mv file1 file2` when `file2` already exists produces one file, not two. This raised the question of what happens to the overwritten file at the filesystem level.

Linux stores files in two separate components:

**Inode** — the data structure holding actual file content, metadata, permissions, timestamps, and disk block locations. This is the real file.

**Directory entry** — the human-readable name (`file1`, `file2`) that acts as a pointer to an inode. The name is not the file. It is a label.

When `mv file1 file2` runs against an existing `file2`:
1. Linux unlinks `file2` from its inode. Since nothing else points to that inode, it is marked free and its storage becomes reclaimable
2. Linux reassigns the name `file2` to point at `file1`'s inode
3. The name `file1` is removed from the directory

The old `file2` data is not immediately wiped. It sits on disk, invisible and unreachable, until new data overwrites those blocks. This is why file recovery tools can sometimes restore deleted files.

This also explains why `rm` stands for remove, not delete. It removes a name. The data disappears only when its inode reference count reaches zero and the blocks are eventually claimed.

---

## Challenges Faced
- `mv file1 file2` produced an unexpected result inside the test directory. Expected two files, found one. Resolved by studying inode and directory entry structure

---

## Key Takeaways
- Linux executes instructions without confirmation. `cp`, `mv`, and `rm` will overwrite and delete without asking. Always verify before running
- `rm` removes a name from a directory, it does not immediately wipe data from disk
- Globbing happens in the shell before any command receives arguments. The command never sees the wildcard, only the expanded result
- Single quotes suppress all shell interpretation. Use them when you want a literal character passed to a command
- Never use `rm -r` with `*` without double-checking the current directory and what will be affected

---

## Commands Used

```bash
echo hello > file1       # write "hello" into file1
touch file2              # create empty file2
cp file1 file2           # copy file1 content into file2 (overwrites)
mkdir test               # create test directory
cp file1 file2 test      # copy both files into test/
cd test                  # navigate into test
mv file1 file2           # rename file1 to file2 (overwrites existing file2)
ls -l                    # long format directory listing
ls -F                    # listing with file type indicators
echo *                   # shell expands glob, prints all filenames
echo '*'                 # single quotes suppress expansion, prints literal *
rm -r test               # recursively delete test directory and contents
```

---

## Resources
- How Linux Works by Brian Ward, chapter 2.1 - 2.4
- [Medium: Day 3 write-up](https://medium.com/@charlesedeki093/day-3-the-shell-the-commands-and-the-file-that-disappeared-ffe2a7a86ef3)

---

## Output
- Hands-on practice with core file manipulation commands completed
- Investigated inode and directory entry structure to explain mv overwrite behaviour
- Day 3 write-up published on Medium
