# Day 24 - User Access and Authentication in Linux

## Objective
Understand how Linux handles user identity at the kernel level,
how user switching works through setuid and system calls, the
difference between effective and real user IDs, and how PAM makes
authentication modular and extensible.

---

## What I Learned

### Three Layers of User Security
- Identification: who the user is. Handled by numeric user IDs
  at the kernel level. Names are a user space convenience
- Authentication: proving identity. Almost entirely in user space.
  The kernel knows nothing about usernames or passwords
- Authorization: what authenticated users are allowed to do
- The kernel only knows numeric user IDs, setuid rules, and file
  permissions. Everything else is user space reading /etc/passwd
  and /etc/shadow

### User Switching
- sudo and su work by changing the numeric user ID at kernel level
- Two mechanisms: running a setuid executable or calling setuid()
- A process running as root can call setuid() to become any user
- A process not running as root cannot call setuid() in most cases
- sudo is a setuid root executable. It starts as root, then calls
  setuid() to become the target user
- User switching at the kernel level is just changing a number

### Effective UID and Real UID
- euid (effective user ID): defines access rights and file permissions
- ruid (real user ID): records who originally started the process
- Normally identical. When a setuid program runs, euid becomes
  the program owner's ID while ruid stays as the original user
- euid is the actor. ruid is the owner
- ruid determines who can kill the process and send it signals
- saved user ID: allows a process to switch euid between ruid
  and saved ID during execution
- filesystem user ID: defines the user accessing the filesystem

### PAM - Pluggable Authentication Modules
- Proposed by Sun Microsystems in 1995
- Separates authentication mechanism from the application
  requesting authentication
- Uses dynamically loadable shared library modules
- Each module performs a specific task
- pam_unix.so checks passwords. pam_deny.so always fails.
  pam_rootok.so succeeds if root is authenticating
- Configuration lives in /etc/pam.d, one file per service
- Important distinction: function is the high-level goal the
  application requests. Action is the specific step PAM takes.
  Always read function and module together as a pair

### PAM Function Types
- auth: authenticate a user, verify identity
- account: check authorisation regardless of identity
- session: perform actions for current session
- password: change credentials
- Same module can serve multiple function types with different
  behaviour. pam_unix.so checks passwords for auth but sets
  passwords for the password function

### PAM Control Arguments
- sufficient: success ends the stack with success. Failure continues
- requisite: success continues. Failure ends the stack with failure
- required: success continues. Failure continues but always reports
  failure at the end regardless of later results

### PAM Stack Example for chsh
```
auth    sufficient    pam_rootok.so
auth    requisite     pam_shells.so
auth    sufficient    pam_unix.so
auth    required      pam_deny.so
```
- pam_rootok.so: root succeeds immediately, stack ends
- pam_shells.so: shell must be in /etc/shells, fails immediately
  if not (requisite)
- pam_unix.so: checks password, succeeds if correct (sufficient)
- pam_deny.so: always fails, safety net to prevent accidental
  success by falling through

### PAM and Passwords
- /etc/login.defs is the original shadow password suite config
  predating PAM. Largely ignored on modern PAM systems
- grep password.*unix /etc/pam.d/* finds the password encryption
  configuration
- sha512 argument tells PAM which algorithm to use when setting
  a new password
- obscure argument checks new password is not too similar to old
- When verifying a password, pam_unix.so asks libcrypt to try
  algorithms until something matches. No explicit config needed

### Practical PAM Tips
- man -k pam_ lists available PAM modules
- locate pam_unix.so finds where modules are installed
- Read comments in /etc/pam.d files before editing. Many are
  auto-generated and will be overwritten on upgrade
- /etc/pam.d/other is the default config for applications without
  their own PAM config file. Default is usually deny everything
- /etc/security contains per-user restriction config files that
  some modules access

---

## What I Built / Practiced
```bash
# Find available PAM modules
man -k pam_

# Find module location
locate pam_unix.so

# Find password encryption configuration
grep password.*unix /etc/pam.d/*

# Check PAM config for a service
cat /etc/pam.d/sudo
cat /etc/pam.d/sshd

# Check effective and real UIDs
cat /proc/self/status | grep -i uid
```

---

## Challenges Faced
- The euid and ruid distinction required careful re-reading.
  Key insight: euid determines what the process can access.
  ruid determines who owns and can control the process
- The function versus action distinction in PAM: function is
  what the application asks for. Action is what PAM does to
  achieve it. Keeping these separate makes config readable

---

## Key Takeaways
- User switching at the kernel level is just changing a number.
  Everything else is user space convention
- euid is the actor. ruid is the owner. A user can still kill
  a setuid process they started because their ID is in the ruid
- PAM decouples authentication from applications completely.
  sudo asking for a password is PAM making that decision
- pam_deny.so at the bottom of a stack is a safety net pattern.
  It prevents accidental success by falling through
- The three control arguments give the stack its logic:
  sufficient stops on success, requisite stops on failure,
  required continues but remembers failure
- The identification, authentication, authorisation pattern
  maps directly to IAM in cloud platforms like AWS and GCP

---

## Commands Used
```bash
man -k pam_                          # list PAM modules
locate pam_unix.so                   # find module location
grep password.*unix /etc/pam.d/*     # find encryption config
cat /etc/pam.d/sudo                  # view PAM config for sudo
cat /etc/pam.d/other                 # default PAM config
cat /proc/self/status | grep -i uid  # check process UIDs
```

---

## Resources
- How Linux Works by Brian Ward, chapter 7.10
- [Medium: Day 24 write-up](https://medium.com/@charlesedeki093/day-24-who-are-you-really-user-access-and-authentication-in-linux-d1124de2fa4c)

---

## Output
- Day 24 write-up published on Medium
