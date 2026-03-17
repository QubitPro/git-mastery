# Day 11 — WebSocket Real-time + Merge Strategies

**Time:** 45 min | **Repo:** `mn-crypto-tracker`

---

## Today's Goal

Add WebSocket real-time price updates and practice all three merge strategies with real code branches.

---

## Phase 1: WebSocket Real-time Prices (20 min)

### 1. Create WebSocket branch

```bash
git checkout main && git pull
git checkout -b feat/websocket-realtime
```

### 2. Create WebSocket endpoint

Create `backend/api/websocket.py`:
```python
"""WebSocket endpoint for real-time price updates."""
import asyncio
import json
from fastapi import APIRouter, WebSocket, WebSocketDisconnect
from backend.services.coingecko import coingecko

router = APIRouter()

# Track connected clients
connected_clients: list[WebSocket] = []


@router.websocket("/ws/prices")
async def websocket_prices(websocket: WebSocket):
    """Stream live prices to connected clients."""
    await websocket.accept()
    connected_clients.append(websocket)

    try:
        while True:
            prices = await coingecko.get_prices(
                ["bitcoin", "ethereum", "solana", "cardano"]
            )

            price_data = [
                {
                    "symbol": p.get("symbol", ""),
                    "price": p.get("current_price", 0),
                    "change_24h": p.get("price_change_percentage_24h", 0),
                    "market_cap": p.get("market_cap", 0),
                }
                for p in prices
            ]

            await websocket.send_json({"type": "price_update", "data": price_data})
            await asyncio.sleep(30)  # Update every 30 seconds

    except WebSocketDisconnect:
        connected_clients.remove(websocket)
    except Exception:
        if websocket in connected_clients:
            connected_clients.remove(websocket)
```

### 3. Create price cache service

Create `backend/services/price_cache.py`:
```python
"""In-memory price cache to reduce API calls."""
import time
from config import settings


class PriceCache:
    """Cache prices to avoid hitting CoinGecko rate limits."""

    def __init__(self, ttl: int = None):
        self.cache: dict = {}
        self.ttl = ttl or settings.PRICE_UPDATE_INTERVAL

    def get(self, key: str) -> dict | None:
        """Get cached price if not expired."""
        if key in self.cache:
            entry = self.cache[key]
            if time.time() - entry["timestamp"] < self.ttl:
                return entry["data"]
            del self.cache[key]
        return None

    def set(self, key: str, data: dict) -> None:
        """Cache price data with timestamp."""
        self.cache[key] = {"data": data, "timestamp": time.time()}

    def clear(self) -> None:
        """Clear all cached data."""
        self.cache.clear()

    @property
    def size(self) -> int:
        """Number of cached entries."""
        return len(self.cache)


price_cache = PriceCache()
```

### 4. Update main.py to include WebSocket

Add to `backend/main.py`:
```python
from backend.api.websocket import router as ws_router

app.include_router(ws_router)
```

### 5. Add WebSocket client to frontend

Add this to the end of `frontend/static/js/app.js`:
```javascript
// WebSocket for real-time updates
function connectWebSocket() {
    const protocol = window.location.protocol === 'https:' ? 'wss:' : 'ws:';
    const ws = new WebSocket(`${protocol}//${window.location.host}/ws/prices`);

    ws.onmessage = (event) => {
        const msg = JSON.parse(event.data);
        if (msg.type === 'price_update') {
            updateTickerFromWS(msg.data);
        }
    };

    ws.onclose = () => {
        // Reconnect after 5 seconds
        setTimeout(connectWebSocket, 5000);
    };

    ws.onerror = () => ws.close();
}

function updateTickerFromWS(prices) {
    const ticker = document.getElementById('ticker');
    ticker.innerHTML = prices.map(coin => `
        <div class="ticker-item">
            <div class="ticker-symbol">${coin.symbol.toUpperCase()}</div>
            <div class="ticker-price">$${coin.price.toLocaleString()}</div>
            <div class="ticker-change ${coin.change_24h >= 0 ? 'positive' : 'negative'}">
                ${coin.change_24h >= 0 ? '+' : ''}${coin.change_24h.toFixed(2)}%
            </div>
        </div>
    `).join('');
}

// Start WebSocket connection
connectWebSocket();
```

### 6. Commit

```bash
git add .
git commit -m "feat(realtime): add WebSocket live price streaming

- WebSocket endpoint at /ws/prices
- Price cache service to reduce API calls
- Frontend auto-reconnect on disconnect
- Real-time ticker updates via WebSocket
- 30-second price refresh interval"

git push -u origin feat/websocket-realtime
```

- [ ] WebSocket branch pushed

---

## Phase 2: Merge Strategies (20 min)

### The Three Options

| Strategy | History | Best For |
|----------|---------|----------|
| **Merge commit** | Shows branch + merge point | Major features, releases |
| **Squash merge** | 1 clean commit on main | Most PRs, daily features |
| **Rebase merge** | Linear, no merge commit | Hotfixes, small changes |

### 7. Strategy 1 — Merge Commit (for WebSocket feature)

This is a major feature, so we use a merge commit to preserve the branch history:

```bash
gh pr create --title "feat: add WebSocket real-time prices" \
  --body "Major feature: real-time price streaming via WebSocket with auto-reconnect"

# Use merge commit (not squash) — shows branch history
gh pr merge --merge
```

```bash
git checkout main && git pull
git log --oneline --graph -5
```

- [ ] Merge commit visible with branch lines
- [ ] Individual commits preserved

> **Learner Tip:** Merge commits show WHERE a branch merged into main. Useful for major features where you want to see the full development history.

### 8. Strategy 2 — Squash Merge (for a quick fix)

```bash
git checkout -b fix/cache-ttl
```

Create a fix with multiple small commits:
```bash
echo "# Cache TTL increased to 60s" >> backend/services/price_cache.py
git add . && git commit -m "fix(cache): increase TTL to 60s"
echo "# Added cache stats logging" >> backend/services/price_cache.py
git add . && git commit -m "chore(cache): add stats logging"
```

```bash
git push -u origin fix/cache-ttl
gh pr create --title "fix: improve cache TTL and logging" --body "Increases cache TTL and adds logging"

# Squash — 2 commits become 1 clean commit
gh pr merge --squash --delete-branch
```

```bash
git checkout main && git pull
git log --oneline -3
```

- [ ] 2 commits squashed into 1 on main
- [ ] Clean, single commit message

> **Learner Tip:** Squash merge is the default for most PRs. Keeps main history clean — one commit per PR.

### 9. Strategy 3 — Rebase Merge (for a hotfix)

```bash
git checkout -b hotfix/health-endpoint
echo "# Health check improved" >> backend/main.py
git add . && git commit -m "fix(health): add uptime to health endpoint"
git push -u origin hotfix/health-endpoint
gh pr create --title "fix: improve health endpoint" --body "Adds uptime info"

# Rebase — linear history, no merge commit
gh pr merge --rebase --delete-branch
```

```bash
git checkout main && git pull
git log --oneline --graph -5
```

- [ ] Linear history, no merge bubbles
- [ ] Commit placed directly on main

> **Learner Tip:** Rebase merge is for small, single-commit changes. The commit goes directly on main's timeline without any merge artifacts.

### 10. Compare all three

```bash
git log --oneline --graph -15
```

You should see:
- Merge commit with branch lines (WebSocket)
- Single squashed commit (cache fix)
- Linear commit (health endpoint)

#### Quick Decision Guide

```
Feature branch    → gh pr merge --merge    (preserve history)
Daily PR          → gh pr merge --squash   (clean main)
Hotfix            → gh pr merge --rebase   (linear)
Dependabot PR     → gh pr merge --squash   (one commit)
```

#### SWOT — Merge Strategies

| | |
|---|---|
| **Strengths** | Three tools for three scenarios. Squash for clean daily work, merge for big features, rebase for hotfixes. Full control over history shape. |
| **Weaknesses** | Team must agree on when to use each. Inconsistent strategy leads to messy history. |
| **Opportunities** | Set default merge strategy in GitHub settings (Settings → General → Pull Requests). |
| **Threats** | Using merge commits for everything creates noisy history. Using squash for everything loses branch context on big features. |

---

## Phase 3: Release v0.3.0 (5 min)

### 11. Tag and release

Update `CHANGELOG.md` with v0.3.0 section, then:

```bash
git checkout -b docs/v030-changelog
# Update CHANGELOG.md with v0.3.0 entries
git add CHANGELOG.md
git commit -m "docs(changelog): add v0.3.0 entries"
git push -u origin docs/v030-changelog
gh pr create --title "docs: update changelog for v0.3.0" --body "Release notes for WebSocket feature"
gh pr merge --squash --delete-branch
git checkout main && git pull

git tag -a v0.3.0 -m "v0.3.0 - Real-time WebSocket prices"
git push origin v0.3.0

gh release create v0.3.0 \
  --title "v0.3.0 — Real-time Prices" \
  --notes "## What's New
- WebSocket real-time price streaming
- Price cache service (reduces API calls)
- Auto-reconnect on connection drop
- Improved health endpoint

## Merge Strategies Used
- Merge commit: WebSocket feature (preserved branch history)
- Squash merge: Cache fix (clean single commit)
- Rebase merge: Health endpoint fix (linear history)"
```

- [ ] v0.3.0 released

---

## Self-Check

- [ ] WebSocket endpoint working
- [ ] Price cache service implemented
- [ ] Used merge commit for major feature
- [ ] Used squash merge for daily fix
- [ ] Used rebase merge for hotfix
- [ ] Can see visual difference in `git log --graph`
- [ ] v0.3.0 released

---

## Command Explanation Reference

| Command | What It Does | When to Use |
|---------|-------------|-------------|
| `gh pr merge --merge` | Merge commit (preserves branch) | Major features |
| `gh pr merge --squash` | Squash into one commit | Daily PRs, fixes |
| `gh pr merge --rebase` | Linear history, no merge commit | Hotfixes |
| `git log --oneline --graph` | Visual branch/merge history | Comparing merge strategies |

---

## ASCII Workflow — Day 11 Visual Summary

```
                        DAY 11 OVERVIEW
  ┌──────────────────────────────────────────────────────────┐
  │                                                          │
  │  WEBSOCKET ARCHITECTURE                                  │
  │  ══════════════════════                                  │
  │                                                          │
  │  ┌────────┐  HTTP    ┌─────────┐  API   ┌──────────┐   │
  │  │ Browser│─────────▶│ FastAPI │──────▶│CoinGecko │   │
  │  │        │          │         │       └──────────┘   │
  │  │        │◀─── WS ──│  /ws/   │                       │
  │  │        │  prices  │ prices  │──▶┌───────────┐      │
  │  └────────┘          └─────────┘   │PriceCache │      │
  │                                    └───────────┘      │
  │  Auto-reconnect on disconnect                          │
  │  30-second refresh interval                            │
  │                                                          │
  ├──────────────────────────────────────────────────────────┤
  │                                                          │
  │  THREE MERGE STRATEGIES                                  │
  │  ══════════════════════                                  │
  │                                                          │
  │  1. MERGE COMMIT (--merge)                               │
  │     ○──○──○──○              (feature branch)             │
  │    /          \                                          │
  │   ○────────────●──○         (main)                       │
  │   Shows branch history. Use for MAJOR features.          │
  │                                                          │
  │  2. SQUASH MERGE (--squash)                              │
  │     ○──○──○  (3 commits)  →  ●  (1 commit on main)     │
  │   Clean. Use for DAILY PRs.                              │
  │                                                          │
  │  3. REBASE MERGE (--rebase)                              │
  │     ○──○──●──○              (linear, no merge commit)   │
  │   Clean + preserves commits. Use for HOTFIXES.           │
  │                                                          │
  └──────────────────────────────────────────────────────────┘
```

**Next → [Day 12: Bisect + Worktree](day-12-bisect-and-worktree.md)**
