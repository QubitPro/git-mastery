# Day 13 — Git Hooks

**Goal:** Automatically check your code BEFORE it's committed. Catch mistakes locally.

---

## Why This Matters

Git hooks run scripts automatically at specific git events.
A pre-commit hook can:
- Block commits with debug statements
- Warn about possible secrets
- Run formatting checks
- Run quick tests

Catches problems BEFORE they reach GitHub.

---

## How It Works

Git hooks live in `.git/hooks/`. They're shell scripts that run automatically.

| Hook | When It Runs |
|------|-------------|
| `pre-commit` | Before a commit is created |
| `commit-msg` | After you write commit message (validates format) |
| `pre-push` | Before pushing to remote |

---

## Hands-On Exercise

### Exercise 1: Create a Pre-Commit Hook

```bash
cd ~/path/to/Spotify-lyric-tracker
```

Create `.git/hooks/pre-commit`:

```bash
#!/bin/bash
echo "Running pre-commit checks..."

# 1. Check for console.log statements
echo "Checking for debug statements..."
if git diff --cached --name-only | xargs grep -l "console.log\|debugger" 2>/dev/null; then
    echo "WARNING: console.log or debugger found in staged files!"
    echo "Remove them before committing, or use 'git commit --no-verify' to skip."
    exit 1
fi

# 2. Check for hardcoded secrets/passwords
echo "Checking for possible secrets..."
if git diff --cached | grep -iE "(password\s*=|api_key\s*=|secret\s*=|token\s*=)" 2>/dev/null; then
    echo "WARNING: Possible hardcoded secret detected!"
    echo "Please review before committing."
    exit 1
fi

# 3. Check for large files (> 500KB)
echo "Checking file sizes..."
large_files=$(git diff --cached --name-only | while read f; do
    if [ -f "$f" ]; then
        size=$(wc -c < "$f" 2>/dev/null)
        if [ "$size" -gt 512000 ] 2>/dev/null; then
            echo "$f ($size bytes)"
        fi
    fi
done)
if [ -n "$large_files" ]; then
    echo "WARNING: Large files detected:"
    echo "$large_files"
    echo "Consider using .gitignore or Git LFS"
    exit 1
fi

echo "All pre-commit checks passed!"
exit 0
```

Make it executable:
```bash
chmod +x .git/hooks/pre-commit
```

- [ ] Pre-commit hook created
- [ ] Hook is executable (`chmod +x`)

### Exercise 2: Test the Hook — Should PASS

```bash
echo "clean code here" >> clean-file.txt
git add clean-file.txt
git commit -m "test(hooks): verify pre-commit hook passes"
```

- [ ] Commit succeeded
- [ ] You saw "All pre-commit checks passed!" in output

### Exercise 3: Test the Hook — Should FAIL

```bash
echo "console.log('debug stuff');" >> debug-file.js
git add debug-file.js
git commit -m "test: this should be blocked"
```

- [ ] Commit was BLOCKED
- [ ] You saw the warning about console.log
- [ ] This is exactly what we want!

Clean up:
```bash
git checkout -- debug-file.js 2>/dev/null
rm -f debug-file.js
```

### Exercise 4: Create a Commit Message Hook

Create `.git/hooks/commit-msg`:

```bash
#!/bin/bash
# Enforce semantic commit format: type(scope): message

commit_msg=$(cat "$1")

# Pattern: type(scope): message  OR  type: message
pattern="^(feat|fix|docs|style|refactor|perf|test|chore|ci|build)(\(.+\))?: .+"

if ! echo "$commit_msg" | grep -qE "$pattern"; then
    echo "ERROR: Commit message doesn't follow semantic format!"
    echo ""
    echo "Expected: type(scope): message"
    echo "Example:  feat(auth): add login validation"
    echo ""
    echo "Types: feat, fix, docs, style, refactor, perf, test, chore, ci, build"
    echo ""
    echo "Your message was: $commit_msg"
    exit 1
fi

echo "Commit message format OK!"
exit 0
```

```bash
chmod +x .git/hooks/commit-msg
```

- [ ] Commit-msg hook created and executable

### Exercise 5: Test Commit Message Validation

Bad message (should fail):
```bash
echo "test" >> hook-test.txt
git add hook-test.txt
git commit -m "updated stuff"
```

- [ ] Commit BLOCKED — "doesn't follow semantic format"

Good message (should pass):
```bash
git commit -m "test(hooks): verify commit message validation"
```

- [ ] Commit succeeded

### Exercise 6: Share Hooks with the Repo (Team-Friendly)

Hooks in `.git/hooks/` are NOT tracked by git. To share with a team:

```bash
# Create a shared hooks directory
mkdir -p .githooks

# Copy your hooks there
cp .git/hooks/pre-commit .githooks/
cp .git/hooks/commit-msg .githooks/

# Tell git to use the shared directory
git config core.hooksPath .githooks

# Commit the shared hooks
git add .githooks/
git commit -m "chore(hooks): add shared pre-commit and commit-msg hooks"
```

Anyone who clones the repo just runs:
```bash
git config core.hooksPath .githooks
```

- [ ] Shared hooks directory created
- [ ] Hooks are tracked in git
- [ ] `core.hooksPath` configured

---

## Bypass Hooks (Emergency Only)

```bash
git commit --no-verify -m "hotfix: emergency production fix"
```

Use sparingly. If you skip hooks often, your hooks are too strict.

---

## Self-Check

- [ ] Pre-commit hook blocks debug statements
- [ ] Pre-commit hook warns about secrets
- [ ] Commit-msg hook enforces semantic format
- [ ] Good commits pass, bad commits are blocked
- [ ] Hooks are shared via `.githooks/` directory
- [ ] I know `--no-verify` exists but will rarely use it

---

**Done? Move to [Day 14 — Week 2 Practice Challenge](day-14-week2-practice.md)**
