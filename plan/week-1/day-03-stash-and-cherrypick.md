# Day 03 — Stash + Cherry-Pick

**Time:** 30 min | **Repo:** `Spotify-lyric-tracker`

---

## Part A: Stash (Save WIP Without Committing)

### Do This Now

#### 1. Named stash

```bash
git checkout main
git checkout -b practice/stash

echo "half done feature" >> wip.txt
git add wip.txt
git stash save "WIP: lyrics caching feature"

git st                    # Clean!
git stash list            # Your stash is saved
```

- [ ] Working directory clean after stash
- [ ] `git stash list` shows named stash

#### 2. Multiple stashes + selective pop

```bash
echo "ui work" >> ui.txt && git add . && git stash save "WIP: button styles"
echo "api work" >> api.txt && git add . && git stash save "WIP: API refresh"
echo "bugfix" >> fix.txt && git add . && git stash save "WIP: null pointer fix"

git stash list
# stash@{0}: WIP: null pointer fix
# stash@{1}: WIP: API refresh
# stash@{2}: WIP: button styles

# Pop only the bugfix
git stash pop stash@{0}
git st
```

- [ ] Popped specific stash, others remain

#### 3. Stash specific files

```bash
echo "db stuff" >> db.txt
echo "css stuff" >> style.txt
git add .

git stash push db.txt -m "WIP: database schema"
git st
# style.txt still staged, db.txt stashed
```

- [ ] Only selected file was stashed

#### 4. Branch from stash

```bash
echo "big idea" >> idea.txt && git add . && git stash save "WIP: big feature idea"
git stash branch feature/big-idea stash@{0}
git st && git br
```

- [ ] New branch created from stash

---

## Part B: Cherry-Pick (Copy Specific Commits)

#### 5. Setup

```bash
git checkout main
git checkout -b feature/cherry-test

echo "feat 1" >> cherry.txt && git add . && git commit -m "feat: part 1"
echo "BUGFIX" >> cherry.txt && git add . && git commit -m "fix: critical bug"
echo "feat 2" >> cherry.txt && git add . && git commit -m "feat: part 2"

git log --oneline -3
# Note the BUGFIX commit hash
```

#### 6. Cherry-pick the bugfix to main

```bash
git checkout main
git cherry-pick <bugfix-hash>

git log --oneline -3
```

- [ ] Main has the bugfix but NOT the feature commits
- [ ] Bugfix has a NEW hash (it's a copy)

#### 7. Cherry-pick without auto-commit

```bash
git checkout -b practice/cherry-review
git cherry-pick -n <feat-part-1-hash>

git staged                # Review what was picked
git commit -m "feat: backported part 1 from feature branch"
```

- [ ] Reviewed changes before committing

#### 8. Handle a conflict

```bash
git checkout main
echo "main version" >> conflict.txt && git add . && git commit -m "feat: main version"

git checkout feature/cherry-test
echo "feature version" >> conflict.txt && git add . && git commit -m "feat: feature version"

git checkout main
git cherry-pick <feature-conflict-hash>
# CONFLICT! Resolve manually, then:
git add conflict.txt
git cherry-pick --continue
```

- [ ] Resolved a cherry-pick conflict

---

## Self-Check

- [ ] Named stashes, selective pop, stash specific files
- [ ] Cherry-picked single commit between branches
- [ ] Cherry-picked without auto-commit (`-n`)
- [ ] Resolved a cherry-pick conflict

**Next → [Day 04: Reflog Recovery](day-04-reflog.md)**
