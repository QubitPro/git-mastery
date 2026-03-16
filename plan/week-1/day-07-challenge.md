# Day 07 — Week 1 Challenge

**Time:** 45 min | **Repo:** `Spotify-lyric-tracker`

Use EVERY skill from Days 1-6 in one realistic workflow.

---

## The Mission

Add a feature, handle a bug interruption, clean up, and ship.

### Phase 1: Start Feature (10 min)

```bash
git checkout main && git pull
git checkout -b feature/lyrics-cache
```

Make 4 semantic commits:
- [ ] `feat(cache): add cache storage module`
- [ ] `feat(cache): implement expiration logic`
- [ ] `fix(cache): handle empty lyrics edge case`
- [ ] `test(cache): add cache unit tests`

Push and create a **PR** (your template should auto-fill).

### Phase 2: Bug Interruption (10 min)

- [ ] Stash uncommitted work: `git stash save "WIP: cache optimization"`
- [ ] Create hotfix:
  ```bash
  git checkout main
  git checkout -b hotfix/sync-crash
  echo "fix" >> sync-fix.txt
  git add . && git commit -m "fix(sync): resolve crash on songs without timestamps"
  ```
- [ ] Cherry-pick fix to main:
  ```bash
  git checkout main
  git cherry-pick <hotfix-hash>
  ```

### Phase 3: Resume Feature (5 min)

```bash
git checkout feature/lyrics-cache
git stash pop
```

Make 2 WIP commits (on purpose):
```bash
echo "wip" >> wip1.txt && git add . && git commit -m "WIP"
echo "wip" >> wip2.txt && git add . && git commit -m "WIP again"
```

### Phase 4: Clean Up (10 min)

- [ ] Interactive rebase — squash WIP into clean commits:
  ```bash
  git rebase -i HEAD~6
  ```
- [ ] Result: 3-4 clean semantic commits, zero WIP
- [ ] Push and verify CI passes on PR

### Phase 5: Disaster Recovery (5 min)

- [ ] "Accidentally" reset: `git reset --hard HEAD~3`
- [ ] Recover with reflog: `git reflog` → `git reset --hard <hash>`
- [ ] Verify all commits are back

### Phase 6: Ship It (5 min)

- [ ] Verify on PR page:
  - [ ] PR template filled
  - [ ] CODEOWNERS assigned reviewer
  - [ ] CI green
- [ ] Merge with **Squash and Merge**
- [ ] Delete branch on GitHub

---

## Score Yourself

| Skill | Points |
|-------|--------|
| All commits semantic | /15 |
| Stash named + recovered | /10 |
| Cherry-pick worked | /15 |
| Rebase cleaned WIP | /20 |
| Reflog recovery worked | /15 |
| CI passed on PR | /10 |
| PR template + CODEOWNERS | /10 |
| Git aliases used | /5 |
| **Total** | **/100** |

**90+:** Ready for Week 2. **Below 70:** Redo weak days.

---

## Week 1 Skills Unlocked

```
[x] Semantic Commits     [x] Git Aliases
[x] Interactive Rebase   [x] Stash + Cherry-Pick
[x] Reflog Recovery      [x] PR Templates
[x] Branch Protection    [x] CODEOWNERS
[x] GitHub Actions CI    [x] Git Hooks
```

**Next → [Week 2 Day 08: Draft PRs + Code Review](../week-2/day-08-drafts-and-review.md)**
