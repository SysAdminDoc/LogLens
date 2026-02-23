# LogLens

![Version](https://img.shields.io/badge/version-0.2.0-blue)
![License](https://img.shields.io/badge/license-MIT-green)
![Platform](https://img.shields.io/badge/platform-Any%20Browser-orange)
![JavaScript](https://img.shields.io/badge/JavaScript-Vanilla-F7DF1E?logo=javascript&logoColor=black)
![Dependencies](https://img.shields.io/badge/dependencies-zero-brightgreen)
![Status](https://img.shields.io/badge/status-active-success)

> Drag-and-drop log file analyzer with auto-format detection, timeline visualization, anomaly detection, error clustering, and correlation analysis — 100% client-side, zero install, single HTML file.

## Why LogLens?

Every sysadmin and developer deals with log files daily. Your options are: grep through them in a terminal, pay thousands for a SIEM, or upload sensitive logs to some random website. LogLens fills the gap — a professional-grade log analyzer that runs entirely in your browser with no data ever leaving your machine.

## Quick Start

1. Download `loglens.html`
2. Open in any browser
3. Drag a log file onto the drop zone

```bash
git clone https://github.com/SysAdminDoc/LogLens.git
cd LogLens
# Open loglens.html in your browser — that's it
```

No server. No install. No dependencies. No data uploaded anywhere. One HTML file.

## Features

| Feature | Description |
|---------|-------------|
| Auto-Format Detection | Identifies 9 log formats automatically on file drop |
| Virtual Scrolling | Renders only visible rows — handles 100k+ line files smoothly |
| Timeline Chart | Canvas-based frequency/severity chart across full timespan |
| Timeline Brush | Click-drag to select a time range, filters entire log viewer to that window |
| Anomaly Detection | Z-score based spike detection (2.5+ standard deviations) with red markers |
| Error Rate Spikes | Detects when error rate suddenly doubles+ between time windows |
| Severity Correlation | Pearson correlation between severity levels over time |
| Error Clustering | Groups similar errors by normalizing numbers/paths/IPs, sorted by frequency |
| Multiline Grouping | Detects and groups Java stack traces, Python tracebacks, indented continuations |
| Structured Extraction | Apache/Nginx logs parsed into IP, method, URL, status code, bytes |
| Severity Filtering | Toggle FATAL/ERROR/WARN/INFO/DEBUG/OTHER with one click |
| Regex Search | Full regex-powered search with highlighted matches |
| Bookmarks | Star any line to pin it, export bookmarked lines as a file |
| Export | Export filtered results or bookmarks as `.log` files |
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

1. **Drop a file** — drag any `.log`, `.txt`, `.csv`, `.json`, `.tsv`, `.out`, or `.err` file onto the drop zone
2. **Review the overview** — right panel shows total lines, timespan, error rate, format, severity breakdown
3. **Scan the timeline** — hover bars for event counts, look for red anomaly dots and orange rate-change triangles
4. **Filter by severity** — click severity buttons in the toolbar to toggle levels on/off
5. **Search** — type in the search box for plain text or regex patterns, matches are highlighted
6. **Drill down** — click anomalies or error clusters in the right panel to jump to those events

### Timeline Brush Selection

Click and drag across the timeline chart to select a time range. The entire log viewer filters to only show events within that window. The brush range displays in the toolbar. Clear with double-click or the "clear" link.

### Bookmarks & Export

Click the star icon on any log line to bookmark it. Bookmarked lines get a yellow highlight and right border. Use the header buttons to export:

- **Export** — downloads all currently filtered lines as a `.log` file
- **Bookmarks** — downloads only bookmarked lines as a `.log` file

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
- Parse and visualize 9+ log formats with 8+ timestamp formats
- Auto-detect format, severity, and structure from any log file
- Provide anomaly detection, rate-of-change analysis, and error clustering
- Group multiline entries (stack traces, tracebacks)
- Extract structured fields from Apache/Nginx logs
- Handle 100k+ line files with virtual scrolling
- Run 100% client-side with zero data transmission
- Export filtered results and bookmarks

**Doesn't:**
- Upload any data anywhere — fully offline after page load
- Support live log streaming / tail mode (planned)
- Parse binary log formats (`.evtx` must be pre-converted to text)
- Replace a full SIEM for enterprise monitoring
- Support multi-file comparison (planned)

## Prerequisites

- Any modern browser (Chrome, Firefox, Edge, Safari)
- That's it

## FAQ / Troubleshooting

**Q: The file loads but the timeline is empty**
A: Your log file likely doesn't have parseable timestamps. Check the "Timestamped" count in the overview panel — if it shows 0%, the format isn't recognized. LogLens still works for search and severity filtering without timestamps.

**Q: Large files (50MB+) take a while to load**
A: The parser runs on the main thread. Files under 20MB load nearly instantly. Files over 50MB may take a few seconds — the loading bar shows progress. Web Worker-based parsing is planned for a future version.

**Q: Java stack traces aren't grouped properly**
A: Multiline detection looks for lines starting with whitespace followed by `at `, `Caused by:`, or `... N more`. If your stack traces use a different format, they may not be grouped. The line must lack its own timestamp to be treated as a continuation.

**Q: Can I use this with live/streaming logs?**
A: Not yet. Currently you drop a static file. Tail mode (re-drop to append) is planned.

**Q: How does the anomaly detection threshold work?**
A: It uses a z-score of 2.5, meaning a time bucket must have event counts 2.5 standard deviations above the mean to be flagged. For most log files this catches genuine spikes without false positives.

**Q: The structured field tags don't appear for my Apache logs**
A: LogLens uses strict regex matching for Apache Combined format: `IP - user [timestamp] "METHOD /path HTTP/x.x" status bytes`. If your logs use a custom Apache format, fields won't be extracted but the logs will still be parsed for timestamps and severity.

**Q: Can I search with regex?**
A: Yes. The search box accepts both plain text and JavaScript-compatible regex patterns. Invalid regex falls back to plain text search automatically.

## Contributing

Issues and PRs welcome. Areas that could use help:

- Additional log format parsers (systemd journal, Docker, k8s)
- Web Worker for background parsing
- Multi-file overlay / comparison view
- Custom format definition builder
- Live tail mode

## License

MIT License — see [LICENSE](LICENSE) for details.
