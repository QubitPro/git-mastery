# Day 03 — Stash + Cherry-Pick

**Time:** 30 min | **Repo:** `Spotify-lyric-tracker`

---

## Part A: Stash (Save WIP Without Committing)

### Do This Now

#### 1. Named stash

```bash
git checkout main
git checkout -b practice/stash

echo "half done feature" >> wip.txt
git add wip.txt
git stash save "WIP: lyrics caching feature"

git st                    # Clean!
git stash list            # Your stash is saved
```

> **Learner Tip:** Think of stash as a "pause button". You're working on something, your boss says "fix this bug NOW", you stash your WIP, fix the bug, then pop your stash and continue where you left off.

- [ ] Working directory clean after stash
- [ ] `git stash list` shows named stash

#### SWOT — Named Stash

| | |
|---|---|
| **Strengths** | Instantly saves your work without creating a messy WIP commit. Working directory becomes clean so you can switch branches safely. |
| **Weaknesses** | Easy to forget what you stashed — always use descriptive names with `git stash save "name"`. |
| **Opportunities** | Perfect for context-switching: stash current work → fix urgent bug → pop stash → resume. |
| **Threats** | Stashes are local only — they don't push to GitHub. If you lose your local repo, stashes are gone. For important work, commit instead. |

#### 2. Multiple stashes + selective pop

```bash
echo "ui work" >> ui.txt && git add . && git stash save "WIP: button styles"
echo "api work" >> api.txt && git add . && git stash save "WIP: API refresh"
echo "bugfix" >> fix2.txt && git add . && git stash save "WIP: null pointer fix"

git stash list
# stash@{0}: WIP: null pointer fix
# stash@{1}: WIP: API refresh
# stash@{2}: WIP: button styles

# Pop only the bugfix
git stash pop stash@{0}
git st
```

> **Learner Tip:** Stash works like a stack (LIFO — Last In, First Out). The most recent stash is always `stash@{0}`. When you pop one, the others shift down. Use `git stash list` frequently to keep track.

> **Learner Tip (pop vs apply):** `git stash pop` restores AND deletes the stash. `git stash apply` restores but KEEPS the stash. Use `apply` when you want to restore the same stash to multiple branches.

- [ ] Popped specific stash, others remain

#### SWOT — Multiple Stashes

| | |
|---|---|
| **Strengths** | Stack multiple work-in-progress items independently. Pop exactly the one you need with `stash@{N}`. |
| **Weaknesses** | Stash numbering shifts when you pop — `stash@{2}` becomes `stash@{1}`. Can be confusing with many stashes. |
| **Opportunities** | Keep separate concerns separated: UI work, API work, bugfixes all stashed independently. |
| **Threats** | Don't hoard stashes — if your stash list grows past 3-4 items, you should commit or branch instead. Old stashes become "what was this?" mysteries. |

#### 3. Stash specific files

```bash
echo "db stuff" >> db.txt
echo "css stuff" >> style.txt
git add .

git stash push db.txt -m "WIP: database schema"
git st
# style.txt still staged, db.txt stashed
```

> **Learner Tip:** `git stash push <file> -m "name"` is the modern syntax. The older `git stash save` stashes EVERYTHING. Use `push` when you only want to stash specific files and keep the rest.

- [ ] Only selected file was stashed

#### SWOT — Selective Stash

| | |
|---|---|
| **Strengths** | Stash only what you need — keep working on the rest. Surgical precision over what gets shelved. |
| **Weaknesses** | Only available with `git stash push` (newer syntax), not with the older `git stash save`. |
| **Opportunities** | Stash the risky experimental file, commit the stable files. Clean separation of concerns. |
| **Threats** | If stashed and unstashed files depend on each other, popping the stash later may cause issues. Keep related changes together. |

#### 4. Branch from stash

```bash
echo "big idea" >> idea.txt && git add . && git stash save "WIP: big feature idea"
git stash branch feature/big-idea stash@{0}
git st && git br
```

> **Learner Tip:** `git stash branch` is perfect when you realize "this stash is bigger than I thought — it deserves its own branch". It creates the branch, applies the stash, and drops it from the stash list in one command.

> **Learner Tip (Windows):** On Windows, `echo` adds double quotes to the text. This is cosmetic and doesn't affect your learning. The whitespace warnings from git are harmless.

- [ ] New branch created from stash

#### SWOT — Stash to Branch

| | |
|---|---|
| **Strengths** | One command does three things: creates branch, applies stash, deletes stash. Clean and efficient. |
| **Weaknesses** | The branch is created from the commit where you originally stashed — not from your current HEAD. Can be surprising if you've moved around. |
| **Opportunities** | Great workflow: start hacking on main → realize it's a feature → stash → branch. No work lost, proper branch created. |
| **Threats** | If the stash conflicts with the base commit, it won't apply cleanly. Resolve conflicts before committing. |

---

## Part B: Cherry-Pick (Copy Specific Commits)

#### 5. Setup

```bash
git checkout main
git checkout -b feature/cherry-test

echo "feat 1" >> cherry.txt && git add . && git commit -m "feat: part 1"
echo "BUGFIX" >> cherry.txt && git add . && git commit -m "fix: critical bug"
echo "feat 2" >> cherry.txt && git add . && git commit -m "feat: part 2"

git log --oneline -3
# Note the BUGFIX commit hash
```

> **Learner Tip (Typos kill commands):** Watch for typos like `gid` instead of `git` — if a command in a `&&` chain fails, the rest of the chain won't run, but the `echo` already happened. If this messes up your commits, use `git reset HEAD~N` to undo the last N commits (keeps files) and redo them.

#### 6. Cherry-pick the bugfix to main

```bash
git checkout main
git cherry-pick <bugfix-hash>

git log --oneline -3
```

> **Learner Tip:** Replace `<bugfix-hash>` with the actual hash from `git log` (e.g., `5f7a567`). You only need the first 7 characters.

> **Learner Tip (Conflicts here?):** If `cherry.txt` doesn't exist on main, git will show a "modify/delete" conflict. Just run `git add cherry.txt` then `git cherry-pick --continue`. This happens because the commit references a file that only exists on the feature branch.

- [ ] Main has the bugfix but NOT the feature commits
- [ ] Bugfix has a NEW hash (it's a copy)

#### SWOT — Cherry-Pick

| | |
|---|---|
| **Strengths** | Surgically copy ONE commit to another branch. Perfect for hotfixes — grab the fix without pulling unfinished feature work. |
| **Weaknesses** | Creates a duplicate commit (same changes, different hash). If you later merge the branches, git usually handles it, but it can cause confusion. |
| **Opportunities** | Emergency production fix: cherry-pick from dev to main without merging unstable features. Used heavily in release management. |
| **Threats** | Cherry-picking many commits is a code smell — if you need 5+ commits from another branch, you should probably merge or rebase instead. |

#### 7. Cherry-pick without auto-commit

```bash
git checkout -b practice/cherry-review
git cherry-pick -n <feat-part-1-hash>

git staged                # Review what was picked
git commit -m "feat: backported part 1 from feature branch"
```

> **Learner Tip:** The `-n` (or `--no-commit`) flag stages the changes but doesn't commit. This gives you a chance to review, modify, or combine with other changes before committing. Very useful when you want to tweak the cherry-picked code.

> **Learner Tip (No changes?):** If cherry-pick with `-n` results in "nothing to commit", it means the changes already exist on your branch. This can happen if you cherry-picked the same file content earlier. It's not an error — git is telling you there's nothing new to add.

- [ ] Reviewed changes before committing

#### SWOT — Cherry-Pick with `-n`

| | |
|---|---|
| **Strengths** | Full control — review and modify changes before committing. Can combine multiple cherry-picks into one commit. |
| **Weaknesses** | Extra steps vs normal cherry-pick. Easy to forget to commit after staging. |
| **Opportunities** | Backporting: cherry-pick a fix, adjust it for an older version, then commit with a proper message explaining the backport. |
| **Threats** | If you cherry-pick `-n` and then forget about it, you'll have unexplained staged changes. Always commit or reset after. |

#### 8. Handle a conflict

```bash
git checkout main
echo "main version" >> conflict.txt && git add . && git commit -m "feat: main version"

git checkout feature/cherry-test
echo "feature version" >> conflict.txt && git add . && git commit -m "feat: feature version"

git checkout main
git cherry-pick <feature-conflict-hash>
# CONFLICT! Resolve manually, then:
git add conflict.txt
git cherry-pick --continue
```

> **Learner Tip (Understanding conflicts):** A conflict means both branches changed the same file differently. Git shows you BOTH versions with markers:
> ```
> <<<<<<< HEAD          ← what's on YOUR branch (main)
> main version
> =======               ← divider
> feature version
> >>>>>>> abc1234       ← what you're cherry-picking
> ```
> YOUR job: delete the markers, keep what you want, `git add`, then `--continue`.

> **Learner Tip (VS Code):** VS Code highlights conflicts with colors and gives you buttons: "Accept Current", "Accept Incoming", "Accept Both". Use "Accept Both" when you want to keep everything. Open the file with `code conflict.txt`.

> **Learner Tip (Windows):** Use `type conflict.txt` to view file contents on Windows — `cat` doesn't work. Or just open in VS Code.

- [ ] Resolved a cherry-pick conflict

#### SWOT — Conflict Resolution

| | |
|---|---|
| **Strengths** | You have complete control over the final result. Can keep one side, both sides, or write something entirely new. |
| **Weaknesses** | Scary for beginners — the conflict markers look intimidating. Takes practice to read confidently. |
| **Opportunities** | This skill applies everywhere: merges, rebases, cherry-picks, stash pops. Learn it once, use it forever. |
| **Threats** | Resolving incorrectly can silently break code. Always test after resolving conflicts. If unsure, `git cherry-pick --abort` to start over. |

---

## Self-Check

- [ ] Named stashes, selective pop, stash specific files
- [ ] Created a branch from a stash
- [ ] Cherry-picked single commit between branches
- [ ] Cherry-picked without auto-commit (`-n`)
- [ ] Resolved a cherry-pick conflict
- [ ] Pushed branches to GitHub

---

## SWOT Analysis — Day 03 Overall

### Strengths
- **Stash = flexibility** — Never lose WIP work, never make messy "WIP" commits, switch contexts freely
- **Cherry-pick = precision** — Move exactly the commits you need, nothing more
- **Both are non-destructive** — Stash saves without committing, cherry-pick copies without moving
- **Real-world essential** — These are daily tools for professional developers, not just theory

### Weaknesses
- **Stash is local-only** — Cannot push stashes to GitHub; if your machine dies, stashes are gone
- **Cherry-pick creates duplicates** — Same changes exist as two different commits on two branches
- **Both can cause conflicts** — Popping a stash or cherry-picking can conflict with changes made since
- **Stash indexing is confusing** — Numbers shift when you pop, names help but aren't searchable

### Opportunities
- **Hotfix workflow** — Cherry-pick is the standard way to get urgent fixes to production without merging unstable code
- **Context-switching mastery** — Stash lets you handle interruptions gracefully — a key professional skill
- **Combined with rebase** — Cherry-pick commits, then rebase to clean up. Powerful branch management
- **Team collaboration** — "Can you cherry-pick commit X from my branch?" is a common team request

### Threats
- **Stash hoarding** — Developers who stash everything and never pop. Old stashes become mysterious dead code
- **Cherry-pick abuse** — Cherry-picking the same commit to 5 branches instead of using a proper merge strategy
- **Conflict fatigue** — Repeated conflicts when cherry-picking many commits. Sign you should merge instead
- **Forgetting to push** — All stash and cherry-pick work is local until you push. Don't forget!

---

## Command Explanation Reference

### Stash Commands

| Command | What It Does | When to Use |
|---------|-------------|-------------|
| `git stash save "name"` | Stashes all staged/unstaged changes with a label | Pausing current work to do something else |
| `git stash push <file> -m "name"` | Stashes only specific files | When you want to shelve one file but keep working on others |
| `git stash list` | Shows all saved stashes | Check what's on your shelf |
| `git stash pop stash@{N}` | Restores stash N and deletes it from the list | Resuming work — you're done with the stash |
| `git stash apply stash@{N}` | Restores stash N but keeps it in the list | Want to apply the same stash to multiple branches |
| `git stash drop stash@{N}` | Deletes a stash without applying it | Cleaning up old stashes you no longer need |
| `git stash clear` | Deletes ALL stashes | Nuclear option — use with caution |
| `git stash branch <name> stash@{N}` | Creates a new branch from a stash | When a stash grows into a full feature |
| `git stash show stash@{N}` | Shows what files a stash changed | Preview before popping |
| `git stash show -p stash@{N}` | Shows the full diff of a stash | Detailed preview of stashed changes |

### Cherry-Pick Commands

| Command | What It Does | When to Use |
|---------|-------------|-------------|
| `git cherry-pick <hash>` | Copies a commit to your current branch | Grabbing a specific fix or feature from another branch |
| `git cherry-pick -n <hash>` | Stages changes without committing | Want to review or modify before committing |
| `git cherry-pick --continue` | Resumes after resolving a conflict | After fixing conflicts and running `git add` |
| `git cherry-pick --abort` | Cancels the cherry-pick entirely | When conflicts are too messy and you want to start over |
| `git cherry-pick --skip` | Skips the current commit and continues | When the commit is no longer needed |
| `git cherry-pick A..B` | Cherry-picks a range of commits (A exclusive, B inclusive) | Copying multiple sequential commits |
| `git log --oneline -N` | Shows last N commits with hashes | Finding the commit hash to cherry-pick |

### Conflict Resolution Commands

| Command | What It Does | When to Use |
|---------|-------------|-------------|
| `git add <file>` | Marks a conflicted file as resolved | After manually editing the conflict markers out |
| `git cherry-pick --continue` | Finishes the cherry-pick after resolving | All conflicts resolved and staged |
| `git cherry-pick --abort` | Cancels everything, returns to pre-cherry-pick state | Conflicts are too complex, want to try a different approach |
| `code <file>` | Opens file in VS Code with conflict highlighting | Easiest way to resolve conflicts visually |
| `type <file>` | Shows file contents on Windows | Quick look at conflict markers (use instead of `cat`) |

> **Key Insight:** Stash and cherry-pick solve the same core problem from different angles: **"I need specific changes somewhere else."** Stash moves your uncommitted work aside. Cherry-pick copies committed work between branches. Together they give you total control over where your code lives.

---

## ASCII Workflow — Day 03 Visual Summary

```
                        DAY 03 OVERVIEW
  ┌─────────────────────────────────────────────────────────┐
  │                                                         │
  │   PART A: STASH                                         │
  │   ═══════════                                           │
  │                                                         │
  │   Working Dir          Stash Stack                      │
  │   ┌──────────┐         ┌──────────────────────┐         │
  │   │ wip.txt  │──save──▶│ stash@{0}: WIP cache │         │
  │   │ (dirty)  │         │ stash@{1}: WIP styles │         │
  │   └──────────┘         │ stash@{2}: WIP API    │         │
  │   ┌──────────┐         └──────────┬───────────┘         │
  │   │ (clean!) │◀──pop───────────────┘                    │
  │   └──────────┘                                          │
  │                                                         │
  │   Stash = shelf for half-done work                      │
  │   Pop = take it back off the shelf                      │
  │                                                         │
  │                                                         │
  │   Stash to Branch:                                      │
  │   ┌───────────┐    stash branch    ┌──────────────┐     │
  │   │ stash@{0} │───────────────────▶│ new branch   │     │
  │   │ "big idea"│                    │ feature/idea │     │
  │   └───────────┘                    └──────────────┘     │
  │                                                         │
  ├─────────────────────────────────────────────────────────┤
  │                                                         │
  │   PART B: CHERRY-PICK                                   │
  │   ═══════════════════                                   │
  │                                                         │
  │   feature/cherry-test:                                  │
  │   ○───○───○───○                                         │
  │       │   │   │                                         │
  │       │  fix  feat2                                     │
  │      feat1                                              │
  │             │                                           │
  │             │ cherry-pick (copy ONE commit)              │
  │             ▼                                           │
  │   main:                                                 │
  │   ○───○───●                                             │
  │           │                                             │
  │        fix (NEW hash,                                   │
  │         same changes)                                   │
  │                                                         │
  │   Cherry-pick ≠ move. It COPIES.                        │
  │   Original commit stays on feature branch.              │
  │                                                         │
  ├─────────────────────────────────────────────────────────┤
  │                                                         │
  │   CONFLICT RESOLUTION:                                  │
  │                                                         │
  │   conflict.txt on main:     conflict.txt on feature:    │
  │   ┌──────────────┐         ┌──────────────────┐         │
  │   │ main version │         │ feature version  │         │
  │   └──────────────┘         └──────────────────┘         │
  │           │                        │                    │
  │           └────────┬───────────────┘                    │
  │                    ▼                                    │
  │           ┌────────────────┐                            │
  │           │ <<<<<<< HEAD   │                            │
  │           │ main version   │  ◀── YOU decide            │
  │           │ =======        │      what to keep          │
  │           │ feature version│                            │
  │           │ >>>>>>> abc123 │                            │
  │           └────────────────┘                            │
  │                    ▼                                    │
  │           ┌────────────────┐                            │
  │           │ main version   │  ◀── After resolving       │
  │           │ feature version│                            │
  │           └────────────────┘                            │
  │                                                         │
  └─────────────────────────────────────────────────────────┘
```

**Next → [Day 04: Reflog Recovery](day-04-reflog.md)**
