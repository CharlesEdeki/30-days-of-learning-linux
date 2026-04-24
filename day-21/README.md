# Day 21 - System Logging: How Linux Remembers What Happened

## Objective

Understand how Linux logging works, where logs live, how log messages are structured, how to search and filter the journal effectively, and how log rotation keeps storage under control.

---

## What I Learned

### System Logging

- Most programs do not manage their own logs. They write diagnostic output as messages to the syslog service
- The traditional `syslogd` daemon handled this by waiting for messages and routing them to the appropriate channels
- On modern systems, `journald` (which comes with systemd) does this job
- The system logger is one of the most important parts of the system. When something goes wrong, the logs are the first place to look

### Log Message Structure

Every log message carries:

- Process name
- Process ID
- Timestamp

Two additional classification fields can also be included:

- **Facility** — the general category of service that sent the message (kernel, mail system, printer, etc.)
- **Severity** — how urgent the message is, numbered 0 through 7:

```
0: emerg     4: warning
1: alert     5: notice
2: crit      6: info
3: err       7: debug
```

Facility and severity together make up the **priority** of a message.

### Where Logs Live

- Log files live in `/var/log`
- There may be subdirectories inside containing logs from specific services
- `/var/log/journal` is where journald stores its binary log files
- Running `journalctl` gives all messages in the journal, oldest first, displayed through a pager so the terminal does not flood

### Checking Your Log Setup

1. Check for journald using the `journalctl` command
2. Check for rsyslogd — look for `/etc/rsyslogd.conf` in the process list
3. If rsyslogd is not found, check for syslog-ng and look for `/etc/syslog-ng`

### Log Rotation

- Every log message written to a traditional text logfile goes to the end of the file. Over time this grows until it consumes storage
- The `logrotate` utility solves this by dividing a logfile into numbered chunks
- When logrotate decides to clean up a file like `auth.log`, it:
  1. Removes the oldest file, `auth.log.3`
  2. Renames `auth.log.2` to `auth.log.3`
  3. Renames `auth.log.1` to `auth.log.2`
  4. Renames `auth.log` to `auth.log.1`
- Some distributions compress the rotated files (e.g. `auth.log.2.gz`) or add a date suffix instead
- `journald` does not need rotation — it manages its own storage automatically, deleting old messages based on configured size limits and available space

---

## Searching and Filtering Logs

| Filter | Command | Notes |
|--------|---------|-------|
| All messages | `journalctl` | Oldest first, paged |
| Live stream | `journalctl -f` | Like `tail -f` for the journal |
| By process ID | `journalctl _PID=8792` | Can combine multiple criteria |
| By time (since) | `journalctl -S -4h` | Past 4 hours |
| By time (specific) | `journalctl -S '2020-01-14 14:30:00'` | Exact timestamp |
| By time (until) | `journalctl -U 06:00:00` | Up to a specific time |
| By severity | `journalctl -p 3` | Level 3 and below (most critical) |
| By severity range | `journalctl -p 2..3` | Specific range |
| Current boot | `journalctl -b` | Messages from this boot only |
| Previous boot | `journalctl -b -1` | Messages from last boot |
| By unit | `journalctl -u cron.service` | Filter to specific service |
| Reverse order | `journalctl -r` | Newest first |
| List boots | `journalctl --list-boots` | All boot IDs |

---

## What I Built / Practiced

```bash
# View all journal messages
journalctl

# Stream logs live
journalctl -f

# Filter to last 4 hours
journalctl -S -4h

# Filter to a specific date and time
journalctl -S '2026-04-21 09:00:00'

# View only critical messages (severity 0-3)
journalctl -p 3

# View messages from current boot
journalctl -b

# View messages from previous boot
journalctl -b -1

# Check if machine shut down cleanly last cycle
journalctl -r -b -1
```

---

## Challenges Faced

- `journalctl -g` requires a build of journalctl with a specific library. Some distributions do not include a version that supports `-g` — this flag may silently fail or error depending on the system
- When searching by text, only matching messages are returned with no surrounding context. The workaround is to note the timestamp of a match and re-run with `journalctl -S` just before that time to see surrounding activity
- `tail -f` does not work with journald because it does not use plaintext files. Use `journalctl -f` instead

---

## Key Takeaways

- The journal is the first place to look when something breaks. Knowing how to filter it by time, severity, unit, and boot makes the difference between finding the problem quickly and scrolling through noise
- For data engineering, `journalctl -S` with a timestamp is the most practical filter. When a pipeline fails at 3am, narrowing to that window immediately is more useful than reading everything
- Log rotation exists because logs grow forever if left alone. Understanding the rotation sequence explains why `auth.log.1` contains yesterday's entries and `auth.log` contains today's
- `journald` handles its own storage. No logrotate configuration needed for journal files

---

## Commands Used

```bash
journalctl                          # all journal messages
journalctl -f                       # live log stream
journalctl -b                       # current boot only
journalctl -b -1                    # previous boot
journalctl -r -b -1                 # reverse previous boot (check clean shutdown)
journalctl --list-boots             # list all boot IDs
journalctl -p 3                     # severity 3 and below
journalctl -p 2..3                  # severity range
journalctl -S -4h                   # last 4 hours
journalctl -S '2026-04-21 14:30:00' # since specific timestamp
journalctl -U '2026-04-21 15:00:00' # until specific timestamp
journalctl -S 06:00:00              # since 6am today
journalctl _PID=8792                # messages from specific process
journalctl -u cron.service          # messages from specific unit
journalctl -F _SYSTEMD_UNIT         # list all unit names in journal
journalctl -g 'kernel.*memory'      # regex search
```

---

## Resources

- *How Linux Works* by Brian Ward, Chapter 7
- Medium: [Day 21 write-up](https://medium.com/@charlesedeki093/day-21-how-linux-remembers-what-happened-65f36ed86f84)

---

## Output

- Day 21 write-up published on Medium
