# Day 02 — Interactive Rebase

**Time:** 30 min | **Repo:** `Spotify-lyric-tracker`

---

## Commands

```bash
git rebase -i HEAD~N    # Rebase last N commits
```

| Command | Does |
|---------|------|
| `pick` | Keep as-is |
| `squash` / `s` | Merge into previous, combine messages |
| `fixup` / `f` | Merge into previous, discard this message |
| `reword` / `r` | Edit the message |
| `drop` / `d` | Delete commit |

---

## Do This Now

### 1. Create messy history on purpose

```bash
git checkout main
git checkout -b practice/rebase
```

```bash
echo "1" >> rebase.txt && git add . && git commit -m "add file"
echo "2" >> rebase.txt && git add . && git commit -m "WIP"
echo "3" >> rebase.txt && git add . && git commit -m "more stuff"
echo "4" >> rebase.txt && git add . && git commit -m "typo fix"
echo "5" >> rebase.txt && git add . && git commit -m "forgot this"
echo "6" >> rebase.txt && git add . && git commit -m "done i think"
```

```bash
git log --oneline -6
```

> **Learner Tip:** These commit messages are intentionally bad — "WIP", "more stuff", "done i think" tell reviewers nothing. You're about to learn how to fix this.

- [ ] 6 ugly commits visible

### 2. Squash all into one

```bash
git rebase -i HEAD~6
```

Your default editor will open with a list of commits. Change it to:
```
pick abc1234 add file
squash def5678 WIP
squash ghi9012 more stuff
squash jkl3456 typo fix
squash mno7890 forgot this
squash pqr1234 done i think
```

> **Learner Tip (Editor):** If you're using Vim (the default), press `i` to enter insert mode, make your changes, then press `Esc` followed by `:wq` to save and exit. If you're stuck, press `Esc` then `:q!` to quit without saving.

A second editor window opens with all 6 messages combined. Clear everything and write one clean message:
```
feat(test): add rebase exercise file
```

```bash
git log --oneline -3
```

- [ ] 6 commits → 1 clean commit

#### SWOT — Squash

| | |
|---|---|
| **Strengths** | Turns messy "work-in-progress" history into one clean, meaningful commit. Makes `git log` readable. Essential before merging feature branches. |
| **Weaknesses** | You lose the granular history of how you got there. If the squashed commit introduces a bug, you can't `git bisect` to the exact small change. |
| **Opportunities** | Most teams require squash-before-merge. Master this and your PRs will always look professional. |
| **Threats** | Squashing commits that are already pushed and shared with others will cause conflicts for your teammates. Only squash LOCAL unpushed commits. |

### 3. Practice reword

```bash
echo "A" >> rw.txt && git add . && git commit -m "bad mesage typo"
echo "B" >> rw.txt && git add . && git commit -m "second commit"
```

```bash
git rebase -i HEAD~2
```

Change the first line from `pick` to `reword`, save and close. The editor reopens — fix the message to:
```
docs(test): add reword practice file
```

> **Learner Tip:** `reword` only changes the commit MESSAGE, not the code. The second commit (`second commit`) stays untouched because we left it as `pick`.

- [ ] Typo fixed in commit message

#### SWOT — Reword

| | |
|---|---|
| **Strengths** | Fix typos, add missing context, or convert to semantic format — without touching any code. |
| **Weaknesses** | Like all rebase operations, it rewrites commit hashes. The commit gets a new SHA even though only the message changed. |
| **Opportunities** | Clean up messages right before opening a PR. Reviewers judge your commit messages — make them count. |
| **Threats** | Don't reword pushed commits on shared branches. Use `reword` on local/unpushed work only. |

### 4. Practice drop

```bash
echo "good" >> drop.txt && git add . && git commit -m "feat: useful"
echo "JUNK" >> drop.txt && git add . && git commit -m "debug: garbage"
echo "good2" >> drop.txt && git add . && git commit -m "feat: more useful"
```

```bash
git rebase -i HEAD~3
# Change middle line to: drop
```

> **Learner Tip (Merge Conflicts):** Dropping the middle commit may cause a **merge conflict** because the 3rd commit was built on top of the 2nd. If this happens:
> 1. Open the conflicted file (look for `<<<<<<<` markers)
> 2. Keep only the lines you want (remove the markers, `=======`, and unwanted lines)
> 3. Run `git add <file>` then `git rebase --continue`
>
> On Windows, use `type <file>` or open in VS Code (`code <file>`) to view the conflict — `cat` doesn't work on Windows.

- [ ] Garbage commit removed from history

#### SWOT — Drop

| | |
|---|---|
| **Strengths** | Completely erases a commit from history as if it never happened. Perfect for removing debug code, secrets accidentally committed, or junk commits. |
| **Weaknesses** | If later commits depend on the dropped commit's changes, you WILL get merge conflicts. Resolving these requires understanding what each commit changed. |
| **Opportunities** | Combine with `squash` for a powerful cleanup workflow: drop the junk, squash the rest. |
| **Threats** | Dropping the wrong commit can silently remove important code. Always `git log -p` to check what a commit contains before dropping it. |

### 5. Rebase your Day 01 branch

```bash
git checkout practice/semantic-commits
git log --oneline
```

Count how many commits are above `main`, then squash them:

```bash
git rebase -i main
```

> **Learner Tip:** Using `git rebase -i main` instead of `HEAD~N` is easier — it automatically selects all commits on your branch that aren't on `main`. No counting needed.

Change all lines except the first to `squash`, then write a clean message:
```
feat: day 01 semantic commits practice
```

- [ ] Day 01 practice branch is now clean

### 6. Push your branches to GitHub

```bash
git push -u origin practice/semantic-commits
git checkout practice/rebase
git push -u origin practice/rebase
```

> **Learner Tip:** If you already pushed before rebasing, you'll need `git push --force` because rebase rewrites history. This is safe on YOUR practice branches, but **never force-push shared branches**.

- [ ] Both branches visible on GitHub

---

## Safety Rules

- **NEVER** rebase commits already pushed + shared with teammates
- If things break mid-rebase: `git rebase --abort` (returns to the state before you started)
- If really broken after rebase: `git reflog` to find your old commits (Day 04)
- When in doubt, create a backup branch first: `git branch backup-before-rebase`

---

## Self-Check

- [ ] Squashed multiple commits into one
- [ ] Reworded a commit message
- [ ] Dropped a commit
- [ ] Handled a merge conflict during rebase
- [ ] Cleaned up Day 01 branch
- [ ] Pushed branches to GitHub

---

## SWOT Analysis — Interactive Rebase (Overall)

### Strengths
- **Total history control** — Rewrite, combine, reorder, or remove commits before sharing your work
- **Professional PRs** — Clean, logical commit history makes code review faster and more effective
- **Undo mistakes** — Fix typos, remove debug commits, combine WIP work — all before anyone sees it
- **Flexible** — One command (`git rebase -i`) handles squash, reword, drop, reorder, and fixup

### Weaknesses
- **Rewrites history** — Every rebased commit gets a new SHA hash, which breaks references for anyone who pulled the old commits
- **Merge conflicts** — Dropping or reordering commits can cause conflicts that require manual resolution
- **Steep learning curve** — The interactive editor can be intimidating, and mistakes feel scary
- **No undo button** — Once you complete a rebase, the old commits are gone from the branch (recoverable via `reflog`, but not obvious)

### Opportunities
- **Team standard** — Many teams require "squash and rebase" workflow. This skill is expected at senior level
- **Open source** — Maintainers reject PRs with messy history. Clean rebased branches get merged faster
- **Automation** — GitHub's "Squash and merge" button does a basic version of this, but knowing interactive rebase gives you full control
- **Combined with reflog** — Rebase fearlessly knowing Day 04's `reflog` can recover anything

### Threats
- **Force-push disasters** — Rebasing a shared branch then force-pushing overwrites teammates' work. This is the #1 Git mistake in teams
- **Over-cleaning** — Squashing everything into one massive commit makes `git bisect` useless for debugging
- **Lost context** — Aggressive squashing can erase the "journey" of how a feature was built, which is sometimes valuable

---

## Command Explanation Reference

| Command | What It Does | When to Use |
|---------|-------------|-------------|
| `git rebase -i HEAD~N` | Opens interactive rebase for the last N commits | When you know exactly how many commits to edit |
| `git rebase -i main` | Interactive rebase for all commits on your branch above `main` | Cleaning up a feature branch before merging — no counting needed |
| `git rebase --abort` | Cancels an in-progress rebase and restores everything | When things go wrong mid-rebase and you want to start over |
| `git rebase --continue` | Resumes rebase after resolving a merge conflict | After fixing conflicts and running `git add` |
| `git log --oneline` | Shows commit history, one line per commit | Quick overview of your branch history |
| `git log -p` | Shows commits with full code diffs | Check what a commit actually changed before dropping it |
| `git branch backup` | Creates a safety copy of your current branch | Before risky rebase operations, just in case |
| `git push --force` | Overwrites the remote branch with your local version | After rebasing a branch you already pushed (YOUR branches only!) |
| `git reflog` | Shows every position HEAD has been at, including "lost" commits | Recovering from a rebase gone wrong (covered in Day 04) |

> **Key Insight:** Interactive rebase is about **crafting your story**. Your commits are a narrative of what you built and why. Rebase lets you edit that narrative before sharing it — remove the false starts, combine the small steps, and present clean, logical changes.

---

## ASCII Workflow — Day 02 Visual Summary

```
                        DAY 02 OVERVIEW
  ┌─────────────────────────────────────────────────────────┐
  │                                                         │
  │   SQUASH — Combine messy commits into one               │
  │   ═══════                                               │
  │                                                         │
  │   BEFORE:                        AFTER:                 │
  │   ┌──────────────────┐           ┌──────────────────┐   │
  │   │ ○ done i think   │           │                  │   │
  │   │ ○ forgot this    │           │                  │   │
  │   │ ○ typo fix       │ squash   │                  │   │
  │   │ ○ more stuff     │────────▶ │ ● feat(test):    │   │
  │   │ ○ WIP            │           │   add rebase     │   │
  │   │ ○ add file       │           │   exercise file  │   │
  │   └──────────────────┘           └──────────────────┘   │
  │   6 ugly commits                 1 clean commit         │
  │                                                         │
  ├─────────────────────────────────────────────────────────┤
  │                                                         │
  │   REWORD — Fix a commit message                         │
  │   ═══════                                               │
  │                                                         │
  │   ○ "bad mesage typo"  ──reword──▶  ○ "docs(test):     │
  │   ○ "second commit"                   add reword        │
  │                                       practice file"    │
  │                                     ○ "second commit"   │
  │                                                         │
  │   Only the MESSAGE changes. Code stays the same.        │
  │                                                         │
  ├─────────────────────────────────────────────────────────┤
  │                                                         │
  │   DROP — Delete a commit from history                   │
  │   ════                                                  │
  │                                                         │
  │   BEFORE:                        AFTER:                 │
  │   ┌──────────────────┐           ┌──────────────────┐   │
  │   │ ○ feat: more     │           │ ○ feat: more     │   │
  │   │ ○ debug: garbage │──drop──▶ │   (gone!)         │   │
  │   │ ○ feat: useful   │           │ ○ feat: useful   │   │
  │   └──────────────────┘           └──────────────────┘   │
  │                                                         │
  │   ⚠ May cause MERGE CONFLICTS if later commits         │
  │     depend on the dropped commit's changes!             │
  │                                                         │
  ├─────────────────────────────────────────────────────────┤
  │                                                         │
  │   THE INTERACTIVE REBASE EDITOR                         │
  │   ═════════════════════════════                          │
  │                                                         │
  │   git rebase -i HEAD~6  opens:                          │
  │   ┌──────────────────────────────────────┐              │
  │   │ pick   abc1234 add file              │              │
  │   │ squash def5678 WIP          ◀── change these        │
  │   │ squash ghi9012 more stuff            │              │
  │   │ squash jkl3456 typo fix              │              │
  │   │ squash mno7890 forgot this           │              │
  │   │ squash pqr1234 done i think          │              │
  │   └──────────────────────────────────────┘              │
  │                                                         │
  │   pick ── keep as-is      reword ── edit message        │
  │   squash ─ merge up       fixup ─── merge, drop msg     │
  │   drop ── delete          edit ──── pause to modify     │
  │                                                         │
  ├─────────────────────────────────────────────────────────┤
  │                                                         │
  │   SAFETY NET                                            │
  │   ══════════                                            │
  │                                                         │
  │   Something went wrong?                                 │
  │   ┌────────────────────────┐                            │
  │   │ git rebase --abort     │ ◀── undo everything        │
  │   └────────────────────────┘                            │
  │                                                         │
  │   Already finished and regret it?                       │
  │   ┌────────────────────────┐                            │
  │   │ git reflog             │ ◀── find old commits       │
  │   └────────────────────────┘     (Day 04)               │
  │                                                         │
  │   About to do something risky?                          │
  │   ┌────────────────────────────────────┐                │
  │   │ git branch backup-before-rebase    │ ◀── save copy  │
  │   └────────────────────────────────────┘                │
  │                                                         │
  │   ⚠ NEVER rebase commits already pushed + shared!       │
  │                                                         │
  └─────────────────────────────────────────────────────────┘
```

**Next → [Day 03: Stash + Cherry-Pick](day-03-stash-and-cherrypick.md)**
