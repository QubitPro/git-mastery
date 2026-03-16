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

- [ ] 6 ugly commits visible

### 2. Squash all into one

```bash
git rebase -i HEAD~6
```

Change to:
```
pick abc1234 add file
squash def5678 WIP
squash ghi9012 more stuff
squash jkl3456 typo fix
squash mno7890 forgot this
squash pqr1234 done i think
```

New message:
```
feat(test): add rebase exercise file
```

```bash
git log --oneline -3
```

- [ ] 6 commits → 1 clean commit

### 3. Practice reword

```bash
echo "A" >> rw.txt && git add . && git commit -m "bad mesage typo"
echo "B" >> rw.txt && git add . && git commit -m "second commit"
```

```bash
git rebase -i HEAD~2
```

Change first line to `reword`, fix the message to:
```
docs(test): add reword practice file
```

- [ ] Typo fixed in commit message

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

- [ ] Garbage commit removed from history

### 5. Rebase your Day 01 branch

```bash
git checkout practice/semantic-commits
git log --oneline
git rebase -i HEAD~<count>
# Squash into 1-2 clean commits
```

- [ ] Day 01 practice branch is now clean

---

## Safety Rules

- **NEVER** rebase commits already pushed + shared
- If things break: `git rebase --abort`
- If really broken: `git reflog` (Day 04)

---

## Self-Check

- [ ] Squashed multiple commits into one
- [ ] Reworded a commit message
- [ ] Dropped a commit
- [ ] Cleaned up Day 01 branch

**Next → [Day 03: Stash + Cherry-Pick](day-03-stash-and-cherrypick.md)**
