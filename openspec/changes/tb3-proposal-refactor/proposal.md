
# Change Proposal: TB3 Project Proposal Refactor

## Why

The existing TurtleBot3 Burger project proposal presents
an AI agent for indoor navigation and task decision-making
using ROS 2 Humble and Nav2.

However, its research motivation, architectural decisions,
technical requirements, and evaluation methodology are
currently distributed across presentation slides.

This change refactors the existing proposal into a
structured, traceable, and verifiable research specification
using OpenSpec and Spec-Driven Development.

The goal is to improve research clarity, documentation
consistency, and implementation readiness without
changing the established project scope.

## What Changes

### 1. A–F Research Abstract

Reorganize the research narrative into six sections:

- A. Attention Getter: Research background and motivation
- B. But: Limitations and research challenges
- C. Cure: Proposed validation-aware AI agent
- D. Development: System architecture and methodology
- E. Experiments: Baselines and evaluation protocol
- F. Findings: Expected contributions and limitations

### 2. Five Core Concepts

Document five established design principles:

1. Localization Uncertainty
2. Sensor Limitations
3. Physical Navigation Feasibility
4. Hierarchical Agent and Navigation Control
5. Reproducible Reliability Evaluation

Each concept will be linked to system requirements
and verification criteria.

### 3. Three Research Trade-offs

Document the three research debates:

1. Modular vs. End-to-End Navigation
2. Map-Based vs. Mapless Navigation
3. Safety vs. Efficiency

For each trade-off, identify competing perspectives,
supporting evidence, the selected approach, and
remaining limitations.

### 4. Ten Critical Technical Questions

Preserve the original Q1–Q10 covering:

- Odometry and localization errors
- LiDAR limitations
- Robot footprint and narrow passages
- Dynamic obstacle handling
- Navigation parameter trade-offs
- Reliability and safety evaluation
- Simulation-to-real transfer
- Agent contribution and ablation
- Ambiguity handling and task cancellation

### 5. OpenSpec Workflow

Convert the existing system design into:

- proposal.md: Motivation and change scope
- specs/: Testable system requirements
- design.md: Architecture and design decisions
- tasks.md: Implementation and verification checklist

### 6. Academic Writing Refactor

Refactor the following research sections:

- IEEE-style Abstract
- Introduction
- Related Work
- Research Gap and Contributions

Preserve the established literature and distinguish
source-supported claims from proposed contributions.

## Capabilities

### New Capabilities

- `task-understanding`: Chinese task parsing,
  registered landmarks, and clarification.
- `navigation-validation`: Robot state, sensor
  freshness, localization, and path feasibility.
- `task-execution`: Validated Nav2 goal dispatch,
  monitoring, cancellation, and exception handling.
- `evidence-reporting`: Evidence-grounded mission
  status and execution logs.
- `evaluation`: Baseline comparisons, ablations,
  reliability metrics, and latency measurement.

### Modified Capabilities

No existing implemented capabilities are being changed
in this documentation-only refactor.

## Impact

### Research Documentation

- A–F abstract and structured research narrative
- Five core concepts and three trade-off analyses
- Ten critical technical questions
- Revised Introduction and Related Work

### OpenSpec Artifacts

- Change proposal
- Requirements specifications
- System design document
- Implementation and verification tasks

### Preserved Project Scope

- Physical TurtleBot3 Burger
- Ubuntu 22.04 and ROS 2 Humble
- Nav2 navigation framework
- Single indoor floor and pre-built map
- Registered landmark destinations
- B0, B1, and B2 baseline comparison

### Preserved Evaluation Plan

- 60 Chinese-language task cases
- 20 development and 40 test cases
- 40 simulation runs per method
- 12 paired real-robot cases per method
- Clarification and pre-check ablation studies

### Research Boundaries

This change does not claim completed robot experiments,
measured improvements, or validated implementation
results.

All experimental outcomes remain planned until
supported by recorded evidence.
