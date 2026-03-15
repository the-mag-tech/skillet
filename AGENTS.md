# AGENTS.md — Skillet

## What is Skillet

Skillet 是 **Skill Hub / Skill Bundle 发现与搜索引擎**。

- 服务对象：Creator / Designer / Prosumer
- 产品角色：后端市场能力（供 OpenClaw 等入口调用）
- 非目标：不做 IM 入口封装，不做内容执行器，不做发布器

一句话：**Skillet 帮用户找到最合适的 Skill/Bundle，不负责替用户执行内容工作流。**

## Mandatory Rules

1. **ADR governance**: Every architectural decision in `doc/adr/`.
   ADRs are immutable once decided. Use a new ADR to supersede old decisions.
2. **Auto-generated indexes**: Never hand-edit between
   `<!-- INDEX:START -->` / `<!-- INDEX:END -->` markers.
3. **TDD**: Write failing tests first, then implement, then refactor.
4. **DRY**: Single authoritative representation for every piece of knowledge.
5. **Separation of concerns**:
   - SkillRank = ranking signals
   - Prism = graph semantics
   - Skillet = discovery/search product layer
6. **Discussion-first ADR flow**: 每个 ADR 必须包含
   `> Discussion: [discussion log](xxx.discussion.md)`。
7. **Governance gate**: 交付前必须通过 `pnpm doc:governance`。

## Architecture

```
User Native Entry (IM/Web/CLI)
            │
            ▼
┌──────────────────────────────────────────────────────────┐
│ Skillet (Discovery/Search Layer)                        │
│                                                          │
│  Ingest → Normalize → Index → Retrieve → Rank → Explain │
└───────────────┬───────────────────────────────┬──────────┘
                │                               │
                ▼                               ▼
          SkillRank API                    Prism API
       (hub/domain signals)             (semantic signals)
```

IM 是入口渠道，不是 Skillet 产品边界内模块。

## Dependencies

| Service | Role |
|---------|------|
| **SkillRank** | Hub/Domain ranking and quality signals |
| **Prism** | Semantic graph and relevance augmentation |

## V1 MVP Scope

聚焦发现与搜索闭环：

1. 聚合多个 Hub 的 Skill/Bundle
2. 支持意图检索与过滤
3. 输出可执行的结果卡（链接、适用场景、排序理由）

## Reference Sub-Documents

| Document | Path | When to read |
|----------|------|--------------|
| Architecture details | `doc/agents/architecture.md` | Module design, data flow |
| Known issues | `doc/agents/known-issues.md` | Debugging, workarounds |
| DX Tooling | `doc/agents/dx-tooling.md` | Skills, commands, doc governance |

## Build & Dev

```bash
pnpm install
pnpm dev
pnpm test
pnpm doc:governance
```
