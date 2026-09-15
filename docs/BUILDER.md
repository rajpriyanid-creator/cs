# VISION Builder Guide & Technical Blueprint

**Owners:** Jeevananthan K (Backend / Builder) & Anushya M (Frontend / DB / Builder)

This guide provides the complete developer blueprint for implementing the **VISION Execution Engine**, persistent state engine, API routes, and frontend integration.

---

## 1. Directory Structure & Module Layout

```text
vision-adaptive-study-agent/
├── slice/
│   ├── __init__.py
│   ├── main.py                # FastAPI / Web Server Application
│   ├── runner.py              # State Machine Engine & Loop Controller
│   ├── state_store.py         # SQLite / JSON Persistent State Manager
│   ├── graph_engine.py        # Prerequisite Graph Loader & Dependency Traversal
│   ├── corpus_retriever.py    # Local Vector/BM25 Corpus Retrieval & Citation Gate
│   ├── evaluator.py           # Rubric Evaluation Gate & Diagnostic Scoring
│   ├── llm_orchestrator.py    # Structured LLM API Calls (OpenAI/Pydantic/Gemini)
│   └── models.py              # Pydantic Schemas (StudentState, Attempt, etc.)
├── domain/
│   ├── prerequisite_graph.json # Bounded Data Structures Graph Data
│   └── rubric.json             # Diagnostic Evaluation Criteria
├── corpus/
│   └── data_structures_notes.md # Approved Study Material & Citation Texts
├── fixtures/
│   ├── sample_student_state.json
│   ├── replay_walkthrough_pass.json
│   └── replay_walkthrough_fail.json
└── tests/
    ├── test_state_machine.py  # Deterministic State Machine Unit Tests
    ├── test_persistence.py    # Second-Encounter Memory Tests
    └── test_provenance.py     # Prompt Injection & Citation Verification Tests
```

---

## 2. Persistent Storage Schema (SQLite & JSON)

`state_store.py` manages persistent student profiles across multiple sessions.

### SQLite Schema (`vision.db`)

```sql
CREATE TABLE IF NOT EXISTS student_states (
    student_id TEXT PRIMARY KEY,
    course_id TEXT NOT NULL,
    mastered TEXT NOT NULL,         -- JSON Array of string concept IDs
    weak TEXT NOT NULL,             -- JSON Array of string concept IDs
    misconceptions TEXT NOT NULL,   -- JSON Array of string misconception IDs
    prerequisite_history TEXT,      -- JSON Array of attempt paths
    successful_modes TEXT,          -- JSON Array of teaching modes
    failed_modes TEXT,              -- JSON Array of teaching modes
    updated_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);

CREATE TABLE IF NOT EXISTS study_sessions (
    run_id TEXT PRIMARY KEY,
    student_id TEXT NOT NULL,
    course_id TEXT NOT NULL,
    target_concept TEXT NOT NULL,
    status TEXT NOT NULL,           -- 'active', 'completed', 'waiting_human', 'given_up'
    model_call_count INTEGER DEFAULT 0,
    revision_count INTEGER DEFAULT 0,
    session_data TEXT NOT NULL,     -- Full serialized execution context
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    FOREIGN KEY(student_id) REFERENCES student_states(student_id)
);
```

---

## 3. Step-by-Step Build Order & Hard Cut-Lines

### Phase 1: Spine, Typed Schemas & Replay Runner (Hours 0–4)
- Implement `slice/models.py` with all Pydantic dataclasses.
- Build `slice/runner.py` with pure deterministic state transitions driven by mock inputs.
- **Hard Cut-Line 1:** Complete study session transitions from `START_STUDY` to `SESSION_COMPLETE` using replay fixtures without calling external APIs.

### Phase 2: Domain, Graph & Retrieval Gate (Hours 4–12)
- Load `domain/prerequisite_graph.json` into `slice/graph_engine.py`.
- Implement `slice/corpus_retriever.py` to retrieve verified text chunks from `corpus/data_structures_notes.md`.
- Build deterministic provenance validator to ensure retrieved quotes match exactly.
- **Hard Cut-Line 2:** Diagnostic question generation uses retrieved context; citation gate rejects fabricated sources.

### Phase 3: Live LLM Integration, Budget Guards & Persistence (Hours 12–19)
- Wire `slice/llm_orchestrator.py` with Pydantic structured outputs (`GapHypothesis`, `Evaluation`, `TeachingAction`).
- Implement hard spend counter (Max 12 calls) and revision counter (Max 3 revisions).
- Wire `slice/state_store.py` to persist `StudentState` after every step.
- Implement `WAITING_FOR_HUMAN` pause/resume API endpoints.
- **Hard Cut-Line 3:** Agent goes backwards (`GO_DEEPER`) when recheck fails, pauses on 3 revisions, and remembers state in a 2nd session.

### Phase 4: UI & Walkthrough Integration (Hours 19–24)
- Build responsive web frontend (HTML/JS/Vanilla CSS) connecting to FastAPI runner.
- Display interactive prerequisite graph status, diagnosis cards, reteaching steps, and second-encounter indicator.
- **Hard Cut-Line 4:** Full live walkthrough end-to-end playable on browser.

---

## 4. API Endpoints Contract

```http
POST /api/session/start
Content-Type: application/json

{
  "student_id": "student_A",
  "course_id": "data_structures",
  "target_concept": "binary_tree_inorder_traversal",
  "study_request": "Teach me inorder traversal and check whether I really understand it."
}

Response 200:
{
  "run_id": "run_98234",
  "current_state": "PRACTICE",
  "action": {
    "type": "diagnostic_question",
    "concept": "binary_tree_inorder_traversal",
    "question": "For a node, which order describes inorder traversal?"
  }
}
```

```http
POST /api/session/step
Content-Type: application/json

{
  "run_id": "run_98234",
  "student_answer": "Root, left, right."
}

Response 200:
{
  "run_id": "run_98234",
  "current_state": "RETEACH_PREREQ",
  "hypothesis": {
    "target": "binary_tree_inorder_traversal",
    "candidate_prerequisite": "recursion",
    "confidence": 0.85,
    "reasoning": "Student misidentified root node placement; prior state shows weak recursion."
  },
  "action": {
    "type": "reteach_prerequisite",
    "concept": "recursion",
    "explanation": "In recursive traversal, before processing a node's value...",
    "question": "When a recursive function reaches a node, what happens before that node is processed if the left child exists?"
  }
}
```

```http
POST /api/session/human-resume
Content-Type: application/json

{
  "run_id": "run_98234",
  "decision": "change_mode",
  "note": "Switch to diagrammatic step-by-step example."
}
```

