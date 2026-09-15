# VISION Architecture

```text
Student
  |
  v
Study Session / Runner
  |
  +--> Persistent StudentState
  +--> Course + Prerequisite Graph
  +--> Evidence Retrieval
  +--> Adaptive Decision
         |
         +--> Teach
         +--> Practice
         +--> Evaluate
         +--> Diagnose Gap
         +--> Reteach
         +--> Go Deeper
         +--> Wait for Human
```

Core back-edge:
`RECHECK_GAP -> GO_DEEPER -> DIAGNOSE_GAP`

Human pause:
`WAITING_FOR_HUMAN -> DIAGNOSE_GAP` after a human decision.

Deterministic controls:
- state transitions
- spend counter
- revision counter
- schema validation
- provenance checks
- unsupported-evidence handling
- pause/resume behavior

Model calls are used for interpretation and drafting, not as the authority for state transitions.
