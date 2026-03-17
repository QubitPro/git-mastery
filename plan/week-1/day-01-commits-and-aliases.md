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

> **Learner Tip:** If aliases aren't set up yet, you'll configure them first. Run `git config --global -l | grep alias` to see what you have.

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

> **Learner Tip:** Notice how each commit does ONE thing with a clear message. The format is `type(scope): what you did`. Think of `scope` as which part of the project you touched.

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

> **Learner Tip:** `git amend` is your "oops, forgot to include that" button. `git undo` is your "let me redo that commit" button. Both only work on your LAST commit — don't try these on older ones.

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

> **Learner Tip:** Always check `git staged` before committing. This prevents accidentally committing files you didn't intend to include.

- [ ] I review staged changes before every commit

### 6. Push your branch to GitHub

```bash
git push -u origin practice/semantic-commits
```

> **Learner Tip:** Don't forget this step! Your work only exists locally until you push. The `-u` flag links your local branch to the remote so future `git push` works without extra args.

- [ ] Branch is visible on GitHub

---

## Self-Check

- [ ] I know all 8 commit types
- [ ] Made 5 semantic commits
- [ ] Used `amend`, `undo`, `staged` aliases
- [ ] My `git log` looks professional
- [ ] Pushed branch to GitHub

---

## SWOT Analysis — Semantic Commits & Aliases

### Strengths
- **Readable history** — Anyone can scan `git log` and instantly understand what changed and why
- **Automated changelogs** — Tools like `standard-version` and `semantic-release` can auto-generate changelogs from commit types
- **Faster code review** — Reviewers can prioritize: `fix` commits need careful review, `style` commits are low-risk
- **Aliases save time** — `git st` vs `git status` adds up over hundreds of daily uses

### Weaknesses
- **Learning curve** — Choosing the right type (`fix` vs `refactor`?) takes practice
- **Overhead on small projects** — For personal throwaway scripts, strict conventions feel heavy
- **Team alignment needed** — Everyone must agree on the format, or it becomes inconsistent

### Opportunities
- **CI/CD integration** — Use commit types to trigger different pipelines (e.g., `feat` → deploy, `docs` → rebuild docs site)
- **Career signal** — Clean git history shows professionalism in interviews and open-source contributions
- **Auto-versioning** — `feat` = minor bump, `fix` = patch bump — fully automated releases

### Threats
- **Over-engineering messages** — Spending 5 minutes crafting the perfect commit message for a one-line change
- **False consistency** — Labeling everything `chore` because you're unsure — defeats the purpose
- **Tool lock-in** — Some teams enforce commit linting so strictly that it slows down rapid prototyping

---

## Command Explanation Reference

| Command | What It Does | When to Use |
|---------|-------------|-------------|
| `git checkout -b <branch>` | Creates a new branch and switches to it | Starting any new feature or practice exercise |
| `git add .` | Stages ALL changed/new files in the current directory | When you want to commit everything you changed |
| `git add <file>` | Stages a specific file only | When you want to commit only certain files |
| `git commit -m "msg"` | Creates a commit with the given message | After staging files you want to save |
| `git log --oneline -N` | Shows the last N commits, one line each | Quick check of recent history |
| `git commit --amend` (`git amend`) | Adds staged changes to the LAST commit and lets you edit its message | Forgot a file or made a typo in the last commit |
| `git reset HEAD~1` (`git undo`) | Undoes the last commit but keeps the changes as unstaged files | Want to redo your last commit differently |
| `git diff --cached` (`git staged`) | Shows what's currently staged (ready to commit) | Always run before `git commit` to double-check |
| `git push -u origin <branch>` | Pushes branch to GitHub and sets up tracking | First time pushing a new branch |
| `echo "text" >> file` | Appends text to a file (creates it if it doesn't exist) | Quick way to create/modify files for practice |

> **Key Insight:** `git add` + `git commit` is a two-step process by design. Staging (`add`) lets you choose exactly what goes into each commit. This is what makes clean, focused commits possible.

---

## ASCII Workflow — Day 01 Visual Summary

```
                        DAY 01 OVERVIEW
  ┌─────────────────────────────────────────────────────────┐
  │                                                         │
  │   SEMANTIC COMMIT FORMAT                                │
  │   ══════════════════════                                │
  │                                                         │
  │   type(scope): subject                                  │
  │   ────┬──────  ───┬───                                  │
  │       │           └── what you did (imperative mood)    │
  │       └── which part of the project                     │
  │                                                         │
  │   feat ──── new feature     fix ───── bug fix           │
  │   docs ──── documentation   style ─── formatting        │
  │   refactor─ restructure     perf ──── performance       │
  │   test ──── tests           chore ─── config/deps       │
  │                                                         │
  ├─────────────────────────────────────────────────────────┤
  │                                                         │
  │   THE TWO-STEP COMMIT PROCESS                           │
  │   ═══════════════════════════                            │
  │                                                         │
  │   Working Dir        Staging Area        Repository     │
  │   ┌──────────┐       ┌──────────┐       ┌──────────┐   │
  │   │ cache.js │─add──▶│ cache.js │─commit▶│ commit 1 │   │
  │   │ dark.css │       │          │       │          │   │
  │   │ fix.js   │       │          │       │          │   │
  │   └──────────┘       └──────────┘       └──────────┘   │
  │                                                         │
  │   git add .    ──▶  stages ALL files                    │
  │   git add file ──▶  stages ONE file                     │
  │   git staged   ──▶  shows what's staged (review!)       │
  │   git commit   ──▶  saves staged files as a commit      │
  │                                                         │
  ├─────────────────────────────────────────────────────────┤
  │                                                         │
  │   AMEND & UNDO                                          │
  │   ═════════════                                         │
  │                                                         │
  │   git amend (forgot a file?):                           │
  │   ┌────────┐    ┌────────┐                              │
  │   │commit 5│ +  │new file│ ──▶ │commit 5│ (updated)     │
  │   └────────┘    └────────┘     └────────┘               │
  │                                                         │
  │   git undo (redo last commit?):                         │
  │   ┌────────┐         ┌──────────┐   ┌────────┐         │
  │   │commit 5│ ──undo─▶│ files as │──▶│commit 5│ (new)   │
  │   └────────┘         │ unstaged │   └────────┘         │
  │                      └──────────┘                       │
  │                                                         │
  ├─────────────────────────────────────────────────────────┤
  │                                                         │
  │   BRANCH + PUSH                                         │
  │   ═════════════                                         │
  │                                                         │
  │   Local                          GitHub (Remote)        │
  │   ┌──────────────────┐           ┌──────────────┐       │
  │   │ practice/        │──push -u─▶│ origin/      │       │
  │   │ semantic-commits │           │ practice/    │       │
  │   │                  │           │ semantic-    │       │
  │   │ ○─○─○─○─○─○      │           │ commits     │       │
  │   └──────────────────┘           └──────────────┘       │
  │                                                         │
  │   Work lives ONLY locally until you push!               │
  │                                                         │
  └─────────────────────────────────────────────────────────┘
```

**Next → [Day 02: Interactive Rebase](day-02-rebase.md)**
