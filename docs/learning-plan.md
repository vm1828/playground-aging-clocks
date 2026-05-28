# Learning plan

**Learn by building**, not by reading papers front-to-back. Concepts get
introduced inline in code and notebooks at the moment they matter; vocabulary
lands in the README glossary as we hit it. Source papers are catalogued in
[reading-list.md](reading-list.md) as references for stuck moments.

---

## Principles

- **Code first, papers second.** Write code, hit a wall, then look up the
  exact thing that's blocking you.
- **Artifacts first, theory second.** A working scatter plot teaches the
  shape of the problem faster than any review.
- **Vocabulary lands when you trip on it.** New term → one-line glossary
  entry → move on.

## How this plays out

For each roadmap item: build the thing, learn the concepts as they bite. The
first notebook walks through loading coefficients, intersecting CpG IDs, and
back-transforming predictions — the math from [theory.md](theory.md), made
concrete on real data.

## Why we apply Horvath 2013 by hand (not via `pyaging`)

`pyaging` bundles many clocks behind one API but depends on PyTorch (overkill
for a linear model) and pulls ~6 GB of unused CUDA libraries. Calling
`pya.pred.predict_age(adata)` also hides what a clock actually is. So we do
it with `pandas` + `numpy`. We'll reach for `pyaging` later when we want to
compare predictions across many clocks at once.

## Trade-off

Build-first means **being wrong first, then correcting**. Some field
terminology lies in wait — e.g. "beta value" is not the statistical beta but
a 0–1 methylation fraction; "methylation array" almost always means one of
Illumina's idiosyncratic chips. You'll occasionally backtrack when reality
differs from your intuition.

## When to break the rule

For structural decisions that are hard to undo, read the source paper first:

- normalization method choice (BMIQ, SWAN, funnorm) — wrong default corrupts
  everything downstream
- definition of the chronological-age ground truth (collection date vs.
  reported age vs. cohort enrollment)
- cross-platform CpG conversions (27K ↔ 450K ↔ EPIC)

Start at [reading-list.md](reading-list.md) for these.
