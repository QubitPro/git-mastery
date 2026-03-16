# Day 12 — Git Bisect + Worktree

**Time:** 30 min

---

## Part A: Bisect — Find Bugs in Seconds (15 min)

Binary search through commits. Finds the bad commit in ~7 steps out of 100.

### 1. Create a repo with a hidden bug

```bash
mkdir ~/bisect-practice && cd ~/bisect-practice && git init
```

```bash
# 5 good commits
for i in 1 2 3 4 5; do
  echo "feature $i" >> app.txt
  git add . && git commit -m "feat: add feature $i"
done

# Commit 6 = THE BUG
echo "BUG_INTRODUCED" >> app.txt
git add . && git commit -m "refactor: restructure data layer"

# 4 more commits (bug still there)
for i in 7 8 9 10; do
  echo "feature $i" >> app.txt
  git add . && git commit -m "feat: add feature $i"
done
```

### 2. Find it manually (bisect)

```bash
git bisect start
git bisect bad                          # HEAD is broken
git bisect good <first-commit-hash>     # commit 1 was fine

# Git checks out middle commit. Test it:
grep "BUG_INTRODUCED" app.txt
# Found? → git bisect bad
# Not found? → git bisect good

# Repeat until git says: "abc1234 is the first bad commit"
git bisect reset
```

- [ ] Found exact bad commit in ~3-4 steps

### 3. Find it automatically

```bash
git bisect start
git bisect bad HEAD
git bisect good <first-commit-hash>
git bisect run grep -L "BUG_INTRODUCED" app.txt

git bisect reset
```

- [ ] Automated bisect found it instantly

---

## Part B: Worktree — Two Branches at Once (15 min)

### 4. Create a worktree

```bash
cd ~/path/to/Spotify-lyric-tracker

git worktree add ../spotify-hotfix main
git worktree list
```

Two directories now:
```
Spotify-lyric-tracker/  ← your workspace
spotify-hotfix/         ← separate copy on main
```

- [ ] `git worktree list` shows 2 entries

### 5. Work on both simultaneously

Terminal 1 (main workspace):
```bash
cd ~/path/to/Spotify-lyric-tracker
# Keep working on your feature...
```

Terminal 2 (hotfix):
```bash
cd ~/path/to/spotify-hotfix
git checkout -b hotfix/urgent-fix
echo "emergency fix" >> urgent.txt
git add . && git commit -m "fix(core): resolve production crash"
```

- [ ] Worked on two branches in parallel

### 6. Clean up

```bash
cd ~/path/to/Spotify-lyric-tracker
git worktree remove ../spotify-hotfix
git worktree list    # Back to 1
```

- [ ] Worktree removed cleanly

### 7. Clean up bisect practice

```bash
rm -rf ~/bisect-practice
```

---

## When to Use

| Tool | Scenario |
|------|----------|
| `bisect` | "Which commit broke this?" |
| `bisect run` | Same, but automated with test |
| `worktree` | Urgent fix while mid-feature |
| `worktree` | Review code while coding |

---

## Self-Check

- [ ] Found a bug with manual bisect
- [ ] Found a bug with automated `bisect run`
- [ ] Created and used a worktree
- [ ] Cleaned up worktree

**Next → [Day 13: Final Challenge](day-13-final-challenge.md)**
