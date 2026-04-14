<h1 class="!text-[#50fa7b]">Key Features</h1>

<div class="grid grid-cols-2 gap-3 text-xs">

<div>

<p class="text-sm font-bold !mb-0" style="color: #50fa7b">01 — Declarative YAML Workflows</p>
<p class="!mt-0">Pipelines with hooks, decision routing, module exclusion, and conditional branching across multiple runners</p>

<p class="text-sm font-bold !mb-0" style="color: #50fa7b">02 — Distributed Execution</p>
<p class="!mt-0">Redis-based master-worker pattern with queue system, webhook triggers, and file sync across workers</p>

<p class="text-sm font-bold !mb-0" style="color: #50fa7b">03 — 80+ Utility Functions</p>
<p class="!mt-0">Nmap, tmux, SSH, Python/TS scripting, SARIF parsing, CDN/WAF classification — all callable from YAML</p>

<p class="text-sm font-bold !mb-0" style="color: #50fa7b">04 — Event-Driven Scheduling</p>
<p class="!mt-0">Cron, file-watch, and event triggers with filtering, deduplication, and delayed task queues</p>

</div>

<div>

<p class="text-sm font-bold !mb-0" style="color: #50fa7b">05 — Agentic LLM Steps</p>
<p class="!mt-0">Tool-calling agent loops with sub-agent orchestration, memory management, and structured output</p>

<p class="text-sm font-bold !mb-0" style="color: #50fa7b">06 — Cloud Infrastructure</p>
<p class="!mt-0">Provision and run scans across DigitalOcean, AWS, GCP, Linode, and Azure with cost controls</p>

<p class="text-sm font-bold !mb-0" style="color: #50fa7b">07 — Rich CLI Interface</p>
<p class="!mt-0">Interactive queries, bulk function eval, workflow linting, progress bars, and comprehensive examples</p>

<p class="text-sm font-bold !mb-0" style="color: #50fa7b">08 — REST API & Web UI</p>
<p class="!mt-0">Full API server with webhook triggers, database queries, and embedded dashboard for visualization</p>

</div>

</div>

<!--
Here are the 8 pillars of Osmedeus. Each of these is a deep topic we'll touch on. The key takeaway: this isn't just a scanner — it's an engine that orchestrates your entire security pipeline.
-->

---

# High-Level Architecture

## From entry point to execution

```
┌─────────────────────────────────────────────────────────────────┐
│  ENTRY POINTS:  CLI  │  REST API  │  Scheduler  │  Dist Workers │
├─────────────────────────────────────────────────────────────────┤
│  PIPELINE:  CONFIG → PARSER → EXECUTOR → STEP DISPATCHER → RUN  │
├─────────────────────────────────────────────────────────────────┤
│  STEP EXECUTORS:  bash │ function │ parallel │ foreach │        │
│    remote-bash │ http │ llm │ agent │ agent-acp │ SARIF/SAST    │
├─────────────────────────────────────────────────────────────────┤
│  HOOKS:  pre_scan_steps → [main steps] → post_scan_steps        │
├─────────────────────────────────────────────────────────────────┤
│  RUNNERS:  HostRunner │ DockerRunner │ SSHRunner                │
├─────────────────────────────────────────────────────────────────┤
│  QUEUE:  DB + Redis polling → dedup → concurrent execution      │
└─────────────────────────────────────────────────────────────────┘
```

<!--
This is the 30,000-foot view. Requests come in through CLI, API, scheduler, or distributed workers. They all feed into the same pipeline: config loading, YAML parsing, execution, step dispatching, and finally running commands through the appropriate runner.
-->

---

# Layered Architecture

## Clean separation of concerns

```
┌─────────────────────────────────────────────────────┐
│  CLI / API              pkg/cli, pkg/server         │
├─────────────────────────────────────────────────────┤
│  Executor Layer         Executor → Dispatcher →     │
│                         Step Executors              │
├─────────────────────────────────────────────────────┤
│  Runner Layer           Host │ Docker │ SSH         │
├─────────────────────────────────────────────────────┤
│  Support Systems        Template Engine │ Function  │
│                         Registry (Goja JS) │        │
│                         Scheduler (cron/event/watch)│
├─────────────────────────────────────────────────────┤
│  Data Layer             YAML Parser │ Database      │
│                         (SQLite/PostgreSQL)         │
│                         │ Workspace Manager         │
└─────────────────────────────────────────────────────┘
```

<!--
The codebase follows a strict layered architecture. Each layer only depends on the layer below it. The executor layer is where the magic happens — it takes parsed workflows and dispatches each step to the appropriate handler.
-->

---

# Workflow Types

## Module vs Flow

<div class="grid grid-cols-2 gap-8">

<div>

### Module
Single execution unit with sequential steps

- Contains a list of Steps executed in order
- Each step has a type (bash, function, agent...)
- Supports pre/post hooks, exports, conditions
- Example: recon module, portscan module

</div>

<div>

### Flow
Orchestrates multiple modules with dependency management

- Contains a list of ModuleRefs with `depends_on`
- Manages execution order via dependency graph
- Supports module exclusion via `-x` / `-X` flags
- Example: general flow = recon + portscan + vuln

</div>

</div>

<!--
There are two workflow kinds. A Module is your atomic unit — sequential steps doing one thing well, like reconnaissance or port scanning. A Flow composes modules into a pipeline with dependency management.
-->

---

# CLI Usage

<div class="grid grid-cols-2 gap-2">

<div>
<img src="/images/installation/install-native.png" class="w-120 rounded-md center" />
</div>

<div>
<img src="/images/cli/cli-run-progress.png" class="w-120 rounded-md center" />
</div>

</div>


<div class="grid grid-cols-2 gap-2">

<div>
<img src="/images/noti/noti-funcs.png" class="w-120 rounded-md center" />
</div>

<div>
<img src="/images/cli/cli-asset-view.png" class="w-120 rounded-md center" />
</div>

</div>