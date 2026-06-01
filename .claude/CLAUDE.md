# CLAUDE.md

Project-specific context and research-discipline rules live in `@PROJECT.md`.
This file is generic engineering rules only.

## YAGNI (most important rule)

Apply before writing anything else:

- No class, field, parameter, file, dir, helper, dep, or pipeline step without a caller in this commit.
- No column, feature, or intermediate table computed "in case we need it later." Compute when a downstream step reads it.
- "Scalable" = the shape can grow non-breakingly. NOT = pre-declaring future fields or future architectures.
- When unsure, delete. Re-add when there's a real caller.
- This overrides "design for scale" elsewhere. Design = shape, not pre-declared content.

## Rules

- minimal output, no boilerplate
- simplest working solution
- short, direct, no repetition

## Code style

- imports: stdlib → third-party → local, blank lines between
- type-annotate all params and returns, built-in generics (3.12+)
- docstrings: 1-line or none for obvious code; multi-line only when intent is non-obvious
- comments explain _why_, not _what_; inline, lowercase, terse
- fail fast: `os.environ["KEY"]`, `df["col"]` — no silent defaults for required values; let missing keys raise loudly
- no hardcoded config (paths, thresholds, hosts) — env vars or config even in scripts; only exception: a true one-off literal with no chance of reuse
- no helpers for one-time use
- no abstraction until pattern repeats 3+ times
- no error handling for impossible cases; do handle real ones
- no backcompat shims
- 3 similar lines > premature abstraction

## Jupyter notebooks

Outline every notebook with markdown-header cells in this fixed order and
casing — R-style: level-1 `#` ALL-CAPS, level-2 `##` Capitalized.

```text
# SETUP
## Install     — package installs; omit when the project manages deps
## Import      — imports only, grouped per Code style
## Constants   — paths, accessions, params, seeds (no hardcoded values below)
## Utils       — helpers reused later in the notebook
# DATA
## Download    — fetch raw external data (checksum-verified)
## Load        — read into memory
## EDA         — shapes, distributions, sanity checks
# ANALYSIS     — the core computation; rename to the verb that fits (MODEL, PREDICT, …)
# RESULTS
## Figures     — plots
## Export      — write outputs to data/processed
```

- One markdown cell per header; at most one line of prose under it.
- YAGNI: emit a header only when a cell fills it — never an empty section.
- This is the superset/order; a given notebook uses the subset it needs.

@PROJECT.md
