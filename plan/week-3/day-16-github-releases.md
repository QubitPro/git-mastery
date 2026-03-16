# Day 16 — GitHub Releases

**Goal:** Create a professional GitHub Release with description, changelog, and assets.

---

## Why This Matters

Tags are just markers. Releases are the public-facing announcement:
- Show what changed
- Provide download links
- Auto-notify watchers
- Look professional to users/recruiters

---

## Hands-On Exercise

### Exercise 1: Create a Release via GitHub UI

1. Go to your `Spotify-lyric-tracker` repo on GitHub
2. Click **"Releases"** (right sidebar) → **"Create a new release"**
3. Fill in:

**Tag:** Choose existing `v1.0.0` (or create new)

**Title:** `v1.0.0 — Initial Release`

**Description:**
```markdown
## What's New

First stable release of Spotify Lyric Tracker!

### Features
- Real-time lyrics display synced to Spotify playback
- Floating popup window showing current lyric
- Current lyric highlighting with auto-scroll
- Sleek dark-themed UI

### How to Use
1. Clone this repository
2. Open `index.html` in your browser
3. Connect your Spotify account
4. Play a song and watch lyrics appear!

### Requirements
- Spotify Premium account
- Modern web browser (Chrome, Firefox, Edge)
- Active internet connection

---
Full changelog: https://github.com/QubitPro/Spotify-lyric-tracker/commits/v1.0.0
```

4. Click **"Publish release"**

- [ ] Release published on GitHub
- [ ] Release page shows formatted description
- [ ] Release appears on the repo's main page

### Exercise 2: Create a Release via Command Line

Using GitHub CLI (`gh`):

```bash
# If you don't have gh installed:
# Windows: winget install GitHub.cli
# Then: gh auth login

# Create release for v1.2.0
gh release create v1.2.0 \
  --title "v1.2.0 — Keyboard Shortcuts" \
  --notes "## What's New

### Features
- **Keyboard Shortcuts:** Control playback without clicking
  - Space: Play/Pause
  - Arrow Left/Right: Seek backward/forward
  - ESC: Close popup

### Bug Fixes
- Fixed lyrics not loading for songs with special characters
- Fixed popup position on multi-monitor setups

### Improvements
- 30% faster lyric sync
- Reduced memory usage"
```

- [ ] Release created via command line
- [ ] Shows up on GitHub Releases page

### Exercise 3: Release with Pre-release Flag

For beta/testing releases:

```bash
gh release create v2.0.0-beta.1 \
  --title "v2.0.0 Beta 1" \
  --notes "## Beta Release - Testing Only

### Breaking Changes
- New configuration format (see migration guide)

### New Features
- Multi-language lyrics support
- Spotify playlist integration

**WARNING:** This is a pre-release. Do not use in production." \
  --prerelease
```

- [ ] Pre-release created (shows "Pre-release" badge)
- [ ] Understand the difference between release and pre-release

### Exercise 4: Auto-Generate Release Notes

GitHub can auto-generate notes from PR titles:

1. Go to **Create Release**
2. Click **"Generate release notes"** button
3. GitHub auto-fills based on merged PRs since last release

- [ ] Tried auto-generated release notes
- [ ] Understand they come from PR titles (another reason for good PR titles!)

### Exercise 5: Add Release Assets (Optional)

If your project has downloadable files:

```bash
# Create a zip of your project
git archive --format=zip HEAD > spotify-lyric-tracker-v1.2.0.zip

# Attach to release
gh release upload v1.2.0 spotify-lyric-tracker-v1.2.0.zip
```

- [ ] Asset attached to release (optional)

---

## Release Naming Convention

```
v1.0.0           → Stable release
v1.0.0-alpha.1   → Alpha (very early, unstable)
v1.0.0-beta.1    → Beta (feature complete, testing)
v1.0.0-rc.1      → Release Candidate (almost ready)
```

---

## Self-Check

- [ ] Created a release via GitHub UI
- [ ] Created a release via `gh` CLI
- [ ] Release has a professional description
- [ ] I understand pre-release vs stable release
- [ ] I tried auto-generate release notes

---

**Done? Move to [Day 17 — CHANGELOG.md](day-17-changelog.md)**
