# Day 19 — Merge Strategies

**Goal:** Know when to merge, squash, or rebase — and why it matters.

---

## The Three Strategies

### 1. Merge Commit (default)
```
main:     A---B---C-------M
               \         /
feature:        D---E---F
```
- Creates a merge commit `M`
- ALL feature commits visible in main history
- Easy to see where a feature started/ended
- **Use for:** major features, releases

### 2. Squash and Merge
```
main:     A---B---C---S
               \
feature:        D---E---F  (squashed into S)
```
- Combines ALL feature commits into ONE commit `S`
- Clean, linear main history
- Individual commits are lost from main
- **Use for:** small features, bug fixes, most PRs

### 3. Rebase and Merge
```
main:     A---B---C---D'---E'---F'
               \
feature:        D---E---F  (replayed as D', E', F')
```
- Replays feature commits on top of main
- Linear history, no merge commit
- Individual commits preserved
- **Use for:** hotfixes, clean linear history

---

## Hands-On Exercise

### Exercise 1: Merge Commit

```bash
git checkout main
git checkout -b feature/merge-demo

echo "merge feature 1" >> merge-demo.txt
git add . && git commit -m "feat(demo): add merge feature part 1"

echo "merge feature 2" >> merge-demo.txt
git add . && git commit -m "feat(demo): add merge feature part 2"

# Merge with merge commit
git checkout main
git merge --no-ff feature/merge-demo -m "Merge branch 'feature/merge-demo'"
```

Check the history:
```bash
git visual
```

- [ ] You see a merge commit with two parent branches joining
- [ ] Both individual commits are visible in history

### Exercise 2: Squash Merge

```bash
git checkout main
git checkout -b feature/squash-demo

echo "squash feature 1" >> squash-demo.txt
git add . && git commit -m "feat: squash part 1"

echo "squash feature 2" >> squash-demo.txt
git add . && git commit -m "WIP: still working"

echo "squash feature 3" >> squash-demo.txt
git add . && git commit -m "fix: oops typo"

# Squash merge — all 3 commits become 1
git checkout main
git merge --squash feature/squash-demo
git commit -m "feat(demo): add squash feature (complete)"
```

Check the history:
```bash
git visual
```

- [ ] Only ONE commit appears on main (not 3)
- [ ] The messy "WIP" and "oops" commits are gone
- [ ] Main history is clean

### Exercise 3: Rebase Merge

```bash
git checkout main
git checkout -b feature/rebase-demo

echo "rebase feature 1" >> rebase-demo.txt
git add . && git commit -m "feat(demo): rebase part 1"

echo "rebase feature 2" >> rebase-demo.txt
git add . && git commit -m "feat(demo): rebase part 2"

# Rebase onto main, then fast-forward merge
git rebase main
git checkout main
git merge feature/rebase-demo
```

Check the history:
```bash
git visual
```

- [ ] Commits appear linearly on main (no merge commit)
- [ ] Both individual commits are preserved

### Exercise 4: Compare All Three

```bash
git log --oneline --graph -15
```

Look at the difference:
- Merge commit: shows branching and merging
- Squash: single clean commit
- Rebase: linear, individual commits

- [ ] I can visually tell the difference between all three

### Exercise 5: Set Default on GitHub

On GitHub, you can restrict which merge methods are allowed:

1. Go to **Settings** → **General** → scroll to **"Pull Requests"**
2. Configure:
   - [x] Allow merge commits
   - [x] Allow squash merging ← **make this the default**
   - [x] Allow rebase merging
   - [x] Always suggest updating pull request branches

- [ ] Default merge strategy set on GitHub

---

## When to Use Which

| Scenario | Strategy | Why |
|----------|----------|-----|
| Feature branch (3+ commits) | **Squash** | Clean history, hide WIP |
| Major feature (important history) | **Merge commit** | Preserve all context |
| Hotfix (1-2 commits) | **Rebase** | Fast, linear |
| Dependabot PRs | **Squash** | One clean update commit |
| Release branch | **Merge commit** | Mark the release point |

---

## Self-Check

- [ ] I performed all three merge strategies
- [ ] I can explain the visual difference in `git log --graph`
- [ ] I know when to use each strategy
- [ ] I set a default strategy on GitHub
- [ ] Squash is my default for most PRs

---

**Done? Move to [Day 20 — Git Bisect & Worktree](day-20-bisect-worktree.md)**
