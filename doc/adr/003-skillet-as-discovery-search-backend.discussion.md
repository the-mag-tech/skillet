# ADR-003 Skillet Discovery/Search Backend — Discussion Log

## Session 2026-03-15 — Clarifying Product Boundary

### Starting Point

Skillet was documented as a creator execution terminal, combining discovery,
execution, and publishing in one boundary. This made the scope broad and
blurred the separation with OpenClaw entry channels.

### Decision Journey

The discussion started from a direct correction by [Human]: Skillet should not
occupy the IM runtime layer. Instead, IM should remain the native user entry,
while Skillet serves as backend market capability.

> **[Human]** emphasized: IM is the user-native entry; Skillet is the backend
> bundle market for agent consumers.

This shifted the framing from "terminal product" to "discovery/search backend."
[Agent] then translated that boundary into concrete architecture impact:
discovery/search stays in Skillet; runtime entry, content execution, and
publishing move out of Skillet V1 scope.

> **[Human]** further constrained execution: do not draft API contracts yet;
> prioritize product/doc positioning alignment first.

That constraint changed delivery order. Instead of deepening technical
contracts, the session focused on ADR-first governance updates so future
implementation starts from a stable product boundary.

### Key Human Insights

1. **Boundary-first correction**
   - **What**: Skillet is backend market layer, not IM shell.
   - **Why it mattered**: Prevented scope inflation and channel coupling.
   - **Non-obvious aspect**: Entry UX and search relevance are different systems
     that scale differently.

2. **IM as native channel, not product core**
   - **What**: Keep IM outside Skillet runtime boundary.
   - **Why it mattered**: Preserves channel-agnostic reuse (IM/Web/CLI).
   - **Non-obvious aspect**: Reduces accidental lock-in to one interaction form.

3. **ADR-led sequencing**
   - **What**: Update ADR/product docs before API specs.
   - **Why it mattered**: Locks strategic intent before interface churn.
   - **Non-obvious aspect**: Documentation order controls implementation drift.

### Downstream Effects

- **Superseded**: ADR-001 positioning content is superseded by ADR-003.
- **Interacts**: ADR-002 remains valid as system-level contract context, but no
  longer implies Skillet owns runtime execution/publishing.
- **Validated**: Research can be attached as evidence, but ADR discussion log is
  the primary decision record.

### Open Questions

- Which 2-3 hubs should be first-class in V1 ingestion?
- Should V1 optimize for bundle-first ranking over single-skill ranking?
- What minimum explanation fields are required in result cards?
