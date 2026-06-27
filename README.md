# LogLens

![Version](https://img.shields.io/badge/version-0.3.0-blue)
![License](https://img.shields.io/badge/license-MIT-green)
![Platform](https://img.shields.io/badge/platform-Any%20Browser-orange)
![JavaScript](https://img.shields.io/badge/JavaScript-Vanilla-F7DF1E?logo=javascript&logoColor=black)
![Dependencies](https://img.shields.io/badge/dependencies-zero-brightgreen)
![Status](https://img.shields.io/badge/status-active-success)

> Drag-and-drop log file analyzer with auto-format detection, multi-file timelines, live append/watch, compressed log import, anomaly detection, error clustering, and incident reporting - 100% client-side, zero install, single HTML file.

https://sysadmindoc.github.io/LogLens/

## Why LogLens?

Every sysadmin and developer deals with log files daily. Your options are: grep through them in a terminal, pay thousands for a SIEM, or upload sensitive logs to some random website. LogLens fills the gap — a professional-grade log analyzer that runs entirely in your browser with no data ever leaving your machine.

## Quick Start

1. Download `index.html`
2. Open in any browser
3. Drag one or more log files onto the drop zone

```bash
git clone https://github.com/SysAdminDoc/LogLens.git
cd LogLens
# Open index.html in your browser - that's it
```

No server. No install. No dependencies. No data uploaded anywhere. One HTML file.

## Features

| Feature | Description |
|---------|-------------|
| Auto-Format Detection | Identifies 18+ log formats automatically on file drop |
| Multi-File Drop | Loads multiple files at once, merges timelines, and filters by source file |
| Live Tail Mode | Uses the File System Access API where available, with same-file re-drop append fallback |
| File Comparison | Compares two source files by normalized message pattern and highlights added/removed/changed events |
| Compressed Logs | Imports `.gz`, `.tar.gz`, and `.zip` text log entries directly in the browser |
| Web Worker Parsing | Parses sources off the UI thread when browser workers are available |
| Virtual Scrolling | Renders only visible rows — handles 100k+ line files smoothly |
| Timeline Chart | Canvas-based frequency/severity chart across full timespan |
| Timeline Brush | Click-drag to select a time range, filters entire log viewer to that window |
| Anomaly Detection | Z-score based spike detection (2.5+ standard deviations) with red markers |
| Error Rate Spikes | Detects when error rate suddenly doubles+ between time windows |
| Severity Correlation | Pearson correlation between severity levels over time |
| Error Clustering | Groups similar errors by normalizing numbers/paths/IPs, sorted by frequency |
| Hot Patterns | Detects recently-emerging normalized templates versus the previous baseline |
| Error Genealogy | Groups related stack-trace failures by common frame signatures |
| Request Lifecycles | Extracts request/trace/correlation IDs and reconstructs timelines |
| Latency Analytics | Extracts duration/took/elapsed/latency fields and reports p50/p95/p99 |
| HTTP Status Mix | Shows 2xx/3xx/4xx/5xx proportions over time |
| Seasonality Detection | Finds repeated event-density periods using autocorrelation |
| Multiline Grouping | Detects and groups Java stack traces, Python tracebacks, indented continuations |
| Structured Extraction | Apache/Nginx logs parsed into IP, method, URL, status code, bytes |
| Custom Format Builder | Saves a named-group regex parser with live sample testing |
| Severity Filtering | Toggle FATAL/ERROR/WARN/INFO/DEBUG/OTHER with one click |
| Regex / Query Search | Full regex search plus simple field queries and natural-language shortcuts |
| Saved Queries | Persists named filter combinations in localStorage |
| Timeline Annotations | Adds local timeline notes for deploys, incident starts, and other markers |
| Incident Mode | Highlights high-signal errors, warnings, anomalies, 5xxs, and slow rows |
| Bookmarks | Star any line to pin it, export bookmarked lines as a file |
| Export | Export filtered results, bookmarks, Markdown reports, snapshots, and notebook JSON |
| Minimap | VS Code-style density minimap showing severity distribution for full file |
| Hourly Distribution | 24-hour heatmap showing when events cluster |
| Day-of-Week Chart | Bar chart showing event distribution across weekdays |
| Sparklines | Inline error rate sparklines in stats cards |
| Dark Theme | Deep dark industrial palette — no light mode |
| Zero Config | Single HTML file, no build step, no server, no dependencies |

## Supported Formats

| Format | Auto-Detection Method |
|--------|----------------------|
| JSON Lines | Valid JSON on each line, extracts `timestamp`/`level`/`message` fields |
| Apache Combined | Matches `IP - user [timestamp] "METHOD /path" status bytes` pattern |
| Nginx | Same as Apache with Nginx-specific field ordering |
| Syslog | Lines starting with `Mon DD HH:MM:SS` pattern |
| IIS W3C | Detects `#Fields:` header line |
| CSV/TSV | Consistent column count with comma or tab delimiter |
| Windows Event | Lines starting with `Information`/`Warning`/`Error`/`Critical` |
| Docker / Kubernetes CRI | Timestamped container stdout/stderr lines |
| systemd journal | `journalctl -o json` JSON export |
| OpenTelemetry Logs | OTLP-style JSON log records |
| Cloud Logs | Common CloudWatch, Stackdriver, and Azure Monitor JSON exports |
| Proxy / Database Logs | HAProxy, nginx error log, PostgreSQL, and MySQL slow-query text logs |
| ISO Timestamped | Lines containing `YYYY-MM-DDTHH:MM:SS` timestamps |
| Generic | Fallback — attempts timestamp and severity extraction from any format |

### Supported Timestamp Formats

| Format | Example |
|--------|---------|
| ISO 8601 | `2024-01-15T09:30:00.000Z`, `2024-01-15 09:30:00+05:00` |
| Common Log Format | `15/Jan/2024:09:30:00 -0700` |
| Syslog | `Jan 15 09:30:00` |
| US Date | `01/15/2024 09:30:00` |
| Slash Date | `2024/01/15 09:30:00` |
| Unix Epoch (seconds) | `1705312200` |
| Unix Epoch (ms) | `1705312200000` |
| Time Only | `09:30:00` (uses current date) |

### Severity Detection

| Level | Matched Keywords |
|-------|-----------------|
| FATAL | `FATAL`, `CRITICAL`, `CRIT`, `EMERG`, `EMERGENCY`, `PANIC` |
| ERROR | `ERROR`, `ERR`, `SEVERE`, `FAILURE`, `FAILED`, `ALERT` |
| WARN | `WARN`, `WARNING`, `CAUTION` |
| INFO | `INFO`, `INFORMATION`, `NOTICE`, `NOTE` |
| DEBUG | `DEBUG`, `TRACE`, `VERBOSE`, `FINE`, `FINER`, `FINEST` |

## Usage

### Basic Workflow

1. **Drop files** - drag any `.log`, `.txt`, `.csv`, `.json`, `.tsv`, `.out`, `.err`, `.gz`, `.tar.gz`, or `.zip` input onto the drop zone
2. **Review the overview** - right panel shows total lines, timespan, error rate, format, severity, and source breakdown
3. **Scan the timeline** - hover bars for event counts, look for red anomaly dots and orange rate-change triangles
4. **Filter by severity/source** - click severity and source buttons in the toolbar
5. **Search or query** - type plain text, regex, `level=error`, `status>=500`, `duration>250ms`, or natural phrases like `errors in last 15 minutes matching payment`
6. **Drill down** - click anomalies, hot patterns, request groups, comparison entries, or error clusters in the right panel

### Timeline Brush Selection

Click and drag across the timeline chart to select a time range. The entire log viewer filters to only show events within that window. The brush range displays in the toolbar. Clear with double-click or the "clear" link.

### Multi-File, Live Tail & Compression

Drop multiple files to merge them into one source-aware timeline. When exactly two files are loaded, LogLens adds a comparison panel that shows normalized patterns that were added, removed, or changed. Re-dropping a larger copy of the same file appends only the new tail content.

The **Live** button uses the File System Access API in Chromium browsers to watch a selected file for appended lines and reloads automatically if the file rotates. Compressed imports support `.gz`, `.tar.gz`, and `.zip` archives containing text-like log entries.

### Incident Workflow

Use **Incident** to focus the viewer on errors, warnings, anomaly rows, HTTP 5xxs, and p95+ latency rows. Add local timeline notes from the Workflow panel, save query/filter combinations, and export a Markdown report or static HTML snapshot from the header actions.

### Custom Formats

Use **Format** to save a custom JavaScript regex with named groups such as `ts`, `timestamp`, `level`, `severity`, `component`, `message`, and `request_id`. The builder tests the regex against a sample line before saving it in localStorage.

### Bookmarks & Export

Click the star icon on any log line to bookmark it. Bookmarked lines get a yellow highlight and right border. Use the header buttons to export:

- **Export** - downloads all currently filtered lines as a `.log` file
- **Bookmarks** - downloads only bookmarked lines as a `.log` file
- **Report** - downloads a Markdown incident report with filters, notes, and sample events
- **Snapshot** - downloads a static HTML snapshot with filtered event data

### Multiline Log Entries

LogLens automatically groups continuation lines with their parent entry:

- **Java stack traces** — `at com.example.Class.method()`, `Caused by:`, `... N more`
- **Python tracebacks** — `File "path"`, `raise`, `Traceback`
- **Indented lines** — any line starting with 2+ spaces/tabs without its own timestamp

Child lines inherit the parent's severity and timestamp, appear visually indented, and collapse line numbers for cleaner reading.

### Structured Fields

For Apache Combined and Nginx log formats, LogLens extracts structured fields and displays them as inline tags:

- **IP address** — client IP
- **HTTP method** — GET, POST, PUT, DELETE, etc.
- **Status code** — color-coded: green (2xx), blue (3xx), orange (4xx), red (5xx)
- **URL path** — displayed as the main message content
- **Bytes transferred** — response size

### Minimap

The right edge of the log viewer shows a VS Code-style minimap rendering severity density for the entire file. Red marks indicate errors/fatals, orange marks show warnings. Click anywhere on the minimap to jump to that position in the file. The viewport indicator tracks your current scroll position.

## How It Works

```
┌─────────────────┐     ┌─────────────────┐     ┌─────────────────┐     ┌─────────────────┐
│   File Reader    │────>│   Parse Engine   │────>│    Analysis     │────>│  Visualization   │
│                 │     │                 │     │                 │     │                 │
│ FileReader API  │     │ Format detect   │     │ Time buckets    │     │ Canvas timeline  │
│ Progress events │     │ Timestamp parse │     │ Z-score anomaly │     │ Virtual scroll   │
│ Text splitting  │     │ Severity parse  │     │ Rate-of-change  │     │ Minimap canvas   │
│                 │     │ Multiline group │     │ Pearson correl. │     │ Stats panels     │
│                 │     │ Field extract   │     │ Error clustering│     │ Severity bars    │
└─────────────────┘     └─────────────────┘     └─────────────────┘     └─────────────────┘
```

### Analysis Methods

**Anomaly Detection** — Divides the timespan into 200 buckets, computes mean and standard deviation of event counts, flags any bucket exceeding 2.5 standard deviations above the mean.

**Error Rate-of-Change** — Uses a sliding window (10 buckets) comparing error counts before vs. after each point. Flags positions where the error rate increases by 2.5x or more, or jumps from zero to sustained errors. Nearby detections are deduplicated.

**Severity Correlation** — Computes Pearson correlation coefficients between all severity level pairs across time buckets. Reports correlations above |r| > 0.5 as moderate, above |r| > 0.8 as strong.

**Error Clustering** — Normalizes error messages by replacing numbers with `N`, hex values with `0xN`, long hashes with `HASH`, file paths with `/PATH`, and IP addresses with `IP`. Groups by normalized pattern and sorts by frequency.

## What It Does and Doesn't Do

**Does:**
- Parse and visualize 18+ log formats with 8+ timestamp formats
- Auto-detect format, severity, and structure from any log file
- Provide anomaly detection, rate-of-change analysis, pattern drift, request grouping, latency analytics, and error clustering
- Group multiline entries (stack traces, tracebacks)
- Extract structured fields from Apache/Nginx logs
- Merge, compare, and source-filter multiple files
- Import `.gz`, `.tar.gz`, and `.zip` text logs
- Append same-file tail content and watch live files in supported browsers
- Handle 100k+ line files with virtual scrolling
- Run 100% client-side with zero data transmission
- Export filtered results, bookmarks, incident reports, snapshots, and notebook JSON

**Doesn't:**
- Upload any data anywhere — fully offline after page load
- Parse binary `.evtx` natively (`.evtx` must be pre-converted to text)
- Replace a full SIEM for enterprise monitoring
- Install a mobile share target without a companion service worker file

## Prerequisites

- Any modern browser (Chrome, Firefox, Edge, Safari)
- That's it

## FAQ / Troubleshooting

**Q: The file loads but the timeline is empty**
A: Your log file likely doesn't have parseable timestamps. Check the "Timestamped" count in the overview panel — if it shows 0%, the format isn't recognized. LogLens still works for search and severity filtering without timestamps.

**Q: Large files (50MB+) take a while to load**
A: The parser uses a Web Worker when the browser allows it, with a main-thread fallback. Files under 20MB load nearly instantly. Larger compressed or multi-file imports can still take a few seconds while the browser decodes them.

**Q: Java stack traces aren't grouped properly**
A: Multiline detection looks for lines starting with whitespace followed by `at `, `Caused by:`, or `... N more`. If your stack traces use a different format, they may not be grouped. The line must lack its own timestamp to be treated as a continuation.

**Q: Can I use this with live/streaming logs?**
A: Yes in Chromium browsers that expose the File System Access API. Use **Live** to watch a local file, or re-drop a larger copy of the same file to append only new lines.

**Q: How does the anomaly detection threshold work?**
A: It uses a z-score of 2.5, meaning a time bucket must have event counts 2.5 standard deviations above the mean to be flagged. For most log files this catches genuine spikes without false positives.

**Q: The structured field tags don't appear for my Apache logs**
A: LogLens uses strict regex matching for Apache Combined format: `IP - user [timestamp] "METHOD /path HTTP/x.x" status bytes`. If your logs use a custom Apache format, fields won't be extracted but the logs will still be parsed for timestamps and severity.

**Q: Can I search with regex?**
A: Yes. The search box accepts both plain text and JavaScript-compatible regex patterns. Invalid regex falls back to plain text search automatically.

## Contributing

Issues and PRs welcome. Areas that could use help:

- Native browser-safe `.evtx` parser integration
- Optional service-worker companion for mobile share targets
- More export adapters for downstream notebooks

## License

MIT License — see [LICENSE](LICENSE) for details.
