# Unified Business Dashboard

### A Real-Time Performance Intelligence Platform for Multi-Entity Portfolios

---

## What It Is

A fully automated, self-contained business intelligence dashboard that consolidates paid advertising, organic search, CRM pipelines, POS systems, and revenue data into a single, always-current report. It is purpose-built for businesses that operate multiple brands, divisions, or locations and need a unified view of performance without logging into a dozen platforms.

The dashboard is delivered as a live web page — no software to install, no logins to manage, no recurring SaaS subscription. Your team opens a URL and sees everything.

---

## The Problem It Solves

Multi-entity businesses face a fragmented data landscape:

- **Ad performance** lives in Google Ads and Meta Ads Manager — separate logins, separate accounts, separate reports for each brand
- **Lead and deal data** lives in one or more CRMs — HubSpot, GoHighLevel, Salesforce, Pipedrive — often with different pipelines per entity
- **Organic traffic and search visibility** lives in Google Analytics and Google Search Console — one property per website, no cross-property view
- **Revenue** is scattered across POS systems, booking platforms, membership platforms, and invoicing tools
- **Period-over-period comparisons** require exporting data, aligning date ranges, and doing manual calculations in spreadsheets

The result: leadership spends hours each week assembling a picture of how the portfolio is performing, or worse, makes decisions based on incomplete data from whichever platform they checked last.

This dashboard eliminates that entirely.

---

## How It Works

An automated pipeline runs on a configurable schedule (daily, weekly, or on-demand) and:

1. **Pulls live data** from every connected platform via their official APIs
2. **Routes and aggregates** the data by business entity, normalizing metrics across platforms
3. **Computes performance metrics** — funnel conversion rates, cost-per-acquisition, period-over-period changes, revenue attribution
4. **Renders a single, self-contained report** — one HTML file with all styling and interactivity embedded
5. **Publishes automatically** — the report is pushed to a hosted URL, live within seconds

No manual exports. No copy-paste. No spreadsheet formulas. The data is always current.

---

## Dashboard Sections

### Executive Summary

The top-level view of portfolio health. Designed for leadership to assess performance in under 30 seconds.

**Portfolio KPI Cards:**
- New Revenue This Period
- Monthly Recurring Revenue (MRR)
- Total Revenue (Month-to-Date)
- Total New Leads (Across All Entities)
- Total Ad Spend (All Platforms Combined)

Each KPI shows the current value and a period-over-period comparison indicator (up/down arrow with percentage change), so you can immediately see whether the portfolio is trending positively.

**Entity Scorecard Table:**
A single table showing every business entity side by side:

| Entity | New Leads | vs Prior Period | Ad Spend | Revenue |
|--------|-----------|----------------|----------|---------|
| Brand A | 45 | +12% | $3,200 | $18,500 |
| Brand B | 22 | -8% | $1,800 | $6,200 |
| Brand C | 61 | +31% | $5,100 | $42,000 |

Lead counts are interactive — click any number to open a dropdown listing every lead by name and status. Click a lead to open a detail modal showing contact info, source, deal stage, and revenue attribution.

---

### Sales & Marketing Funnels

A per-entity view of the full lead-to-revenue pipeline, combining ad platform data with CRM data.

**Marketing Conversion Funnel (3 stages):**
- Total Impressions across all ad platforms
- Total Clicks (with click-through rate)
- Total Conversions (with cost per conversion)

**Sales Funnel (4+ stages, customizable per entity):**
- New Leads entering the pipeline
- Proposals Sent / Consultations Booked / Tours Scheduled
- Deals Closed / Events Booked / Memberships Sold
- Revenue Collected

Each stage shows:
- The count for the current period
- The stage-to-stage conversion rate (e.g., "32% of leads received proposals")
- A period-over-period comparison ("up 15% vs prior period")

**Customizable Funnel Structures:**
Not every business has the same sales process. The dashboard supports custom funnel configurations per entity:
- **Service businesses** (e.g., med spas): New Leads → Consultations Booked → Sales Made → Revenue
- **Event venues**: New Leads → Proposals Sent → Events Booked → Revenue Collected
- **Membership/coworking**: New Leads → Tours Scheduled → Memberships Sold → MRR Generated → Booking Fees → One-Off Sales
- **Restaurants/retail**: Total Guests → Total Orders → Net Sales → Avg per Guest → Avg per Order → Discounts & Comps

**Lead Detail Modals:**
Every lead count in the funnel is clickable. Selecting a lead opens a detail modal showing:
- Contact name, email, and phone
- Lead source (which ad campaign, form, or referral generated them)
- Current deal stage and status
- Event or appointment date
- Revenue attributed to the lead
- Visual status badges (e.g., "CLOSED WON" in green for converted leads)

---

### Paid Ads Reports

Blended performance metrics across Google Ads and Meta (Facebook/Instagram), broken out by entity.

**Per-Entity Metrics (6 KPI cards):**
- Total Spend (blended across platforms)
- Total Clicks
- Total Conversions
- Total Impressions
- Cost Per Acquisition (CPA)
- Cost Per Thousand Impressions (CPM)

**Campaign-Level Breakdown:**
Each entity's accordion expands to show individual campaign performance by platform:

*Google Ads campaigns show:* Spend, Impressions, Clicks, CTR, CPC, Conversions, Cost per Lead

*Meta campaigns show:* Spend, Impressions, Reach, Frequency, Link Clicks, Leads, CPM, CPC, CTR, Engagement

**Weekly Summary Table:**
A cross-entity summary at the bottom aggregates total spend, clicks, conversions, and cost per lead across all entities — giving a single view of total advertising efficiency.

---

### SEO Reports

Organic search performance per entity, pulling directly from Google Analytics 4 and Google Search Console.

**Organic Traffic Overview:**
- Total organic sessions with a visual sparkline trend chart
- Period-over-period change indicator
- Organic conversions and engagement metrics

**Google Search Performance (from Search Console):**
- Total search impressions
- Total clicks from search
- Average search position across all queries
- Click-through rate (CTR)

**Top Search Queries by Clicks:**
A ranked table of the queries driving the most traffic, showing:
- Query text
- Click count
- Average position in search results

The top 10 queries are shown by default, with an expandable "Show all queries" button for the complete list.

**Top Landing Pages:**
The 10 highest-traffic organic landing pages, with an "Other" summary row aggregating the long tail.

**New vs Returning Users:**
A visual bar showing the split between new and returning organic visitors, with session and user counts for each segment.

---

## Interactive Features

### Date Range Selection

Seven preset date ranges are available from a dropdown:
- Today
- Yesterday
- This Week
- Last Week
- Last 7 Days
- This Month (default)
- Last Month

Selecting a preset instantly reloads the entire dashboard with data for that period.

**Custom Date Range:**
A calendar picker allows selection of any arbitrary date range. The dashboard recomputes all metrics client-side — no server round-trip required. The previous comparison period is calculated automatically (same-length period immediately prior).

### Entity Filter

A dropdown in the header filters the entire dashboard to a single business entity:
- All irrelevant sections and accordions are hidden
- The Executive Summary KPI cards recalculate to show only the selected entity's metrics
- The scorecard table filters to show only the selected entity's row
- Period-over-period comparisons update accordingly

This allows a brand manager to see a focused view of just their entity, while leadership can toggle back to the "All Entities" portfolio view.

### Dark & Light Mode

A toggle in the header switches between light and dark themes. The preference is saved to the browser and persists across visits. If no preference is set, the dashboard respects the operating system's theme setting.

Both themes are carefully designed with appropriate contrast ratios, entity-specific color palettes, and smooth transitions.

### PDF Export

A single click exports the current dashboard view as a print-optimized PDF. All sections are expanded, UI controls are hidden, and the layout is reformatted for clean page output. Useful for board meetings, investor updates, or client reporting.

### Lead Modals

Throughout the dashboard, lead counts and names are interactive. Clicking opens a detail modal with full contact information, deal stage, revenue attribution, and source tracking. Modals close via the X button, clicking outside, or pressing Escape.

---

## Supported Integrations

The dashboard connects to the following platforms via their official APIs. Each client deployment is configured with the specific platforms in their stack.

### Advertising Platforms
| Platform | Data Pulled |
|----------|------------|
| **Google Ads** | Campaigns, spend, impressions, clicks, conversions, CTR, CPC, quality score, impression share |
| **Meta Ads** (Facebook & Instagram) | Campaigns, spend, impressions, reach, frequency, link clicks, leads, engagement, CPM |

### Analytics & Search
| Platform | Data Pulled |
|----------|------------|
| **Google Analytics 4** | Sessions, users, new vs returning, landing pages, organic conversions, engagement rate, session duration |
| **Google Search Console** | Search impressions, clicks, average position, CTR, top queries, indexed pages |

### CRM Systems
| Platform | Data Pulled |
|----------|------------|
| **HubSpot** | Deals, contacts, pipeline stages, deal values, close dates, lead sources |
| **GoHighLevel / Moxie** | Opportunities, contacts, pipeline stages, monetary values, appointment status |
| **Salesforce** | Opportunities, contacts, pipeline stages, amounts, close dates |
| **Pipedrive** | Deals, contacts, pipeline stages, values, activities |

### Revenue & POS
| Platform | Data Pulled |
|----------|------------|
| **Toast POS** | Orders, net sales, guest count, average check, discounts & comps |
| **Square POS** | Transactions, net sales, item categories, discounts |
| **Stripe** | Payments, subscriptions, MRR, churn |

### Booking & Membership
| Platform | Data Pulled |
|----------|------------|
| **Tripleseat** | Leads, bookings, event details, revenue, confirmation dates, food/beverage breakdown |
| **OfficeRND** | Members, memberships, invoices (MRR, booking fees, one-off fees), occupancy |
| **Google Calendar** | Event counts by title pattern (tours, consultations, etc.) |

### Supplemental
| Platform | Data Pulled |
|----------|------------|
| **Google Sheets** | Supplemental lead lists, manual tracking data |
| **Facebook Lead Forms** | Lead submissions with automatic CRM sync |

*Not every client uses every integration. The dashboard is configured with only the platforms relevant to your business.*

---

## Technical Characteristics

### Self-Contained
The entire dashboard is a single HTML file — all styling, interactivity, and data are embedded inline. No external dependencies, no CDN failures, no loading spinners. It works offline once loaded.

### Fast
There is no server rendering on page load. Data is pre-computed and embedded. Date range switching and entity filtering happen entirely client-side with zero network requests. The dashboard feels instant.

### Lightweight
No JavaScript frameworks. No CSS libraries. No build tools. Pure vanilla HTML, CSS, and JavaScript. The typical dashboard file is under 800KB — smaller than a single hero image on most websites.

### Responsive
The layout adapts to desktop, tablet, and mobile screens. Metric cards reflow from 3-column to 2-column grids. Tables remain scrollable. Touch targets are appropriately sized for mobile use.

### Secure
API credentials never appear in the published dashboard. All data is pulled server-side during the build process and embedded as static values. The published URL contains no API keys, tokens, or secrets.

### Hosted & Always Available
The dashboard is published to a CDN-backed hosting platform (Netlify) with automatic SSL. It loads in under 1 second from anywhere in the world. No server to maintain, no uptime to monitor.

---

## What You Get

| Deliverable | Description |
|-------------|-------------|
| **Live Dashboard URL** | A permanent, always-updated web address your team bookmarks |
| **Automated Daily Updates** | Data refreshes on a configurable schedule (daily, weekly, or on-demand) |
| **All Integrations Configured** | Every API connection set up, tested, and validated against source data |
| **Custom Entity Configuration** | Your specific brands, divisions, or locations mapped with appropriate funnel stages |
| **Custom Date Presets** | Standard presets (today, this week, this month, etc.) plus custom range support |
| **PDF Export** | One-click export for meetings, board decks, or client reporting |
| **Dark & Light Mode** | Professional appearance in any viewing environment |
| **Ongoing Maintenance** | Token rotation, integration monitoring, and entity updates as your business evolves |

---

## Frequently Asked Questions

**How often does the data update?**
On whatever schedule you choose. Most clients run daily updates, but the build can be triggered hourly, weekly, or on-demand.

**Can I add a new brand or entity later?**
Yes. New entities can be added with their own ad accounts, CRM pipeline, analytics properties, and revenue sources. The dashboard scales to any number of entities.

**Can I remove or hide an entity?**
Yes. Entities can be removed from the dashboard at any time, or temporarily hidden using the entity filter.

**What if I switch CRMs or add a new platform?**
New integrations can be added and existing ones swapped. The modular architecture means changing one data source does not affect the others.

**Do my team members need logins?**
No. The dashboard is a public URL (or can be password-protected if needed). Anyone with the link can view it. There are no user accounts, no per-seat pricing, no access management overhead.

**Can I use this for client reporting?**
Absolutely. The entity filter lets you isolate a single brand's data, and the PDF export produces a clean, professional report. Many operators use this to deliver weekly or monthly performance reports to stakeholders, investors, or franchise owners.

**What happens if an API token expires?**
Most tokens are long-lived or auto-rotating. When manual rotation is needed, it takes under 5 minutes. The maintenance plan includes proactive token monitoring.

**Is my data secure?**
Yes. API credentials are stored server-side and never appear in the published dashboard. The published file contains only pre-computed metrics — no raw customer data, no PII beyond lead names you choose to include, and no API access of any kind.

**Can I run it myself?**
Yes. The build is a single Python command (`python build.py`) that can be run from any machine with Python installed. No proprietary tools, no vendor lock-in.
