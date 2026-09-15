# VISION Starter Kit Integration Guide

**Reference Repository:** [`rsimhan/agentic-slice-kit`](https://github.com/rsimhan/agentic-slice-kit)

This document specifies how **VISION** builds upon the official agentic starter kit spine, mapping generic agentic mechanisms to learning-specific abstractions.

---

## 1. Architectural Separation: Runtime Spine vs. Domain Layer

```text
┌────────────────────────────────────────────────────────────────────────┐
│                   STARTER KIT REUSABLE RUNTIME SPINE                   │
│                                                                        │
│  - Durable State Machine Runner     - Hard Cost & Spend Budget Counter │
│  - SQLite Persistent Storage        - Revision Loop Limit Guard        │
│  - Human-in-the-Loop Callback Host  - Schema Verification Gate         │
└───────────────────────────────────┬────────────────────────────────────┘
                                    │ Hooks & Event Interfaces
                                    ▼
┌────────────────────────────────────────────────────────────────────────┐
│                        VISION EDTECH DOMAIN LAYER                      │
│                                                                        │
│  - Data Structures Prerequisite Graph (`domain/prerequisite_graph.json`)│
│  - Adaptive Student State Manager (`slice/state_store.py`)             │
│  - Concept Gap Diagnostic Evaluator (`slice/evaluator.py`)             │
│  - Local Corpus Citation & Provenance Retriever (`slice/corpus.py`)    │
└────────────────────────────────────────────────────────────────────────┘
```

---

## 2. Component Mapping Table

| Starter Kit Capability | Starter Kit Module | VISION Domain Implementation |
|---|---|---|
| **Durable State Machine** | `runtime/runner.py` | Executable state machine implementing the 12 VISION study states (`START_STUDY` $\rightarrow$ `SESSION_COMPLETE`) |
| **State Persistence** | `runtime/db.py` | `StudentState` database layer preserving student mastery, misconceptions, and learning styles across sessions |
| **Spend Limit Guard** | `runtime/budget.py` | Hard limit of 12 LLM API calls per run; automatically transitions to `status="given_up"` on breach |
| **Revision Limit Guard** | `runtime/budget.py` | Hard limit of 3 backward prerequisite depth steps; triggers `WAITING_FOR_HUMAN` |
| **Human Callback** | `runtime/human.py` | Serialization & resume engine for `HumanDecision` records when user or mentor input is requested |
| **Retrieval Engine** | `runtime/retrieval.py` | Structured retrieval from `corpus/data_structures_notes.md` with exact quote verification |

---

## 3. Starter Kit Verification & Smoke Test Protocol

Before attaching LLM API keys or building domain UI, the team must execute the starter kit verification protocol:

1. **Clone Starter Kit Spine:** Verify `runner.py`, `db.py`, and `budget.py` compile cleanly.
2. **Execute Smoke Test:** Run `python -m tests.test_smoke` to verify deterministic state transitions without API keys.
3. **Verify Replay Support:** Pass mock JSON responses through state transitions to validate schema serialization.
4. **Test Pause/Resume:** Trigger a mock human callback and verify that session state serializes to disk and resumes correctly.

