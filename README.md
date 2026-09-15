# VISION — Adaptive Study Agent

AGENT-A-THON 2026 | CEG, Anna University

## Core idea
Learn -> Practice -> Diagnose -> Reteach -> Re-test -> Improve -> Remember

This repository is the **design/specification repository for the preliminary submission**. It intentionally does not pretend unfinished application code exists. The event build is a narrow study slice: one student, one course, bounded prerequisite graph, persistent learner state, adaptive teaching, re-testing, backwards reasoning, and human pause/resume.

## Repository
- `AGENTSPEC.md` — final 16-section submission specification
- `docs/` — architecture, design, builder, verifier, testing and submission guidance
- `domain/` — VISION learning-domain fixtures
- `corpus/` — approved evidence-material placeholder
- `evidence/` — real-user and stress-test evidence placeholder
- `tests/` — verification plan

## Event scope
Included: personalized study state, teaching, practice, concept-gap diagnosis, prerequisite reasoning, reteaching, re-testing, persistence, human escalation.

Excluded from the event slice: projects, project stress testing, collaboration/peer matching, mentor matching, faculty dashboards, voice, multilingual interaction, broad resource recommendation, formal grading.

## Official starter kit
https://github.com/rsimhan/agentic-slice-kit

The starter kit separates reusable runtime machinery from domain behavior. VISION's domain layer supplies learner state, prerequisite graph, teaching rules, evaluation rules and study content.

## Team
- Rajpriyan S — Team Lead / Designer
- Megala M — UI/UX
- Jeevananthan K — Backend / Builder
- Anushya M — Frontend / DB
- Dhanush S — Verifier / QA

## Submission
The Google Form asks for the GitHub repository URL and the AgentSpec file. The submission master is `AGENTSPEC.md`.

## Current status
**Design/spec ready. Implementation is not claimed as complete yet.**
