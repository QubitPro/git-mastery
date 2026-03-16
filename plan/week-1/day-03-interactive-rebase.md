# Day 03 — Interactive Rebase

**Goal:** Rewrite messy commit history into clean, professional history before anyone sees it.

---

## Why This Matters

Your practice branch from Day 1-2 probably looks like this:
```
a1b2c3d docs(code): add inline comment explaining sync logic
b2c3d4e style(html): fix indentation in popup template
c3d4e5f chore(config): add node_modules to gitignore
d4e5f6g feat(lyrics): add placeholder for lyrics caching
e5f6g7h fix(readme): correct installation command typo
f6g7h8i chore(config): add git aliases to workflow
```

6 tiny commits. For a PR, this should be **1-2 clean commits**. That's what rebase does.

---

## The Commands

```bash
git rebase -i HEAD~N    # Rebase last N commits
```

This opens an editor showing your commits. You can:
| Command | What It Does |
|---------|-------------|
| `pick` | Keep commit as-is |
| `squash` (or `s`) | Merge into previous commit, combine messages |
| `fixup` (or `f`) | Merge into previous commit, DISCARD this message |
| `reword` (or `r`) | Keep commit but edit the message |
| `drop` (or `d`) | Delete the commit entirely |
| `edit` (or `e`) | Pause here so you can make changes |

---

## Hands-On Exercise

### Exercise 1: Create Messy History (On Purpose)

Create a new practice branch:
```bash
git checkout main
git checkout -b practice/rebase-exercise
```

Make 6 sloppy commits intentionally:
```bash
# Commit 1
echo "line 1" >> rebase-test.txt
git add rebase-test.txt
git commit -m "add file"

# Commit 2
echo "line 2" >> rebase-test.txt
git add rebase-test.txt
git commit -m "WIP"

# Commit 3
echo "line 3" >> rebase-test.txt
git add rebase-test.txt
git commit -m "more stuff"

# Commit 4
echo "line 4" >> rebase-test.txt
git add rebase-test.txt
git commit -m "typo fix"

# Commit 5
echo "line 5" >> rebase-test.txt
git add rebase-test.txt
git commit -m "forgot this"

# Commit 6
echo "line 6" >> rebase-test.txt
git add rebase-test.txt
git commit -m "done i think"
```

Check the mess:
```bash
git log --oneline -6
```
- [ ] You should see 6 ugly commits

### Exercise 2: Squash Into One Clean Commit

```bash
git rebase -i HEAD~6
```

Your editor opens with:
```
pick abc1234 add file
pick def5678 WIP
pick ghi9012 more stuff
pick jkl3456 typo fix
pick mno7890 forgot this
pick pqr1234 done i think
```

Change it to:
```
pick abc1234 add file
squash def5678 WIP
squash ghi9012 more stuff
squash jkl3456 typo fix
squash mno7890 forgot this
squash pqr1234 done i think
```

(Change `pick` to `squash` on lines 2-6. Keep `pick` on line 1.)

Save and close. A new editor opens — write a clean message:
```
feat(test): add rebase exercise file

- Created test file with 6 lines
- Practiced interactive rebase squashing
```

Save and close.

```bash
git log --oneline -3
```

- [ ] You should see ONE clean commit instead of 6

### Exercise 3: Reword a Commit Message

Make 3 commits:
```bash
echo "A" >> reword-test.txt && git add . && git commit -m "bad mesage with typo"
echo "B" >> reword-test.txt && git add . && git commit -m "another commit"
echo "C" >> reword-test.txt && git add . && git commit -m "third commit"
```

Fix the typo in the first message:
```bash
git rebase -i HEAD~3
```

Change only the first line:
```
reword abc1234 bad mesage with typo
pick def5678 another commit
pick pqr1234 third commit
```

Save. Editor opens — fix the message:
```
docs(test): add reword practice file
```

Save and close.

```bash
git log --oneline -3
```

- [ ] First commit now has the corrected message

### Exercise 4: Drop a Commit

Make 3 commits, one is garbage:
```bash
echo "keep this" >> drop-test.txt && git add . && git commit -m "feat: useful change"
echo "GARBAGE" >> drop-test.txt && git add . && git commit -m "debug: temporary garbage"
echo "keep this too" >> drop-test.txt && git add . && git commit -m "feat: another useful change"
```

Remove the garbage commit:
```bash
git rebase -i HEAD~3
```

```
pick abc1234 feat: useful change
drop def5678 debug: temporary garbage
pick pqr1234 feat: another useful change
```

- [ ] The garbage commit is gone from history

### Exercise 5: Rebase Your Day 1 Practice Branch

NOW do it for real. Go to your practice branch from Day 1:
```bash
git checkout practice/semantic-commits
git log --oneline
```

Squash all your practice commits into 1-2 clean commits:
```bash
git rebase -i HEAD~<number-of-commits>
```

- [ ] Your practice branch now has 1-2 clean commits instead of many small ones

---

## DANGER Rules

- **NEVER** rebase commits that are already pushed and shared with others
- **ONLY** rebase YOUR local/feature branches before merging
- If something goes wrong: `git rebase --abort` cancels everything
- If you REALLY mess up: `git reflog` can save you (Day 06 topic)

---

## Self-Check

- [ ] I squashed 6 commits into 1
- [ ] I reworded a commit message
- [ ] I dropped a commit from history
- [ ] I rebased my Day 1 practice branch
- [ ] I understand when NOT to rebase (shared branches)

---

## Cleanup

```bash
git checkout main
# Keep practice branches — we'll use them later
```

**Done? Move to [Day 04 — Stash with Purpose](day-04-stash-with-purpose.md)**
