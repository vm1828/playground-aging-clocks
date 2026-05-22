# playground-aging-clocks

Predicting biological age from DNA methylation, and studying the gap between
predicted and chronological age (**age acceleration**) as a systemic signal of aging.

## Roadmap

- [ ] Setup
- [ ] **Apply an existing clock** — run a published clock on public data, plot predicted vs. chronological age. No training; learn the data pipeline.
- [ ] **Train my own clock** — fit an elastic net on the methylation matrix, compare to the published clock.
- [ ] **Ask one real question** — e.g. does the clock transfer across tissues? Which CpGs drive it?

## Data

Public methylation datasets from NCBI GEO: GSE40279 (blood, primary), GSE74193 (brain, transfer test).

## Stack

Python · `pyaging` · `scikit-learn` · `pandas` · `matplotlib` · `jupyter`

## Glossary

- **CpG site** — genomic position where methylation occurs; the model's features.
- **Beta value** — 0–1, how methylated one CpG is in one sample.
- **Aging clock** — model mapping CpG values to predicted age.
- **Age acceleration** — predicted minus chronological age; the signal of interest.
