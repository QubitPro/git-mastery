# Elite-Level GitHub Development Workflow 🚀

Master these **undervalued but crucial** techniques to work like top-tier engineers.

**Table of Contents**
- [Part 1: Advanced Git Workflow](#part-1-advanced-git-workflow)
- [Part 2: Undervalued GitHub Features](#part-2-undervalued-github-features)
- [Part 3: Advanced Collaboration Patterns](#part-3-advanced-collaboration-patterns)
- [Part 4: Powerful Git Techniques](#part-4-powerful-git-techniques)
- [Part 5: Architecture-Level Practices](#part-5-architecture-level-practices)
- [Part 6: Code Quality Gates](#part-6-code-quality-gates)
- [Part 7: Metrics That Matter](#part-7-metrics-that-matter)
- [Part 8: Elite Team Workflow](#part-8-elite-team-workflow)
- [Quick Wins to Implement Today](#quick-wins-to-implement-today)

---

## Part 1: Advanced Git Workflow

### 1. Interactive Rebase - Rewrite History Like a Pro

Most devs ignore this. **Elite devs live here.**

```bash
git rebase -i HEAD~5        # Rebase last 5 commits
```

**What you can do:**
- **squash** - combines with previous commit
- **reorder** - drag lines to change order
- **reword** - edit commit message
- **edit** - make changes to commit
- **drop** - remove commit entirely
- **fixup** - like squash but discards commit message

#### Pro Pattern: Clean History Before PR

```bash
# Before submitting PR: make your branch clean
git rebase -i origin/main

# Commands during interactive rebase:
pick a1b2c3d Initial feature
squash d4e5f6g WIP fix
squash h8i9j0k typo fix
reword i1j2k3l Better message

# Result: One clean commit with perfect message
```

**Why it matters:** 
- Code reviewers respect clean history
- Shows professionalism
- Makes `git log` readable
- Easy to revert if needed
- Looks great in GitHub blame view

**Advanced: Preserve Merges**
```bash
git rebase -i --preserve-merges origin/main
```

---

### 2. Cherry-Pick with Strategy - Selective Commits

```bash
git cherry-pick a1b2c3d              # Copy specific commit
git cherry-pick a1b2c3d..f6g7h8i     # Range of commits
git cherry-pick -n a1b2c3d           # No commit yet (review first)
git cherry-pick --continue           # Continue after resolving conflicts
git cherry-pick --abort              # Cancel cherry-pick
```

#### Pro Use Cases

**Scenario 1: Backport to hotfix branch**
```bash
# Feature merged to main, but needs hotfix branch too
git checkout hotfix
git cherry-pick a1b2c3d
# Done in 30 seconds instead of rewriting code
```

**Scenario 2: Review before committing**
```bash
git cherry-pick -n a1b2c3d  # No commit yet
git diff                     # Review changes
git commit -m "Backported: feature X"
```

**Scenario 3: Multiple commits from feature branch**
```bash
git cherry-pick feature-branch~3..feature-branch  # Last 3 commits
```

---

### 3. Reflog - The Safety Net Nobody Uses

The reflog is your GitHub backup. It records EVERY action.

```bash
git reflog                          # See everything you did
git reflog --all                    # Include all branches
git reset --hard a1b2c3d            # Jump back to any state
git checkout a1b2c3d                # Same, but safer (checks out commit)
```

#### Disaster Recovery Scenario

```bash
# You accidentally rebased and lost 5 commits
$ git reflog
3e2d1c5 (HEAD) rebase: continue (finish)
a1b2c3d (HEAD@{1}) rebase: start
f6g7h8i (HEAD@{5}) commit: important feature

# Go back to before the bad rebase
$ git reset --hard f6g7h8i

# All commits are back!
```

**Why this matters:**
- Git rarely actually deletes data
- Reflog is your time machine
- Saves you from amateur mistakes
- Works for 30 days by default

---

### 4. Stash with Purpose - Not Just "Save Work"

```bash
# Basic stash
git stash

# Named stash (ELITE MOVE)
git stash save "WIP: database optimization"

# List all stashes
git stash list

# Apply specific stash
git stash apply stash@{2}

# Pop specific stash (apply + delete)
git stash pop stash@{2}

# Stash only specific files (PRO MOVE)
git stash push src/Services/ -m "API changes"

# Create branch from stash
git stash branch new-branch stash@{0}

# Show what's in stash
git stash show -p stash@{0}

# Clean all stashes
git stash drop --all
```

#### Pro Pattern: Stash by Scope

```bash
# Only stash database changes
git stash push src/Data/ -m "DB schema work"

# Only stash tests
git stash push tests/ -m "Test improvements"

# Keep API code, stash everything else
git stash push -u -k --  # -u includes untracked, -k keeps staged
```

---

## Part 2: Undervalued GitHub Features

### 1. GitHub Issues as Spec Documents

Most teams waste this feature.

**Create detailed issue template:**

```markdown
# [Feature] User Authentication System

## Problem Statement
Users cannot securely authenticate. Current system lacks 2FA support.
This affects compliance with SOC2 requirements.

## User Story
As a user, I want to enable 2FA via authenticator app,
so that my account is more secure.

## Acceptance Criteria
- [ ] User can log in with email/password
- [ ] User can enable 2FA via authenticator app
- [ ] Session timeout after 24 hours of inactivity
- [ ] Password reset email contains time-limited token (24h)
- [ ] Rate limiting: max 5 failed attempts per 15 minutes
- [ ] Invalid passwords logged for security audit

## Technical Design
See linked PR #452 for implementation details

## Testing Requirements
- [ ] Unit tests for JWT validation (>90% coverage)
- [ ] Integration tests for full auth flow
- [ ] Security test: SQL injection prevention
- [ ] Performance test: login < 500ms
- [ ] Load test: 1000 concurrent logins

## Performance Targets
- Login response time: < 500ms
- Token generation: < 100ms
- Database queries: optimized with indices

## Links & References
- Related: #451 (user management), #453 (sessions)
- Blocks: #460 (dashboard), #461 (API)
- RFC: https://github.com/company/rfcs/blob/main/001-auth.md
- Security guide: https://owasp.org/www-community/

## Acceptance Definition
Feature is done when:
1. All acceptance criteria checked
2. All tests passing
3. Code review approved by 2+ team members
4. QA validates against acceptance criteria
5. Performance benchmarks passed
6. Documentation updated
7. Deployed to staging successfully

## Assignees
@john @jane

## Labels
priority/high, type/feature, team/backend, size/large
```

**Why this approach:**
- Becomes your spec document
- PR directly follows the spec
- QA validates against spec
- Single source of truth
- Trackable completion

---

### 2. Branch Protection Rules - Enforce Quality

**Settings → Branches → Add Rule**

#### Basic Configuration
```
Pattern: main
✅ Require pull request reviews (minimum 2)
✅ Dismiss stale reviews when new commits pushed
✅ Require review from code owners (CODEOWNERS file)
✅ Require status checks to pass: CI/CD pipeline
✅ Require branches up to date with base branch
✅ Require conversations resolved before merge
✅ Include administrators in restrictions
✅ Lock branch (for emergency only)
✅ Allow force pushes: No
✅ Allow deletions: No
```

#### Create CODEOWNERS File

**Location:** `.github/CODEOWNERS`

```
# .github/CODEOWNERS
# Database team owns all database code
/src/Database/        @organization/database-team

# Security team reviews all auth code
/src/Auth/            @security-engineer @cto

# QA team signs off on all tests
/tests/               @qa-lead @qa-engineer

# All C# files require .NET team review
*.cs                  @dotnet-architect

# Configuration changes require DevOps
Dockerfile            @devops-team
docker-compose.yml    @devops-team
.github/workflows/    @devops-team
```

**Benefits:**
- Auto-assigns reviewers based on code path
- Enforces expertise-based reviews
- Prevents security vulnerabilities
- Ensures proper documentation review

---

### 3. Draft PRs - Show Work, Request Early Feedback

```
Create Pull Request → Check "Draft" checkbox
```

**When to use:**
- Early feedback on approach (before full implementation)
- Architecture review needed
- "Is this the right direction?"
- Work-in-progress that's still being developed
- Blocks accidental merges
- Shows progress to team

**Checklist:**
- [ ] Convert to READY when:
  - Implementation complete
  - Tests passing
  - Ready for formal review
  - No WIP commits
  - Documentation updated

---

### 4. Pull Request Templates - Standardize Quality

**Create:** `.github/pull_request_template.md`

```markdown
## Description
Brief summary of changes and why they're needed.

## Type of Change
- [ ] Bug fix (non-breaking change fixing an issue)
- [ ] Feature (non-breaking change adding functionality)
- [ ] Breaking change (fix or feature causing existing functionality to break)
- [ ] Documentation update

## Related Issues
Fixes #123, Related to #456, Blocks #789

## Changes Made
- [ ] Change 1
- [ ] Change 2
- [ ] Change 3

## Testing Done
- [ ] Unit tests added
- [ ] Integration tests added
- [ ] Manual testing completed

### Manual Testing Steps
1. Login as test user
2. Navigate to Dashboard
3. Verify data loads within 500ms
4. Check console for errors

### Test Results
- All unit tests: ✅ PASSED
- All integration tests: ✅ PASSED
- Coverage: 87% (+3%)

## Breaking Changes
- [ ] No breaking changes
- [ ] Yes, breaking changes:
  - List them here
  - Update migration guide
  - Notify stakeholders

## Performance Impact
- [ ] No performance impact
- [ ] Performance improvement:
  - Login: 800ms → 200ms
  - Dashboard load: 2s → 400ms
- [ ] Potential performance regression:
  - Describe here
  - Mitigation plan

## Checklist
- [ ] Code follows project style guide
- [ ] No new compiler warnings
- [ ] Self-review completed
- [ ] Comments added for complex logic
- [ ] Documentation updated
- [ ] No console.log/print statements (except logging)
- [ ] Database migrations tested
- [ ] Configuration changes documented

## Screenshots (if applicable)
If UI changes, add before/after screenshots

## Security Considerations
- [ ] No security vulnerabilities introduced
- [ ] Potential issues:
  - Input validation added
  - SQL injection prevented
  - Authentication enforced
  - Secrets not committed

## Database Changes
- [ ] No database changes
- [ ] Migration script included
- [ ] Migration tested on staging
- [ ] Rollback plan documented

## Deployment Notes
- [ ] Backward compatible
- [ ] Requires configuration changes:
  - List here
- [ ] Requires data migration:
  - Script location: scripts/migrate_v2.sql
  - Estimated runtime: 15 minutes
  - Rollback available: Yes

## Reviewers Notes
Any specific areas you want reviewers to focus on?
```

**Result:** Every PR includes all critical information. No "did you test this?" comments needed.

---

### 5. GitHub Actions - Professional CI/CD Pipeline

Create `.github/workflows/ci.yml`

```yaml
name: CI Pipeline

on:
  pull_request:
    branches: [main, develop]
  push:
    branches: [main]
  schedule:
    - cron: '0 2 * * *'  # Run nightly

jobs:
  build-and-test:
    runs-on: ubuntu-latest
    timeout-minutes: 30
    
    strategy:
      matrix:
        dotnet-version: ['7.0.x', '8.0.x']
    
    steps:
      - name: Checkout code
        uses: actions/checkout@v3
      
      - name: Setup .NET
        uses: actions/setup-dotnet@v3
        with:
          dotnet-version: ${{ matrix.dotnet-version }}
      
      - name: Cache dependencies
        uses: actions/cache@v3
        with:
          path: ~/.nuget/packages
          key: ${{ runner.os }}-nuget-${{ hashFiles('**/packages.lock.json') }}
          restore-keys: |
            ${{ runner.os }}-nuget-
      
      - name: Restore dependencies
        run: dotnet restore
      
      - name: Build
        run: dotnet build --no-restore --configuration Release
      
      - name: Run unit tests
        run: dotnet test --no-build --configuration Release --logger "trx" --collect:"XPlat Code Coverage"
      
      - name: Upload coverage to Codecov
        uses: codecov/codecov-action@v3
        with:
          files: ./coverage.xml
          flags: unittests
          fail_ci_if_error: true
  
  code-quality:
    runs-on: ubuntu-latest
    steps:
      - name: Checkout code
        uses: actions/checkout@v3
      
      - name: Setup .NET
        uses: actions/setup-dotnet@v3
        with:
          dotnet-version: '8.0.x'
      
      - name: Run SonarCloud analysis
        uses: SonarSource/sonarcloud-github-action@master
        env:
          GITHUB_TOKEN: ${{ secrets.GITHUB_TOKEN }}
          SONAR_TOKEN: ${{ secrets.SONAR_TOKEN }}
  
  security-scan:
    runs-on: ubuntu-latest
    steps:
      - name: Checkout code
        uses: actions/checkout@v3
      
      - name: Setup .NET
        uses: actions/setup-dotnet@v3
        with:
          dotnet-version: '8.0.x'
      
      - name: Restore dependencies
        run: dotnet restore
      
      - name: Run Snyk security scan
        uses: snyk/actions/dotnet@master
        env:
          SNYK_TOKEN: ${{ secrets.SNYK_TOKEN }}
  
  integration-tests:
    runs-on: ubuntu-latest
    
    services:
      postgres:
        image: postgres:15
        env:
          POSTGRES_PASSWORD: postgres
          POSTGRES_DB: testdb
        options: >-
          --health-cmd pg_isready
          --health-interval 10s
          --health-timeout 5s
          --health-retries 5
        ports:
          - 5432:5432
    
    steps:
      - name: Checkout code
        uses: actions/checkout@v3
      
      - name: Setup .NET
        uses: actions/setup-dotnet@v3
        with:
          dotnet-version: '8.0.x'
      
      - name: Run integration tests
        run: dotnet test --filter "Category=Integration" --configuration Release
        env:
          ConnectionStrings__DefaultConnection: "Host=localhost;Database=testdb;Username=postgres;Password=postgres"

  performance-benchmarks:
    runs-on: ubuntu-latest
    steps:
      - name: Checkout code
        uses: actions/checkout@v3
      
      - name: Setup .NET
        uses: actions/setup-dotnet@v3
        with:
          dotnet-version: '8.0.x'
      
      - name: Run benchmarks
        run: dotnet run -c Release --project src/Benchmarks/Benchmarks.csproj
      
      - name: Store benchmark result
        uses: benchmark-action/github-action-benchmark@v1
        with:
          tool: 'customBiggerIsBetter'
          output-file-path: benchmarks/output.json
          github-token: ${{ secrets.GITHUB_TOKEN }}
          auto-push: true
```

**Status badge in README:**
```markdown
![CI Status](https://github.com/your-user/your-repo/workflows/CI%20Pipeline/badge.svg?branch=main)
```

---

## Part 3: Advanced Collaboration Patterns

### 1. Semantic Commits - Commits With Purpose

Stop writing meaningless commit messages.

```
<type>(<scope>): <subject>

<body>

<footer>
```

#### Types
- **feat** - A new feature
- **fix** - A bug fix
- **docs** - Documentation changes
- **style** - Code style changes (formatting, semicolons, etc.)
- **refactor** - Code refactoring
- **perf** - Performance improvements
- **test** - Test additions/changes
- **chore** - Build, dependency, tooling changes
- **ci** - CI/CD configuration changes
- **build** - Build system changes

#### Examples

```
feat(auth): implement JWT token validation
  
  - Added JwtTokenValidator service
  - Implemented token expiration checks
  - Added refresh token rotation
  
Closes #123, Relates to #456

fix(database): resolve N+1 query in user listing
  
  Changed from:
    foreach user in users
      var orders = db.Orders.Where(u => u.Id == user.Id)
  
  To eager loading:
    var users = db.Users.Include(u => u.Orders)

docs(readme): update setup instructions for .NET 8

refactor(api): extract UserService layer

  Moved business logic from controller to service layer
  Reduces controller from 200 lines to 50 lines
  Improves testability
  
  Breaking: UserRepository is now internal

perf(cache): implement Redis caching for dashboard
  
  Benchmark results:
  - Dashboard load: 2.1s → 250ms (8.4x faster)
  - API endpoint: 540ms → 45ms (12x faster)
  - Database hits reduced by 95%
  
  Memory usage: +250MB Redis

test(auth): add integration tests for login flow

chore(deps): upgrade .NET from 7.0 to 8.0
```

**Why this matters:**
- Auto-generate changelogs
- Easy to search: `git log --grep="perf"`
- Shows commit intent at glance
- Enables automated workflows
- Makes `git blame` more informative

#### Tool: Commitizen (Interactive Commit Helper)

```bash
# Install globally
npm install -g commitizen

# Or use npx
npx cz commit

# Interactive guided commit
# Follows conventional commits format
```

#### Setup for Your Team

Create `.commitlintrc.json`:
```json
{
  "extends": ["@commitlint/config-conventional"],
  "rules": {
    "type-enum": [
      2,
      "always",
      ["feat", "fix", "docs", "style", "refactor", "perf", "test", "chore", "ci", "build"]
    ],
    "type-case": [2, "always", "lowercase"],
    "type-empty": [2, "never"],
    "subject-empty": [2, "never"],
    "subject-full-stop": [2, "never", "."],
    "subject-case": [2, "never", ["sentence-case"]]
  }
}
```

Create `.husky/commit-msg`:
```bash
#!/bin/sh
. "$(dirname "$0")/_/husky.sh"

npx --no -- commitlint --edit "${1}"
```

---

### 2. Code Review as Teaching - The Elite Approach

**What junior reviewers do:**
```
"This is wrong"
```

**What elite reviewers do:**
```
"I see you're using List<T> for storing user IDs. 
Have you considered HashSet<T> for O(1) lookups instead? 
This could matter when user_ids scales to millions.

For reference, we use a similar pattern in 
src/Services/UserService.cs (line 45-52).

Here's a benchmark showing the difference:
List<T>: 2.3ms per lookup
HashSet<T>: 0.003ms per lookup

What do you think? Happy to pair on it if you want!"
```

#### Review Checklist

As a reviewer, check:

1. **Functionality**
   - Does it solve the stated problem?
   - Are edge cases handled?
   - Error handling appropriate?

2. **Code Quality**
   - Follows naming conventions?
   - Functions under 20 lines?
   - No code duplication?
   - Comments for complex logic?

3. **Performance**
   - Any O(n²) loops?
   - Unnecessary database queries?
   - Memory leaks possible?
   - Run benchmarks if relevant

4. **Security**
   - Input validation?
   - SQL injection prevented?
   - Secrets not hardcoded?
   - Authentication/authorization checked?

5. **Testing**
   - Unit tests for new logic?
   - Edge cases tested?
   - Coverage > 80%?

6. **Documentation**
   - Public APIs documented?
   - README updated?
   - Configuration documented?

#### GitHub Feature: Suggestion Comments

```
When reviewing, click "Add a suggestion" button
Your code block appears:
User can "Commit suggestion" with one click
Shows up in conversation thread
Creates commit automatically
```

#### Template for Constructive Reviews

```
### 🎯 Overall Assessment
Brief summary of what's good and what needs work

### ✅ What I Liked
- Efficient use of async/await
- Great test coverage
- Clear variable names

### 🤔 Questions/Concerns
1. On line 45, why use List instead of HashSet?
   Context: When user_id collections scale to millions...

2. The database query on line 67 looks like N+1
   Suggestion: Use .Include() for eager loading

### 🔧 Requested Changes
1. Add null checks for userInput parameter
2. Add XML docs to public method
3. Consider extracting validation logic to separate class

### 📚 References
- Similar pattern: src/Services/UserService.cs
- OWASP guide: [link]
- Microsoft docs: [link]

### ✨ Nice Job!
This shows significant growth in your architecture understanding.
```

---

### 3. Merge Strategy - Clean vs. Readable

#### Option 1: Merge Commit (Default)
```
Shows all commits in main history
Better for tracking
Easier to revert entire feature
More verbose history

Use for: Major features, releases
```

#### Option 2: Squash and Merge (ELITE for features)
```
1 clean commit: "Add user authentication"
Cleaner main history
Easier to revert if needed
Less cluttered git log

Use for: Feature branches, UI work
```

#### Option 3: Rebase and Merge (ELITE for hotfixes)
```
Keeps linear history
No merge commit cluttering
Perfect for hotfixes
Clean revision history

Use for: Bug fixes, hotfixes
```

**GitHub UI:** Dropdown next to merge button (select option)

**Recommended Strategy:**

```
Feature branches → Squash and merge (feature/auth → main)
Release branches → Merge commit (with tag)
Hotfix branches → Rebase and merge (fastest to main)
```

---

## Part 4: Powerful Git Techniques

### 1. Git Aliases - Work Faster

Create/edit `~/.gitconfig`:

```ini
[user]
    name = Your Name
    email = your.email@example.com

[alias]
    # Basic shortcuts
    st = status
    co = checkout
    br = branch
    ci = commit
    
    # Advanced operations
    amend = commit --amend --no-edit
    undo = reset --soft HEAD~1
    last = log -1 HEAD
    visual = log --graph --oneline --all
    visualbranch = log --graph --oneline --all --decorate
    
    # Team operations
    contributors = shortlog -sn --all
    recentbranches = branch -a --sort=-committerdate
    review = log --no-merges --oneline origin/main..HEAD
    
    # Cleaning up
    deletemerged = "!git branch -d $(git branch --merged)"
    cleanlocal = "!git fetch origin && git branch -d $(git branch -v | grep gone)"
    
    # Advanced debugging
    blame-stats = "!git blame src/file.cs | cut -d' ' -f1 | sort | uniq -c | sort -rn"
    who-broke-it = bisect start
    
    # Staging operations
    staged = diff --cached
    unstage = reset HEAD
    discard = checkout --
    
    # Viewing commits
    mycommits = log --author="Your Name" --oneline
    today = log --since="1 day ago" --oneline
    week = log --since="1 week ago" --oneline
    month = log --since="1 month ago" --oneline
    
    # Quick fixes
    fixup = "!git add -A && git commit --fixup"
    squash = "!git add -A && git commit --squash"
```

**Usage:**
```bash
git st              # instead of git status
git visual          # beautiful tree view
git amend           # fix last commit without retyping
git undo            # undo last commit, keep changes
git review          # what did I do today?
git contributors    # who's been active?
```

---

### 2. Git Hooks - Prevent Bad Commits

Create `.git/hooks/pre-commit` (executable):

```bash
#!/bin/bash
set -e

echo "🔍 Running pre-commit checks..."

# 1. Check for console.log/print statements
echo "✓ Checking for debug statements..."
if git diff --cached | grep -E "(console\.log|Debug\.WriteLine|print\()" > /dev/null; then
    echo "❌ Error: Remove console.log/print statements before committing!"
    exit 1
fi

# 2. Check for secrets (passwords, tokens, API keys)
echo "✓ Checking for secrets..."
if git diff --cached | grep -iE "(password|secret|token|api[_-]?key|aws_access)" > /dev/null; then
    echo "⚠️  WARNING: Possible secrets detected!"
    echo "Please review before committing."
    read -p "Continue anyway? (y/N) " -n 1 -r
    echo
    if [[ ! $REPLY =~ ^[Yy]$ ]]; then
        exit 1
    fi
fi

# 3. Check commit message format
echo "✓ Checking commit format..."
if [ -f ".commitlintrc.json" ]; then
    npx commitlint --from HEAD --to HEAD
fi

# 4. Run linting
echo "✓ Running linting..."
dotnet format --verify-no-changes
if [ $? -ne 0 ]; then
    echo "❌ Code formatting issues. Run: dotnet format"
    exit 1
fi

# 5. Run unit tests
echo "✓ Running tests..."
dotnet test --filter "Category!=Integration" --verbosity quiet
if [ $? -ne 0 ]; then
    echo "❌ Tests failed. Commit rejected."
    exit 1
fi

echo "✅ All checks passed!"
exit 0
```

Make executable:
```bash
chmod +x .git/hooks/pre-commit
```

**Elite Move: Share hooks with team**

Create `.githooks/` directory:
```bash
mkdir .githooks
cp .git/hooks/pre-commit .githooks/
git config core.hooksPath .githooks
git add .githooks/
git commit -m "chore: add shared git hooks"
```

Team members will auto-use hooks when they clone.

---

### 3. Git Bisect - Find Bugs Like a Detective

Find which commit introduced a bug in ~10 steps instead of manually checking 100s.

```bash
# Start bisect mode
git bisect start

# Mark current commit as bad (has the bug)
git bisect bad HEAD

# Mark last known good commit
git bisect good v1.0

# Git checks out middle commit
# Test it... is it good or bad?
git bisect good                # or: git bisect bad

# Repeat until git finds the exact breaking commit
# Git shows: "commit abc123 is first bad commit"

# Exit bisect
git bisect reset
```

**Real Example:**
```bash
$ git bisect start
$ git bisect bad HEAD
$ git bisect good v2.0

# Git checks out a commit from the middle
# Run your tests...
# Failed? It's bad, keep going
$ git bisect bad

# Git narrows down further...
# Eventually finds:
$ git bisect good
$ git bisect log
# 7fe2dee was the first bad commit
```

**Automated Bisect (with tests):**
```bash
# Run tests automatically on each commit
git bisect run npm test

# Git will:
# 1. Check out commits
# 2. Run npm test
# 3. Mark as good/bad based on test result
# 4. Find bad commit automatically
```

---

### 4. Git Worktree - Multiple Branches Simultaneously

```bash
# You're on main working on feature A
# Suddenly need to fix critical bug

# Create separate working directory for hotfix
git worktree add ../hotfix-branch origin/main

# Change to it
cd ../hotfix-branch

# You're now on a SEPARATE copy of the repo!
# Work on hotfix independently
# Commit independently
# Never conflicts with your main workspace

# Check status
git worktree list

# Back to main workspace
cd ../yourrepo

# Remove worktree when done
git worktree remove ../hotfix-branch
```

**Pro Use Cases:**
- Team A works on feature, Team B on hotfix
- Parallel development without conflicts
- Code review while working on new feature
- Compare two branches side-by-side

**Setup Multiple Worktrees:**
```bash
# Main workspace
cd ~/project

# Feature work
git worktree add ../feature-auth auth-branch
cd ../feature-auth
# ... work on feature

# Hotfix work (separate terminal)
cd ~/project
git worktree add ../hotfix-urgent origin/main
cd ../hotfix-urgent
# ... work on hotfix
```

---

### 5. Advanced: Git Patches and Bundles

#### Create Patch File
```bash
# Diff between branches
git diff main feature-branch > feature.patch

# Last 3 commits as patch
git format-patch -3 HEAD

# Patch with full history
git format-patch main..feature-branch
```

#### Apply Patch
```bash
# Apply patch
git apply feature.patch

# Apply patch with commit
git am < feature.patch
```

#### Git Bundle (Full Repository Offline)
```bash
# Create bundle of commits
git bundle create myrepo.bundle main

# Share myrepo.bundle file...

# On another machine
git clone myrepo.bundle -b main myrepo
cd myrepo
```

---

## Part 5: Architecture-Level Practices

### 1. Monorepo vs Multirepo Decision

#### Monorepo Structure
```
/your-company
├── services/
│   ├── api-service/
│   ├── auth-service/
│   ├── notification-service/
│   └── data-service/
├── packages/
│   ├── shared-lib/
│   ├── ui-components/
│   └── domain-models/
├── tools/
│   ├── scripts/
│   └── build-tools/
├── docs/
└── .github/workflows/
```

**Monorepo Pros:**
- Shared code easily accessible
- Atomic commits across services
- Single CI/CD pipeline
- Unified dependency management
- Easier refactoring

**Monorepo Cons:**
- Everyone needs all tools installed
- Large clones (100s of GB possible)
- More complex CI/CD
- All changes visible to everyone
- Harder to have separate releases

#### Multirepo Structure
```
company/api-service
company/auth-service
company/notification-service
company/shared-lib (NuGet package)
```

**Multirepo Pros:**
- Lean clones
- Independent deployment
- Clear separation of concerns
- Teams can work independently
- Smaller CI/CD pipelines

**Multirepo Cons:**
- Coordinating changes across repos
- Dependency version management
- Publishing/versioning shared packages
- Testing integration harder

#### Elite Decision: Multirepo with Shared Packages

```
Repository Structure:
├── company/api-service (depends on shared-lib v1.2.0)
├── company/auth-service (depends on shared-lib v1.2.0)
└── company/shared-lib (published to NuGet)

Workflow:
1. Update shared-lib
2. Release new version (v1.3.0)
3. Services update dependency
4. Test integration
5. Deploy independently
```

---

### 2. Semantic Versioning - Version Like Professionals

```
MAJOR.MINOR.PATCH
v2.3.5
 | | └─ Patch (2.3.0 → 2.3.5): bug fixes, backward compatible
 | └─── Minor (2.3.0 → 2.4.0): new features, backward compatible
 └───── Major (2.3.0 → 3.0.0): breaking changes
```

#### Examples

```
1.0.0 → 1.0.1   (bug fix: fix login timeout)
1.0.1 → 1.1.0   (feature: add 2FA)
1.1.0 → 2.0.0   (breaking: remove legacy API)
```

#### GitHub Releases

```
1. Click "Create Release"
2. Tag: v2.0.0
3. Release title: Version 2.0.0 - Major Update
4. Description:

## 🎉 New Features
- User authentication system
- Dashboard redesign
- Real-time notifications

## 🔧 Improvements
- 40% faster database queries
- Refactored API layer
- Updated UI framework to latest

## ⚠️ Breaking Changes
- Removed legacy API v1 endpoints
- Database schema migration required
- Client app must update within 7 days

## 🐛 Bug Fixes
- Fixed memory leak in background service
- Fixed race condition in auth flow
- Fixed UI rendering on mobile

## 📚 Migration Guide
See MIGRATION_GUIDE.md for upgrade instructions

## 📦 Download
- Windows: [installer]
- macOS: [installer]
- Linux: [tarball]
```

---

### 3. Changelog - Document Professional Releases

**Create:** `CHANGELOG.md`

```markdown
# Changelog

All notable changes to this project will be documented in this file.

The format is based on [Keep a Changelog](https://keepachangelog.com/),
and this project adheres to [Semantic Versioning](https://semver.org/).

## [Unreleased]
### Added
- New feature in development
- Another upcoming feature

## [2.0.0] - 2026-03-16

### Added
- Two-factor authentication (TOTP, SMS)
- User dashboard with analytics
- Real-time notifications
- Email notification preferences
- Dark mode support

### Changed
- Refactored database layer for better performance
- Updated authentication framework
- Redesigned user interface
- Improved API error messages

### Deprecated
- Legacy API v1 endpoints (use v2 instead)
- Flash-based components (migrating to modern JS)

### Removed
- Legacy API v1 endpoints
- Support for .NET Framework (now .NET 8 only)
- Old dashboard

### Fixed
- Memory leak in background service
- Race condition in auth flow
- UI rendering issues on mobile

### Security
- Added rate limiting to login endpoint (5 attempts/15min)
- Implemented OWASP password requirements
- Added SQL injection prevention
- Enabled CSRF token validation

### Performance
- Dashboard loads 8x faster (2.1s → 250ms)
- API response time improved 40%
- Database queries optimized with indices
- Added caching layer (Redis)

## [1.9.0] - 2026-02-15

### Added
- Support for .NET 8
- User preferences API

### Fixed
- Login timeout issue
- Email delivery failures

## [1.8.0] - 2026-01-20

...

[Unreleased]: https://github.com/yourname/yourrepo/compare/v2.0.0...HEAD
[2.0.0]: https://github.com/yourname/yourrepo/compare/v1.9.0...v2.0.0
[1.9.0]: https://github.com/yourname/yourrepo/releases/tag/v1.9.0
```

**Auto-generate from commits:**
```bash
# Install
npm install conventional-changelog-cli

# Generate
conventional-changelog -p angular -i CHANGELOG.md -s

# Prepend to file
conventional-changelog -p angular -i CHANGELOG.md -s -r 0
```

---

## Part 6: Code Quality Gates

### 1. SonarQube Integration

Create `.github/workflows/sonarqube.yml`:

```yaml
name: SonarQube Code Quality

on:
  pull_request:
    branches: [main, develop]
  push:
    branches: [main]

jobs:
  sonarqube:
    runs-on: windows-latest
    steps:
      - uses: actions/checkout@v3
        with:
          fetch-depth: 0  # Full history for analysis
      
      - name: Setup .NET
        uses: actions/setup-dotnet@v3
        with:
          dotnet-version: '8.0.x'
      
      - name: Cache SonarQube
        uses: actions/cache@v3
        with:
          path: ~\.sonar\cache
          key: ${{ runner.os }}-sonar
      
      - name: Install SonarQube scanner
        run: dotnet tool install --global dotnet-sonarscanner
      
      - name: Begin SonarQube analysis
        run: dotnet sonarscanner begin /k:"project-key" /o:"organization" /d:sonar.token="${{ secrets.SONAR_TOKEN }}"
      
      - name: Build
        run: dotnet build --no-incremental
      
      - name: End SonarQube analysis
        run: dotnet sonarscanner end /d:sonar.token="${{ secrets.SONAR_TOKEN }}"
```

**Quality Gates Block PRs When:**
- Code smells detected
- Security vulnerabilities found
- Test coverage < 80% (configurable)
- Duplicate code > 5%
- Cognitive complexity too high

---

### 2. Dependabot - Automated Dependency Updates

**Settings → Code security → Enable Dependabot**

Features:
- ✅ Automatically creates PRs for dependency updates
- ✅ Includes security patches
- ✅ Groups updates by priority
- ✅ Runs CI on each automatically
- ✅ Configurable update schedule

**Configuration:** `.github/dependabot.yml`

```yaml
version: 2
updates:
  # NuGet packages
  - package-ecosystem: "nuget"
    directory: "/"
    schedule:
      interval: "daily"
      time: "03:00"
    open-pull-requests-limit: 5
    reviewers:
      - "security-team"
    allow:
      - dependency-type: "all"
    commit-message:
      prefix: "chore(deps):"

  # GitHub Actions
  - package-ecosystem: "github-actions"
    directory: "/"
    schedule:
      interval: "weekly"
      day: "monday"

  # npm (if you have Node projects)
  - package-ecosystem: "npm"
    directory: "/frontend"
    schedule:
      interval: "daily"
```

---

### 3. Performance Benchmarking

Create benchmarking project:

```csharp
using BenchmarkDotNet.Attributes;
using BenchmarkDotNet.Running;

[MemoryDiagnoser]
[SimpleJob(warmupCount: 3, targetCount: 5)]
public class AuthBenchmarks
{
    private IAuthService _authService;
    private const string TestPassword = "SecurePassword123!";

    [GlobalSetup]
    public void Setup()
    {
        _authService = new AuthService();
    }

    [Benchmark(Description = "Hash password")]
    public string HashPassword()
    {
        return _authService.HashPassword(TestPassword);
    }

    [Benchmark(Description = "Verify password")]
    public bool VerifyPassword()
    {
        var hash = _authService.HashPassword(TestPassword);
        return _authService.VerifyPassword(TestPassword, hash);
    }
}

class Program
{
    static void Main(string[] args)
    {
        var summary = BenchmarkRunner.Run<AuthBenchmarks>();
    }
}
```

**GitHub Action to track performance:**

```yaml
name: Performance Benchmarks

on:
  pull_request:
    branches: [main]
  push:
    branches: [main]

jobs:
  benchmark:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v3
      
      - uses: actions/setup-dotnet@v3
        with:
          dotnet-version: '8.0.x'
      
      - name: Run benchmarks
        run: dotnet run -c Release --project src/Benchmarks/Benchmarks.csproj
      
      - name: Store benchmark result
        uses: benchmark-action/github-action-benchmark@v1
        with:
          tool: 'customBiggerIsBetter'
          output-file-path: BenchmarkDotNet.Artifacts/results/AuthBenchmarks-results.json
          github-token: ${{ secrets.GITHUB_TOKEN }}
          auto-push: true
```

**Shows performance regression automatically in PR comments.**

---

## Part 7: Metrics That Matter

Track these metrics to measure team health:

### 1. Cycle Time
**Definition:** Time from commit to production
**Target:** < 1 day
**Measurement:**
```
Average = (Deployment date - Commit date) for all commits
```

### 2. Review Turnaround
**Definition:** Time from PR opened to first review
**Target:** < 4 hours
**Measurement:**
```
Average = (First review date - PR open date)
```

### 3. Deployment Frequency
**Definition:** How often you deploy to production
**Target:** Daily
**Measurement:**
```
Deployments per week
```

### 4. Mean Time to Recovery (MTTR)
**Definition:** Time to fix production incidents
**Target:** < 1 hour
**Measurement:**
```
Average = (Fix deployed - Issue detected)
```

### 5. Test Coverage
**Definition:** % of code with tests
**Target:** > 80%
**Measurement:**
```
Code coverage reports from CI/CD
```

### 6. Bug Escape Rate
**Definition:** % of bugs that reach production
**Target:** < 5%
**Measurement:**
```
Production bugs / Total bugs found * 100
```

### 7. Lead Time
**Definition:** Time from feature start to deployment
**Target:** < 1 week
**Measurement:**
```
Average = (Deployed date - Created date)
```

**Track in GitHub:**
- Settings → Insights → view metrics
- Use Issues/Projects for cycle time
- Use Actions for deployment frequency
- Use SonarQube for coverage

---

## Part 8: Elite Team Workflow (Putting It Together)

### The Complete Workflow

```
┌─ 1. Engineer Starts Work
│  ├─ Creates issue with detailed spec
│  │  ├─ Problem statement
│  │  ├─ Acceptance criteria (checklist)
│  │  └─ Testing requirements
│  ├─ Creates feature branch: git checkout -b feature/user-auth
│  └─ Creates DRAFT PR immediately (early feedback)
│
├─ 2. Development Phase
│  ├─ Commits with semantic messages
│  │  ├─ feat(auth): implement JWT validation
│  │  ├─ test(auth): add unit tests
│  │  ├─ perf(auth): optimize token generation
│  │  └─ fix(auth): handle expired tokens
│  ├─ Pre-commit hooks run automatically
│  │  ├─ Lint check
│  │  ├─ Format check
│  │  └─ Unit tests
│  ├─ Pushes regularly (signals progress)
│  └─ GitHub Actions runs on every push
│     ├─ Build
│     ├─ Tests
│     ├─ Coverage
│     └─ Linting
│
├─ 3. Code Review Preparation
│  ├─ Interactive rebase to clean history
│  │  ├─ Squash WIP commits
│  │  ├─ Reword messages
│  │  └─ Reorder if needed
│  ├─ Converts PR from DRAFT to READY
│  └─ Resolves merge conflicts if any
│
├─ 4. Code Review Phase
│  ├─ 2+ code reviewers assigned (CODEOWNERS)
│  ├─ Reviewers:
│  │  ├─ Check functionality against issue
│  │  ├─ Review code quality
│  │  ├─ Validate performance
│  │  ├─ Check security
│  │  └─ Verify tests
│  ├─ Author responds to feedback
│  │  ├─ Pushes fixes
│  │  ├─ Re-requests review
│  │  └─ GitHub Actions re-runs
│  ├─ All reviewers approve
│  └─ All CI checks pass
│     ├─ Unit tests ✅
│     ├─ Integration tests ✅
│     ├─ Coverage > 80% ✅
│     ├─ SonarQube gates ✅
│     ├─ Security scan ✅
│     └─ Performance benchmarks ✅
│
├─ 5. Merge & Deploy to Staging
│  ├─ Merge with "Squash and merge"
│  │  └─ Single clean commit on main
│  ├─ Automatic deployment to staging
│  └─ Smoke tests on staging
│
├─ 6. QA Testing
│  ├─ QA validates acceptance criteria
│  │  ├─ All checklist items ✅
│  │  ├─ Edge cases ✅
│  │  └─ Manual testing ✅
│  ├─ QA runs regression tests
│  │  └─ No regressions ✅
│  └─ QA approves for production
│
├─ 7. Production Deployment
│  ├─ Tag release: git tag v2.1.0
│  ├─ Create GitHub Release
│  │  ├─ Title: "v2.1.0 - User Authentication"
│  │  ├─ Description: changelog excerpt
│  │  └─ Artifacts: installer, documentation
│  ├─ Auto-deploy to production
│  └─ Monitoring alerts configured
│
└─ 8. Post-Deployment
   ├─ Monitor error rates
   ├─ Monitor performance metrics
   ├─ Check user feedback
   ├─ Document in runbook
   └─ Plan follow-up improvements

```

### Daily Standup Metrics

```
Engineer Reports:
1. Cycle Time: 3 days (feature opened → merged)
2. Review Turnaround: 45 minutes
3. Tests Added: 12 unit + 3 integration
4. Coverage: 87% (+2%)
5. Issues: None, smooth review

Team Reports:
1. Deployment Frequency: 8 deploys today
2. Mean Time to Recovery: 12 minutes (for hotfix)
3. Test Coverage: 82% overall
4. Open PRs: 3 (avg age: 2.5 hours)
5. Main branch status: ✅ Green
```

---

## Quick Wins to Implement Today

### Week 1 - Foundation
- [ ] **Day 1 (5 min):** Add PR template (`.github/pull_request_template.md`)
- [ ] **Day 2 (5 min):** Enable branch protection (Require reviews + CI)
- [ ] **Day 3 (10 min):** Create CODEOWNERS file
- [ ] **Day 4 (15 min):** Setup basic GitHub Actions (build + test)
- [ ] **Day 5 (10 min):** Create semantic commit template

### Week 2 - Workflow
- [ ] **Setup git aliases** (copy to ~/.gitconfig)
- [ ] **Create PR template** with checklist
- [ ] **Add .editorconfig** for formatting
- [ ] **Setup pre-commit hooks** (skip if Windows)

### Week 3 - Quality
- [ ] **Integrate SonarQube** or CodeFactor
- [ ] **Enable Dependabot** for dependency updates
- [ ] **Add coverage reporting** (Codecov/Coveralls)
- [ ] **Setup performance benchmarks**

### Week 4 - Team
- [ ] **Document review checklist** for team
- [ ] **Create CHANGELOG.md**
- [ ] **Setup semantic versioning**
- [ ] **Create contribution guide** (CONTRIBUTING.md)

---

## Resources for Mastery

### Books
- "Git Internals" - Understand git deeply
- "Accelerate" - Metrics that matter
- "The DevOps Handbook" - Modern workflows

### Online Resources
- GitHub Learning Lab (free, official)
- Pro Git Book (free online: git-scm.com/book)
- Conventional Commits (conventionalcommits.org)

### Tools to Master
- GitHub CLI: `gh` command-line tool
- GitKraken - Visual git client
- VS Code/JetBrains built-in git tools
- BenchmarkDotNet for performance

### Communities
- GitHub Discussions in your repos
- Dev.to articles on workflows
- Your team's best practices

---

## Next Steps

1. **Pick 2-3 ideas from this guide**
2. **Implement this week**
3. **Get team feedback**
4. **Refine process**
5. **Document for team**
6. **Make it a habit**

The key: Consistency > Perfection

Small improvements every day = Elite team in 3 months.

---

## Summary: Elite Developer Checklist

- [ ] Master interactive rebase
- [ ] Use semantic commits
- [ ] Understand git hooks
- [ ] Know git bisect
- [ ] Use git worktree
- [ ] Create detailed issues
- [ ] Use branch protection
- [ ] Automate with GitHub Actions
- [ ] Track meaningful metrics
- [ ] Teach others

**You're now ready to work like the best teams in the industry. 🚀**

---

*Last updated: March 2026*
*Feel free to adapt this to your team's specific needs*
