# Day 13 — Final Challenge: v1.0.0 Production Release

**Time:** 60 min | **Repo:** `mn-crypto-tracker`

Use EVERY skill from both weeks. This is your graduation test.

---

## Mission: Bug Fix + Feature + v1.0.0 Release

### Task 1: Bug Report → Hotfix → Patch Release (15 min)

#### Create an Issue

```bash
gh issue create --title "[Bug] Portfolio PnL shows NaN when price is zero" \
  --body "## Bug Report
When a coin's price drops to $0 (delisted), the PnL calculation returns NaN.

## Steps to Reproduce
1. Add a transaction for a delisted coin
2. View portfolio — PnL shows NaN

## Expected
PnL should show -100% loss, not NaN"
```

#### Fix it

```bash
git checkout main && git pull
git checkout -b hotfix/pnl-nan
```

Create the fix in `backend/services/portfolio.py` — add a zero-price check in the PnL calculation:

```python
# Add this function to portfolio.py
def calculate_pnl(invested: float, current_value: float) -> dict:
    """Calculate PnL with zero-price safety."""
    if invested == 0:
        return {"pnl": 0.0, "pnl_percent": 0.0}
    pnl = current_value - invested
    pnl_percent = (pnl / invested) * 100
    return {"pnl": round(pnl, 2), "pnl_percent": round(pnl_percent, 2)}
```

```bash
git add . && git commit -m "fix(portfolio): handle zero-price coins in PnL calculation

Prevents NaN when a coin price is zero (delisted).
Returns -100% loss instead of NaN.

Closes #<issue-number>"

git push -u origin hotfix/pnl-nan
gh pr create --title "fix: handle zero-price PnL calculation" --body "Fixes #<number>"
gh pr merge --rebase --delete-branch
```

#### Patch release

```bash
git checkout main && git pull
git tag -a v0.4.1 -m "v0.4.1 - Fix PnL NaN for zero-price coins"
git push origin v0.4.1

gh release create v0.4.1 \
  --title "v0.4.1 — PnL Fix" \
  --notes "### Fixed
- Portfolio PnL no longer shows NaN for delisted/zero-price coins"
```

- [ ] Issue created
- [ ] Hotfix merged with rebase
- [ ] v0.4.1 patch released
- [ ] Issue auto-closed by "Closes #N"

---

### Task 2: Feature → Draft PR → Review → Ship (25 min)

#### Create Issue

```bash
gh issue create --title "[Feature] Add seed data script for demo" \
  --body "## Feature Request
Create a script that seeds the database with sample transactions for demo purposes.
Should include BTC, ETH, SOL purchases at various prices."
```

#### Build the feature

```bash
git checkout main && git pull
git checkout -b feat/seed-data
```

Create `scripts/seed_data.py`:
```python
"""Seed database with sample portfolio data for demo."""
import sys
import os
sys.path.insert(0, os.path.dirname(os.path.dirname(os.path.abspath(__file__))))

from backend.models.database import SessionLocal, init_db
from backend.services.portfolio import add_transaction


SAMPLE_TRANSACTIONS = [
    {"coin_id": "bitcoin", "symbol": "BTC", "amount": 0.5, "buy_price": 42000, "notes": "Initial BTC purchase"},
    {"coin_id": "bitcoin", "symbol": "BTC", "amount": 0.3, "buy_price": 38000, "notes": "DCA buy"},
    {"coin_id": "ethereum", "symbol": "ETH", "amount": 5.0, "buy_price": 2200, "notes": "ETH position"},
    {"coin_id": "ethereum", "symbol": "ETH", "amount": 2.0, "buy_price": 1800, "notes": "ETH dip buy"},
    {"coin_id": "solana", "symbol": "SOL", "amount": 50, "buy_price": 95, "notes": "SOL entry"},
    {"coin_id": "cardano", "symbol": "ADA", "amount": 5000, "buy_price": 0.45, "notes": "ADA long-term"},
]


def seed():
    """Insert sample transactions."""
    init_db()
    db = SessionLocal()
    try:
        for tx in SAMPLE_TRANSACTIONS:
            add_transaction(db, **tx)
        print(f"Seeded {len(SAMPLE_TRANSACTIONS)} transactions")
    finally:
        db.close()


if __name__ == "__main__":
    seed()
```

Create `scripts/run_server.py`:
```python
"""Run the development server."""
import uvicorn


if __name__ == "__main__":
    uvicorn.run(
        "backend.main:app",
        host="0.0.0.0",
        port=8000,
        reload=True,
    )
```

#### Draft PR first

```bash
git add scripts/
git commit -m "feat(scripts): add seed data and server run scripts"
git push -u origin feat/seed-data

gh pr create --title "feat: add seed data script for demo" \
  --body "WIP — adds sample portfolio transactions for demo" \
  --draft
```

#### Add more commits

Create `backend/tests/test_services.py`:
```python
"""Service layer tests."""
from backend.services.portfolio import calculate_pnl


def test_pnl_positive():
    """PnL is positive when value > invested."""
    result = calculate_pnl(invested=1000, current_value=1500)
    assert result["pnl"] == 500
    assert result["pnl_percent"] == 50.0


def test_pnl_negative():
    """PnL is negative when value < invested."""
    result = calculate_pnl(invested=1000, current_value=700)
    assert result["pnl"] == -300
    assert result["pnl_percent"] == -30.0


def test_pnl_zero_invested():
    """PnL handles zero investment."""
    result = calculate_pnl(invested=0, current_value=0)
    assert result["pnl"] == 0.0
```

```bash
git add backend/tests/
git commit -m "test(services): add PnL calculation tests"

echo "# Contribution Guide
1. Fork the repo
2. Create feature branch
3. Run tests: pytest backend/tests/ -v
4. Create PR with description" > CONTRIBUTING.md
git add CONTRIBUTING.md
git commit -m "docs(contrib): add contribution guide"

git push
```

#### Interactive rebase if needed

```bash
git log --oneline -3
# If messy, clean up:
# git rebase -i HEAD~3
```

#### Convert to Ready, self-review, merge

```bash
gh pr ready
```

Post self-review on the PR:
```markdown
### Self-Review
- Seed script adds realistic demo data (BTC, ETH, SOL, ADA)
- PnL tests cover positive, negative, and zero edge cases
- CONTRIBUTING.md guides future contributors
```

```bash
gh pr merge --squash --delete-branch
git checkout main && git pull
```

- [ ] Draft PR → Ready → Merged
- [ ] Tests added
- [ ] CONTRIBUTING.md added
- [ ] Self-review posted

---

### Task 3: Disaster Recovery (5 min)

- [ ] Delete the feature branch:
  ```bash
  git branch -D feat/seed-data 2>/dev/null
  ```

- [ ] "Accidentally" reset main back 2 commits:
  ```bash
  git reset --hard HEAD~2
  ```

- [ ] Recover with reflog:
  ```bash
  git reflog
  git reset --hard <correct-hash>
  ```

- [ ] Verify everything is back:
  ```bash
  git log --oneline -5
  ```

- [ ] Create worktree to verify a file exists:
  ```bash
  git worktree add ../verify-release main
  ls ../verify-release/scripts/seed_data.py
  git worktree remove ../verify-release
  ```

- [ ] All skills working

---

### Task 4: v1.0.0 Production Release (10 min)

#### Update CHANGELOG.md

Add the final entries under a new `[1.0.0]` section:

```markdown
## [1.0.0] - 2026-03-22
### Added
- Portfolio analytics (total value, best/worst performer, PnL, diversity)
- CSV portfolio export
- Seed data script for demo
- Server run script
- Contribution guide
- PnL calculation tests

### Fixed
- PnL NaN for zero-price/delisted coins
- Empty portfolio ZeroDivisionError in analytics
```

```bash
git checkout -b release/v1.0.0
git add CHANGELOG.md
git commit -m "docs(changelog): update for v1.0.0 release"
git push -u origin release/v1.0.0
gh pr create --title "release: v1.0.0 production release" \
  --body "## v1.0.0 — First Stable Release

Full-stack crypto portfolio tracker with:
- Live prices via CoinGecko
- Portfolio tracking with PnL
- WebSocket real-time updates
- Interactive dashboard
- REST API with Swagger docs
- CI/CD pipeline"

# Use merge commit for releases
gh pr merge --merge
git checkout main && git pull
```

#### Tag and release

```bash
git tag -a v1.0.0 -m "v1.0.0 - First stable production release

Features:
- FastAPI backend with REST API
- Live crypto prices via CoinGecko
- WebSocket real-time price streaming
- Portfolio tracking with PnL calculation
- Interactive dark-themed dashboard
- Chart.js price visualizations
- SQLite database with SQLAlchemy ORM
- CSV portfolio export
- CI/CD with GitHub Actions
- Comprehensive test suite"

git push origin v1.0.0

gh release create v1.0.0 \
  --title "v1.0.0 — MN Crypto Tracker" \
  --notes "## MN Crypto Portfolio Tracker — First Stable Release

### Features
- Live cryptocurrency prices via CoinGecko API
- Portfolio tracking with buy/sell transactions
- Real-time PnL (Profit & Loss) calculation
- WebSocket-powered live price streaming
- Interactive dark-themed dashboard with Chart.js
- REST API with auto-generated Swagger docs
- CSV portfolio export
- Seed data script for demo

### Tech Stack
- FastAPI + SQLAlchemy + WebSocket
- HTML/CSS/JS + Chart.js
- GitHub Actions CI/CD
- pytest test suite

### Quick Start
\`\`\`bash
git clone https://github.com/QubitPro/mn-crypto-tracker.git
cd mn-crypto-tracker
pip install -r requirements.txt
python scripts/seed_data.py
python scripts/run_server.py
# Visit http://localhost:8000
\`\`\`

### Version History
- v0.1.0 — Core API & Database
- v0.2.0 — Frontend Dashboard
- v0.3.0 — WebSocket Real-time
- v0.4.0 — Analytics & Export
- v0.4.1 — PnL Fix
- **v1.0.0 — Production Release**"
```

- [ ] v1.0.0 tagged and released
- [ ] Release notes comprehensive
- [ ] CHANGELOG updated

---

## Final Repo Audit

Check your `mn-crypto-tracker` on GitHub:

### Files
- [ ] `backend/main.py` — FastAPI app
- [ ] `backend/api/` — Price, portfolio, WebSocket routes
- [ ] `backend/models/` — Database + SQLAlchemy models
- [ ] `backend/services/` — CoinGecko, portfolio, cache, analytics, export
- [ ] `backend/tests/` — API + service tests
- [ ] `frontend/templates/index.html` — Dashboard
- [ ] `frontend/static/css/style.css` — Dark theme
- [ ] `frontend/static/js/app.js` — Frontend logic + WebSocket
- [ ] `config/settings.py` — Environment config
- [ ] `scripts/seed_data.py` — Demo data
- [ ] `scripts/run_server.py` — Dev server
- [ ] `.github/workflows/ci.yml` — CI pipeline
- [ ] `.github/pull_request_template.md` — PR template
- [ ] `.github/CODEOWNERS` — Auto-assign reviewers
- [ ] `.github/dependabot.yml` — Auto-update deps
- [ ] `CHANGELOG.md` — Full release history
- [ ] `CONTRIBUTING.md` — Contributor guide
- [ ] `README.md` — With CI badge + architecture docs
- [ ] `.gitignore` — Python + IDE + env
- [ ] `requirements.txt` — Pinned dependencies

### GitHub Settings
- [ ] Branch protection on main
- [ ] CI required to merge
- [ ] 5+ releases (v0.1.0 through v1.0.0)
- [ ] CI badge in README
- [ ] Issues created and closed

### Git History
- [ ] All commits semantic
- [ ] No WIP/garbage in main
- [ ] Used merge commit (releases), squash (features), rebase (hotfixes)
- [ ] Clean, professional history

---

## Score

| Category | Points | Earned |
|----------|--------|--------|
| Bug: Issue → hotfix → patch release | /10 | |
| Feature: Draft PR → review → squash merge | /10 | |
| All commits semantic + hooks validated | /10 | |
| Correct merge strategies used | /10 | |
| Version tags correct (patch/minor/major) | /10 | |
| GitHub Releases with notes | /10 | |
| CHANGELOG + CONTRIBUTING docs | /5 | |
| Disaster recovery (reflog) | /10 | |
| Worktree used | /5 | |
| Full repo audit passes | /10 | |
| Tests written and passing | /5 | |
| Project actually runs | /5 | |
| **Total** | **/100** | |

**90+:** Elite GitHub Developer.
**70-89:** Strong. Review weak areas.
**Below 70:** Redo Days 08-12 exercises.

---

## You're Done!

```
  ┌──────────────────────────────────────────────┐
  │                                              │
  │   WEEK 1: Git Core + Workflow    ✅ COMPLETE  │
  │   WEEK 2: Full Project + Release ✅ COMPLETE  │
  │                                              │
  │   Skills Mastered: 25+ techniques            │
  │   Tools: git, gh, GitHub Actions, FastAPI    │
  │   Project: Production crypto tracker         │
  │   Releases: v0.1.0 → v1.0.0                 │
  │                                              │
  │   Status: ELITE GITHUB DEVELOPER             │
  │                                              │
  └──────────────────────────────────────────────┘
```

### What You Built

| Version | What | Git Skills Used |
|---------|------|-----------------|
| v0.1.0 | Core API + Database | Draft PR, code review, branch protection |
| v0.2.0 | Frontend Dashboard | Squash merge, changelog, tagging |
| v0.3.0 | WebSocket Real-time | Merge strategies (3 types), release notes |
| v0.4.0 | Analytics + Export | Bisect, worktree, parallel development |
| v0.4.1 | PnL Bug Fix | Issue → hotfix → patch release |
| v1.0.0 | Production Release | Full workflow: issue → branch → PR → release |

### Keep Going
- Deploy to a real server (Railway, Render, or VPS)
- Add authentication (JWT tokens)
- Add more coins and historical charts
- Apply this workflow to ALL your repos
- Explore: GitHub Projects, Packages, signed commits, GitOps
- Teach someone else — best way to solidify knowledge

**Your repos now look like they're maintained by a senior engineer.**

---

## ASCII Workflow — Day 13 / Complete Project

```
                     COMPLETE PROJECT TIMELINE
  ┌──────────────────────────────────────────────────────────┐
  │                                                          │
  │  v0.1.0        v0.2.0       v0.3.0      v0.4.0  v1.0.0 │
  │    │             │            │            │       │     │
  │    ●─────────────●────────────●────────────●───●───●     │
  │    │             │            │            │   │   │     │
  │  Core API    Frontend    WebSocket    Analytics │ Prod   │
  │  Database    Dashboard   Real-time    Export  v0.4.1    │
  │  Config      Charts      Cache       Bisect  PnL fix   │
  │  CI/CD       Changelog   3 merge     Worktree          │
  │  PR template            strategies                      │
  │                                                          │
  ├──────────────────────────────────────────────────────────┤
  │                                                          │
  │  FULL ARCHITECTURE                                       │
  │  ═════════════════                                       │
  │                                                          │
  │  ┌────────────────────────────────────────┐              │
  │  │            FRONTEND                    │              │
  │  │  Dashboard + Charts + WebSocket client │              │
  │  └───────────────┬────────────────────────┘              │
  │                  │ HTTP + WebSocket                      │
  │  ┌───────────────┴────────────────────────┐              │
  │  │            FASTAPI                     │              │
  │  │  /api/prices  /api/portfolio  /ws      │              │
  │  └───────┬───────────────┬────────────────┘              │
  │          │               │                               │
  │  ┌───────┴──────┐ ┌─────┴────────┐                      │
  │  │   Services   │ │   Database   │                      │
  │  │  CoinGecko   │ │  SQLAlchemy  │                      │
  │  │  Portfolio   │ │  SQLite      │                      │
  │  │  Analytics   │ │              │                      │
  │  │  Cache       │ │              │                      │
  │  │  Export      │ │              │                      │
  │  └──────────────┘ └──────────────┘                      │
  │                                                          │
  ├──────────────────────────────────────────────────────────┤
  │                                                          │
  │  GIT WORKFLOW USED                                       │
  │  ═════════════════                                       │
  │                                                          │
  │  Issue → Branch → Commits → PR → CI → Review → Merge    │
  │                                                          │
  │  Hotfix:  --rebase   (linear)                            │
  │  Feature: --squash   (clean)                             │
  │  Release: --merge    (preserved)                         │
  │                                                          │
  │  Tag → Release → CHANGELOG                               │
  │                                                          │
  └──────────────────────────────────────────────────────────┘
```
