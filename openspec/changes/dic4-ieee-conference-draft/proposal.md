# Proposal: DIC4 IEEE Conference Draft

## Why

This change translates the established TurtleBot3 indoor navigation AI agent research proposal (from DIC3) into a publication-ready IEEE conference manuscript targeting the International Conference on Electronic Communications, Internet of Things and Big Data (ICEIB). It bridges the academic research specifications into a formal conference paper draft, complete with compilable LaTeX sources, plain text manuscript, vector figures, IEEE bibliography, Overleaf packaging, and GitHub delivery, while maintaining strict academic integrity by treating all experimental outcomes as planned.

## What Changes

### 1. Conference Paper Manuscript Structure and Content
- Create `ICEIB/main.tex` as a compilable LaTeX entry point using the standard `IEEEtran` conference class.
- Create `ICEIB/main.txt` containing the full conference paper text in academic English with IEEE-style numbered citations.
- Include all required sections: Title & Authors, Abstract (≤ 250 words), Index Terms, I. Introduction, II. Related Work, III. Proposed System Architecture, IV. Methodology and Task Workflow, V. Experimental Design, VI. Proposed Evaluation and Expected Outcomes, VII. Conclusion and Future Work, and References.
- Faithfully preserve group members (陳至恆 7115064009, 蕭丞皓 7115064186, 林瑞裕 7115064168, 邱原美 7115064159, 官奕愷 7115093001) without fabricated affiliations or emails.
- Emphasize academic integrity by labeling all experimental outcomes as planned, without fabricated data or figures.

### 2. Publication-Quality Figures and Tables
- Create vector-quality diagrams: Figure 1 (Overall System Architecture) and Figure 2 (Validation–Dispatch–Monitoring Workflow).
- Create formal IEEE tables: Table I (Related Work Comparison), Table II (B0/B1/B2 Capability Comparison), Table III (Planned Experiment Protocol), and Table IV (Evaluation Metrics).
- Reference and analyze every figure and table in the paper body.

### 3. Bibliography and Class Assets
- Create `ICEIB/references.bib` containing verified bibliographic entries for refs [1]–[12].
- Provide `IEEEtran.cls` to ensure self-contained, reproducible compilation across environments.

### 4. Overleaf Packaging and Sync
- Package all LaTeX sources, figures, bibliography, and style files into `ICEIB/overleaf-upload.zip`.
- Provide exact Overleaf project import instructions and report authenticated MCP status honestly.

### 5. Repository Documentation and Delivery
- Update root `README.md` to document the DIC4 IEEE paper workflow, artifact structure, and Overleaf integration.
- Ensure all DIC3 artifacts and robot control boundaries remain intact.

## Capabilities

### New Capabilities
- `conference-paper-manuscript`: Defines the academic structure, required sections, content elements, and research integrity constraints for the IEEE conference draft.
- `source-traceability`: Defines the mapping and faithful preservation of DIC3 research artifacts (5 core concepts, 3 trade-offs, 10 critical questions, RQ1–RQ3, and B0/B1/B2 baseline protocols).
- `publication-formatting-build`: Defines the IEEE conference formatting rules (`IEEEtran`), compilable LaTeX entry point, plain-text manuscript requirement, vector diagrams, and bibliography validation.
- `release-delivery`: Defines Overleaf packaging, GitHub repository documentation, commit discipline, and non-modification of physical robot control code.

### Modified Capabilities
No existing implemented capabilities are modified. Existing DIC3 specifications remain preserved.

## Impact

- Creates `ICEIB/` directory with `main.tex`, `main.txt`, `references.bib`, `figures/`, `README.md`, and `overleaf-upload.zip`.
- Updates root `README.md` to reference DIC4 deliverables.
- Preserves all DIC3 documents in `docs/` and OpenSpec change `tb3-proposal-refactor`.
- Zero changes to physical robot code or ROS 2 packages.
