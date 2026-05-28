# PROJECT.md

## What this is

A learning-by-building playground — not a thesis, not a paper. Clean tooling,
real public data, a reproducible pipeline I can later point at harder problems
(cross-species clocks, reprogramming time-courses).

Analysis-first: correct result on real data before any generalization.

## Working style

- Default to conversation. Do not write code, create files, edit docs, install dependencies, or fetch data unless I explicitly ask. I will always tell you when to act.
- Suggestions are fine ("we could capture this in theory.md if you want"); stop after the suggestion and wait for the green light.

## Reproducibility (non-negotiable)

- every result traces to: dataset accession/version, code commit, params. No orphan numbers.
- random seeds set and recorded wherever stochastic (splits, model init, sampling)
- raw data is read-only; transforms write new files/objects
- pin dependency versions; pin reference genome/annotation build (e.g. GRCh38, Ensembl release) explicitly
- a result you can't regenerate from a clean checkout doesn't exist

## Data provenance

- dataset identifiers (GEO/SRA/EGA/dbGaP, GTEx/TCGA/UK Biobank) in config, not in code
- record genome build, assay type, normalization, and units for every matrix loaded
- paths via env vars or config, never hardcoded to a local dump
