# Tasks

## 1. Directory and Asset Scaffolding

- [x] 1.1 Create `ICEIB/` and `ICEIB/figures/` directories and verify paths exist.
- [x] 1.2 Create `ICEIB/references.bib` with verified BibTeX entries for [1]–[12] and verify syntax.
- [x] 1.3 Bundle official `IEEEtran.cls` into `ICEIB/` for standalone compilation.

## 2. Publication Figures and Tables

- [x] 2.1 Implement Figure 1 (Overall System Architecture) as vector TikZ/diagram and verify legibility.
- [x] 2.2 Implement Figure 2 (Validation–Dispatch–Monitoring Workflow) as vector TikZ/diagram and verify legibility.
- [x] 2.3 Construct Table I (Related Work Comparison), Table II (B0/B1/B2 Comparison), Table III (Experiment Protocol), and Table IV (Metrics) for integration into the paper.

## 3. Conference Paper Manuscript Generation

- [x] 3.1 Draft complete IEEE conference paper `ICEIB/main.tex` using `IEEEtran` class with all required sections and verify structure.
- [x] 3.2 Draft complete standalone plain-text manuscript `ICEIB/main.txt` matching all sections, tables, and references of `main.tex`.
- [x] 3.3 Create `ICEIB/README.md` documenting paper structure, compilation steps, and Overleaf import instructions.

## 4. Packaging and Repository Verification

- [x] 4.1 Create `ICEIB/overleaf-upload.zip` containing all LaTeX sources, style file, bibliography, and figures.
- [x] 4.2 Validate OpenSpec change `dic4-ieee-conference-draft` with `--strict` mode and confirm 0 errors.
- [x] 4.3 Update root `README.md` with DIC4 conference paper workflows and deliverables.
- [x] 4.4 Verify git status, perform git commit and push to remote `main`, and verify remote commit hash.
