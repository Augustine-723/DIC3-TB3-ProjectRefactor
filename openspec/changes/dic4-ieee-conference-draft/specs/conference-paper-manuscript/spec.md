# Spec Delta

## Purpose

Defines the required academic structure, section composition, author attribution, and research integrity constraints for the IEEE conference draft manuscript.

## ADDED Requirements

### Requirement: Academic Paper Section Structure
The manuscript SHALL contain all standard IEEE conference sections: Title and Author Information, Abstract, Index Terms, Section I Introduction, Section II Related Work, Section III Proposed System Architecture, Section IV Methodology and Task Workflow, Section V Experimental Design, Section VI Proposed Evaluation and Expected Outcomes, Section VII Conclusion and Future Work, and References.

#### Scenario: All required sections are present
- **WHEN** the manuscript text or LaTeX source is parsed
- **THEN** every required section header from Abstract through References SHALL be present in sequential order.

### Requirement: Faithful Author Representation
The author metadata SHALL include all five actual group members from the original proposal (Zhi-Heng Chen 7115064009, Cheng-Hao Hsiao 7115064186, Rui-Yu Lin 7115064168, Yuan-Mei Chiu 7115064159, and Yi-Kai Guan 7115093001) without fabricated institutional affiliations, artificial emails, or unauthorized corresponding-author assertions.

#### Scenario: Authors match proposal roster
- **WHEN** inspecting the author block of the manuscript
- **THEN** exactly the five group members SHALL be listed with student identifiers and no fabricated external affiliations.

### Requirement: Academic English and IEEE Citation Style
The paper draft SHALL be composed in academic English adhering to IEEE conference writing standards, utilizing IEEE-style bracketed numeric citations referencing the verified bibliography entries.

#### Scenario: Citations follow IEEE bracketed format
- **WHEN** in-text literature references are evaluated
- **THEN** citations SHALL appear as numbered brackets corresponding directly to the numbered references list.

### Requirement: Research Integrity and Planned Outcomes
All empirical evaluations, metrics, and experimental discussions SHALL be explicitly designated as planned evaluation protocols, and the manuscript SHALL NOT report fabricated measurements, fictitious data points, or unverified physical trials.

#### Scenario: Section VI designates outcomes as planned
- **WHEN** evaluating Section VI of the manuscript
- **THEN** the section title SHALL be Proposed Evaluation and Expected Outcomes and all quantitative tables SHALL contain planned analysis structures rather than fabricated empirical measurements.
