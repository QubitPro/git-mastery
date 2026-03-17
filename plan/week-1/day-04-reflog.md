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

> **Learner Tip:** You MUST switch to another branch before deleting — git won't let you delete the branch you're standing on. If you see `cannot delete branch used by worktree`, run `git checkout main` first.

> **Learner Tip:** When `git branch -D` deletes a branch, it helpfully prints the hash: `Deleted branch important-feature (was 302477c)`. You can use that hash directly! But if you missed it, `git reflog` has it too.

- [ ] Branch deleted then fully recovered

#### SWOT — Branch Recovery

| | |
|---|---|
| **Strengths** | Deleted branches are never truly gone — reflog keeps the commits for 30 days. Recovery is one command. |
| **Weaknesses** | Only works for committed changes. Uncommitted work that wasn't stashed IS gone forever. |
| **Opportunities** | Fearless branch cleanup — delete experimental branches knowing you can always bring them back. |
| **Threats** | The 30-day expiry is real. After `git gc` runs or 30 days pass, orphaned commits are permanently removed. Don't rely on reflog as long-term backup. |

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
git reflog    # Find hash BEFORE "rebase (start)"
git reset --hard <pre-rebase-hash>
git log --oneline -3
```

> **Learner Tip (Reading reflog):** In `git reflog`, look for entries like:
> ```
> abc1234 HEAD@{3}: rebase (start): checkout HEAD~3
> def5678 HEAD@{4}: commit: feat: work 3        ◀── THIS is your pre-rebase state
> ```
> The hash on the line BEFORE `rebase (start)` is where you want to go back to.

> **Learner Tip:** `git reset --hard` moves your branch pointer AND changes your files. It's the "time machine" that takes everything back to that exact state. Powerful but destructive to current work — make sure you've committed anything you want to keep.

- [ ] Recovered all 3 commits after bad rebase

#### SWOT — Rebase Recovery

| | |
|---|---|
| **Strengths** | Complete undo of any rebase — restores all original commits exactly as they were. Makes rebase risk-free. |
| **Weaknesses** | `git reset --hard` destroys any uncommitted changes in your working directory. Commit or stash first. |
| **Opportunities** | Experiment freely with rebase knowing you can always get back. Try aggressive squashing, reordering — learn by doing. |
| **Threats** | If you `reset --hard` to the wrong hash, you lose your rebase result. Always double-check the hash in reflog before resetting. |

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

> **Learner Tip:** `HEAD~2` means "2 commits before where I am now". After `reset --hard HEAD~2`, your branch looks like those 2 commits never existed — but reflog remembers.

> **Learner Tip (Common mistake):** Make sure you reset to the CORRECT direction. If you want to recover commits, reset FORWARD to the newer hash. If you accidentally reset forward instead of backward (or vice versa), just check reflog again and reset to the right one.

- [ ] Recovered from `git reset --hard`

#### SWOT — Hard Reset Recovery

| | |
|---|---|
| **Strengths** | Even the most destructive git command (`reset --hard`) is recoverable through reflog. Nothing committed is truly lost. |
| **Weaknesses** | Only recovers committed changes. Any unstaged or uncommitted modifications at the time of reset are gone permanently. |
| **Opportunities** | This knowledge removes the fear of `reset --hard`. Use it confidently for cleanup, knowing reflog is your backup. |
| **Threats** | Multiple resets in a row make the reflog harder to read. Take it slow — one recovery at a time. |

### 4. Browse old state safely

```bash
git reflog
git checkout <any-old-hash>    # Detached HEAD — just viewing
# Look around...
git checkout main              # Back to safety
```

> **Learner Tip (Detached HEAD):** Git will show a scary warning about "detached HEAD state". This just means you're viewing an old snapshot, not on any branch. You can look at files, run tests, even make commits — but when you `checkout main`, you're back to normal. Think of it as "read-only time travel".

> **Learner Tip:** If you accidentally make commits in detached HEAD and want to keep them, create a branch before leaving: `git checkout -b save-my-work`. Then those commits are safe on a real branch.

- [ ] Explored old state without breaking anything

#### SWOT — Detached HEAD Browsing

| | |
|---|---|
| **Strengths** | Safe way to inspect any historical state without changing anything. Perfect for "what did this look like last week?" |
| **Weaknesses** | The "detached HEAD" warning scares beginners. Commits made here are orphaned unless you create a branch. |
| **Opportunities** | Debug by checking out the last known working commit. Compare old vs new code visually. |
| **Threats** | Making commits in detached HEAD then switching away — those commits become invisible (recoverable via reflog, but easy to forget). |

---

## Reflog Cheat Sheet

| Disaster | Fix |
|----------|-----|
| Deleted branch | `git branch <name> <reflog-hash>` |
| Bad rebase | `git reset --hard <pre-rebase-hash>` |
| Bad reset | `git reset --hard <lost-commit-hash>` |
| Lost commits | `git cherry-pick <reflog-hash>` |
| Bad merge | `git reset --hard <pre-merge-hash>` |
| Detached HEAD commits | `git branch save-work <reflog-hash>` |

---

## Self-Check

- [ ] Recovered deleted branch
- [ ] Recovered from bad rebase
- [ ] Recovered from hard reset
- [ ] Browsed old state in detached HEAD
- [ ] I feel confident I can't permanently lose committed work

---

## SWOT Analysis — Day 04 Overall

### Strengths
- **Ultimate safety net** — Reflog makes almost every git mistake recoverable. Deleted branches, bad rebases, accidental resets — all fixable
- **30-day history** — Every HEAD movement is logged, giving you a detailed timeline of everything you've done
- **Builds confidence** — Knowing reflog exists means you can use powerful commands (rebase, reset, branch -D) without fear
- **Simple interface** — `git reflog` + `git reset --hard <hash>` solves 90% of recovery scenarios

### Weaknesses
- **Uncommitted work is NOT saved** — Reflog only tracks commits. Unstaged changes lost to `reset --hard` are gone forever
- **Local only** — Reflog is per-machine. It doesn't sync to GitHub. New clone = empty reflog
- **Expires after 30 days** — Unreferenced commits are garbage collected. Not a permanent backup
- **Noisy output** — Active repos generate long reflogs. Finding the right hash takes practice

### Opportunities
- **Fearless experimentation** — Try any git operation knowing you have a 30-day undo button
- **Team rescue** — Help teammates recover from their git disasters. This is a rare, valuable skill
- **Debugging tool** — Trace exactly when and how a branch changed. Useful for "who did what when?"
- **Interview differentiator** — Most developers don't know reflog exists. Demonstrating this knowledge stands out

### Threats
- **False sense of security** — Relying on reflog instead of proper commits and pushes. Reflog is a safety net, not a workflow
- **`git gc` can remove entries** — Running garbage collection prunes old reflog entries. Usually automatic, but manual `git gc --prune=now` is destructive
- **Resetting to wrong hash** — In a long reflog, picking the wrong hash can make things worse. Always verify with `git log <hash>` before resetting
- **Doesn't help with pushed mistakes** — If bad code is already pushed to GitHub, reflog can fix your local copy but the remote still has the bad version

---

## Command Explanation Reference

| Command | What It Does | When to Use |
|---------|-------------|-------------|
| `git reflog` | Shows a log of every position HEAD has been at, with hashes and descriptions | First step in any recovery — find the hash you need |
| `git reflog show <branch>` | Shows reflog for a specific branch instead of HEAD | When you need history of a particular branch |
| `git reset --hard <hash>` | Moves branch pointer AND working directory to the specified commit | Full recovery — go back to an exact state (destroys uncommitted work!) |
| `git reset --soft <hash>` | Moves branch pointer but keeps all changes staged | When you want to undo commits but keep the code changes |
| `git reset --mixed <hash>` | Moves branch pointer, keeps changes but unstages them (default) | When you want to undo commits and re-stage selectively |
| `git branch <name> <hash>` | Creates a new branch pointing at the specified commit | Recovering a deleted branch — create it again from the reflog hash |
| `git branch -D <name>` | Force-deletes a branch (even if not merged) | Cleanup — safe because reflog can recover it |
| `git checkout <hash>` | Enters detached HEAD mode at that commit | Safely browse an old state without changing any branch |
| `git cherry-pick <hash>` | Copies a specific commit to your current branch | Recovering individual lost commits without resetting |
| `git log <hash>` | Shows commit history starting from that hash | Verify a reflog hash is the right one before resetting to it |
| `git gc` | Runs garbage collection on the repository | Usually automatic — be careful with `--prune=now` as it removes reflog entries |

### The Three Resets Compared

```
git reset --soft HEAD~1   → undo commit, keep changes STAGED
git reset --mixed HEAD~1  → undo commit, keep changes UNSTAGED (default)
git reset --hard HEAD~1   → undo commit, DELETE changes completely
```

> **Key Insight:** Reflog is git's "undo history" — like Ctrl+Z for your entire repository. The rule is simple: **if you committed it, reflog can find it.** This is why you should commit early and commit often, even messy commits. You can always clean up with rebase (Day 02), but you can't recover what was never committed.

---

## ASCII Workflow — Day 04 Visual Summary

```
                        DAY 04 OVERVIEW
  ┌─────────────────────────────────────────────────────────┐
  │                                                         │
  │   WHAT IS REFLOG?                                       │
  │   ═══════════════                                       │
  │                                                         │
  │   Every time HEAD moves, git records it:                │
  │                                                         │
  │   git reflog output:                                    │
  │   ┌──────────────────────────────────────────────┐      │
  │   │ 302477c HEAD@{0}: checkout: to main          │      │
  │   │ 302477c HEAD@{1}: commit: feat: more critical│      │
  │   │ 56b9c52 HEAD@{2}: commit: feat: critical     │      │
  │   │ ca75307 HEAD@{3}: checkout: to important-feat│      │
  │   │ ca75307 HEAD@{4}: commit: feat: feature ver  │      │
  │   │ ...                                          │      │
  │   └──────────────────────────────────────────────┘      │
  │       ▲                                                 │
  │       └── Every hash is a recovery point!               │
  │                                                         │
  ├─────────────────────────────────────────────────────────┤
  │                                                         │
  │   RECOVER DELETED BRANCH                                │
  │   ══════════════════════                                │
  │                                                         │
  │   ○──○──○  important-feature                             │
  │         │                                               │
  │         │  git branch -D                                │
  │         ▼                                               │
  │   ○──○  main  (branch gone!)                             │
  │         │                                               │
  │         │  git branch important-feature <hash>          │
  │         ▼                                               │
  │   ○──○──○  important-feature  (recovered!)               │
  │                                                         │
  ├─────────────────────────────────────────────────────────┤
  │                                                         │
  │   RECOVER FROM BAD REBASE                               │
  │   ═══════════════════════                                │
  │                                                         │
  │   BEFORE rebase:          AFTER rebase:                 │
  │   ○──○──○──○              ○──●                          │
  │      │  │  │                 │                          │
  │      w1 w2 w3            "all combined"                 │
  │                                                         │
  │   reflog shows pre-rebase hash ──▶ reset --hard         │
  │                                                         │
  │   RECOVERED:                                            │
  │   ○──○──○──○   (all 3 commits back!)                    │
  │      │  │  │                                            │
  │      w1 w2 w3                                           │
  │                                                         │
  ├─────────────────────────────────────────────────────────┤
  │                                                         │
  │   RECOVER FROM HARD RESET                               │
  │   ═══════════════════════                                │
  │                                                         │
  │   ○──○──○──○   HEAD is here                              │
  │            │                                            │
  │            │  git reset --hard HEAD~2                    │
  │            ▼                                            │
  │   ○──○         HEAD jumped back (commits "gone")         │
  │      │                                                  │
  │      │  git reset --hard <reflog-hash>                  │
  │      ▼                                                  │
  │   ○──○──○──○   HEAD restored! All commits back.          │
  │                                                         │
  ├─────────────────────────────────────────────────────────┤
  │                                                         │
  │   DETACHED HEAD (safe browsing)                         │
  │   ═════════════════════════════                          │
  │                                                         │
  │   main: ○──○──○──●  (HEAD here)                          │
  │                                                         │
  │   git checkout <old-hash>                               │
  │                                                         │
  │         ○──○──●──○   HEAD is "detached" (just viewing)   │
  │              ▲                                          │
  │              └── you are here, looking around            │
  │                                                         │
  │   git checkout main                                     │
  │                                                         │
  │   main: ○──○──○──●  (back to normal)                     │
  │                                                         │
  ├─────────────────────────────────────────────────────────┤
  │                                                         │
  │   THE THREE RESETS                                      │
  │   ════════════════                                      │
  │                                                         │
  │                    commit    staging    working dir      │
  │   --soft            undo     KEEP        KEEP           │
  │   --mixed (default) undo     undo        KEEP           │
  │   --hard            undo     undo        undo           │
  │                                                         │
  │   --soft  = "redo the commit differently"               │
  │   --mixed = "restage selectively"                       │
  │   --hard  = "nuke everything, go back in time"          │
  │                                                         │
  │   ⚠ RULE: if you committed it, reflog can find it.     │
  │     Uncommitted work lost to --hard is gone FOREVER.    │
  │                                                         │
  └─────────────────────────────────────────────────────────┘
```

**Next → [Day 05: PR Template + Branch Protection + CODEOWNERS](day-05-pr-protection-codeowners.md)**
