# Day 09 — Database + API Endpoints + First Release (v0.1.0)

**Time:** 45 min | **Repo:** `mn-crypto-tracker`

---

## Today's Goal

Build the database layer, core API endpoints, and CoinGecko integration. Tag and release v0.1.0.

---

## Phase 1: Database Models (15 min)

### 1. Create database branch

```bash
cd mn-crypto-tracker
git checkout main && git pull
git checkout -b feat/database-models
```

### 2. Create database connection

Create `backend/models/__init__.py` (empty file)

Create `backend/models/database.py`:
```python
"""Database connection and session management."""
from sqlalchemy import create_engine
from sqlalchemy.orm import sessionmaker, DeclarativeBase

from config import settings


class Base(DeclarativeBase):
    """Base class for all models."""
    pass


engine = create_engine(settings.DATABASE_URL, echo=settings.DEBUG)
SessionLocal = sessionmaker(bind=engine)


def get_db():
    """Dependency: yield database session."""
    db = SessionLocal()
    try:
        yield db
    finally:
        db.close()


def init_db():
    """Create all tables."""
    Base.metadata.create_all(bind=engine)
```

### 3. Create models

Create `backend/models/schema.py`:
```python
"""SQLAlchemy models for crypto tracker."""
from datetime import datetime
from sqlalchemy import Column, Integer, String, Float, DateTime, Boolean

from .database import Base


class Cryptocurrency(Base):
    """Tracked cryptocurrency."""
    __tablename__ = "cryptocurrencies"

    id = Column(Integer, primary_key=True, index=True)
    coin_id = Column(String, unique=True, nullable=False)  # coingecko id
    symbol = Column(String, nullable=False)                 # BTC, ETH
    name = Column(String, nullable=False)                   # Bitcoin, Ethereum
    current_price = Column(Float, default=0.0)
    price_change_24h = Column(Float, default=0.0)
    market_cap = Column(Float, default=0.0)
    last_updated = Column(DateTime, default=datetime.utcnow)


class PortfolioEntry(Base):
    """User's portfolio transaction."""
    __tablename__ = "portfolio"

    id = Column(Integer, primary_key=True, index=True)
    coin_id = Column(String, nullable=False)
    symbol = Column(String, nullable=False)
    amount = Column(Float, nullable=False)
    buy_price = Column(Float, nullable=False)       # price per coin at purchase
    transaction_type = Column(String, default="buy") # buy or sell
    notes = Column(String, nullable=True)
    created_at = Column(DateTime, default=datetime.utcnow)


class PriceHistory(Base):
    """Historical price snapshots."""
    __tablename__ = "price_history"

    id = Column(Integer, primary_key=True, index=True)
    coin_id = Column(String, nullable=False)
    price = Column(Float, nullable=False)
    recorded_at = Column(DateTime, default=datetime.utcnow)
```

### 4. Commit and push

```bash
git add backend/models/
git commit -m "feat(models): add database connection and SQLAlchemy models

- Cryptocurrency model for tracking coins
- PortfolioEntry model for buy/sell transactions
- PriceHistory model for historical snapshots
- Database session management with dependency injection"

git push -u origin feat/database-models
gh pr create --title "feat: add database models" --body "Adds SQLAlchemy models for crypto, portfolio, and price history"
gh pr merge --squash --delete-branch
git checkout main && git pull
```

- [ ] Database models merged

---

## Phase 2: API Endpoints (15 min)

### 5. Create API branch

```bash
git checkout -b feat/api-endpoints
```

### 6. Create CoinGecko service

Create `backend/services/__init__.py` (empty file)

Create `backend/services/coingecko.py`:
```python
"""CoinGecko API client for fetching crypto prices."""
import aiohttp
from config import settings


class CoinGeckoService:
    """Fetch cryptocurrency data from CoinGecko API."""

    BASE_URL = settings.COINGECKO_BASE_URL

    async def get_prices(self, coin_ids: list[str]) -> list[dict]:
        """Fetch current prices for given coin IDs."""
        ids = ",".join(coin_ids)
        url = f"{self.BASE_URL}/coins/markets"
        params = {
            "vs_currency": "usd",
            "ids": ids,
            "order": "market_cap_desc",
            "sparkline": "false",
        }

        async with aiohttp.ClientSession() as session:
            async with session.get(url, params=params) as resp:
                if resp.status == 200:
                    return await resp.json()
                return []

    async def search_coins(self, query: str) -> list[dict]:
        """Search for coins by name or symbol."""
        url = f"{self.BASE_URL}/search"
        params = {"query": query}

        async with aiohttp.ClientSession() as session:
            async with session.get(url, params=params) as resp:
                if resp.status == 200:
                    data = await resp.json()
                    return data.get("coins", [])[:10]
                return []


coingecko = CoinGeckoService()
```

### 7. Create portfolio service

Create `backend/services/portfolio.py`:
```python
"""Portfolio business logic."""
from sqlalchemy.orm import Session
from backend.models.schema import PortfolioEntry


def add_transaction(db: Session, coin_id: str, symbol: str,
                    amount: float, buy_price: float,
                    transaction_type: str = "buy",
                    notes: str = None) -> PortfolioEntry:
    """Add a buy/sell transaction to portfolio."""
    entry = PortfolioEntry(
        coin_id=coin_id,
        symbol=symbol.upper(),
        amount=amount,
        buy_price=buy_price,
        transaction_type=transaction_type,
        notes=notes,
    )
    db.add(entry)
    db.commit()
    db.refresh(entry)
    return entry


def get_portfolio(db: Session) -> list[dict]:
    """Get portfolio summary with PnL calculation."""
    entries = db.query(PortfolioEntry).all()
    holdings = {}

    for entry in entries:
        if entry.coin_id not in holdings:
            holdings[entry.coin_id] = {
                "coin_id": entry.coin_id,
                "symbol": entry.symbol,
                "total_amount": 0.0,
                "total_invested": 0.0,
            }
        if entry.transaction_type == "buy":
            holdings[entry.coin_id]["total_amount"] += entry.amount
            holdings[entry.coin_id]["total_invested"] += entry.amount * entry.buy_price
        elif entry.transaction_type == "sell":
            holdings[entry.coin_id]["total_amount"] -= entry.amount

    return list(holdings.values())


def get_transactions(db: Session, coin_id: str = None) -> list[PortfolioEntry]:
    """Get all transactions, optionally filtered by coin."""
    query = db.query(PortfolioEntry)
    if coin_id:
        query = query.filter(PortfolioEntry.coin_id == coin_id)
    return query.order_by(PortfolioEntry.created_at.desc()).all()
```

### 8. Create API routes

Create `backend/api/__init__.py` (empty file)

Create `backend/api/prices.py`:
```python
"""Price API endpoints."""
from fastapi import APIRouter
from backend.services.coingecko import coingecko

router = APIRouter(prefix="/api/prices", tags=["prices"])


@router.get("/")
async def get_prices(coins: str = "bitcoin,ethereum"):
    """Get current prices for specified coins."""
    coin_list = [c.strip() for c in coins.split(",")]
    prices = await coingecko.get_prices(coin_list)
    return {"prices": prices}


@router.get("/search")
async def search_coins(q: str):
    """Search for coins by name or symbol."""
    results = await coingecko.search_coins(q)
    return {"results": results}
```

Create `backend/api/portfolio.py`:
```python
"""Portfolio API endpoints."""
from fastapi import APIRouter, Depends
from pydantic import BaseModel
from sqlalchemy.orm import Session

from backend.models.database import get_db
from backend.services import portfolio as portfolio_service

router = APIRouter(prefix="/api/portfolio", tags=["portfolio"])


class TransactionCreate(BaseModel):
    """Request body for creating a transaction."""
    coin_id: str
    symbol: str
    amount: float
    buy_price: float
    transaction_type: str = "buy"
    notes: str | None = None


@router.get("/")
def get_portfolio(db: Session = Depends(get_db)):
    """Get portfolio summary with holdings."""
    holdings = portfolio_service.get_portfolio(db)
    return {"portfolio": holdings}


@router.post("/transactions")
def add_transaction(tx: TransactionCreate, db: Session = Depends(get_db)):
    """Add a buy/sell transaction."""
    entry = portfolio_service.add_transaction(
        db=db,
        coin_id=tx.coin_id,
        symbol=tx.symbol,
        amount=tx.amount,
        buy_price=tx.buy_price,
        transaction_type=tx.transaction_type,
        notes=tx.notes,
    )
    return {"message": "Transaction added", "id": entry.id}


@router.get("/transactions")
def get_transactions(coin_id: str = None, db: Session = Depends(get_db)):
    """Get transaction history."""
    transactions = portfolio_service.get_transactions(db, coin_id)
    return {"transactions": [
        {
            "id": t.id,
            "coin_id": t.coin_id,
            "symbol": t.symbol,
            "amount": t.amount,
            "buy_price": t.buy_price,
            "type": t.transaction_type,
            "notes": t.notes,
            "date": t.created_at.isoformat(),
        }
        for t in transactions
    ]}
```

### 9. Wire up main.py

Update `backend/main.py`:
```python
"""FastAPI application entry point."""
from fastapi import FastAPI
from fastapi.staticfiles import StaticFiles

from config import settings
from backend.models.database import init_db
from backend.api.prices import router as prices_router
from backend.api.portfolio import router as portfolio_router

app = FastAPI(
    title=settings.APP_NAME,
    version=settings.VERSION,
    description="Real-time crypto portfolio tracker",
)

# Register routes
app.include_router(prices_router)
app.include_router(portfolio_router)


@app.on_event("startup")
async def startup():
    """Initialize database on startup."""
    init_db()


@app.get("/health")
async def health_check():
    """Health check endpoint."""
    return {"status": "healthy", "version": settings.VERSION}
```

### 10. Add a basic test

Create `backend/tests/__init__.py` (empty file)

Create `backend/tests/test_api.py`:
```python
"""API endpoint tests."""
from fastapi.testclient import TestClient
from backend.main import app

client = TestClient(app)


def test_health_check():
    """Health endpoint returns 200."""
    response = client.get("/health")
    assert response.status_code == 200
    assert response.json()["status"] == "healthy"


def test_get_portfolio_empty():
    """Portfolio returns empty list initially."""
    response = client.get("/api/portfolio/")
    assert response.status_code == 200
    assert "portfolio" in response.json()
```

### 11. Commit and push

```bash
git add .
git commit -m "feat(api): add price and portfolio endpoints with CoinGecko integration

- GET /api/prices/ — fetch live crypto prices
- GET /api/prices/search — search coins
- GET /api/portfolio/ — portfolio summary with holdings
- POST /api/portfolio/transactions — add buy/sell
- GET /api/portfolio/transactions — transaction history
- CoinGecko service with async HTTP client
- Portfolio service with PnL calculation
- Basic API tests"

git push -u origin feat/api-endpoints
gh pr create --title "feat: add price and portfolio API endpoints" --body "Core API with CoinGecko integration, portfolio CRUD, and tests"
gh pr merge --squash --delete-branch
git checkout main && git pull
```

- [ ] API endpoints merged

---

## Phase 3: First Release — v0.1.0 (10 min)

### 12. Semantic Versioning

```
MAJOR.MINOR.PATCH → v0.1.0
  |     |     └─ Bug fix (backward compatible)
  |     └─────── New feature (backward compatible)
  └───────────── Breaking change (or pre-1.0 development)
```

> **Learner Tip:** Start at `v0.1.0` (not v1.0.0) for projects in development. `v0.x.x` signals "API may change". `v1.0.0` means "stable, production-ready".

### 13. Tag and release

```bash
git tag -a v0.1.0 -m "v0.1.0 - Core API and database

- FastAPI backend with health check
- Database models (Cryptocurrency, Portfolio, PriceHistory)
- CoinGecko price API integration
- Portfolio CRUD endpoints
- CI pipeline with Python tests
- Project scaffolding with config management"

git push origin v0.1.0
```

### 14. Create GitHub Release

```bash
gh release create v0.1.0 \
  --title "v0.1.0 — Core API & Database" \
  --notes "## What's New
- FastAPI backend with auto-generated Swagger docs
- Live crypto prices via CoinGecko API
- Portfolio tracking (buy/sell transactions)
- SQLite database with SQLAlchemy ORM
- CI pipeline with automated tests

## API Endpoints
- \`GET /health\` — Health check
- \`GET /api/prices/?coins=bitcoin,ethereum\` — Live prices
- \`GET /api/prices/search?q=bitcoin\` — Search coins
- \`GET /api/portfolio/\` — Portfolio summary
- \`POST /api/portfolio/transactions\` — Add transaction
- \`GET /api/portfolio/transactions\` — Transaction history

## Quick Start
\`\`\`bash
pip install -r requirements.txt
python -m uvicorn backend.main:app --reload
# Visit http://localhost:8000/docs
\`\`\`"
```

- [ ] v0.1.0 tag pushed
- [ ] GitHub Release created

#### SWOT — Semantic Versioning

| | |
|---|---|
| **Strengths** | Clear communication of change impact. Users know if an update is safe (patch) or breaking (major). |
| **Weaknesses** | Requires discipline — easy to ship a breaking change as a minor version. |
| **Opportunities** | Automation tools (semantic-release) can auto-bump versions based on commit types. |
| **Threats** | Version inflation — bumping major for every change. Follow semver rules strictly. |

---

## Self-Check

- [ ] Database models with 3 tables
- [ ] 5+ API endpoints working
- [ ] CoinGecko integration
- [ ] Tests passing
- [ ] v0.1.0 tagged and released
- [ ] Release notes on GitHub

---

## Command Explanation Reference

| Command | What It Does | When to Use |
|---------|-------------|-------------|
| `git tag -a v0.1.0 -m "msg"` | Creates annotated tag with message | Marking a release version |
| `git push origin v0.1.0` | Pushes specific tag to GitHub | Publishing a version |
| `git push origin --tags` | Pushes ALL tags | Publishing multiple versions |
| `gh release create` | Creates GitHub Release from tag | Publishing downloadable release |
| `git log v0.1.0..HEAD` | Shows commits since a version | Checking what changed since release |
| `git diff v0.1.0..v0.2.0` | Full diff between versions | Comparing releases |

---

## ASCII Workflow — Day 09 Visual Summary

```
                        DAY 09 OVERVIEW
  ┌──────────────────────────────────────────────────────────┐
  │                                                          │
  │  DATABASE LAYER                                          │
  │  ══════════════                                          │
  │                                                          │
  │  ┌─────────────────┐  ┌─────────────────┐               │
  │  │ Cryptocurrency  │  │ PortfolioEntry  │               │
  │  │ • coin_id       │  │ • coin_id       │               │
  │  │ • symbol        │  │ • amount        │               │
  │  │ • current_price │  │ • buy_price     │               │
  │  │ • market_cap    │  │ • type (buy/sell)│              │
  │  └─────────────────┘  └─────────────────┘               │
  │  ┌─────────────────┐                                     │
  │  │ PriceHistory    │                                     │
  │  │ • coin_id       │                                     │
  │  │ • price         │                                     │
  │  │ • recorded_at   │                                     │
  │  └─────────────────┘                                     │
  │                                                          │
  ├──────────────────────────────────────────────────────────┤
  │                                                          │
  │  API ENDPOINTS                                           │
  │  ═════════════                                           │
  │                                                          │
  │  GET  /health                 → status check             │
  │  GET  /api/prices/            → live crypto prices       │
  │  GET  /api/prices/search      → search coins             │
  │  GET  /api/portfolio/         → portfolio summary        │
  │  POST /api/portfolio/tx       → add buy/sell             │
  │  GET  /api/portfolio/tx       → transaction history      │
  │                                                          │
  │  ┌────────┐  ┌─────────┐  ┌───────────┐  ┌──────────┐  │
  │  │ Client │─▶│ FastAPI │─▶│ Services  │─▶│CoinGecko │  │
  │  │        │  │ Router  │  │ Portfolio │  │  API     │  │
  │  │        │  │         │  │ PnL calc  │  └──────────┘  │
  │  │        │  │         │  │           │─▶┌──────────┐  │
  │  └────────┘  └─────────┘  └───────────┘  │ SQLite   │  │
  │                                          └──────────┘  │
  ├──────────────────────────────────────────────────────────┤
  │                                                          │
  │  VERSIONING                                              │
  │  ══════════                                              │
  │                                                          │
  │  v0.1.0  ◀── we are here (core API)                     │
  │  v0.2.0  ◀── Day 10 (frontend + dashboard)              │
  │  v0.3.0  ◀── Day 11 (WebSocket + real-time)             │
  │  v1.0.0  ◀── Day 13 (production release)                │
  │                                                          │
  │  git tag -a v0.1.0 -m "msg"                             │
  │  git push origin v0.1.0                                  │
  │  gh release create v0.1.0                                │
  │                                                          │
  └──────────────────────────────────────────────────────────┘
```

**Next → [Day 10: Frontend Dashboard + Changelog](day-10-changelog-and-dependabot.md)**
