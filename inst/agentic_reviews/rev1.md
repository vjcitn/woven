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
- Terminology inconsistency around Nyström/Nystrom appears in docs/comments; canonicalize to **Nystrom** (ASCII) throughout:
  - `R/woven.R` includes the exact in-code typo "Nystrm projection" (missing "o")
  - `README.md` uses "Nystrom"

## Testing quality issues

- Some important paths appear under-tested, including precompute reuse and scaling persistence in the scoring/prediction paths:
  - Implementation paths: `R/woven.R` (`woven_precompute`, `woven_scores`, `woven_predict`)
  - Existing tests: `tests/testthat/test-woven-api.R`
- Plot/summary edge-case coverage also appears limited:
  - Implementation paths: `R/woven.R` (`plot.woven`, `summary.woven`)
  - Existing tests: `tests/testthat/test-woven-api.R`

## Follow-up hypothesis (not yet confirmed)

- Candidate mismatch to verify with a focused test: `R/metrics.R` references `fit$Za_list`, while the fit object in `R/woven.R` stores `Z_anchors = raw$Za_list`.
