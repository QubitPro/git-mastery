# Day 01 — Semantic Commits + Aliases

**Time:** 30 min | **Repo:** `Spotify-lyric-tracker`

---

## Semantic Commit Format

```
<type>(<scope>): <subject>
```

| Type | Use | Example |
|------|-----|---------|
| `feat` | New feature | `feat(player): add lyrics popup` |
| `fix` | Bug fix | `fix(sync): resolve timing drift` |
| `docs` | Documentation | `docs(readme): add install steps` |
| `style` | Formatting only | `style(css): fix alignment` |
| `refactor` | Restructure code | `refactor(api): extract token logic` |
| `perf` | Performance | `perf(render): cache DOM queries` |
| `test` | Tests | `test(parser): add edge cases` |
| `chore` | Config/deps | `chore(deps): update spotify-api` |

---

## Do This Now

### 1. Check your aliases (already set up)

```bash
cd ~/path/to/Spotify-lyric-tracker
git st
git visual
git last
```

- [ ] All aliases work

### 2. Create practice branch, make 5 semantic commits

```bash
git checkout -b practice/semantic-commits
```

```bash
echo "// cache module" >> cache.js
git add . && git commit -m "feat(cache): add lyrics caching module"

echo "/* dark styles */" >> dark.css
git add . && git commit -m "style(ui): add dark mode base styles"

echo "node_modules/" >> .gitignore
git add . && git commit -m "chore(config): update gitignore"

echo "// fix null" >> fix.js
git add . && git commit -m "fix(lyrics): handle null lyrics response"

echo "# Install" >> INSTALL.md
git add . && git commit -m "docs(setup): add installation guide"
```

### 3. Review your work

```bash
git log --oneline -5
```

- [ ] Every commit clearly shows type + scope + what changed
- [ ] No "WIP", "update", or "fix stuff" messages

### 4. Practice `amend` and `undo`

```bash
# Amend: add forgotten file to last commit
echo "# more" >> INSTALL.md
git add . && git amend
git last

# Undo: undo last commit, keep changes
git undo
git st
git commit -m "docs(setup): add installation guide with details"
```

- [ ] `git amend` works
- [ ] `git undo` works

### 5. Use `staged` before committing

```bash
echo "test" >> test.txt
echo "test2" >> test2.txt
git add test.txt
git staged          # Only test.txt shows
git add test2.txt
git staged          # Both show
git commit -m "test: add test files"
```

- [ ] I review staged changes before every commit

---

## Self-Check

- [ ] I know all 8 commit types
- [ ] Made 5 semantic commits
- [ ] Used `amend`, `undo`, `staged` aliases
- [ ] My `git log` looks professional

**Next → [Day 02: Interactive Rebase](day-02-rebase.md)**
