# Data sources

Every external file expected in `data/raw/` is listed here with its origin,
citation, access date, and SHA-256 so the same file can be re-verified
after download. Raw files are gitignored — fetch them from the URLs below.

---

## Horvath 2013 clock coefficients

- **File**: `data/raw/horvath2013_coefficients.csv` (134,367 bytes, 357 lines)
- **SHA-256**: `1d028d6ac2ca8fb4bc05f3a1453574f167b24e56414cc66512a854a5ac65f88c`
- **Source URL**: <https://static-content.springer.com/esm/art%3A10.1186%2Fgb-2013-14-10-r115/MediaObjects/13059_2013_3156_MOESM3_ESM.csv>
- **Access date**: 2026-05-22
- **Citation**: Horvath S. *DNA methylation age of human tissues and cell types.* *Genome Biology* 14, R115 (2013). <https://doi.org/10.1186/gb-2013-14-10-r115>
- **License**: Open access (CC BY)
- **Contents**: CSV with 354 coefficients (1 intercept + 353 CpGs) plus per-CpG annotation columns. Two coefficient columns: `CoefficientTraining` (full clock, all CpGs nonzero) and `CoefficientTrainingShrunk` (sparse variant, many `NA`).
- **Sanity checks**: intercept = `0.695507258`; 353 rows match `cgXXXXXXXX`; first CpG `cg00075967` has coefficient `0.12933661`.
- **Mirror note**: The UCLA original (`horvath.genetics.ucla.edu/html/dnamage/AdditionalFile3.csv`) currently has an expired TLS cert. The Springer URL above is the byte-equivalent supplementary from the same paper.

Re-verify: `sha256sum data/raw/horvath2013_coefficients.csv` should match the hash above.
