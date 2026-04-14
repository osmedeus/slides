# Installation & Quick Start

## Up and running in under a minute

<div class="grid grid-cols-2 gap-4">

<div>

- Single command install with checksum verification
- Supports Linux (amd64/arm64) and macOS
- Docker: `j3ssie/osmedeus:latest`
- Preset workflows out of the box

</div>

<div>

```bash
# Install
curl -sSL http://www.osmedeus.org/install.sh | bash

# Install preset workflows
osmedeus install base --preset
osmedeus install workflow --preset

# Run your first scan
osmedeus run -m recon -t example.com

# Run a flow (multi-module pipeline)
osmedeus run -f general -t example.com

# Dry-run mode
osmedeus run -f general -t example.com --dry-run
```

</div>

</div>

<!--
Getting started is fast. The install script detects your platform, downloads the binary with checksum verification, and adds it to PATH. The --dry-run flag is your best friend when testing workflows.
-->

---

# Web UI: Scans & Assets

## Unified dashboard for scan management and asset inventory

<div class="grid grid-cols-2 gap-8">

<div>

### Scans View
- Status tracking: Running, Completed, Failed with color badges
- Workflow and module info, step progress bars
- Trigger source (Manual, Cron, Event) visible per scan
- New Scan button for launching from the UI

</div>

<div>

### Assets Inventory
- Cross-workspace asset aggregation with filtering
- Status codes, technologies, host IPs at a glance
- CDN/WAF/Cloud detection flags
- Export and filter by source, type, technology

</div>

</div>

<!--
The Web UI gives you two critical views. The Scans page shows all active and completed scan runs with real-time progress. The Assets Inventory aggregates discovered assets across all workspaces.
-->

---

# Web UI: Vulnerabilities

## Severity-based vulnerability tracking with confidence scoring

- Statistics bar: Critical (4), High (3), Medium (3), Low (2), Info (0)
- Severity + Confidence scoring for prioritization
- Tags for vulnerability classification (auth-bypass, reflected, info-disclosure...)
- Filter by workspace, severity, confidence level
- SARIF import from Semgrep, Trivy, Kingfisher, Bearer
- Severity mapping: SARIF error → high, warning → medium, note → low

<!--
The vulnerability dashboard is where everything comes together. Results from nuclei, SAST tools via SARIF import, and custom scanners all flow into one unified view. The confidence scoring helps you separate confirmed findings from potential false positives.
-->


---

# Web UI #1

<div class="grid grid-cols-2 gap-2">

<div>
<img src="/images/web-ui/web-ui-assets.png" class="w-120 rounded-md center" />
</div>

<div>
<img src="/images/web-ui/web-ui-new-scan.png" class="w-120 rounded-md center" />
</div>

</div>

---

# Web UI #2

<div class="grid grid-cols-2 gap-2">

<div>
<img src="/images/workflow-editor/web-ui-workflow-1.png" class="w-120 rounded-md center" />
</div>

<div>
<img src="/images/workflow-editor/web-ui-workflow-2.png" class="w-120 rounded-md center" />
</div>

</div>