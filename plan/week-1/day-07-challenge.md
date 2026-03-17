# Day 07 — Week 1 Challenge

**Time:** 45 min | **Repo:** `Spotify-lyric-tracker`

Use EVERY skill from Days 1-6 in one realistic workflow.

---

## The Mission

Add a feature, handle a bug interruption, clean up, and ship.

### Phase 1: Start Feature (10 min)

```bash
git checkout main && git pull origin main
git checkout -b feature/lyrics-cache
```

Make 4 semantic commits:

```bash
echo "// cache storage" >> cache-storage.js
git add . && git commit -m "feat(cache): add cache storage module"

echo "// expiration" >> cache-expiry.js
git add . && git commit -m "feat(cache): implement expiration logic"

echo "// empty check" >> cache-empty.js
git add . && git commit -m "fix(cache): handle empty lyrics edge case"

echo "// tests" >> cache-test.js
git add . && git commit -m "test(cache): add cache unit tests"
```

Push and create a **PR** (your template should auto-fill).

```bash
git push -u origin feature/lyrics-cache
```

> **Learner Tip:** Don't merge this PR yet — you'll update it during the challenge. The PR stays open as your "work in progress" while you handle a bug interruption.

- [ ] `feat(cache): add cache storage module`
- [ ] `feat(cache): implement expiration logic`
- [ ] `fix(cache): handle empty lyrics edge case`
- [ ] `test(cache): add cache unit tests`
- [ ] PR created with template auto-filled

#### SWOT — Feature Branch Workflow

| | |
|---|---|
| **Strengths** | Isolated work — your feature branch doesn't affect main. Multiple features can be developed in parallel on separate branches. |
| **Weaknesses** | Long-lived branches diverge from main over time, leading to merge conflicts. Keep branches short-lived (1-3 days). |
| **Opportunities** | Each branch = one PR = one reviewable unit of work. This maps perfectly to agile tickets/stories. |
| **Threats** | Forgetting to pull main before branching. Always `git pull origin main` first, or your branch starts from stale code. |

### Phase 2: Bug Interruption (10 min)

Simulate uncommitted work and stash it:

```bash
echo "// optimization" >> cache-optimize.js
git add cache-optimize.js
git stash save "WIP: cache optimization"
```

> **Learner Tip:** You MUST `git add` before stashing. `git stash save` only stashes tracked/staged changes by default. New untracked files need to be staged first, or use `git stash -u` to include untracked files.

Create hotfix branch and push as PR (main is protected):

```bash
git checkout main && git pull origin main
git checkout -b hotfix/sync-crash
echo "fix" >> sync-fix.txt
git add . && git commit -m "fix(sync): resolve crash on songs without timestamps"
git push -u origin hotfix/sync-crash
```

Create PR on GitHub → merge it → sync main:

```bash
git checkout main && git pull origin main
```

> **Learner Tip (No cherry-pick needed):** With branch protection, you can't cherry-pick directly to main. Instead, push the hotfix as a PR branch and merge via GitHub. This is the real-world pattern — cherry-pick is for local work, PRs are for shipping to main.

- [ ] Stashed WIP with descriptive name
- [ ] Hotfix PR created and merged
- [ ] Main synced after merge

#### SWOT — Bug Interruption Handling

| | |
|---|---|
| **Strengths** | Stash lets you context-switch instantly without messy WIP commits. Hotfix branches keep urgent fixes separate from feature work. |
| **Weaknesses** | Forgetting to stash before switching branches — git will either block the checkout or carry dirty files to the wrong branch. |
| **Opportunities** | This workflow (stash → fix → pop) is how senior developers handle production emergencies without losing focus on their feature. |
| **Threats** | Stash conflicts when popping — if main changed the same files while you were fixing the bug, the pop may conflict. Resolve like any merge conflict. |

### Phase 3: Resume Feature (5 min)

```bash
git checkout feature/lyrics-cache
git stash pop
```

Make 2 WIP commits (on purpose):

```bash
echo "wip" >> wip1.txt && git add . && git commit -m "WIP" --no-verify
echo "wip" >> wip2.txt && git add . && git commit -m "WIP again" --no-verify
```

> **Learner Tip (`--no-verify`):** Your commit-msg hook from Day 06 blocks non-semantic messages like "WIP". Use `--no-verify` to bypass hooks temporarily. This is legitimate here because you'll clean up with rebase before merging. NEVER use `--no-verify` on final commits.

```bash
git log --oneline -7
```

- [ ] Stash popped successfully
- [ ] 2 WIP commits created (to be cleaned up)

#### SWOT — Intentional WIP Commits

| | |
|---|---|
| **Strengths** | WIP commits save your progress as checkpoints. If something breaks, you can go back. Better than uncommitted code sitting in your working directory. |
| **Weaknesses** | "WIP" and "WIP again" tell nobody anything. These MUST be cleaned up before merging — they're temporary by design. |
| **Opportunities** | Combined with rebase, WIP commits become a powerful workflow: commit often (save progress), rebase before merge (clean history). |
| **Threats** | Forgetting to rebase before merging — WIP commits in main history are unprofessional and make `git log` useless. |

### Phase 4: Clean Up (10 min)

Interactive rebase — squash WIP into clean commits:

```bash
git rebase -i HEAD~6
```

Change to:
```
pick ... feat(cache): add cache storage module
pick ... feat(cache): implement expiration logic
pick ... fix(cache): handle empty lyrics edge case
pick ... test(cache): add cache unit tests
squash ... WIP
squash ... WIP again
```

This merges the WIP commits into the test commit. Keep the message:
```
test(cache): add cache unit tests
```

> **Learner Tip:** Squash goes UPWARD — the squashed commit merges into the `pick` above it. So WIP commits at the bottom squash into the test commit (the last `pick`). You can also rearrange commits to squash them into a different parent.

```bash
git log --oneline -5
```

- [ ] Result: 4 clean semantic commits, zero WIP
- [ ] Force push to update PR: `git push --force origin feature/lyrics-cache`
- [ ] CI passes on PR

> **Learner Tip (`--force` push):** Rebase rewrites commit hashes, so you must force push to update the remote branch. This is SAFE on your own feature branch. NEVER force push to main or shared branches.

#### SWOT — Rebase Cleanup Before Merge

| | |
|---|---|
| **Strengths** | Clean history — reviewers see 4 logical commits, not 6 messy ones. Each commit tells a clear story. |
| **Weaknesses** | Force push is required after rebase, which can be scary. And if someone else is working on the same branch, force push overwrites their work. |
| **Opportunities** | Many teams use "squash and merge" on GitHub, which does this automatically. But knowing manual rebase gives you more control over the final history. |
| **Threats** | Rebasing the wrong number of commits (`HEAD~7` instead of `HEAD~6`) can include commits from other branches. Always check `git log` first. |

### Phase 5: Disaster Recovery (5 min)

"Accidentally" reset:

```bash
git reset --hard HEAD~3
git log --oneline -3
```

Recover with reflog:

```bash
git reflog
git reset --hard <hash-of-test-cache-commit>
git log --oneline -5
```

> **Learner Tip:** By now, reflog should feel routine. The pattern is always: `git reflog` → find the hash → `git reset --hard <hash>`. This works for deleted branches, bad rebases, bad resets — anything where commits were "lost".

- [ ] "Accidentally" lost 3 commits
- [ ] Recovered all commits with reflog
- [ ] All 4 commits verified

#### SWOT — Disaster Recovery in Practice

| | |
|---|---|
| **Strengths** | Reflog recovery is now a practiced skill, not just theory. Under pressure (production is down, code is "lost"), you can calmly recover. |
| **Weaknesses** | Only works for committed code. If you `reset --hard` with uncommitted changes, those changes are permanently gone. |
| **Opportunities** | In a team, being the person who can recover from git disasters makes you invaluable. Most developers panic — you won't. |
| **Threats** | Overconfidence — "I can always recover" is true for commits, but not for uncommitted work. Commit early, commit often. |

### Phase 6: Ship It (5 min)

Force push your recovered branch:

```bash
git push --force origin feature/lyrics-cache
```

Verify on PR page:
- [ ] PR template filled
- [ ] CODEOWNERS assigned reviewer
- [ ] CI green (check Actions tab)

Merge with **Squash and Merge** → Delete branch on GitHub.

```bash
git checkout main && git pull origin main
```

> **Learner Tip (Squash and Merge):** GitHub's "Squash and Merge" button squashes all PR commits into ONE commit on main. This is the easiest way to keep main history clean. You already cleaned up with rebase, so either merge strategy works — but "Squash and Merge" is the team standard.

- [ ] PR merged with Squash and Merge
- [ ] Branch deleted on GitHub
- [ ] Local main synced

#### SWOT — Squash and Merge

| | |
|---|---|
| **Strengths** | One clean commit per PR on main. Even if the PR had 10 messy commits, main stays clean. Simplest approach for teams. |
| **Weaknesses** | Loses the individual commit history on main. If you need to bisect within a PR's changes, you can't. |
| **Opportunities** | Combined with PR descriptions, the squash commit message links back to the PR for full context. Best of both worlds. |
| **Threats** | Developers may stop writing clean commits because "squash will fix it". The discipline of clean commits (Day 01) is still valuable for the PR review process. |

---

## Score Yourself

| Skill | Points | Earned |
|-------|--------|--------|
| All commits semantic | /15 | |
| Stash named + recovered | /10 | |
| Hotfix PR merged | /15 | |
| Rebase cleaned WIP | /20 | |
| Reflog recovery worked | /15 | |
| CI passed on PR | /10 | |
| PR template + CODEOWNERS | /10 | |
| Git aliases used | /5 | |
| **Total** | **/100** | |

**90+:** Ready for Week 2. **Below 70:** Redo weak days.

---

## Week 1 Skills Unlocked

```
[x] Semantic Commits     [x] Git Aliases
[x] Interactive Rebase   [x] Stash + Cherry-Pick
[x] Reflog Recovery      [x] PR Templates
[x] Branch Protection    [x] CODEOWNERS
[x] GitHub Actions CI    [x] Git Hooks
```

---

## SWOT Analysis — Day 07 / Week 1 Overall

### Strengths
- **Complete workflow mastery** — You can create features, handle interruptions, clean up history, recover from disasters, and ship code through PRs
- **Professional habits** — Semantic commits, branch protection, CI, code review — you work like a senior developer
- **Git confidence** — Rebase, reflog, stash, cherry-pick are no longer scary. You've used them all in realistic scenarios
- **Safety everywhere** — Hooks locally, CI remotely, branch protection on main. Three layers of quality gates

### Weaknesses
- **Solo practice limits** — Code review, team conflicts, and collaboration patterns haven't been tested with real teammates yet
- **Muscle memory still building** — These commands need daily use for 2-3 weeks before they become automatic
- **Edge cases** — Complex merge conflicts, multi-branch rebases, and large-scale refactors need more practice
- **Windows quirks** — `cat` vs `type`, `mkdir -p`, `chmod` — some commands behave differently on Windows

### Opportunities
- **Week 2 builds on this** — Draft PRs, releases, changelog, merge strategies, bisect, worktree — all build on Week 1 foundations
- **Open source ready** — You can now contribute to open source projects with confidence in the git workflow
- **Portfolio value** — Your Spotify-lyric-tracker repo shows professional git practices, CI, and branch protection
- **Team readiness** — When you join a team, you'll already know the workflow instead of learning it on the job

### Threats
- **Skill decay** — If you stop using these skills for weeks, they fade. Keep practicing on real projects
- **Overcomplicating simple tasks** — Not every change needs 5 branches and 3 rebases. Match the tool to the task
- **Git is not GitHub** — These skills are git + GitHub specific. GitLab and Bitbucket have similar but different UIs
- **Merge conflict anxiety** — Even with all these skills, merge conflicts still happen. The key is staying calm and reading carefully

---

## Command Explanation Reference

### Phase 1 — Feature Branch

| Command | What It Does | When to Use |
|---------|-------------|-------------|
| `git checkout -b feature/name` | Create and switch to feature branch | Starting any new feature |
| `git push -u origin feature/name` | Push branch and set tracking | First push of new branch, creates PR-able branch |

### Phase 2 — Bug Interruption

| Command | What It Does | When to Use |
|---------|-------------|-------------|
| `git add <file>` then `git stash save "name"` | Stage and stash with label | Pausing current work for urgent task |
| `git stash -u` | Stash including untracked files | When new files aren't staged yet |
| `git checkout main && git pull origin main` | Sync with latest main | Before creating hotfix branch |
| `git checkout -b hotfix/name` | Create hotfix branch from main | Urgent fix needed on main |

### Phase 3 — Resume Work

| Command | What It Does | When to Use |
|---------|-------------|-------------|
| `git checkout feature/name` | Switch back to feature branch | Resuming after interruption |
| `git stash pop` | Restore stashed changes and delete stash | Ready to continue where you left off |
| `git commit --no-verify` | Skip all git hooks | Temporary WIP commits that will be rebased |

### Phase 4 — Cleanup

| Command | What It Does | When to Use |
|---------|-------------|-------------|
| `git rebase -i HEAD~N` | Interactive rebase last N commits | Squashing WIP, reordering, cleaning up |
| `git push --force origin branch` | Force push after rebase | Updating remote branch after history rewrite |

### Phase 5 — Recovery

| Command | What It Does | When to Use |
|---------|-------------|-------------|
| `git reset --hard HEAD~N` | Delete last N commits (simulating disaster) | Testing recovery skills (or actual accident) |
| `git reflog` | Show all HEAD movements | Finding the hash to recover to |
| `git reset --hard <hash>` | Jump to specific commit | Recovering lost commits |

### Phase 6 — Ship

| Command | What It Does | When to Use |
|---------|-------------|-------------|
| `git push --force origin branch` | Update PR with cleaned-up commits | After rebase + recovery |
| Squash and Merge (GitHub) | Merges all PR commits as one on main | Cleanest merge strategy for PRs |
| Delete branch (GitHub) | Removes merged branch | Cleanup after merge — keeps branch list tidy |
| `git checkout main && git pull` | Sync local after merge | Always do after merging a PR on GitHub |

---

## ASCII Workflow — Day 07 Visual Summary

```
                     DAY 07 — THE COMPLETE WORKFLOW
  ┌──────────────────────────────────────────────────────────────┐
  │                                                              │
  │  PHASE 1: START FEATURE                                      │
  │  ═══════════════════════                                     │
  │                                                              │
  │  main: ○──○──○                                               │
  │               │                                              │
  │               └──▶ feature/lyrics-cache: ○──○──○──○          │
  │                    feat  feat  fix   test                    │
  │                      │                                       │
  │                      └──▶ Push + Create PR                   │
  │                                                              │
  ├──────────────────────────────────────────────────────────────┤
  │                                                              │
  │  PHASE 2: BUG INTERRUPTION                                   │
  │  ═════════════════════════                                   │
  │                                                              │
  │  feature/lyrics-cache: ○──○──○──○  + uncommitted work        │
  │                                        │                     │
  │                                   git stash save             │
  │                                        │                     │
  │  Stash: [WIP: cache optimization]      ▼                     │
  │                                   Working dir clean!         │
  │                                        │                     │
  │  main: ○──○──○                         │                     │
  │               │                        │                     │
  │               └──▶ hotfix/sync-crash: ○                      │
  │                    "fix(sync): ..."    │                      │
  │                                       └──▶ PR → Merge        │
  │                                                              │
  │  main: ○──○──○──●  (hotfix merged)                           │
  │                                                              │
  ├──────────────────────────────────────────────────────────────┤
  │                                                              │
  │  PHASE 3: RESUME + MESSY COMMITS                             │
  │  ═══════════════════════════════                              │
  │                                                              │
  │  feature/lyrics-cache: ○──○──○──○                            │
  │                                 │                            │
  │                            git stash pop                     │
  │                                 │                            │
  │                        ○──○──○──○──○──○                      │
  │                        feat feat fix test WIP WIP            │
  │                                          ^^^                 │
  │                                          messy!              │
  │                                                              │
  ├──────────────────────────────────────────────────────────────┤
  │                                                              │
  │  PHASE 4: REBASE CLEANUP                                     │
  │  ═══════════════════════                                     │
  │                                                              │
  │  BEFORE: ○──○──○──○──○──○                                    │
  │          feat feat fix test WIP WIP                          │
  │                                                              │
  │  git rebase -i HEAD~6  (squash WIP into test)                │
  │                                                              │
  │  AFTER:  ○──○──○──○                                          │
  │          feat feat fix test  ◀── clean!                      │
  │                                                              │
  ├──────────────────────────────────────────────────────────────┤
  │                                                              │
  │  PHASE 5: DISASTER + RECOVERY                                │
  │  ════════════════════════════                                │
  │                                                              │
  │  ○──○──○──○   (4 clean commits)                              │
  │       │                                                      │
  │       │  git reset --hard HEAD~3  (DISASTER!)                │
  │       ▼                                                      │
  │  ○              (3 commits "gone")                           │
  │       │                                                      │
  │       │  git reflog → git reset --hard <hash>                │
  │       ▼                                                      │
  │  ○──○──○──○   (RECOVERED!)                                   │
  │                                                              │
  ├──────────────────────────────────────────────────────────────┤
  │                                                              │
  │  PHASE 6: SHIP IT                                            │
  │  ════════════════                                            │
  │                                                              │
  │  feature/lyrics-cache: ○──○──○──○                            │
  │                                 │                            │
  │                        git push --force                      │
  │                                 │                            │
  │                           ┌─────┴─────┐                      │
  │                           │    PR     │                      │
  │                           │ ✅ CI     │                      │
  │                           │ ✅ Template│                     │
  │                           │ ✅ Review  │                     │
  │                           └─────┬─────┘                      │
  │                                 │                            │
  │                        Squash and Merge                      │
  │                                 │                            │
  │  main: ○──○──○──●   (one clean squash commit)               │
  │                                                              │
  │  Delete branch → git checkout main && git pull               │
  │                                                              │
  ├──────────────────────────────────────────────────────────────┤
  │                                                              │
  │  WEEK 1 COMPLETE!                                            │
  │                                                              │
  │  ┌────────────┐  ┌────────────┐  ┌────────────┐             │
  │  │ Day 1      │  │ Day 2      │  │ Day 3      │             │
  │  │ Semantic   │  │ Interactive│  │ Stash +    │             │
  │  │ Commits    │  │ Rebase     │  │ Cherry-Pick│             │
  │  └────────────┘  └────────────┘  └────────────┘             │
  │  ┌────────────┐  ┌────────────┐  ┌────────────┐             │
  │  │ Day 4      │  │ Day 5      │  │ Day 6      │             │
  │  │ Reflog     │  │ PR + Branch│  │ CI + Git   │             │
  │  │ Recovery   │  │ Protection │  │ Hooks      │             │
  │  └────────────┘  └────────────┘  └────────────┘             │
  │  ┌──────────────────────────────────────┐                    │
  │  │ Day 7: ALL SKILLS IN ONE WORKFLOW    │                    │
  │  └──────────────────────────────────────┘                    │
  │                                                              │
  └──────────────────────────────────────────────────────────────┘
```

**Next → [Week 2 Day 08: Draft PRs + Code Review](../week-2/day-08-drafts-and-review.md)**
