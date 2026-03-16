# Day 09 — Branch Protection Rules

**Goal:** Prevent direct pushes to main. Force all changes through PRs with checks.

---

## Why This Matters

Without protection, anyone (including you) can:
- Push broken code directly to main
- Force-push and destroy history
- Skip code review entirely

Elite teams NEVER allow direct pushes to main. Every change goes through a PR.

---

## Hands-On Exercise

### Exercise 1: Enable Branch Protection on GitHub

1. Go to your `Spotify-lyric-tracker` repo on GitHub
2. Click **Settings** → **Branches** (left sidebar)
3. Click **"Add branch protection rule"** (or "Add rule")
4. Set:

```
Branch name pattern: main

Enable these:
[x] Require a pull request before merging
    [x] Require approvals: 1  (set to 0 if solo project)
    [x] Dismiss stale pull request approvals when new commits are pushed

[x] Require status checks to pass before merging
    (you'll add CI in Day 11 — skip for now if no checks exist)

[x] Require conversation resolution before merging

[ ] Require signed commits (optional, advanced)

[x] Do not allow bypassing the above settings
    (even YOU can't skip rules — this is discipline)

Under "Rules applied to everyone including administrators":
[x] Allow force pushes: NO (unchecked)
[x] Allow deletions: NO (unchecked)
```

5. Click **"Create"** / **"Save changes"**

- [ ] Branch protection rule created for `main`

### Exercise 2: Test That It Works

Try pushing directly to main:
```bash
git checkout main
echo "this should fail" >> test-protection.txt
git add . && git commit -m "test: try direct push to main"
git push origin main
```

- [ ] Push was **REJECTED** by GitHub (this is what we want!)

Now do it the right way:
```bash
# Undo the local commit
git undo

# Create a branch instead
git checkout -b test/protection-check
echo "this goes through PR" >> test-protection.txt
git add . && git commit -m "test: verify branch protection works"
git push -u origin test/protection-check
```

Then create a PR on GitHub.

- [ ] Direct push to main is blocked
- [ ] PR workflow still works

### Exercise 3: Try Force Push (Should Fail)

```bash
git checkout main
git push --force origin main
```

- [ ] Force push is **REJECTED** (your history is safe)

### Exercise 4: Document Your Rules

Add to your repo's README or a CONTRIBUTING.md:

```markdown
## Branch Rules

- `main` is protected — no direct pushes
- All changes require a Pull Request
- PRs require conversation resolution before merge
- Force pushes to main are disabled
```

- [ ] Rules documented

---

## Protection Levels (For Future Reference)

| Level | Solo Project | Small Team | Large Team |
|-------|-------------|------------|------------|
| Require PR | Yes | Yes | Yes |
| Require approvals | 0 | 1 | 2 |
| Require CI checks | Optional | Yes | Yes |
| Dismiss stale reviews | No | Yes | Yes |
| Require CODEOWNERS | No | Optional | Yes |
| Block force push | Yes | Yes | Yes |
| Include admins | Optional | Yes | Yes |

---

## Self-Check

- [ ] Main branch has protection rules enabled
- [ ] Direct push to main is blocked
- [ ] Force push to main is blocked
- [ ] I understand WHY this matters (not just how)
- [ ] I can still push via PR workflow

---

**Done? Move to [Day 10 — CODEOWNERS](day-10-codeowners.md)**
