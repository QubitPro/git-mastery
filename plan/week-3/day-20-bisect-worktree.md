# Day 20 — Git Bisect & Worktree

**Goal:** Find bugs like a detective (bisect) and work on multiple branches simultaneously (worktree).

---

## Part A: Git Bisect

### Why This Matters

Something is broken. You have 100 commits. Which one caused it?

- Bad approach: manually check each commit (hours)
- Elite approach: `git bisect` (binary search, finds it in ~7 steps)

### How It Works

```
commit 1 (good) → 2 → 3 → 4 → 5 → ... → 98 → 99 → 100 (bad)
                                ^
                          bisect finds this
```

Git checks the middle commit, you say good/bad, it narrows down by half each time.

---

### Exercise 1: Set Up a Repo With a Hidden Bug

```bash
mkdir ~/bisect-practice && cd ~/bisect-practice
git init
```

Create 10 commits. Commit #6 introduces a "bug":

```bash
# Commits 1-5 (good)
for i in 1 2 3 4 5; do
  echo "feature $i" >> app.txt
  git add . && git commit -m "feat: add feature $i"
done

# Commit 6 (THE BUG)
echo "BUG_INTRODUCED" >> app.txt
git add . && git commit -m "refactor: restructure data layer"

# Commits 7-10 (also bad, bug still there)
for i in 7 8 9 10; do
  echo "feature $i" >> app.txt
  git add . && git commit -m "feat: add feature $i"
done
```

Verify the "bug" exists:
```bash
grep "BUG_INTRODUCED" app.txt
# Found! But which commit added it?
```

### Exercise 2: Find the Bug with Bisect

```bash
# Start bisect
git bisect start

# Current (HEAD) is bad
git bisect bad

# First commit was good (find its hash)
git log --oneline
# Copy the hash of "feat: add feature 1"
git bisect good <first-commit-hash>
```

Git checks out a middle commit. Test it:
```bash
# Check if bug exists
grep "BUG_INTRODUCED" app.txt
# If found → bad. If not found → good.

git bisect bad    # or: git bisect good
```

Repeat until git tells you the exact bad commit:
```
abc1234 is the first bad commit
```

```bash
git bisect reset    # Exit bisect mode
```

- [ ] Bisect found the exact commit that introduced the bug
- [ ] It took ~3-4 steps instead of checking all 10

### Exercise 3: Automated Bisect

Even faster — let git test automatically:

```bash
git bisect start
git bisect bad HEAD
git bisect good <first-commit-hash>

# Run a test command automatically
git bisect run grep -L "BUG_INTRODUCED" app.txt
```

`grep -L` returns success (exit 0) if the string is NOT found (good), and fails if found (bad).

- [ ] Automated bisect found the bug without manual input

```bash
git bisect reset
```

---

## Part B: Git Worktree

### Why This Matters

Scenario: You're deep in a feature branch. Urgent hotfix needed. Options:
- Bad: stash everything, switch, fix, switch back
- Elite: open a SECOND copy of the repo on a different branch

`git worktree` = multiple working directories, one repo.

---

### Exercise 4: Create a Worktree

```bash
cd ~/path/to/Spotify-lyric-tracker
```

Create a separate worktree for a hotfix:
```bash
git worktree add ../spotify-hotfix main
```

Now you have TWO directories:
```
~/path/to/Spotify-lyric-tracker/     ← your main workspace
~/path/to/spotify-hotfix/            ← separate copy on main branch
```

```bash
# Check worktrees
git worktree list
```

- [ ] `git worktree list` shows 2 worktrees

### Exercise 5: Work in the Worktree

```bash
# Main workspace — continue your feature
cd ~/path/to/Spotify-lyric-tracker
# ... keep working here ...

# In another terminal — work on hotfix
cd ~/path/to/spotify-hotfix
git checkout -b hotfix/critical-fix
echo "hotfix applied" >> hotfix.txt
git add . && git commit -m "fix(core): resolve critical production issue"
```

Both directories share the same git history but can be on different branches!

- [ ] Worked on two branches simultaneously in separate directories

### Exercise 6: Clean Up Worktree

```bash
cd ~/path/to/Spotify-lyric-tracker

# Remove the worktree when done
git worktree remove ../spotify-hotfix

# Verify
git worktree list
```

- [ ] Worktree removed cleanly

---

## When to Use What

| Tool | Use Case |
|------|----------|
| **Bisect** | "Which commit broke this?" |
| **Bisect run** | Same, but automated with a test command |
| **Worktree** | Need to work on 2 branches without switching |
| **Worktree** | Code review while coding on another branch |
| **Worktree** | Compare behavior of 2 branches side-by-side |

---

## Self-Check

- [ ] I found a bug using `git bisect` manually
- [ ] I found a bug using `git bisect run` automatically
- [ ] I created a git worktree
- [ ] I worked on two branches simultaneously
- [ ] I removed a worktree cleanly
- [ ] I know bisect uses binary search (log2 N steps)

---

## Cleanup

```bash
rm -rf ~/bisect-practice
cd ~/path/to/Spotify-lyric-tracker
git worktree prune    # Clean up any stale worktrees
```

---

**Done? Move to [Day 21 — Final Challenge](day-21-final-challenge.md)**
