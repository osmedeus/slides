# Agentic LLM Steps

## Tool-calling agent loops — from YAML

<div class="grid grid-cols-2 gap-4">

<div>

- Full agentic loop: Query → LLM → Tool Calls → Execute → Repeat
- Optional planning stage generates a plan before execution
- 16 preset tools: bash, read_file, glob, grep, http_get, jq, exec_python, run_module...
- Custom tools with JS handler expressions
- Memory: sliding window, summarization on truncate, persistence to disk
- Structured output via JSON schema enforcement
- Stop conditions evaluated after each iteration

</div>

<div>

```yaml
steps:
  - name: security-analyst
    type: agent
    query: "Analyze {{Target}} and report."
    system_prompt: "You are a security analyst."
    max_iterations: 10
    agent_tools:
      - preset: bash
      - preset: read_file
      - preset: http_get
    models:
      - claude-sonnet-4-20250514
    stop_condition: >
      contains(agent_content, "DONE")
```

</div>

</div>

<!--
The agent step type is a full agentic LLM execution loop. You define the task, available tools, and constraints in YAML — and the engine handles the tool-calling loop. Memory management prevents context window overflow with sliding windows and automatic summarization.
-->

---

# Sub-Agent Orchestration

## Divide and conquer with specialized sub-agents

<div class="grid grid-cols-2 gap-4">

<div>

- Delegates via `spawn_agent` tool
- Sub-agents defined inline with own tools
- `max_agent_depth` controls nesting (default: 3)
- Child tokens merged into parent
- Each sub-agent gets own context

</div>

<div>

```yaml
steps:
  - name: orchestrator
    type: agent
    query: "Analyze {{Target}}"
    system_prompt: "Delegate tasks."
    agent_tools:
      - preset: bash
    sub_agents:
      - name: recon_agent
        description: "Recon specialist"
        max_iterations: 5
        agent_tools:
          - preset: bash
          - preset: http_get
      - name: vuln_scanner
        description: "Vuln scanner"
        agent_tools:
          - preset: bash
          - preset: read_file
```

</div>

</div>

<!--
Sub-agent orchestration is the multi-agent pattern. The parent orchestrator decides which specialist to invoke. Token accounting rolls up — you always know the total cost. Depth limiting prevents runaway recursion.
-->

---

# Agent-ACP

## External AI agents via Agent Communication Protocol

<div class="grid grid-cols-2 gap-4">

<div>

- Spawns real agent binaries as subprocesses — not just API calls
- Communicates via stdin/stdout ACP protocol
- Built-in agents: Claude Code, Codex, OpenCode, Gemini
- File access scoped to `allowed_paths` for sandboxing
- Auto-approval for tool permissions
- OpenAI-compatible API endpoint for integration

</div>

<div>

```yaml
steps:
  - name: code-review
    type: agent-acp
    agent: claude-code
    cwd: "{{Output}}"
    allowed_paths:
      - "{{Output}}"
    acp_config:
      write_enabled: true
    messages:
      - role: user
        content: "Analyze the scan results."
    exports:
      analysis: "{{acp_output}}"

# CLI usage
# osmedeus agent "analyze this codebase"
# osmedeus agent --agent codex "explain main.go"
```

</div>

</div>

<!--
Agent-ACP is different from the agent step type. Instead of using the internal LLM loop, it spawns external agent binaries — Claude Code, Codex, OpenCode, Gemini — and communicates via the Agent Communication Protocol.
-->

---

# Using Osmedeus with Coding Agent

<div class="grid grid-cols-2 gap-2">

<div>


- You can install the osmedeus expert skills at **github.com/osmedeus/osmedeus-skills**
- Then use this to write workflow or query data and run further testing if needed

</div>

<div>

```bash
$ bunx skills add https://github.com/osmedeus/osmedeus-skills --skill osmedeus-expert

> Write me an Osmedeus module that runs subfinder and amass in parallel, merges the results, then resolves live hosts with dnsx.

> Get me all of the url with 200 status code 
```


</div>

</div>

---

# Using Osmedeus with Claude

<img src="/images/osm-with-claude.png" class="w-full rounded-md center" />
