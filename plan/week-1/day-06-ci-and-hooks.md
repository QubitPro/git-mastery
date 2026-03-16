# Day 06 — GitHub Actions CI + Git Hooks

**Time:** 35 min | **Repo:** `Spotify-lyric-tracker`

---

## Part A: GitHub Actions CI (20 min)

### 1. Create workflow

```bash
git checkout main && git pull
git checkout -b ci/add-pipeline
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
      - uses: actions/checkout@v4

      - name: Validate JSON files
        run: |
          for f in $(find . -name "*.json" -not -path "./node_modules/*"); do
            echo "Checking $f"
            python3 -m json.tool "$f" > /dev/null
          done

      - name: Check for debug statements
        run: |
          if grep -rn "console.log\|debugger" --include="*.js" --include="*.html" . 2>/dev/null; then
            echo "WARNING: Debug statements found"
          else
            echo "Clean"
          fi

      - name: Check for large files
        run: find . -size +1M -not -path "./.git/*" | head -5
```

### 2. Add CI badge to README

Add this line at the top of your `README.md`:

```markdown
![CI](https://github.com/QubitPro/Spotify-lyric-tracker/actions/workflows/ci.yml/badge.svg)
```

### 3. Push and verify

```bash
git add .github/workflows/ci.yml README.md
git commit -m "ci(github): add CI pipeline and status badge"
git push -u origin ci/add-pipeline
```

Create PR → watch CI run in **Actions** tab.

- [ ] CI runs on PR
- [ ] Green checkmark visible

### 4. Connect CI to branch protection

**Settings** → **Branches** → edit `main` rule → **Require status checks** → search `lint-and-validate` → add it.

- [ ] CI is now required to merge

Merge the PR.

---

## Part B: Git Hooks (15 min)

### 5. Create pre-commit hook

```bash
git checkout main && git pull
```

Create `.githooks/pre-commit`:

```bash
#!/bin/bash
echo "Running pre-commit checks..."

if git diff --cached --name-only | xargs grep -l "console.log\|debugger" 2>/dev/null; then
    echo "BLOCKED: Remove console.log/debugger statements"
    exit 1
fi

if git diff --cached | grep -iE "(password\s*=|api_key\s*=|secret\s*=)" 2>/dev/null; then
    echo "BLOCKED: Possible hardcoded secret detected"
    exit 1
fi

echo "All checks passed!"
```

### 6. Create commit-msg hook

Create `.githooks/commit-msg`:

```bash
#!/bin/bash
commit_msg=$(cat "$1")
pattern="^(feat|fix|docs|style|refactor|perf|test|chore|ci|build)(\(.+\))?: .+"

if ! echo "$commit_msg" | grep -qE "$pattern"; then
    echo "BLOCKED: Use semantic commit format"
    echo "Example: feat(auth): add login validation"
    echo "Your message: $commit_msg"
    exit 1
fi
```

### 7. Activate and test

```bash
mkdir -p .githooks
chmod +x .githooks/pre-commit .githooks/commit-msg
git config core.hooksPath .githooks

# Test FAIL (bad message)
echo "test" >> hook-test.txt && git add .
git commit -m "updated stuff"
# Should be BLOCKED!

# Test PASS (good message)
git commit -m "test(hooks): verify commit validation works"
# Should PASS!
```

### 8. Share hooks in repo

```bash
git add .githooks/
git commit -m "chore(hooks): add shared pre-commit and commit-msg hooks"
git push
```

- [ ] Bad commits blocked
- [ ] Good commits pass
- [ ] Hooks tracked in repo

---

## Self-Check

- [ ] CI runs automatically on PRs
- [ ] CI is required to merge to main
- [ ] README shows CI badge
- [ ] Git hooks block bad commits locally
- [ ] Hooks enforce semantic commit format

**Next → [Day 07: Week 1 Challenge](day-07-challenge.md)**
