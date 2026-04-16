---
name: inject
description: Scan an AI agent pipeline for prompt injection vulnerabilities — goal hijack via tool outputs, untrusted input interpolation, and cross-agent instruction propagation.
---

# Inject — Prompt Injection Scanner

Focused audit for ASI01 (Agent Goal Hijack). Finds every surface in your pipeline where untrusted content can reach an agent's reasoning context and redirect its behaviour.

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
- Combine multiple files to reconstruct the full topology
- If nothing is found, ask the user to describe their pipeline

## What to Look For

For each agent and connection, identify:

1. **Raw interpolation** — variables like `{topic}`, `{input}`, `{query}` inserted directly into goal strings or task descriptions without sanitization
2. **Tool output trust** — web fetch, search, file read, or API results passed unfiltered into the next agent's context
3. **Cross-agent propagation** — one agent's output used as another's instruction input, creating a chain where a single injected payload reaches all downstream agents
4. **Missing delimiters** — tool outputs mixed into the reasoning context without structural separation (e.g. no `<external_source>` wrapper or equivalent)
5. **No instruction-pattern filtering** — no checks for imperative verbs, quoted commands, or self-referential meta-instructions in untrusted content

## Output Format

```
# Cantina Inject — [pipeline name]

## Injection surfaces

For each vulnerable surface:

---
**`[node name]`** · [interpolation / tool output / cross-agent] · CRITICAL | HIGH | MEDIUM

[One sentence: exactly how a payload reaches this node's reasoning context.]

​```yaml
# [node name] — paste into agents.yaml / tasks.yaml / system prompt
[Complete drop-in replacement. Adds input sanitization, structural delimiters,
and instruction-pattern filtering specific to this node's role.]
​```
---

## Attack chain

[If multiple nodes are affected, describe the full propagation path in 2-3 sentences —
which node is the entry point, how the payload moves, and what the blast radius is.]

## Priority fixes

1. [Most urgent — one line]
2. [Second — one line]
3. [Third — one line]
```

Only surfaces that are actually reachable by untrusted input are reported. Be specific to this pipeline's topology — not generic injection advice.
