# 🦊 Supervisor Orchestrator

**Multi-agent orchestration framework with Trinity pipeline (Thinker → Worker → Verifier) and Graph-of-Agents (GoA) extension.**

Inspired by Sakana Fugu papers (ICLR 2026) — lightweight coordinator architecture with evolutionary optimization principles.

## ✨ Features

| Mode | Description | Best for |
|------|-------------|----------|
| **Parallel** | Simultaneous sub-agent spawning | Web research, data collection |
| **Trinity** | Sequential Thinker → Worker → Verifier | Analysis, code, legal research |
| **GoA** | Worker↔Worker graph communication | Complex workflows with interdependent tasks |

### Core capabilities

- **Automatic mode selection** — scores task characteristics and picks the right pipeline
- **Cost-aware routing** — routes sub-tasks to cheapest capable model (DeepSeek Flash default, Gemini for verification)
- **Self-improvement loop** — post-run assessment and automated optimization
- **Metric tracking** — tracks quality, cost, and iteration data per run
- **Alternating Verifier** — switches to Gemini after 2+ iterations for independent validation

## 🏗 Architecture

```
Supervisor (Klarisa)
│
├── 🎯 Thinker — decomposes problem, assigns workers
├── 🛠 Worker Pool — executes subtasks (parallel or graph)
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

## 🚀 Quick Start

### Prerequisites
- OpenClaw gateway (v0.9+)
- MCP server with `sessions_spawn` support
- Python 3.10+ for support scripts

### Installation
1. Copy `skills/supervisor-orchestrator/` to your OpenClaw workspace
2. Add the skill reference in AGENTS.md or your gateway config
3. Ensure sequential-thinking and memory MCP tools are available

### Usage
The orchestrator activates automatically based on task complexity:
- Simple Q&A → direct response
- Multi-source research → Parallel mode
- Analysis/decision tasks → Trinity mode
- Complex multi-worker workflows → GoA mode

Or invoke explicitly:
```
"Use supervisor orchestrator for: [task description]"
```

## 📦 Included Skills

- `supervisor-orchestrator/SKILL.md` — Main skill definition
- Protocol-aligned templates for Thinker, Worker, Verifier roles
- Metric tracking and logging templates

## 💰 Cost Profile

| Model | Cost/response | Role |
|------|--------------|------|
| DeepSeek V4 Flash | ~$0.00028 | Default worker |
| DeepSeek V4 Pro | ~$0.00087 | Complex analysis |
| Gemini 3.5 Flash | Free (tier) | Alternating verifier |

Full routing: `AGENTS.md` → Program: Agentic Workflow (model routing table)

## 📊 Example Output

```
Režim: Trinity
Iterace: 2/3
Worker výstup: [structured analysis]
Verifier: ACCEPT (confidence: 92%)
Důvod: Všechny požadavky splněny, data ověřena ze 3 zdrojů
Čas: 12.4s
Cena: $0.00084
```

## 🧪 Development

Built and tested on:
- OpenClaw gateway (Windows, Node.js v24+)
- DeepSeek Direct API + Google Gemini API
- MCP: sequential-thinking, memory
- Playwright for browser automation

## 📄 License

MIT — free for personal and commercial use.

## 🤝 Contributing

PRs welcome! Focus areas:
- New worker types
- Additional verifier strategies
- Enhanced cost optimization
- Alternative LLM backends

---

**Created by:** Klarisa AI (2026)  
**Based on:** Sakana Fugu Papers (ICLR 2026): Trinity (2512.04695) + Conductor (2512.04388)
