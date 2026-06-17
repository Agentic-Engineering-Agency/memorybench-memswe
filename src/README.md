# Source Structure

## MemSWE Reference Map

Treat `src/` as implementation examples for future MemSWE harness work, not as canonical
MemSWE architecture. The most reusable ideas are the small adapter interfaces, phase
orchestration, durable checkpoint files, and normalized report generation. Keep any MemSWE
implementation independently owned unless Eduardo explicitly approves a direct dependency.

Reference areas:
- `providers/`: adapter boundary for memory/search backends; useful for comparing filesystem, RAG, and hosted providers.
- `orchestrator/`: resumable phase pipeline and per-question checkpoint state.
- `orchestrator/phases/report.ts`: normalized report aggregation across accuracy, latency, tokens, retrieval, and question type.
- `benchmarks/`: dataset adapter pattern only; not a MemSWE task-schema source of truth.
- `judges/`: LLM-as-judge plumbing only; MemSWE deterministic verifiers should avoid judge-first assumptions.

```
src/
├── benchmarks/      # Benchmark adapters (LoCoMo, LongMemEval, ConvoMem)
├── providers/       # Memory provider integrations (Supermemory, Mem0, Zep)
├── judges/          # LLM-as-judge implementations (OpenAI, Anthropic, Google)
├── orchestrator/    # Pipeline execution and checkpointing
│   └── phases/      # Individual phase runners (ingest, search, answer, evaluate)
├── prompts/         # Default judge prompts by question type
├── types/           # TypeScript interfaces
├── cli/             # CLI commands
├── server/          # Web UI server
└── utils/           # Config, logging, model utilities
```

## Key Files

| File | Purpose |
|------|---------|
| `types/provider.ts` | Provider interface |
| `types/benchmark.ts` | Benchmark interface |
| `types/judge.ts` | Judge interface |
| `types/unified.ts` | Shared data types (UnifiedSession, UnifiedQuestion) |
| `types/prompts.ts` | Prompt type definitions |
| `utils/models.ts` | Model configurations and aliases |
| `utils/config.ts` | Environment config loading |
| `prompts/defaults.ts` | Default judge prompts |
