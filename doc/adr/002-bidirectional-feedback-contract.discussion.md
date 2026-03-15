# ADR-002 Bidirectional Feedback Contract — Discussion Log

## Session 2026-02-17 — "Are They Really Unidirectional?"

### Starting Point

After defining Skillet's orchestrator role (ADR-001), the natural question was
how SkillRank and Prism communicate. The initial architecture assumed a clean
unidirectional flow: Skillet queries SkillRank for rankings, queries Prism for
content, and the two infrastructure layers never talk to each other.

### Decision Journey

[Agent] presented the three-repo architecture as having clean, one-way
dependencies: Skillet → SkillRank (read rankings), Skillet → Prism (read/write
knowledge graph). This seemed architecturally clean — each component has a
single responsibility, and the orchestrator handles all coordination.

> **[Human]** challenged this directly: "他们真的是单向的 SkillRank 和 Prism
> 的关联关系吗？难道互相之间不会影响对方的权重吗？"

This was the pivotal question of the session. It forced a deeper analysis of
the data flow, and the answer was clearly no — the relationship is
bidirectional:

- **SkillRank → Prism**: When Prism ingests a Skill, knowing that it comes from
  a high-quality Hub (SkillRank score) should influence how much trust/weight
  to assign to that entity in the knowledge graph.

- **Prism → SkillRank**: When Prism has ingested many Skills from a Hub and
  analyzed their semantic quality, citation density, and contributor reputation,
  this rich signal should feed back into SkillRank's ranking of that Hub.

> **[Agent]** drew the analogy to Google's architecture: PageRank (link graph)
> and content understanding (semantic analysis) are not independent — they
> co-evolve. Structure informs semantics, semantics inform structure.

[Human] immediately accepted this framing and asked where the contract should
be documented. Since Skillet is the orchestrator driving both data flows, the
ADR was placed in the Skillet repository.

> **[Human]** approved the specific implementation approach: pre-reserved
> interface stubs in both SkillRank (`POST /signal`) and Prism
> (`priorWeight` in `/ingest`, `GET /graph/hub-signals`). V1 accepts and
> stores data without using it; V2 integrates it into ranking/weighting.

This "interface shape first, logic later" strategy was critical — it allows
all three repos to develop independently while guaranteeing they'll connect
when ready.

[Agent] then defined the signal type enumeration: `citation_density`,
`contributor_reputation`, `content_quality`, `fork_inbound`,
`semantic_uniqueness`. Each maps to a specific Prism capability (graph queries,
entity analysis, ingestion metrics, relationship extraction, vector comparison).

The Skillet orchestration logic was specified as a three-phase flow:
1. Discovery (user-triggered) — query SkillRank
2. Ingestion (async) — feed SkillRank scores into Prism as prior weights
3. Feedback (cron/event-driven) — extract Prism signals, send to SkillRank

### Key Human Insights

1. **"难道互相之间不会影响对方的权重吗？"** — This single question transformed
   the architecture from a tree (Skillet at root, two leaves) into a cycle
   (Skillet orchestrating a feedback loop between two peers). The insight was
   not technical — it was systemic: any two systems that both contribute to
   "quality assessment" will inevitably have bidirectional influence. Modeling
   this explicitly prevents hidden coupling from emerging later.

2. **Contract placement in Skillet** — When asked "ADR 写到哪里比较好？",
   [Human] deferred to [Agent]'s recommendation to place it in Skillet. The
   reasoning: Skillet is the orchestrator that drives both data flows, so it
   owns the coordination contract. SkillRank and Prism each `@see` the Skillet
   ADR rather than maintaining their own copies. This is a DRY decision at the
   architectural documentation level.

3. **V1 "store only" strategy approved** — [Human] accepted that V1 endpoints
   should accept and persist data without using it in calculations. This
   acknowledges that the *shape* of the interface is the high-value decision,
   while the *logic* can evolve. It also means all three repos can develop
   without blocking each other.

### Downstream Effects

**Extended**: SkillRank ADR-001 — added `POST /signal` as a new input endpoint.
The AGENTS.md now references this ADR with a `@see` link.

**Extended**: Fulmail ADR-001 (Prism extraction) — updated to document the
bidirectional interface: `priorWeight` field on `/ingest` and new
`/graph/hub-signals` endpoint.

**Interacts**: Prism Phase 4 (Agent Subsystem Prompt Redirection) — the
PhysicsSystem will need to incorporate `priorWeight` from SkillRank, and Scout
will need to generate the semantic signals that flow back.

**Interacts**: SkillRank ADR-002 (D1 Schema) — the `signals` table was designed
specifically to store the feedback defined in this contract.

### Open Questions

- **[Unowned]** Feedback loop damping — V2 needs a mechanism to prevent ranking
  oscillation if SkillRank and Prism continuously amplify each other's signals.
- **[Unowned]** Signal freshness — should old signals decay over time, or does
  historical accumulation have value?
- **[Human]** Which signal type to implement first when moving from V1 to V2?
  `citation_density` is likely easiest (pure graph query), but
  `content_quality` may have more immediate impact.
