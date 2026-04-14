---
layout: center
---

# The Problem

## Security automation is broken

- Dozens of disconnected tools — subfinder, nmap, nuclei, httpx, semgrep...
- Glued together with brittle bash scripts that nobody wants to maintain
- No orchestration — manual sequencing, no parallelism, no error handling
- No repeatability — every engineer runs things differently
- Scaling means SSH-ing into boxes and copy-pasting commands
- Results scattered across files with no unified view

<!--
We've all been there. You have 20+ security tools, each with different input/output formats. You write bash scripts to chain them. Those scripts break. Nobody documents them. Scaling means more terminals and more pain.
-->

---

# What is Osmedeus?

## Declarative orchestration for security workflows

- A security-focused workflow engine that turns complex automation into auditable YAML definitions
- Encrypted data handling, secure credential management, sandboxed execution
- Composable — modules snap together into flows with dependency management
- Built for both beginners and experts
- Written in Go — single binary, fast startup, low memory

<!--
Osmedeus is essentially a workflow orchestration engine, purpose-built for security. Think of it as Airflow or Temporal, but designed specifically for security scanning pipelines. Everything is defined in YAML — declarative, version-controllable, auditable.
-->
