# Day 22 - Users, Time, and Teaching the System to Work While You Sleep

## Objective
Understand how Linux manages users and groups at the system level,
how timekeeping works under the hood, and how to schedule recurring
tasks using cron.

---

## What I Learned

### Users and the /etc/passwd File
- Linux identifies users by numbers at the kernel level. Usernames
  exist only in user space as a convenience layer on top of those numbers
- /etc/passwd is a plain text file readable by any user on the system.
  It maps usernames to user IDs and stores home directory and shell info
- The password field shows x on modern systems, meaning the actual
  encrypted password is stored in /etc/shadow which regular users
  cannot read
- An asterisk in the password field means the account is permanently
  locked and cannot be logged into
- A blank password field means no password is required. This should
  never exist on any system
- UIDs 0 through 999 are reserved for system and service accounts.
  The first real human user gets UID 1000
- The passwd file is like a local DNS for users. Names go in, numbers
  come out. The kernel only ever sees the numbers

### Special and Pseudo Users
- Root always has UID 0 and GID 0. The kernel only recognises this
  one special ID. All others are user space conventions
- Pseudo users like daemon and nobody cannot log in but the system
  can run processes under their IDs for security reasons
- nobody is an underprivileged account used by processes that should
  not be able to write anything on the system

### The Shadow File
- /etc/shadow stores actual encrypted passwords and password expiration
  information
- Regular users have no read permission for shadow
- PAM (Pluggable Authentication Modules) handles most of the actual
  authentication work underneath the login process

### Groups
- Groups allow file permissions to be shared among specific users
- /etc/group defines groups with four fields: name, password, GID,
  and additional members
- whoami returns only the current username
- groups lists all group names the current user belongs to
- id gives everything at once: UID, primary GID, and all group
  memberships with both names and numbers. Use this when debugging
  permission problems

### Login Process
- getty attaches to a terminal and displays the login prompt
- When a username is entered, getty hands off to the login program
  which handles password verification through PAM
- If the password matches, login replaces itself with the user's shell

### Timekeeping
- The kernel maintains a system clock in memory consulted by date
- Hardware has a battery-backed real-time clock that keeps time when
  powered off. The kernel sets its clock from this at boot
- date returns human-readable time. date +%s returns Unix time,
  seconds since January 1 1970 midnight UTC
- timesyncd comes with systemd and synchronises the clock against
  a remote time server automatically

### Cron
- Cron runs programs on a repeating schedule. A program running
  through cron is called a cron job
- Crontab entries have five time fields followed by the command:
  minute, hour, day of month, month, day of week
- A star in any field means every value for that field
- crontab -e opens the crontab file for editing
- crontab -l lists all current cron jobs
- Each user has their own crontab stored in /var/spool/cron/crontabs

---

## What I Built / Practiced

```bash
# Explored user files
cat /etc/passwd
cat /etc/group | grep ceo
sudo cat /etc/shadow | head -5
id
whoami
groups

# My user entry
ceo:x:1000:1000:,,,:/home/ceo:/bin/bash

# Created and ran a cron job
crontab -e
# Added: * * * * * echo "cron is alive - $(date)" >> /tmp/crontest.log
# Left running overnight at 5-minute intervals

# Checked the output next morning
cat /tmp/crontest.log
# Output showed entries every 5 minutes going back hours

# Removed the job
crontab -e   # deleted the line
crontab -l   # confirmed removal
```

---

## Challenges Faced
- watch command was not available on the VM. Used repeated manual
  cat /tmp/crontest.log calls to observe entries appearing instead

---

## Key Takeaways
- The passwd file is readable by anyone. Security comes from
  separating identity records (passwd) from credentials (shadow),
  not from hiding the identity file
- The kernel only understands numbers. The passwd file is the
  translation layer between human-readable names and kernel IDs
- Cron is a contract with the system. Write the schedule, write the
  command, and the system honours it indefinitely
- id is the command to use when debugging permission problems because
  it shows both names and the numbers the kernel actually uses
- The difference between reading about cron and watching a log file
  fill up overnight while you sleep is the difference between
  knowledge and understanding

---

## Commands Used

```bash
cat /etc/passwd              # view all user accounts
cat /etc/group               # view all groups
sudo cat /etc/shadow         # view encrypted passwords (root only)
id                           # full user and group info with numbers
whoami                       # current username only
groups                       # current group memberships by name
date                         # human readable current time
date +%s                     # unix time in seconds
timedatectl                  # time and timezone status
crontab -e                   # edit crontab file
crontab -l                   # list current cron jobs
crontab -r                   # remove all cron jobs
```

---

## Resources
- How Linux Works by Brian Ward, chapter 7
- [Medium: Day 22 write-up](https://medium.com/@charlesedeki093/day-22-who-the-system-thinks-you-are-and-teaching-it-to-work-while-you-sleep-c6beb3d5e2c0)

---

## Output
- Explored /etc/passwd and decoded own user entry
- Confirmed group memberships including sudo and adm
- Created cron job that ran overnight producing timestamped log entries
- Committed crontest_file.txt to GitHub showing cron output
- Day 22 write-up published on Medium
