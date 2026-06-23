---
name: "supervisor-orchestrator"
description: "Klarisa jako orchestrátor sub-agentů s Trinity pipeline, Graph-of-Agents rozšířením, cost-aware routingem a self-improvement loopem"
---

# Supervisor Orchestrator — Trinity + Graph-of-Agents

> Rozšiřuje agentic-workflow o specializované agenty, Trinity pipeline (Thinker→Worker→Verifier) podle Sakana Fugu principů (ICLR 2026) a Graph-of-Agents (GoA) rozšíření pro Worker↔Worker komunikaci.
> > **Výchozí model pro všechny agenty je DeepSeek V4 Flash.**
> >
> > ## Režimy orchestrace
> >
> > | Režim | Kdy použít | Princip |
> > |---|---|---|
> > | **Paralelní** (výchozí) | Data z vícero nezávislých zdrojů | Současný spawn agentů |
> > | **Trinity** | Strategie + exekuce + validace | Sekvenční Thinker → Worker → Verifier |
> > | **GoA (Graph-of-Agents)** | Více workerů si potřebuje vyměňovat informace | Thinker → Worker1↔Worker2↔Worker3 → Verifier |
> >
> > ### Automatická volba režimu
> >
> > ```python
> > function choose_mode(ukol):
> >   trinity_score = 0
> >   if ukol.vyzaduje_dekompozici: trinity_score += 1
> >   if ukol.obsahuje_slova(analyzuj, navrhni, rozhodni): trinity_score += 1
> >   if ukol.vyzaduje_validaci: trinity_score += 1
> >
> >   goa_score = 0
> >   if ukol.vyzaduje_vice_workeru: goa_score += 1
> >   if ukol.worker_musi_komunikovat: goa_score += 1
> >   if ukol.je_synteza_z_vice_oblasti: goa_score += 1
> >
> >   parallel_score = 0
> >   if ukol.vyzaduje_web_data: parallel_score += 1
> >   if ukol.vyzaduje_paralelni_prace: parallel_score += 1
> >
> >   if goa_score >= 2: return "goa"
> >   elif trinity_score >= 2 and parallel_score == 0: return "trinity"
> >   elif parallel_score >= 2: return "parallel"
> >   elif trinity_score >= 2 and parallel_score >= 1: return "hybrid"
> >   else: return "direct"
> > ```
> >
> > ## Architektura — Graph-of-Agents režim
> >
> > ```
> > TY (Klarisa - Supervisor)
> > │
> > ├── 🎯 Thinker
> > │   └── Rozloží problém na podúlohy + určí, kteří workeři jsou potřeba
> > │
> > ├── 🛠 Worker Pool (grafová struktura)
> > │   │
> > │   ├── Worker A ────→ Worker B
> > │   │       ↑              │
> > │   │       └──────────────┘
> > │   │
> > │   └── Worker C ────→ Worker A
> > │
> > └── ✅ Verifier
> >     └── Validuje agregovaný výstup všech workerů
> > ```
> >
> > ### GoA pipeline (Graph-of-Agents)
> >
> > ```python
> > def goa_pipeline(query):
> >     strategy = thinker_phase(query, mode="goa")
> >     workers, edges = parse_goa_graph(strategy)
> >     messages = {}
> >     for worker in topological_sort(workers, edges):
> >         input_data = merge(query, messages[worker.inputs])
> >         output = call_worker(worker, input_data)
> >         messages[worker.name] = output
> >         for successor in edges[worker.name]:
> >             messages.setdefault(successor, "")
> >             messages[successor] += f"\nVýstup od {worker.name}:\n{output}\n"
> >     final = aggregate_worker_outputs(messages)
> >     result, verification = verifier_phase(query, strategy, final)
> >     log_metrics(...)
> >     return result, verification
> > ```
> >
> > Thinker (Stejný jako v Trinity, ale s GoA extension)
> > Worker (Stejný jako v Trinity)
> > Verifier (Stejný jako v Trinity)
> >
> > Veškeré ostatní části (model routing, fallback chain, metric tracking, cost management,
> > templaty, chybové scénáře) jsou identické s Trinity režimem.
