# Claude Code Session Prompt — New Client Dashboard Build

> Copy everything below the line into a new Claude Code session to begin.

---

## Project: Client Business Dashboard

Build a comprehensive, self-contained HTML business dashboard that pulls data from multiple APIs (ad platforms, CRMs, analytics, POS systems), aggregates it into a unified report, and publishes as a single `index.html` file to GitHub (auto-deployed via Netlify).

### Reference Architecture

This dashboard is based on a proven architecture originally built for a multi-entity business portfolio. The reference implementation lives at:

**Reference repo:** https://github.com/rspuller/506-ramseur-media-report

Use the reference codebase as your architectural guide. The file structure, data flow, template design, and deployment pipeline should mirror that project. However, all business-specific configuration (entities, API credentials, account IDs, routing logic) will be unique to this new client.

---

### Architecture Overview

The dashboard is a **Python build pipeline** that:
1. Pulls data from multiple APIs in parallel
2. Aggregates and routes data to business entities
3. Computes funnel metrics, ad performance, SEO stats, and revenue
4. Renders a self-contained HTML file with embedded CSS/JS
5. Pushes the HTML to GitHub via the Contents API (Netlify auto-deploys)

**Key design principles:**
- **stdlib-only Python** — no pip packages except `PyJWT` + `cryptography` (for Google service account JWT signing)
- **Single HTML output** — all CSS and JS inline, no external dependencies except Google Fonts CDN
- **Dark/light mode** — CSS variable-based theming with localStorage persistence
- **Date presets** — Today, Yesterday, This Week, Last Week, Last 7 Days, This Month, Last Month
- **Custom date range** — calendar picker for arbitrary date ranges with client-side recomputation
- **Collapsible accordions** — each section and entity is collapsible
- **Entity filter dropdown** — filter the entire dashboard to a single business entity
- **Executive Summary** — portfolio-level KPIs + entity scorecard with period-over-period comparison
- **PDF export** — via `window.print()` with `@media print` CSS
- **Lead modals** — clickable lead counts open detailed lead info popups

### File Structure to Create

```
project-root/
├── .env                        # API keys (WINDSOR_API_KEY, etc.)
├── service_account.json        # Google service account key (GA4, GSC, Calendar)
├── build.py                    # Main orchestrator — CLI entry point
├── windsor.py                  # Windsor.ai connector (Google Ads, Meta, FB Leads)
├── aggregator.py               # Campaign routing + entity aggregation
├── ga4_direct.py               # Google Analytics 4 Data API (direct, JWT auth)
├── gsc_direct.py               # Google Search Console API (direct, JWT auth)
├── template.html               # Self-contained HTML/CSS/JS report template
├── github_push.py              # Pushes index.html to GitHub via REST API
├── [crm_connector].py          # One file per CRM (see CRM section below)
└── docs/
    └── SETUP.md                # Client-specific setup notes
```

### Dashboard Sections

1. **Executive Summary** (collapsible, starts closed)
   - KPI cards: New Revenue, MRR, Total Revenue, Total New Leads, Total Ad Spend
   - Entity scorecard table: Entity | New Leads | Leads vs Prev | Ad Spend | Revenue
   - KPI cards update dynamically when entity filter is applied

2. **Sales & Marketing Funnels** (collapsible, per entity)
   - Marketing Conversion Funnel: Impressions → Clicks → Conversions
   - Sales Funnel: customizable stages per entity (typically 4 boxes)
   - Stage-to-stage conversion rates + period-over-period change indicators
   - Clickable lead counts with detail modals

3. **Paid Ads Reports** (collapsible, per entity)
   - Blended metrics: Spend, Clicks, Conversions, Impressions, CPA, CPM
   - Campaign-level breakdown table
   - Weekly Summary accordion (aggregated across all entities)

4. **SEO Reports** (collapsible, per entity/property)
   - Organic Sessions with sparkline + vs-prev change
   - Google Search Performance: Impressions, Clicks, Avg Position, CTR
   - Top Search Queries by Clicks (top 10, expandable)
   - Top 10 Landing Pages (with "Other" summary row)
   - New vs Returning Users (visual bar)

### Build Pipeline Data Flow

```
build.py main()
  ├── Parse CLI args (--from/--to, --week, --run-date, --dry-run)
  ├── Compute 7 date presets from anchor date
  ├── For each preset:
  │   ├── Pull Windsor data (Google Ads + Meta campaigns)
  │   ├── Pull GA4 sessions/users (current + previous period)
  │   ├── Pull CRM data per entity (leads, deals, opportunities)
  │   ├── Pull GSC search data (queries, impressions, clicks)
  │   ├── Pull GA4 landing pages + new vs returning
  │   ├── Pull revenue data (POS, invoices, bookings)
  │   ├── Route campaigns to entities via aggregator
  │   ├── Compute funnel metrics per entity
  │   └── Compute period-over-period comparisons
  ├── Assemble report_data dict (all presets + entities + metadata)
  ├── Inject JSON into template.html via string replacement
  └── Push to GitHub (or write locally if --dry-run)
```

### CLI Interface

```bash
python build.py                                              # preset mode (This Month default)
python build.py --from 2026-03-20 --to 2026-03-27           # custom date range
python build.py --week 2026-03-20                            # 7-day range from date
python build.py --dry-run --output index.html                # local only, no GitHub push
```

---

### New Client Configuration

Before building, I need the following information for this client:

#### 1. Business Entities
What are the business units/brands/divisions to report on? For each entity, provide:
- **Entity key** (snake_case identifier, e.g., `main_brand`)
- **Display label** (e.g., "Main Brand Co.")
- **Which sections apply:** Paid Ads? SEO? Funnel? All?
- **Funnel stages** (e.g., New Leads → Qualified → Proposal → Closed Won → Revenue)

#### 2. Ad Platforms (via Windsor.ai)
- **Windsor.ai API key**
- **Google Ads account IDs** (format: XXX-XXX-XXXX) and which entity each maps to
- **Meta/Facebook Ad account IDs** and entity mapping
- **Campaign name routing rules** — how do campaign names map to entities? (e.g., campaigns containing "brand" → `main_brand`)

#### 3. Google Analytics 4
- **GA4 Property IDs** and which entity each maps to
- **Google service account email** (must be added as Viewer to each GA4 property)

#### 4. Google Search Console
- **GSC properties** (domain or URL prefix) and which GA4 property/entity each maps to
- **Google service account email** (must be added as user to each GSC property)

#### 5. CRM Systems
For each CRM in use, I need:
- **Platform name** (HubSpot, GoHighLevel, Salesforce, Pipedrive, etc.)
- **API credentials** (tokens, client ID/secret, etc.)
- **Pipeline/stage definitions** — what are the deal stages and what do they map to in the funnel?
- **Which entity does this CRM serve?**
- **Lead source routing rules** (if one CRM serves multiple entities)
- **Any test/sample records to filter out?**

#### 6. POS / Revenue Systems (if applicable)
- **Platform** (Toast, Square, Clover, Stripe, etc.)
- **API credentials**
- **Restaurant/location GUID** (if POS)
- **What revenue metrics to extract?** (net sales, guest count, order count, discounts, etc.)

#### 7. Other Data Sources (if applicable)
- **Google Calendar** — calendar ID + event title pattern for counting events (e.g., tours, consultations)
- **Google Sheets** — sheet ID + tab name for supplemental lead data
- **Booking platforms** (Tripleseat, Caterease, etc.) — API credentials + lead routing rules
- **Membership/workspace platforms** (OfficeRND, Nexudus, etc.) — API credentials + revenue account IDs

#### 8. Deployment
- **GitHub repo** (owner/name) — I'll create if needed
- **GitHub PAT** (Personal Access Token with repo write access)
- **Netlify site** — connected to the GitHub repo for auto-deploy
- **Custom domain** (optional)

#### 9. Branding (optional)
- **Dashboard title** (default: "[Client Name] Dashboard")
- **Font preference** (default: DM Sans)
- **Accent color** (default: teal `#3ecfb4`)

---

### Important Implementation Notes

1. **Windsor.ai ignores `account_id` parameter** — always filter client-side after fetching
2. **Google service account JWT auth** — use `PyJWT` + `cryptography` to sign JWTs, no google-auth library
3. **All API clients use stdlib only** — `urllib.request` for HTTP, `json` for parsing
4. **Template uses `{DATA_JSON}` placeholder** — replaced by `json.dumps(report_data)` at build time
5. **Period-over-period comparison** — compare current period against the same-length period immediately prior
6. **Entity filter recalculates KPIs** — JavaScript-side, using `window._execRowData` exposed from the scorecard build
7. **Tripleseat tokens rotate** — every refresh token use returns a new one, must persist to disk
8. **Toast rate limiting** — max 4 concurrent workers, retry with exponential backoff
9. **HubSpot dual-account dedup** — fuzzy name matching (>80% character overlap) across accounts

Once you provide the client details above, I'll build the complete dashboard following this architecture.
