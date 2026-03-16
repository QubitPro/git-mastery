# Day 13 — Final Challenge

**Time:** 60 min | **Repo:** `Spotify-lyric-tracker`

Use EVERY skill from both weeks. This is your graduation test.

---

## Mission: Bug Fix + Feature + Release

### Task 1: Bug Report → Hotfix → Release (20 min)

- [ ] Create an **Issue** on GitHub:
  ```bash
  gh issue create --title "[Bug] Lyrics fail for songs with apostrophes" \
    --body "Songs like \"Don't Stop Me Now\" cause a parse error"
  ```

- [ ] Create hotfix branch:
  ```bash
  git checkout main && git pull
  git checkout -b hotfix/apostrophe-fix
  ```

- [ ] Fix and commit:
  ```bash
  echo "// escape apostrophes" >> fix-apostrophe.js
  git add . && git commit -m "fix(lyrics): escape apostrophes in title parsing

  Closes #<issue-number>"
  ```

- [ ] Create PR and merge:
  ```bash
  git push -u origin hotfix/apostrophe-fix
  gh pr create --title "fix: escape apostrophes in lyrics parsing" --body "Fixes #<number>"
  gh pr merge --rebase --delete-branch
  ```

- [ ] Tag + Release:
  ```bash
  git checkout main && git pull
  git tag -a v1.1.1 -m "v1.1.1 - Fix apostrophe parsing"
  git push origin v1.1.1
  gh release create v1.1.1 --title "v1.1.1 — Apostrophe Fix" \
    --notes "### Fixed
  - Lyrics now load for songs with apostrophes in titles"
  ```

- [ ] Update CHANGELOG.md with `[1.1.1]` section

### Task 2: Feature → Draft PR → Review → Ship (25 min)

- [ ] Create Issue:
  ```bash
  gh issue create --title "[Feature] Add mini-player mode" \
    --body "Compact lyrics view that takes less screen space"
  ```

- [ ] Branch + Draft PR:
  ```bash
  git checkout main && git pull
  git checkout -b feature/mini-player
  echo "// mini player" >> mini-player.js
  git add . && git commit -m "feat(ui): add mini-player layout"
  git push -u origin feature/mini-player
  gh pr create --title "feat: add mini-player mode" --body "WIP" --draft
  ```

- [ ] Make 3+ more commits (hooks validate them):
  ```bash
  echo "// animation" >> mini-player.js && git add . && git commit -m "feat(ui): add collapse animation"
  echo "/* responsive */" >> mini-player.css && git add . && git commit -m "style(ui): add responsive styles"
  echo "// tests" >> mini-player.test.js && git add . && git commit -m "test(ui): add mini-player tests"
  git push
  ```

- [ ] Interactive rebase if any messy commits:
  ```bash
  git rebase -i HEAD~4
  ```

- [ ] Convert to Ready + self-review:
  ```bash
  gh pr ready
  ```
  Post elite review comment on PR.

- [ ] Merge:
  ```bash
  gh pr merge --squash --delete-branch
  ```

- [ ] Tag + Release:
  ```bash
  git checkout main && git pull
  git tag -a v1.2.0 -m "v1.2.0 - Mini player mode"
  git push origin v1.2.0
  gh release create v1.2.0 --title "v1.2.0 — Mini Player" \
    --notes "### Added
  - Mini-player compact view
  - Collapse/expand animation
  - Responsive layout"
  ```

- [ ] Update CHANGELOG.md

### Task 3: Disaster Recovery (10 min)

- [ ] Delete the feature branch: `git branch -D feature/mini-player`
- [ ] Recover with `git reflog`
- [ ] Create worktree to verify: `git worktree add ../verify feature/mini-player`
- [ ] Clean up: `git worktree remove ../verify`

---

## Final Repo Audit

Check your `Spotify-lyric-tracker` on GitHub:

### Files
- [ ] `.github/pull_request_template.md`
- [ ] `.github/CODEOWNERS`
- [ ] `.github/dependabot.yml`
- [ ] `.github/workflows/ci.yml`
- [ ] `.githooks/pre-commit`
- [ ] `.githooks/commit-msg`
- [ ] `CHANGELOG.md`

### GitHub Settings
- [ ] Branch protection on `main`
- [ ] CI required to merge
- [ ] Multiple releases (v1.0.0, v1.0.1, v1.1.0, etc.)
- [ ] CI badge in README

### History
- [ ] All commits semantic
- [ ] No WIP/garbage in main
- [ ] Clean, linear history

---

## Score

| Category | Points |
|----------|--------|
| Bug: Issue → PR → merge → release | /15 |
| Feature: Draft PR → review → squash merge | /15 |
| All commits semantic + hooks validated | /10 |
| Interactive rebase cleaned history | /10 |
| Correct merge strategies (rebase for fix, squash for feat) | /10 |
| Version tags correct (PATCH for fix, MINOR for feat) | /10 |
| GitHub Releases created via `gh` | /10 |
| CHANGELOG updated | /5 |
| Disaster recovery worked | /10 |
| All repo files + settings in place | /5 |
| **Total** | **/100** |

---

## You're Done!

```
Week 1: Git Core + Workflow    ████████████ COMPLETE
Week 2: Release + Advanced     ████████████ COMPLETE

Skills: 20+ elite techniques mastered
Tools:  git, gh, GitHub Actions, hooks
Status: ELITE GITHUB DEVELOPER
```

### Keep Going
- Apply to ALL your repos (AlphaPulse_test, ESP32 projects, etc.)
- Explore: GitHub Projects, Packages, signed commits, GitOps
- Teach someone else — best way to solidify knowledge

**Your repos now look like they're maintained by a senior engineer.**
