# Day 04 — Stash with Purpose

**Goal:** Save work-in-progress without committing, and find it again later.

---

## Why This Matters

You're working on a feature. Suddenly your boss says "fix this bug NOW."
You have uncommitted work. Options:
- Bad: `git commit -m "WIP asdfg"` (dirty commit)
- Bad: lose your changes
- Elite: `git stash save "WIP: lyrics caching feature"` (saved, labeled, retrievable)

---

## The Commands

```bash
git stash                              # Quick save (unnamed)
git stash save "description"           # Named save (ALWAYS do this)
git stash list                         # See all stashes
git stash show stash@{0}               # Preview what's in a stash
git stash show -p stash@{0}            # Full diff of stash
git stash apply stash@{0}              # Apply stash (keep in list)
git stash pop stash@{0}                # Apply stash (remove from list)
git stash drop stash@{0}               # Delete a stash
git stash clear                        # Delete ALL stashes (careful!)
git stash push <path> -m "desc"        # Stash SPECIFIC files only
git stash branch <branch> stash@{0}    # Create branch from stash
```

---

## Hands-On Exercise

### Exercise 1: Basic Stash & Retrieve

```bash
git checkout main
git checkout -b practice/stash-exercise
```

Start "working" on something:
```bash
echo "new feature code" >> feature-wip.txt
echo "more code here" >> feature-wip2.txt
git add feature-wip.txt
# feature-wip.txt is staged, feature-wip2.txt is untracked
git st
```

Now stash it with a name:
```bash
git stash save "WIP: new lyrics caching feature"
```

Check:
```bash
git st
# Working directory is clean!

git stash list
# stash@{0}: On practice/stash-exercise: WIP: new lyrics caching feature
```

- [ ] Your working directory is clean
- [ ] `git stash list` shows your named stash

Now get it back:
```bash
git stash pop stash@{0}
git st
# Your files are back!
```

- [ ] Files are restored

### Exercise 2: Multiple Named Stashes

Create 3 different stashes:
```bash
# Stash 1: UI work
echo "button styles" >> ui-changes.txt
git add ui-changes.txt
git stash save "WIP: button styling for popup"

# Stash 2: API work
echo "api endpoint" >> api-changes.txt
git add api-changes.txt
git stash save "WIP: spotify API token refresh"

# Stash 3: Bug fix
echo "fix null check" >> bugfix.txt
git add bugfix.txt
git stash save "WIP: fix null pointer in lyric parser"
```

Now list them:
```bash
git stash list
```

You should see:
```
stash@{0}: On practice/stash-exercise: WIP: fix null pointer in lyric parser
stash@{1}: On practice/stash-exercise: WIP: spotify API token refresh
stash@{2}: On practice/stash-exercise: WIP: button styling for popup
```

- [ ] All 3 stashes are listed with clear names
- [ ] You can tell what each one contains WITHOUT looking at the code

Preview a specific stash:
```bash
git stash show -p stash@{1}
```

- [ ] You can see the diff of the API stash

Apply only the bug fix (stash@{0}):
```bash
git stash pop stash@{0}
git st
# Only the bugfix file is back
```

- [ ] Only bugfix.txt appeared, other stashes still saved

### Exercise 3: Stash Specific Files

This is the pro move. Stash only certain files:

```bash
# Create files in different "areas"
echo "database schema" >> db-change.txt
echo "frontend style" >> style-change.txt
git add .

# Stash ONLY the database file
git stash push db-change.txt -m "WIP: database schema migration"
git st
# style-change.txt is still staged, db-change.txt is stashed away
```

- [ ] Only `db-change.txt` was stashed
- [ ] `style-change.txt` remains in your working directory

### Exercise 4: Create Branch From Stash

Sometimes a stash grows into a full feature. Turn it into a branch:

```bash
# Make sure you have a stash
echo "big feature" >> big-feature.txt
git add big-feature.txt
git stash save "WIP: big feature that needs its own branch"

# Create a branch from that stash
git stash branch feature/big-feature stash@{0}

# You're now on a new branch with the stash applied!
git st
git br
```

- [ ] New branch `feature/big-feature` was created
- [ ] The stash was automatically applied and removed from stash list

---

## When to Stash vs Commit

| Situation | Use Stash | Use Commit |
|-----------|-----------|------------|
| Switching branches quickly | Yes | No |
| Work is half-done, not ready | Yes | No |
| You want it in git history | No | Yes |
| Might need it in days/weeks | No | Yes (on a branch) |
| Quick context switch | Yes | No |

---

## Self-Check

- [ ] I can create named stashes
- [ ] I can list and preview stashes
- [ ] I can pop a specific stash (not just the latest)
- [ ] I can stash specific files only
- [ ] I can create a branch from a stash
- [ ] I ALWAYS name my stashes (never bare `git stash`)

---

## Cleanup

```bash
git checkout main
git stash clear    # Remove all practice stashes
```

**Done? Move to [Day 05 — Cherry-Pick](day-05-cherry-pick.md)**
