# Refactor Report: DIC3 Requirements-to-Files Mapping

> **Change**: tb3-proposal-refactor
> **Date**: 2026-11-12
> **Source**: TB3_Proposal_Final_35slides.pptx (35 slides)
> **Status**: Documentation refactor complete. No robot experiments have been conducted.

---

## 1. DIC3 Requirement Coverage

### Requirement 1: A–F Research Abstract

| Item | Status | File |
|------|--------|------|
| A — Attention Getter (background and motivation) | ✅ Created | [docs/abstract-A-to-F.md](abstract-A-to-F.md) §A |
| B — But (limitations and challenges) | ✅ Created | [docs/abstract-A-to-F.md](abstract-A-to-F.md) §B |
| C — Cure (proposed approach) | ✅ Created | [docs/abstract-A-to-F.md](abstract-A-to-F.md) §C |
| D — Development (architecture and methodology) | ✅ Created | [docs/abstract-A-to-F.md](abstract-A-to-F.md) §D |
| E — Experiments (planned evaluation protocol) | ✅ Created | [docs/abstract-A-to-F.md](abstract-A-to-F.md) §E |
| F — Findings (expected contributions and limitations) | ✅ Created | [docs/abstract-A-to-F.md](abstract-A-to-F.md) §F |

### Requirement 2: Five Core Concepts (Slide 7)

| Principle | Status | File |
|-----------|--------|------|
| 1. Localization Uncertainty | ✅ Documented | [docs/core-concepts.md](core-concepts.md) §Principle 1 |
| 2. Sensor Limitations | ✅ Documented | [docs/core-concepts.md](core-concepts.md) §Principle 2 |
| 3. Physical Navigation Feasibility | ✅ Documented | [docs/core-concepts.md](core-concepts.md) §Principle 3 |
| 4. Hierarchical Agent and Navigation Control | ✅ Documented | [docs/core-concepts.md](core-concepts.md) §Principle 4 |
| 5. Reproducible Reliability Evaluation | ✅ Documented | [docs/core-concepts.md](core-concepts.md) §Principle 5 |

### Requirement 3: Three Research Trade-offs (Slides 8–10)

| Debate | Status | File |
|--------|--------|------|
| Debate I: Modular vs. End-to-End | ✅ Documented | [docs/research-tradeoffs.md](research-tradeoffs.md) §Debate I |
| Debate II: Map-Based vs. Mapless | ✅ Documented | [docs/research-tradeoffs.md](research-tradeoffs.md) §Debate II |
| Debate III: Safety vs. Efficiency | ✅ Documented | [docs/research-tradeoffs.md](research-tradeoffs.md) §Debate III |

### Requirement 4: Critical Questions Q1–Q10 (Slides 11–12)

| Question | Status | File |
|----------|--------|------|
| Q1 — Odometry divergence when stuck | ✅ Preserved | [docs/critical-questions.md](critical-questions.md) §Q1 |
| Q2 — Localization vs. controller error | ✅ Preserved | [docs/critical-questions.md](critical-questions.md) §Q2 |
| Q3 — LiDAR blind spots and table obstacles | ✅ Preserved | [docs/critical-questions.md](critical-questions.md) §Q3 |
| Q4 — Footprint vs. centre-line passability | ✅ Preserved | [docs/critical-questions.md](critical-questions.md) §Q4 |
| Q5 — New obstacle after global planning | ✅ Preserved | [docs/critical-questions.md](critical-questions.md) §Q5 |
| Q6 — Inflation radius and safety | ✅ Preserved | [docs/critical-questions.md](critical-questions.md) §Q6 |
| Q7 — Comparing methods with different collision rates | ✅ Preserved | [docs/critical-questions.md](critical-questions.md) §Q7 |
| Q8 — Gazebo success, physical robot failure | ✅ Preserved | [docs/critical-questions.md](critical-questions.md) §Q8 |
| Q9 — Proving agent contribution vs. Nav2 alone | ✅ Preserved | [docs/critical-questions.md](critical-questions.md) §Q9 |
| Q10 — Ambiguous destination and mid-task cancellation | ✅ Preserved | [docs/critical-questions.md](critical-questions.md) §Q10 |

---

## 2. OpenSpec Artifact Coverage

### Change Proposal

| Artifact | Status | Path |
|---------|--------|------|
| proposal.md (pre-existing) | ✅ Verified | [proposal.md](../../../proposal.md) |

### Capability Specifications

| Capability | Status | Path |
|-----------|--------|------|
| task-understanding | ✅ Created | [openspec/changes/tb3-proposal-refactor/specs/task-understanding/spec.md](../openspec/changes/tb3-proposal-refactor/specs/task-understanding/spec.md) |
| navigation-validation | ✅ Created | [openspec/changes/tb3-proposal-refactor/specs/navigation-validation/spec.md](../openspec/changes/tb3-proposal-refactor/specs/navigation-validation/spec.md) |
| task-execution | ✅ Created | [openspec/changes/tb3-proposal-refactor/specs/task-execution/spec.md](../openspec/changes/tb3-proposal-refactor/specs/task-execution/spec.md) |
| evidence-reporting | ✅ Created | [openspec/changes/tb3-proposal-refactor/specs/evidence-reporting/spec.md](../openspec/changes/tb3-proposal-refactor/specs/evidence-reporting/spec.md) |
| evaluation | ✅ Created | [openspec/changes/tb3-proposal-refactor/specs/evaluation/spec.md](../openspec/changes/tb3-proposal-refactor/specs/evaluation/spec.md) |

### Design and Task Artifacts

| Artifact | Status | Path |
|---------|--------|------|
| design.md | ✅ Created | [openspec/changes/tb3-proposal-refactor/design.md](../openspec/changes/tb3-proposal-refactor/design.md) |
| tasks.md | ✅ Created | [openspec/changes/tb3-proposal-refactor/tasks.md](../openspec/changes/tb3-proposal-refactor/tasks.md) |

---

## 3. Academic Writing Coverage

| Document | Status | Path |
|---------|--------|------|
| IEEE-style abstract | ✅ Created | [docs/abstract-ieee.md](abstract-ieee.md) |
| Introduction (motivation, gap, RQ1–RQ3, contributions) | ✅ Created | [docs/introduction.md](introduction.md) |
| Related Work (LLM, Nav2, reliability, debates, gap) | ✅ Created | [docs/related-work.md](related-work.md) |

---

## 4. Experiment Protocol Preservation

| Experiment Element | Status | Source Slide | Documented In |
|-------------------|--------|-------------|--------------|
| B0: Fixed Menu + Nav2 | ✅ Preserved | Slide 17 | specs/evaluation, design.md, abstract-A-to-F.md |
| B1: LLM + Schema + Allowlist | ✅ Preserved | Slide 17 | specs/evaluation, design.md |
| B2: Full Agent | ✅ Preserved | Slide 17 | specs/evaluation, design.md |
| 60 language tasks (20 dev / 40 test) | ✅ Preserved | Slide 18, A09 | specs/evaluation |
| 40 simulation runs per method (4×2×5) | ✅ Preserved | Slide 18, A10 | specs/evaluation |
| 12 real-robot cases per method (36 pairs) | ✅ Preserved | Slide 18, A10 | specs/evaluation |
| Ablation: B2 − Clarification | ✅ Preserved | Slide 17, A11 | specs/evaluation, design.md |
| Ablation: B2 − Agent Pre-check | ✅ Preserved | Slide 17, A11 | specs/evaluation, design.md |
| "Planned only" label on all experiment outcomes | ✅ Verified | Slide 18 note | All experiment docs |

---

## 5. Spec Format Validation

### Spec Format Checklist (all five specs)

| Check | task-understanding | navigation-validation | task-execution | evidence-reporting | evaluation |
|-------|-------------------|----------------------|---------------|-------------------|-----------|
| `## Purpose` ≥ 50 chars | ✅ | ✅ | ✅ | ✅ | ✅ |
| `## ADDED Requirements` present | ✅ | ✅ | ✅ | ✅ | ✅ |
| `### Requirement:` headers | ✅ | ✅ | ✅ | ✅ | ✅ |
| SHALL/MUST normative language | ✅ | ✅ | ✅ | ✅ | ✅ |
| `#### Scenario:` headers | ✅ | ✅ | ✅ | ✅ | ✅ |
| WHEN in every scenario | ✅ | ✅ | ✅ | ✅ | ✅ |
| THEN in every scenario | ✅ | ✅ | ✅ | ✅ | ✅ |
| Observable behavior (not impl. details) | ✅ | ✅ | ✅ | ✅ | ✅ |

---

## 6. Validation Results

### CLI Validation

The OpenSpec 2.0.0 CLI strictly validated the change with zero warnings:

```bash
$ export PATH="$HOME/.local/bin:$PATH"
$ openspec validate tb3-proposal-refactor --strict
Change 'tb3-proposal-refactor' is valid

$ openspec status --change tb3-proposal-refactor
- Loading change status...
Change: tb3-proposal-refactor
Schema: spec-driven
Change root: /home/augustine/DIC3-TB3-ProjectRefactor/openspec/changes/tb3-proposal-refactor
Progress: 4/4 artifacts complete

[x] proposal
[x] specs
[x] design
[x] tasks

All planning artifacts complete!
```

### Manual Validation Performed

- All five spec files verified for format compliance (see table above).
- proposal.md verified for all six change sections, five capabilities, and research boundaries.
- design.md verified for all eight modules, navigation adapter interface, algorithm, and design decisions.
- tasks.md verified for all phases and verification items.

---

## 7. Files Created (Complete List)

| File | Description |
|------|-------------|
| `docs/abstract-A-to-F.md` | A–F research structure |
| `docs/abstract-ieee.md` | IEEE-style abstract |
| `docs/introduction.md` | Academic introduction with RQ1–RQ3 |
| `docs/related-work.md` | Literature review (refs [1]–[12]) |
| `docs/core-concepts.md` | Five design principles (Slide 7) |
| `docs/research-tradeoffs.md` | Three research debates (Slides 8–10) |
| `docs/critical-questions.md` | Q1–Q10 (Slides 11–12) |
| `docs/refactor-report.md` | This document |
| `openspec/changes/tb3-proposal-refactor/design.md` | System design document |
| `openspec/changes/tb3-proposal-refactor/tasks.md` | Implementation/verification checklist |
| `openspec/changes/tb3-proposal-refactor/specs/task-understanding/spec.md` | task-understanding capability spec |
| `openspec/changes/tb3-proposal-refactor/specs/navigation-validation/spec.md` | navigation-validation capability spec |
| `openspec/changes/tb3-proposal-refactor/specs/task-execution/spec.md` | task-execution capability spec |
| `openspec/changes/tb3-proposal-refactor/specs/evidence-reporting/spec.md` | evidence-reporting capability spec |
| `openspec/changes/tb3-proposal-refactor/specs/evaluation/spec.md` | evaluation capability spec |
| `README.md` | Updated project README |

**Files preserved (not modified)**:
- `proposal.md` (pre-existing, verified)
- `references/TB3_Proposal_Final_35slides.pptx` (source material, not modified)
- `openspec/config.yaml` (not modified)
- `openspec/changes/tb3-proposal-refactor/.openspec.yaml` (not modified)

---

## 8. Remaining Issues and Open Items

| Issue | Priority | Note |
|-------|----------|------|
| LLM provider not selected | Low | Planned measurement; will be recorded in experiment logs. See Section 11.1 in design.md. |
| Nav2 Humble API verification | Medium | Navigation Adapter function names (`get_path`, `navigate_to_landmark`) are conceptual; must be mapped to actual ROS 2 Humble + Nav2 action server interfaces during implementation. |
| Landmark registry content | Low | `landmarks.json` content (actual lab destinations) is not yet defined; placeholder structure documented in design.md. |
| Navigation parameters | Low | Footprint, inflation radius, velocity limits require empirical verification in the lab environment before experiments. |
| Real-robot repeat count | Low | Slide 31 notes: "repeat number pending" for the 12 real-robot cases per method. |

---

## 9. Suggested Commit Commands

```bash
# Review all created files
git status
git diff HEAD

# Stage all new and modified files
git add docs/
git add openspec/changes/tb3-proposal-refactor/
git add README.md

# Verify what will be committed
git diff --staged --stat

# Commit (do not push automatically)
git commit -m "docs(tb3-proposal-refactor): complete OpenSpec spec-driven refactor

Tasks completed:
- Task 1: Research structure (abstract A-F, core concepts, tradeoffs, Q1-Q10)
- Task 2: OpenSpec artifacts (proposal, 5 specs, design, tasks)
- Task 3: Academic writing (IEEE abstract, introduction, related work)
- Task 4: Experiment protocol preserved (B0/B1/B2, 60/40/12 cases, ablations)
- Task 5: README updated, refactor report created

Source: TB3_Proposal_Final_35slides.pptx (35 slides)
Platform: TurtleBot3 Burger, Ubuntu 22.04, ROS 2 Humble, Nav2
No robot control code modified. No experiments conducted.
No git push executed (manual step)."

# Review log before any push
git log --oneline -5
```
