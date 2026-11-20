# RSSHub

A CLI application — an RSS feed aggregator that fetches and parses RSS feeds, stores articles in PostgreSQL, and aggregates feeds in the background using a worker pool.

## Overview

RSSHub is a service that collects publications from various RSS sources (news sites, blogs, forums) and centralizes them in one place. Users can add feeds, browse articles, and configure background fetching — all from the command line.

## Features

- Fetch and parse RSS feeds from any URL
- Store articles in PostgreSQL
- Background aggregation with a configurable worker pool
- Dynamically adjustable fetch interval and worker count without restarting
- Full CLI interface with intuitive commands

## Requirements

- Go (gofumpt-formatted code)
- Docker & Docker Compose
- PostgreSQL

## Getting Started

### 1. Clone the repository

```bash
git clone <repo-url>
cd rsshub
```

### 2. Configure environment

Copy or create a `.env` file with the following variables:

```env
# CLI App
CLI_APP_TIMER_INTERVAL=3m
CLI_APP_WORKERS_COUNT=3

# PostgreSQL
POSTGRES_HOST=localhost
POSTGRES_PORT=5432
POSTGRES_USER=postgres
POSTGRES_PASSWORD=changeme
POSTGRES_DBNAME=rsshub
```

### 3. Start services with Docker Compose

```bash
docker compose up -d
```

This starts:
- **PostgreSQL** on port `5432`
- **RSSHub** CLI application

### 4. Build the application

```bash
go build -o rsshub .
```

## Usage

### Start background fetching

Starts the background process that periodically fetches and processes RSS feeds.

```bash
./rsshub fetch
```

Output:
```
The background process for fetching feeds has started (interval = 3 minutes, workers = 3)
```

> Only one instance can run at a time. Running it again while active will log:
> `Background process is already running`

Stop with `Ctrl+C`:
```
Graceful shutdown: aggregator stopped
```

---

### Add a new RSS feed

```bash
./rsshub add --name "tech-crunch" --url "https://techcrunch.com/feed/"
```

---

### List available feeds

```bash
./rsshub list --num 5
```

Without `--num`, shows all feeds.

Output:
```
# Available RSS Feeds

1. Name: tech-crunch
   URL: https://techcrunch.com/feed/
   Added: 2025-06-10 15:34

2. Name: hacker-news
   URL: https://news.ycombinator.com/rss
   Added: 2025-06-10 15:37
```

---

### Show latest articles

```bash
./rsshub articles --feed-name "tech-crunch" --num 5
```

Default is 3 articles if `--num` is not provided.

Output:
```
Feed: tech-crunch

1. [2025-06-18] Apple announces new M4 chips for MacBook Pro
   https://techcrunch.com/apple-announces-m4/

2. [2025-06-17] OpenAI launches GPT-5 with multimodal capabilities
   https://techcrunch.com/openai-launches-gpt-5/
```

---

### Delete a feed

```bash
./rsshub delete --name "tech-crunch"
```

---

### Set fetch interval (while `fetch` is running)

```bash
./rsshub set-interval 2m
```

Output:
```
Interval of fetching feeds changed from 3 minutes to 2 minutes
```

---

### Set number of workers (while `fetch` is running)

```bash
./rsshub set-workers 5
```

Output:
```
Number of workers changed from 3 to 5
```

---

### Help

```bash
./rsshub --help
```

Output:
```
Usage:
  rsshub COMMAND [OPTIONS]

Common Commands:
     add             add new RSS feed
     set-interval    set RSS fetch interval
     set-workers     set number of workers
     list            list available RSS feeds
     delete          delete RSS feed
     articles        show latest articles
     fetch           starts the background process that periodically fetches and processes RSS feeds using a worker pool
```

## Example Workflow

**Terminal 1 — start the aggregator:**
```bash
./rsshub fetch
# The background process for fetching feeds has started (interval = 3 minutes, workers = 3)

# Stop with Ctrl+C
# Graceful shutdown: aggregator stopped
```

**Terminal 2 — manage feeds and settings:**
```bash
./rsshub add --name "hacker-news" --url "https://news.ycombinator.com/rss"
./rsshub set-interval 2m
# Interval of fetching feeds changed from 3 minutes to 2 minutes

./rsshub set-workers 5
# Number of workers changed from 3 to 5

./rsshub articles --feed-name "hacker-news" --num 3
```

## Database Schema

### `feeds` table

| Column | Type | Description |
|---|---|---|
| id | UUID (PK) | Unique identifier |
| created_at | TIMESTAMP | When the feed was added |
| updated_at | TIMESTAMP | When the feed was last updated |
| name | TEXT (unique) | Human-readable name |
| url | TEXT | RSS feed URL |

### `articles` table

| Column | Type | Description |
|---|---|---|
| id | UUID (PK) | Unique identifier |
| created_at | TIMESTAMP | When the article was stored |
| updated_at | TIMESTAMP | When the article was last modified |
| title | TEXT | Article title |
| link | TEXT | Article URL |
| published_at | TIMESTAMP | Original publication date |
| description | TEXT | Short summary from the RSS feed |
| feed_id | UUID (FK) | References `feeds.id` |

## Migrations

Database migrations are stored in the `migrations/` directory:

```
migrations/
├── create_feeds_table.up.sql
├── create_feeds_table.down.sql
├── create_articles_table.up.sql
└── create_articles_table.down.sql
```

- `*.up.sql` — applies the migration
- `*.down.sql` — rolls back the migration

## Default Configuration

| Setting | Default |
|---|---|
| Fetch interval | 3 minutes |
| Worker count | 3 |

Both values are configurable via environment variables and can be changed at runtime without restarting.

## Sample RSS Feeds

| Source | URL |
|---|---|
| TechCrunch | https://techcrunch.com/feed/ |
| Hacker News | https://news.ycombinator.com/rss |
| BBC World | https://feeds.bbci.co.uk/news/world/rss.xml |
| Ars Technica | http://feeds.arstechnica.com/arstechnica/index |
| The Verge | https://www.theverge.com/rss/index.xml |
| UN News | https://news.un.org/feed/subscribe/ru/news/all/rss.xml |

## Authors

- akkassymb
- daarapbek