---
name: tripwire
description: Audit an AI agent pipeline against the OWASP Agentic Top 10 2026. Auto-detects pipeline files, reconstructs the topology, and returns drop-in hardened prompts for every finding.
---

# Tripwire — Agentic Pipeline Security Auditor

Tripwire audits AI agent pipelines against the OWASP Top 10 for Agentic Applications 2026. It reconstructs your pipeline topology, evaluates it across all 10 risk categories, and outputs copy-pasteable hardened prompts for every finding — no security expertise required.

## Supported Frameworks

- CrewAI (`crew.py`, `agents.yaml`, `tasks.yaml`)
- n8n (exported `.json` workflows)
- LangGraph, AutoGen, Flowise, and any pipeline describable in YAML, JSON, or Python

## Input Resolution

If `$ARGUMENTS` is provided:
- File path → read and analyze the file
- Python code (contains `Agent(`, `Task(`, `@agent`) → parse as CrewAI
- JSON → detect n8n (`nodes` + `connections`) or CrewAI (`agents` array)
- Plain text → treat as a pipeline description

If no arguments are provided:
- Search the current directory for pipeline files: `crew.py`, `agents.yaml`, `tasks.yaml`, `*.json` workflows, or any file containing `Agent(`, `@agent`, `crewai`, `n8n`
- If multiple files are found, read and combine them to reconstruct the full topology
- If nothing is found, ask the user to describe their pipeline or paste a file

## Audit Process

1. **Reconstruct topology** — identify each agent, tool, memory store, input source, output, and human gate
2. **Map connections** — who delegates to whom, what data flows where, what each node implicitly trusts from upstream
3. **Evaluate all 10 OWASP Agentic risks:**

| Code | Risk |
|------|------|
| ASI01 | Agent goal hijack (prompt injection) |
| ASI02 | Covert agent actions (unlogged tool calls) |
| ASI03 | Recursive self-improvement / runaway agents |
| ASI04 | Supply chain — poisoned tools, MCP servers, packages |
| ASI05 | Unsafe tool execution (unvalidated tool calls) |
| ASI06 | Memory poisoning (shared vector store attacks) |
| ASI07 | Privilege escalation between agents |
| ASI08 | Resource exhaustion / denial of service |
| ASI09 | Fake rationale / misleading explanations to humans |
| ASI10 | Rogue agents — self-replication, collusion |

## Output Format

```
# Cantina Tripwire — [pipeline name]
**Verdict:** PASS | REVIEW | BLOCK

## Findings

---
**[CODE] [Risk name]** · `[node name]` · CRITICAL | HIGH | MEDIUM

[One sentence: what the actual attack is in this specific pipeline.]

​```yaml
# [node name] — paste into agents.yaml / tasks.yaml / system prompt
[Complete drop-in replacement prompt. Specific to this node and this risk.]
​```
---

## Priority fixes

1. [Most urgent fix — one line]
2. [Second priority — one line]
3. [Third priority — one line]
```

Only CRITICAL, HIGH, and MEDIUM findings are reported. LOW and N/A are omitted. Each finding leads with a copy-pasteable hardened prompt so users can apply fixes directly without needing to interpret security advice.
