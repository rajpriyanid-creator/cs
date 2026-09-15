# AgentSpec — VISION: Prerequisite Debugger — Adaptive Study Agent

**Team:** VISION  
**College:** CEG, Anna University  
**Department:** Computer Science and Engineering  
**Primary challenge area:** Agentic Personalized Education  
**Supporting directions:** Persistent Student State; Long-Term Cognitive Tracking; Human-AI Collaborative Learning  
**Submission:** Preliminary AgentSpec

## Team

- **Rajpriyan S** — Team Lead / Agent Handler — Roll No. 2024103563 — **Designer**
- **Megala M** — UI/UX Designer — Roll No. 2024103608 — **UX / Design support**
- **Jeevananthan K** — Backend Developer — Roll No. 2024103554 — **Builder**
- **Anushya M** — Frontend / DB — Roll No. 2024103566 — **Builder**
- **Dhanush S** — QA and Tester — Roll No. 2024103533 — **Verifier: three real-user walkthroughs, one adversarial stress test, regression checks, and iteration log**

---

## 1. The setting

One engineering student starts a focused study session for a difficult course topic. For the event prototype, the course is **Data Structures** and the target is **Binary Tree Inorder Traversal**.

The student wants VISION to do more than explain a concept: **teach → practice → test → identify what is weak → reteach what is needed → test again → remember the result**.

**Who exactly:** one engineering student studying one topic from one known course.

**What they do today:** read notes or a lesson, solve questions, see right/wrong feedback, search for another explanation, and decide what to study next.

**Why that is hard:** the visible mistake may not be the real learning gap. A student can fail the target because a prerequisite or misconception underneath it is weak. A fixed lesson-and-quiz flow may keep repeating the target instead of going backwards.

## 2. The problem this solves

A student can repeatedly study the same topic and still fail because the real problem sits underneath it. For example, a student may fail inorder traversal because recursive call reasoning is weak. Rereading traversal notes may not repair that cause.

The result is repeated confusion, wasted practice, and a misleading progress signal. VISION turns the study session into an adaptive loop:

**Learn → Practice → Diagnose → Reteach → Re-test → Improve → Remember.**

The key decision is not just what content to generate; it is **what the student should do next based on current evidence and persistent learning state**.

## 3. What you are building

**Input:** a student's study request, one bounded course/prerequisite graph, approved teaching material and exercises, student responses, and saved learner state.

**Output:** an appropriate teaching/test action, a supported learning-gap hypothesis when needed, targeted reteaching, re-evaluation, and an updated learner state.

**Never, however much a user wants it:** reveal an answer merely to make the student pass; invent a prerequisite or claim unsupported evidence; or make formal grading/progression decisions.

**Why this is agentic:** VISION reads persistent state, chooses between teaching, testing, diagnosing, reteaching, going deeper, or asking a human; revises its hypothesis when evidence contradicts it; and can send work backwards to an earlier prerequisite. The number and direction of steps depend on the student's responses and stored history rather than one fixed sequence.

## 4. A complete walkthrough

This is the concrete acceptance run. The same path becomes the baseline test; live users add real observations.

### Step 1 — Start study and read state

**Student:** Student A  
**Course:** Data Structures  
**Goal:** Learn and apply Binary Tree Inorder Traversal  
**Saved state:** `recursion = weak`; `binary_tree_traversal = inconsistent`; `last_successful_mode = short_example_and_diagram`.

Student enters:

> “Teach me inorder traversal and check whether I really understand it.”

VISION loads the learner state and course graph and chooses a short readiness check instead of assuming a full lesson is best.

```json
{
  "target_concept": "binary_tree_inorder_traversal",
  "initial_action": "diagnostic_check",
  "known_weaknesses": ["recursion"],
  "teaching_mode": "short_example_and_diagram"
}
```

### Step 2 — Test understanding

**Q1:** “For a node, which order describes inorder traversal?”  
**Student:** “Root, left, right.”

VISION treats this as evidence, not as a final diagnosis.

**Tie-breaker Q2:** “For A with left child B and right child C, which node is output first in inorder traversal?”  
**Student:** “A.”

The agent now has evidence that the target rule is not yet being applied correctly.

### Step 3 — Form one gap hypothesis

VISION compares the failure with the local prerequisite graph and persistent state.

```json
{
  "target": "binary_tree_inorder_traversal",
  "candidate_prerequisite": "recursion",
  "reason": "current failure + prerequisite relation + prior weak state"
}
```

### Step 4 — Reteach the prerequisite

VISION retrieves approved recursion material and uses the student's previously successful format.

It does **not** reveal the original answer.

**Diagnostic question:**  
“When a recursive function reaches a node, what happens before that node is processed when the left subtree still has nodes to visit?”

**Student:** “It keeps going into the left side.”

The answer is partial, so VISION does not yet mark the prerequisite as repaired. It chooses a concrete tie-breaker that checks the prerequisite's base case before deciding whether more reteaching is necessary.

**Tie-breaker Q:** “What should happen when the current node is `null`?”  
**Student:** “Return without processing a node.”

Because the tie-breaker resolves the uncertainty, VISION can now evaluate the prerequisite as demonstrated.

### Step 5 — Evaluate the prerequisite

```json
{
  "concept": "recursion",
  "status": "demonstrated",
  "evidence": [
    "student explained recursive descent",
    "student identified the null/base case"
  ]
}
```

### Step 6 — Return to the target

VISION asks the original target question again.

**Question:** “For A with left child B and right child C, what is the inorder traversal?”  
**Student:** “B, A, C.”

VISION records:

```json
{
  "student_id": "student_A",
  "concept": "binary_tree_inorder_traversal",
  "status": "improved",
  "prerequisite_repaired": "recursion"
}
```

### Required failure branch

If the student still fails the prerequisite checks, VISION does not repeat the same lesson forever:

```text
RECHECK_GAP → GO_DEEPER → DIAGNOSE_GAP
             ↓
      next supported prerequisite
             ↓
        RETEACH_PREREQ
             ↓
          RECHECK_GAP
```

After the revision limit is reached, VISION enters `WAITING_FOR_HUMAN`.

## 5. Who is doing the thinking

| step | the agent does it | the human does it | what the human loses if the agent does it |
|---|---|---|---|
| Read learner state | yes |  | nothing; it is already-recorded evidence |
| Choose teach/test/diagnose/reteach/go-deeper | yes |  | nothing; this is the adaptive decision |
| Select a prerequisite hypothesis | yes |  | nothing; it is a bounded hypothesis to test |
| Choose diagnostic questions | yes |  | nothing; this is part of the learning loop |
| Evaluate against the rubric | yes |  | nothing; the rubric is explicit |
| Define the student's learning goal |  | yes | the student's intention must remain theirs |
| Resolve an escalation |  | yes | removes unnecessary human judgment if automated |
| Formal grading/progression |  | yes | academic authority must remain human |

**If the agent asks a person something:**

**The question it asks, and who answers it:**  
“If the prerequisite path has been revised three times and we still cannot establish the missing foundation, should we continue drilling or switch explanation mode?” The student or designated mentor answers.

**What happens if nobody answers, and how the output shows that:**  
The run enters `WAITING_FOR_HUMAN`, stores `answer_status = "no_answer_yet"`, shows that the response is pending, and remains resumable.

## 6. The state machine

```text
START_STUDY
    |
    v
READ_LEARNER_STATE
    |
    v
TEACH_OR_DIAGNOSE
    |
    v
PRACTICE
    |
    v
EVALUATE
   /   \
 pass   fail/uncertain
  |         |
  v         v
TARGET   DIAGNOSE_GAP
MASTERED       |
  |            v
  |       RETEACH_PREREQ
  |            |
  |            v
  |       RECHECK_GAP
  |        /         \
  | demonstrated   unresolved/uncertain
  |      |              |
  |      v              v
  |  RECHECK_ORIGINAL  GO_DEEPER
  |     /      \         |
  |  pass      fail       v
  |   |          |   DIAGNOSE_GAP
  |   |          |       |
  |   |          +-------+
  |   |
  |   v
  +-> SESSION_COMPLETE

GO_DEEPER -- revision limit reached --> WAITING_FOR_HUMAN
WAITING_FOR_HUMAN -- answer/resume --> DIAGNOSE_GAP
WAITING_FOR_HUMAN -- no answer --> WAITING_FOR_HUMAN

Any state requiring termination because evidence/spend/revision bounds are exhausted
    --> SESSION_COMPLETE (status = "given_up")
```

| state | active / waiting / finished | what moves it on |
|---|---|---|
| `START_STUDY` | active | valid study request |
| `READ_LEARNER_STATE` | active | state loaded |
| `TEACH_OR_DIAGNOSE` | active | next action selected |
| `PRACTICE` | active | student submits response |
| `EVALUATE` | active | response classified |
| `DIAGNOSE_GAP` | active | supported gap hypothesis |
| `RETEACH_PREREQ` | active | intervention produced |
| `RECHECK_GAP` | active | demonstrated / unresolved / uncertain |
| `GO_DEEPER` | active | deeper prerequisite exists or human escalation required |
| `RECHECK_ORIGINAL` | active | target re-tested |
| `WAITING_FOR_HUMAN` | waiting | human response/resume |
| `TARGET_MASTERED` | finished | none; immediately transitions to `SESSION_COMPLETE` |
| `SESSION_COMPLETE` | finished | none |
| `GIVEN_UP` | finished | terminal outcome represented by `SESSION_COMPLETE(status="given_up")`; this label is not used as a separate runtime state |

**What can send work backwards:** `RECHECK_GAP` can reject the current hypothesis and send the run through `GO_DEEPER` to `DIAGNOSE_GAP`. A failed `RECHECK_ORIGINAL` also returns to `DIAGNOSE_GAP`; a passed re-check moves to `SESSION_COMPLETE`. `RECHECK_ORIGINAL` can also send a failed target re-check to `DIAGNOSE_GAP` rather than ending the session.

**What the run decides that the diagram cannot show:** which hypothesis is most plausible, whether evidence is sufficient, whether the current teaching mode worked, whether another diagnostic is needed, and whether to escalate.

**Spend limit - what bounds cost:** maximum **12 model/tool calls per run**, including retries, plus a runtime ceiling.

**Revision limit - what bounds going backwards:** maximum **3 backward prerequisite revisions per target per run**. This counter is separate from the spend budget.

## 7. The data model

```python
class StudentState(BaseModel):
    student_id: str
    course_id: str
    mastered: list[str] = Field(max_length=50)
    weak: list[str] = Field(max_length=50)
    misconceptions: list[str] = Field(max_length=50)
    prerequisite_history: list[list[str]] = Field(max_length=20)
    successful_modes: list[str] = Field(max_length=10)
    failed_modes: list[str] = Field(max_length=10)

class StudySession(BaseModel):
    run_id: str
    student_id: str
    course_id: str
    target_concept: str
    status: str

class Attempt(BaseModel):
    run_id: str
    concept: str
    question: str
    answer: str
    expected: str

class GapHypothesis(BaseModel):
    run_id: str
    target_concept: str
    candidate_prerequisite: str
    confidence: float
    evidence_refs: list[str] = Field(max_length=5)

class Evaluation(BaseModel):
    run_id: str
    concept: str
    status: str
    reason: str
    next_action: str

class HumanDecision(BaseModel):
    run_id: str
    decision: str
    note: str
```

**Record kinds written to the store:**

| kind | written by | when |
|---|---|---|
| `student_state` | state manager | start/end and every state update |
| `study_session` | session manager | create/complete |
| `attempt` | practice step | every student answer |
| `gap_hypothesis` | agent | every prerequisite hypothesis |
| `teaching_action` | teaching step | every intervention |
| `evaluation` | evaluator | every check |
| `learning_update` | state manager | confirmed learning change |
| `human_question` | escalation step | human needed |
| `human_decision` | human UI | response received |

Repeated attempts, interventions and evaluations are retained as history, not overwritten.

## 8. Step-by-step contracts

**`read_learner_state` · `START_STUDY` → `READ_LEARNER_STATE`**  
- **What:** load durable learner state for the selected course.  
- **Why this way:** the next action must depend on previous learning.  
- **Reads / writes:** reads `StudentState`; writes active session context.  
- **Done when:** state is available or the run stops as unsupported.

**`teach_or_diagnose` · `READ_LEARNER_STATE` → `TEACH_OR_DIAGNOSE`**  
- **What:** choose a bounded teaching or diagnostic action.  
- **Why this way:** do not teach what is already demonstrated.  
- **Reads / writes:** learner state + target; writes next-action decision.  
- **Done when:** exactly one next action is selected.

**`practice` · `TEACH_OR_DIAGNOSE` → `PRACTICE`**  
- **What:** provide one diagnostic/practice question.  
- **Why this way:** exposure is not evidence of understanding.  
- **Reads / writes:** concept + learner state + approved exercises; writes `Attempt`.  
- **Done when:** one response is received.

**`evaluate` · `PRACTICE` → `EVALUATE`**  
- **What:** classify the response against an explicit rubric.  
- **Why this way:** the result drives the next state.  
- **Reads / writes:** attempt + rubric + state; writes `Evaluation`.  
- **Done when:** `demonstrated`, `not_demonstrated`, or `uncertain`.

**`diagnose_gap` · `EVALUATE` → `DIAGNOSE_GAP`**  
- **What:** select one prerequisite hypothesis from current evidence, graph and history.  
- **Why this way:** one testable hypothesis is safer than a generic list.  
- **Reads / writes:** graph + learner state + evaluation; writes `GapHypothesis`.  
- **Done when:** candidate and evidence validate.

**`reteach_prereq` · `DIAGNOSE_GAP` → `RETEACH_PREREQ`**  
- **What:** reteach the suspected prerequisite using an appropriate mode.  
- **Why this way:** repair the suspected cause rather than repeat the target lesson unchanged.  
- **Reads / writes:** approved corpus + mode history; writes teaching action.  
- **Done when:** bounded intervention and diagnostic question are ready.

**`recheck_gap` · `RETEACH_PREREQ` → `RECHECK_GAP`**  
- **What:** test whether the prerequisite is demonstrated.  
- **Why this way:** reteaching is not counted as success without evidence.  
- **Reads / writes:** teaching action + response + rubric; writes evaluation.  
- **Done when:** demonstrated, unresolved, or uncertain.

**`go_deeper` · `RECHECK_GAP` → `GO_DEEPER`**  
- **What:** move one level backwards only when unresolved and revision budget remains.  
- **Why this way:** this is the explicit agentic back-edge.  
- **Reads / writes:** prerequisite history + evaluation; writes revision record.  
- **Done when:** next candidate exists or escalation is required.

**Where the documents come in:** the prototype reads one course syllabus, a textbook/table of contents or approved notes, a bounded prerequisite graph, and selected exercise material.

**What each one lets it prove:** syllabus/TOC defines scope; graph defines allowed dependencies; approved teaching material supports lessons; exercises support diagnostics.

**What it does when evidence is not there:** returns `could_not_establish` and stops or asks for human review. It does not silently fill the gap from general model memory.

**How a citation gets checked:** a displayed source must have a returned source identifier, and a quotation must match retrieved source text exactly. The provenance check is deterministic.

**Where the human comes in:** the human decision is stored as `HumanDecision`, read again on resume, and changes whether VISION continues drilling or switches mode. A missing response does not count as permission to continue: the run stays in `WAITING_FOR_HUMAN`.

## 9. The second encounter

The second encounter is a new study session with the same student.

After the first session, VISION stores:

```text
Target demonstrated: binary_tree_inorder_traversal
Prerequisite repaired: recursion
Still weak: stack-depth reasoning
Successful mode: short example + diagram
Failed mode: long verbal explanation
```

Later the student starts **BST Insertion**.

A fresh chat would start again. VISION does not:

- it does not automatically reteach a prerequisite already demonstrated;
- it tests the unresolved stack-depth reasoning;
- it starts with the previously successful teaching mode;
- it uses the previous attempt history when deciding the next action.

The second encounter therefore proves that persistent state materially changes behavior.

## 10. Files and responsibilities

| file | owner | owns | done when |
|---|---|---|---|
| `slice/` | Builder | state machine, persistence, limits, pause/resume | runtime works |
| `domain/graph.md` | Designer | bounded prerequisite graph | demo concepts covered |
| `corpus/` | Designer + Builder | syllabus, approved notes, exercises | evidence retrievable |
| `prompts/` | Designer | agent rules and rubrics | versioned and reproducible |
| `app/` | Builder + Megala | student UI | walkthrough executable |
| `tests/` | Verifier + Builder | normal/failure/adversarial tests | regression passes |
| `evidence/` | Dhanush | user tests + iteration log | evidence captured |

**Helpers that carry real logic:** state persistence, prerequisite lookup, evaluation gate, spend counter, revision counter, provenance check, transition guards.

**Which are model calls:** next-action decision, gap hypothesis, teaching draft, question generation, evaluation interpretation.

**Architecture vs domain opinion:** persistence/state transitions/budgets/provenance are architecture; prerequisite relationships and learning rubrics are domain decisions.

## 11. What this deliberately does not do

1. **Not every subject in the event build.** One course and a bounded graph let us test depth and reliability.
2. **Not a general chatbot.** Learning actions remain tied to the selected course, evidence, current response and learner state.
3. **No projects, project stress testing, collaboration matching, mentor matching or faculty dashboard.** These belong to the broader VISION roadmap, not this event slice.
4. **No voice or multilingual layer in the core build.** These are future teaching/interface modes.
5. **No answer dumping or formal grading.** The goal is improved understanding; academic authority remains human.

## 12. Build order

| phase | what lands | hours |
|---|---|---:|
| 1 | Starter-kit spine, states, typed records, persistence, fake transitions, replayable responses | 4 |
| | *cut line: complete study state-machine run works without a live model* | |
| 2 | One-subject graph, retrieval, teaching, practice, evaluation, provenance | 8 |
| | *cut line: study → test → targeted intervention → improvement works* | |
| 3 | Backward loop, separate budgets, second-encounter state, human pause/resume, UI | 7 |
| | *cut line: agent changes direction and uses saved state* | |
| 4 | Three user walkthroughs, one stress test, fix, regression, evidence and demo packaging | 5 |

**Total planned build time: 24 hours**, leaving buffer within the 48-hour event for debugging, testing, visible iteration and presentation.

**Where the hours will actually go:** validating diagnoses, making the back-edge safe, checking evidence/provenance, and observing students.

## 13. The demo

1. Start with Student A's saved learner state.
2. Begin the study session and show the readiness diagnosis.
3. Show the prerequisite hypothesis.
4. Reteach without revealing the final target answer.
5. Show a diagnostic re-check.
6. Show the failure branch going one prerequisite deeper.
7. Return to the target and show improvement.
8. Start a second encounter and show persistent-state-driven behavior.
9. Trigger `WAITING_FOR_HUMAN` and resume.
10. Run the deliberate careless-error stress test and show the fix.

**Which beat is the argument:** VISION rejects an insufficient path, goes backwards to repair what is needed, returns to the target, and remembers the result.

**What is live and what is recorded:** the main run and one failure branch are live; prepared course material and replayable responses are labelled; real-user and stress-test evidence is recorded.

**What you do if the model agrees when it should object:** the deterministic evaluation gate rejects unsupported or unchanged diagnoses and routes the run to `GO_DEEPER` or `WAITING_FOR_HUMAN`.

## 14. How this grows

The event slice is the learning engine for the broader VISION idea.

- **Persistent learner model:** richer mastery, misconceptions, confidence and long-term progress.
- **Adaptive multimodal teaching:** voice, diagrams, code examples and other teaching modes.
- **Multiple courses:** course-scoped prerequisite graphs connected to the same learner state.
- **Richer assessment/resource support:** transfer tests, verified resources and mastery analytics.
- **Future peer/project/faculty features:** added around the learner-state interface without rewriting the core adaptive study loop.

The seam is the persistent learner-state interface.

## 15. What you are least sure about

1. **Root-cause accuracy:** a wrong answer may be carelessness rather than a missing prerequisite; the tie-breaker must prevent unnecessary drilling.
2. **Learning-effectiveness signal:** a correct re-test may not prove durable understanding; a transfer question may be needed.
3. **Student behavior:** students may prefer direct answers; we need to see whether the adaptive loop feels useful rather than obstructive.

## 16. Claims to verify

| claim | how to check | checked? |
|---|---|---|
| Starter kit supports durable state and pause/resume | run the smoke test and inspect relevant functions | ☐ |
| Selected model reliably returns typed decisions | replay fixtures and validate schemas | ☐ |
| Spend budget is respected including retries | run counted end-to-end fixture | ☐ |
| Retrieval returns verifiable source identifiers | compare returned IDs/text with corpus | ☐ |
| Prerequisite graph is defensible for selected syllabus | review with subject-aware teammate/mentor | ☐ |
| Evaluation distinguishes demonstrated/unresolved/uncertain | run positive, negative and ambiguous fixtures | ☐ |
| Tie-breaker reduces false diagnoses | run careless-error fixtures | ☐ |
| Provenance gate rejects altered/fabricated quotes | run altered-quote fixture | ☐ |

**Verification status:** boxes remain unchecked until the team actually runs each test. No verification claim is being made in this preliminary submission.

---

## Appendix - Verification Checklist

*This appendix is not one of the 16 template sections. It is the team’s own pre-event verification checklist.*

**The check that the pipeline works:** run a complete study session from `START_STUDY` to `SESSION_COMPLETE`, restart the process, resume from saved state, and confirm that history, spend/revision counters and second-encounter behavior are preserved.

**The adversarial one:** provide a careless/misleading student error and retrieved text containing `ignore the task`. Confirm that external text is treated as data, not instructions; unsupported claims are rejected; the agent does not reveal the answer merely to finish; and the same stress case is rerun after a visible fix.

**Final evidence:** complete three first-time student walkthroughs plus one deliberate break test, record what confused users, and make at least one visible change before the final presentation.
