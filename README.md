# StockIQ — AVL-Powered Stock Intelligence Platform

**Group C4 · DSA Course Project (BIT 4105)**

A multi-tenant financial analytics platform where every persistent data structure — user records, stock time-series, audit logs, price alerts, and cross-tenant permissions — is backed exclusively by a hand-written, self-balancing **AVL Tree**. No arrays-as-database, no localStorage, no external DB: just AVL trees, from insert to range query to delete.

Built as a single dependency-free HTML/CSS/JS file — open it in a browser and it runs.

---

## ✨ Highlights

- **AVL Tree implementation from scratch** — all 4 rotation cases (LL, RR, LR, RL), height-balanced on every insert/delete, with a live rotation counter for empirical verification.
- **O(log n) guarantees at scale** — insert, search, and delete are O(log n); range queries are O(log n + k). Verified with a built-in **1,000,000+ record load test**.
- **Multi-tenant architecture** — two demo tenants (`AlphaFund`, `BetaCapital`), each with isolated users, stock trees, alerts, and audit logs, plus a cross-tenant permission system.
- **Real security primitives** — SHA-256 password hashing via `SubtleCrypto`, UUID-4 session tokens, rate-limited login attempts, role-gated pages (Viewer / Analyst / Admin).
- **Immutable audit log** — an append-only AVL tree; calling `delete()` on it throws `ImmutableLogError` by design.
- **Interactive AVL Inspector** — insert/search/delete keys manually and watch the tree rebalance, or run canned demos for each rotation case with a step-by-step operation trace.
- **Dashboard analytics** — price trend, top-queried tickers, and role distribution, rendered as hand-built inline SVG charts driven directly by the live AVL-backed data (no charting library).

---

## 🖥️ Tech Stack

- Vanilla JavaScript (ES6 classes, no framework, no build step)
- Vanilla CSS (custom properties / design tokens, no framework)
- Web Crypto API (`crypto.subtle`, `crypto.randomUUID`)
- Single `index.html` file — zero dependencies, zero build tooling

---

## 🚀 Running It

No install, no server required for local testing:

```bash
git clone https://github.com/<your-username>/stockiq.git
cd stockiq
open index.html   # or just double-click it
```

For a proper local dev loop (recommended, since some browsers restrict `crypto.subtle` on `file://`):

```bash
npx serve .
# or
python3 -m http.server 8000
```

Then visit `http://localhost:8000`.

---

## 🔑 Demo Accounts

Password for all demo accounts: `pass123`

| Username | Tenant       | Role    |
|----------|--------------|---------|
| alice    | AlphaFund    | Admin   |
| bob      | AlphaFund    | Analyst |
| carol    | AlphaFund    | Viewer  |
| dave     | BetaCapital  | Admin   |
| eve      | BetaCapital  | Analyst |

Click any demo card on the login screen to sign in instantly.

---

## 🧠 Core Data Structure

Every service in the app (`AS`, `SS`, `Audit`, `AL`, `PS`, `TS`) is backed by the same generic `AVL` class:

```js
class AVL {
  insert(k, v)       // O(log n)
  get(k)             // O(log n)
  delete(k)          // O(log n)
  range(lo, hi)      // O(log n + k)
  inorder() / values()
  height() / viz()   // introspection for the Inspector page
}
```

Stock data, for example, is stored as a tree-of-trees: `AVL<tenantId::ticker, AVL<date, StockRecord>>` — so a range query on a date window is a single balanced-tree traversal, not a linear scan.

---

## 📂 App Structure

- **Dashboard** — live AVL node counts, audit entries, tree height, plus price-trend / top-ticker / role-distribution charts
- **Stock Query** — range query UI over per-ticker AVL trees, with query timing shown live
- **Price Alerts** *(Analyst+)* — threshold-keyed AVL tree, checked against query results
- **Top Performers** *(Analyst+)* — most-queried tickers via AVL in-order traversal
- **Users** *(Admin)* — tenant user management, role changes, deactivation
- **Audit Log** *(Admin)* — immutable, append-only AVL tree
- **Permissions** *(Admin)* — cross-tenant data-sharing grants
- **AVL Inspector** — manual insert/search/delete against a scratch tree, with rotation-case demos
- **Load Test** — chunked async insert of up to 1,000,000 records via `requestAnimationFrame`, benchmarking height, rotation count, and range-query latency at scale

---

## 📊 Complexity Summary

| Operation          | Complexity     |
|---------------------|----------------|
| Insert              | O(log n)       |
| Search              | O(log n)       |
| Delete              | O(log n)       |
| Range query         | O(log n + k)   |
| In-order traversal  | O(n)           |

---

## ⚠️ Notes on Scope

This is an academic DSA demonstration, not a production system:
- All data lives in memory and resets on page reload (no backend/persistence layer).
- Auth is client-side for demo purposes — a real deployment would need a server-side trust boundary.
- Stock data is synthetically seeded on boot, not live market data.

---

## 📄 License

Add your preferred license here (e.g. MIT) — none specified yet.
