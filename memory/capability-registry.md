# Agent Capability Registry

**Vytvořeno:** 2026-06-25
**Účel:** Centrální registr capability metadat pro capability-based routing sub-agentů.

---

## Researcher

| Atribut | Hodnota |
|---|---|
| **Name** | researcher |
| **Capabilities** | research, web-search, fact-checking, data-collection, source-verification |
| **Tools whitelist** | web_search, web_fetch, playwright__browser_navigate, playwright__browser_snapshot, playwright__browser_click, playwright__browser_type, playwright__browser_wait_for, playwright__browser_take_screenshot |
| **Primární model** | google/gemini-2.5-flash (free) |
| **Fallback model** | deepseek-v4-flash (DS Direct) |
| **Max steps** | 10 |
| **Timeout** | 180s |
| **Notes** | Nemá přístup k exec, read, write — nemůže spouštět kód ani modifikovat soubory. Pouze sběr dat z webu. |

## Coder

| Atribut | Hodnota |
|---|---|
| **Name** | coder |
| **Capabilities** | coding, programming, debugging, code-review, scripting, testing |
| **Tools whitelist** | exec, read, write, edit, apply_patch |
| **Primární model** | xiaomi-token-plan/mimo-v2.5 |
| **Fallback model** | deepseek-v4-flash (DS Direct) |
| **Max steps** | 15 |
| **Timeout** | 300s |
| **Notes** | Nemá přístup k web_search, web_fetch, playwright — nemůže prohlížet web. Pouze kód a filesystém. |

## Analyst

| Atribut | Hodnota |
|---|---|
| **Name** | analyst |
| **Capabilities** | analysis, reasoning, comparison, synthesis, evaluation, classification |
| **Tools whitelist** | sequential-thinking__sequentialthinking, memory_search, memory_get, memory__open_nodes |
| **Primární model** | google/gemini-2.5-flash (free) |
| **Fallback model** | deepseek-v4-flash (DS Direct) |
| **Max steps** | 8 |
| **Timeout** | 120s |
| **Notes** | Žádné externí nástroje. Pouze reasoning + přístup do paměti. Čistý analytik. |

## Legal

| Atribut | Hodnota |
|---|---|
| **Name** | legal |
| **Capabilities** | legal-analysis, regulation, compliance, document-drafting, statutory-interpretation |
| **Tools whitelist** | memory_search, memory_get, exec (pouze query_owui.py --pravnik) |
| **Primární model** | google/gemini-2.5-flash (free) |
| **Fallback model** | deepseek-v4-flash (DS Direct) |
| **Max steps** | 10 |
| **Timeout** | 180s |
| **Notes** | Přístup do OWUI KB Pravnik přes query_owui.py. Omezený exec jen pro jeden skript. |

## Planner

| Atribut | Hodnota |
|---|---|
| **Name** | planner |
| **Capabilities** | decomposition, strategy, orchestration, decision, planning |
| **Tools whitelist** | sequential-thinking__sequentialthinking **pouze** |
| **Primární model** | xiaomi-token-plan/mimo-v2.5 |
| **Fallback model** | deepseek-v4-pro (DS Direct) — složitější reasoning |
| **Max steps** | 20 |
| **Timeout** | 300s |
| **Notes** | **Čistý plánovač — žádné nástroje kromě sequential-thinking.** Nemůže sám nic dělat, jen rozhodovat a delegovat. Používá se v Iterative režimu jako orchestrátor. |

## Verifier

| Atribut | Hodnota |
|---|---|
| **Name** | verifier |
| **Capabilities** | validation, quality-check, error-detection, consistency, compliance-check |
| **Tools whitelist** | sequential-thinking__sequentialthinking, memory_search, memory_get |
| **Primární model** | deepseek-v4-flash (DS Direct) |
| **Fallback model** | google/gemini-2.5-flash (free) |
| **Max steps** | 5 |
| **Timeout** | 120s |
| **Notes** | Validuje výstupy ostatních agentů. Kontroluje konzistenci, fakta, úplnost. Primárně DeepSeek — Láďovi přijde přesnější než Gemini.

---

## Routing Matrix

| Task capability | Primární agent | Fallback agent | Primární model | Fallback model |
|---|---|---|---|---|
| research, web-search | researcher | (přímý web_search) | gemini-2.5-flash | deepseek-v4-flash |
| coding, programming | coder | (přímý exec) | mimo-v2.5 | deepseek-v4-flash |
| analysis, comparison | analyst | researcher (data) | gemini-2.5-flash | deepseek-v4-flash |
| legal, regulation | legal | analyst | gemini-2.5-flash | deepseek-v4-flash |
| planning, strategy | planner | analyst | mimo-v2.5 | deepseek-v4-pro |
| validation | verifier | analyst | gemini-2.5-flash | deepseek-v4-flash |
| general (neurčeno) | — | direct (Klarisa) | dle model routing | dle model routing |

---

## Pravidla

1. **Princip nejmenších privilegií:** Každý agent má jen tooly, které potřebuje ke své práci. Nic navíc.
2. **Žádný agent nemá přístup k sessions_\* toolům** — jen Klarisa (supervisor) orchestrátoruje.
3. **Žádný agent nemá přístup k cron, process, media toolům** — ty patří jen supervisorovi.
4. **Capability match má přednost před name matchem.**
5. Pokud capability není v registru → fallback na direct (Klarisa zpracuje sama).
6. Při spawnování vždy explicitně uvést `tools_allow` — nikdy nenechat defaultní set.
