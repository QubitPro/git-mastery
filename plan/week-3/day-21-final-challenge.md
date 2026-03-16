# Day 21 — Final Challenge

**Goal:** Prove you've mastered all 3 weeks. Complete this challenge using every skill you learned.

---

## The Scenario

You're a professional developer maintaining `Spotify-lyric-tracker`.
A user reported a bug, and you also need to ship a new feature.
Do everything with elite-level workflow.

---

## Challenge Tasks

### Task 1: Bug Report → Fix → Release (30 min)

- [ ] **Create an Issue** on GitHub with a proper bug report template:
  - Title: `[Bug] Lyrics don't load for songs with apostrophes`
  - Use your issue template from Day 08

- [ ] **Create a hotfix branch:**
  ```bash
  git checkout main && git pull
  git checkout -b hotfix/apostrophe-fix
  ```

- [ ] **Make the fix** (create any file simulating the fix):
  ```bash
  git commit -m "fix(lyrics): escape apostrophes in song title parsing

  Songs with titles like \"Don't Stop Me Now\" caused a parse error
  due to unescaped apostrophes in the API query string.

  Closes #<issue-number>"
  ```

- [ ] **Create a PR** (not draft — this is a hotfix, it's ready):
  - Fill in PR template completely
  - Reference the issue: `Fixes #<number>`

- [ ] **Verify CI passes** on the PR

- [ ] **Merge using Rebase and Merge** (hotfix strategy from Day 19)

- [ ] **Tag the fix:** `git tag -a v1.2.1 -m "v1.2.1 - Fix apostrophe parsing"`

- [ ] **Update CHANGELOG.md** under a new `[1.2.1]` section

- [ ] **Create a GitHub Release** for v1.2.1

### Task 2: Feature Branch → Draft PR → Review → Ship (30 min)

- [ ] **Create an Issue** for the feature:
  - Title: `[Feature] Add mini-player mode`

- [ ] **Create feature branch:**
  ```bash
  git checkout main && git pull
  git checkout -b feature/mini-player
  ```

- [ ] **Create a Draft PR** immediately (early feedback)

- [ ] **Make 4+ commits** with semantic messages:
  - `feat(ui): add mini-player component layout`
  - `feat(ui): add collapse/expand animation`
  - `style(ui): add mini-player responsive styles`
  - `test(ui): add mini-player toggle tests`

- [ ] **Git hooks** should validate your commits (Day 13)

- [ ] **Push regularly** — CI runs on each push

- [ ] **Interactive rebase** to clean history (squash WIP if any)

- [ ] **Convert Draft → Ready for Review**

- [ ] **Write a self-review** using elite review format

- [ ] **Merge using Squash and Merge** (feature strategy)

- [ ] **Tag:** `git tag -a v1.3.0 -m "v1.3.0 - Mini player mode"`

- [ ] **Update CHANGELOG.md** with `[1.3.0]` section

- [ ] **Create GitHub Release** for v1.3.0

### Task 3: Simulate a Disaster & Recovery (10 min)

- [ ] **Accidentally** delete the feature branch:
  ```bash
  git branch -D feature/mini-player
  ```

- [ ] **Recover** using `git reflog` (Day 06)

- [ ] **Create a worktree** to verify the recovered branch:
  ```bash
  git worktree add ../verify-recovery feature/mini-player
  ```

- [ ] **Clean up** the worktree:
  ```bash
  git worktree remove ../verify-recovery
  ```

---

## Final Audit — Check Your Repo

Go to your `Spotify-lyric-tracker` on GitHub and verify:

### Repository Setup
- [ ] `.github/pull_request_template.md` exists
- [ ] `.github/CODEOWNERS` exists
- [ ] `.github/dependabot.yml` exists
- [ ] `.github/workflows/ci.yml` exists
- [ ] `.github/ISSUE_TEMPLATE/` has bug + feature templates
- [ ] `.githooks/` shared hooks directory exists

### Branch Protection
- [ ] Main branch is protected
- [ ] Direct push blocked
- [ ] Force push blocked
- [ ] CI is a required check

### Releases
- [ ] Multiple version tags exist (v1.0.0, v1.0.1, v1.2.0, etc.)
- [ ] GitHub Releases with proper descriptions
- [ ] CHANGELOG.md is up to date

### Code Quality
- [ ] All recent commits use semantic format
- [ ] README has CI status badge
- [ ] No "WIP" or "asdfg" commits in main

---

## Grading

| Category | Points |
|----------|--------|
| **Week 1 Skills** | |
| Semantic commits throughout | /10 |
| Interactive rebase used | /10 |
| Stash or cherry-pick used | /5 |
| Reflog recovery worked | /10 |
| **Week 2 Skills** | |
| PR template auto-filled | /5 |
| Branch protection working | /10 |
| CODEOWNERS auto-assigns | /5 |
| CI runs on PRs | /10 |
| Draft PR used correctly | /5 |
| Self-review posted | /5 |
| Git hooks validated commits | /5 |
| **Week 3 Skills** | |
| Correct version bumps | /5 |
| GitHub Releases created | /5 |
| CHANGELOG.md updated | /5 |
| Correct merge strategies | /5 |
| **Total** | **/100** |

### Score Meaning
- **90-100:** Elite developer workflow achieved
- **75-89:** Very solid — review weak areas
- **60-74:** Good foundation — practice the areas you skipped
- **Below 60:** Go back and redo the weak weeks

---

## What's Next?

You've completed the 3-week plan. Here's how to keep growing:

### Keep Practicing
- Use semantic commits on EVERY project (not just this one)
- Set up CI/CD on every new repo
- Always protect main branch
- Review your own PRs before requesting review

### Level Up (Advanced Topics)
- **GitHub Projects** — Kanban boards for project management
- **GitHub Packages** — Publish your own packages
- **Signed Commits** — GPG sign your commits for verification
- **GitHub API** — Automate GitHub with scripts
- **Monorepo Tools** — Nx, Turborepo for large projects
- **GitOps** — Infrastructure as Code with git workflows

### Teach Others
The best way to solidify knowledge is to teach it.
- Write a blog post about your workflow
- Help a teammate set up their repo
- Create a team onboarding guide

---

## Congratulations!

```
Week 1: Git Power User        ████████████ COMPLETE
Week 2: Professional Workflow  ████████████ COMPLETE
Week 3: Release Engineering    ████████████ COMPLETE

Status: ELITE GITHUB DEVELOPER
```

Your `Spotify-lyric-tracker` repo now looks like it's maintained by a senior engineer.
Apply these practices to ALL your repositories.

**You're now in the top 10% of GitHub users. Keep building.**
