# 506 Ramseur — Paid Media Report Builder

Pulls data from Windsor.ai, aggregates it by business entity, generates a
single-page HTML report, and publishes it to GitHub → Netlify.

## Setup

```bash
# Python 3.11+ required; no external packages needed (stdlib only)
cd 506-ramseur-report

# Add your Windsor.ai API key to .env
echo "WINDSOR_API_KEY=your_key_here" > .env
```

## Run

```bash
python build.py --week 2026-03-22
```

`--week` must be the **Monday** of the report week (ISO date).

### Options

| Flag | Description |
|------|-------------|
| `--week YYYY-MM-DD` | Monday of the report week (required) |
| `--dry-run` | Build HTML but skip the GitHub push |
| `--output path/to/file.html` | Also save a local copy of the HTML |

## Project structure

```
506-ramseur-report/
├── build.py          # Main script: pull → aggregate → render → push
├── windsor.py        # Windsor.ai REST API client
├── aggregator.py     # Routing + aggregation logic
├── template.html     # HTML report template (vanilla JS, no dependencies)
├── github_push.py    # GitHub Contents API helper
├── .env              # WINDSOR_API_KEY=xxx  (gitignored)
└── .gitignore
```

## Business entities

| Entity | Sources |
|--------|---------|
| Events at 506 | Google (DBC "Events" campaigns), Meta 1842950213182409 |
| Private Office | Google (DBC "Office" campaigns), Meta 1842950213182409 |
| Catering — Nerra | Google 242-168-9888, Meta 1842950213182409 |
| Oak & Glow Med Spa | Google 823-166-5237, Meta 2291771151276627 |
| Smashing Boxes | Google 304-843-0196 (PMax), LinkedIn 525741235 |
| Coworking | Google (DBC "Coworking" campaigns), Meta 1842950213182409 |

## Deployment

GitHub repo: `rspuller/506-ramseur-media-report`
Branch: `main`, file: `index.html`
Netlify auto-publishes on every push.
