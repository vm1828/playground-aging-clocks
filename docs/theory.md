# Theory

Foundational concepts behind everything we do in this project: what DNA
methylation is, how it's measured, and what an aging clock actually computes.

---

## DNA methylation, briefly

In mammals, certain cytosine bases in DNA can carry a methyl group (`–CH₃`).
This modification doesn't change the DNA sequence; it changes how genes are
read. Methylation patterns shift with age, environment, and disease.

## CpG sites

A "CpG" is a place in the genome where a cytosine (C) is directly followed
by a guanine (G). These are the positions where most DNA methylation happens
in mammals. A human genome has ~28 million CpGs; methylation arrays measure
a chosen subset.

## Methylation microarrays (how beta values are produced)

A **microarray** is a small piece of glass with hundreds of thousands of
microscopic spots. Each spot holds millions of identical copies of one
short DNA strand — a **probe** — that matches one specific genomic
location. Different spot, different probe, different target.

Sample DNA is washed over the array; matching DNA sticks to its probe,
non-matching washes away. Bound DNA is fluorescent-labelled and scanned —
each spot returns one intensity number (bright = lots of matching DNA).

### How methylation is read off the array

A pre-treatment called **bisulfite conversion** rewrites the DNA: unmethylated
C → U (reads as T), methylated C → unchanged. After conversion, methylated
and unmethylated versions of the same CpG site have different sequences, so
Illumina prints **two probes per CpG**, one for each version. The two
intensities feed the beta-value formula below.

### Illumina IDs

**Illumina, Inc.** dominates the methylation-array market — "methylation
array" almost always means "Illumina methylation array." Each probe (pair)
gets a catalog number called an **Illumina ID**:

- `cg00075967` — `cg` prefix = CpG-targeting probe (what we use)
- `ch...` — non-CpG cytosine probes (rare)
- `rs...` — SNP probes for sample-identity QC

The eight-digit number is just an identifier — **it does not encode the
genome coordinate.** Translation needs an Illumina manifest, or in our case
the annotation columns of the Horvath CSV.

IDs are **stable across array generations**: `cg00075967` on a 2008 chip is
the same CpG as `cg00075967` on a 2022 chip — which is why a clock trained
on 27K/450K data still applies to modern EPIC data.

| Array | Year | CpG probes |
| --- | --- | --- |
| HumanMethylation27 ("27K") | 2008 | ~27,000 |
| HumanMethylation450 ("450K") | 2011 | ~485,000 |
| MethylationEPIC v1 | 2016 | ~850,000 |
| MethylationEPIC v2 | 2022 | ~935,000 |

Each generation is mostly a superset of the previous one.

### Note on hardware

The physical product is not a printed grid but a "BeadArray": tiny silica
beads, each coated with copies of one probe sequence, are scattered randomly
into wells; ~15–20 beads per probe type get averaged for noise reduction.
The grid mental model is fine for understanding the data — beads just
explain why the hardware looks unusual in photos.

## Beta value

For one CpG in one sample, the **beta value** is the ratio computed from
the two probe intensities described above:

```text
beta = methylated_intensity / (methylated_intensity + unmethylated_intensity)
```

It's a number between 0 and 1 representing the fraction of DNA molecules
at this site that were methylated:

- 0 = unmethylated in every copy
- 1 = methylated in every copy
- 0.5 = half methylated

Beta values are what we feed into a clock.

## Aging clock

An **aging clock** predicts chronological age from a sample's CpG
methylation values. The Horvath 2013 clock — our working example — is a
**regularized linear regression**: 353 beta values in, one age out.

### It's linear regression, in biology notation

Generic linear regression with `n` inputs:

```text
y = w₁·x₁ + w₂·x₂ + ... + wₙ·xₙ + b
```

For Horvath, the names change but the formula is identical:

- `x₁..x₃₅₃` → the 353 beta values
- `w₁..w₃₅₃` → the `CoefficientTraining` column of our CSV
- `b` → `0.695507258` (intercept, first row of the CSV)
- `y` → `transformed_age` — **not years yet**; see back-transform below

So `transformed_age = intercept + sum(coef_i × beta_i)` is just
`y = w₁·x₁ + ... + wₙ·xₙ + b`.

### Why there's a back-transform

Horvath did not train the model to predict age in years directly. He first
mapped each sample's age through this function:

```text
F(age) = log((age + 1) / 21)         if age ≤ 20     (childhood scale)
F(age) = (age − 20) / 21             if age > 20     (adult scale)
```

The regression was fitted on `F(age)`, so what the model returns is `F(age)`
— and to recover years we apply the inverse:

```text
F⁻¹(y) = 21 × exp(y) − 1             if y < 0        (childhood-like)
F⁻¹(y) = 21 × y + 20                 if y ≥ 0        (adult-like)
```

(The 21 in both formulas = 20 (child/adult boundary) + 1 (keeps `log(0)`
from happening at age 0). Full prediction: `back_transform(intercept + sum(coef_i × beta_i))`.)

**Why transform age at all?** Childhood methylomes change very fast — more
"methylation distance" between ages 0 and 5 than between 40 and 45. Fitted
to raw years, an adult-heavy training set would underfit children. Compressing
childhood with `log` and stretching adulthood linearly makes the
CpGs→`F(age)` relationship something a linear model can actually learn well.

### Worked examples for the back-transform

**Adult branch.** Suppose the linear sum gives `transformed_age = 1.95`.
Since `1.95 ≥ 0` we use the adult branch:

```text
predicted_age = 21 × 1.95 + 20 = 60.95
```

→ the model predicts this person is ~61 years old.

**Childhood branch.** Suppose the linear sum gives `transformed_age = −0.5`.
Since `−0.5 < 0` we use the childhood branch:

```text
predicted_age = 21 × exp(−0.5) − 1
              = 21 × 0.6065 − 1
              ≈ 11.7
```

→ predicted age ~12 years old.

## Age acceleration

Once we have predicted age and chronological age for each sample:

```text
age_acceleration = predicted_age − chronological_age
```

Positive value = the clock thinks the person is *older* than they actually
are (epigenetically "accelerated"). This is the signal the README is
interested in.

---

## Horvath 2013, briefly

- Trained on 8,000+ samples across 51 tissue types (Horvath, UCLA, *Genome
  Biology* 2013).
- Multi-tissue — works on blood, brain, kidney, etc. (unusual; most clocks
  are tissue-specific).
- 353 CpGs selected by elastic-net regularization from ~27,000 candidates.
- Coefficients published as Additional File 3 → our
  `data/raw/horvath2013_coefficients.csv`.

The whole clock is those 354 numbers (353 weights + 1 intercept) plus the
back-transform — no library needed to apply it.
