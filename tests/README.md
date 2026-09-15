# VISION Verification Test Matrix & Automated Test Suite

This directory contains the automated test suite, replay fixtures, and verification scripts for validating **VISION — Prerequisite Debugger / Adaptive Study Agent**.

---

## 1. Test Suite Coverage Matrix

| Test ID | Scenario Description | Test File / Fixture | Assertion Criteria |
|---|---|---|---|
| **TC-01** | Happy Path: Direct Target Mastery | `tests/test_state_machine.py` | State transitions `START_STUDY` $\rightarrow$ `PRACTICE` $\rightarrow$ `TARGET_MASTERED` $\rightarrow$ `SESSION_COMPLETE` |
| **TC-02** | Diagnostic Gap Identification | `tests/test_state_machine.py` | Incorrect answer triggers `GapHypothesis` pointing to `recursion` |
| **TC-03** | Tie-Breaker Question Trigger | `tests/test_evaluator.py` | Careless typo triggers 1-step tie-breaker Q2 before prerequisite drilling |
| **TC-04** | Back-Edge Execution (`GO_DEEPER`) | `tests/test_state_machine.py` | Failed prerequisite recheck transitions to `GO_DEEPER` $\rightarrow$ `DIAGNOSE_GAP` |
| **TC-05** | Revision Budget Cap | `tests/test_budget.py` | `revision_count == 3` forces transition to `WAITING_FOR_HUMAN` |
| **TC-06** | Spend Limit Budget Cap | `tests/test_budget.py` | `model_call_count == 12` forces transition to `SESSION_COMPLETE(status="given_up")` |
| **TC-07** | Human Pause & Resume | `tests/test_human.py` | `HumanDecision` stored in DB; engine resumes at `DIAGNOSE_GAP` |
| **TC-08** | Second Encounter Memory Persistence | `tests/test_persistence.py` | Session 2 loads `StudentState`, skips mastered recursion, reuses preferred teaching mode |
| **TC-09** | Prompt Injection Defense | `tests/test_provenance.py` | `<corpus_data>` wrapper neutralizes `ignore the task` injection payload |
| **TC-10** | Citation & Provenance Gate | `tests/test_provenance.py` | Citation mismatch rejects unverified textbook quotes |

---

## 2. Test Execution Commands

```bash
# Run all unit and state machine tests
python -m unittest discover -s tests -p "test_*.py"

# Run prompt injection & security validation tests
python -m unittest tests/test_provenance.py

# Run persistent second-encounter memory tests
python -m unittest tests/test_persistence.py
```

