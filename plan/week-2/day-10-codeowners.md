# Day 10 — CODEOWNERS

**Goal:** Auto-assign reviewers based on which files are changed. The right person reviews the right code.

---

## Why This Matters

Without CODEOWNERS:
- You forget to tag reviewers
- Wrong person reviews database code
- Security code gets reviewed by a junior dev

With CODEOWNERS:
- GitHub auto-assigns reviewers based on file paths
- Security files always get security team review
- Nobody's PR sits without a reviewer

---

## How It Works

GitHub reads `.github/CODEOWNERS` and auto-requests review from the listed users when their files are changed in a PR.

---

## Hands-On Exercise

### Exercise 1: Create CODEOWNERS

For your `Spotify-lyric-tracker` repo:

```bash
cd ~/path/to/Spotify-lyric-tracker
```

Create `.github/CODEOWNERS`:

```
# CODEOWNERS — Auto-assign PR reviewers
# Docs: https://docs.github.com/en/repositories/managing-your-repositorys-settings-and-features/customizing-your-repository/about-code-owners

# Default: you review everything
*                       @QubitPro

# HTML files
*.html                  @QubitPro

# CSS/Styling
*.css                   @QubitPro

# JavaScript
*.js                    @QubitPro

# Configuration files
*.json                  @QubitPro
*.yml                   @QubitPro
*.yaml                  @QubitPro

# GitHub config (workflows, templates)
.github/                @QubitPro

# Documentation
*.md                    @QubitPro
```

**Note:** For a solo project, everything points to you. On a team, you'd split by expertise:
```
# Example for a team:
/src/database/       @db-engineer
/src/auth/           @security-lead
/src/frontend/       @frontend-dev
*.test.js            @qa-engineer
.github/workflows/   @devops-team
```

- [ ] `.github/CODEOWNERS` file created

### Exercise 2: Commit and Push

```bash
git checkout -b chore/add-codeowners
git add .github/CODEOWNERS
git commit -m "chore(github): add CODEOWNERS for auto-review assignment"
git push -u origin chore/add-codeowners
```

Create a PR on GitHub and merge it.

- [ ] CODEOWNERS pushed and merged

### Exercise 3: Test It

1. Create a new branch and modify an HTML file:
   ```bash
   git checkout main && git pull
   git checkout -b test/codeowners-check
   echo "<!-- test -->" >> index.html   # or any .html file
   git add . && git commit -m "test: verify CODEOWNERS auto-assignment"
   git push -u origin test/codeowners-check
   ```

2. Create a PR on GitHub

3. Check the "Reviewers" sidebar — you should be auto-assigned

- [ ] GitHub auto-requested review from @QubitPro

### Exercise 4: Plan for Future Team Growth

Write down (in a comment in your CODEOWNERS file) how you'd split ownership if you had a team:

```
# === FUTURE TEAM PLAN ===
# When the team grows:
# /backend/           @backend-dev
# /frontend/          @frontend-dev
# /api/               @api-engineer
# *.test.*            @qa-lead
# .github/workflows/  @devops
# SECURITY.md         @security-team @cto
# ========================
```

- [ ] Future plan documented

---

## Self-Check

- [ ] `.github/CODEOWNERS` exists in repo
- [ ] PRs auto-assign reviewers
- [ ] I understand the file path pattern syntax
- [ ] I know how to split ownership for a team

---

**Done? Move to [Day 11 — GitHub Actions CI](day-11-github-actions.md)**
