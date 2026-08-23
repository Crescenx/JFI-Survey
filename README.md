# Learning and Strategic Guarantees

This repository is the collaboration source for the manuscript. The same tree
is intended for GitHub, Overleaf, and downloadable LaTeX packages.

- Main document: `main.tex`
- Bibliography: `ref.bib`
- Manuscript sections: `sections/`
- Production figures and editable figure sources: `figure/`

Compile locally with:

```sh
latexmk -pdf -interaction=nonstopmode -halt-on-error main.tex
```

On Overleaf, select `main.tex` as the main document and pdfLaTeX as the
compiler. Generated PDFs and LaTeX intermediate files are not versioned.
