# cantinasec/tripwire + inject

Proposed addition to [cantina.security/resources/security-plugins](https://www.cantina.security/resources/security-plugins).

Two **Claude Code slash commands** that run inside Claude agents to audit AI agent pipelines against the OWASP Top 10 for Agentic Applications 2026. Drop them into `~/.claude/commands/cantinasec/` and invoke them directly from any Claude Code session — no separate tooling, no config, no API keys.

Claude reads your pipeline files (`crew.py`, `agents.yaml`, `tasks.yaml`, `.json` workflows), reconstructs the topology, and returns findings with copy-pasteable hardened prompts you can drop straight back into your pipeline.

## Commands

| Command | What it does |
|---|---|
| `/cantinasec:tripwire` | Full audit across all 10 OWASP Agentic risk categories. Returns a drop-in hardened prompt for every finding. |
| `/cantinasec:inject` | Focused prompt injection scan (ASI01). Finds raw interpolation surfaces, unfiltered tool outputs, and cross-agent propagation chains. |

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
