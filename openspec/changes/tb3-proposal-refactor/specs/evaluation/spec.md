## Purpose

The `evaluation` capability defines the complete planned evaluation protocol for the TB3 AI agent project, including the three baseline conditions (B0, B1, B2), the two ablation variants, the language evaluation task set, the simulation and real-robot trial protocol, the operational definitions of all metrics, and the statistical reporting requirements. This specification preserves the experimental design decisions from the original proposal and distinguishes planned procedures from completed results.

## ADDED Requirements

### Requirement: Baseline Conditions

The evaluation framework SHALL evaluate navigation performance across three baseline configurations (B0 Fixed Menu, B1 Language, B2 Full Agent) sharing identical Nav2 navigation parameters, map definitions, and registered landmarks.

#### Scenario: Baseline feature composition

WHEN configuring the baseline methods,  
THEN B0 SHALL provide Nav2 navigation without language input, B1 SHALL add Chinese LLM parsing and schema allowlists, and B2 SHALL additionally include clarification, agent pre-checks, and evidence reporting.

#### Scenario: Baseline method isolation is maintained

WHEN results for B0, B1, and B2 are collected,  
THEN the navigation infrastructure (map, Nav2 version, parameter set, goal definitions) SHALL be identical across all three methods, and any performance difference SHALL be attributable to agent-level features.

#### Scenario: Language metrics applied only where applicable

WHEN language-dependent metrics (e.g., Chinese parsing accuracy, clarification rate) are computed,  
THEN these metrics SHALL be marked N/A for B0 (which has no language input) and SHALL NOT be reported as zero for B0.

### Requirement: Ablation Conditions

The evaluation framework SHALL evaluate two ablation variants of the B2 agent (B2 minus Clarification and B2 minus Agent Pre-check) while retaining underlying Nav2 safety mechanisms.

#### Scenario: Ablation results are compared to B2

WHEN ablation results are reported,  
THEN the report SHALL compare each ablation to B2 on the relevant metric (e.g., improper dispatch rate for B2 − Clarification, NOT_READY avoidance rate for B2 − Agent Pre-check).

### Requirement: Language Evaluation Task Set

The evaluation suite SHALL evaluate language understanding across 60 Chinese-language task cases partitioned into 20 development cases and 40 test cases across six standard command categories.

#### Scenario: Task set composition and category coverage

WHEN the language evaluation task suite is executed,  
THEN it SHALL contain 10 explicit commands (parse registered landmark), 10 ambiguous commands (CLARIFY), 10 unknown locations (REJECTED), 10 cancellation commands (cancel and confirm), 10 compound commands (enforce single-task constraint), and 10 out-of-scope commands (reject disallowed operation).

#### Scenario: Task set split is maintained

WHEN development cases are used for system tuning,  
THEN the 40 test cases SHALL NOT be used in development, and results on test cases SHALL be reported separately from development-set performance.

### Requirement: Simulation Evaluation Protocol

The evaluation suite SHALL execute 40 simulation runs per baseline method in Gazebo under identical map, route, and Nav2 parameter configurations.

#### Scenario: Simulation trial logging

WHEN a simulation trial is conducted,  
THEN the system SHALL log the trial with: case ID, method (B0/B1/B2), environment ID, route ID, repetition number, Nav2 configuration version, timestamps, and terminal result.

### Requirement: Real-Robot Evaluation Protocol

The evaluation protocol SHALL execute 12 paired navigation trials per baseline method on the physical TurtleBot3 Burger under identical map and goal configurations without intentional collisions.

#### Scenario: Real-robot trial logging

WHEN a real-robot trial is conducted,  
THEN the system SHALL log the trial with: case ID, method, repetition number, platform configuration (Nav2 version, map version, software versions), timestamps, and terminal result.

#### Scenario: Simulation and real-robot results reported separately

WHEN evaluation results are compiled,  
THEN simulation results and real-robot results SHALL be reported in separate tables and SHALL NOT be aggregated into a single performance figure.

### Requirement: Metric Operational Definitions

The evaluation framework SHALL compute task handling accuracy, improper dispatch rate, reporting accuracy, navigation success rate, and segmented latencies using operational definitions grounded in logged trial events.

#### Scenario: Accuracy and reliability metric computation

WHEN computing benchmark performance metrics,  
THEN Task Handling Accuracy SHALL be computed as correctly handled tasks over applicable tasks, Improper Dispatch Rate as invalid tasks dispatched over invalid cases, Reporting Accuracy as evidence-consistent reports over evaluable reports, and Navigation Success Rate as arrived outcomes over valid navigation trials.

#### Scenario: Latency measured per trial

WHEN a trial is logged,  
THEN the event log SHALL record timestamps at T0 (command received), T1 (intent parsed), T2 (goal accepted), and T3 (terminal result), permitting T0→T1, T1→T2, and T2→T3 latency segments to be computed per trial.

### Requirement: Statistical Reporting

All proportional benchmark metrics SHALL be reported with trial sample size n and Wilson 95% confidence intervals, and latencies with median and 95th percentiles.

#### Scenario: Zero observed collisions reported correctly

WHEN zero collision events are observed in n real-robot trials,  
THEN the report SHALL state "0/n collisions observed (Wilson 95% CI: 0% – x%)" rather than claiming zero collision probability.

#### Scenario: Insufficient sample size disclosed

WHEN any metric is computed on a sample smaller than 10,  
THEN the report SHALL note the small sample size and qualify any conclusions drawn from the metric.

### Requirement: Failure Preservation

All evaluation trial outcomes including failures, cancellations, timeouts, and unconfirmed states SHALL be preserved and reported in benchmark results.

#### Scenario: Failed trial recorded and reported

WHEN a trial results in any non-ARRIVED terminal state,  
THEN the trial SHALL be recorded in the event log with the terminal state and reason, and SHALL appear in the results table under the appropriate failure category.
