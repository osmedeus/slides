# Execution Pipeline

## From CLI command to running scan

<div class="grid grid-cols-2 gap-4">

<div>

```
CLI / API
  → Executor (init context, inject vars)
  → Step Dispatcher (plugin registry)
  → Step Executors
      BashExecutor
      FunctionExec
      ForeachExec
      ParallelExec
      RemoteBashExec
      HTTPExecutor
      LLMExecutor
      AgentExecutor
      ACPExecutor
  → Runner
      HostRunner | DockerRunner | SSHRunner
```

</div>

<div>

```go
// StepExecutor interface
// — the plugin contract
type StepExecutor interface {
    CanHandle(
      stepType core.StepType,
    ) bool
    Execute(
      ctx context.Context,
      step *core.Step,
      execCtx *core.ExecutionContext,
      runner runner.Runner,
    ) (*core.StepResult, error)
}
```

</div>

</div>

<!--
The execution pipeline is the core of Osmedeus. The StepDispatcher uses a plugin registry — each executor registers what step types it handles. Adding a new step type means implementing one interface with two methods.
-->

---

# Runner System

## Where your commands actually execute

<div class="grid grid-cols-2 gap-4">

<div class="text-sm">

| Runner | Detail |
|--------|--------|
| **HostRunner** | Local via `os/exec` |
| **DockerRunner** | Ephemeral or persistent |
| **SSHRunner** | Remote via `crypto/ssh` |
| **remote-bash** | Per-step Docker/SSH |

</div>

<div>

```yaml
# Module-level runner
runner: docker
runner_config:
  image: "alpine:latest"
  persistent: true

# Per-step runner override
steps:
  - name: nmap-in-docker
    type: remote-bash
    step_runner: docker
    step_runner_config:
      image: instrumentisto/nmap
    command: nmap -sV {{target}}
```

</div>

</div>

<!--
The Runner interface is clean — Execute, Setup, Cleanup, Type, IsRemote. The interesting part is the remote-bash step type: you can run most of your workflow locally but spin up Docker containers or SSH sessions for specific steps.
-->

---

# Distributed Execution

## Scale across machines with Redis-based orchestration

<div class="grid grid-cols-2 gap-4">

<div>

- Master-worker pattern: `wosm-<uuid8>`
- Redis queue: DB poller + BRPOP
- Deduplication: seen-set prevents double exec
- File sync: rsync over SSH
- Configurable concurrency per worker

</div>

<div>

```bash
# Join as a worker
osmedeus worker join \
  --redis-url redis://host:6379

# Distributed scan
osmedeus run --distributed-run \
  -f general -t hackerone.com

# Queue tasks
osmedeus worker queue new \
  -f general -t example.com
osmedeus worker queue run --concurrency 5

# Check status
osmedeus worker status
```

</div>

</div>

<!--
Distributed execution uses a dual-source polling pattern. Workers poll both the database and Redis for queued tasks. The deduplication layer ensures a task is only executed once even if it appears in both sources.
-->

---

# Cloud Infrastructure

## Provision, scan, and tear down — one command

<div class="grid grid-cols-2 gap-4">

<div>

- AWS, GCP, DigitalOcean, Linode, Azure, Hetzner
- Infrastructure-as-code via Pulumi
- Cost estimates before provisioning
- Spot instances (60% discount on AWS)
- Auto-sync results & auto-destroy
- Parallel worker configuration

</div>

<div>

```bash
# Provision cloud machines
osmedeus cloud create --instances 3

# One-command cloud scan
osmedeus cloud run -f fast \
  -t hackerone.com \
  --sync-back --auto-destroy

# Setup existing machines
osmedeus cloud setup 1.2.3.4 5.6.7.8

# List and manage
osmedeus cloud list
```

</div>

</div>

<!--
Cloud infrastructure is handled via Pulumi — real infrastructure-as-code, not bash scripts calling API endpoints. You get cost estimates upfront. The --sync-back flag automatically rsyncs results back to your machine when done.
-->

---
layout: center
---

# Cloud Scan in Action

## Provision → Setup → Scan → Sync → Destroy

1. Pulumi provisions EC2 instances (~17s)
2. SSH setup installs Osmedeus on workers
3. Scan executes with distributed task submission
4. Results sync back via rsync
5. Infrastructure auto-destroyed on completion

<!--
Full lifecycle in one command. Pulumi creates EC2 instances in about 17 seconds, then SSH setup runs apt-get, installs dependencies, and configures Osmedeus on each worker. You pay only for the compute time you actually use.
-->

---

### CLI Distributed Run

<img src="/images/cli/cli-distributed-run.png" class="w-200 rounded-md center" />

---

### CLI Event Trigger

<img src="/images/cli/cli-event-test.png" class="w-200 rounded-md center" />
