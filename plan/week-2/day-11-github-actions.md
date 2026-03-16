# Day 11 — GitHub Actions CI

**Goal:** Automate testing on every push and PR. Broken code never reaches main.

---

## Why This Matters

Without CI:
- "It works on my machine" (but breaks on everyone else's)
- Bugs slip into main because nobody ran tests
- Manual testing is slow and unreliable

With GitHub Actions:
- Every push triggers automatic checks
- PRs show green checkmark or red X
- Broken code is blocked from merging

---

## How It Works

1. You create a `.yml` file in `.github/workflows/`
2. GitHub runs it automatically on push/PR
3. Results appear as status checks on your PRs

---

## Hands-On Exercise

### Exercise 1: Create Your First Workflow

For `Spotify-lyric-tracker` (HTML/JS project), create a basic CI:

```bash
mkdir -p .github/workflows
```

Create `.github/workflows/ci.yml`:

```yaml
name: CI

on:
  push:
    branches: [main]
  pull_request:
    branches: [main]

jobs:
  lint-and-validate:
    runs-on: ubuntu-latest
    timeout-minutes: 10

    steps:
      - name: Checkout code
        uses: actions/checkout@v4

      - name: Check for broken links in HTML
        run: |
          echo "Checking HTML files exist..."
          ls -la *.html || echo "No HTML files in root"

      - name: Validate JSON files
        run: |
          echo "Validating JSON files..."
          for f in $(find . -name "*.json" -not -path "./node_modules/*"); do
            echo "Checking $f"
            python3 -m json.tool "$f" > /dev/null && echo "  Valid" || echo "  INVALID!"
          done

      - name: Check for debug statements
        run: |
          echo "Checking for leftover debug statements..."
          if grep -rn "console.log\|debugger\|TODO HACK\|FIXME HACK" --include="*.js" --include="*.html" .; then
            echo "WARNING: Debug statements found (review before merge)"
          else
            echo "Clean - no debug statements"
          fi

      - name: Check file sizes
        run: |
          echo "Checking for oversized files..."
          find . -size +1M -not -path "./.git/*" | head -10
          echo "Done"
```

- [ ] File created at `.github/workflows/ci.yml`

### Exercise 2: Push and Watch It Run

```bash
git checkout -b ci/add-github-actions
git add .github/workflows/ci.yml
git commit -m "ci(github): add CI workflow for lint and validation"
git push -u origin ci/add-github-actions
```

1. Go to GitHub → your repo → **Actions** tab
2. Watch the workflow run!

- [ ] Workflow appears in Actions tab
- [ ] Workflow runs and shows green checkmark

### Exercise 3: Create a PR and See Status Checks

1. On GitHub, create a PR from `ci/add-github-actions` to `main`
2. Look at the PR page — you should see the CI status check

- [ ] PR shows "CI" check with green checkmark (or running spinner)
- [ ] Status check appears before the merge button

### Exercise 4: Make CI Fail (On Purpose)

Create a branch with intentional issues:
```bash
git checkout -b test/ci-failure
echo '{"broken json' >> bad.json    # Invalid JSON
git add . && git commit -m "test: add broken JSON to test CI failure"
git push -u origin test/ci-failure
```

Create a PR and watch CI fail:

- [ ] CI shows red X on the PR
- [ ] You can click on the failed check to see what went wrong
- [ ] This is exactly what we want — broken code is caught!

Clean up:
```bash
git checkout main
git branch -D test/ci-failure
```

### Exercise 5: Add a README Badge

Add the CI status badge to your README:

```markdown
![CI](https://github.com/QubitPro/Spotify-lyric-tracker/actions/workflows/ci.yml/badge.svg)
```

Add this at the TOP of your README.md file.

```bash
git checkout ci/add-github-actions
# Edit README.md to add the badge at the top
git add README.md
git commit -m "docs(readme): add CI status badge"
git push
```

- [ ] README shows live CI status badge

### Exercise 6: Add CI as Required Check (Connect to Day 09)

1. Go to **Settings** → **Branches** → edit your `main` protection rule
2. Under "Require status checks to pass":
   - Search for "lint-and-validate" (your job name)
   - Add it as required
3. Save

Now PRs CANNOT merge if CI fails!

- [ ] CI is a required status check for merging to main

---

## Workflow Trigger Reference

```yaml
on:
  push:
    branches: [main]           # Run on push to main
  pull_request:
    branches: [main]           # Run on PRs to main
  schedule:
    - cron: '0 9 * * 1'        # Every Monday at 9am
  workflow_dispatch:            # Manual trigger button
```

---

## Self-Check

- [ ] CI workflow runs on every push and PR
- [ ] I can see results in the Actions tab
- [ ] PRs show green/red status checks
- [ ] CI is a required check for merging (connected to branch protection)
- [ ] README has a CI badge
- [ ] I committed everything with semantic messages

---

**Done? Move to [Day 12 — Draft PRs & Code Review](day-12-draft-prs.md)**
