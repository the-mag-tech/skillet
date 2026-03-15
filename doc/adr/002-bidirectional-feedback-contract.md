# ADR-002: Bidirectional Feedback Contract between SkillRank and Prism

Status: Proposed
Date: 2026-02-28

> Discussion: [discussion log](002-bidirectional-feedback-contract.discussion.md)

## Context

SkillRank（Hub 排名引擎）和 Prism（知识图谱引擎）最初被设计为独立组件，
由 Skillet 单向调用。但分析后发现它们之间存在双向反馈关系：

- **SkillRank → Prism**：Hub 质量分作为 Skill 实体的先验权重
- **Prism → SkillRank**：语义关联密度、引用图、贡献者声誉作为 Hub 排名信号

这类似于 Google 中 PageRank（链接图）与内容理解（语义分析）的协同关系——
结构排名和语义理解互相增强，而非单向依赖。

## Decision

### 1. 双向数据流定义

```
┌──────────┐   Hub Score (prior weight)    ┌──────────┐
│          │ ──────────────────────────▶   │          │
│ SkillRank│                               │  Prism   │
│          │ ◀──────────────────────────   │          │
└──────────┘   Semantic Signals            └──────────┘
```

### 2. SkillRank 侧接口

#### 输出（已有）：Hub 质量分

```
GET /rank?hubId={id}

Response:
{
  "hubId": "clawhub",
  "score": 0.87,
  "signals": {
    "api_freshness": 0.92,
    "skill_count": 156,
    "sdk_available": true,
    "uptime_30d": 0.997
  }
}
```

#### 输入（新增）：接收语义反馈信号

```
POST /signal

Body:
{
  "hubId": "clawhub",
  "signals": [
    {
      "type": "citation_density",
      "value": 0.73,
      "metadata": { "sample_size": 42 }
    },
    {
      "type": "contributor_reputation",
      "value": 0.85,
      "metadata": { "unique_contributors": 12 }
    },
    {
      "type": "content_quality",
      "value": 0.68,
      "metadata": { "avg_structure_score": 0.68 }
    }
  ],
  "source": "prism",
  "timestamp": "2026-02-28T12:00:00Z"
}

Response: 202 Accepted
```

**V1 行为**：记录信号到 D1，不参与排名计算。V2 将信号纳入 PageRank 权重。

#### 信号类型枚举

| Signal Type | 描述 | 来源 |
|-------------|------|------|
| `citation_density` | Hub 内 Skill 被其他 Skill 引用的密度 | Prism 图谱查询 |
| `contributor_reputation` | Hub 贡献者在全局图谱中的声誉 | Prism 实体分析 |
| `content_quality` | Skill Markdown 的结构完整性评分 | Prism 摄入时计算 |
| `fork_inbound` | 其他 Hub 的 Skill fork 自此 Hub 的数量 | Prism 关系提取 |
| `semantic_uniqueness` | Hub 内 Skill 的语义独特性（非重复度） | Prism 向量比较 |

### 3. Prism 侧接口

#### 输出（新增）：语义密度查询

```
GET /graph/hub-signals?hubId={id}

Response:
{
  "hubId": "clawhub",
  "citation_density": 0.73,
  "contributor_reputation": 0.85,
  "content_quality": 0.68,
  "fork_inbound": 7,
  "semantic_uniqueness": 0.81
}
```

#### 输入（扩展）：摄入时接受先验权重

```
POST /ingest

Body:
{
  "url": "https://clawhub.dev/skills/auto-doc-index",
  "title": "Auto Doc Index",
  "content": "...",
  "priorWeight": 0.87,       // ← 来自 SkillRank 的 Hub 质量分
  "priorSource": "skillrank"  // ← 标记来源
}
```

**V1 行为**：`priorWeight` 字段存入数据库但不影响图谱权重计算。
V2 将其作为实体初始 PageRank 值的乘数因子。

### 4. Skillet 编排逻辑

Skillet 负责驱动双向数据流的时序：

```
Phase 1: Discovery（用户触发）
  Skillet → SkillRank: GET /rank           → 获取 Hub 排名
  Skillet → SkillRank: GET /search         → 搜索 Skill

Phase 2: Ingestion（后台异步）
  Skillet → SkillRank: GET /rank?hubId=X   → 获取 Hub Score
  Skillet → Prism: POST /ingest { priorWeight: hubScore }
                                           → 摄入 Skill，附带先验权重

Phase 3: Feedback（定时任务 / 事件驱动）
  Skillet → Prism: GET /graph/hub-signals?hubId=X
                                           → 获取语义信号
  Skillet → SkillRank: POST /signal        → 反馈语义信号
```

### 5. V1 实现策略

**核心原则**：接口形状先定，逻辑后填。

| 组件 | V1 实现 | V2 演进 |
|------|---------|---------|
| SkillRank `/signal` | 接收并存储，不参与排名 | 信号纳入 PageRank 计算 |
| Prism `priorWeight` | 接收并存储，不影响图谱 | 作为实体初始权重乘数 |
| Prism `/graph/hub-signals` | 基于已有图谱数据计算 | 增加缓存 + 增量更新 |
| Skillet Phase 3 | 手动触发或 cron | 事件驱动（Skill 变更时自动触发） |

## Consequences

### Benefits
- 接口预留使得 V1→V2 演进只需改内部逻辑，不改 API 形状
- 每个组件可以独立开发和测试，只需 mock 对方的接口
- 双向反馈使排名和图谱互相增强，形成数据飞轮

### Costs
- 每个组件多一个"空壳"端点的维护成本（极低）
- Skillet 的编排逻辑复杂度增加（但这本来就是它的职责）

### Risks
- 反馈环如果设计不当可能导致排名震荡（V2 需要阻尼机制）
- 信号类型枚举需要随着理解加深而扩展

## Cross-References

- SkillRank: `@see skillet/doc/adr/002-bidirectional-feedback-contract.md`
- Prism: `@see skillet/doc/adr/002-bidirectional-feedback-contract.md`
- Skillet ADR-001: `doc/adr/001-creator-content-terminal.md`
