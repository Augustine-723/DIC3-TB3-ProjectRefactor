# ICEIB IEEE Conference Paper Package

**Title**: A Validation-Aware and Evidence-Grounded AI Agent for Indoor Navigation Using TurtleBot3  
**Target Conference**: [International Conference on Electronic Communications, Internet of Things and Big Data (ICEIB)](https://www.iceib.asia/)  
**Target Publication**: IEEE Conference Proceedings / IEEE Xplore Track  
**Format**: Official IEEEtran Conference Format (2-Column)  
**Authors**: Zhi-Heng Chen (7115064009), Cheng-Hao Hsiao (7115064186), Rui-Yu Lin (7115064168), Yuan-Mei Chiu (7115064159), and Yi-Kai Guan (7115093001)  
**Affiliation**: Group 7, Department of Information and Communication Engineering, National Taiwan University of Science and Technology  

---

## Directory Contents

```
ICEIB/
├── main.tex                 # Compilable LaTeX paper entry point (IEEEtran)
├── main.txt                 # Complete unabridged plain-text manuscript
├── references.bib           # Verified IEEE BibTeX bibliography (refs [1]–[12])
├── IEEEtran.cls             # Official IEEE LaTeX conference document class (v1.8b)
├── figures/                 # Vector TikZ and diagram source files
│   ├── fig1_architecture.tex # Figure 1: 3-Layer, 8-Module Architecture
│   └── fig2_workflow.tex     # Figure 2: Validation–Dispatch–Monitoring Workflow
├── overleaf-upload.zip      # Complete self-contained package for Overleaf import
└── README.md                # This documentation file
```

---

## Overleaf Import Instructions

To import and compile this manuscript on [Overleaf](https://www.overleaf.com):

1. **Download Archive**:
   Download `ICEIB/overleaf-upload.zip` from this repository.

2. **Create Overleaf Project**:
   - Log in to [Overleaf](https://www.overleaf.com).
   - In the top-left corner, click **New Project** $\to$ **Upload Project**.
   - Drag and drop or browse to select `overleaf-upload.zip`.

3. **Verify Settings and Recompile**:
   - Ensure `main.tex` is set as the **Main document**.
   - Compiler can be set to **pdfLaTeX** or **XeLaTeX** (default settings work out-of-the-box).
   - Click **Recompile** to produce the publication-grade PDF.

---

## Local Compilation (If LaTeX is Installed)

To compile locally using `pdflatex` or `latexmk`:

```bash
cd ICEIB
pdflatex main.tex
bibtex main
pdflatex main.tex
pdflatex main.tex
```

Or using `latexmk`:
```bash
latexmk -pdf main.tex
```

---

## Academic Integrity Statement

All experimental figures, benchmark matrices, and latency tables in this conference draft describe **planned evaluation protocols** and **expected research outcomes**. No physical robot experiments have been executed yet, and no numerical measurements or statistical significance claims have been fabricated. Section VI is formally designated as *Proposed Evaluation and Expected Outcomes*.
