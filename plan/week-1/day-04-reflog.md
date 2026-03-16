# Day 04 — Reflog Recovery

**Time:** 25 min | **Repo:** `Spotify-lyric-tracker`

---

## Key Idea

Git reflog records EVERY action for 30 days. If it was committed, it can be recovered.

```bash
git reflog                          # See all actions
git reset --hard <hash>             # Jump back to any state
git branch <name> <hash>            # Create branch from old state
```

---

## Do This Now

### 1. Recover a deleted branch

```bash
git checkout main
git checkout -b important-feature
echo "critical code" >> important.txt
git add . && git commit -m "feat: critical feature"
echo "more critical" >> important.txt
git add . && git commit -m "feat: more critical code"

# "Accidentally" delete it
git checkout main
git branch -D important-feature
git br    # Gone!

# RECOVER
git reflog    # Find "feat: more critical code" hash
git branch important-feature <that-hash>
git checkout important-feature
git log --oneline -3
```

- [ ] Branch deleted then fully recovered

### 2. Recover from bad rebase

```bash
git checkout main
git checkout -b practice/bad-rebase

echo "1" >> victim.txt && git add . && git commit -m "feat: work 1"
echo "2" >> victim.txt && git add . && git commit -m "feat: work 2"
echo "3" >> victim.txt && git add . && git commit -m "feat: work 3"

# Bad rebase — squash everything
git rebase -i HEAD~3
# Squash all into one

# Oops, want them back!
git reflog    # Find hash BEFORE "rebase: start"
git reset --hard <pre-rebase-hash>
git log --oneline -3
```

- [ ] Recovered all 3 commits after bad rebase

### 3. Recover from hard reset

```bash
git checkout -b practice/bad-reset
echo "precious" >> precious.txt && git add . && git commit -m "feat: precious work"
echo "more" >> precious2.txt && git add . && git commit -m "feat: more precious"

# "Accidentally" nuke everything
git reset --hard HEAD~2

# RECOVER
git reflog
git reset --hard <hash-of-more-precious>
```

- [ ] Recovered from `git reset --hard`

### 4. Browse old state safely

```bash
git reflog
git checkout <any-old-hash>    # Detached HEAD — just viewing
# Look around...
git checkout main              # Back to safety
```

- [ ] Explored old state without breaking anything

---

## Reflog Cheat Sheet

| Disaster | Fix |
|----------|-----|
| Deleted branch | `git branch <name> <reflog-hash>` |
| Bad rebase | `git reset --hard <pre-rebase-hash>` |
| Bad reset | `git reset --hard <lost-commit-hash>` |
| Lost commits | `git cherry-pick <reflog-hash>` |

---

## Self-Check

- [ ] Recovered deleted branch
- [ ] Recovered from bad rebase
- [ ] Recovered from hard reset
- [ ] I feel confident I can't permanently lose committed work

**Next → [Day 05: PR Template + Branch Protection + CODEOWNERS](day-05-pr-protection-codeowners.md)**
