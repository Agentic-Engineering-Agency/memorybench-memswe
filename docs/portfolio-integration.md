# Portfolio integration contract

This is a repository-local mapping, not a second portfolio schema. MemoryBench remains reference
material for MemSWE; it is not the MemSWE benchmark, scoring, condition, runtime, or run-record
authority. The proposed Telar layer does not currently ingest these runs.

## Role and maturity

| Classification | Current status |
|---|---|
| Primary role | Evaluation/benchmark evidence, reference implementation |
| Native execution surface | Bun CLI and checkpointed six-phase pipeline |
| System of record | `data/runs/<runId>/checkpoint.json`, per-question results, and `report.json` |
| Comparison ownership | `data/compare/<compareId>/manifest.json` pins providers, models, sampling, and target question IDs |
| Portfolio integration | Proposed; no Ultimate Harness or Telar adapter is shipped here |

## Evidence receipt mapping

| Required category | Current native evidence | Limit |
|---|---|---|
| Provenance | Run/compare IDs, benchmark, provider, answering model, judge, target question IDs, sampling, concurrency, and timestamps | Random sampling uses unseeded `Math.random()`; it is not reproducible unless target IDs are reused |
| Quality | Judge label/score and aggregate accuracy in `report.json` | Judge-based QA accuracy, not deterministic coding-task verification |
| Latency | Phase and aggregate latency statistics | Local/provider wall time; environment and provider load may drift |
| Tokens | Prompt/base/context token counts and MemScore context-token component | Token counts are not monetary cost |
| Cost | Not recorded | Must be exported as `unavailable`, never `0` |
| Artifacts | Checkpoint, search results, report, and compare manifest | Generated run data stays outside source-control claims unless explicitly retained |

## Blind-review limits

The judge contract should not receive an explicit provider label. Current provider-specific answer
and judge prompt hooks can still create indirect identity leakage, and output style can reveal the
provider. A future receipt must state whether the actual payload was audited for provider identity;
"label omitted" alone is not a claim of full blinding.

## Portfolio seam

1. MemoryBench owns its native checkpoints and reports; consumers use immutable references and
   hashes rather than copying or rewriting them.
2. MemSWE may borrow adapter isolation, checkpointing, and normalized-report patterns, but it owns
   different schemas and deterministic-first scoring.
3. Ultimate Harness may later execute a bounded MemoryBench command and link its attempt receipt;
   it does not become the score owner.
4. The proposed Telar plane may register authorization and evidence references only after a versioned
   adapter exists. No current repository artifact proves that integration.

## Future plan

- Add a versioned export at the portfolio seam only after the root contract is canonical.
- Record repository revision, exact CLI arguments, resolved package/runtime versions, payload hashes,
  and explicit cost availability alongside the existing model, judge, latency, token, and quality data.
- Add a payload audit that proves what identity information crossed the judge seam.
