# ADR-001: Skillet — Creator Content Terminal Architecture

Status: Proposed
Date: 2026-02-28

> Discussion: [discussion log](001-creator-content-terminal.discussion.md)

## Context

自媒体创作者需要一个统一的入口来完成内容生产的完整工作流：
发现能力（Skill）→ 组织内容 → 编排 → 发布到多平台。

当前生态中已有两个独立的基础设施：
- **SkillRank**：Hub 排名引擎，能够索引和排序 Skill Hub
- **Prism**：知识图谱引擎，能够摄入 Markdown、提取实体、图谱检索

缺少的是**产品层**——将这些基础设施组装起来，通过 IM Agent 接口
向创作者提供端到端服务的终端。

## Decision

### Skillet 定位

Skillet 是**产品层 orchestrator**，不重复实现底层能力：

| 能力 | 由谁提供 | Skillet 的角色 |
|------|---------|----------------|
| Hub 发现与排名 | SkillRank | 调用 API，呈现结果 |
| 内容存储与检索 | Prism | 调用 API，读写图谱 |
| AI 能力执行 | Skill 本身 | 加载并执行 Skill |
| 多平台发布 | 平台 SDK | 封装适配层 |
| IM Agent 交互 | SkillNet 自身 | 核心自有能力 |

### 核心流程

```
1. Hub Discovery
   用户: "帮我找小红书相关的 Skill"
   Skillet → SkillRank API: GET /search?platform=xiaohongshu
   SkillRank → 返回排名后的 Hub + Skill 列表

2. Skill Loading
   用户: "用这个 Skill"
   Skillet → 从 Hub 下载/缓存 SKILL.md
   Skillet → Prism: ingestFinding(skillUrl, title, content)
   Prism → 提取结构化元数据，存入图谱

3. Content Organization
   用户: "帮我写一篇关于 XX 的小红书笔记"
   Skillet → 执行 Skill 的 Prompt 模板
   Skillet → Prism: recall("XX 相关素材")
   Skillet → 组合素材 + Skill 输出 → 编排内容

4. Publishing
   用户: "发布到小红书"
   Skillet → 平台 SDK: 格式适配 + API 调用
   Skillet → 反馈发布结果
```

### V1 MVP

完成上述流程中的**任意一个完整环节**即可验证：
- 环节 1：通过 IM 成功发现并列出 Hub 中的 Skill
- 环节 2：成功加载一个 Skill 并执行其核心能力
- 环节 3：成功生成一篇符合平台规范的内容
- 环节 4：成功发布到任一平台

### V1 目标平台

图文优先：小红书、微信公众号、X、Instagram、Threads

### IM Agent 方案

两种路径：
- **A. 基于 OpenClaw**：利用已有的 IM Agent 框架，作为 OpenClaw 的一个 Skill/Extension
- **B. 独立实现**：自建轻量 IM 适配层（Telegram Bot API / WhatsApp Business API）

V1 建议先走路径 A（OpenClaw），降低 IM 接入成本。

## Consequences

### Benefits
- 创作者通过已熟悉的 IM 工具即可使用，零学习成本
- 底层能力由 SkillRank + Prism 提供，Skillet 只做编排
- 平台 SDK 封装为独立适配层，新增平台只需加适配器
- 基于 Skill 生态，能力可以由社区贡献和迭代

### Costs
- 需要维护 5 个平台的发布适配器
- IM Agent 接入本身有一定工程量
- 依赖 SkillRank 和 Prism 的 API 稳定性

### Risks
- 平台 API 政策变更可能导致发布功能中断
- Skill 质量参差不齐，需要 Curator 机制兜底
- IM Agent 的交互体验受限于平台能力（如微信对 Bot 的限制）
