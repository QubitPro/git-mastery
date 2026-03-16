# Day 07 — Week 1 Practice Challenge

**Goal:** Combine everything from this week in one realistic workflow.

---

## The Challenge

You will simulate a real workday using ALL the skills from Days 1-6.

### Scenario

You're working on `Spotify-lyric-tracker`. You need to:
1. Add a new feature (lyrics caching)
2. Handle an urgent bug interruption
3. Clean up your history before PR

---

## Step-by-Step Challenge

### Phase 1: Start Feature Work

```bash
git checkout main
git checkout -b feature/lyrics-cache
```

Make 4 commits using **semantic commit format** (Day 01):

- [ ] `feat(cache): add cache storage module`
- [ ] `feat(cache): implement cache expiration logic`
- [ ] `fix(cache): handle empty lyrics edge case`
- [ ] `test(cache): add unit tests for cache module`

(Create actual files for each — content doesn't matter, the workflow does)

### Phase 2: Urgent Bug Interruption

You're mid-feature but a bug needs fixing NOW.

- [ ] **Stash** your uncommitted work with a descriptive name (Day 04)
  ```bash
  git stash save "WIP: cache optimization not finished"
  ```

- [ ] Switch to main and create a hotfix branch:
  ```bash
  git checkout main
  git checkout -b hotfix/sync-crash
  ```

- [ ] Make the fix with a proper commit:
  ```bash
  echo "fix applied" >> sync-fix.txt
  git add . && git commit -m "fix(sync): resolve crash when song has no timestamps"
  ```

- [ ] **Cherry-pick** (Day 05) this fix to main:
  ```bash
  git checkout main
  git cherry-pick <hotfix-commit-hash>
  ```

### Phase 3: Return to Feature

- [ ] Go back to your feature branch:
  ```bash
  git checkout feature/lyrics-cache
  ```

- [ ] Retrieve your stashed work:
  ```bash
  git stash pop
  ```

- [ ] Make 2 more sloppy "WIP" commits (on purpose):
  ```bash
  echo "wip stuff" >> wip1.txt && git add . && git commit -m "WIP"
  echo "more wip" >> wip2.txt && git add . && git commit -m "WIP again"
  ```

### Phase 4: Clean Up History

Before submitting a PR, clean your branch (Day 03):

- [ ] **Interactive rebase** — squash the 2 WIP commits into the feature commits:
  ```bash
  git rebase -i HEAD~6
  ```

- [ ] Result should be 3-4 clean semantic commits, no WIP

- [ ] Verify with `git log --oneline`

### Phase 5: Simulate a Disaster & Recover

- [ ] "Accidentally" reset your branch:
  ```bash
  git reset --hard HEAD~3
  # OH NO! Commits are gone!
  ```

- [ ] Use **reflog** (Day 06) to recover:
  ```bash
  git reflog
  git reset --hard <hash-before-reset>
  ```

- [ ] Verify all commits are back

### Phase 6: Final Review

Run these commands using your **aliases** (Day 02):

- [ ] `git visual` — see the full tree
- [ ] `git review` — see your commits vs main
- [ ] `git last` — check your latest commit

---

## Grading Yourself

| Criteria | Points |
|----------|--------|
| All commits use semantic format | /20 |
| Stash was named descriptively | /10 |
| Cherry-pick worked correctly | /15 |
| Rebase cleaned up WIP commits | /20 |
| Reflog recovery worked | /20 |
| Used git aliases throughout | /15 |
| **Total** | **/100** |

### Score Meaning
- **90-100:** You're ready for Week 2
- **70-89:** Review the weak area, redo that day's exercise
- **Below 70:** Redo Days 1-6 before moving on

---

## Cleanup

```bash
# Delete all practice branches
git checkout main
git branch | grep practice/ | xargs git branch -D
git branch | grep feature/ | xargs git branch -D
git branch | grep hotfix/ | xargs git branch -D
```

---

## Week 1 Summary — Skills Unlocked

```
[x] Semantic Commits    → Professional commit messages
[x] Git Aliases         → 2x faster git workflow
[x] Interactive Rebase  → Clean history before PRs
[x] Stash with Purpose  → Save WIP without dirty commits
[x] Cherry-Pick         → Move specific commits between branches
[x] Reflog              → Recover from ANY disaster
```

**Congratulations! You now use Git better than 90% of developers.**

---

**Done? Move to [Week 2 Day 08 — PR Template](../week-2/day-08-pr-template.md)**
