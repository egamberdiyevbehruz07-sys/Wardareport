# Stock Book — Main Management System v1.8.1

Release: `2026-11-20-smart-retail-intelligence-r4`

This is the **main Netlify website package**. The Telegram Mini App remains a separate Render deployment and is not bundled as a Render service here. The existing `/tg/` entry and Telegram-related Netlify functions are retained as in the base project.

## What's included

- Existing Stock Book main shell and authenticated operational application.
- Existing Firebase/Firestore login, catalog, sales, imports, transfers, write-offs and movement history.
- Existing BILLZ synchronization, product and store data, Management Analytics and reports.
- Product details and product-level import/sale/return/stock reporting.
- Catalog stock recalculation after successful saved movements; sold-out items remain available under Sold out and All products.
- Slow-moving catalog view with 30/60/90/180-day filters and last sale dates.
- **Smart Retail Intelligence** section:
  - Daily Action Center with review priorities.
  - Product Health score (transparent rule-based 0–100 indicator).
  - Slow-moving / no-recent-sale products.
  - Restock watch based on recent net sales and estimated days of cover.
  - Suggested inter-branch transfers based on destination demand and estimated source surplus.
  - Branch performance, color demand, size demand and best-performing products.
  - Estimated stock cost using available BILLZ supply prices, with price-coverage percentage.
  - Excel export for summaries, branches, slow stock, transfer suggestions, restock watch and product health.
  - Physical stock count workbook: download a count sheet, upload the completed workbook, and compare counted quantities with Stock Book. This is a preview/audit only; it does not adjust stock automatically.
- Existing Telegram Mini App static page and product-image preview are retained. Deploy the Mini App backend separately to Render using its own Render package/repository.

## Important calculation notes

- Smart Retail Intelligence is **rule-based**, not an AI forecast. It uses the Stock Book movement ledger and the selected 30/60/90-day analysis window.
- Product last-sale date is taken from all available positive sale movements; period totals use only the selected analysis period.
- Returns are negative `out` movements and reduce net unit sales. Free/deleted/unmatched records are excluded from these sales insights.
- Stock and branch quantities are derived from current Stock Book movements plus the existing BILLZ catalog merge. Missing or unsynchronized BILLZ sales cannot update a variant until they are synced and matched.
- Stock valuation is an estimate using the latest available BILLZ supply price for each store/variant. It is not historical landed cost; unpriced units are excluded and coverage is displayed.
- Transfer recommendations are advisory. The user must review and save a normal transfer. This page does not change quantities, create transfers, reorder goods or change selling prices automatically.
- The physical count upload only compares a spreadsheet with current Stock Book quantities. It never writes counts into Firebase or BILLZ. Review unmatched rows and differences before creating approved adjustments through the existing workflow.
- Management analytics and cash/card totals still depend on BILLZ endpoints and transaction data available to the current integration. This package does not claim to verify live account totals.

## Deploy safely to Netlify

1. Back up the currently deployed project and keep the current production deploy available.
2. Extract this ZIP. The extracted root must contain `index.html`, `legacy-app.html`, `package.json`, `netlify.toml`, `server.mjs`, `netlify/` and `tg/`.
3. Run `npm run preflight:integrated` from the extracted root (Node 20+).
4. Deploy to a **separate Netlify staging site first**. Do not overwrite production for the first test.
5. Copy the existing environment variables into the staging site using Netlify's environment settings. Keep the existing Firebase, BILLZ, Telegram and Supabase values. Never add secrets to source files or GitHub.
6. Test login, catalog quantities, product detail reports, sales/returns, imports, transfers, write-offs, BILLZ full sync, BILLZ comparison, Management Analytics, Smart Retail Intelligence, Excel exports, physical count comparison and `/tg/`.
7. Only promote to production after data has been compared with BILLZ and the core workflows pass.

## Render Mini App separation

The Render Mini App is a separate service/repository. Do not replace the existing Render Warda service with this Netlify package. Do not run Telegram webhook setup commands as part of deploying this main ZIP; the bot has one active webhook and must be switched only after the separate Render Mini App is tested and ready.


## Quick GitHub → Netlify setup (v1.8.1)

1. Create a GitHub repository and upload **everything inside** the extracted folder (not the wrapper folder). `package.json`, `index.html`, `netlify.toml` must sit at the repo root.
2. In Netlify: **Add new site → Import from Git**, pick the repo. Build command: *(empty)*. Publish directory: `.` (already set in `netlify.toml`).
3. Add the environment variables listed in `.env.example` (Site settings → Environment variables). Never commit real values.
4. Every push to the connected branch redeploys automatically. HTML is served `no-cache`, so users get the new version on refresh.
5. Notes and older READMEs live in `docs/`. `archive/` holds stale root copies of functions that were never used by Netlify.
