# Day 18 — Dependabot

**Goal:** Automate dependency updates. Never run outdated/vulnerable packages.

---

## Why This Matters

- Outdated dependencies have known security vulnerabilities
- Manual updates are tedious and forgotten
- Dependabot creates PRs automatically with updates
- You just review and merge

---

## Hands-On Exercise

### Exercise 1: Enable Dependabot on GitHub

1. Go to your `Spotify-lyric-tracker` repo on GitHub
2. Click **Settings** → **Code security and analysis** (left sidebar)
3. Enable:
   - **Dependabot alerts:** ON
   - **Dependabot security updates:** ON

- [ ] Dependabot alerts enabled
- [ ] Dependabot security updates enabled

### Exercise 2: Create Dependabot Config

For your `Spotify-lyric-tracker` repo:

```bash
git checkout main && git pull
git checkout -b chore/add-dependabot
```

Create `.github/dependabot.yml`:

```yaml
version: 2
updates:
  # Keep GitHub Actions up to date
  - package-ecosystem: "github-actions"
    directory: "/"
    schedule:
      interval: "weekly"
      day: "monday"
    commit-message:
      prefix: "ci(deps):"
    labels:
      - "dependencies"
      - "github-actions"

  # If you have npm/package.json (add if applicable)
  # - package-ecosystem: "npm"
  #   directory: "/"
  #   schedule:
  #     interval: "weekly"
  #   commit-message:
  #     prefix: "chore(deps):"
  #   labels:
  #     - "dependencies"
  #   open-pull-requests-limit: 5
```

```bash
git add .github/dependabot.yml
git commit -m "chore(deps): add Dependabot configuration"
git push -u origin chore/add-dependabot
```

Create PR and merge.

- [ ] `dependabot.yml` created and merged

### Exercise 3: Set Up Dependabot for AlphaPulse_test Too

Since `AlphaPulse_test` is a Python project, clone it and add Dependabot:

```bash
cd ~/path/to/
git clone https://github.com/QubitPro/AlphaPulse_test.git
cd AlphaPulse_test
git checkout -b chore/add-dependabot
mkdir -p .github
```

Create `.github/dependabot.yml`:

```yaml
version: 2
updates:
  - package-ecosystem: "pip"
    directory: "/"
    schedule:
      interval: "weekly"
    commit-message:
      prefix: "chore(deps):"
    labels:
      - "dependencies"
      - "python"
    open-pull-requests-limit: 5

  - package-ecosystem: "github-actions"
    directory: "/"
    schedule:
      interval: "weekly"
    commit-message:
      prefix: "ci(deps):"
```

```bash
git add .github/dependabot.yml
git commit -m "chore(deps): add Dependabot for Python dependency updates"
git push -u origin chore/add-dependabot
```

- [ ] Dependabot configured for Python project

### Exercise 4: Review a Dependabot PR

After Dependabot is enabled, it will start creating PRs (usually within a day).

When you see a Dependabot PR:
1. Read what dependency was updated
2. Check the changelog/release notes (Dependabot includes a link)
3. Check if CI passes
4. Merge if everything looks good

- [ ] I understand how to review Dependabot PRs

### Exercise 5: Check Security Alerts

1. Go to your repo → **Security** tab → **Dependabot alerts**
2. Review any vulnerability alerts
3. If Dependabot created a security PR, prioritize merging it

- [ ] Checked Security tab for alerts

---

## Dependabot Schedule Options

```yaml
schedule:
  interval: "daily"        # Every day
  interval: "weekly"       # Once a week
    day: "monday"          # Specific day
  interval: "monthly"      # Once a month
```

---

## Self-Check

- [ ] Dependabot alerts enabled on GitHub
- [ ] `dependabot.yml` config file in repo
- [ ] Commit messages use semantic prefix (`chore(deps):`)
- [ ] I know how to review and merge Dependabot PRs
- [ ] I checked the Security tab

---

**Done? Move to [Day 19 — Merge Strategies](day-19-merge-strategies.md)**
