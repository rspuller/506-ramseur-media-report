# Client Dashboard — Implementation Playbook

Complete step-by-step guide for setting up a new client dashboard, from initial discovery through deployment and ongoing maintenance.

---

## Phase 0: Client Discovery & Scoping (Before Any Technical Work)

### 0.1 — Define Business Entities
Meet with the client and map out their portfolio:

| Question | Why It Matters |
|----------|---------------|
| How many brands/divisions/locations do you have? | Determines entity count and routing complexity |
| Which entities run paid ads? | Determines which entities get Paid Ads section |
| Which entities have websites with analytics? | Determines SEO section scope |
| What CRM(s) do you use, and for which entities? | Determines funnel data sources |
| Do you have a POS system (restaurant/retail)? | Determines revenue source |
| Do you have a booking/event platform? | Determines booking/event funnel |
| Do you have a membership/workspace platform? | Determines MRR tracking |

**Deliverable:** Entity matrix spreadsheet:

| Entity Key | Display Name | Paid Ads? | SEO? | CRM | POS? | Booking? | Membership? |
|------------|-------------|-----------|------|-----|------|----------|-------------|
| `main_brand` | Main Brand Co. | Yes (Google + Meta) | Yes | HubSpot | No | No | No |
| `restaurant` | The Restaurant | No | Yes | No | Toast | No | No |
| `medspa` | The Med Spa | Yes (Google + Meta) | Yes | GoHighLevel | No | No | No |

### 0.2 — Define Funnel Stages Per Entity
For each entity with a CRM, define the sales funnel:

**Standard funnel (4 stages):**
1. New Leads
2. Proposals Sent / Appointments Set
3. Deals Closed / Events Booked
4. Revenue Collected

**Custom funnels (examples):**
- **Restaurant/POS:** Total Guests → Total Orders → Net Sales → Avg/Guest → Avg/Order → Discounts
- **Coworking/Membership:** New Leads → Tours Scheduled → Memberships Sold → MRR Generated → Booking Fees → One-Off Sales
- **Events venue:** New Leads → Proposals Sent → Events Booked → Revenue Collected (with food/catering split)

### 0.3 — Agree on KPI Definitions
Clarify upfront what each metric means:

| KPI | Definition to Confirm |
|-----|----------------------|
| "New Revenue" | Only revenue from new deals closed THIS period? Or all revenue received? |
| "MRR" | Monthly recurring revenue — from what source? Memberships? Subscriptions? |
| "Total Revenue" | Sum of all revenue streams or just specific ones? |
| "New Leads" | CRM leads created in period? Or all leads including carryover? |
| "Ad Spend" | Google + Meta combined? Include agency fees? |

---

## Phase 1: Account & API Setup (Client-Side Tasks)

### 1.1 — Windsor.ai Account
**Time:** 15-30 minutes | **Who:** You or client

1. Sign up at [windsor.ai](https://windsor.ai) (paid plan required for API access)
2. Connect data sources:
   - **Google Ads** — OAuth connect for each account
   - **Meta Ads** — OAuth connect for each Business Manager
   - **Facebook Lead Forms** (if applicable) — separate connector
3. Note the **API key** from Settings → API
4. Note all **account IDs** visible in the connected accounts

**Important:** Windsor ignores account_id filters server-side. You must filter client-side.

### 1.2 — Google Cloud Project & Service Account
**Time:** 30-45 minutes | **Who:** You

1. Go to [console.cloud.google.com](https://console.cloud.google.com)
2. Create a new project (e.g., `clientname-dashboard`)
3. Enable these APIs:
   - Google Analytics Data API (v1beta)
   - Google Search Console API
   - Google Calendar API (if needed)
4. Create a **Service Account:**
   - IAM & Admin → Service Accounts → Create
   - Name: `dashboard-reader` (or similar)
   - No roles needed at project level
   - Create a JSON key → download as `service_account.json`
5. Note the **service account email** (format: `name@project.iam.gserviceaccount.com`)

### 1.3 — Google Analytics 4 Access
**Time:** 5 min per property | **Who:** Client (or you with admin access)

For each GA4 property:
1. Go to GA4 Admin → Property → Property Access Management
2. Add the service account email as **Viewer**
3. Note the **Property ID** (numeric, found in Admin → Property Settings)

**Property ID mapping example:**
```
529628224 → main_brand (smashingboxes.com)
526086629 → medspa (oakandglowmedspa.com)
296398889 → restaurant (nerracatering.com)
```

### 1.4 — Google Search Console Access
**Time:** 5 min per property | **Who:** Client (or you with admin access)

For each GSC property:
1. Go to [search.google.com/search-console](https://search.google.com/search-console)
2. Settings → Users and permissions → Add user
3. Add service account email as **Full** or **Restricted** user
4. Note the **property format:**
   - Domain property: `sc-domain:example.com`
   - URL prefix: `https://www.example.com/`

### 1.5 — CRM API Access
Set up per CRM platform in use:

#### HubSpot
1. Settings → Integrations → Private Apps → Create
2. Scopes needed: `crm.objects.deals.read`, `crm.objects.contacts.read`, `crm.schemas.deals.read`
3. Copy the **Personal Access Token** (starts with `pat-`)
4. Note the **pipeline ID** (Settings → Deals → Pipelines)
5. Note **deal stage IDs** (API: `GET /crm/v3/pipelines/deals`)

#### GoHighLevel (Moxie)
1. Settings → Business Profile → API Keys (or Agency → Settings)
2. Generate API key with scopes: `contacts.readonly`, `opportunities.readonly`, `opportunities.write` (for lead sync)
3. Copy the **API token** (starts with `pit-`)
4. Note: **Location ID**, **Pipeline ID** (from URL or API)
5. Note **stage names/IDs** (API: `GET /opportunities/pipelines`)

#### Pipedrive
1. Settings → Personal preferences → API → Generate token
2. Copy the **API token**
3. Note **pipeline ID** and **stage IDs**

#### Salesforce
1. Setup → Apps → App Manager → New Connected App
2. Enable OAuth, set callback URL, select scopes
3. Note **Consumer Key**, **Consumer Secret**, **Security Token**
4. More complex — may need a dedicated connector file

### 1.6 — POS System Access (if applicable)

#### Toast
1. Toast Developer Portal → Create API Client
2. Note **Client ID**, **Client Secret**
3. Note **Restaurant GUID** (from Toast admin URL or API)
4. API host: `https://ws-api.toasttab.com`

#### Square
1. Developer Dashboard → Create Application
2. Note **Access Token**, **Location ID**
3. API host: `https://connect.squareup.com`

### 1.7 — Booking Platform Access (if applicable)

#### Tripleseat
1. Contact Tripleseat for API access (requires Enterprise plan)
2. Register an OAuth 2.0 application (get Client ID + Secret)
3. Complete the initial OAuth authorization code flow (one-time browser step)
4. Store initial tokens in `.tripleseat_tokens.json`
5. **Critical:** Tokens rotate on every use — the code must persist new tokens to disk

### 1.8 — Membership/Workspace Platform (if applicable)

#### OfficeRND
1. Admin → Settings → Integrations → API
2. Create OAuth client with `client_credentials` grant
3. Note **Client ID**, **Client Secret**, **Org Slug**
4. Identify **revenue account IDs** for: Membership Fees, Booking Fees, One-Off Fees
   - API: `GET /api/v1/organizations/{slug}/accounts`

### 1.9 — Google Calendar (if applicable)
1. Share the relevant calendar with the service account email (Viewer permission)
2. Note the **Calendar ID** (usually the owner's email or a generated ID)
3. Define the **event title pattern** to search for (e.g., "Office Tour at ClientCo")

### 1.10 — Google Sheets Supplemental Data (if applicable)
1. Share the sheet with the service account email (Viewer) or make it public
2. Note the **Sheet ID** (from the URL) and **Tab name**
3. Define which columns map to: name, email, date, status

### 1.11 — GitHub + Netlify Deployment
**Time:** 15 minutes | **Who:** You

1. **GitHub:**
   - Create a new repo (e.g., `clientname-dashboard`)
   - Generate a **Personal Access Token** (PAT) with `repo` scope
   - Initial commit: just a placeholder `index.html`

2. **Netlify:**
   - Log in → New site from Git → Connect to the new repo
   - Build settings: leave blank (we push pre-built HTML)
   - Note the **Netlify URL** (e.g., `clientname-dashboard.netlify.app`)
   - Optional: configure custom domain

---

## Phase 2: Project Setup (Technical)

### 2.1 — Create Project Directory
```
mkdir client-dashboard
cd client-dashboard
```

### 2.2 — Create .env File
```env
WINDSOR_API_KEY=your_windsor_api_key_here
```

### 2.3 — Place Credential Files
- Copy `service_account.json` into project root
- Create `.tripleseat_tokens.json` with initial tokens (if using Tripleseat)

### 2.4 — Install Python Dependencies
```bash
pip install PyJWT cryptography
```
These are the ONLY pip dependencies. Everything else uses Python stdlib.

### 2.5 — Create Client Configuration
Create a `client_config.py` (or define constants in each file) with:

```python
# -- Entity Configuration --
ENTITY_ORDER = ["brand_a", "brand_b", "brand_c"]
ENTITY_LABELS = {
    "brand_a": "Brand A",
    "brand_b": "Brand B",
    "brand_c": "Brand C",
}

# -- Google Ads Account IDs --
GOOGLE_ACCOUNTS = {
    "123-456-7890": "brand_a",
    "098-765-4321": "brand_b",
}

# -- Meta Account IDs --
META_ACCOUNTS = ["1234567890", "0987654321"]

# -- GA4 Property IDs --
GA4_PROPERTIES = ["123456789", "987654321"]

# -- GSC Property Mapping --
PROPERTY_TO_SITE = {
    "123456789": "sc-domain:branda.com",
    "987654321": "sc-domain:brandb.com",
}

# -- Deployment --
GITHUB_TOKEN = "ghp_..."
REPO_OWNER = "your-github-user"
REPO_NAME = "client-dashboard"
```

---

## Phase 3: Build the Dashboard (Claude Code Session)

### 3.1 — Start a New Claude Code Session
Open the project directory in Claude Code and paste the contents of `CLAUDE_SESSION_PROMPT.md`.

### 3.2 — Provide Client Details
Answer each section of the prompt with the specifics gathered in Phases 0-1.

### 3.3 — Build Order
Claude will build the files roughly in this order:

1. **windsor.py** — Windsor.ai API client (Google Ads, Meta, FB Leads)
2. **aggregator.py** — Campaign routing + entity aggregation
3. **ga4_direct.py** — GA4 Data API client
4. **gsc_direct.py** — GSC API client
5. **[crm].py** — One connector per CRM platform
6. **[pos].py** — POS connector (if applicable)
7. **[booking].py** — Booking platform connector (if applicable)
8. **[membership].py** — Membership platform connector (if applicable)
9. **gcal.py** — Google Calendar connector (if applicable)
10. **office_leads.py** — Google Sheets connector (if applicable)
11. **build.py** — Main orchestrator
12. **template.html** — Full HTML/CSS/JS template
13. **github_push.py** — Deployment script

### 3.4 — Test Locally
```bash
python build.py --dry-run --output index.html
```
Open `index.html` in a browser to verify.

### 3.5 — Deploy
```bash
python build.py
```
This pushes to GitHub → Netlify auto-deploys.

---

## Phase 4: Validation & QA

### 4.1 — Data Accuracy Checklist

| Check | How to Verify |
|-------|---------------|
| Ad spend matches platform | Compare dashboard totals to Google Ads / Meta Ads Manager |
| Lead counts match CRM | Compare dashboard leads to CRM filtered view for same date range |
| Revenue matches POS/invoices | Compare dashboard revenue to POS reports or accounting |
| GA4 sessions match | Compare to GA4 → Reports → Acquisition for same date range |
| GSC clicks/impressions match | Compare to GSC → Performance for same date range |
| Period-over-period % changes | Manually calculate for one entity to verify formula |
| Entity filter KPIs | Select each entity and verify KPI cards recalculate correctly |
| Date preset switching | Click each preset; verify data refreshes correctly |
| Custom date range | Pick a known date range and verify against source systems |

### 4.2 — Visual & UX Checklist

- [ ] All accordions expand/collapse correctly
- [ ] Dark mode toggle works and persists on reload
- [ ] Lead count dropdowns open and show correct lead details
- [ ] Lead modals display properly with all fields
- [ ] Entity filter dropdown filters all sections correctly
- [ ] PDF export produces clean output (no UI controls visible)
- [ ] Mobile responsive — test at 375px, 768px, 1024px widths
- [ ] Sparkline charts render (if included)
- [ ] "Show all queries" expander works in SEO tables
- [ ] No JavaScript console errors

---

## Phase 5: Client Handoff

### 5.1 — Deliver
- Share the Netlify URL
- Explain the preset date picker and custom range
- Walk through each section
- Show dark/light mode toggle
- Show entity filter
- Show PDF export

### 5.2 — Document
Create a `SETUP.md` in the project with:
- Client name and entities
- All API credential locations (not the credentials themselves)
- How to regenerate/rotate tokens
- How to run manually: `python build.py`
- Netlify URL and GitHub repo

### 5.3 — Schedule Automated Runs (Optional)
Options for automated daily/weekly builds:

**Option A: Windows Task Scheduler**
```
Action: Start a program
Program: python
Arguments: C:\path\to\build.py
```

**Option B: GitHub Actions**
```yaml
name: Build Dashboard
on:
  schedule:
    - cron: '0 8 * * *'  # Daily at 8 AM UTC
  workflow_dispatch:       # Manual trigger
jobs:
  build:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - uses: actions/setup-python@v5
        with:
          python-version: '3.11'
      - run: pip install PyJWT cryptography
      - run: python build.py
        env:
          WINDSOR_API_KEY: ${{ secrets.WINDSOR_API_KEY }}
```

**Option C: Claude Code Scheduled Task**
Use the `/schedule` skill to create a recurring build trigger.

---

## Phase 6: Ongoing Maintenance

### 6.1 — Token Rotation Schedule

| Credential | Rotation Frequency | How to Rotate |
|------------|-------------------|---------------|
| Windsor API key | Rarely (unless compromised) | Windsor dashboard → Settings → Regenerate |
| Google service account | Never (key file is permanent) | Only rotate if compromised |
| HubSpot PAT | Annually or as needed | HubSpot → Settings → Private Apps → Rotate |
| GoHighLevel token | Varies (check expiry) | GHL → Settings → API → Regenerate |
| Tripleseat tokens | Auto-rotating (every use) | Automatic — just ensure `.tripleseat_tokens.json` is writable |
| Toast client secret | Rarely | Toast Developer Portal → Regenerate |
| GitHub PAT | 90 days (recommended) | GitHub → Settings → Developer settings → Tokens |
| OfficeRND OAuth | Token refreshes automatically | Only rotate client secret if compromised |

### 6.2 — Common Issues & Fixes

| Issue | Likely Cause | Fix |
|-------|-------------|-----|
| Dashboard shows $0 revenue | Token expired or API error | Check API credentials, re-run build |
| Missing entity in report | No campaign data in period | Verify ads are running; check entity routing rules |
| Windsor returns empty data | Account disconnected | Re-authorize in Windsor dashboard |
| GA4 shows 0 sessions | Service account lost access | Re-add service account to GA4 property |
| Tripleseat auth failure | Token file corrupted | Delete `.tripleseat_tokens.json`, re-authorize |
| GitHub push fails | PAT expired | Generate new PAT, update `github_push.py` |
| Netlify not updating | Build succeeded but old cache | Clear Netlify cache, or check deploy logs |

### 6.3 — Adding a New Entity
1. Add entity key + label to `aggregator.py` (ENTITY_ORDER, ENTITY_LABELS)
2. Add campaign routing rules to `aggregator.py`
3. Add CRM connector (or update existing one) for the new entity
4. Add GA4 property ID to the properties list
5. Add GSC property mapping
6. Update `build.py` to include the new entity in data pulls
7. Run `python build.py --dry-run` to verify

### 6.4 — Removing an Entity
1. Remove from ENTITY_ORDER and ENTITY_LABELS
2. Remove campaign routing rules
3. Remove GA4/GSC property mappings
4. Remove CRM data pull calls from `build.py`
5. Optionally remove the connector .py file

---

## Appendix A: API Quick Reference

| Platform | Base URL | Auth Type | Docs |
|----------|----------|-----------|------|
| Windsor.ai | `https://connectors.windsor.ai` | API Key (query param) | [docs.windsor.ai](https://docs.windsor.ai) |
| GA4 Data API | `https://analyticsdata.googleapis.com/v1beta` | JWT Bearer | [developers.google.com/analytics/devguides/reporting/data/v1](https://developers.google.com/analytics/devguides/reporting/data/v1) |
| GSC API | `https://www.googleapis.com/webmasters/v3` | JWT Bearer | [developers.google.com/webmaster-tools](https://developers.google.com/webmaster-tools) |
| Google Calendar | `https://www.googleapis.com/calendar/v3` | JWT Bearer | [developers.google.com/calendar](https://developers.google.com/calendar) |
| HubSpot | `https://api.hubapi.com` | Bearer (PAT) | [developers.hubspot.com](https://developers.hubspot.com) |
| GoHighLevel | `https://services.leadconnectorhq.com` | Bearer (API Key) | [highlevel.stoplight.io](https://highlevel.stoplight.io) |
| Tripleseat | `https://api.tripleseat.com/v1/` | OAuth 2.0 | [support.tripleseat.com/api](https://support.tripleseat.com) |
| Toast | `https://ws-api.toasttab.com` | OAuth 2.0 Client Credentials | [doc.toasttab.com](https://doc.toasttab.com) |
| OfficeRND | `https://app.officernd.com/api/v1/organizations/{slug}` | OAuth 2.0 Client Credentials | [developer.officernd.com](https://developer.officernd.com) |
| GitHub Contents | `https://api.github.com` | Bearer (PAT) | [docs.github.com/rest](https://docs.github.com/rest) |

## Appendix B: Estimated Setup Time Per Component

| Component | Time Estimate | Depends On |
|-----------|--------------|------------|
| Windsor.ai setup | 15-30 min | Client granting ad account access |
| Google Cloud project | 30-45 min | — |
| GA4 access (per property) | 5 min | Client admin access |
| GSC access (per property) | 5 min | Client admin access |
| HubSpot setup | 15 min | Client admin access |
| GoHighLevel setup | 15 min | Client admin access |
| Toast setup | 30 min | Toast developer portal access |
| Tripleseat setup | 45-60 min | Enterprise plan + API approval |
| OfficeRND setup | 20 min | Admin access |
| GitHub + Netlify | 15 min | — |
| **Code build (Claude Code)** | **2-4 hours** | All credentials collected |
| **QA & validation** | **1-2 hours** | Build complete |
| **Total (typical 3-entity client)** | **5-8 hours** | — |

## Appendix C: Pricing Considerations

When pricing this as a service, factor in:

| Cost Component | Notes |
|----------------|-------|
| **Windsor.ai subscription** | Required for Google Ads + Meta data; pricing per connector |
| **Google Cloud** | GA4/GSC/Calendar APIs are free within quotas |
| **Netlify hosting** | Free tier is sufficient (static HTML) |
| **GitHub** | Free tier is sufficient |
| **Your time: initial setup** | See Appendix B estimates |
| **Your time: ongoing maintenance** | Token rotation, entity changes, troubleshooting |
| **Your time: monthly QA** | Spot-check data accuracy against source systems |
