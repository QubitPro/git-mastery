# Day 12 — Draft PRs & Code Review

**Goal:** Use Draft PRs for early feedback. Learn to review code like a senior engineer.

---

## Why This Matters

**Draft PRs:**
- Show work-in-progress to your team
- Get feedback BEFORE you finish (saves rework)
- Can't be accidentally merged

**Elite Code Reviews:**
- Teach, don't criticize
- Explain the WHY, not just the WHAT
- Include benchmarks, links, and alternatives

---

## Hands-On Exercise

### Exercise 1: Create a Draft PR

```bash
cd ~/path/to/Spotify-lyric-tracker
git checkout main && git pull
git checkout -b feature/dark-mode
```

Start a feature (doesn't need to be complete):
```bash
echo "/* Dark mode styles */" >> dark-mode.css
echo ".dark { background: #1a1a1a; color: #fff; }" >> dark-mode.css
git add dark-mode.css
git commit -m "feat(ui): add initial dark mode stylesheet"
git push -u origin feature/dark-mode
```

On GitHub:
1. Click **"Compare & pull request"**
2. Click the dropdown arrow next to "Create pull request"
3. Select **"Create draft pull request"**
4. Fill in the template (from Day 08)
5. Add a comment: "WIP: Looking for feedback on the approach before I continue"

- [ ] Draft PR created (shows "Draft" label)
- [ ] PR cannot be merged (merge button is grayed out)
- [ ] Template auto-filled the PR body

### Exercise 2: Add Progress Updates to Draft PR

Make more commits and push — the PR updates automatically:
```bash
echo ".dark a { color: #4fc3f7; }" >> dark-mode.css
git add . && git commit -m "feat(ui): add dark mode link colors"
git push
```

Add a comment on the PR:
```
Progress update:
- [x] Base dark mode colors
- [x] Link colors
- [ ] Button styles
- [ ] Toggle switch component
- [ ] localStorage persistence
```

- [ ] PR shows updated commits
- [ ] Progress checklist posted as comment

### Exercise 3: Convert to Ready

When "done", convert the Draft to Ready:

1. On the PR page, click **"Ready for review"**
2. The PR is now a full PR that can be reviewed and merged

- [ ] PR converted from Draft to Ready

### Exercise 4: Practice Elite Code Review

Go to ANY open-source project on GitHub (or one of your forked repos) and read a PR.

Now practice writing a review comment using this template:

```markdown
### Overall
Good approach! A few suggestions below.

### What I Liked
- Clean separation of dark mode into its own file
- Good use of CSS variables

### Suggestions
1. **Line 3:** Consider using CSS custom properties instead of hardcoded colors:
   ```css
   :root { --bg-dark: #1a1a1a; --text-dark: #fff; }
   .dark { background: var(--bg-dark); color: var(--text-dark); }
   ```
   **Why:** Makes it easy to adjust the theme in one place.

2. **Accessibility:** Ensure contrast ratio meets WCAG AA (4.5:1 minimum).

### Questions
- Are we supporting system preference detection (`prefers-color-scheme`)?
```

Write a review comment like this on your OWN PR for practice:

- [ ] Posted a self-review comment using the elite template
- [ ] Review includes: what's good, suggestions with WHY, and questions

### Exercise 5: Use GitHub Suggestion Feature

On your PR, click on a specific line of code and use the suggestion feature:

1. Click the `+` button next to a line of code
2. Click the suggestion icon (looks like `+-`)
3. Write a code suggestion:

```suggestion
.dark {
  background: var(--bg-dark, #1a1a1a);
  color: var(--text-dark, #ffffff);
}
```

The person can click "Commit suggestion" to apply it in one click.

- [ ] Created a suggestion comment on a code line
- [ ] Applied the suggestion (creates an automatic commit)

---

## Review Checklist (Bookmark This)

When reviewing ANY PR, check:

```
Functionality:
[ ] Does it solve the stated problem?
[ ] Edge cases handled?

Code Quality:
[ ] Follows naming conventions?
[ ] No code duplication?
[ ] Functions are small and focused?

Performance:
[ ] No unnecessary loops or queries?
[ ] Efficient data structures?

Security:
[ ] No hardcoded secrets?
[ ] Input validated?
[ ] No XSS/injection vulnerabilities?

Testing:
[ ] Tests added for new logic?
[ ] Edge cases tested?
```

---

## Self-Check

- [ ] I created a Draft PR
- [ ] I know Draft PRs can't be merged accidentally
- [ ] I converted Draft to Ready
- [ ] I wrote an elite-style review comment
- [ ] I used GitHub's suggestion feature
- [ ] I understand the review checklist

---

**Done? Move to [Day 13 — Git Hooks](day-13-git-hooks.md)**
