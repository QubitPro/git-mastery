# Day 08 — Draft PRs + Code Review

**Time:** 30 min | **Repo:** `Spotify-lyric-tracker`

---

## Part A: Draft PRs (15 min)

### 1. Create a Draft PR

```bash
git checkout main && git pull
git checkout -b feature/dark-mode

echo "/* Dark mode */" >> dark-mode.css
git add . && git commit -m "feat(ui): add initial dark mode stylesheet"
git push -u origin feature/dark-mode
```

Using `gh`:
```bash
gh pr create --title "feat(ui): add dark mode support" \
  --body "WIP: Looking for feedback on approach before continuing" \
  --draft
```

- [ ] Draft PR created (shows "Draft" badge)
- [ ] Cannot be merged (button grayed out)

### 2. Push updates — PR updates automatically

```bash
echo ".dark a { color: #4fc3f7; }" >> dark-mode.css
git add . && git commit -m "feat(ui): add dark mode link colors"
git push
```

Add progress comment on PR:
```
- [x] Base dark colors
- [x] Link colors
- [ ] Button styles
- [ ] Toggle switch
```

### 3. Convert to Ready

```bash
gh pr ready
```

Or on GitHub: click **"Ready for review"**.

- [ ] PR converted from Draft to Ready

---

## Part B: Code Review (15 min)

### 4. Write an elite self-review

Post this comment on your PR:

```markdown
### Self-Review

**What's Good**
- Clean separation into own CSS file
- Consistent color variables

**Concerns**
- Should we detect `prefers-color-scheme` for auto dark mode?
- Contrast ratio needs WCAG AA check (4.5:1 min)

**Testing**
- Tested in Chrome + Firefox
- Mobile responsive verified
```

- [ ] Self-review posted

### 5. Use GitHub's suggestion feature

On the PR "Files changed" tab:
1. Click `+` next to any line of code
2. Click the `+-` suggestion icon
3. Write a code improvement as a suggestion
4. Submit review

- [ ] Created a code suggestion
- [ ] Applied suggestion (creates auto-commit)

### 6. Merge the PR

```bash
gh pr merge --squash --delete-branch
```

- [ ] Squash-merged and branch deleted

---

## Review Checklist (Bookmark)

```
[ ] Solves the stated problem?
[ ] Edge cases handled?
[ ] No code duplication?
[ ] No hardcoded secrets?
[ ] Input validated?
[ ] Tests for new logic?
```

---

## Self-Check

- [ ] Created Draft PR via `gh`
- [ ] Converted to Ready
- [ ] Posted elite self-review
- [ ] Used suggestion feature
- [ ] Merged with `gh pr merge`

**Next → [Day 09: Versioning + Releases](day-09-versioning-and-releases.md)**
