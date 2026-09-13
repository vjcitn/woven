# Review: Bioconductor Principles, S4 Usage, Documentation, and Testing

## Strong points

- Documentation coverage is broad (README, vignette, man pages), especially user workflow and examples:
  - `README.md`
  - `vignettes/woven-intro.Rmd`
- Core API has test coverage for main fit/predict/metrics/laplacian behavior:
  - `tests/testthat/test-woven-api.R`
  - `tests/testthat/test-metrics.R`
  - `tests/testthat/test-laplacian.R`

## Gaps vs Bioconductor principles

- The package uses a custom **S3** object (`class = "woven"`) rather than Bioconductor-style **S4** containers/methods:
  - `R/woven.R` (class construction, `print.woven`, `summary.woven`, `plot.woven`)
- Inputs are plain matrix lists, not interoperable Bioconductor containers (e.g., `SummarizedExperiment`/`MultiAssayExperiment`):
  - `R/woven.R`
- Parallelization uses `parallel::mclapply`, which is not ideal for Bioconductor cross-platform expectations (Windows):
  - `R/woven.R` (`woven_precompute`)

## S4 usage assessment

- No `setClass`, `setMethod`, or `setGeneric` usage found in package code:
  - `R/`
  - `NAMESPACE`
- So S4 usage is currently **absent**, not just inconsistent.

## Documentation consistency issues

- `man/woven.Rd` details mention solver behavior that does not match current code/tests:
  - `man/woven.Rd` says V>=3 uses ALS
  - `tests/testthat/test-woven-api.R` expects unified dual solver
  - `R/woven.R` routes fitting through `woven_mcca_dual`
- Spelling error to fix:
  - `R/woven.R` includes a misspelled Nyström/Nystrom term in the projection description
- Separate style standardization decision:
  - Across docs/comments, choose one canonical form (`Nystrom` ASCII or `Nyström` diacritic) and apply consistently (e.g., `README.md`, `R/woven.R`, `man/woven.Rd`).

## Testing follow-up hypotheses (not yet confirmed)

- Candidate under-tested area: precompute reuse and scaling persistence in scoring/prediction paths:
  - Implementation paths: `R/woven.R` (`woven_precompute`, `woven_scores`, `woven_predict`)
  - Existing tests: `tests/testthat/test-woven-api.R`
- Candidate under-tested area: plot/summary edge-case coverage:
  - Implementation paths: `R/woven.R` (`plot.woven`, `summary.woven`)
  - Existing tests: `tests/testthat/test-woven-api.R`

## Follow-up hypothesis (not yet confirmed)

- Candidate mismatch to verify with a focused test in `woven_nystrom_error` (`R/metrics.R`): check whether the fit-object field names accessed there match the fields produced during fit-object construction in `R/woven.R`; add this case under `tests/testthat/test-metrics.R`.
