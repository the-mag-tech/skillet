# ADR-003: Skillet as Skill Hub/Bundle Discovery and Search Backend

Status: Proposed
Date: 2026-03-15

> Discussion: [discussion log](003-skillet-as-discovery-search-backend.discussion.md)
> Optional evidence: [market and boundary research](../research/2026-03-15-skillet-boundary-and-market-research.md)

## Context

此前文档将 Skillet 定位为“创作者内容终端”，并将 IM 入口、内容执行、发布
都纳入核心边界。这会导致职责扩张，削弱系统可演进性：

- 发现/搜索问题与执行/发布问题耦合
- 与 OpenClaw 等入口层职责重叠
- 难以聚焦 Skill/Bundle 市场本体价值

实际共识是：IM 作为用户原生入口，Skillet 作为后端市场能力供其调用。

## Decision

将 Skillet 重新定义为：

**Skill Hub / Skill Bundle 发现与搜索引擎（Backend Market Layer）**

### In Scope

1. 多 Hub 数据聚合（catalog ingestion）
2. Skill/Bundle 元数据规范化（canonical schema）
3. 检索与排序（query + ranking）
4. 结果解释（why this result）
5. 为 OpenClaw/Web/CLI 提供统一发现能力

### Out of Scope (V1)

1. IM runtime / bot gateway
2. 内容生成执行
3. 发布到内容平台

### Layering

- **SkillRank**: Hub/Domain ranking signals
- **Prism**: semantic/graph signals
- **Skillet**: productized discovery/search layer
- **OpenClaw (or other entries)**: user-facing entry runtime

## Consequences

### Benefits

- 边界清晰，系统复杂度显著下降
- V1 可先交付“可用搜索结果”，降低实现风险
- 与 OpenClaw 形成上下游关系，避免能力重叠
- 更容易扩展到多入口（IM/Web/CLI）

### Costs

- 短期内不提供端到端执行体验
- 需要定义更清晰的结果卡标准（可安装、可购买、可调用）

### Risks

- 若结果质量不足，用户会感知“只会搜不会干”
- 多 Hub 元数据质量参差，规范化成本可能上升

## Supersedes

This ADR supersedes the product positioning parts of ADR-001.
