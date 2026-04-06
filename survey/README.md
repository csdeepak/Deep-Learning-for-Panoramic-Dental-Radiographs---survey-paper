# Survey Manuscript

This directory contains the source and documentation for the survey paper:

Deep Learning for Panoramic Dental Radiographs: A Comprehensive Survey on Caries Detection, Periodontal Bone Loss Assessment and Explainable AI

## Contents

```text
survey/
├── README.md
└── latex/
    ├── conference_101719.tex
    ├── source.bib
    └── IEEEtran.cls
```

Figure assets are stored centrally at:
- `assets/figures/`

The LaTeX source references those images using relative paths.

## Build Instructions

```bash
cd survey/latex
pdflatex conference_101719.tex
bibtex conference_101719
pdflatex conference_101719.tex
pdflatex conference_101719.tex
```

## Notes

- `conference_101719.tex` is the main manuscript source.
- `source.bib` contains the bibliography (47+ references).
- `IEEEtran.cls` is the conference template class used by the manuscript.
