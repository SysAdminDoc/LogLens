# LogLens Roadmap

Single-file browser log analyzer — drop a `.log`, get parsing, timeline, anomaly detection, error clustering, correlation analysis. v0.2.0. Roadmap targets live streaming, more formats, multi-file comparison, and deeper analytics.

## Planned Features

### Input & Formats
- **Live tail mode** — File System Access API polling or re-drop to append, watches a file for new lines
- **Multi-file drop** — load N files, merge timelines, filter by source file
- **File comparison** — diff two log files (e.g. before/after deploy) with timeline overlay
- **systemd journal** — parse `journalctl -o json` export
- **Docker / k8s** — auto-detect `docker logs --timestamps` and k8s container log formats
- **Windows Event .evtx (via wasm)** — parse native event logs, not just CSV export
- **Zipped log drop** — accept `.zip`/`.tar.gz`/`.log.gz` without manual extraction
- **Custom format builder** — visual regex builder for proprietary formats with live test input

### Analysis
- **Web Worker parsing** — move parse off main thread, handle 500MB files without UI freeze
- **Hot patterns panel** — top N normalized error templates with drift indicator (new today vs baseline)
- **Error genealogy** — cluster errors by stack-trace similarity (Jaccard on file+line pairs)
- **Request ID grouping** — extract correlation IDs (trace-id, request-id, X-Correlation-ID) and reconstruct request lifecycles
- **Latency extraction** — detect `took=12ms`, `duration=1.2s` patterns, compute p50/p95/p99 over time
- **HTTP status mix timeline** — per-minute chart of 2xx/3xx/4xx/5xx proportions
- **Seasonality detection** — FFT over time buckets to flag periodic spikes (hourly batch jobs, nightly cron)

### Visualization
- **Heatmap calendar** — GitHub-style year/month view of event density
- **Flame graph** — for profiling logs with nested timing
- **Sankey** — source → severity → component flow
- **Session playback** — scrub through time as if replaying logs

### Workflow
- **Saved queries** — named filter combos (severity + regex + time range) persisted in localStorage
- **Export to Markdown report** — selected events + analysis notes as a shareable report
- **Annotation layer** — drop notes on the timeline ("deploy", "incident start"), persist locally
- **Incident mode** — flip a switch, LogLens highlights relevant rows and builds a timeline auto-draft

### Format Depth
- **OpenTelemetry logs** — OTLP JSON ingest
- **CloudWatch / Stackdriver / Azure Monitor** — common cloud log export formats
- **ProxySQL / HAProxy / nginx error_log** (separate from access)
- **PostgreSQL / MySQL slow-query logs** with query normalization

## Competitive Research
- **Klogg / LogExpert / BareTail** — desktop log tailers. LogLens beats on zero-install + analytics; loses on true-live-tail (addressable with File System Access API).
- **Splunk / Loki / Elastic** — enterprise SIEM-grade. Not the competitor — LogLens is for the moment before you stand up a SIEM.
- **Papertrail / LogTail / Better Stack** — hosted log aggregation; require upload. LogLens privacy pitch holds.
- **ripgrep / lnav (terminal)** — terminal log tools; lnav is closest in spirit with SQL-over-logs. Steal lnav's auto-format detection approach.

## Nice-to-Haves
- LLM-assisted summarization via LlamaLink endpoint: "summarize this incident"
- Natural-language query: "errors in the last 5 minutes matching payment" → auto-build filter
- Shareable self-hosted snapshot (static HTML with data inlined)
- Kusto/KQL-like query language for power users, transpiled to JS filter
- Offline PWA install with share-target (mobile "share log file to LogLens")
- Integration with Bookmark Organizer Pro / PromptCompanion for cross-tool notebook exports

## Open-Source Research (Round 2)

### Related OSS Projects
- tstack/lnav — https://github.com/tstack/lnav — terminal log navigator; multi-file time-merge, SQL queries over logs, systemd integration
- variar/klogg — https://github.com/variar/klogg — Qt desktop log explorer, fork of glogg; reads directly from disk, SIMD-optimized, handles 10GB+ files
- nickbnf/glogg — https://github.com/nickbnf/glogg — the original smart log explorer; klogg's upstream
- mthenw/frontail — https://github.com/mthenw/frontail — Node-based browser log streamer (tail -f over websocket); minimal; closest analog for a web app
- logdy-dev/logdy — https://github.com/logdy-dev/logdy — single-binary web log viewer with jq/grep/awk-like pipeline in the UI
- Graylog2/graylog2-server — https://github.com/Graylog2/graylog2-server — full log management platform; heavyweight reference for long-term ingest
- Humio/grafana-loki — https://github.com/grafana/loki — log aggregation, LogQL query language; good for query-language inspiration
- rcoh/angle-grinder — https://github.com/rcoh/angle-grinder — command-line slice/dice with SumoLogic-like DSL; great pattern for ad-hoc aggregation
- vectordotdev/vector — https://github.com/vectordotdev/vector — log transform/route pipeline; VRL (Vector Remap Language) is a small DSL for log parsing
- drain3 / IBM/Drain3 — https://github.com/IBM/Drain3 — log template mining (auto-cluster similar lines); huge UX win for noisy logs

### Features to Borrow
- **Multi-file time-merge** (lnav) — tail multiple log files interleaved by timestamp; critical for correlating service logs
- **SQL-over-logs** (lnav) — `SELECT count(*) WHERE level='ERROR' GROUP BY service` over parsed logs; formal query model beats ad-hoc regex
- **LogQL-style pipeline** (Loki) — `{app="x"} |= "error" | json | duration > 5s` — composable filter + parse + filter chain
- **Template mining for deduplication** (Drain3) — auto-cluster "User 123 logged in" / "User 456 logged in" into one template with count; dramatic noise reduction
- **jq-in-URL live filter** (Logdy) — filter expression in URL so shareable link preserves query
- **Multi-threaded SIMD search** (klogg) — table-stakes for large files; if LogLens is web, push heavy search to WASM + worker pool
- **Histogram of events over time** (lnav) — small timeline chart at top, click-drag to zoom to range
- **Semantic highlighting** (lnav) — auto-color IPs, PIDs, UUIDs, timestamps without user config
- **Compressed log support** (.gz, .xz, journald) (lnav) — transparently; huge QoL
- **Saved views / session state** (klogg) — filters + highlights + scroll position per file, restored across restarts

### Patterns & Architectures Worth Studying
- lnav's **auto-format detection** — dozens of log format recognizers applied in priority order; fallback to generic. Each recognizer is a declarative config, not code
- klogg's **two-view model** — main log pane + separate search-results pane that doesn't lose context; user navigates between them
- Drain3's **online parse-tree clustering** — O(1) per line classification once tree is built; suits streaming ingest
- Vector's **VRL transformation** — small, safe, sandboxed DSL for parse/enrich step; lets users write parsers without full scripting
- For a web-based LogLens: **OPFS-backed streaming parser** + **WebWorker search**; never load whole file into main-thread memory, and never upload the file anywhere
