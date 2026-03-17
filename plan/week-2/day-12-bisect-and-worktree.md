# Day 12 — Git Bisect + Worktree

**Time:** 35 min | **Repo:** `mn-crypto-tracker`

---

## Today's Goal

Use `git bisect` to find a bug in the crypto tracker, and use `git worktree` to work on two things simultaneously.

---

## Part A: Bisect — Find the Bad Commit (15 min)

### 1. Create commits with a hidden bug

```bash
git checkout main && git pull
git checkout -b feat/portfolio-analytics
```

Make 6 commits — one of them introduces a bug:

```bash
echo "def total_value(holdings):
    return sum(h['amount'] * h['price'] for h in holdings)" >> backend/services/analytics.py
git add . && git commit -m "feat(analytics): add total portfolio value calculation"

echo "def best_performer(holdings):
    return max(holdings, key=lambda h: h['change'])" >> backend/services/analytics.py
git add . && git commit -m "feat(analytics): add best performer detection"

echo "def worst_performer(holdings):
    return min(holdings, key=lambda h: h['change'])" >> backend/services/analytics.py
git add . && git commit -m "feat(analytics): add worst performer detection"

# THIS IS THE BUG — dividing by 0 when portfolio is empty
echo "def average_pnl(holdings):
    total = sum(h['pnl'] for h in holdings)
    return total / len(holdings)" >> backend/services/analytics.py
git add . && git commit -m "feat(analytics): add average PnL calculation"

echo "def portfolio_diversity(holdings):
    return len(set(h['symbol'] for h in holdings))" >> backend/services/analytics.py
git add . && git commit -m "feat(analytics): add diversity score"

echo "def format_summary(holdings):
    return {'count': len(holdings), 'value': total_value(holdings)}" >> backend/services/analytics.py
git add . && git commit -m "feat(analytics): add summary formatter"
```

Check your log:
```bash
git log --oneline -6
```

### 2. Find the bug with manual bisect

The "bug" is the `average_pnl` function — it crashes with `ZeroDivisionError` on empty portfolios. Let's find it:

```bash
git bisect start
git bisect bad                  # HEAD has the bug
git bisect good HEAD~6          # 6 commits ago was fine
```

Git checks out a middle commit. Test it:
```bash
grep "len(holdings)" backend/services/analytics.py
# Found "/ len(holdings)" without zero check? → git bisect bad
# Not found? → git bisect good
```

Repeat until git says "X is the first bad commit".

```bash
git bisect reset
```

> **Learner Tip:** Bisect does a binary search — it halves the remaining commits each time. For 100 commits, it finds the bug in ~7 steps. For 6 commits, it takes 2-3 steps.

- [ ] Found the exact bad commit in 2-3 steps

### 3. Find the bug with automated bisect

```bash
git bisect start
git bisect bad HEAD
git bisect good HEAD~6
git bisect run grep -L "/ len(holdings)" backend/services/analytics.py
```

> **Learner Tip:** `grep -L` means "files that do NOT contain this pattern". So the test says: "a commit is GOOD if the file does NOT contain `/ len(holdings)`". Bisect runs this on each commit automatically.

```bash
git bisect reset
```

- [ ] Automated bisect found the bug instantly

### 4. Fix the bug and commit

```bash
# Fix the bug — replace the bad function
```

Open `backend/services/analytics.py` and fix `average_pnl`:
```python
def average_pnl(holdings):
    if not holdings:
        return 0.0
    total = sum(h['pnl'] for h in holdings)
    return total / len(holdings)
```

```bash
git add . && git commit -m "fix(analytics): handle empty portfolio in average PnL

Prevents ZeroDivisionError when portfolio has no holdings.
Found via git bisect in 3 steps across 6 commits."

git push -u origin feat/portfolio-analytics
gh pr create --title "feat: add portfolio analytics with bug fix" \
  --body "Adds analytics functions (total value, best/worst performer, PnL, diversity). Includes fix for empty portfolio edge case found via git bisect."
gh pr merge --squash --delete-branch
git checkout main && git pull
```

- [ ] Bug fixed and merged

#### SWOT — Git Bisect

| | |
|---|---|
| **Strengths** | Binary search finds any bug in O(log n) steps. Automated mode with `bisect run` is hands-free. Works on any testable condition. |
| **Weaknesses** | Requires a known "good" commit. If you don't know when it last worked, you need to guess or go far back. |
| **Opportunities** | Combine with `bisect run` and your test suite: `git bisect run pytest tests/test_analytics.py`. Fully automated bug hunting. |
| **Threats** | Commits that don't compile/build can confuse bisect. Use `git bisect skip` to skip untestable commits. |

---

## Part B: Worktree — Two Branches at Once (15 min)

### 5. Create a worktree for a hotfix

Scenario: You're working on a new feature, but a critical bug is reported. Instead of stashing, use a worktree:

```bash
# Start a feature
git checkout -b feat/export-csv
echo "import csv" >> backend/services/exporter.py
git add . && git commit -m "feat(export): start CSV export service"
```

Now a critical bug is reported! Create a worktree to fix it without leaving your feature:

```bash
git worktree add ../crypto-hotfix main
```

> **Learner Tip:** This creates a SECOND copy of the repo at `../crypto-hotfix`, checked out to `main`. You now have two working directories — one for your feature, one for the hotfix.

```bash
git worktree list
```

Should show:
```
/path/to/mn-crypto-tracker         abc1234 [feat/export-csv]
/path/to/crypto-hotfix             def5678 [main]
```

- [ ] Worktree created

### 6. Fix the bug in the worktree

Open a NEW terminal:

```bash
cd ../crypto-hotfix
git checkout -b hotfix/price-format
echo "def format_price(price):
    return f'${price:,.2f}'" >> backend/utils/helpers.py
git add . && git commit -m "fix(utils): add price formatting helper"
git push -u origin hotfix/price-format
```

Create PR from your hotfix and merge it:
```bash
gh pr create --title "fix: add price formatting" --body "Utility for consistent price display"
gh pr merge --rebase --delete-branch
```

- [ ] Hotfix merged while feature branch untouched

### 7. Continue feature in original directory

Back in your original terminal:

```bash
cd ../mn-crypto-tracker
# Still on feat/export-csv!
echo "def export_portfolio(data, filename):
    with open(filename, 'w') as f:
        writer = csv.writer(f)
        writer.writerow(['Symbol', 'Amount', 'Value'])
        for row in data:
            writer.writerow(row)" >> backend/services/exporter.py
git add . && git commit -m "feat(export): implement portfolio CSV export"
git push -u origin feat/export-csv
gh pr create --title "feat: add CSV portfolio export" --body "Export portfolio data to CSV files"
gh pr merge --squash --delete-branch
git checkout main && git pull
```

- [ ] Feature completed independently of hotfix

### 8. Clean up the worktree

```bash
git worktree remove ../crypto-hotfix
git worktree list    # Back to 1
```

- [ ] Worktree removed cleanly

#### SWOT — Git Worktree

| | |
|---|---|
| **Strengths** | Work on two branches simultaneously without stashing. Each worktree is a full working directory. No context-switching overhead. |
| **Weaknesses** | Uses more disk space (full copy of repo). Can't checkout the same branch in two worktrees. |
| **Opportunities** | Review a PR in one worktree while coding in another. Run tests on main while developing a feature. |
| **Threats** | Forgetting to clean up worktrees. Run `git worktree list` regularly and remove unused ones. |

---

## Phase 3: Release v0.4.0 (5 min)

### 9. Tag and release

```bash
git tag -a v0.4.0 -m "v0.4.0 - Portfolio Analytics + CSV Export"
git push origin v0.4.0

gh release create v0.4.0 \
  --title "v0.4.0 — Analytics & Export" \
  --notes "## What's New
- Portfolio analytics (total value, best/worst performer, PnL, diversity)
- CSV portfolio export
- Price formatting utility

## Bug Fixes
- Fixed ZeroDivisionError on empty portfolio (found via git bisect)

## Git Skills Practiced
- git bisect (manual + automated)
- git worktree (parallel development)"
```

- [ ] v0.4.0 released

---

## Self-Check

- [ ] Found a bug with manual bisect
- [ ] Found a bug with automated `bisect run`
- [ ] Created and used a worktree
- [ ] Fixed a hotfix in worktree while feature was in progress
- [ ] Cleaned up worktree
- [ ] v0.4.0 released

---

## Command Explanation Reference

| Command | What It Does | When to Use |
|---------|-------------|-------------|
| `git bisect start` | Begin binary search for bad commit | Starting a bug hunt |
| `git bisect bad` | Mark current commit as having the bug | During bisect |
| `git bisect good <hash>` | Mark a commit as bug-free | Setting the known-good boundary |
| `git bisect run <cmd>` | Automate bisect with a test command | When you have an automated test |
| `git bisect skip` | Skip untestable commit | Commit doesn't compile |
| `git bisect reset` | End bisect, return to original state | After finding the bug |
| `git worktree add <path> <branch>` | Create second working directory | Parallel development |
| `git worktree list` | Show all worktrees | Check what's active |
| `git worktree remove <path>` | Delete a worktree | Cleanup after use |

---

## ASCII Workflow — Day 12 Visual Summary

```
                        DAY 12 OVERVIEW
  ┌──────────────────────────────────────────────────────────┐
  │                                                          │
  │  GIT BISECT — Binary Search for Bugs                     │
  │  ══════════════════════════════════                       │
  │                                                          │
  │  6 commits, 1 has the bug:                               │
  │  ○──○──○──●──○──○                                        │
  │  1  2  3  BUG 5  6                                       │
  │                                                          │
  │  Step 1: bisect start, bad HEAD, good HEAD~6             │
  │  Step 2: git checks out commit 3 → test → good           │
  │  Step 3: git checks out commit 5 → test → bad            │
  │  Step 4: git checks out commit 4 → test → BAD!           │
  │                                                          │
  │  Result: "commit 4 is the first bad commit"              │
  │  Found in 3 steps out of 6 commits!                      │
  │                                                          │
  │  For 100 commits → ~7 steps                              │
  │  For 1000 commits → ~10 steps                            │
  │                                                          │
  ├──────────────────────────────────────────────────────────┤
  │                                                          │
  │  GIT WORKTREE — Parallel Development                     │
  │  ═════════════════════════════════                        │
  │                                                          │
  │  mn-crypto-tracker/        crypto-hotfix/                │
  │  ┌──────────────────┐     ┌──────────────────┐          │
  │  │ feat/export-csv  │     │ main             │          │
  │  │                  │     │                  │          │
  │  │ Working on       │     │ Fix critical     │          │
  │  │ CSV export...    │     │ bug here...      │          │
  │  │                  │     │                  │          │
  │  └──────────────────┘     └──────────────────┘          │
  │         Terminal 1              Terminal 2               │
  │                                                          │
  │  Both share the same .git — branches are synced!         │
  │  No stashing needed. No context switching.               │
  │                                                          │
  │  Cleanup: git worktree remove ../crypto-hotfix           │
  │                                                          │
  └──────────────────────────────────────────────────────────┘
```

**Next → [Day 13: Final Challenge — v1.0.0 Production Release](day-13-final-challenge.md)**
