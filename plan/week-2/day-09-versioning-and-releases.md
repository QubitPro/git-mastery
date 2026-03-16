# Day 09 — Semantic Versioning + GitHub Releases

**Time:** 30 min | **Repo:** `Spotify-lyric-tracker`

---

## Version Format

```
MAJOR.MINOR.PATCH → v2.3.5
  |     |     └─ Bug fix (backward compatible)
  |     └─────── New feature (backward compatible)
  └───────────── Breaking change
```

| Change | Bump |
|--------|------|
| Bug fix | 1.0.0 → 1.0.1 |
| New feature | 1.0.1 → 1.1.0 |
| Breaking change | 1.1.0 → 2.0.0 |

---

## Do This Now

### 1. Tag your first version

```bash
cd ~/path/to/Spotify-lyric-tracker
git checkout main && git pull

git tag -a v1.0.0 -m "v1.0.0 - Initial stable release

- Real-time Spotify lyric tracking
- Floating popup window
- Lyric highlighting and auto-scroll"

git tag                    # List tags
git show v1.0.0            # Show details
git push origin v1.0.0     # Push to GitHub
```

- [ ] Tag created and pushed

### 2. Create GitHub Release with `gh`

```bash
gh release create v1.0.0 \
  --title "v1.0.0 — Initial Release" \
  --notes "## Features
- Real-time lyrics display synced to Spotify
- Floating popup with current lyric highlighted
- Auto-scroll to keep current line visible
- Dark-themed UI

## How to Use
1. Clone the repo
2. Open index.html
3. Connect Spotify and play a song"
```

- [ ] Release visible at GitHub → Releases

### 3. Simulate version bumps

```bash
# PATCH — bug fix
echo "// fix" >> patch-fix.txt
git add . && git commit -m "fix(lyrics): handle empty lyrics response"
git tag -a v1.0.1 -m "v1.0.1 - Fix empty lyrics"

# MINOR — new feature
echo "// dark mode" >> dark.txt
git add . && git commit -m "feat(ui): add dark mode"
git tag -a v1.1.0 -m "v1.1.0 - Dark mode support"

git push origin --tags
```

### 4. Create release for v1.1.0

```bash
gh release create v1.1.0 \
  --title "v1.1.0 — Dark Mode" \
  --notes "## New
- Dark mode toggle

## Fixes
- Empty lyrics handling"
```

- [ ] Multiple versions tagged
- [ ] Releases created via CLI

### 5. Pre-release (beta)

```bash
gh release create v2.0.0-beta.1 \
  --title "v2.0.0 Beta 1" \
  --notes "Beta release for testing. Do not use in production." \
  --prerelease
```

- [ ] Pre-release shows "Pre-release" badge

### 6. Compare versions

```bash
git log v1.0.0..v1.1.0 --oneline    # What changed between versions
git diff v1.0.0..v1.1.0              # Full diff
```

- [ ] I can compare versions

---

## Self-Check

- [ ] Created annotated tags with messages
- [ ] Pushed tags to GitHub
- [ ] Created releases via `gh release create`
- [ ] Created a pre-release
- [ ] Know MAJOR vs MINOR vs PATCH

**Next → [Day 10: Changelog + Dependabot](day-10-changelog-and-dependabot.md)**
