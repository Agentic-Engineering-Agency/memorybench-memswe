# MemoryBench Agent Rules

This repository is a pluggable TypeScript benchmark for memory and context providers and serves only as reference material for sibling MemSWE work.

Workspace standards apply here: [../../docs/standards/README.md](../../docs/standards/README.md).
This file adds only what is specific to this repo. Where they conflict, this file wins, and the conflict is recorded under `## Deviations`.

## Stack

- Language / runtime: TypeScript ES modules on Bun.
- Framework / platform: AI SDK provider and judge adapters, Drizzle-backed run data, plus a Next.js 15 / React 19 inspection UI.
- Package manager: Bun (`bun.lock`).

## Non-negotiables

- Keep providers, benchmarks, and judges behind their declared interfaces; orchestration and normalized reports must not depend on one vendor's private shape.
- Preserve checkpoint compatibility across ingest, index, search, answer, evaluate, and report so interrupted runs can resume by run ID.
- Keep API keys in local environment files. Never commit keys, provider payloads containing secrets, or private benchmark data.
- This repository is not the MemSWE schema, scoring, condition, or runtime authority. Do not introduce a direct MemSWE dependency on it without Eduardo's explicit approval.
- For comparable runs, hold benchmark, target question IDs, answering model, judge model, prompt policy, and concurrency policy constant across providers, and preserve the checkpoint and report artifacts.
- Random sampling currently uses unseeded `Math.random()`. Do not describe it as reproducible; use consecutive selection or reuse the checkpointed `targetQuestionIds` for repeatable comparisons.
- Keep provider identity out of blind judge inputs. The judge contract should receive only the question evidence needed to score the hypothesis, not the provider label being compared.

## Commands

| Purpose | Command |
|---|---|
| Install | `bun install` |
| CLI help | `bun run src/index.ts help` |
| Run benchmark | `bun run src/index.ts run -p <provider> -b <benchmark> -r <run-id>` |
| Compare providers | `bun run src/index.ts compare -p <provider-a>,<provider-b> -b <benchmark> -j <judge> --compare-id <compare-id>` |
| Test | `bun test` |
| Format check | `bun run format:check` |
| UI development | `cd ui && bun run dev` |
| UI build | `cd ui && bun run build` |

## Verification gates

- Required for framework changes: `bun test` and `bun run format:check`.
- Required for CLI changes: the relevant command plus `bun run src/index.ts help` must execute locally without making an unrelated paid provider call.
- Required for UI changes: `cd ui && bun run build` and an interactive inspection of the affected run view.
- A benchmark claim requires retained `checkpoint.json` and `report.json`, matched target question IDs and configuration, and clearly identified answering and judge models.

## Read order

1. `README.md`
2. `src/README.md`
3. `src/types/checkpoint.ts` and the affected interface under `src/types/`
4. The relevant guide under `src/providers/`, `src/benchmarks/`, or `src/judges/`
5. The affected orchestrator phase and its tests
6. `ui/package.json` and the affected route under `ui/app/` for UI work

## Scope discipline

- Default to one adapter or pipeline phase; avoid provider-specific branches in shared orchestration.
- Do not turn reference patterns from this repository into canonical MemSWE definitions.
- Keep run artifacts and credentials out of source control, and never use paid comparison runs as a routine smoke test.
- Distinguish judge-based accuracy from deterministic verification in all claims and reports.

## Command Code (alternate harness)

Command Code is an **alternate** executor available in this repo, admitted for a named
capability gap (taste learning, checkpoints/rewind, plan-mode review, headless `cmd -p` runs,
native MCP with per-server permission gating). It reads this `AGENTS.md` as its memory file, so
this file remains the single instruction source. It is **not** the default — OMP is. The
generated `.commandcode/settings.json` mirrors the OMP discipline in Command Code's permission
rules and is materialized from `scripts/harness-matrix.json`; never hand-edit it. See
`docs/standards/harness.md` (Command Code section) and `docs/research/command-code-evaluation.md`.

## Deviations

- None.
