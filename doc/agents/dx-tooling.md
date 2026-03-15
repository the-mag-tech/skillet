# DX Tooling — Skillet

## Auto-Generated Doc Indexes

```bash
pnpm gen:index
```

**Rule**: Never hand-edit between `<!-- INDEX:START/END -->` markers.

## Documentation Governance — Three-Layer Model

| Layer | Content | Source of Truth |
|-------|---------|-----------------|
| **Structural** | Doc indexes, dependency graph | Auto-generated |
| **Semantic** | In-code `@see ADR-NNN` annotations | Code itself |
| **Rationale** | ADRs, Pitfalls | `doc/adr/`, `doc/pitfall/` |
