# Spec Delta

## Purpose

Defines the traceability and preservation requirements connecting the DIC4 conference paper directly to the established DIC3 research artifacts and decisions.

## ADDED Requirements

### Requirement: Preservation of Core Principles and Trade-offs
The conference paper SHALL explicitly incorporate the five core robotics design principles (Localization Uncertainty, Sensor Limitations, Physical Navigation Feasibility, Hierarchical Control, Reproducible Reliability) and the three research debates (Modular vs. End-to-End, Map-Based vs. Mapless, Safety vs. Efficiency).

#### Scenario: Core principles reflected in design
- **WHEN** reviewing the architecture and methodology sections
- **THEN** each of the five design principles and three trade-offs SHALL be referenced with its architectural rationale.

### Requirement: Preservation of Critical Questions Analysis
The manuscript SHALL address the technical challenges identified in Q1 through Q10 from the original proposal, including odometry divergence during stall, 2D LiDAR blind spots, footprint inflation limits, and mid-mission cancellation.

#### Scenario: Technical challenges addressed in text
- **WHEN** analyzing the failure modes and safety validation discussions
- **THEN** the solutions to Q1 through Q10 SHALL be integrated into the system design and evaluation narrative.

### Requirement: Preservation of Experimental Baselines and Protocol
The experimental design SHALL preserve the exact baseline conditions (B0 Fixed Menu, B1 Language, B2 Full Agent), ablations (B2 minus Clarification, B2 minus Agent Pre-check), 60 Chinese language tasks (20 dev / 40 test), 40 simulation runs per method, and 12 paired real-robot trials.

#### Scenario: Baseline and protocol alignment
- **WHEN** checking the experimental design specification
- **THEN** the evaluation conditions and trial counts SHALL match the DIC3 protocol with zero discrepancy.
