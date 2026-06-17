# Providers

Memory provider integrations. Each provider implements the `Provider` interface.

## MemSWE Reference Notes

For MemSWE, use this provider layer as a conceptual adapter boundary: initialize a backend,
ingest or prepare inputs, wait for any indexing step, search with scoped options, then clear
test data. The pattern can inform filesystem/RAG baselines and hosted-provider comparisons,
but it is not a required dependency and should not define MemSWE run records or scoring.

Avoid carrying over QA/judge-first assumptions from MemoryBench providers. MemSWE deterministic
scoring should keep provider retrieval/output capture separate from verifier rules.

## Interface

```typescript
interface Provider {
    name: string
    prompts?: ProviderPrompts
    initialize(config: ProviderConfig): Promise<void>
    ingest(sessions: UnifiedSession[], options: IngestOptions): Promise<IngestResult>
    awaitIndexing(result: IngestResult, containerTag: string): Promise<void>
    search(query: string, options: SearchOptions): Promise<unknown[]>
    clear(containerTag: string): Promise<void>
}
```

## Adding a Provider

1. Create `src/providers/myprovider/index.ts`
2. Implement `Provider` interface
3. Register in `src/providers/index.ts`
4. Add to `ProviderName` type in `src/types/provider.ts`
5. Add config in `src/utils/config.ts`

**Required returns:**
- `initialize()` - Set up client with API key
- `ingest()` - Return `{ documentIds: string[], taskIds?: string[] }`
- `awaitIndexing()` - Wait for async indexing to complete
- `search()` - Return array of results (provider-specific format)
- `clear()` - Delete data by containerTag

## Custom Prompts

Providers can override answer generation and judge prompts via `ProviderPrompts`:

```typescript
interface ProviderPrompts {
    answerPrompt?: string | ((question: string, context: unknown[], questionDate?: string) => string)
    judgePrompt?: (question: string, groundTruth: string, hypothesis: string) => { default: string, [type: string]: string }
}
```

**Answer Prompt:** Transform search results into an LLM prompt. Function receives raw search results.

**Judge Prompt:** Return prompts keyed by question type. Must include `default`. Falls back to built-in prompts if not provided.

Example: See `src/providers/zep/prompts.ts`

## Existing Providers

| Provider | SDK | Notes |
|----------|-----|-------|
| `supermemory` | `supermemory` | Raw JSON sessions |
| `mem0` | `mem0ai` | v2 API with graph |
| `zep` | `@getzep/zep-cloud` | Graph-based, custom prompts |
| `filesystem` | local files + OpenAI extraction | MEMORY.md-style baseline; pattern only for MemSWE |
| `rag` | local RAG utilities | Local retrieval baseline; pattern only for MemSWE |
