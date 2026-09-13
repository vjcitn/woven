# Review: Bioconductor Principles, S4 Usage, Documentation, and Testing

## Strong points

- Documentation coverage is broad (README, vignette, man pages), especially user workflow and examples:
  - `/home/runner/work/woven/woven/README.md`
  - `/home/runner/work/woven/woven/vignettes/woven-intro.Rmd`
- Core API has test coverage for main fit/predict/metrics/laplacian behavior:
  - `/home/runner/work/woven/woven/tests/testthat/test-woven-api.R`
  - `/home/runner/work/woven/woven/tests/testthat/test-metrics.R`
  - `/home/runner/work/woven/woven/tests/testthat/test-laplacian.R`

## Gaps vs Bioconductor principles

- The package uses a custom **S3** object (`class = "woven"`) rather than Bioconductor-style **S4** containers/methods:
  - `/home/runner/work/woven/woven/R/woven.R` (class construction, `print.woven`, `summary.woven`, `plot.woven`)
- Inputs are plain matrix lists, not interoperable Bioconductor containers (e.g., `SummarizedExperiment`/`MultiAssayExperiment`).
- Parallelization uses `parallel::mclapply`, which is not ideal for Bioconductor cross-platform expectations (Windows):
  - `/home/runner/work/woven/woven/R/woven.R`

## S4 usage assessment

- No `setClass`, `setMethod`, or `setGeneric` usage found in package code.
- So S4 usage is currently **absent**, not just inconsistent.

## Documentation consistency issues

- `woven.Rd` details mention solver behavior that does not match current code/tests:
  - `/home/runner/work/woven/woven/man/woven.Rd` says V>=3 uses ALS
  - `/home/runner/work/woven/woven/tests/testthat/test-woven-api.R` expects unified dual solver
- Minor terminology inconsistency/typo around “Nyström/Nystrm” in docs/comments.

## Testing quality issues

- Some important paths are untested (precompute reuse, scaling persistence, plot/summary edge cases, multi-modality >2 behavior).
- Potential bug path appears untested in Nyström metric internals:
  - `/home/runner/work/woven/woven/R/metrics.R` references `fit$Za_list` while fit stores `Z_anchors`.
