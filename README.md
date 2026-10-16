# News Aggregator CLI

A command-line tool that collects headlines from multiple sources, stores them in SQLite, and lets you filter and export them to CSV, Excel or JSON.

## Features

- **Multiple sources**: RSS/Atom feeds (BBC, NPR, NYT, Guardian, TechCrunch), HTML scraping (Hacker News via BeautifulSoup), and optional [NewsAPI](https://newsapi.org) top headlines.
- **Filters**: by source, keyword (title/summary) and date range.
- **Persistent storage**: results are saved to a SQLite database (`news.db`) for later queries.
- **Deduplication**: headlines are normalized (case and punctuation ignored), so the same story is stored once, even across outlets and repeated fetches.
- **Export**: CSV (Excel-friendly UTF-8), Excel `.xlsx` (formatted header, frozen pane), and JSON.

## Install

```bash
pip install -r requirements.txt
```

Requires Python 3.9+.

## Usage

```bash
# Fetch from all free sources (bbc, npr, nytimes, guardian, techcrunch, hackernews)
python -m news_aggregator fetch

# Fetch only some sources
python -m news_aggregator fetch bbc hackernews

# NewsAPI (set a key first: https://newsapi.org/register)
export NEWSAPI_KEY=your_key        # PowerShell: $env:NEWSAPI_KEY="your_key"
python -m news_aggregator fetch newsapi -q "climate"

# Show stored headlines with filters
python -m news_aggregator list
python -m news_aggregator list -s bbc -k election --since 2025-10-01 --until 2025-10-15 -n 20

# Export (same filters as `list`)
python -m news_aggregator export -f csv  -o headlines.csv
python -m news_aggregator export -f xlsx -k python -o python_news.xlsx
python -m news_aggregator export -f json -s npr

# Utilities
python -m news_aggregator sources
python -m news_aggregator stats
```

Use `--db path/to/file.db` (or the `NEWS_DB` env var) to choose a different database.

If one source fails (network error, blocked, bad key), the others still run and the error is reported.

## Project structure

```
news_aggregator/
  cli.py        argparse commands: fetch, list, export, sources, stats
  sources.py    RSS/Atom parser, Hacker News scraper, NewsAPI client
  storage.py    SQLite store, filtering, dedup via unique normalized-title hash
  exporters.py  CSV / Excel / JSON writers
  models.py     Article dataclass, text and date normalization
tests/          offline unit tests (no network needed)
```

## Tests

```bash
python -m pytest
```
