# AI Director Agent Orchestration

## 1. 定位

AI Director 是个人 AI 漫剧工厂的创作总导演，不直接绑定具体 AI Provider，也不直接负责最终渲染。

它负责把用户的自然语言创意逐层转换成稳定的生产计划：

```text
Idea
 ↓
Story Plan
 ↓
Episode Plan
 ↓
Scene Plan
 ↓
Shot Plan
 ↓
Generation Tasks
```

核心原则：

> Agent 负责思考，Workflow 负责执行，Human 负责关键决策。

## 2. Agent 分工

```text
AI Director
│
├── Story Planner
├── Episode Planner
├── Scene Planner
├── Shot Planner
├── Character Director
├── Prompt Director
├── Budget Director
└── QC Director
```

### Story Planner

负责：

- 故事主题
- 世界观
- 主线冲突
- 角色关系
- 故事弧
- Season 方向

输出 Story Bible。

### Episode Planner

负责：

- Episode 目标
- 本集冲突
- 情绪曲线
- 开场 Hook
- 中段升级
- 结尾 Hook

### Scene Planner

负责将 Episode 拆解为连续的 Scene，并保证时间、空间、人物状态连续。

### Shot Planner

负责生成 Shot Specification，包括：

- 景别
- 机位
- 镜头运动
- 动作
- 表情
- 环境
- 对白
- 时长
- Generation Mode

### Character Director

负责角色引用、Character Version、服装状态与角色连续性约束。

### Prompt Director

将结构化 Shot Specification 转换成 Provider 可执行 Prompt，不允许业务层直接依赖 Provider Prompt 格式。

### Budget Director

根据 Episode Budget 为每个 Shot 选择：

```text
STATIC
MOTION_COMIC
IMAGE_TO_VIDEO
TEXT_TO_VIDEO
```

并阻止超出预算的升级。

### QC Director

负责生成前检查与生成后审核规则，包括：

- Character consistency
- Location continuity
- Narrative continuity
- Visual quality
- Audio timing
- Duration

## 3. Agent 协作原则

Agent 之间不通过自由文本无限传递，而使用结构化 Artifact。

```text
Story Bible
Episode Plan
Scene Plan
Shot Specification
Character Reference
Prompt Package
QC Result
```

这样可以减少幻觉、格式漂移和上下文污染。

## 4. Human Approval Gates

并非所有步骤都需要人工确认。

### 自动通过

- 普通 Scene 拆解
- 普通 Shot 拆解
- Prompt 生成
- 免费模式选择
- 常规 QC

### 建议人工确认

- Story Bible
- Character Reference
- Episode Plan
- 第一集完整 Storyboard
- 高成本 Shot

### 必须人工决定

- 主角重大视觉修改
- 主线剧情改变
- Episode 结局改变
- 超出预算的付费生成

## 5. State Machine

AI Director 不直接控制整个系统，而驱动状态转换：

```text
DRAFT
 ↓
PLANNING
 ↓
AWAITING_REVIEW
 ↓
APPROVED
 ↓
PRODUCTION
 ↓
QC
 ↓
PUBLISHED
```

如果失败：

```text
FAILED
 ↓
REPLAN / RETRY
```

重试必须尽可能从失败节点恢复，而不是重新运行整个项目。

## 6. Context Strategy

不同 Agent 只读取必要上下文。

### Story Planner

读取：Idea + Project Brief。

### Episode Planner

读取：Story Bible + Season Plan。

### Scene Planner

读取：Episode Plan + Character/Location Bible。

### Shot Planner

读取：Scene Plan + Character/Location State。

### Prompt Director

读取：Shot Specification + Style + References。

### Budget Director

读取：Shot Specification + Provider Matrix + Episode Budget。

### QC Director

读取：Generated Artifact + Specification + Reference。

避免所有 Agent 都携带完整项目上下文。

## 7. Deterministic Workflow

Agent 输出必须经过 Workflow 校验：

```text
Agent
 ↓
Schema Validation
 ↓
Business Validation
 ↓
Budget Validation
 ↓
Human Gate（必要时）
 ↓
Task Queue
```

Agent 不能直接绕过校验调用高成本 Provider。

## 8. Regeneration Policy

当用户提出“这个镜头不好”时，Director 必须判断问题层级：

### Visual Problem

只重新生成当前 Shot。

### Character Problem

重新生成当前 Shot，并复用 Character Reference。

### Continuity Problem

检查前后 Shot，必要时重排局部 Shot。

### Story Problem

回到 Scene / Episode Planner，而不是直接重新生成视频。

## 9. Budget Escalation

Provider 升级需要明确理由：

```text
Current Mode
 ↓
Does it satisfy narrative requirement?
 ↓ YES → Keep
 ↓ NO
Can Motion Comic solve it?
 ↓ YES → Motion Comic
 ↓ NO
Can low-cost Image-to-Video solve it?
 ↓ YES → Image-to-Video
 ↓ NO
High-quality Video AI
```

高成本生成必须通过 Budget Gate。

## 10. Memory / Continuity

长期记忆不是聊天记录，而是结构化生产资产：

```text
Story Bible
Character Bible
World Bible
Episode State
Shot History
QC History
Provider History
```

Agent 每次重新运行都优先读取这些结构化资产。

## 11. Prompt Versioning

每次 Prompt Director 生成 Prompt 都记录：

- prompt_version
- source_spec_version
- style_version
- character_version
- provider
- model
- generation_parameters

这样可以复现历史生成任务。

## 12. Failure Handling

### LLM 规划失败

重新执行当前 Agent。

### Schema 错误

自动修复一次，再进入人工审核。

### Provider 失败

切换同能力 Provider 或降级 Generation Mode。

### QC 失败

只重新生成失败 Shot。

### 预算不足

自动降级，不允许静默增加成本。

## 13. Personal Free-First Policy

这是个人生产工具，不以 SaaS 商业化为目标。

AI Director 的优化目标依次为：

```text
完成整集
 >
免费/低成本
 >
角色一致性
 >
画面质量
 >
生成速度
```

但对于用户明确标记的关键镜头，可以主动提高质量等级。

## 14. First Episode Execution

第一集建议执行：

```text
User Idea
 ↓
Story Planner
 ↓
Episode Planner
 ↓
Scene Planner
 ↓
Shot Planner
 ↓
Character Director
 ↓
Budget Director
 ↓
Prompt Director
 ↓
Generation
 ↓
QC Director
 ↓
Timeline
```

第一集默认目标：约 120 秒、约 16 个核心 Shot，并优先采用免费/低成本 Generation Mode。

## 15. 关键原则

1. Agent 不直接操作最终媒体资产。
2. Agent 不直接绕过 Budget Gate。
3. Agent 不直接修改已批准的历史资产。
4. 任何重大剧情修改都必须产生新版本。
5. 所有生成任务都必须可追溯到 Shot Specification。
6. 所有高成本任务都必须可解释。
7. 单 Shot 失败不得导致整个 Episode 重新生产。
