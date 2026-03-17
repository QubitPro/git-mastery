# Day 10 — Frontend Dashboard + Changelog + Dependabot

**Time:** 45 min | **Repo:** `mn-crypto-tracker`

---

## Today's Goal

Build the interactive frontend dashboard with live charts, create the CHANGELOG, and configure Dependabot. Release v0.2.0.

---

## Phase 1: Frontend Dashboard (25 min)

### 1. Create frontend branch

```bash
git checkout main && git pull
git checkout -b feat/frontend-dashboard
```

### 2. Create the HTML dashboard

Create `frontend/templates/index.html`:
```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>MN Crypto Tracker</title>
    <link rel="stylesheet" href="/static/css/style.css">
    <script src="https://cdn.jsdelivr.net/npm/chart.js"></script>
</head>
<body>
    <div class="container">
        <header>
            <h1>MN Crypto Portfolio Tracker</h1>
            <span class="version" id="version">v0.2.0</span>
        </header>

        <!-- Price Ticker -->
        <section class="ticker" id="ticker">
            <div class="ticker-loading">Loading prices...</div>
        </section>

        <!-- Portfolio Summary -->
        <section class="card">
            <h2>Portfolio Summary</h2>
            <div class="portfolio-stats" id="portfolio-stats">
                <div class="stat">
                    <span class="stat-label">Total Invested</span>
                    <span class="stat-value" id="total-invested">$0.00</span>
                </div>
                <div class="stat">
                    <span class="stat-label">Current Value</span>
                    <span class="stat-value" id="current-value">$0.00</span>
                </div>
                <div class="stat">
                    <span class="stat-label">Total PnL</span>
                    <span class="stat-value" id="total-pnl">$0.00</span>
                </div>
            </div>
        </section>

        <!-- Price Chart -->
        <section class="card">
            <h2>Price Chart</h2>
            <canvas id="priceChart" height="300"></canvas>
        </section>

        <!-- Add Transaction -->
        <section class="card">
            <h2>Add Transaction</h2>
            <form id="tx-form">
                <div class="form-row">
                    <input type="text" id="coin-id" placeholder="Coin ID (e.g., bitcoin)" required>
                    <input type="text" id="symbol" placeholder="Symbol (e.g., BTC)" required>
                </div>
                <div class="form-row">
                    <input type="number" id="amount" placeholder="Amount" step="0.0001" required>
                    <input type="number" id="price" placeholder="Buy Price (USD)" step="0.01" required>
                </div>
                <div class="form-row">
                    <select id="tx-type">
                        <option value="buy">Buy</option>
                        <option value="sell">Sell</option>
                    </select>
                    <button type="submit">Add Transaction</button>
                </div>
            </form>
        </section>

        <!-- Transaction History -->
        <section class="card">
            <h2>Recent Transactions</h2>
            <table id="tx-table">
                <thead>
                    <tr>
                        <th>Date</th>
                        <th>Coin</th>
                        <th>Type</th>
                        <th>Amount</th>
                        <th>Price</th>
                        <th>Total</th>
                    </tr>
                </thead>
                <tbody id="tx-body"></tbody>
            </table>
        </section>

        <footer>
            <p>MN Crypto Tracker &copy; 2026 | Data from CoinGecko</p>
        </footer>
    </div>

    <script src="/static/js/app.js"></script>
</body>
</html>
```

### 3. Create the CSS

Create `frontend/static/css/style.css`:
```css
:root {
    --bg-primary: #0d1117;
    --bg-card: #161b22;
    --text-primary: #e6edf3;
    --text-secondary: #8b949e;
    --accent: #58a6ff;
    --green: #3fb950;
    --red: #f85149;
    --border: #30363d;
}

* { margin: 0; padding: 0; box-sizing: border-box; }

body {
    font-family: -apple-system, BlinkMacSystemFont, 'Segoe UI', sans-serif;
    background: var(--bg-primary);
    color: var(--text-primary);
    line-height: 1.6;
}

.container { max-width: 1200px; margin: 0 auto; padding: 20px; }

header {
    display: flex; justify-content: space-between; align-items: center;
    padding: 20px 0; border-bottom: 1px solid var(--border); margin-bottom: 20px;
}
header h1 { font-size: 1.5rem; }
.version { color: var(--text-secondary); font-size: 0.85rem; }

.card {
    background: var(--bg-card); border: 1px solid var(--border);
    border-radius: 8px; padding: 20px; margin-bottom: 20px;
}
.card h2 { font-size: 1.1rem; margin-bottom: 15px; color: var(--accent); }

.ticker {
    display: flex; gap: 15px; overflow-x: auto; padding: 10px 0; margin-bottom: 20px;
}
.ticker-item {
    background: var(--bg-card); border: 1px solid var(--border);
    border-radius: 8px; padding: 12px 20px; min-width: 180px; text-align: center;
}
.ticker-symbol { font-weight: bold; font-size: 1.1rem; }
.ticker-price { font-size: 1.2rem; margin: 5px 0; }
.ticker-change { font-size: 0.85rem; }
.positive { color: var(--green); }
.negative { color: var(--red); }

.portfolio-stats { display: grid; grid-template-columns: repeat(3, 1fr); gap: 15px; }
.stat { text-align: center; }
.stat-label { display: block; color: var(--text-secondary); font-size: 0.85rem; }
.stat-value { display: block; font-size: 1.5rem; font-weight: bold; margin-top: 5px; }

form { display: flex; flex-direction: column; gap: 10px; }
.form-row { display: flex; gap: 10px; }
.form-row > * { flex: 1; }
input, select {
    background: var(--bg-primary); border: 1px solid var(--border);
    color: var(--text-primary); padding: 10px; border-radius: 6px; font-size: 0.9rem;
}
button {
    background: var(--accent); color: #fff; border: none;
    padding: 10px 20px; border-radius: 6px; cursor: pointer; font-size: 0.9rem;
}
button:hover { opacity: 0.9; }

table { width: 100%; border-collapse: collapse; }
th, td { padding: 10px; text-align: left; border-bottom: 1px solid var(--border); }
th { color: var(--text-secondary); font-size: 0.85rem; text-transform: uppercase; }

footer { text-align: center; padding: 20px; color: var(--text-secondary); font-size: 0.85rem; }

canvas { max-height: 300px; }
```

### 4. Create the JavaScript

Create `frontend/static/js/app.js`:
```javascript
/**
 * MN Crypto Tracker — Frontend Logic
 */

const API_BASE = '';

// Fetch and display prices
async function loadPrices() {
    try {
        const res = await fetch(`${API_BASE}/api/prices/?coins=bitcoin,ethereum,solana,cardano`);
        const data = await res.json();
        renderTicker(data.prices);
        renderChart(data.prices);
    } catch (err) {
        document.getElementById('ticker').innerHTML = '<div class="ticker-loading">Failed to load prices</div>';
    }
}

function renderTicker(prices) {
    const ticker = document.getElementById('ticker');
    ticker.innerHTML = prices.map(coin => `
        <div class="ticker-item">
            <div class="ticker-symbol">${coin.symbol.toUpperCase()}</div>
            <div class="ticker-price">$${coin.current_price.toLocaleString()}</div>
            <div class="ticker-change ${coin.price_change_percentage_24h >= 0 ? 'positive' : 'negative'}">
                ${coin.price_change_percentage_24h >= 0 ? '+' : ''}${coin.price_change_percentage_24h.toFixed(2)}%
            </div>
        </div>
    `).join('');
}

function renderChart(prices) {
    const ctx = document.getElementById('priceChart').getContext('2d');
    new Chart(ctx, {
        type: 'bar',
        data: {
            labels: prices.map(p => p.symbol.toUpperCase()),
            datasets: [{
                label: 'Price (USD)',
                data: prices.map(p => p.current_price),
                backgroundColor: ['#f7931a', '#627eea', '#00ffa3', '#0033ad'],
                borderRadius: 6,
            }]
        },
        options: {
            responsive: true,
            plugins: { legend: { display: false } },
            scales: {
                y: { ticks: { color: '#8b949e' }, grid: { color: '#30363d' } },
                x: { ticks: { color: '#8b949e' }, grid: { display: false } }
            }
        }
    });
}

// Portfolio
async function loadPortfolio() {
    try {
        const res = await fetch(`${API_BASE}/api/portfolio/`);
        const data = await res.json();
        const portfolio = data.portfolio;

        let totalInvested = 0;
        portfolio.forEach(h => { totalInvested += h.total_invested; });

        document.getElementById('total-invested').textContent = `$${totalInvested.toLocaleString()}`;
    } catch (err) {
        // Portfolio not loaded
    }
}

// Transactions
async function loadTransactions() {
    try {
        const res = await fetch(`${API_BASE}/api/portfolio/transactions`);
        const data = await res.json();
        const tbody = document.getElementById('tx-body');
        tbody.innerHTML = data.transactions.map(tx => `
            <tr>
                <td>${new Date(tx.date).toLocaleDateString()}</td>
                <td>${tx.symbol}</td>
                <td class="${tx.type === 'buy' ? 'positive' : 'negative'}">${tx.type.toUpperCase()}</td>
                <td>${tx.amount}</td>
                <td>$${tx.buy_price.toLocaleString()}</td>
                <td>$${(tx.amount * tx.buy_price).toLocaleString()}</td>
            </tr>
        `).join('');
    } catch (err) {
        // Transactions not loaded
    }
}

// Form submit
document.getElementById('tx-form').addEventListener('submit', async (e) => {
    e.preventDefault();
    const body = {
        coin_id: document.getElementById('coin-id').value,
        symbol: document.getElementById('symbol').value,
        amount: parseFloat(document.getElementById('amount').value),
        buy_price: parseFloat(document.getElementById('price').value),
        transaction_type: document.getElementById('tx-type').value,
    };

    await fetch(`${API_BASE}/api/portfolio/transactions`, {
        method: 'POST',
        headers: { 'Content-Type': 'application/json' },
        body: JSON.stringify(body),
    });

    e.target.reset();
    loadTransactions();
    loadPortfolio();
});

// Initialize
loadPrices();
loadPortfolio();
loadTransactions();

// Auto-refresh prices every 30 seconds
setInterval(loadPrices, 30000);
```

### 5. Update main.py to serve frontend

Add to `backend/main.py` (add these imports and mount):
```python
from fastapi.staticfiles import StaticFiles
from fastapi.templating import Jinja2Templates
from fastapi import Request

# After app creation, add:
app.mount("/static", StaticFiles(directory="frontend/static"), name="static")
templates = Jinja2Templates(directory="frontend/templates")

@app.get("/")
async def dashboard(request: Request):
    """Serve the main dashboard."""
    return templates.TemplateResponse("index.html", {"request": request})
```

### 6. Commit and push

```bash
git add frontend/ backend/main.py
git commit -m "feat(frontend): add interactive dashboard with charts and portfolio UI

- Dark-themed responsive dashboard
- Live price ticker with 30s auto-refresh
- Portfolio summary with total invested
- Chart.js price comparison chart
- Transaction form (buy/sell)
- Transaction history table
- Jinja2 template rendering via FastAPI"

git push -u origin feat/frontend-dashboard
gh pr create --title "feat: add frontend dashboard with charts" --body "Interactive dashboard with live prices, portfolio tracking, and Chart.js visualizations"
gh pr merge --squash --delete-branch
git checkout main && git pull
```

- [ ] Frontend dashboard merged

---

## Phase 2: CHANGELOG (10 min)

### 7. Create CHANGELOG

```bash
git checkout -b docs/changelog
```

Create `CHANGELOG.md`:
```markdown
# Changelog

All notable changes documented here.
Format: [Keep a Changelog](https://keepachangelog.com/) + [Semantic Versioning](https://semver.org/).

## [Unreleased]

## [0.2.0] - 2026-03-19
### Added
- Interactive dark-themed dashboard
- Live price ticker with 30s auto-refresh
- Portfolio summary (total invested, current value, PnL)
- Chart.js price comparison charts
- Transaction form (buy/sell)
- Transaction history table

## [0.1.0] - 2026-03-18
### Added
- FastAPI backend with auto-generated Swagger docs
- Database models (Cryptocurrency, Portfolio, PriceHistory)
- CoinGecko price API integration
- Portfolio CRUD endpoints (add/view transactions)
- CI pipeline with Python tests
- Project scaffolding with config management

[Unreleased]: https://github.com/QubitPro/mn-crypto-tracker/compare/v0.2.0...HEAD
[0.2.0]: https://github.com/QubitPro/mn-crypto-tracker/compare/v0.1.0...v0.2.0
[0.1.0]: https://github.com/QubitPro/mn-crypto-tracker/releases/tag/v0.1.0
```

> **Learner Tip:** The changelog workflow is: develop → add entries under `[Unreleased]` → when releasing, move entries to a new version section → tag → release.

| Section | Use For |
|---------|---------|
| Added | New features |
| Changed | Modified features |
| Fixed | Bug fixes |
| Removed | Deleted features |
| Security | Security fixes |

```bash
git add CHANGELOG.md
git commit -m "docs(changelog): add CHANGELOG.md with release history"
git push -u origin docs/changelog
gh pr create --title "docs: add CHANGELOG.md" --body "Changelog following Keep a Changelog format"
gh pr merge --squash --delete-branch
git checkout main && git pull
```

- [ ] CHANGELOG in repo

---

## Phase 3: Release v0.2.0 (5 min)

### 8. Tag and release

```bash
git tag -a v0.2.0 -m "v0.2.0 - Frontend Dashboard"
git push origin v0.2.0

gh release create v0.2.0 \
  --title "v0.2.0 — Frontend Dashboard" \
  --notes "## What's New
- Interactive dark-themed dashboard
- Live price ticker (BTC, ETH, SOL, ADA)
- Chart.js price comparison charts
- Portfolio tracking with transaction form
- Auto-refresh every 30 seconds
- CHANGELOG.md added

## Screenshot
Dashboard with live crypto prices, portfolio summary, and transaction history.

## Full Changelog
https://github.com/QubitPro/mn-crypto-tracker/blob/main/CHANGELOG.md"
```

- [ ] v0.2.0 released

---

## Self-Check

- [ ] Frontend dashboard with dark theme
- [ ] Live price ticker and charts
- [ ] Transaction form working
- [ ] CHANGELOG.md with proper format
- [ ] Dependabot configured (from Day 08)
- [ ] v0.2.0 tagged and released

---

## Command Explanation Reference

| Command | What It Does | When to Use |
|---------|-------------|-------------|
| `gh release create v0.2.0` | Creates release from tag | Publishing a new version |
| `gh pr merge --squash` | Squash merge a PR | Standard feature merge |
| `git tag -a v0.2.0` | Create annotated tag | Marking a release |

---

## ASCII Workflow — Day 10 Visual Summary

```
                        DAY 10 OVERVIEW
  ┌──────────────────────────────────────────────────────────┐
  │                                                          │
  │  FRONTEND DASHBOARD                                      │
  │  ══════════════════                                      │
  │                                                          │
  │  ┌────────────────────────────────────────┐              │
  │  │  MN Crypto Portfolio Tracker    v0.2.0 │              │
  │  ├────────────────────────────────────────┤              │
  │  │  BTC          ETH          SOL         │              │
  │  │  $67,234      $3,456       $178        │              │
  │  │  +2.3%        -1.2%        +5.1%       │              │
  │  ├────────────────────────────────────────┤              │
  │  │  Portfolio Summary                     │              │
  │  │  Invested: $10,000  Value: $12,340     │              │
  │  │  PnL: +$2,340 (+23.4%)                │              │
  │  ├────────────────────────────────────────┤              │
  │  │  ┌──┐                                  │              │
  │  │  │  │ ┌──┐                             │              │
  │  │  │  │ │  │ ┌──┐ ┌──┐                  │              │
  │  │  │BTC│ │ETH│ │SOL│ │ADA│  ◀── Chart.js│              │
  │  │  └──┘ └──┘ └──┘ └──┘                  │              │
  │  ├────────────────────────────────────────┤              │
  │  │  [Add Transaction]  [History Table]    │              │
  │  └────────────────────────────────────────┘              │
  │                                                          │
  ├──────────────────────────────────────────────────────────┤
  │                                                          │
  │  CHANGELOG WORKFLOW                                      │
  │  ══════════════════                                      │
  │                                                          │
  │  Developing:    Add under [Unreleased]                   │
  │  Releasing:     Move to [0.2.0] - date                   │
  │  Tag:           git tag -a v0.2.0                        │
  │  Release:       gh release create v0.2.0                 │
  │                                                          │
  │  v0.1.0 ──▶ v0.2.0 ──▶ v0.3.0 ──▶ v1.0.0              │
  │  API        Frontend   WebSocket  Production             │
  │                                                          │
  └──────────────────────────────────────────────────────────┘
```

**Next → [Day 11: WebSocket + Merge Strategies](day-11-merge-strategies.md)**
