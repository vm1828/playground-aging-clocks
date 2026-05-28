# playground-aging-clocks

Predicting biological age from DNA methylation, and studying the gap between
predicted and chronological age (**age acceleration**) as a systemic signal of aging.

## Roadmap

- [ ] Setup
- [ ] **Apply an existing clock** — run a published clock on public data, plot predicted vs. chronological age. No training; learn the data pipeline.
- [ ] **Train my own clock** — fit an elastic net on the methylation matrix, compare to the published clock.
- [ ] **Ask one real question** — e.g. does the clock transfer across tissues? Which CpGs drive it?

## Data

Public methylation datasets from NCBI GEO. Specific dataset still open.

## Stack

Python · `pandas` · `scikit-learn` · `matplotlib` · `jupyter`

## Concepts

Field terminology (CpG site, beta value, methylation array, Illumina ID,
aging clock, age acceleration, etc.) is defined in [docs/theory.md](docs/theory.md).
