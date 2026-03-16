# Day 08 — Pull Request Template

**Goal:** Every PR you create auto-fills with a professional checklist. No more empty PRs.

---

## Why This Matters

Without a template, PRs look like:
```
Title: "update stuff"
Body: (empty)
```

With a template, EVERY PR auto-fills with sections for description, testing, security, etc.
Reviewers love you. Your code looks professional.

---

## Hands-On Exercise

### Exercise 1: Create the Template

In your `Spotify-lyric-tracker` repo:

```bash
cd ~/path/to/Spotify-lyric-tracker
mkdir -p .github
```

Create the file `.github/pull_request_template.md` with this content:

```markdown
## Description
<!-- What changed and why? Keep it brief. -->


## Type of Change
- [ ] Bug fix
- [ ] New feature
- [ ] Breaking change
- [ ] Documentation update
- [ ] Refactoring
- [ ] Performance improvement

## Changes Made
- [ ] Change 1
- [ ] Change 2

## Testing Done
- [ ] Manual testing completed
- [ ] Tested edge cases
- [ ] No console errors

## Checklist
- [ ] Code follows project style
- [ ] Self-review completed
- [ ] No debug/console.log statements left
- [ ] No hardcoded secrets or credentials

## Screenshots (if UI changes)
<!-- Add before/after screenshots -->
```

- [ ] File created at `.github/pull_request_template.md`

### Exercise 2: Commit the Template

```bash
git add .github/pull_request_template.md
git commit -m "chore(github): add pull request template"
git push origin main
```

- [ ] Template is pushed to GitHub

### Exercise 3: Test It

1. Create a test branch:
   ```bash
   git checkout -b test/pr-template
   echo "test" >> test-pr.txt
   git add . && git commit -m "test: verify PR template works"
   git push -u origin test/pr-template
   ```

2. Go to GitHub → your repo → click "Compare & pull request"

3. Verify the template auto-fills the PR body

- [ ] PR body shows the template with all sections
- [ ] Checkboxes are clickable

4. Close the test PR (don't merge)

### Exercise 4: Add Issue Templates (Bonus)

Create `.github/ISSUE_TEMPLATE/bug_report.md`:

```markdown
---
name: Bug Report
about: Report a bug
labels: bug
---

## Bug Description
<!-- What happened? -->

## Steps to Reproduce
1.
2.
3.

## Expected Behavior
<!-- What should have happened? -->

## Actual Behavior
<!-- What actually happened? -->

## Environment
- OS:
- Browser:
- Version:
```

Create `.github/ISSUE_TEMPLATE/feature_request.md`:

```markdown
---
name: Feature Request
about: Suggest a new feature
labels: enhancement
---

## Feature Description
<!-- What do you want? -->

## Problem It Solves
<!-- Why do you need this? -->

## Proposed Solution
<!-- How should it work? -->
```

```bash
mkdir -p .github/ISSUE_TEMPLATE
# Create both files above
git add .github/
git commit -m "chore(github): add issue templates for bugs and features"
git push
```

- [ ] Bug report template created
- [ ] Feature request template created
- [ ] Both pushed to GitHub

---

## Self-Check

- [ ] PR template auto-fills when creating a new PR
- [ ] Template has description, type, testing, and checklist sections
- [ ] Issue templates work when clicking "New Issue" on GitHub
- [ ] Everything committed with semantic messages

---

**Done? Move to [Day 09 — Branch Protection](day-09-branch-protection.md)**
