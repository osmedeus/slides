# Step Types

## 10 built-in step executors via plugin registry

<div class="grid grid-cols-2 gap-3 text-sm">

| # | Type | Description |
|---|------|-------------|
| 01 | `bash` | Execute shell commands locally |
| 02 | `function` | Call 80+ utility functions via Goja JS runtime |
| 03 | `foreach` | Iterate over file lines with configurable parallelism |
| 04 | `parallel-steps` | Run multiple steps concurrently |
| 05 | `remote-bash` | Per-step Docker or SSH execution |

| # | Type | Description |
|---|------|-------------|
| 06 | `http` | Make HTTP requests with method/headers/body |
| 07 | `llm` | Single-shot LLM completion with structured output |
| 08 | `agent` | Agentic LLM loop with tool calling and memory |
| 09 | `agent-acp` | External AI agents via Agent Communication Protocol |
| 10 | `SARIF/SAST` | Import and parse static analysis results |

</div>

<!--
Every step type is a plugin implementing the StepExecutor interface — CanHandle() and Execute(). The dispatcher matches step type to executor at runtime. Adding a new step type means implementing one interface with two methods.
-->

---

# Declarative YAML Workflows

## Everything defined in version-controllable YAML

<div class="grid grid-cols-2 gap-4">

<div>

- Hooks: `pre_scan_steps` and `post_scan_steps` bracket your main pipeline
- Exports: step outputs propagate to subsequent steps as variables
- Conditional execution via `pre_condition` expressions
- On-success / on-error actions per step

</div>

<div>

```yaml
kind: module
name: recon-example
...
hooks:
  pre_scan_steps:
    - name: setup
      type: bash
      command: mkdir -p {{Output}}/results

steps:
  - name: subdomain-enum
    type: bash
    command: >
      subfinder -d {{Target}}
      -o {{Output}}/subs.txt
    exports:
      sub_count: "output"
    on_error:
      - type: function
        function: log_error("subfinder failed")
...
```

</div>

</div>

<!--
Here's what a module workflow looks like. Pre-scan hooks set up the workspace. Each step has a type, command, and optional exports. The pre_condition field lets you skip steps based on previous results.
-->

---

# Decision Routing

## Conditional branching with switch/case/goto

<div class="grid grid-cols-2 gap-4">

<div>

- `switch` evaluates a template variable or expression
- `cases` map values to targets (step names)
- `default` provides a fallback path

</div>

<div>

```yaml
steps:
  # Step 1: Test inline function for visible output
  - name: detect-mode
    type: bash
    command: echo "Detecting scan mode"
    exports:
      mode: "{{scan_mode}}"
    decision:
      switch: "{{mode}}"
      cases:
        "quick":
          function: "log_info('[INLINE] Quick scan selected')"
        "deep":
          function: "log_info('[INLINE] Deep scan selected')"
      default:
        function: "log_info('[INLINE] Default mode selected')"

```

</div>

</div>

<!--
Decision routing is how you build intelligent branching into workflows. Osmedeus auto-detects target type — domain, IP, CIDR — and routes to the appropriate scanning strategy.
-->

---

# Template Engine & Variables

## Dynamic interpolation at every level

<div class="grid grid-cols-2 gap-4">

<div class="text-sm">

| Category | Examples |
|----------|----------|
| Path | `{{BaseFolder}}`, `{{Binaries}}`, `{{Data}}` |
| Target | `{{Target}}`, `{{TargetFile}}`, `{{TargetType}}` |
| Output | `{{Output}}`, `{{Workspace}}`, `{{RunUUID}}` |
| Heuristic | `{{TargetRootDomain}}`, `{{TargetTLD}}`, `{{Org}}` |
| Platform | `{{PlatformOS}}`, `{{PlatformArch}}` |

</div>

<div>

```yaml
# Template syntax: {{variable}}
command: subfinder -d {{Target}} -o {{Output}}/subs.txt

# Foreach uses double-bracket: [[variable]]
- name: process-items
  type: foreach
  input: "{{Output}}/subs.txt"
  variable: subdomain
  threads: 10
  step:
    command: httpx -u [[subdomain]] -json

# Exports create new variables
exports:
  alive_count: "output"
```

</div>

</div>

<!--
The template engine uses double-curly-brace syntax for variable interpolation. It's available everywhere — commands, conditions, exports, file paths. Foreach steps use double-bracket [[var]] to avoid conflicts.
-->

---

# Workflow Linter

## Static analysis for YAML workflows — catch issues before execution

<div class="grid grid-cols-2 gap-4">

<div class="text-sm">

| Rule | Severity |
|------|----------|
| `missing-required-field` | warning |
| `duplicate-step-name` | warning |
| `empty-step` | warning |
| `invalid-goto` | warning |
| `circular-dependency` | warning |
| `unused-variable` | info |

</div>

<div>

```bash
# Lint a workflow
osmedeus workflow lint my-workflow.yaml

# Output formats
osmedeus workflow lint my-workflow.yaml --format json
osmedeus workflow lint my-workflow.yaml --format github

# CI mode — exit code 1 on errors
osmedeus workflow lint my-workflow.yaml --check
```

</div>

</div>

<!--
The linter catches common issues before you run anything. The GitHub output format integrates with CI annotations. It recognizes all 30+ built-in variables to avoid false positives.
-->

---

### Cloud Provisioning

<img src="/images/cloud/osm-cloud-provision.png" class="w-200 rounded-md center" />

---

### Cloud Run

<img src="/images/cloud/osm-cloud-auto-sync.png" class="w-200 rounded-md center" />
