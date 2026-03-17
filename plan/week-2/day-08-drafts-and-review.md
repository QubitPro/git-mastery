# Day 08 — Project Setup + Draft PRs + Code Review

**Time:** 45 min | **New Repo:** `mn-crypto-tracker`

---

## Today's Goal

Create the MN Crypto Portfolio Tracker repo from scratch with production-grade architecture, set up the project foundation via Draft PR, and practice code review.

---

## Phase 1: Create the Repo (15 min)

### 1. Create repo on GitHub

```bash
gh repo create mn-crypto-tracker --public --description "Real-time crypto portfolio tracker with live prices, PnL analysis, and dashboard" --clone
cd mn-crypto-tracker
```

> **Learner Tip:** `gh repo create --clone` creates the repo on GitHub AND clones it locally in one command. If you prefer, create on GitHub's website first then `git clone`.

### 2. Initialize project structure

```bash
git checkout -b setup/project-init
```

Create the full directory structure. On Windows, create folders via VS Code's explorer or run:

```bash
mkdir backend
mkdir backend\api
mkdir backend\models
mkdir backend\services
mkdir backend\utils
mkdir backend\tests
mkdir frontend
mkdir frontend\templates
mkdir frontend\static
mkdir frontend\static\css
mkdir frontend\static\js
mkdir config
mkdir docs
mkdir scripts
mkdir data
mkdir .github\workflows
```

### 3. Create core configuration files

Create `requirements.txt`:
```
fastapi==0.115.0
uvicorn==0.30.0
sqlalchemy==2.0.30
aiohttp==3.9.5
websockets==12.0
python-dotenv==1.0.1
pydantic==2.7.0
jinja2==3.1.4
pytest==8.2.0
httpx==0.27.0
```

Create `config/__init__.py`:
```python
from .settings import settings

__all__ = ["settings"]
```

Create `config/settings.py`:
```python
"""Application configuration."""
import os
from dotenv import load_dotenv

load_dotenv()


class Settings:
    """Global settings loaded from environment."""

    APP_NAME: str = "MN Crypto Tracker"
    VERSION: str = "0.1.0"
    DEBUG: bool = os.getenv("DEBUG", "false").lower() == "true"

    # Database
    DATABASE_URL: str = os.getenv("DATABASE_URL", "sqlite:///data/crypto.db")

    # External APIs
    COINGECKO_BASE_URL: str = "https://api.coingecko.com/api/v3"
    PRICE_UPDATE_INTERVAL: int = 30  # seconds

    # Server
    HOST: str = os.getenv("HOST", "0.0.0.0")
    PORT: int = int(os.getenv("PORT", "8000"))


settings = Settings()
```

Create `.env.example`:
```
DEBUG=true
DATABASE_URL=sqlite:///data/crypto.db
HOST=0.0.0.0
PORT=8000
```

Create `.gitignore`:
```
__pycache__/
*.py[cod]
*.egg-info/
dist/
build/
venv/
.venv/
.env
data/*.db
.vscode/
.idea/
.DS_Store
Thumbs.db
logs/*.log
```

Create `backend/__init__.py` (empty file)

Create `backend/main.py`:
```python
"""FastAPI application entry point."""
from fastapi import FastAPI
from fastapi.staticfiles import StaticFiles
from fastapi.templating import Jinja2Templates

from config import settings

app = FastAPI(
    title=settings.APP_NAME,
    version=settings.VERSION,
    description="Real-time crypto portfolio tracker",
)


@app.get("/health")
async def health_check():
    """Health check endpoint."""
    return {"status": "healthy", "version": settings.VERSION}
```

Create `README.md`:
```markdown
# MN Crypto Portfolio Tracker

![CI](https://github.com/QubitPro/mn-crypto-tracker/actions/workflows/ci.yml/badge.svg)

Real-time cryptocurrency portfolio tracker with live prices, PnL analysis, and interactive dashboard.

## Features

- Live cryptocurrency prices via CoinGecko API
- Portfolio tracking with buy/sell transactions
- Real-time PnL (Profit & Loss) calculation
- WebSocket-powered live price updates
- Interactive charts and dashboard
- REST API with full CRUD operations

## Tech Stack

| Layer | Technology |
|-------|-----------|
| Backend | FastAPI (Python 3.11+) |
| Database | SQLAlchemy + SQLite |
| Real-time | WebSocket + aiohttp |
| Frontend | HTML/CSS/JS + Chart.js |
| Testing | pytest + httpx |
| CI/CD | GitHub Actions |

## Quick Start

\```bash
git clone https://github.com/QubitPro/mn-crypto-tracker.git
cd mn-crypto-tracker
python -m venv venv
source venv/bin/activate  # Windows: venv\Scripts\activate
pip install -r requirements.txt
cp .env.example .env
python -m uvicorn backend.main:app --reload
\```

## API Docs

Visit `http://localhost:8000/docs` after starting the server.

## License

MIT
```

### 4. Commit and push as Draft PR

```bash
git add .
git commit -m "chore(init): scaffold project with FastAPI backend, config, and README"
git push -u origin setup/project-init
```

Create a **Draft PR**:

```bash
gh pr create --title "chore: initialize MN Crypto Tracker project" --body "## Description
Scaffolds the full project structure:
- FastAPI backend with health check
- Config module with env support
- Requirements with pinned versions
- README with architecture docs

## Status
- [x] Directory structure
- [x] Config module
- [x] FastAPI entry point
- [ ] CI pipeline
- [ ] PR template + CODEOWNERS" --draft
```

- [ ] Draft PR created (shows "Draft" badge)

#### SWOT — Draft PRs

| | |
|---|---|
| **Strengths** | Early feedback before heavy investment. Visible progress. Merge button disabled prevents premature merging. |
| **Weaknesses** | Can go stale if not actively updated. Creates noise with many open drafts. |
| **Opportunities** | Use PR description as live checklist. Great for complex features that span multiple days. |
| **Threats** | Staying in draft too long. Convert to Ready once core work is done. |

---

## Phase 2: Add GitHub Config (10 min)

### 5. Add CI, PR template, CODEOWNERS, Dependabot

Create `.github/pull_request_template.md`:
```markdown
## Description
<!-- What changed and why? -->

## Type of Change
- [ ] Bug fix
- [ ] New feature
- [ ] Breaking change
- [ ] Docs / Config

## Testing Done
- [ ] Unit tests pass
- [ ] Manual testing completed
- [ ] API endpoints verified

## Checklist
- [ ] Self-review completed
- [ ] No debug statements left
- [ ] No hardcoded secrets
- [ ] CHANGELOG updated (if applicable)
```

Create `.github/CODEOWNERS`:
```
*                   @QubitPro
backend/            @QubitPro
frontend/           @QubitPro
.github/            @QubitPro
config/             @QubitPro
```

Create `.github/workflows/ci.yml`:
```yaml
name: CI

on:
  push:
    branches: [main]
  pull_request:
    branches: [main]

jobs:
  lint-and-test:
    runs-on: ubuntu-latest
    timeout-minutes: 10
    steps:
      - uses: actions/checkout@v4

      - name: Set up Python
        uses: actions/setup-python@v5
        with:
          python-version: "3.11"

      - name: Install dependencies
        run: |
          python -m pip install --upgrade pip
          pip install -r requirements.txt

      - name: Check for debug statements
        run: |
          if grep -rn "print(" --include="*.py" backend/ 2>/dev/null | grep -v "# noqa"; then
            echo "WARNING: print() found in backend/"
          else
            echo "Clean"
          fi

      - name: Run tests
        run: python -m pytest backend/tests/ -v --tb=short 2>/dev/null || echo "No tests yet"
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

  - package-ecosystem: "github-actions"
    directory: "/"
    schedule:
      interval: "weekly"
    commit-message:
      prefix: "ci(deps):"
```

```bash
git add .github/
git commit -m "ci(github): add CI pipeline, PR template, CODEOWNERS, and Dependabot"
git push
```

- [ ] CI running on PR
- [ ] All GitHub config in place

---

## Phase 3: Code Review (10 min)

### 6. Convert Draft to Ready

```bash
gh pr ready
```

### 7. Post elite self-review on the PR

```markdown
### Self-Review — Architecture Decisions

**What's Good**
- Clean separation: api/ (routes) → services/ (logic) → models/ (data)
- Config via environment variables with sensible defaults
- Pinned dependency versions for reproducible builds

**Concerns**
- CoinGecko free tier: 10-30 req/min — need caching strategy
- WebSocket reconnection logic needed for frontend
- Should we use alembic for DB migrations?

**Next Steps**
- Day 09: Database models + API endpoints → v0.1.0
- Day 10: Frontend dashboard + changelog
- Day 11: WebSocket + merge strategies
```

- [ ] Self-review posted

### 8. Use suggestion feature

On "Files changed" tab → click `+` next to any line → suggest an improvement → submit review.

- [ ] Code suggestion created and applied

### 9. Merge

```bash
gh pr merge --squash --delete-branch
git checkout main && git pull
```

- [ ] Foundation merged to main

---

## Phase 4: Branch Protection (5 min)

### 10. Set up Rulesets

GitHub → Settings → Rules → Rulesets → New branch ruleset:

```
Ruleset Name: main
Enforcement: Active
Target: Include default branch

[x] Restrict deletions
[x] Require a pull request before merging
[x] Require status checks to pass → "lint-and-test"
[x] Block force pushes
```

- [ ] Branch protection active

---

## Self-Check

- [ ] Repo `mn-crypto-tracker` created on GitHub
- [ ] Production-grade project structure
- [ ] Draft PR → Ready → Merged
- [ ] Self-review posted
- [ ] CI, PR template, CODEOWNERS, Dependabot configured
- [ ] Branch protection active

---

## Command Explanation Reference

| Command | What It Does | When to Use |
|---------|-------------|-------------|
| `gh repo create --public --clone` | Creates GitHub repo and clones locally | Starting a new project |
| `gh pr create --draft` | Creates work-in-progress PR | Early feedback before completing |
| `gh pr ready` | Converts Draft to Ready | Work is complete |
| `gh pr merge --squash --delete-branch` | Squash merge + cleanup | Standard feature merge |
| `pip install -r requirements.txt` | Install Python dependencies | After clone or updating deps |

---

## ASCII Workflow — Day 08 Visual Summary

```
                        DAY 08 OVERVIEW
  ┌──────────────────────────────────────────────────────────┐
  │                                                          │
  │  PROJECT ARCHITECTURE                                    │
  │  ════════════════════                                    │
  │                                                          │
  │  mn-crypto-tracker/                                      │
  │  ├── backend/                                            │
  │  │   ├── main.py           ◀── FastAPI entry point       │
  │  │   ├── api/              ◀── Route handlers            │
  │  │   ├── models/           ◀── Database schemas          │
  │  │   ├── services/         ◀── Business logic            │
  │  │   ├── utils/            ◀── Helpers                   │
  │  │   └── tests/            ◀── Test suite                │
  │  ├── frontend/                                           │
  │  │   ├── templates/        ◀── HTML (Jinja2)             │
  │  │   └── static/           ◀── CSS + JS + Charts         │
  │  ├── config/               ◀── Settings + env            │
  │  └── .github/              ◀── CI + PR template          │
  │                                                          │
  │  Request Flow:                                           │
  │  ┌────────┐  ┌───────┐  ┌─────────┐  ┌──────────┐      │
  │  │ Client │─▶│  API  │─▶│ Service │─▶│ DB / API │      │
  │  └────────┘  └───────┘  └─────────┘  └──────────┘      │
  │       ▲                       │                          │
  │       └──── WebSocket ◀───────┘                          │
  │                                                          │
  ├──────────────────────────────────────────────────────────┤
  │                                                          │
  │  DRAFT PR → READY → MERGE                                │
  │  ════════════════════════                                 │
  │                                                          │
  │  ┌────────────┐   ┌────────────┐   ┌────────────┐       │
  │  │  Draft PR  │──▶│  Ready PR  │──▶│  Merged    │       │
  │  │ (can't     │   │ (review +  │   │  to main   │       │
  │  │  merge)    │   │  CI pass)  │   │            │       │
  │  └────────────┘   └────────────┘   └────────────┘       │
  │    gh pr create     gh pr ready    gh pr merge           │
  │    --draft                         --squash              │
  │                                                          │
  └──────────────────────────────────────────────────────────┘
```

**Next → [Day 09: Database + API + First Release](day-09-versioning-and-releases.md)**
