# Day 11 — Merge Strategies

**Time:** 25 min | **Repo:** `Spotify-lyric-tracker`

---

## The Three Options

| Strategy | History | Use For |
|----------|---------|---------|
| **Merge commit** | Shows branch + merge point | Major features, releases |
| **Squash merge** | 1 clean commit on main | Most PRs, features |
| **Rebase merge** | Linear, no merge commit | Hotfixes |

---

## Do This Now

### 1. Merge Commit

```bash
git checkout main
git checkout -b feature/merge-demo

echo "part 1" >> merge.txt && git add . && git commit -m "feat: merge part 1"
echo "part 2" >> merge.txt && git add . && git commit -m "feat: merge part 2"

git checkout main
git merge --no-ff feature/merge-demo -m "Merge feature/merge-demo"
git visual
```

- [ ] See branch lines joining at merge commit

### 2. Squash Merge

```bash
git checkout -b feature/squash-demo

echo "1" >> squash.txt && git add . && git commit -m "feat: squash 1"
echo "2" >> squash.txt && git add . && git commit -m "WIP: still working"
echo "3" >> squash.txt && git add . && git commit -m "fix: oops"

git checkout main
git merge --squash feature/squash-demo
git commit -m "feat: add squash feature (complete)"
git visual
```

- [ ] 3 commits → 1 clean commit on main
- [ ] No WIP visible in main history

### 3. Rebase Merge

```bash
git checkout -b feature/rebase-demo

echo "1" >> rebase.txt && git add . && git commit -m "fix: rebase part 1"
echo "2" >> rebase.txt && git add . && git commit -m "fix: rebase part 2"

git rebase main
git checkout main
git merge feature/rebase-demo
git visual
```

- [ ] Linear history, no merge commit
- [ ] Individual commits preserved

### 4. Compare all three

```bash
git log --oneline --graph -15
```

- [ ] I can see the visual difference

### 5. Set defaults on GitHub

**Settings** → **General** → **Pull Requests**:
- [x] Allow merge commits
- [x] Allow squash merging ← **default**
- [x] Allow rebase merging

### 6. Practice via `gh`

```bash
# Squash merge (features)
gh pr merge <PR-number> --squash --delete-branch

# Merge commit (releases)
gh pr merge <PR-number> --merge

# Rebase merge (hotfixes)
gh pr merge <PR-number> --rebase --delete-branch
```

---

## Quick Decision Guide

```
Feature branch  → gh pr merge --squash
Hotfix branch   → gh pr merge --rebase
Release branch  → gh pr merge --merge
Dependabot PR   → gh pr merge --squash
```

---

## Self-Check

- [ ] Did all three merge types
- [ ] Can see visual difference in `git log --graph`
- [ ] Know which strategy for which scenario
- [ ] Can merge PRs via `gh` CLI

**Next → [Day 12: Bisect + Worktree](day-12-bisect-and-worktree.md)**
