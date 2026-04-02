# Day 01: Linux Environment Setup & First Commands

## Objective
Set up a working Linux environment on a virtual machine and establish a public GitHub workflow to track and share 30 days of progress.

---

## What I Learned
- A VM (virtual machine) is an isolated computer running inside your physical machine - safe to experiment on without risk to your main system
- `mkdir` creates a new directory; `cd` navigates into it - the two commands that start every project
- `git clone` copies a remote repository to your local machine, including its full history and remote configuration
- `git remote set-url origin <url>` redirects where your local repo pushes to - essential when cloning someone else's repo and wanting to push to your own
- `git push origin --all` pushes all local branches to the remote repository
- Linux is explicit: it does exactly what you tell it, nothing more, nothing less

---

## What I Built / Practiced
- Provisioned and logged into an Ubuntu virtual machine — the environment for the entire 30-day challenge
- Created a working directory using `mkdir` and navigated into it with `cd`
- Cloned a GitHub repository into the working directory using `git clone`
- Reconfigured the git remote to point to a personal repository using `git remote set-url`
- Pushed the initial setup to a personal GitHub repo using `git push origin --all`

---

## Challenges Faced
- After cloning, the remote `origin` pointed to the source repository, not my own, pushing would have gone to the wrong place
- Solved by running `git remote set-url origin <my-repo-url>` inside the cloned directory, then verifying with `git remote -v` before pushing
- Also had issues authenticating my github profile, but after reading up I realized I needed to create a classic token and use it as my password for git authentication.

---

## Key Takeaways
- Linux doesn't guess what you want. You tell it exactly what to do, and it does exactly that.
- Setting up your environment and version control on Day 1 is not optional, it's the foundation everything else builds on
- Four commands (`mkdir`, `cd`, `git clone`, `git remote set-url`) were enough to go from nothing to a fully connected, publicly tracked workspace
- Public accountability from Day 1 changes how seriously you take the work

---

## Commands Used

```bash
# Create a working directory
mkdir <directory-name>

# Navigate into it
cd <directory-name>

# Clone a GitHub repository
git clone "https://github.com/repo-url.git"

# Move into the cloned directory
cd <cloned-directory-name>

# Point git remote to your own repo
git remote set-url origin https://github.com/CharlesEdeki/30-days-of-learning-linux.git

# Verify the remote was updated
git remote -v

# Push all branches to your repo
git push origin --all
```

---

## Resources
- [Najeeb Sulaiman — Linux & Bash Scripting Guide](https://github.com/Najeeb-Sulaiman/linux-and-bash-scripting-guide)
- [Medium: Day 1 write-up](https://medium.com/@charlesedeki093/day-1-learning-to-navigate-a-new-terrain-e917ffede662)

---

## Output
- Ubuntu VM provisioned and running
- Working directory created inside the VM
- Repository cloned and remote reconfigured to [CharlesEdeki/30-days-of-learning-linux](https://github.com/CharlesEdeki/30-days-of-learning-linux)
- Initial setup pushed to personal GitHub repo
- Day 1 write-up published on Medium
