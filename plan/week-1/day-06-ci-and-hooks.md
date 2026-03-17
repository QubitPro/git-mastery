# Day 06 — GitHub Actions CI + Git Hooks

**Time:** 35 min | **Repo:** `Spotify-lyric-tracker`

---

## Part A: GitHub Actions CI (20 min)

### 1. Create workflow

```bash
git checkout main && git pull origin main
git checkout -b ci/add-pipeline
mkdir .github/workflows        # Windows: no -p flag needed
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

> **Learner Tip:** Create this file with VS Code: `code .github/workflows/ci.yml`. The YAML file must be in `.github/workflows/` — GitHub only looks there for Actions.

> **Learner Tip (YAML is picky):** YAML uses spaces for indentation, NOT tabs. Use 2 spaces per level. A single wrong indent will break the entire workflow. VS Code helps by highlighting YAML syntax errors.

> **Learner Tip (Windows):** `mkdir -p` doesn't work on Windows. If `.github` already exists, just run `mkdir .github\workflows`. If it says "already exists", that's fine — the directory is there.

- [ ] File created

#### SWOT — GitHub Actions CI

| | |
|---|---|
| **Strengths** | Runs automatically on every PR and push — no manual testing needed. Free for public repos. Catches issues before they reach main. |
| **Weaknesses** | CI runs in the cloud — takes 30-60 seconds minimum even for simple checks. Can't test everything (UI, performance, etc.). |
| **Opportunities** | Add more checks over time: linting, unit tests, build validation, deploy previews. CI is the foundation of DevOps. |
| **Threats** | Flaky tests (pass sometimes, fail sometimes) erode trust in CI. Keep checks fast and reliable. If CI is slow, developers stop waiting for it. |

### 2. Add CI badge to README

Add this line at the **very top** of your `README.md`:

```markdown
![CI](https://github.com/QubitPro/Spotify-lyric-tracker/actions/workflows/ci.yml/badge.svg)
```

> **Learner Tip:** This is markdown text that goes INSIDE `README.md` — don't type it in the terminal! Open the file with `code README.md`, add the line at the top, and save.

> **Learner Tip (Badge not showing?):** The badge only works after the workflow has run at least once. If it shows "no status" initially, that's expected — it updates after the first CI run.

> **Learner Tip (Separate PR):** If you forget to add the badge in the CI commit, that's fine — create a separate branch and PR for it: `git checkout -b docs/add-badge`. This is actually better practice (one concern per PR).

- [ ] Badge added

#### SWOT — CI Badge

| | |
|---|---|
| **Strengths** | Instant visual indicator of project health. Green = passing, red = broken. Builds trust with users and contributors. |
| **Weaknesses** | Only shows the latest status — doesn't tell you WHAT failed. You still need to check the Actions tab for details. |
| **Opportunities** | Add multiple badges: CI status, test coverage, license, version. Professional repos have a badge bar at the top of README. |
| **Threats** | A red badge is worse than no badge — it signals a broken project. Fix CI failures quickly or remove the badge temporarily. |

### 3. Push and verify

```bash
git add .github/workflows/ci.yml README.md
git commit -m "ci(github): add CI pipeline and status badge"
git push -u origin ci/add-pipeline
```

Create PR → watch CI run in **Actions** tab.

> **Learner Tip (Uncommitted changes?):** If `git checkout main` fails with "local changes would be overwritten", stash first: `git stash save "WIP: current work"`, then checkout and pull.

- [ ] CI runs on PR
- [ ] Green checkmark visible

### 4. Connect CI to branch protection

**Settings** → **Rules** → **Rulesets** → edit your `main` ruleset → enable **Require status checks to pass** → search `lint-and-validate` → add it → **Save**.

> **Learner Tip (Check not found?):** The status check name only appears in the search AFTER the workflow has run at least once. If you can't find `lint-and-validate`, merge the CI PR first, then come back and add the status check requirement.

> **Learner Tip (Rulesets vs classic):** If you're using the newer Rulesets UI (Settings → Rules → Rulesets), that's fine — it works the same way. The plan mentions "Branches" but both paths achieve the same result.

- [ ] CI is now required to merge

Merge the PR.

---

## Part B: Git Hooks (15 min)

### 5. Create pre-commit hook

```bash
git checkout main && git pull origin main
```

Create `.githooks/pre-commit`:

```bash
#!/bin/bash
echo "Running pre-commit checks..."

FLAGGED=$(git diff --cached --name-only | grep -v "^\.githooks/" | xargs grep -l "console\.log\|debugger" 2>/dev/null)
if [ -n "$FLAGGED" ]; then
    echo "BLOCKED: Remove console.log/debugger statements"
    echo "$FLAGGED"
    exit 1
fi

if git diff --cached -- . ':!.githooks' | grep -iE "(password\s*=|api_key\s*=|secret\s*=)" 2>/dev/null; then
    echo "BLOCKED: Possible hardcoded secret detected"
    exit 1
fi

echo "All checks passed!"
```

> **Learner Tip (Hook scanning itself):** The original version of this hook had a bug — it scanned its OWN file for "console.log" and "debugger" (since those strings appear in the grep pattern). The fix: exclude `.githooks/` from the search with `grep -v "^\.githooks/"`. Always test hooks by committing the hook files themselves!

> **Learner Tip (grep warning):** You may see `Usage: grep [OPTION]...` when no files match the pattern. This is harmless — it means no staged files contain debug statements. The hook still works correctly.

#### SWOT — Pre-commit Hook

| | |
|---|---|
| **Strengths** | Catches problems BEFORE they become commits. No debug code or secrets ever enter git history. Instant feedback — no waiting for CI. |
| **Weaknesses** | Only runs locally — each developer must configure `core.hooksPath`. Can be bypassed with `git commit --no-verify`. |
| **Opportunities** | Add more checks: code formatting (prettier), linting (eslint), file size limits, forbidden patterns. Pre-commit frameworks like `husky` or `pre-commit` automate setup. |
| **Threats** | Slow hooks frustrate developers. Keep pre-commit checks under 2 seconds. Move heavy checks (tests, builds) to CI instead. |

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

> **Learner Tip:** This hook enforces Day 01's semantic commit format automatically. No more accidental "fix stuff" or "WIP" commits. The regex requires: `type(optional-scope): message`.

#### SWOT — Commit-msg Hook

| | |
|---|---|
| **Strengths** | Enforces semantic commit format automatically. Bad messages are caught instantly — no need for PR review comments about commit messages. |
| **Weaknesses** | The regex may be too strict or too lenient. Edge cases like `Merge branch...` (auto-generated by git) may get blocked. |
| **Opportunities** | Extend the pattern: require ticket numbers (`feat(JIRA-123): ...`), minimum message length, or specific scopes. |
| **Threats** | Developers frustrated by rejections may use `--no-verify` to bypass. Make sure the error message is helpful and shows the expected format. |

### 7. Activate and test

```bash
mkdir .githooks
git config core.hooksPath .githooks
```

> **Learner Tip (Windows):** `chmod +x` doesn't apply on Windows. Git for Windows handles hook execution differently — it uses the shebang (`#!/bin/bash`) to find the interpreter. If hooks don't run, make sure Git Bash is installed (it comes with Git for Windows).

> **Learner Tip (`core.hooksPath`):** This setting is per-repo. New clones won't have it set. Add a note in your README telling collaborators to run `git config core.hooksPath .githooks` after cloning.

Test **FAIL** (bad message):
```bash
echo "test" >> hook-test.txt && git add .
git commit -m "updated stuff"
# Should be BLOCKED!
```

Test **PASS** (good message):
```bash
git commit -m "test(hooks): verify commit validation works"
# Should PASS!
```

- [ ] Bad commits blocked
- [ ] Good commits pass

### 8. Share hooks via PR

> **Learner Tip (Branch protection!):** Since main is protected, you can't push directly. If you accidentally committed on main, move it to a branch:
> ```bash
> git checkout -b chore/add-hooks
> git checkout main
> git reset --hard origin/main
> git checkout chore/add-hooks
> git push -u origin chore/add-hooks
> ```
> This is a common real-world pattern — accidentally committing on a protected branch and moving the commit to a feature branch.

```bash
git checkout -b chore/add-hooks
git add .githooks/ hook-test.txt
git commit -m "chore(hooks): add shared pre-commit and commit-msg hooks"
git push -u origin chore/add-hooks
```

Create PR → merge.

- [ ] Hooks tracked in repo
- [ ] PR merged

---

## Self-Check

- [ ] CI runs automatically on PRs
- [ ] CI is required to merge to main
- [ ] README shows CI badge
- [ ] Git hooks block bad commits locally
- [ ] Hooks enforce semantic commit format
- [ ] Hooks are shared in the repo via `.githooks/`

---

## SWOT Analysis — Day 06 Overall

### Strengths
- **Two layers of protection** — Hooks catch problems locally (instant), CI catches them remotely (thorough). Belt AND suspenders
- **Automated quality** — No human needs to remember to check for debug statements or bad commit messages. The tools enforce it
- **CI is the industry standard** — Every professional project has CI. GitHub Actions is free for public repos and deeply integrated
- **Hooks are portable** — Stored in `.githooks/`, they travel with the repo. Any collaborator can activate them

### Weaknesses
- **Hooks are opt-in** — Each developer must run `git config core.hooksPath .githooks`. There's no way to force it remotely
- **CI has a delay** — Even simple workflows take 30+ seconds. Developers may push and move on without checking results
- **YAML configuration** — GitHub Actions uses YAML, which is whitespace-sensitive and easy to misconfigure
- **False positives** — Hooks may flag legitimate code (e.g., a comment mentioning "console.log") or block auto-generated messages

### Opportunities
- **Expand CI gradually** — Start with simple checks (today), add linting, testing, deployment later. CI grows with your project
- **Pre-commit frameworks** — Tools like `husky` (Node) or `pre-commit` (Python) auto-install hooks on `npm install` / `pip install`
- **Branch-specific workflows** — Run different CI jobs for different branches (staging vs production)
- **GitHub Marketplace** — Thousands of pre-built Actions for common tasks: deploy, notify, release, scan for vulnerabilities

### Threats
- **CI addiction** — Relying entirely on CI and never testing locally. CI should be a safety net, not a replacement for local testing
- **Secret exposure** — Workflow files are public. Never hardcode API keys or tokens in `.yml` — use GitHub Secrets instead
- **Hook bypass** — `git commit --no-verify` skips all hooks. CI is the backstop for when hooks are bypassed
- **Workflow complexity** — Over-engineered CI with 20 jobs and 15-minute runs. Keep it fast — slow CI kills productivity

---

## Command Explanation Reference

### GitHub Actions Commands

| Term / Command | What It Does | When to Use |
|---------|-------------|-------------|
| `on: push / pull_request` | Triggers — when the workflow runs | `push` for main, `pull_request` for PRs |
| `runs-on: ubuntu-latest` | The OS for your CI runner | Always use `ubuntu-latest` unless you need Windows/macOS |
| `uses: actions/checkout@v4` | Clones your repo into the CI runner | First step of every workflow |
| `run: \|` | Runs shell commands in the CI runner | Any bash command you'd run locally |
| `timeout-minutes: 10` | Kills the job if it takes too long | Prevents runaway jobs from burning CI minutes |
| `jobs:` | Groups of steps that run in parallel | Each job gets its own fresh runner |
| `steps:` | Sequential commands within a job | Each step runs after the previous one completes |

### Git Hook Commands

| Command | What It Does | When to Use |
|---------|-------------|-------------|
| `git config core.hooksPath .githooks` | Tells git to look in `.githooks/` for hook scripts | After cloning a repo with shared hooks |
| `git commit --no-verify` | Skips all hooks (pre-commit, commit-msg) | Emergency bypass — use sparingly! |
| `git diff --cached` | Shows what's currently staged | Used inside pre-commit hooks to check staged changes |
| `git diff --cached --name-only` | Lists filenames of staged changes | Used to selectively check specific files |
| `exit 1` (in hook script) | Blocks the operation | When the hook detects a problem |
| `exit 0` (in hook script) | Allows the operation to continue | When all checks pass (default if script ends normally) |

### Git Workflow Commands (Day 06 patterns)

| Command | What It Does | When to Use |
|---------|-------------|-------------|
| `git stash save "name"` | Saves uncommitted changes temporarily | When checkout fails due to local changes |
| `git checkout -b <branch>` | Creates branch from current commit | Moving an accidental main commit to a branch |
| `git reset --hard origin/main` | Resets local main to match GitHub | After moving a commit to a branch |
| `git pull origin main` | Syncs local main with GitHub | After merging a PR on GitHub |
| `mkdir .github/workflows` | Creates the Actions directory | First-time CI setup |

### Hook Types Reference

| Hook | Runs When | Common Use |
|------|-----------|------------|
| `pre-commit` | Before commit is created | Check for debug code, secrets, formatting |
| `commit-msg` | After message is written, before commit | Enforce commit message format |
| `pre-push` | Before push to remote | Run tests, check branch name |
| `post-merge` | After a merge completes | Install dependencies, rebuild |
| `pre-rebase` | Before rebase starts | Prevent rebase of shared branches |

> **Key Insight:** CI and hooks serve the same purpose — automated quality gates — but at different points in the workflow. Hooks are your **local guard** (instant, bypassable). CI is your **remote guard** (slower, mandatory). Together, they create a workflow where bad code has to get past TWO checkpoints before reaching main. This is how professional teams maintain code quality at scale.

---

## ASCII Workflow — Day 06 Visual Summary

```
                        DAY 06 OVERVIEW
  ┌─────────────────────────────────────────────────────────┐
  │                                                         │
  │   TWO LAYERS OF PROTECTION                              │
  │   ════════════════════════                               │
  │                                                         │
  │   Layer 1: LOCAL (Git Hooks)                            │
  │   ┌──────────────────────────────────────────┐          │
  │   │                                          │          │
  │   │  You type: git commit -m "msg"           │          │
  │   │           │                              │          │
  │   │           ▼                              │          │
  │   │  ┌─────────────────┐                     │          │
  │   │  │  pre-commit     │                     │          │
  │   │  │  • debug code?  │──FAIL──▶ BLOCKED    │          │
  │   │  │  • secrets?     │                     │          │
  │   │  └────────┬────────┘                     │          │
  │   │           │ PASS                         │          │
  │   │           ▼                              │          │
  │   │  ┌─────────────────┐                     │          │
  │   │  │  commit-msg     │                     │          │
  │   │  │  • semantic     │──FAIL──▶ BLOCKED    │          │
  │   │  │    format?      │                     │          │
  │   │  └────────┬────────┘                     │          │
  │   │           │ PASS                         │          │
  │   │           ▼                              │          │
  │   │     Commit created!                      │          │
  │   │                                          │          │
  │   └──────────────────────────────────────────┘          │
  │                                                         │
  │   Layer 2: REMOTE (GitHub Actions CI)                   │
  │   ┌──────────────────────────────────────────┐          │
  │   │                                          │          │
  │   │  You push + create PR                    │          │
  │   │           │                              │          │
  │   │           ▼                              │          │
  │   │  ┌─────────────────┐                     │          │
  │   │  │  CI Pipeline    │                     │          │
  │   │  │  • valid JSON?  │                     │          │
  │   │  │  • debug code?  │──FAIL──▶ ❌ Can't   │          │
  │   │  │  • large files? │          merge      │          │
  │   │  └────────┬────────┘                     │          │
  │   │           │ PASS                         │          │
  │   │           ▼                              │          │
  │   │     ✅ Ready to merge                    │          │
  │   │                                          │          │
  │   └──────────────────────────────────────────┘          │
  │                                                         │
  ├─────────────────────────────────────────────────────────┤
  │                                                         │
  │   GITHUB ACTIONS WORKFLOW                               │
  │   ═══════════════════════                                │
  │                                                         │
  │   .github/workflows/ci.yml                              │
  │   ┌──────────────────────────────────────────┐          │
  │   │ on:                                      │          │
  │   │   push: [main]      ◀── runs on push     │          │
  │   │   pull_request: [main] ◀── runs on PR    │          │
  │   │                                          │          │
  │   │ jobs:                                    │          │
  │   │   lint-and-validate:                     │          │
  │   │     runs-on: ubuntu  ◀── cloud runner    │          │
  │   │     steps:                               │          │
  │   │       1. checkout    ◀── get code        │          │
  │   │       2. validate JSON                   │          │
  │   │       3. check debug                     │          │
  │   │       4. check large files               │          │
  │   └──────────────────────────────────────────┘          │
  │                                                         │
  │   GitHub runs this automatically.                       │
  │   You see ✅ or ❌ on every PR.                         │
  │                                                         │
  ├─────────────────────────────────────────────────────────┤
  │                                                         │
  │   GIT HOOKS FLOW                                        │
  │   ══════════════                                        │
  │                                                         │
  │   .githooks/pre-commit    .githooks/commit-msg          │
  │   ┌──────────────────┐    ┌──────────────────┐          │
  │   │ Runs BEFORE      │    │ Runs AFTER you   │          │
  │   │ commit is created│    │ write the message│          │
  │   │                  │    │                  │          │
  │   │ Checks CODE:     │    │ Checks MESSAGE:  │          │
  │   │ • console.log    │    │ • semantic format│          │
  │   │ • debugger       │    │ • feat/fix/docs  │          │
  │   │ • passwords      │    │                  │          │
  │   └──────────────────┘    └──────────────────┘          │
  │                                                         │
  │   Activate: git config core.hooksPath .githooks         │
  │   Bypass:   git commit --no-verify (emergency only!)    │
  │                                                         │
  ├─────────────────────────────────────────────────────────┤
  │                                                         │
  │   ACCIDENTAL COMMIT ON PROTECTED MAIN                   │
  │   ═══════════════════════════════════                    │
  │                                                         │
  │   Oops, committed on main:                              │
  │   main: ○──○──● (can't push!)                           │
  │                                                         │
  │   Fix:                                                  │
  │   1. git checkout -b fix-branch  (branch has commit)    │
  │   2. git checkout main                                  │
  │   3. git reset --hard origin/main (main is clean)       │
  │   4. git checkout fix-branch                            │
  │   5. git push -u origin fix-branch                      │
  │   6. Create PR → merge                                  │
  │                                                         │
  │   fix-branch: ○──○──●  ──PR──▶  main: ○──○──●          │
  │                                                         │
  └─────────────────────────────────────────────────────────┘
```

**Next → [Day 07: Week 1 Challenge](day-07-challenge.md)**
