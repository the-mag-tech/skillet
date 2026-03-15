# Research Record: Skillet Boundary and Market Positioning

Date: 2026-03-15  
Scope: Product boundary clarification before ADR-003 push

## Research Question

Should Skillet be a creator execution terminal, or a Skill Hub / Skill Bundle discovery and search backend?

## Method

Desk research + product-page analysis of representative platforms in adjacent spaces:

- Skill hubs / agent marketplaces
- Creator workflow stores
- Design/creation tools

Data source type:

1. Public website/product pages
2. Public docs/help pages
3. Public pricing and catalog pages

## Key Findings

### Finding 1: Market has supply-side hubs, but weak cross-hub discovery

- Existing hubs provide inventory (skills/workflows) but usually within a single ecosystem.
- Cross-hub ranking and normalized discovery are not a core product in observed platforms.

Implication:

- A dedicated discovery/search layer has independent product value.

### Finding 2: Creator-facing products validate willingness to pay for workflow assets

- Buyer-oriented workflow catalogs (single-skill and bundle packs) show clear packaging and monetization patterns.
- This validates demand for discoverability quality (what to use, why, and from where).

Implication:

- Skillet should optimize retrieval quality and explainability, not execution orchestration.

### Finding 3: Entry channels and discovery layer are different problem domains

- IM/Web/CLI entry points optimize interaction UX.
- Discovery/search backend optimizes relevance, ranking, and freshness.

Implication:

- Keeping IM outside Skillet boundary reduces coupling and preserves reuse across channels.

### Finding 4: Execution-layer ambitions expand scope too early

- Bundling runtime, generation, and publishing into V1 introduces high integration risk.
- Discovery-first V1 can ship faster with measurable quality metrics.

Implication:

- V1 should deliver ranked, explainable result cards rather than end-to-end content execution.

## Competitive Snapshot (high-level)

| Category | Representative products | Strength | Gap relevant to Skillet |
|---|---|---|---|
| Skill hubs | LobeHub, ClawHub | Inventory and ecosystem gravity | Limited cross-hub normalized discovery |
| Creator workflow stores | CreatorSkills-like stores | Buyer-focused packaging and bundles | Usually single-source, weak ranking transparency |
| Design tools | Lovart-like tools | Strong generation/editing UX | Not a neutral discovery/search backend |

## Decision Support

This research supports ADR-003:

- Skillet as **Backend Market Layer** (discovery/search)
- IM as **consumer entry channel**, not Skillet runtime concern
- V1 scope: ingest, normalize, retrieve, rank, explain

## Non-Goals Confirmed

The following are explicitly out of scope for Skillet V1:

1. IM gateway/runtime ownership
2. Content generation execution engine
3. Multi-platform publishing runtime

## Open Follow-ups

1. Define minimum result-card schema (install URL, source hub, confidence, ranking reasons).
2. Define freshness SLA for indexed hubs.
3. Choose first 2-3 hubs for ingestion pilot.

## Confidence

Overall confidence: Medium-High.

Reason:

- Positioning conclusion is stable across multiple product categories.
- Exact TAM/SAM estimates are not included in this record and require separate quantitative research.
