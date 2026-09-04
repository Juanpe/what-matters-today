# What Matters Today

*[Leer en español](README.es.md)*

Automated daily digest covering **Apple, AI Software, AI (general), and Tech**, published every morning at 8:00 AM (Spain time) at:

**👉 [whatmatterstoday.juanpecatalan.com](https://whatmatterstoday.juanpecatalan.com)**

Available in Spanish and English, with a language switcher on the site itself.

## What it is

Every day, a workflow collects the most relevant news across those four topics, deduplicates it, writes a summary (in both Spanish and English), and publishes it as a dated digest. The site shows the current day's digest (`index.html`) and a browsable date archive (`archive.html`).

## How it works

The entire pipeline (source fetching → deduplication → summarizing/writing → publishing) is orchestrated with **[n8n](https://n8n.io)**, hosted outside this repository. This repo **does not contain the n8n workflow**: it's only the destination where n8n publishes its output, and the static site that displays it.

Each workflow run:

1. Generates the day's digest as JSON, with every editorial field written in both Spanish and English.
2. Appends it to `data/archive/YYYY-MM-DD.json` and updates `data/latest.json`.
3. Updates `data/archive-index.json` with the list of available dates.
4. Commits and pushes the changes to this repository.

The static site (no build step, no backend) reads those JSON files directly to render the current digest and the historical archive, in whichever language each reader picks.

## Repository structure

```
.
├── index.html              # Today's digest view (reads data/latest.json)
├── archive.html            # Archive view (reads data/archive-index.json and data/archive/*.json)
├── data/
│   ├── latest.json         # Most recent digest
│   ├── archive-index.json  # Index of available archive dates
│   └── archive/
│       └── YYYY-MM-DD.json # Each day's digest
├── favicon-32.png, icon-512.png, apple-touch-icon.png
└── README.md
```

## Digest format (`data/latest.json` / `data/archive/*.json`)

Every editorial text field is an object `{ "es": "...", "en": "..." }`. Metadata fields (`id`, `topic`, `source`, `link`, `image`) are plain strings.

```jsonc
{
  "date": "YYYY-MM-DD",
  "daily_summary": { "es": "...", "en": "Overall summary of the day" },
  "topic_summaries": {
    "apple": { "es": "...", "en": "..." },
    "ai_software": { "es": "...", "en": "..." },
    "ai_general": { "es": "...", "en": "..." },
    "tech": { "es": "...", "en": "..." }
  },
  "top_stories": [
    {
      "id": "...",
      "title": { "es": "...", "en": "..." },
      "body": { "es": "...", "en": "..." },
      "topic": "apple | ai_software | ai_general | tech",
      "source": "Outlet name",
      "link": "https://...",
      "image": "https://..."
    }
  ],
  "quick_reads": [
    { "id": "...", "title": { "es": "...", "en": "..." }, "text": { "es": "...", "en": "..." } }
  ],
  "trending_repos": [
    { "id": "...", "why_it_matters": { "es": "...", "en": "..." } }
  ],
  "developer_signals": [
    { "id": "...", "text": { "es": "...", "en": "..." } }
  ]
}
```

The site also accepts the legacy format (a plain string instead of `{es, en}`) for backward compatibility with digests generated before the pipeline became bilingual.

## Topics covered

- **Apple** — products, litigation, leaks, events.
- **AI Software** — AI-assisted development tools, models, and products.
- **AI (general)** — adoption, research, and AI applications outside of pure software.
- **Tech** — general technology news.

## Publishing

The site is static (HTML + JSON, no frameworks, no build step) and is served directly from this repository.
