# Day 23 - Scheduling Deeper: Cron Syntax, Timer Units, and One-Time Jobs

## Objective
Go deeper into scheduling on Linux, understand the full cron syntax,
compare cron to systemd timer units, learn one-time scheduling with
at, and understand how /etc organises system configuration.

---

## What I Learned

### The /etc Directory
- Almost all Linux system configuration lives in /etc
- The traditional problem was flat files from every program cluttering
  the directory. Modern approach uses subdirectories per service
- Customisations in subdirectory files survive package upgrades.
  Customisations in flat files owned by packages get overwritten
- What belongs in /etc: machine-specific configuration like user info
  and network details. General application defaults belong elsewhere

### Full Cron Syntax
- Star means every value for that field
- Specific number means that exact value only
- Comma separates multiple values in one field
- Hyphen defines a range of values
- Slash with number defines a step interval

```
15 09 * * *       every day at 9:15am
15 09 14 * *      only on the 14th of each month
15 09 5,14 * *    on the 5th and 14th
00 06 * * 1-5     every weekday at 6am
*/15 * * * *      every 15 minutes
```

- Days of week: 0 through 7 where both 0 and 7 are Sunday
- Each user has their own crontab in /var/spool/cron/crontabs
- Never write to that directory directly. Use the crontab command

### Cron vs systemd Timer Units
Cron advantages:
- Simpler configuration, one line in a crontab file
- No service unit file needed
- Compatible with almost everything
- Easier for users to install their own tasks

Timer unit advantages:
- Every execution tracked in the journal via journalctl
- Processes tracked in cgroups
- Can depend on other systemd units
- More activation options: calendar time, elapsed since boot,
  elapsed since last run
- Better for complex pipelines that need logging and dependency management

### One-Time Jobs with at
- at schedules a command to run once at a specific future time
- atq lists scheduled jobs
- atrm removes a scheduled job by ID
- Useful for one-off maintenance tasks without setting up and
  removing a cron job

---

## What I Built / Practiced

```bash
# Cron syntax examples in crontab
crontab -e

# Every 15 minutes
*/15 * * * * /path/to/script.sh

# Weekdays at 6am
00 06 * * 1-5 /path/to/script.sh

# One-time job with at
at 22:30
at> echo "one time job" >> /tmp/attest.log

# Check scheduled jobs
atq

# Remove a job
atrm 1
```

---

## Challenges Faced
- Reading day focused on theory and syntax. Cron practicals already
  done on Day 22

---

## Key Takeaways
- Cron is simpler. Timer units give more visibility. The choice
  depends on how much needs to go wrong before it matters
- The /etc subdirectory pattern exists specifically to survive
  package upgrades. Always check whether a service uses a .d
  subdirectory before editing its config file directly
- at fills a gap that cron does not cover cleanly: running
  something once at a specific future time without the overhead
  of a recurring schedule
- For production data pipelines, timer units are worth the extra
  setup cost because silent failures at 3am are worse than
  slightly more complex configuration

---

## Commands Used

```bash
crontab -e               # edit crontab
crontab -l               # list cron jobs
crontab -r               # remove all cron jobs
at 22:30                 # schedule one-time job at 10:30pm
at 22:30 30.07.26        # schedule for specific future date
atq                      # list scheduled at jobs
atrm jobnumber           # remove scheduled at job
systemctl list-timers    # list active systemd timer units
journalctl -u name.timer # view timer unit execution history
```

---

## Resources
- How Linux Works by Brian Ward, chapter 7
- [Medium: Day 23 write-up](https://medium.com/@charlesedeki093/day-23-scheduling-deeper-cron-syntax-timer-units-and-one-time-jobs-544ad2d244f3)

---

## Output
- Day 23 write-up published on Medium
