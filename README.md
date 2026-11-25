# DIC3-TB3-ProjectRefactor

**AI Agent for Indoor Navigation and Task Decision-Making**
TurtleBot3 Burger · Ubuntu 22.04 · ROS 2 Humble · Nav2

> **Status**: Research proposal and documentation refactor. No robot experiments have been conducted. All experimental outcomes are planned.

---

## Project Goals

This project designs and proposes a **validation-aware AI agent** that enables a physical TurtleBot3 Burger robot to:

1. Accept Chinese-language navigation commands from a user.
2. Parse intent using an LLM constrained by a JSON schema and landmark allowlist.
3. Validate robot state (sensor freshness, localization, path feasibility) before dispatching any navigation goal.
4. Execute validated navigation goals via Nav2, monitor outcomes, and handle cancellation and timeouts.
5. Report mission results grounded in Nav2 action outcomes and an event log.

---

## Research Contributions (Expected)

Three research questions drive the evaluation:

| RQ | Question | Metric |
|----|----------|--------|
| **RQ1** | Does task clarification + state validation reduce improper dispatch? | Improper dispatch rate; task handling accuracy |
| **RQ2** | Does evidence-grounded reporting improve outcome consistency? | Reporting accuracy |
| **RQ3** | What additional interaction latency does the full agent introduce? | T0→T1, T1→T2, T2→T3 latency (p50/p95) |

**Baseline conditions** (all share identical Nav2 infrastructure):
- **B0**: Fixed menu + Nav2 (no language input)
- **B1**: LLM + Schema + Allowlist (no clarification or pre-check)
- **B2**: Full agent (B1 + clarification + pre-check + evidence reporting)

**Ablations**: B2 − Clarification; B2 − Agent Pre-check

---

## DIC4 — IEEE Conference Paper Draft (ICEIB)

This repository extends the DIC3 refactor with a complete academic conference draft targeting the **International Conference on Electronic Communications, Internet of Things and Big Data (ICEIB)** (IEEE Conference Track).

- **Paper Title**: *A Validation-Aware and Evidence-Grounded AI Agent for Indoor Navigation Using TurtleBot3*
- **Authors**: Zhi-Heng Chen (7115064009), Cheng-Hao Hsiao (7115064186), Rui-Yu Lin (7115064168), Yuan-Mei Chiu (7115064159), and Yi-Kai Guan (7115093001)
- **Affiliation**: Group 7, Department of Information and Communication Engineering, National Taiwan University of Science and Technology
- **Draft Files**:
  - `ICEIB/main.tex`: Compilable LaTeX source using official `IEEEtran` conference class.
  - `ICEIB/main.txt`: Full, unabridged academic manuscript text.
  - `ICEIB/references.bib`: Verified IEEE BibTeX bibliography covering references [1]–[12].
  - `ICEIB/IEEEtran.cls`: Official IEEE document class (v1.8b).
  - `ICEIB/figures/`: Vector TikZ source files for Figure 1 (Architecture) and Figure 2 (Workflow).
  - `ICEIB/overleaf-upload.zip`: Complete self-contained package for immediate Overleaf import.
  - `ICEIB/README.md`: Compilation and Overleaf import instructions.
- **OpenSpec Change**: `openspec/changes/dic4-ieee-conference-draft/`

### Overleaf Import Workflow
1. Download `ICEIB/overleaf-upload.zip`.
2. Go to [Overleaf](https://www.overleaf.com), select **New Project** → **Upload Project**, and upload the ZIP.
3. Confirm that `main.tex` is selected as the main file, and click **Recompile**.

---

## OpenSpec Workflow

This project uses [OpenSpec](https://openspec.dev) Spec-Driven Development. Two active changes document the full research specification:
1. `tb3-proposal-refactor`: DIC3 research specification refactor.
2. `dic4-ieee-conference-draft`: DIC4 IEEE conference draft specification.

### Artifact Dependency Order

```
proposal.md   →   specs/   →   design.md   →   tasks.md
```

### Checking Status

```bash
# Check DIC3 change
openspec status --change tb3-proposal-refactor

# Check DIC4 change
openspec status --change dic4-ieee-conference-draft
```

### Validating Specs

```bash
# Strict validation of DIC4 change
openspec validate dic4-ieee-conference-draft --strict
```

---

## Directory Structure

```
DIC3-TB3-ProjectRefactor/
├── README.md                          ← This file
├── proposal.md                        ← OpenSpec change proposal (root)
│
├── ICEIB/                             ← DIC4 IEEE Conference Package
│   ├── main.tex                       ← Compilable Overleaf LaTeX entry point
│   ├── main.txt                       ← Complete plain text manuscript
│   ├── references.bib                 ← BibTeX bibliography (refs [1]–[12])
│   ├── IEEEtran.cls                   ← Official IEEE LaTeX conference class
│   ├── figures/                       ← Vector graphics and TikZ diagrams
│   │   ├── fig1_architecture.tex      ← Figure 1: 3-Layer, 8-Module Architecture
│   │   └── fig2_workflow.tex          ← Figure 2: Validation-Dispatch Workflow
│   ├── overleaf-upload.zip            ← Self-contained Overleaf upload package
│   └── README.md                      ← Overleaf import and compilation instructions
│
├── openspec/
│   ├── config.yaml                    ← OpenSpec project configuration
│   ├── specs/                         ← Main specs
│   └── changes/
│       ├── tb3-proposal-refactor/     ← DIC3 change artifacts (preserved)
│       │   ├── .openspec.yaml
│       │   ├── design.md
│       │   ├── tasks.md
│       │   └── specs/
│       └── dic4-ieee-conference-draft/ ← DIC4 change artifacts
│           ├── .openspec.yaml
│           ├── proposal.md
│           ├── design.md
│           ├── tasks.md
│           └── specs/
│               ├── conference-paper-manuscript/spec.md
│               ├── source-traceability/spec.md
│               ├── publication-formatting-build/spec.md
│               └── release-delivery/spec.md
│
├── docs/                              ← DIC3 Research Documentation
│   ├── abstract-A-to-F.md             ← A–F research structure
│   ├── abstract-ieee.md               ← IEEE-style abstract
│   ├── introduction.md                ← Academic introduction with RQ1–RQ3
│   ├── related-work.md                ← Literature review (refs [1]–[12])
│   ├── core-concepts.md               ← Five design principles (Slide 7)
│   ├── research-tradeoffs.md          ← Three research debates (Slides 8–10)
│   ├── critical-questions.md          ← Q1–Q10 technical questions (Slides 11–12)
│   └── refactor-report.md             ← DIC3 requirements-to-files mapping
│
└── references/
    └── TB3_Proposal_Final_35slides.pptx  ← Source material (do not modify)
```

---

## Validation Instructions

### Format Validation (Manual)

For each spec file in `openspec/changes/tb3-proposal-refactor/specs/*/spec.md`, verify:

- [ ] `## Purpose` section present and ≥ 50 characters
- [ ] `## ADDED Requirements` section present
- [ ] All requirements use `### Requirement:` headers
- [ ] Normative language: SHALL/MUST used consistently
- [ ] All scenarios use `#### Scenario:` headers
- [ ] Every scenario contains WHEN and THEN clauses
- [ ] Scenarios describe observable behavior, not implementation details

### OpenSpec CLI Validation

```bash
# Check change status
openspec status --change tb3-proposal-refactor

# Validate all specs
openspec validate --change tb3-proposal-refactor

# Validate with strict mode (if supported)
openspec validate --change tb3-proposal-refactor --strict
```

### Content Validation Checklist

- [ ] No spec claims completed experimental results
- [ ] All experiment figures are labeled as "planned"
- [ ] Evidence boundaries are stated for all literature claims
- [ ] Reference numbering [1]–[12] is consistent across all docs
- [ ] No robot control code has been modified
- [ ] `references/TB3_Proposal_Final_35slides.pptx` is unmodified

---

## Platform

| Component | Version |
|-----------|---------|
| Robot | TurtleBot3 Burger (physical) |
| OS | Ubuntu 22.04 LTS |
| Middleware | ROS 2 Humble |
| Navigation | Nav2 |
| Sensor | 2D LiDAR |
| Map | Pre-built 2D occupancy grid |

---

## Scope Boundaries

- ✅ Research documentation and OpenSpec specifications
- ✅ Literature review and academic writing
- ✅ Evaluation protocol design
- ❌ Robot control code (not modified)
- ❌ Physical robot deployment or execution
- ❌ Completed experimental results (none exist yet)
- ❌ GitHub push (manual step, review first)

---

## Suggested Commit

```bash
git add docs/ openspec/changes/tb3-proposal-refactor/ README.md
git commit -m "docs(tb3-proposal-refactor): complete OpenSpec spec-driven refactor

Source: TB3_Proposal_Final_35slides.pptx (35 slides)
Platform: TurtleBot3 Burger, Ubuntu 22.04, ROS 2 Humble, Nav2
No robot control code modified. No experiments conducted."
```
