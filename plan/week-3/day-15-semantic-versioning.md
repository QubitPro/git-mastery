# Day 15 — Semantic Versioning

**Goal:** Version your project like a professional. Know when to bump MAJOR, MINOR, or PATCH.

---

## The Format

```
MAJOR.MINOR.PATCH
  v2.3.5
  |  |  └─ PATCH: bug fix (backward compatible)
  |  └──── MINOR: new feature (backward compatible)
  └─────── MAJOR: breaking change (not backward compatible)
```

---

## When to Bump What

| Change | Version Bump | Example |
|--------|-------------|---------|
| Fixed a typo | PATCH: 1.0.0 → 1.0.1 | `fix(ui): correct button text` |
| Fixed a bug | PATCH: 1.0.1 → 1.0.2 | `fix(sync): resolve timing issue` |
| Added dark mode | MINOR: 1.0.2 → 1.1.0 | `feat(ui): add dark mode` |
| Added keyboard shortcuts | MINOR: 1.1.0 → 1.2.0 | `feat(ui): add shortcuts` |
| Removed old API | MAJOR: 1.2.0 → 2.0.0 | Existing users must update |
| Changed config format | MAJOR: 2.0.0 → 3.0.0 | Old configs won't work |

**Key rules:**
- PATCH resets to 0 on MINOR bump: 1.0.3 → 1.1.0
- PATCH and MINOR reset to 0 on MAJOR bump: 1.5.3 → 2.0.0
- Start at `v1.0.0` for first stable release
- Use `v0.x.x` for pre-release / development

---

## Hands-On Exercise

### Exercise 1: Tag Your First Version

```bash
cd ~/path/to/Spotify-lyric-tracker
git checkout main && git pull
```

Create your first version tag:
```bash
git tag -a v1.0.0 -m "v1.0.0 - Initial stable release

Features:
- Real-time Spotify lyric tracking
- Floating popup window
- Lyric highlighting and auto-scroll"
```

Verify:
```bash
git tag                  # List all tags
git show v1.0.0          # Show tag details
```

- [ ] Tag `v1.0.0` created
- [ ] Tag has a descriptive message

### Exercise 2: Push Tags to GitHub

```bash
git push origin v1.0.0
```

Or push ALL tags:
```bash
git push origin --tags
```

Go to GitHub → your repo → look for "Releases" or "Tags" section.

- [ ] Tag appears on GitHub

### Exercise 3: Simulate Version Bumps

Practice deciding which bump to use:

```bash
# Bug fix → PATCH
echo "// bugfix" >> fix.txt
git add . && git commit -m "fix(lyrics): handle songs with no lyrics data"
git tag -a v1.0.1 -m "v1.0.1 - Fix empty lyrics handling"

# New feature → MINOR
echo "// new feature" >> feature.txt
git add . && git commit -m "feat(ui): add dark mode toggle"
git tag -a v1.1.0 -m "v1.1.0 - Add dark mode support"

# Another feature → MINOR
echo "// shortcuts" >> shortcuts.txt
git add . && git commit -m "feat(ui): add keyboard shortcuts"
git tag -a v1.2.0 -m "v1.2.0 - Add keyboard shortcuts"
```

Check your version history:
```bash
git tag --sort=-version:refname
# v1.2.0
# v1.1.0
# v1.0.1
# v1.0.0
```

- [ ] Correct version bumps applied
- [ ] PATCH for bug fix, MINOR for features

### Exercise 4: Quiz Yourself

What version bump for each? Write your answers:

1. Fixed a CSS alignment bug: `v1.2.0 → ________`
2. Added export to PDF feature: `v1.2.1 → ________`
3. Changed the config file format (old format won't work): `v1.3.0 → ________`
4. Updated documentation: `v2.0.0 → ________`
5. Performance optimization (no API change): `v2.0.0 → ________`

<details>
<summary>Answers</summary>

1. v1.2.1 (PATCH — bug fix)
2. v1.3.0 (MINOR — new feature)
3. v2.0.0 (MAJOR — breaking change)
4. v2.0.1 (PATCH — docs are non-breaking)
5. v2.0.1 (PATCH — perf improvement, no API change)

</details>

### Exercise 5: See Diff Between Versions

```bash
# What changed between v1.0.0 and v1.2.0?
git log v1.0.0..v1.2.0 --oneline

# Full diff between versions
git diff v1.0.0..v1.2.0
```

- [ ] I can compare versions using tags

---

## Self-Check

- [ ] I understand MAJOR.MINOR.PATCH
- [ ] I created annotated tags with messages
- [ ] I pushed tags to GitHub
- [ ] I know when to bump MAJOR vs MINOR vs PATCH
- [ ] I can compare versions with `git diff` and `git log`

---

**Done? Move to [Day 16 — GitHub Releases](day-16-github-releases.md)**
