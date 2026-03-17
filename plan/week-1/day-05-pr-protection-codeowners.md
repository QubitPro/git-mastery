# Day 05 — PR Template + Branch Protection + CODEOWNERS

**Time:** 30 min | **Repo:** `Spotify-lyric-tracker` (push to GitHub today)

---

## 1. PR Template (10 min)

```bash
cd ~/path/to/Spotify-lyric-tracker
git checkout main
git pull origin main
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

> **Learner Tip:** Create this file with VS Code: `code .github/pull_request_template.md`. The file MUST be in the `.github/` directory and named exactly `pull_request_template.md` — GitHub looks for this specific name.

> **Learner Tip (First PR won't auto-fill):** The template only auto-fills for PRs created AFTER the template is merged into the default branch (main). Your very first PR — the one adding the template — will have an empty description. This is expected! Fill it in manually the first time.

- [ ] File created

#### SWOT — PR Template

| | |
|---|---|
| **Strengths** | Forces consistent PR descriptions. Every PR has a checklist — no more "forgot to test" or "left debug code". Reviewers always know what to look for. |
| **Weaknesses** | Developers may fill it in lazily (check all boxes without actually doing them). The template is only a guide, not enforcement. |
| **Opportunities** | Customize per project — add sections for screenshots, API changes, migration notes. CI can even check if the template was filled in properly. |
| **Threats** | Over-complicated templates slow down small changes. Keep it concise — 3-4 sections max. If people skip it, the template is too long. |

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

> **Learner Tip:** CODEOWNERS uses glob patterns. `*` matches everything (default owner). More specific patterns override the default. The LAST matching pattern wins — order matters!

> **Learner Tip:** In a team, CODEOWNERS is powerful: `*.css @designer-team`, `*.py @backend-team`. The right people get auto-assigned to review the right files. For solo projects like yours, it's mostly practice — but it shows you understand professional workflows.

- [ ] File created

#### SWOT — CODEOWNERS

| | |
|---|---|
| **Strengths** | Automatic reviewer assignment — no one forgets to request a review. Ensures domain experts review their area of the codebase. |
| **Weaknesses** | Can bottleneck if one person owns too many files. That person becomes a review blocker for the whole team. |
| **Opportunities** | Combine with "Require review from Code Owners" branch rule for mandatory expert review. Scales well as teams grow. |
| **Threats** | Outdated CODEOWNERS file (person left the team, files moved) causes PRs to get stuck with no valid reviewer. Keep it updated! |

## 3. Commit & Push

```bash
git add .github/
git commit -m "chore(github): add PR template and CODEOWNERS"
git push -u origin chore/github-setup
```

Create a PR on GitHub and merge it.

> **Learner Tip:** After merging on GitHub, your local main is behind. Always run `git checkout main && git pull origin main` to sync up. If your local main has extra commits from practice exercises, reset first: `git reset --hard origin/main` then pull.

> **Learner Tip (Practice commits on main):** During Days 01-04, you may have practice commits on local main (cherry-picks, conflict tests) that were never pushed. This is normal. `git reset --hard origin/main` cleans your local main to match GitHub exactly. Those practice commits are safe in reflog if you ever need them.

- [ ] PR merged with template auto-filled

#### SWOT — PR Workflow

| | |
|---|---|
| **Strengths** | PRs create a review trail — every change is documented, discussed, and approved before merging. Professional standard for all teams. |
| **Weaknesses** | Solo developers reviewing their own PRs feels redundant. But it builds the habit for when you join a team. |
| **Opportunities** | PR descriptions become project documentation. Searching old PRs to understand "why was this changed?" is incredibly valuable. |
| **Threats** | Rubber-stamping PRs (approving without reading) defeats the purpose. Even solo, actually read your own diff before merging. |

## 4. Branch Protection (10 min)

On GitHub → **Settings** → **Rules** → **Rulesets** → **New ruleset** → **New branch ruleset**:

> **Learner Tip (UI versions):** GitHub has two interfaces for branch protection. Older repos show **Settings → Branches → Add rule** (classic). Newer repos show **Settings → Rules → Rulesets** (modern). Both achieve the same goal — the screenshots below use the Rulesets UI.

**Configure the ruleset:**

```
Ruleset Name: main
Enforcement status: Active         ◀── IMPORTANT! Default is "Disabled"

Target branches:
  Click "Add target" → "Include default branch"

Rules to enable:
  [x] Restrict deletions
  [x] Require a pull request before merging
  [x] Block force pushes
  [x] Require conversation resolution before merging
```

> **Learner Tip (Enforcement status):** The biggest gotcha — if you leave Enforcement as "Disabled", the rule exists but does NOTHING. Always set it to **Active**. "Evaluate" mode is for testing rules without enforcing them.

> **Learner Tip (Target branches):** You must add a target branch or the rule has nothing to protect. Click "Add target" → select "Include default branch" to protect `main`.

- [ ] Rule created

Test it:
```bash
git checkout main && git pull origin main
echo "test" >> test-protection.txt && git add . && git commit -m "test: direct push"
git push origin main
```

Expected output:
```
remote: error: GH013: Repository rule violations found for refs/heads/main.
remote: - Changes must be made through a pull request.
! [remote rejected] main -> main (push declined due to repository rule violations)
```

- [ ] Push REJECTED (this is correct!)

> **Learner Tip (Cleanup after test):** After the rejected push, undo the test commit and delete the test file:
> ```bash
> git reset HEAD~1
> del test-protection.txt     # Windows
> rm test-protection.txt      # Mac/Linux
> ```
> Use `git reset HEAD~1` (not `git undo`) to unstage. Then delete the untracked file manually. `git checkout -- <file>` only works on tracked files.

```bash
git reset HEAD~1
del test-protection.txt
```

#### SWOT — Branch Protection

| | |
|---|---|
| **Strengths** | Prevents accidental pushes to main. Enforces PR workflow — no shortcuts. Blocks force pushes that could destroy shared history. |
| **Weaknesses** | Can be annoying for quick hotfixes — you MUST go through a PR even for one-line changes. Solo developers may feel slowed down. |
| **Opportunities** | Add status checks (Day 06) — require CI to pass before merging. Add required reviewers for team projects. Builds towards a production-grade workflow. |
| **Threats** | Overly strict rules (require 3 approvals, all checks pass, signed commits) can grind development to a halt. Start simple, add rules as needed. |

## 5. Verify CODEOWNERS

```bash
git checkout -b test/codeowners
echo "/* test */" >> test.css
git add . && git commit -m "test: verify CODEOWNERS"
git push -u origin test/codeowners
```

Create PR → check if you're auto-assigned as reviewer.

> **Learner Tip:** On the PR page, look at the right sidebar under "Reviewers". If CODEOWNERS is working, you'll see your name (@QubitPro) auto-assigned. For solo repos, you can't review your own PR in some configurations — but the assignment itself proves it's working.

- [ ] Auto-assigned as reviewer

#### SWOT — CODEOWNERS Verification

| | |
|---|---|
| **Strengths** | Automated — no human needs to remember to assign reviewers. Works based on file paths, so the right expert always reviews the right code. |
| **Weaknesses** | For solo developers, auto-assigning yourself is circular. The real value shows in teams of 2+ people. |
| **Opportunities** | Combine with "Require review from Code Owners" to make reviews mandatory, not just suggested. |
| **Threats** | If CODEOWNERS patterns are wrong (typos, wrong paths), reviews won't be assigned. Test with a real PR like you just did. |

---

## Self-Check

- [ ] PR template auto-fills on new PRs
- [ ] CODEOWNERS auto-assigns reviewers
- [ ] Direct push to main is blocked
- [ ] Force push to main is blocked
- [ ] Understood the difference between classic branch protection and Rulesets

---

## SWOT Analysis — Day 05 Overall

### Strengths
- **Professional workflow** — PR templates, branch protection, and CODEOWNERS are the three pillars of team collaboration on GitHub
- **Automated quality gates** — No more "oops, pushed directly to main" or "forgot to get a review"
- **Documentation trail** — Every change has a PR with description, checklist, and review comments
- **Industry standard** — Every serious open-source project and company uses these. Knowing them is expected

### Weaknesses
- **Setup overhead** — These are GitHub-specific configurations, not git skills. They don't transfer to GitLab/Bitbucket as-is (though similar features exist)
- **Solo friction** — For one-person projects, PRs and reviews feel like bureaucracy. The value comes in teams
- **False security** — Branch protection only protects the remote. Local branches have no protection — you can still mess up locally
- **Template fatigue** — If every PR requires filling out a long template, developers start copy-pasting or ignoring sections

### Opportunities
- **CI integration** — Day 06 adds GitHub Actions. Combined with branch protection ("require status checks"), you get automated testing on every PR
- **Open source credibility** — Repos with PR templates, CODEOWNERS, and branch protection signal a mature, well-maintained project
- **Scaling foundation** — When your project grows from 1 to 5 contributors, these rules are already in place. No scramble to set them up later
- **Portfolio piece** — Showing these in your GitHub repos demonstrates you understand production workflows, not just code

### Threats
- **Over-configuration** — Adding too many rules too early kills velocity. Start with the basics (PR required, no force push) and add more as needed
- **Rule bypass** — Admins can bypass rules. In a real team, even admins should follow the PR workflow. "Do not allow bypassing" is important
- **Stale CODEOWNERS** — As projects evolve, file patterns change. CODEOWNERS that doesn't match reality causes confusion
- **Merge conflicts from long-lived PRs** — If branch protection slows merging, branches diverge further, leading to more conflicts

---

## Command Explanation Reference

### Git Commands Used Today

| Command | What It Does | When to Use |
|---------|-------------|-------------|
| `mkdir -p .github` | Creates `.github` directory (and parents if needed) | First-time setup for GitHub config files |
| `git add .github/` | Stages all files in the `.github` directory | After creating PR template and CODEOWNERS |
| `git push -u origin <branch>` | Pushes branch and sets up tracking | First push of any new branch |
| `git reset HEAD~1` | Undoes last commit, keeps changes as unstaged | After a rejected push — undo the commit |
| `git reset --hard origin/main` | Resets local main to match GitHub exactly | When local main has extra practice commits |
| `git checkout -- <file>` | Discards changes to a tracked file | Reverting a file to its last committed state |
| `del <file>` (Windows) / `rm <file>` | Deletes an untracked file | Cleaning up test files after experiments |
| `git pull origin main` | Fetches and merges remote main into local | After merging a PR on GitHub — sync your local |

### GitHub Configuration Files

| File | Location | Purpose |
|------|----------|---------|
| `pull_request_template.md` | `.github/` | Auto-fills PR description with your template |
| `CODEOWNERS` | `.github/` | Auto-assigns reviewers based on file patterns |
| `ISSUE_TEMPLATE/` | `.github/` | Templates for new issues (not covered today) |
| `workflows/` | `.github/` | GitHub Actions CI/CD (Day 06) |

### CODEOWNERS Pattern Syntax

| Pattern | Matches | Example |
|---------|---------|---------|
| `*` | Everything (default) | `* @QubitPro` |
| `*.js` | All JavaScript files | `*.js @frontend-team` |
| `*.css` | All CSS files | `*.css @design-team` |
| `.github/` | All GitHub config files | `.github/ @devops-team` |
| `docs/` | All files in docs directory | `docs/ @docs-team` |
| `/src/api/` | Specific directory from root | `/src/api/ @backend-team` |

> **Key Insight:** Day 05 is where git (local tool) meets GitHub (collaboration platform). Everything before today was about YOUR workflow. Today is about TEAM workflow — even if you're the only team member right now. These habits will make you immediately productive when you join a team or start accepting contributors.

---

## ASCII Workflow — Day 05 Visual Summary

```
                        DAY 05 OVERVIEW
  ┌─────────────────────────────────────────────────────────┐
  │                                                         │
  │   PR TEMPLATE                                           │
  │   ═══════════                                           │
  │                                                         │
  │   .github/pull_request_template.md                      │
  │   ┌──────────────────────────────┐                      │
  │   │ ## Description               │                      │
  │   │ ## Type of Change            │                      │
  │   │ ## Testing Done              │    Every new PR       │
  │   │ ## Checklist                 │──▶ auto-fills with    │
  │   └──────────────────────────────┘    this template      │
  │                                                         │
  │   First PR: empty (template not on main yet)            │
  │   All future PRs: auto-filled!                          │
  │                                                         │
  ├─────────────────────────────────────────────────────────┤
  │                                                         │
  │   CODEOWNERS                                            │
  │   ══════════                                            │
  │                                                         │
  │   .github/CODEOWNERS                                    │
  │   ┌──────────────────────────────┐                      │
  │   │ *        @QubitPro           │  ◀── default owner   │
  │   │ *.css    @QubitPro           │  ◀── CSS specialist  │
  │   │ *.js     @QubitPro           │  ◀── JS specialist   │
  │   │ .github/ @QubitPro           │  ◀── config owner    │
  │   └──────────────────────────────┘                      │
  │              │                                          │
  │              ▼                                          │
  │   PR touches test.css ──▶ @QubitPro auto-assigned       │
  │   PR touches app.js  ──▶ @QubitPro auto-assigned        │
  │   PR touches anything──▶ @QubitPro auto-assigned        │
  │                                                         │
  │   In a team:                                            │
  │   *.css  @designer ──▶ designer reviews CSS changes     │
  │   *.py   @backend  ──▶ backend dev reviews Python       │
  │   *.test @qa-team  ──▶ QA reviews test changes          │
  │                                                         │
  ├─────────────────────────────────────────────────────────┤
  │                                                         │
  │   BRANCH PROTECTION (Rulesets)                          │
  │   ════════════════════════════                           │
  │                                                         │
  │   WITHOUT protection:                                   │
  │   ┌──────────┐    git push     ┌──────────┐            │
  │   │  local   │────────────────▶│  main    │  Anyone     │
  │   │  main    │                 │ (GitHub) │  can push!  │
  │   └──────────┘                 └──────────┘            │
  │                                                         │
  │   WITH protection:                                      │
  │   ┌──────────┐    git push     ┌──────────┐            │
  │   │  local   │───────╳────────▶│  main    │  BLOCKED!  │
  │   │  main    │                 │ (GitHub) │            │
  │   └──────────┘                 └──────────┘            │
  │                                                         │
  │   The ONLY way in:                                      │
  │   ┌──────────┐   push    ┌──────────┐  PR    ┌──────┐  │
  │   │ feature  │──────────▶│ feature  │──────▶│ main │  │
  │   │ (local)  │          │ (GitHub) │ merge │(GitHub)│  │
  │   └──────────┘          └──────────┘       └──────┘  │
  │                                                         │
  ├─────────────────────────────────────────────────────────┤
  │                                                         │
  │   COMPLETE WORKFLOW                                     │
  │   ═════════════════                                     │
  │                                                         │
  │   1. Create branch locally                              │
  │      git checkout -b feature/thing                      │
  │              │                                          │
  │   2. Make commits                                       │
  │      git commit -m "feat: add thing"                    │
  │              │                                          │
  │   3. Push branch                                        │
  │      git push -u origin feature/thing                   │
  │              │                                          │
  │   4. Create PR on GitHub                                │
  │      ┌────────────────────────┐                         │
  │      │ PR template auto-fills │                         │
  │      │ CODEOWNERS assigns     │                         │
  │      │ reviewer               │                         │
  │      └────────────────────────┘                         │
  │              │                                          │
  │   5. Review + Merge                                     │
  │              │                                          │
  │   6. Sync local                                         │
  │      git checkout main && git pull                      │
  │                                                         │
  │   ⚠ Direct push to main? REJECTED.                     │
  │   ⚠ Force push to main? BLOCKED.                       │
  │   ⚠ Delete main? RESTRICTED.                           │
  │                                                         │
  └─────────────────────────────────────────────────────────┘
```

**Next → [Day 06: GitHub Actions + Git Hooks](day-06-ci-and-hooks.md)**
