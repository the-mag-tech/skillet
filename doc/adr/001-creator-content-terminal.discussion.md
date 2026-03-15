# ADR-001 Skillet Creator Content Terminal — Discussion Log

## Session 2026-02-17 — From SkillNet to Skillet: Naming, Scope, and MVP

### Starting Point

After SkillRank's architecture was defined and Prism extraction was planned,
a gap became apparent: neither SkillRank (ranking engine) nor Prism (knowledge
graph) provided a creator-facing product. The question was whether to build
this into an existing repo or create something new.

### Decision Journey

The discussion began with [Human] identifying the need for a creator-facing
terminal service. Initially, [Agent] proposed building this as an extension of
SkillRank, but [Human] immediately corrected: SkillRank is pure infrastructure.
The creator product needs its own repository with its own concerns.

> **[Human]** named the repository "SkillNet" — "拼一个 word 的意思." This was
> a deliberate naming choice emphasizing the network aspect of skill discovery.

[Agent] scaffolded the repository with an ADR, AGENTS.md, and supporting
infrastructure (auto-generated doc indexes, scripts). The technical stack was
defined as similar to SkillHub/EvoMap — an index address that can find
SkillHubs.

> **[Human]** then refined the MVP to be deliberately minimal: "最小可信版本是
> 通过 IM 查找到这个 SkillHub，然后读到里面的 Skills，最后完成内容组织编排，
> 并发布其中的任意一个小环节。" The key word was **任意一个** — completing
> *any single step* of the pipeline validates the architecture.

This was a critical insight: instead of requiring end-to-end functionality for
V1, any single link in the chain (discover → load → organize → publish) is
sufficient proof that the orchestration layer works.

Shortly after scaffolding, [Human] corrected the name:

> **[Human]**: "我说的是叫做 Skillet。"

The rename from SkillNet to Skillet was immediately applied across all files.
The name "Skillet" carries connotations of a cooking pan — a tool that combines
ingredients (skills) into a finished product (content) — fitting the
orchestrator metaphor.

[Agent] then proposed two IM integration paths: building on OpenClaw (existing
IM framework) vs. standalone implementation. The ADR documents both options,
with V1 recommending the OpenClaw path for lower integration cost.

### Key Human Insights

1. **"任意一个小环节"** — The MVP definition as "any single complete step"
   rather than "minimal end-to-end flow" is strategically important. It allows
   validation of the orchestration architecture without requiring all
   dependencies (SkillRank, Prism, platform SDKs) to be production-ready
   simultaneously.

2. **Repository separation** — Insistence that the creator product cannot live
   inside SkillRank or Prism. This three-repo structure (SkillRank for ranking,
   Prism for knowledge, Skillet for product) maintains clean architectural
   boundaries even though it increases coordination cost.

3. **"Skillet" naming** — A single-word name that works as both a brand and a
   metaphor. The cooking analogy (combining raw ingredients into a finished
   dish) maps directly to the product's function (combining skills into
   published content).

### Downstream Effects

**Extended**: Established the three-repo ecosystem architecture:
- SkillRank → pure ranking infrastructure
- Prism → pure knowledge graph infrastructure
- Skillet → product-layer orchestrator

**Interacts**: Led directly to Skillet ADR-002 (Bidirectional Feedback
Contract) — once Skillet's orchestrator role was defined, the need for
SkillRank↔Prism coordination became clear.

**Interacts**: Prism extraction priority was raised — since Skillet depends on
Prism as infrastructure, Prism needed to be extracted from Fulmail first.

### Open Questions

- **[Human]** IM Agent integration path (OpenClaw vs standalone) — deferred
  to implementation phase.
- **[Unowned]** ADR still contains one reference to "SkillNet" in the
  orchestrator table (line 30) — minor cleanup needed.
- **[Human]** Target platforms (小红书, 微信公众号, X, Instagram, Threads) —
  which one for V1 validation?
