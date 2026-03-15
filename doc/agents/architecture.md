# Architecture — Skillet

## System Overview

Skillet is a discovery/search backend for Skill Hubs and Skill Bundles.
It does not own user entry channels (IM/Web/CLI); it serves them.

## Core Pipeline

1. **Ingest**: Collect catalog data from multiple Hubs
2. **Normalize**: Map raw entries to unified Skill/Bundle schema
3. **Index**: Build searchable fields and semantic references
4. **Retrieve**: Candidate recall by query intent
5. **Rank**: Blend SkillRank signals with Prism semantic relevance
6. **Explain**: Return ranking reasons and source provenance

## Module Map

| Module | Responsibility | Dependencies |
|--------|---------------|-------------|
| `src/ingest/` | Pull Hub catalogs and updates | Hub sources |
| `src/normalize/` | Convert to canonical schema | Internal schema |
| `src/index/` | Build search index and facets | Storage/index engine |
| `src/retrieve/` | Query-time candidate recall | Index + Prism |
| `src/rank/` | Signal blending and scoring | SkillRank + Prism |
| `src/explain/` | Ranking reason generation | Rank outputs |

## Data Flow

```
Hub Sources ──► Ingest ──► Normalize ──► Index
                                      │
User Query ───────────────► Retrieve ─┴─► Rank ─► Explain ─► Result Cards
                                        ▲
                              SkillRank + Prism signals
```

## Boundary Notes

- Skillet returns discoverability results, not executed content.
- OpenClaw/IM layer is a consumer of Skillet, not part of Skillet runtime.
