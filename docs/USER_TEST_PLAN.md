# VISION Real-User Walkthrough & Testing Plan

**Lead Tester:** Dhanush S (QA and Tester / Verifier)

This document details the operational execution plan for testing **VISION — Prerequisite Debugger / Adaptive Study Agent** with 3 real engineering students during Phase 4 of the hackathon.

---

## 1. Test Objectives & Target Participants

The goal is to gather empirical qualitative and quantitative evidence on how real students experience adaptive prerequisite debugging.

### Test Participants
- **User 1 (Student A — Weak Baseline):** Has struggled with recursion in past semesters. Tests the full diagnostic and reteaching path.
- **User 2 (Student B — Intermediate):** Understands tree concepts well; tests tie-breaker differentiation on careless errors.
- **User 3 (Student C — Second Encounter Tester):** Completes Session 1 (Inorder Traversal) and returns for Session 2 (BST Insertion) to verify cross-session cognitive state memory.

---

## 2. Step-by-Step Walkthrough Scripts

### Script for Session 1 (Users 1 & 2)

```text
[Step 1] Initial Setup:
  - Direct the student to open the VISION Study Interface.
  - Prompt: "Teach me Binary Tree Inorder Traversal and check whether I really understand it."

[Step 2] Diagnostic Check:
  - The system presents Q1: "For a node, which order describes inorder traversal?"
  - Instruct User 1 to select "Root, left, right" (Intentional failure).
  - Instruct User 2 to select "Left, root, right" (Pass path).

[Step 3] Tie-Breaker / Diagnosis:
  - Observe system output: Does it present a tie-breaker question or directly output a GapHypothesis?
  - Verify if candidate prerequisite identified ("recursion") matches User 1's real learning gap.

[Step 4] Targeted Reteaching:
  - User 1 receives targeted recursion explanation formatted as "short_example_and_diagram".
  - Verify that the explanation does NOT reveal the answer to Q1.

[Step 5] Prerequisite Recheck:
  - User 1 answers diagnostic question on base-case handling.
  - State machine updates recursion status to "demonstrated".

[Step 6] Target Re-test & Mastery:
  - User 1 receives target question again and answers correctly ("B, A, C").
  - System updates persistent state and completes session.
```

### Script for Session 2 (User 3 — Second Encounter)

```text
[Step 1] Initiate Session 2:
  - Student C initiates study session for a NEW target topic: "BST Insertion".

[Step 2] Observe Memory Persistence:
  - Verify that system loads StudentState from Session 1.
  - Confirm system does NOT repeat basic recursion lessons.
  - Confirm system utilizes "short_example_and_diagram" as preferred teaching mode automatically.
```

---

## 3. Feedback Capture Sheet

| Field | User 1 | User 2 | User 3 |
|---|---|---|---|
| **Student ID / Alias** | Student A | Student B | Student C |
| **First Point of Hesitation** | | | |
| **Did Diagnosis Make Sense? (Y/N)** | | | |
| **Did Reteaching Feel Targeted? (1-5)**| | | |
| **Was 2nd Session Memory Visible?** | N/A | N/A | |
| **Student Quote / Qualitative Note**| | | |
| **Concrete UX Improvement Identified**| | | |

