# Implementation and Verification Tasks: TB3 Proposal Refactor

> **Change**: tb3-proposal-refactor
> **Schema**: spec-driven
> **Status**: Implementation tasks. No tasks are marked complete; all are pending.

---

## Task Tracking

Progress: 0 / 32 tasks complete

---

## Phase 1 — Research Documentation

### T1.1 — Create A–F Research Abstract

- [ ] Create `docs/abstract-A-to-F.md` with six sections (Attention Getter, But, Cure, Development, Experiments, Findings).
- [ ] Verify all source slide references are cited (Slides 2–4, 17–21).
- [ ] Verify experiments section is explicitly marked as planned, not completed.

### T1.2 — Create Core Concepts Document

- [ ] Create `docs/core-concepts.md` documenting all five design principles from Slide 7.
- [ ] Include: principle name (Chinese + English), system implication, evidence basis, design implications, and evidence boundary for each principle.
- [ ] Verify references [2][4][5][6][8][9] are cited appropriately.

### T1.3 — Create Research Trade-offs Document

- [ ] Create `docs/research-tradeoffs.md` documenting the three research debates from Slides 8–10.
- [ ] For each debate: include Perspective A, Perspective B, evidence and unresolved questions, our approach, and remaining limitation.
- [ ] Verify that claims are qualified and not overstated (no "X is always better than Y").

### T1.4 — Create Critical Questions Document

- [ ] Create `docs/critical-questions.md` preserving Q1–Q10 from Slides 11–12.
- [ ] Include Chinese original, English translation, context/analysis, system implication, and evidence basis for each question.
- [ ] Verify that Q7 and Q8 include statistical nuance (small sample, sim-to-real gap).

---

## Phase 2 — OpenSpec Artifacts

### T2.1 — Verify Proposal

- [ ] Confirm `proposal.md` exists and covers: Why (motivation), What Changes (six sections), Capabilities (five), Impact (documentation + scope), Preserved Evaluation Plan, and Research Boundaries.
- [ ] Verify proposal explicitly states that no completed robot experiments are claimed.

### T2.2 — task-understanding Spec

- [ ] Create `openspec/changes/tb3-proposal-refactor/specs/task-understanding/spec.md`.
- [ ] Verify: `## Purpose` section is present and ≥ 50 characters.
- [ ] Verify: `## ADDED Requirements` section is present.
- [ ] Verify: All requirements use `### Requirement:` headers.
- [ ] Verify: Normative language (SHALL/MUST) used consistently.
- [ ] Verify: All scenarios use `#### Scenario:` headers with WHEN and THEN.
- [ ] Verify: Scenarios describe observable behavior, not implementation details.

### T2.3 — navigation-validation Spec

- [ ] Create `openspec/changes/tb3-proposal-refactor/specs/navigation-validation/spec.md`.
- [ ] Verify: `## Purpose` ≥ 50 characters, `## ADDED Requirements`, `### Requirement:` headers, SHALL/MUST language, `#### Scenario:` with WHEN/THEN.
- [ ] Verify: Sensor freshness, localization, lifecycle, and path feasibility checks are each specified as separate requirements.
- [ ] Verify: "SHALL NOT dispatch" is explicitly stated for all failure conditions.

### T2.4 — task-execution Spec

- [ ] Create `openspec/changes/tb3-proposal-refactor/specs/task-execution/spec.md`.
- [ ] Verify: `## Purpose` ≥ 50 characters, `## ADDED Requirements`, `### Requirement:` headers, SHALL/MUST language, `#### Scenario:` with WHEN/THEN.
- [ ] Verify: Single-retry logic is specified with all three qualifying conditions.
- [ ] Verify: State transition table is present and covers all terminal states.
- [ ] Verify: Cancellation confirmation requirement is specified.

### T2.5 — evidence-reporting Spec

- [ ] Create `openspec/changes/tb3-proposal-refactor/specs/evidence-reporting/spec.md`.
- [ ] Verify: `## Purpose` ≥ 50 characters, `## ADDED Requirements`, `### Requirement:` headers, SHALL/MUST language, `#### Scenario:` with WHEN/THEN.
- [ ] Verify: "SHALL NOT report ARRIVED without SUCCEEDED result" is explicitly stated.
- [ ] Verify: Event log JSONL format with required fields is specified.
- [ ] Verify: State mapping table covers all 8 states (CLARIFY, REJECTED, NOT\_READY, NO\_PATH, ARRIVED, FAILED, CANCELED, TIMEOUT/FAULT).

### T2.6 — evaluation Spec

- [ ] Create `openspec/changes/tb3-proposal-refactor/specs/evaluation/spec.md`.
- [ ] Verify: `## Purpose` ≥ 50 characters, `## ADDED Requirements`, `### Requirement:` headers, SHALL/MUST language, `#### Scenario:` with WHEN/THEN.
- [ ] Verify: B0, B1, B2 baseline table is complete and accurate.
- [ ] Verify: 60/20/40 language task split is preserved.
- [ ] Verify: 40 simulation runs per method (4 × 2 × 5) is preserved.
- [ ] Verify: 12 real-robot cases per method (36 case–method pairs) is preserved.
- [ ] Verify: Both ablations (B2 − Clarification, B2 − Agent Pre-check) are specified.
- [ ] Verify: Wilson 95% CI statistical requirement is present.
- [ ] Verify: "experiments are planned, not completed" is stated.

### T2.7 — Design Document

- [ ] Create `openspec/changes/tb3-proposal-refactor/design.md`.
- [ ] Verify: All 8 modules are described with responsibility, input, and output.
- [ ] Verify: Navigation Adapter interface table is present.
- [ ] Verify: Five design decisions are documented with rationale and trade-off.
- [ ] Verify: Algorithm summary covers all 14 steps from Appendix A06–A07.
- [ ] Verify: Baseline/ablation configuration table is present and accurate.

### T2.8 — Tasks Document

- [ ] Create `openspec/changes/tb3-proposal-refactor/tasks.md` (this document).
- [ ] Verify: All phases and task IDs are present.
- [ ] Verify: Each task has at least one verifiable checkbox item.

---

## Phase 3 — Academic Writing

### T3.1 — IEEE Abstract

- [ ] Create `docs/abstract-ieee.md` with an IEEE-style abstract (≤ 250 words).
- [ ] Verify: Covers motivation, proposed method, experimental design, expected contributions.
- [ ] Verify: Planned experiments are framed as planned, not as reported results.
- [ ] Verify: Academic English; no unsupported claims.

### T3.2 — Introduction

- [ ] Create `docs/introduction.md`.
- [ ] Verify: Sections present — research motivation, limitations and gap, proposed method, RQ1–RQ3, expected contributions.
- [ ] Verify: RQ1 (improper dispatch), RQ2 (reporting accuracy), RQ3 (latency) are explicitly stated.
- [ ] Verify: Original reference numbering preserved; no fabricated citations.
- [ ] Verify: Claims flagged with [EVIDENCE NEEDED] where source support is missing.

### T3.3 — Related Work

- [ ] Create `docs/related-work.md`.
- [ ] Verify: Sections present — LLM reasoning and tool use, ROS 2/Nav2 navigation, navigation reliability, research debates, gap and positioning.
- [ ] Verify: References [1]–[12] are cited accurately with correct year, title, and finding.
- [ ] Verify: Evidence boundaries and "not directly applicable to Burger" qualifications are present.
- [ ] Verify: Original reference numbering preserved throughout.

---

## Phase 4 — Project Review and Delivery

### T4.1 — Update README

- [ ] Update `README.md` with: project goals, research contributions (RQ1–RQ3), OpenSpec workflow overview, directory structure, and validation instructions.
- [ ] Verify: README does not modify or reference robot control code.

### T4.2 — Create Refactor Report

- [ ] Create `docs/refactor-report.md` mapping each DIC3 requirement to files produced in this change.
- [ ] Verify: All six change sections from `proposal.md` are mapped to output files.
- [ ] Verify: All five capabilities are mapped to spec files.
- [ ] Verify: Validation results and remaining issues are documented.

### T4.3 — Source Material Verification

- [ ] Verify: `references/TB3_Proposal_Final_35slides.pptx` is unmodified.
- [ ] Verify: No robot control code files have been created or modified.
- [ ] Verify: No navigation commands have been executed.

### T4.4 — Spec Validation

- [ ] Validate all five spec files against the requirement format (Purpose ≥ 50 chars, ADDED Requirements, Requirement: headers, SHALL/MUST, Scenario: with WHEN/THEN).
- [ ] Check that no spec claims completed experimental results.
- [ ] Verify cross-references between specs are consistent (task-understanding → navigation-validation → task-execution → evidence-reporting → evaluation).

---

## Verification Checklist (Post-Creation)

Run these checks after all artifacts are created:

- [ ] All files in `openspec/changes/tb3-proposal-refactor/` are present: `.openspec.yaml`, `design.md`, `tasks.md`, `specs/task-understanding/spec.md`, `specs/navigation-validation/spec.md`, `specs/task-execution/spec.md`, `specs/evidence-reporting/spec.md`, `specs/evaluation/spec.md`.
- [ ] All files in `docs/` are present: `abstract-A-to-F.md`, `core-concepts.md`, `research-tradeoffs.md`, `critical-questions.md`, `abstract-ieee.md`, `introduction.md`, `related-work.md`, `refactor-report.md`.
- [ ] `README.md` has been updated.
- [ ] `proposal.md` exists at repository root (pre-existing; not modified by this change).
- [ ] No files in the robot control code directories have been modified.
- [ ] No `git push` has been executed.

---

## Suggested Commit Commands

After completing all tasks and reviewing all artifacts:

```bash
# Stage all new documentation and openspec artifacts
git add docs/
git add openspec/changes/tb3-proposal-refactor/
git add README.md

# Commit with descriptive message
git commit -m "docs(tb3-proposal-refactor): complete OpenSpec spec-driven refactor

- Add A-F research abstract (docs/abstract-A-to-F.md)
- Add five core concepts from slide 7 (docs/core-concepts.md)
- Add three research trade-offs from slides 8-10 (docs/research-tradeoffs.md)
- Add critical questions Q1-Q10 from slides 11-12 (docs/critical-questions.md)
- Add IEEE-style abstract (docs/abstract-ieee.md)
- Add academic introduction with RQ1-RQ3 (docs/introduction.md)
- Add related work review with references [1]-[12] (docs/related-work.md)
- Add OpenSpec specs for five capabilities
- Add design.md with eight-module architecture
- Add tasks.md with implementation checklist
- Add refactor-report.md with DIC3 requirements mapping
- Update README.md

Source: TB3_Proposal_Final_35slides.pptx (35 slides)
Platform: TurtleBot3 Burger, Ubuntu 22.04, ROS 2 Humble, Nav2
No robot control code modified. No experiments conducted."

# Review before push (do not push automatically)
git log --oneline -5
git status
```
