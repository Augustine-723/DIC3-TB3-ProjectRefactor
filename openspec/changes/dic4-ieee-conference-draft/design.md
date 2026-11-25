# Design: DIC4 IEEE Conference Paper Draft

## Context

This design outlines the technical approach for generating the publication-ready IEEE conference draft for the International Conference on Electronic Communications, Internet of Things and Big Data (ICEIB), titled *"A Validation-Aware and Evidence-Grounded AI Agent for Indoor Navigation Using TurtleBot3"*.

The work directly extends the DIC3 refactor (`tb3-proposal-refactor`), transforming the structured research specifications and proposal materials into a full academic paper in two-column IEEE conference format (`IEEEtran`), complete with plain text counterpart, vector figures, bibliography, and Overleaf packaging.

See `proposal.md` for the motivation and change scope.

## Goals / Non-Goals

**Goals:**
- Provide a rigorous, academic 4–8 page conference paper draft in `ICEIB/main.tex` and `ICEIB/main.txt`.
- Fully preserve and articulate the 8-module 3-layer architecture, 5 core design principles, 3 research debates, and solutions to Q1–Q10.
- Present the planned evaluation protocol (B0/B1/B2, ablations, 60 language tasks, 40 simulation runs, 12 paired real-robot trials, and RQ1–RQ3 metrics).
- Generate publication-grade TikZ/vector diagrams for Figures 1 and 2, and formal IEEE Tables I through IV.
- Package `ICEIB/overleaf-upload.zip` with complete LaTeX assets and provide precise import instructions.
- Ensure strict academic integrity: all experimental results are explicitly described as planned evaluation protocols.

**Non-Goals:**
- Fabricating numerical results, simulated metrics, or physical test outcomes.
- Modifying robot control code, ROS 2 Humble launch files, or Nav2 packages.
- Executing physical robot navigation.
- Archiving or modifying the existing `tb3-proposal-refactor` change.

## Decisions

### 1. Document Class and Formatting
- **Choice**: Standard `IEEEtran` document class (`\documentclass[conference]{IEEEtran}`) with A4/Letter sizing.
- **Rationale**: Official standard for IEEE conference proceedings, perfectly matching ICEIB guidelines.
- **Alternatives Considered**: Generic article class or MDPI template; rejected because IEEEtrack submissions to ICEIB require IEEE format.

### 2. Dual Deliverable: main.tex and main.txt
- **Choice**: Create both `ICEIB/main.tex` (compilable LaTeX) and `ICEIB/main.txt` (full text manuscript).
- **Rationale**: Specifically requested by course instructors to ensure immediate readability without compiling, while `main.tex` serves Overleaf.
- **Alternatives Considered**: Only `main.tex`; rejected because course submission explicitly requires `main.txt`.

### 3. Vector Figures via TikZ / PGF
- **Choice**: Implement Figure 1 (Architecture) and Figure 2 (Workflow) using native LaTeX TikZ vector commands and exportable standalone scripts.
- **Rationale**: TikZ graphics scale infinitely without pixelation, compile directly in LaTeX, and fit cleanly within single/double column widths.
- **Alternatives Considered**: Bitmap screenshots or PPTX exports; rejected due to resolution loss and academic quality standards.

### 4. Honest Academic Labeling of Results
- **Choice**: Section VI is titled *"Proposed Evaluation and Expected Outcomes"* and contains planned analysis frameworks rather than synthetic data.
- **Rationale**: Research integrity is paramount; the physical experiments are planned for future execution, not yet completed.
- **Alternatives Considered**: Simulating or estimating mock numbers; rejected as academic fabrication.

### 5. Overleaf Integration Strategy
- **Choice**: Produce a standalone `overleaf-upload.zip` containing `main.tex`, `references.bib`, `IEEEtran.cls`, and all figure source/assets, with comprehensive import documentation.
- **Rationale**: Since Overleaf MCP is not authenticated on this sandbox machine, packaging the zip ensures 100% turnkey Overleaf usability.
- **Alternatives Considered**: Mocking an Overleaf project creation; rejected as untruthful.

## Risks / Trade-offs

- **[Risk] LaTeX compiler absence in local environment** → *Mitigation*: Deliver fully verified, standard LaTeX syntax, bundle official `IEEEtran.cls` and TikZ libraries, package into zip, and document Overleaf cloud compilation.
- **[Risk] Two-column overflow for wide tables or complex TikZ diagrams** → *Mitigation*: Utilize `figure*` or `table*` environments for span-across-two-columns or tune node coordinates carefully to single column (3.5 in) width.
- **[Risk] Sync discrepancy between main.tex and main.txt** → *Mitigation*: Generate `main.txt` directly alongside `main.tex` to maintain 1:1 section and argument parity.

## Migration and Verification Plan

1. Create directory `ICEIB/` and subdirectories `ICEIB/figures/`.
2. Draft `ICEIB/references.bib` with verified BibTeX entries for [1]–[12].
3. Draft `ICEIB/main.tex` and `ICEIB/main.txt`.
4. Generate TikZ diagram source codes and figures.
5. Create `ICEIB/overleaf-upload.zip` bundling all LaTeX dependencies.
6. Run OpenSpec validation and update root `README.md`.
7. Verify git changes and push to GitHub.
