# cantinasec/tripwire + inject

![GitHub Stars](https://img.shields.io/github/stars/aidan269/owasp-project?style=flat&color=FA5E06)

Two **Claude Code slash commands** that run inside Claude agents to audit AI agent pipelines against the OWASP Top 10 for Agentic Applications 2026. Drop them into `~/.claude/commands/cantinasec/` and invoke them directly from any Claude Code session — no separate tooling, no config, no API keys.

Works with Claude, CrewAI, n8n, and more. Point it at your project and Claude reads your pipeline files (`crew.py`, `agents.yaml`, `tasks.yaml`, `.json` workflows), reconstructs the topology, and returns findings with copy-pasteable hardened prompts you can drop straight back into your pipeline.

## What you get

Most agent security issues aren't in the code — they're in how agents trust each other. A topic variable interpolated raw into a goal string. A web search result passed unfiltered to the next agent. A planner that believes whatever an executor tells it.

Tripwire finds those gaps and hands you the fix. For each finding you get a hardened drop-in prompt, written for that specific node, ready to paste straight back into your `agents.yaml` or system prompt. No security background needed.

## Commands

| Command | What it does |
|---|---|
| `/cantinasec:tripwire` | Full audit across all 10 OWASP Agentic risk categories. Returns a drop-in hardened prompt for every finding. |
| `/cantinasec:inject` | Focused prompt injection scan (ASI01). Finds raw interpolation surfaces, unfiltered tool outputs, and cross-agent propagation chains. |

## Install

**1. Make sure Claude Code is installed:**
```bash
npm install -g @anthropic-ai/claude-code
```

**2. Add the commands:**
```bash
mkdir -p ~/.claude/commands/cantinasec

curl -sL https://raw.githubusercontent.com/aidan269/owasp-project/main/commands/tripwire.md \
  -o ~/.claude/commands/cantinasec/tripwire.md

curl -sL https://raw.githubusercontent.com/aidan269/owasp-project/main/commands/inject.md \
  -o ~/.claude/commands/cantinasec/inject.md
```

**3. Run from inside any agent project:**
```bash
cd your-agent-project
claude
```
Then type:
```
/cantinasec:tripwire
```
or pass a specific file:
```
/cantinasec:tripwire agents.yaml
/cantinasec:inject crew.py
```

## Repo structure

```
owasp-project/
├── commands/
│   ├── tripwire.md          ← /cantinasec:tripwire command shim
│   └── inject.md            ← /cantinasec:inject command shim
└── skills/
    ├── tripwire/
    │   └── SKILL.md         ← full OWASP Top 10 audit
    └── inject/
        └── SKILL.md         ← prompt injection scanner (ASI01)
```

---

If this caught something in your pipeline, give it a ⭐ so others can find it.
