# Day 10 — Changelog + Dependabot

**Time:** 25 min | **Repo:** `Spotify-lyric-tracker`

---

## Part A: CHANGELOG.md (15 min)

### 1. Create CHANGELOG.md

```bash
git checkout main && git pull
git checkout -b docs/changelog
```

Create `CHANGELOG.md` in project root:

```markdown
# Changelog

All notable changes documented here.
Format: [Keep a Changelog](https://keepachangelog.com/) + [Semantic Versioning](https://semver.org/).

## [Unreleased]

## [1.1.0] - 2026-03-16
### Added
- Dark mode toggle

### Fixed
- Empty lyrics response handling

## [1.0.1] - 2026-03-16
### Fixed
- Handle songs with no lyrics data

## [1.0.0] - 2026-03-16
### Added
- Real-time lyrics display synced to Spotify
- Floating popup window
- Current lyric highlighting with auto-scroll
- Dark-themed UI

[Unreleased]: https://github.com/QubitPro/Spotify-lyric-tracker/compare/v1.1.0...HEAD
[1.1.0]: https://github.com/QubitPro/Spotify-lyric-tracker/compare/v1.0.1...v1.1.0
[1.0.1]: https://github.com/QubitPro/Spotify-lyric-tracker/compare/v1.0.0...v1.0.1
[1.0.0]: https://github.com/QubitPro/Spotify-lyric-tracker/releases/tag/v1.0.0
```

### 2. Push and merge

```bash
git add CHANGELOG.md
git commit -m "docs(changelog): add CHANGELOG.md with release history"
git push -u origin docs/changelog
gh pr create --title "docs: add CHANGELOG.md" --body "Adds changelog following Keep a Changelog format"
gh pr merge --squash --delete-branch
```

- [ ] CHANGELOG.md in repo

### 3. Workflow rule (remember this)

```
Developing → add entries under [Unreleased]
Releasing  → move [Unreleased] to new version section
             create tag → create GitHub Release
```

| Section | Use For |
|---------|---------|
| Added | New features |
| Changed | Modified features |
| Fixed | Bug fixes |
| Removed | Deleted features |
| Security | Security fixes |

---

## Part B: Dependabot (10 min)

### 4. Enable on GitHub

Go to repo → **Settings** → **Code security and analysis**:
- [x] Dependabot alerts: **ON**
- [x] Dependabot security updates: **ON**

- [ ] Alerts enabled

### 5. Create config

```bash
git checkout main && git pull
git checkout -b chore/dependabot
```

Create `.github/dependabot.yml`:

```yaml
version: 2
updates:
  - package-ecosystem: "github-actions"
    directory: "/"
    schedule:
      interval: "weekly"
      day: "monday"
    commit-message:
      prefix: "ci(deps):"
    labels:
      - "dependencies"
```

```bash
git add .github/dependabot.yml
git commit -m "chore(deps): add Dependabot configuration"
git push -u origin chore/dependabot
gh pr create --title "chore: add Dependabot config" --body "Automated dependency updates for GitHub Actions"
gh pr merge --squash --delete-branch
```

- [ ] Dependabot configured
- [ ] Will auto-create PRs for outdated Actions

### 6. Also add to AlphaPulse_test (Python)

```bash
cd ~/path/to/AlphaPulse_test    # Clone first if needed
git checkout -b chore/dependabot
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
```

```bash
git add . && git commit -m "chore(deps): add Dependabot for Python deps"
git push -u origin chore/dependabot
gh pr create --title "chore: add Dependabot" --body "Auto-update Python dependencies"
```

- [ ] Dependabot on Python project too

---

## Self-Check

- [ ] CHANGELOG.md follows Keep a Changelog format
- [ ] I know the develop → release changelog workflow
- [ ] Dependabot enabled and configured
- [ ] Using `gh pr create` and `gh pr merge` fluently

**Next → [Day 11: Merge Strategies](day-11-merge-strategies.md)**
