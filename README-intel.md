# WIKIGOTHAM — Intel

Real-time Wikipedia intelligence dashboard. Live edit stream, activity rankings, article monitoring.

## What is this?

An ops-room style dashboard that monitors English Wikipedia in real-time. Every edit, every new article, every deletion, every page move — streaming live via Wikipedia's EventStreams API. Designed for spotting breaking news, controversies, and trending topics as they emerge on Wikipedia, often before they hit mainstream media.

## How to use

1. Download `wikigotham-intel.html`
2. Open in any browser
3. Data starts flowing immediately. Leave it open.

## Dashboard layout

### ⚡ Live Feed (Panel 1)
Real-time stream of every edit happening on English Wikipedia's main namespace.

- Each edit shows: article title (clickable → opens Wikipedia), editor, byte change (+/- in green/red), edit comment
- Green dot = new article created
- Amber **BOT** badge for automated edits
- Green **NEW** badge for article creations
- **Bots checkbox** — toggle bot edits on/off
- **⏸ PAUSE / ▶ RESUME** — freeze the feed to read without scrolling. Edits are buffered during pause and burst into the feed when you resume. Stats keep counting in the background.
- Hover any item for full details (editor, complete edit summary, byte change)

### NEW / MOVE / DEL (Panel 2)
Toggle between three views:

- **NEW** (green) — newly created Wikipedia articles. Pre-loaded from API + live stream additions. Articles that just became notable enough for Wikipedia.
- **MOVE** (amber) — page renames/moves. Pre-loaded last 24 hours + live stream. Shows old title → new title. Articles get renamed when they become more notable or during controversies.
- **DEL** (red) — deleted articles with deletion reasons. Pre-loaded from API. Auto-refreshes every 60 seconds with countdown bar. Deletion reasons often reveal why (notability, copyright, spam, attack page).

Time selector for NEW and DEL: 1H / 6H / 12H / 24H

Hover any item for full details (creator/deleter, reason, summary).

### EDITS / CHURN / REVERTS (Panel 3)
Three sort modes on the same data — toggle to answer different questions:

- **EDITS** (amber) — ranked by edit count. Shows: edit count, revert count if any. "Which articles are being touched the most?"
- **CHURN** (green) — ranked by total bytes moved (adds + removes combined). Shows: total churn (cyan), net change (+/- green/red), edit count. "Where is the most content being rewritten?"
- **REVERTS** (red) — ranked by revert count, filtered to articles with reverts. Shows: revert count, edit count, revert ratio %. "Where are editors fighting?"

Time selector: 10M / 1H / 6H / 24H

Auto-refreshes every 60 seconds with countdown progress bar.

Hover any data badge for explanation of what it means.

### ▲ Viewed (Panel 4)
Most viewed Wikipedia articles ranked by page views.

- **YDAY** — yesterday's top 50 (most recent available data)
- **WEEK** — last 7 days merged
- **MONTH** — last completed month
- **YEAR** — last 3 months merged

Note: The Wikimedia pageviews API minimum granularity is daily. There is no hourly "most viewed" endpoint.

### Resizable panels
Drag the dividers between any panels to resize them. Give more space to whichever panel matters most.

### Session bar (bottom)
Persistent stats tracking from the moment you opened the page:

- **Uptime** — how long the session has been running
- **Articles** — unique articles touched this session
- **Editors** — unique editors seen this session
- **Most active** — article with the highest cumulative churn (total bytes moved across all edits). Clickable link to Wikipedia.
- **⬇ EXPORT** — export a snapshot of the current dashboard state:
  - **📄 JSON** — structured data file for analysis
  - **🌐 HTML** — styled dark-themed report that opens in a browser

### Page Moves (below the fold)
Scroll down past the session bar to see the full page moves log. Pre-loaded last 24 hours + live stream. Low volume but occasionally reveals significant renames.

## Tooltips
Every interactive element has a hover tooltip explaining what it does:
- Toggle buttons explain each mode
- Data badges explain what the numbers mean
- Panel headers explain the data source
- Feed items show full edit summaries, deletion reasons, move reasons

## Data sources

| Data | Source | Update |
|------|--------|--------|
| Live edits | EventStreams SSE (`stream.wikimedia.org`) | Real-time |
| New articles | EventStreams + `recentchanges` API pre-load | Real-time + API |
| Page moves | EventStreams + `logevents` API pre-load | Real-time + API |
| Deleted articles | `logevents` API | Every 60 seconds |
| Edits/Churn/Reverts | `recentchanges` API with tags + sizes | Every 60 seconds |
| Most viewed | Wikimedia pageviews REST API | On time window change |

## Why this is useful

Wikipedia edit activity is a leading indicator for news events. Traders, journalists, and analysts have noticed that:

- An article spiking from 0 to 15 edits in 10 minutes often means something is about to break
- The "Deaths in 20XX" article getting edited = someone notable just died
- High revert ratio = contested/controversial topic
- New articles being created = new notable events or people emerging
- Page moves from Draft → Main namespace = topic just became notable

Wikipedia editors are often faster than news outlets because they're updating articles the moment information becomes available.

## Technical details

- Single HTML file (~600 lines) with React via CDN
- Three simultaneous EventSource (SSE) connections: recentchange, page-move
- No authentication required
- No server needed
- All filtering (English Wikipedia, main namespace, bot detection) done client-side

## Limitations

- Requires internet connection (live streams + API calls)
- Edits/Churn/Reverts data beyond 24 hours becomes unreliable (API pagination limits)
- Most Viewed minimum granularity is daily — no hourly top pages available
- Page moves are low volume (few per hour for English Wikipedia)
- Session data is not persisted — refreshing the page resets everything
