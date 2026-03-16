# Day 17 — CHANGELOG.md

**Goal:** Maintain a professional changelog that documents every release.

---

## Why This Matters

- Users want to know what changed before updating
- Contributors want to see project progress
- It's a standard in professional open-source projects
- Shows you take your project seriously

---

## The Format: Keep a Changelog

Standard format from [keepachangelog.com](https://keepachangelog.com):

```
## [Version] - Date

### Added       (new features)
### Changed     (changes to existing features)
### Deprecated  (features that will be removed)
### Removed     (features that were removed)
### Fixed       (bug fixes)
### Security    (security fixes)
```

---

## Hands-On Exercise

### Exercise 1: Create CHANGELOG.md

In your `Spotify-lyric-tracker` repo:

```bash
git checkout main && git pull
git checkout -b docs/add-changelog
```

Create `CHANGELOG.md` in the project root:

```markdown
# Changelog

All notable changes to Spotify Lyric Tracker will be documented in this file.

The format is based on [Keep a Changelog](https://keepachangelog.com/),
and this project adheres to [Semantic Versioning](https://semver.org/).

## [Unreleased]
### Added
- (upcoming changes go here)

## [1.2.0] - 2026-03-16
### Added
- Keyboard shortcuts for playback control (Space, Arrow keys, ESC)
- Dark mode toggle

### Fixed
- Lyrics not loading for songs with special characters
- Popup position on multi-monitor setups

### Changed
- Improved lyric sync speed by 30%
- Reduced memory usage

## [1.0.1] - 2026-03-15
### Fixed
- Handle songs with no lyrics data gracefully

## [1.0.0] - 2026-03-14
### Added
- Real-time lyrics display synced to Spotify playback
- Floating popup window showing current lyric
- Current lyric highlighting with auto-scroll
- Dark-themed UI design
- MIT License

[Unreleased]: https://github.com/QubitPro/Spotify-lyric-tracker/compare/v1.2.0...HEAD
[1.2.0]: https://github.com/QubitPro/Spotify-lyric-tracker/compare/v1.0.1...v1.2.0
[1.0.1]: https://github.com/QubitPro/Spotify-lyric-tracker/compare/v1.0.0...v1.0.1
[1.0.0]: https://github.com/QubitPro/Spotify-lyric-tracker/releases/tag/v1.0.0
```

- [ ] `CHANGELOG.md` created with proper format

### Exercise 2: Commit and Merge

```bash
git add CHANGELOG.md
git commit -m "docs(changelog): add CHANGELOG.md with release history"
git push -u origin docs/add-changelog
```

Create PR and merge.

- [ ] CHANGELOG pushed and merged

### Exercise 3: Practice Adding a New Entry

Simulate adding a new version entry. Edit `CHANGELOG.md`:

Move current `[Unreleased]` items to a new version:

```markdown
## [Unreleased]
### Added
- (upcoming changes go here)

## [1.3.0] - 2026-03-17
### Added
- Multi-language lyrics support
- Lyrics font size adjustment

### Fixed
- Memory leak when switching songs rapidly
```

Update the links at the bottom:
```markdown
[Unreleased]: https://github.com/QubitPro/Spotify-lyric-tracker/compare/v1.3.0...HEAD
[1.3.0]: https://github.com/QubitPro/Spotify-lyric-tracker/compare/v1.2.0...v1.3.0
```

- [ ] New version entry added correctly
- [ ] Links updated at bottom

### Exercise 4: Changelog Workflow Rule

From now on, follow this rule:

```
1. While developing: add entries under [Unreleased]
2. When releasing: move [Unreleased] items to new version section
3. Create git tag
4. Create GitHub Release
5. Changelog, tag, and release all match
```

- [ ] I understand the workflow: code → changelog → tag → release

---

## Quick Reference

| Section | Use For |
|---------|---------|
| Added | Brand new features |
| Changed | Modifications to existing features |
| Deprecated | Features marked for future removal |
| Removed | Features that were deleted |
| Fixed | Bug fixes |
| Security | Security-related fixes |

---

## Self-Check

- [ ] CHANGELOG.md exists in my repo
- [ ] Format follows Keep a Changelog standard
- [ ] Each version has a date
- [ ] Comparison links at the bottom are correct
- [ ] I know to add entries under [Unreleased] while developing

---

**Done? Move to [Day 18 — Dependabot](day-18-dependabot.md)**
