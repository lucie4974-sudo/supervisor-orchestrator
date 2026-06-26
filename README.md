# 🦊 Supervisor Orchestrator

**Multi-agent orchestration framework with Trinity pipeline (Thinker → Worker → Verifier) and Graph-of-Agents (GoA) extension.**

[![GitHub Sponsors](https://img.shields.io/github/sponsors/lucie4974-sudo?label=Sponsor&logo=github&style=for-the-badge)](https://github.com/sponsors/lucie4974-sudo)
[![Buy Me A Coffee](https://img.shields.io/badge/Buy%20Me%20A%20Coffee-ffdd00?style=for-the-badge&logo=buy-me-a-coffee&logoColor=black)](https://buymeacoffee.com/lucie4974)
[![License: MIT](https://img.shields.io/badge/License-MIT-green.svg?style=for-the-badge)](LICENSE)
[![OpenClaw](https://img.shields.io/badge/Built%20for-OpenClaw-purple?style=for-the-badge)](https://openclaw.ai)

> Inspired by Sakana Fugu papers (ICLR 2026) — lightweight coordinator architecture with evolutionary optimization principles.
> **First open-source multi-agent orchestrator with built-in memory (Honcho), cost-aware routing, and capability registry.**

---

## ✨ Features

| Mode | Description | Best for |
|------|-------------|----------|
| **Parallel** | Simultaneous sub-agent spawning | Web research, data collection |
| **Trinity** | Sequential Thinker → Worker → Verifier | Analysis, code, legal research |
| **GoA** | Worker↔Worker graph communication | Complex workflows with interdependent tasks |
| **Iterative** | Think→Execute→Repeat→Finish loop | Deep research with uncertain scope |

### Core capabilities

- **Automatic mode selection** — scores task characteristics and picks the right pipeline
- **Cost-aware routing** — routes sub-tasks to cheapest capable model ($0.00028-$0.00087/response)
- **Capability registry** — 6 specialized agents (Researcher, Coder, Analyst, Legal, Planner, Verifier) with explicit tool whitelists
- **Honcho memory integration** — cross-session memory, peer cards, context enrichment before every run
- **Self-improvement loop** — post-run assessment and automated optimization
- **Alternating Verifier** — switches to Gemini after 2+ iterations for independent validation

## 🏗 Architecture

```
Supervisor (Orchestrator)
│
├── 🧠 Honcho Context Enrichment (memory recall + peer cards)
│
├── 🎯 Thinker — decomposes problem, assigns workers
├── 🛠 Worker Pool — executes subtasks (parallel or graph)
│   ├── Researcher (web_search, web_fetch, playwright)
│   ├── Coder (exec, read, write, edit)
│   ├── Analyst (sequential-thinking, memory)
│   ├── Legal (OWUI KB Pravnik + query_owui.py)
│   └── Planner (sequential-thinking ONLY — pure decision maker)
└── ✅ Verifier — validates aggregated output with confidence score
```

### Trinity pipeline
```
Thinker → Worker(s) → Verifier → [ACCEPT | REVISE → repeat | FINAL]
```

### Graph-of-Agents (GoA)
```
Thinker ─→ Worker A ←──→ Worker B
                ↓              ↓
            Worker C ─────→ Verifier
```
Workers exchange intermediate results via message propagation through directed edges.

### Iterative (Deep Research)
```
Planner ←→ Execute (parallel agents) → Repeat → Finish → Verifier
```
Adaptive loop for research with uncertain scope. Planner decides when to stop.

## 🚀 Quick Start

### Prerequisites
- OpenClaw gateway (v0.9+)
- MCP server with `sessions_spawn` support
- Python 3.10+ for support scripts

### Installation
```bash
# Clone
git clone https://github.com/lucie4974-sudo/supervisor-orchestrator.git
cd supervisor-orchestrator

# Copy to your OpenClaw workspace
cp -r skills/supervisor-orchestrator/ ~/.openclaw/workspace/skills/

# Copy capability registry
cp memory/capability-registry.md ~/.openclaw/workspace/memory/
```

### Usage
The orchestrator activates automatically based on task complexity:
- Simple Q&A → direct response
- Multi-source research → Parallel mode
- Analysis/decision tasks → Trinity mode
- Complex multi-worker workflows → GoA mode
- Deep research with uncertain scope → Iterative mode

Or invoke explicitly:
```
"Use supervisor orchestrator for: [task description]"
```

## 📊 Cost Profile

| Model | Cost/response | Role |
|------|--------------|------|
| DeepSeek V4 Flash | ~$0.00028 | Default worker, fallback |
| DeepSeek V4 Pro | ~$0.00087 | Complex analysis |
| Gemini 3.5 Flash | Free (tier) | Alternating verifier |
| MiMo V2.5 | $17/mo flat | Coder, Planner |

**Real-world example:** 3-agent Trinity run = ~$0.00084 total cost.

## 🧪 Example Output

```
Režim: Trinity
Iterace: 2/3
Worker výstup: [structured analysis]
Verifier: ACCEPT (confidence: 92%)
Důvod: Všechny požadavky splněny, data ověřena ze 3 zdrojů
Čas: 12.4s
Cena: $0.00084
```

## 📦 What's Included

- `skills/supervisor-orchestrator/SKILL.md` — Main skill definition (446 lines)
- `memory/capability-registry.md` — Agent capability definitions with tool whitelists
- Protocol-aligned templates for Thinker, Worker, Verifier roles
- Metric tracking and logging templates
- Honcho memory integration pipeline

## 🔧 Built With

- **OpenClaw** — Agent framework
- **MCP** — sequential-thinking, memory tools
- **DeepSeek Direct API** — Cost-effective LLM routing
- **Google Gemini API** — Free-tier verification
- **Honcho** — Self-hosted memory system
- **Playwright** — Browser automation

## 🤝 Contributing

PRs welcome! Focus areas:
- New worker types (Medical, Financial, etc.)
- Additional verifier strategies
- Enhanced cost optimization
- Alternative LLM backends
- Integration with more MCP tools

## 📄 License

MIT — free for personal and commercial use.

---

**Created by:** [Klarisa AI](https://github.com/lucie4974-sudo) (2026)  
**Based on:** Sakana Fugu Papers (ICLR 2026): Trinity (2512.04695) + Conductor (2512.04388)

If this project helps you, consider [sponsoring](https://github.com/sponsors/lucie4974-sudo) or [buying me a coffee](https://buymeacoffee.com/lucie4974)! ☕
