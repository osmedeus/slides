# Function Registry

## 80+ utility functions on Goja JavaScript runtime

<div class="grid grid-cols-2 gap-4">

<div class="text-sm">

| Category | Functions |
|----------|-----------|
| File & I/O | `read_file`, `glob`, `grep_regex` |
| Network | `run_nmap`, `http_get`, `ssh_exec` |
| Process | `tmux_run`, `tmux_capture`, `tmux_kill` |
| Code Analysis | `db_import_sarif`, `detect_language` |
| Events | `generate_event`, `db_import_port_assets` |
| Scripting | `exec_python`, `exec_ts`, `jq` |

</div>

<div>

```bash
# Evaluate functions from CLI
osmedeus func eval \
  'log_info("hello")'

# Bulk evaluation
osmedeus func eval \
  -e 'http_get("https://example.com")' \
  -T targets.txt -c 10

# Platform detection
osmedeus func eval \
  'log_info("OS: " + PlatformOS)'
```

</div>

</div>

<!--
The function registry is powered by Goja — a Go-native JavaScript VM. Functions are implemented in Go and exposed to JS with VM pooling for performance. Adding new functions is three steps: implement in Go, register in the runtime, add a constant.
-->

---

# Event-Driven Scheduling

## Reactive automation with cron, events, and file watchers

<div class="grid grid-cols-2 gap-4">

<div class="text-sm">

| Trigger | Description |
|---------|-------------|
| **cron** | Standard cron expressions |
| **event** | Topic-based with JS filters |
| **watch** | fsnotify file system changes |

**Event delivery chain:**

Server API → Redis Pub/Sub → DB Queue → Webhooks

</div>

<div>

```yaml
triggers:
  - name: on-new-asset
    on: event
    event:
      topic: assets.new
      filters:
        - "event.data_type == 'subdomain'"
      filter_functions:
        - "contains(event_data.url, '/api/')"
    input:
      target: event_data.url
      source: event.source
```

</div>

</div>

<!--
The scheduler manages three trigger types. Cron for time-based execution. Events for reactive pipelines — when a new subdomain is discovered, automatically trigger a deeper scan. File watchers use fsnotify for near-instant response.
-->

---
layout: center
---

# Schedule Management

## Web UI for managing recurring security workflows

- Visual dashboard for all scheduled jobs
- Daily Reconnaissance, Hourly Asset Monitoring, Weekly Vulnerability Scans
- Enable/disable schedules without editing YAML
- Run counts and last/next execution timestamps
- Event-based triggers (e.g., `asset.discovered`) alongside cron
- Full CRUD via REST API for automation

<!--
The Web UI gives you a central view of all scheduled workflows. You can see cron schedules, event triggers, run counts, and toggle them on and off without touching YAML.
-->
