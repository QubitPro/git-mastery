# Day 05 — Cherry-Pick

**Goal:** Copy specific commits from one branch to another without merging everything.

---

## Why This Matters

Scenario: You fixed a bug on your feature branch. Production needs that fix NOW.
You don't want to merge the whole feature — just the one fix commit.

`git cherry-pick` = copy ONE commit to another branch.

---

## The Commands

```bash
git cherry-pick <commit-hash>           # Copy one commit
git cherry-pick <hash1> <hash2>         # Copy multiple commits
git cherry-pick <hash1>..<hash2>        # Copy a range
git cherry-pick -n <hash>               # Copy but DON'T commit (review first)
git cherry-pick --abort                 # Cancel if conflicts
git cherry-pick --continue              # Continue after resolving conflicts
```

---

## Hands-On Exercise

### Exercise 1: Setup Two Branches

```bash
git checkout main

# Create a "feature" branch with 3 commits
git checkout -b feature/cherry-exercise

echo "feature part 1" >> feature.txt
git add . && git commit -m "feat(app): add feature part 1"

echo "critical bugfix" >> bugfix.txt
git add . && git commit -m "fix(core): resolve null pointer crash"

echo "feature part 2" >> feature.txt
git add . && git commit -m "feat(app): add feature part 2"
```

Check your commits:
```bash
git log --oneline -3
```

Note the hash of the BUGFIX commit (the middle one). You'll need it.

```
abc1234 feat(app): add feature part 2
def5678 fix(core): resolve null pointer crash    <-- THIS ONE
ghi9012 feat(app): add feature part 1
```

- [ ] You have 3 commits, and you identified the bugfix hash

### Exercise 2: Cherry-Pick the Bugfix to Main

```bash
# Go back to main
git checkout main

# Cherry-pick ONLY the bugfix commit
git cherry-pick def5678    # Use YOUR actual hash!

# Check
git log --oneline -3
```

- [ ] Main now has the bugfix commit
- [ ] Main does NOT have the feature commits
- [ ] The bugfix commit has a NEW hash (it's a copy, not a move)

### Exercise 3: Cherry-Pick Without Committing

Sometimes you want to review before committing:

```bash
git checkout main
git checkout -b practice/cherry-review

# Cherry-pick but DON'T auto-commit
git cherry-pick -n <hash-of-feature-part-1>

# Review what was picked
git st
git staged

# Now commit with your own message
git commit -m "feat(app): backport feature part 1 from feature branch"
```

- [ ] You reviewed the changes before committing
- [ ] You wrote your own commit message

### Exercise 4: Cherry-Pick Multiple Commits

```bash
git checkout main
git checkout -b practice/cherry-multi

# Pick the first and third commits from feature branch (skip the middle)
git cherry-pick <hash1> <hash3>

git log --oneline -5
```

- [ ] Both commits are on your new branch
- [ ] The middle commit was skipped

### Exercise 5: Handle a Conflict

```bash
git checkout main
git checkout -b practice/cherry-conflict

# Create a file that will conflict
echo "main version" >> conflict-test.txt
git add . && git commit -m "feat: add conflict test on main"

# Go to feature branch, modify same file
git checkout feature/cherry-exercise
echo "feature version" >> conflict-test.txt
git add . && git commit -m "feat: add conflict test on feature"

# Try to cherry-pick to main branch
git checkout practice/cherry-conflict
git cherry-pick <hash-of-feature-conflict-commit>
```

You'll see a CONFLICT. Handle it:
```bash
# Open the conflicting file and resolve it manually
# Look for <<<<<<< HEAD / ======= / >>>>>>> markers
# Keep what you want, delete the markers

# After resolving:
git add conflict-test.txt
git cherry-pick --continue
```

- [ ] You resolved a cherry-pick conflict
- [ ] You understand the conflict markers

---

## Real-World Use Cases

| Scenario | Command |
|----------|---------|
| Hotfix needed on production | `git cherry-pick <fix-hash>` to release branch |
| Backport security fix | `git cherry-pick <fix>` to older version branches |
| Pull one feature from a big branch | `git cherry-pick -n <hash>` and review |
| Undo a cherry-pick | `git revert <cherry-picked-hash>` |

---

## Self-Check

- [ ] I can cherry-pick a single commit to another branch
- [ ] I can cherry-pick without auto-committing (`-n` flag)
- [ ] I can cherry-pick multiple specific commits
- [ ] I can resolve a cherry-pick conflict
- [ ] I understand cherry-pick creates a COPY (new hash)

---

## Cleanup

```bash
git checkout main
```

**Done? Move to [Day 06 — Reflog](day-06-reflog.md)**
