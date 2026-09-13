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

- `man/woven.Rd` details mention solver behavior that does not match implementation/tests:
  - `man/woven.Rd` says V>=3 uses ALS
  - `R/woven.R` routes fitting through `woven_mcca_dual`
  - `tests/testthat/test-woven-api.R` expects unified dual solver
  - Recommended resolution: update `man/woven.Rd` to document the unified `woven_mcca_dual` solver behavior.
- Inconsistent spelling/terminology appears across docs/comments, including a misspelled form in source text:
  - `R/woven.R` includes a misspelled Nyström/Nystrom term in the projection description
  - Across docs/comments, choose one canonical form (`Nystrom` ASCII or `Nyström` diacritic) and apply consistently (e.g., `README.md`, `R/woven.R`, `man/woven.Rd`).

## Follow-up TODOs (owner: package maintainer)

- Candidate under-tested area: precompute reuse and scaling persistence in scoring/prediction paths:
  - Implementation paths: `R/woven.R` (`woven_precompute`, `woven_scores`, `woven_predict`)
  - Existing tests: `tests/testthat/test-woven-api.R`
- Candidate under-tested area: plot/summary edge-case coverage:
  - Implementation paths: `R/woven.R` (`plot.woven`, `summary.woven`)
  - Existing tests: `tests/testthat/test-woven-api.R`
- Open question for follow-up testing in `woven_nystrom_error` (`R/metrics.R`): verify that fit-object fields accessed there align with the fields produced during fit-object construction in `R/woven.R`; add this case under `tests/testthat/test-metrics.R`.
