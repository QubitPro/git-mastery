# Day 06 — Reflog (Your Safety Net)

**Goal:** Learn to recover from ANY git disaster. Never lose work again.

---

## Why This Matters

You will eventually:
- Accidentally delete a branch
- Rebase and lose commits
- Reset and lose changes
- Think your work is gone forever

**It's NOT gone.** Git reflog records EVERYTHING for 30 days.

---

## The Commands

```bash
git reflog                      # Show all actions (HEAD movements)
git reflog show <branch>        # Show reflog for specific branch
git reset --hard <hash>         # Jump back to any state
git checkout <hash>             # View a past state (detached HEAD)
git branch recover-branch <hash>  # Create branch from old state
```

---

## Hands-On Exercise

### Exercise 1: See Your Reflog

```bash
cd ~/path/to/Spotify-lyric-tracker
git reflog
```

You'll see something like:
```
abc1234 (HEAD -> main) HEAD@{0}: checkout: moving from practice/stash-exercise to main
def5678 HEAD@{1}: commit: chore(config): add git aliases to workflow
ghi9012 HEAD@{2}: commit: fix(readme): correct installation command typo
...
```

Every action you've EVER taken is here.

- [ ] You can see your reflog
- [ ] You recognize actions from previous days

### Exercise 2: Recover a Deleted Branch

```bash
# Create a branch with important work
git checkout -b important-feature
echo "super important code" >> important.txt
git add . && git commit -m "feat: critically important feature"
echo "more important stuff" >> important.txt
git add . && git commit -m "feat: add more critical code"

# Note the last commit hash
git log --oneline -1
```

Now "accidentally" delete it:
```bash
git checkout main
git branch -D important-feature
# Branch is GONE!
git br
# Not in the list!
```

- [ ] Branch is deleted and not visible

Now RECOVER it:
```bash
git reflog
# Find the line: "commit: feat: add more critical code"
# Note its hash (e.g., abc1234)

git branch important-feature <that-hash>
git checkout important-feature
git log --oneline -3
# ALL YOUR WORK IS BACK!
```

- [ ] Branch recovered with ALL commits intact

### Exercise 3: Recover From a Bad Rebase

```bash
git checkout main
git checkout -b practice/bad-rebase

# Make 3 important commits
echo "commit 1" >> rebase-victim.txt && git add . && git commit -m "feat: important work 1"
echo "commit 2" >> rebase-victim.txt && git add . && git commit -m "feat: important work 2"
echo "commit 3" >> rebase-victim.txt && git add . && git commit -m "feat: important work 3"

git log --oneline -3
# All 3 commits are here
```

Now simulate a bad rebase (squash everything into 1):
```bash
git rebase -i HEAD~3
# Change all to "squash" except first, save
# Write message: "squashed everything"
```

Oops, you didn't want that. Recover:
```bash
git reflog
# Find: "rebase: start" — the line BEFORE it is your safe point
# Example: HEAD@{4}: commit: feat: important work 3

git reset --hard <hash-before-rebase>
git log --oneline -3
# All 3 original commits are back!
```

- [ ] You rebased (lost commits), then recovered them with reflog

### Exercise 4: Recover From a Hard Reset

```bash
git checkout -b practice/bad-reset

echo "very important" >> critical-file.txt
git add . && git commit -m "feat: add critical file"
echo "also important" >> critical-file2.txt
git add . && git commit -m "feat: add second critical file"

# "Accidentally" reset hard
git reset --hard HEAD~2
# Both commits are gone!
git log --oneline -3
```

Recover:
```bash
git reflog
# Find: "commit: feat: add second critical file"
git reset --hard <that-hash>
# BOTH commits are back
```

- [ ] Recovered from a `git reset --hard`

### Exercise 5: Browse History Without Changing Anything

```bash
git reflog

# Pick any old state hash
git checkout <old-hash>
# WARNING: "detached HEAD" — you're just VIEWING, not changing

# Look around
git log --oneline -5
ls

# Go back to safety
git checkout main
```

- [ ] You explored an old state without breaking anything

---

## Reflog Survival Guide

| Disaster | Recovery |
|----------|----------|
| Deleted branch | `git reflog` → find commit → `git branch <name> <hash>` |
| Bad rebase | `git reflog` → find pre-rebase state → `git reset --hard <hash>` |
| Bad reset | `git reflog` → find lost commit → `git reset --hard <hash>` |
| Lost commits | `git reflog` → find commit → `git cherry-pick <hash>` |
| Bad merge | `git reflog` → find pre-merge state → `git reset --hard <hash>` |

**Golden rule:** If it was committed, reflog can find it for 30 days.

---

## Self-Check

- [ ] I can read and understand the reflog output
- [ ] I recovered a deleted branch
- [ ] I recovered from a bad rebase
- [ ] I recovered from a hard reset
- [ ] I feel confident that I can't permanently lose committed work
- [ ] I know reflog entries expire after 30 days

---

**Done? Move to [Day 07 — Week 1 Practice Challenge](day-07-week1-practice.md)**
