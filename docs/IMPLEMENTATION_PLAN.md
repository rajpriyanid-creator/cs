# VISION Detailed 24-Hour Build & Implementation Roadmap

This document outlines the precise hour-by-hour build schedule for **VISION — Prerequisite Debugger / Adaptive Study Agent** during the 48-hour hackathon event (19–20 September 2026).

---

## Build Plan Strategy
- **Total Planned Build Time:** 24 Hours
- **Event Buffer:** 24 Hours reserved for debugging, live user testing, adversarial fixes, video recording, and presentation prep.

---

## Phase Breakdown

### Phase 1: Core Spine, State Machine & Replay Fixtures (Hours 0–4)
* **Goal:** Build the deterministic state machine framework without live LLM calls.
* **Tasks:**
  1. Initialize FastAPI backend application structure in `slice/`.
  2. Implement Pydantic data models in `slice/models.py`.
  3. Construct state machine transition logic in `slice/runner.py`.
  4. Create JSON replay fixtures (`fixtures/walkthrough_pass.json`, `fixtures/walkthrough_fail.json`).
* **Owner:** Jeevananthan K & Anushya M
* **Hard Cut-Line:** State machine transitions from `START_STUDY` through `SESSION_COMPLETE` deterministically using replay fixtures.

### Phase 2: Domain Modeling, Graph Engine & Corpus Retrieval (Hours 4–12)
* **Goal:** Embed the Data Structures prerequisite graph, corpus retrieval, and tie-breaker evaluator.
* **Tasks:**
  1. Create `domain/prerequisite_graph.json` covering Binary Tree Inorder Traversal $\rightarrow$ Recursion $\rightarrow$ Stack Depth.
  2. Populate `corpus/data_structures_notes.md` with verified course text and diagrams.
  3. Build `slice/graph_engine.py` for graph lookup and path finding.
  4. Build `slice/corpus_retriever.py` with deterministic citation verification.
* **Owner:** Rajpriyan S (Domain/Prompts) & Jeevananthan K (Retriever)
* **Hard Cut-Line:** Querying a concept returns verified corpus chunks; citation gate rejects unverified quotes.

### Phase 3: Live LLM Integration, Budget Guards & Persistence (Hours 12–19)
* **Goal:** Connect structured LLM calls (Gemini/OpenAI), enforce budgets, and persist state.
* **Tasks:**
  1. Implement `slice/llm_orchestrator.py` with structured output schemas for `GapHypothesis`, `Evaluation`, and `TeachingAction`.
  2. Integrate `slice/state_store.py` (SQLite) to persist `StudentState` across sessions.
  3. Enforce spend counter (Max 12 LLM calls) and revision limit (Max 3 revisions).
  4. Implement pause/resume endpoints for `WAITING_FOR_HUMAN`.
* **Owner:** Jeevananthan K & Anushya M
* **Hard Cut-Line:** System executes backwards loop (`GO_DEEPER`), pauses on 3 revisions, and demonstrates second-encounter memory reuse.

### Phase 4: Frontend UI & Verification Walkthroughs (Hours 19–24)
* **Goal:** Build interactive web frontend, execute 3 user walkthroughs and 1 stress test.
* **Tasks:**
  1. Build web UI (`app/index.html`, `app/style.css`, `app/main.js`) displaying graph status, state indicators, and diagnostic cards.
  2. Conduct 3 real-user walkthroughs with fellow students (Dhanush S).
  3. Conduct 1 adversarial stress test (prompt injection & tie-breaker checks).
  4. Implement visible fix commit for any identified flaw.
* **Owner:** Megala M (UI Design), Anushya M (Frontend JS), Dhanush S (QA/Testing)
* **Hard Cut-Line:** Live demo playable end-to-end with recorded user feedback and iteration log ready for submission.

