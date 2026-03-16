# Day 01 — Semantic Commits

**Goal:** Never write a meaningless commit message again.

---

## Why This Matters

Bad commits:
```
"fixed stuff"
"update"
"asdfg"
"final version"
"final final version"
```

Elite commits:
```
feat(auth): add JWT token validation
fix(api): resolve timeout on large payloads
docs(readme): add setup instructions for Windows
perf(db): optimize user query with index
```

Recruiters, teammates, and future-you will judge your commit history. Make it count.

---

## The Format


```
<type>(<scope>): <subject>
```

### Types (memorize these)

| Type | When to Use | Example |
|------|-------------|---------|
| `feat` | New feature | `feat(player): add lyrics popup window` |
| `fix` | Bug fix | `fix(sync): resolve timing drift on long songs` |
| `docs` | Documentation | `docs(readme): add installation steps` |
| `style` | Formatting only (no logic change) | `style(css): fix button alignment` |
| `refactor` | Code restructure (no behavior change) | `refactor(api): extract token refresh logic` |
| `perf` | Performance improvement | `perf(render): cache DOM queries` |
| `test` | Adding/fixing tests | `test(parser): add edge case for empty lyrics` |
| `chore` | Dependencies, configs, tooling | `chore(deps): update spotify-web-api to v5` |

### Scope
- The part of the project you changed
- Keep it short: `auth`, `api`, `ui`, `db`, `config`, `player`, `lyrics`

### Subject
- Start with lowercase verb: `add`, `fix`, `update`, `remove`, `refactor`
- No period at the end
- Under 50 characters

---

## Hands-On Exercise

### Setup
Open your terminal and navigate to your `Spotify-lyric-tracker` repo:
```bash
cd ~/path/to/Spotify-lyric-tracker
```

### Exercise 1: Rewrite Your Mental Model

Look at your recent commits:
```bash
git log --oneline -10
```

- [ ] **Write down** what each commit SHOULD have been named using semantic format
- [ ] Example: if you see `"added popup"` → it should be `feat(ui): add floating lyrics popup window`

### Exercise 2: Make 5 Practice Commits

Create a test branch (so you don't mess up main):
```bash
git checkout -b practice/semantic-commits
```

Now make 5 small changes and commit each with proper semantic format:

- [ ] **Commit 1 (docs):** Add a comment to any file
  ```bash
  # Edit any file, add a comment
  git add <file>
  git commit -m "docs(code): add inline comment explaining sync logic"
  ```

- [ ] **Commit 2 (style):** Fix any formatting (indentation, spacing)
  ```bash
  git add <file>
  git commit -m "style(html): fix indentation in popup template"
  ```

- [ ] **Commit 3 (chore):** Add or update .gitignore
  ```bash
  echo "node_modules/" >> .gitignore
  git add .gitignore
  git commit -m "chore(config): add node_modules to gitignore"
  ```

- [ ] **Commit 4 (feat):** Add ANY small feature or placeholder
  ```bash
  git add <file>
  git commit -m "feat(lyrics): add placeholder for lyrics caching"
  ```

- [ ] **Commit 5 (fix):** Fix any small thing (typo, wrong value, etc.)
  ```bash
  git add <file>
  git commit -m "fix(readme): correct installation command typo"
  ```

### Exercise 3: Admire Your Work

```bash
git log --oneline -5
```

- [ ] Check: Does every commit clearly tell you WHAT changed and WHY?
- [ ] Check: Can you tell the type of change without reading the code?

### Exercise 4: Multi-line Commit (Advanced)

For bigger changes, add a body:
```bash
git commit -m "feat(player): add real-time lyric highlighting

- Highlight current lyric line based on timestamp
- Auto-scroll popup to keep current line visible
- Add smooth CSS transition for highlight effect

Closes #12"
```

- [ ] Try writing one multi-line commit with a body explaining the details

---

## Rules to Follow From Now On

1. **EVERY commit** uses semantic format — no exceptions
2. Start with the type — it forces you to think about what you're doing
3. Keep subject under 50 chars
4. If you can't describe it in one line, your commit is too big — split it

---

## Self-Check

- [ ] I can name all 8 commit types from memory
- [ ] I made 5 semantic commits on my practice branch
- [ ] I understand the `type(scope): subject` format
- [ ] My `git log --oneline` looks professional

---

## Cleanup

Keep the practice branch for now (we'll use it tomorrow):
```bash
git checkout main
```

**Done? Move to [Day 02 — Git Aliases](day-02-git-aliases.md)**
