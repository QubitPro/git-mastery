# Day 02 — Git Aliases

**Goal:** Type less, do more. Set up shortcuts that save you thousands of keystrokes.

---

## Why This Matters

Every day you type `git status`, `git checkout`, `git branch` dozens of times.
Elite devs type `git st`, `git co`, `git br` — same result, half the effort.

---

## Hands-On Exercise

### Exercise 1: Set Up Your Aliases

Open your git config:
```bash
git config --global --edit
```

Add this `[alias]` section (after your `[user]` section):

```ini
[alias]
    # Daily shortcuts
    st = status
    co = checkout
    br = branch
    ci = commit
    sw = switch

    # History visualization
    visual = log --graph --oneline --all --decorate
    last = log -1 HEAD --stat
    today = log --since="1 day ago" --oneline
    week = log --since="1 week ago" --oneline
    mycommits = log --author="Qubit1729" --oneline

    # Quick fixes
    amend = commit --amend --no-edit
    undo = reset --soft HEAD~1

    # Staging helpers
    staged = diff --cached
    unstage = reset HEAD

    # Branch management
    recentbranches = branch -a --sort=-committerdate
    review = log --no-merges --oneline origin/main..HEAD

    # Publish branch to remote
    publish = !git push -u origin $(git branch --show-current)
```

Save and close.

### Exercise 2: Test Every Alias

Navigate to your `Spotify-lyric-tracker` repo and try each one:

```bash
cd ~/path/to/Spotify-lyric-tracker
```

- [ ] `git st` — should show status
- [ ] `git br` — should list branches
- [ ] `git visual` — should show a beautiful tree graph
- [ ] `git last` — should show your latest commit with file stats
- [ ] `git today` — shows commits from today
- [ ] `git week` — shows commits from this week
- [ ] `git mycommits` — shows only YOUR commits
- [ ] `git recentbranches` — branches sorted by recent activity

### Exercise 3: Use Aliases in a Real Workflow

Switch to your practice branch and do a mini workflow using ONLY aliases:

```bash
# Instead of: git checkout practice/semantic-commits
git co practice/semantic-commits

# Instead of: git status
git st

# Make a small change to any file, then:
git add <file>

# Instead of: git commit -m "..."
git ci -m "chore(config): add git aliases to workflow"

# Check your work:
git last

# See the visual tree:
git visual
```

- [ ] Complete the mini workflow above using only aliases
- [ ] You should feel the speed difference already

### Exercise 4: The `amend` and `undo` Power Moves

These two will save you constantly:

**amend** — fix your last commit (add a forgotten file, no message change):
```bash
# Oops, forgot to add a file to last commit
echo "# test" >> somefile.txt
git add somefile.txt
git amend
# Last commit now includes the new file, same message
git last
```

**undo** — undo last commit but KEEP your changes:
```bash
# Made a bad commit? Undo it safely
git undo
# Changes are back in staging, commit is gone
git st
# Re-commit properly
git ci -m "fix(player): correct lyric offset calculation"
```

- [ ] Practice `git amend` — add a forgotten file to your last commit
- [ ] Practice `git undo` — undo a commit, then recommit with a better message

### Exercise 5: The `staged` Trick

Before committing, always review what you're about to commit:

```bash
# Stage some files
git add <file1> <file2>

# Review what's staged (what will be committed)
git staged

# If something shouldn't be there:
git unstage <file>
```

- [ ] Stage 2 files, run `git staged`, unstage one, then commit

---

## Cheat Sheet (Print This)

```
git st          → status
git co <branch> → checkout branch
git br          → list branches
git ci -m "..." → commit with message
git visual      → pretty tree view
git last        → last commit details
git amend       → fix last commit (add files)
git undo        → undo last commit (keep changes)
git staged      → see what's about to be committed
git unstage     → remove from staging
git publish     → push current branch to remote
git review      → see your commits vs main
```

---

## Self-Check

- [ ] I have 15+ aliases in my `~/.gitconfig`
- [ ] `git st` works
- [ ] `git visual` shows a tree graph
- [ ] I used `git amend` successfully
- [ ] I used `git undo` successfully
- [ ] I feel faster already

---

**Done? Move to [Day 03 — Interactive Rebase](day-03-interactive-rebase.md)**
