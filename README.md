# LLM Evaluation Program

The evaluation methodology and tooling I designed and run for **[Prox](https://www.proxdesign.co)** is a production RAG-based AI discovery platform (Voyage AI embeddings, pgvector, Claude, 300K+ products). This is the system that decides whether a change ships - whether it is an AI change (prompt, route, model) or architecture change (data table, ranking, search fallback).

> **What's here.** These are real artifacts extracted from the production Prox repository. Prox is built via AI-assisted development (Claude writes the code); I own the evaluation design, the calibration decisions, and the ship/hold authority documented here. Internal cross-references (Linear tickets, file paths) are authentic.

## Why this exists
Non-deterministic experiences need it. This is how AI is managed and how it is able to meet user and business needs. 

- **A hand-calibrated 4-dimension rubric** (Relevance / Specificity / Factuality / Actionability) with anchored 1–4 scales.
- **A reproducibility-locked LLM-as-judge.** I selected the judge model by measuring self-consistency. *Key finding* through the eval program a newer, more capable model disagreed with itself on 17% of identical re-runs, so the judge is pinned to an older model at temp 0 with a locked rubric version, and a drift check guards the lock.
- **A 22-tag failure taxonomy** built bottom-up from labeled production traces. The v1 pass localized 71% of quality failures to one layer (context extraction), which is what made the fix tractable.
- **Pre-committed ship/hold gates.** The decision rule is written down before the experiment runs. One gate returned HOLD on a prompt redesign I'd built to support a better customer experience; I retired it. The same run surfaced a retrieval bug in the shipped version — fixed, re-gated clean, off-target recommendations went from 4/7 to 0/7.
- **Dataset lifecycle discipline.** Iteration / test / holdout sets are disjoint by construction (checked on both the id axis and normalized query text), so the optimizer can never tune against a case the gate later scores.
- **Gold-rot monitoring.** Ranking gold labels decay as the live catalog churns; rot is measured weekly against the catalog (WARN >5%, FAIL >15%) and rotted rows are re-certified, never silently dropped — a silent drop shrinks the denominator and flatters recall.

## What's in here

### `methodology/` — the framework

The **EFF (Evaluation Foundation Framework)** plus the 11 reusable templates. EFF has the why (pairwise vs. scaled signals, judge calibration approach, dataset sizing rationale, solo-scale defaults); the templates are what I use to fill in per eval workstream - if there is enough time:

| # | Template | Purpose |
|---|---|---|
| 01 | Evaluation brief | Per-workstream eval scoping |
| 02 | Value definition | What value is measured; the riskiest assumption |
| 03 | Failure taxonomy | Named, prioritizable failure categories from observed traces |
| 04 | Distribution-aware quality | Overall + tail + per-segment thresholds |
| 05 | Semantic rubric | Anchored 1–4 scaled rubric for absolute bars + diagnosis |
| 06 | Pairwise rubric | A/B comparison protocol; win-rate gate signal |
| 07 | Dataset lifecycle | Versioning, disjointness, and leakage policy |
| 08 | Experiment one-pager | Per-experiment design summary |
| 09 | Decision memo | The ship / hold / scope-restrict artifact |
| 10 | Monitoring plan | Post-launch monitoring |
| 11 | Surface map | Forward-looking inventory of where failures *could* occur |


---

*Dave Pollack · [LinkedIn](https://www.linkedin.com/in/dave-pollack) · [github.com/D-Pollack](https://github.com/D-Pollack)*
