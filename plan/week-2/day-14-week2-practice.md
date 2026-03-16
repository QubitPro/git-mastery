# Day 14 — Week 2 Practice Challenge

**Goal:** Combine Days 8-13 into one complete professional workflow.

---

## The Challenge

Simulate adding a real feature to `Spotify-lyric-tracker` using the full elite workflow.

---

## Step-by-Step

### Phase 1: Create Feature Branch + Draft PR

- [ ] Create branch: `feature/keyboard-shortcuts`
  ```bash
  git checkout main && git pull
  git checkout -b feature/keyboard-shortcuts
  ```

- [ ] Make an initial commit:
  ```bash
  echo "// Keyboard shortcut handlers" >> shortcuts.js
  git add shortcuts.js
  git commit -m "feat(ui): add keyboard shortcuts module"
  ```

- [ ] Push and create a **Draft PR** on GitHub:
  ```bash
  git push -u origin feature/keyboard-shortcuts
  ```
  (On GitHub: select "Create draft pull request")

- [ ] Verify the PR template auto-fills (Day 08)
- [ ] Verify CODEOWNERS auto-assigns reviewer (Day 10)

### Phase 2: Develop with Hooks Active

- [ ] Verify your git hooks are active (Day 13)

- [ ] Try a BAD commit (should be blocked):
  ```bash
  echo "console.log('test');" >> shortcuts.js
  git add . && git commit -m "added thing"
  ```
  Should fail on BOTH pre-commit (console.log) and commit-msg (not semantic)

- [ ] Fix and commit properly:
  ```bash
  # Remove the console.log line from shortcuts.js
  git add . && git commit -m "feat(ui): add spacebar pause/resume shortcut"
  ```

- [ ] Make 2 more proper commits:
  ```bash
  echo "// Arrow key handler" >> shortcuts.js
  git add . && git commit -m "feat(ui): add arrow key seek shortcuts"

  echo "// ESC handler" >> shortcuts.js
  git add . && git commit -m "feat(ui): add escape key to close popup"
  ```

- [ ] Push and verify CI runs (Day 11):
  ```bash
  git push
  ```
  Check GitHub Actions tab.

### Phase 3: Code Review

- [ ] On your Draft PR, add a progress comment:
  ```
  Progress:
  - [x] Spacebar pause/resume
  - [x] Arrow key seeking
  - [x] ESC to close
  - [ ] Key binding configuration
  ```

- [ ] Convert PR from Draft to **Ready for Review**

- [ ] Write a self-review comment using elite review format (Day 12):
  ```
  ### Self-Review

  #### What's Good
  - Clean separation of keyboard logic
  - Each shortcut in its own function

  #### Concerns
  - Should we check for input focus before handling keys?
  - ESC might conflict with browser fullscreen exit

  #### Testing Done
  - Manual testing in Chrome
  ```

### Phase 4: Verify All Checks

On the PR page, confirm:

- [ ] CI status check shows green (or running)
- [ ] CODEOWNERS assigned you as reviewer
- [ ] PR template was filled in
- [ ] Branch protection prevents merging without checks

### Phase 5: Merge

- [ ] Merge the PR using **"Squash and merge"**
  (This combines all commits into one clean commit on main)

- [ ] Delete the feature branch on GitHub (it offers this after merge)

- [ ] Locally:
  ```bash
  git checkout main
  git pull
  git branch -d feature/keyboard-shortcuts
  ```

---

## Grading

| Criteria | Points |
|----------|--------|
| Draft PR created properly | /10 |
| PR template auto-filled | /10 |
| CODEOWNERS auto-assigned | /10 |
| Git hooks blocked bad commits | /15 |
| All commits use semantic format | /15 |
| CI ran on the PR | /15 |
| Self-review comment posted | /10 |
| Squash-merged cleanly | /15 |
| **Total** | **/100** |

---

## Week 2 Summary — Skills Unlocked

```
[x] PR Templates      → Every PR has consistent quality
[x] Branch Protection  → Main is always safe
[x] CODEOWNERS         → Right reviewers auto-assigned
[x] GitHub Actions     → Automated CI on every push
[x] Draft PRs          → Early feedback, no accidental merges
[x] Code Review        → Review like a senior engineer
[x] Git Hooks          → Catch mistakes before they commit
```

**You now have a professional GitHub workflow. Most dev teams don't even have this.**

---

**Done? Move to [Week 3 Day 15 — Semantic Versioning](../week-3/day-15-semantic-versioning.md)**
