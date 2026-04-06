# Deep Learning for Panoramic Dental Radiographs

A research repository for an academic survey on AI methods for panoramic dental radiographs (OPG), with emphasis on caries detection, periodontal bone-loss assessment, tooth localization/numbering, and explainable AI.

## Project Snapshot

- Title: Deep Learning for Panoramic Dental Radiographs: A Comprehensive Survey on Caries Detection, Periodontal Bone Loss Assessment and Explainable AI
- Institution: Department of Computer Science, PES University (RR Campus), Bangalore, India
- Team: C S Deepak, Chennupati Gunadeep, Archana, AVSN Sai Srujan
- Academic guidance: Dr. Mamatha H R
- Literature coverage: 47 reviewed papers (2021-2026)

## Repository Purpose

This repository organizes the survey artifacts and supporting material used in the capstone literature study.

It includes:
- The survey manuscript source (LaTeX + bibliography)
- Final manuscript and analysis documents
- A curated paper corpus used for review
- Figure assets used in the manuscript

## Repository Structure

```text
.
├── assets/
│   └── figures/                                # Figures used by manuscript
├── docs/
│   ├── analysis/
│   │   └── capstone-charts.pdf                 # Analysis charts
│   ├── manuscript/
│   │   └── survey-paper-v2.pdf                 # Compiled survey manuscript (PDF)
│   └── review/
│       └── dental-xray-capstone-literature-review.xlsx
├── references/
│   └── reviewed-research-papers/               # 47 reviewed research PDFs
├── survey/
│   ├── README.md                               # Survey submodule guide
│   └── latex/
│       ├── conference_101719.tex               # Main LaTeX source
│       ├── source.bib                          # Bibliography entries
│       └── IEEEtran.cls                        # IEEE template class
├── .gitignore
├── CITATION.cff
├── CONTRIBUTING.md
├── LICENSE
├── NOTICE
└── README.md
```

## How To Build The Survey PDF

Prerequisites:
- TeX distribution with `pdflatex` and `bibtex` (TeX Live or MiKTeX)

Build steps:

```bash
cd survey/latex
pdflatex conference_101719.tex
bibtex conference_101719
pdflatex conference_101719.tex
pdflatex conference_101719.tex
```

Expected output:
- `conference_101719.pdf` in `survey/latex/`

## Research Scope Summary

Reviewed categories:
- Caries detection and severity classification
- Periodontal disease and alveolar bone-loss assessment
- Multi-task end-to-end OPG diagnosis
- Explainable AI in dental radiograph analysis
- Dataset, benchmark, and clinical validation studies
- Tooth segmentation and numbering

Key gap highlighted by the survey:
- Existing studies report strong performance on individual tasks, but there is no broadly accepted unified, clinically integrated panoramic diagnostic pipeline combining multi-disease detection, severity staging, and explainability.

## Data and Content Notes

- This repository contains research documents and manuscript artifacts.
- The referenced public datasets (for future model development) are not bundled here.
- Some files in `references/reviewed-research-papers/` may have restrictive publisher copyrights; keep use academic and non-commercial unless permissions allow otherwise.

## Citation

If you use this repository in academic work, cite using metadata in `CITATION.cff`.

## License

This repository is released under the MIT License. See `LICENSE`.

Third-party research papers in `references/reviewed-research-papers/` remain the property of their respective copyright holders and are not relicensed by this repository.
