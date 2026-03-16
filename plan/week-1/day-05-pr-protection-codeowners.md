# Day 05 — PR Template + Branch Protection + CODEOWNERS

**Time:** 30 min | **Repo:** `Spotify-lyric-tracker` (push to GitHub today)

---

## 1. PR Template (10 min)

```bash
cd ~/path/to/Spotify-lyric-tracker
git checkout -b chore/github-setup
mkdir -p .github
```

Create `.github/pull_request_template.md`:

```markdown
## Description
<!-- What changed and why? -->

## Type of Change
- [ ] Bug fix
- [ ] New feature
- [ ] Breaking change
- [ ] Docs / Refactor

## Testing Done
- [ ] Manual testing completed
- [ ] Edge cases tested

## Checklist
- [ ] Self-review completed
- [ ] No debug statements left
- [ ] No hardcoded secrets
```

- [ ] File created

## 2. CODEOWNERS (5 min)

Create `.github/CODEOWNERS`:

```
# Default owner
*               @QubitPro

# GitHub config
.github/        @QubitPro

# Styles
*.css           @QubitPro

# Scripts
*.js            @QubitPro
```

- [ ] File created

## 3. Commit & Push

```bash
git add .github/
git commit -m "chore(github): add PR template and CODEOWNERS"
git push -u origin chore/github-setup
```

Create a PR on GitHub and merge it.

- [ ] PR merged with template auto-filled

## 4. Branch Protection (10 min)

On GitHub → **Settings** → **Branches** → **Add rule**:

```
Branch name pattern: main

[x] Require pull request before merging
[x] Require conversation resolution before merging
[x] Do not allow bypassing the above settings
[ ] Allow force pushes: NO
[ ] Allow deletions: NO
```

- [ ] Rule created

Test it:
```bash
git checkout main && git pull
echo "test" >> test.txt && git add . && git commit -m "test: direct push"
git push origin main
```

- [ ] Push REJECTED (this is correct!)

```bash
git undo    # Undo the test commit
```

## 5. Verify CODEOWNERS

```bash
git checkout -b test/codeowners
echo "/* test */" >> test.css
git add . && git commit -m "test: verify CODEOWNERS"
git push -u origin test/codeowners
```

Create PR → check if you're auto-assigned as reviewer.

- [ ] Auto-assigned as reviewer

---

## Self-Check

- [ ] PR template auto-fills on new PRs
- [ ] CODEOWNERS auto-assigns reviewers
- [ ] Direct push to main is blocked
- [ ] Force push to main is blocked

**Next → [Day 06: GitHub Actions + Git Hooks](day-06-ci-and-hooks.md)**
