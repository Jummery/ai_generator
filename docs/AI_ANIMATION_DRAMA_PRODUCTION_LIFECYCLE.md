# AI 漫剧生产全生命周期方案

## 1. 定位

`ai_generator` 是基于第三方云端 AI 能力的漫剧生产编排平台。

明确边界：
- 不训练基础模型
- 不做本地模型部署
- 不建设 GPU 推理集群
- 不绑定单一 AI 厂商
- 通过 Provider Gateway 统一调用云端 LLM、图片、视频、TTS、音乐等能力

核心原则：**Agent 负责决策，Workflow 负责执行，Shot 负责恢复。**

## 2. 从创意到成片

```text
Idea
 ↓
Project Brief
 ↓
Story Bible
 ↓
Character / Location / Prop Bible
 ↓
Season Plan
 ↓
Episode Script
 ↓
Scene Breakdown
 ↓
Storyboard / Shot Plan
 ↓
Asset Preparation
 ↓
Key Frame
 ↓
Video Shot
 ↓
Voice / BGM / SFX
 ↓
Timeline
 ↓
Episode Render
 ↓
AI + Media QC
 ↓
Human Review
 ↓
Publish Package
```

## 3. Phase 0：创意输入

用户提供：故事创意、题材、受众、预计集数、单集时长、画幅和视觉偏好。

AI Director 输出结构化 Project Brief：
- Logline
- 核心卖点
- 世界观方向
- 主线冲突
- 人物初稿
- 视觉方向
- 季/集规划建议

用户确认项目方向后进入下一阶段。

## 4. Phase 1：Story Bible

建立项目级长期约束：

```text
世界观 / 时代 / 核心规则
故事主线 / 主冲突 / 人物关系
叙事风格 / 视觉风格 / 语言风格
```

Story Bible 必须版本化，例如 `v1.0`。后续修改创建新版本，不覆盖历史版本。所有 Episode、Scene、Shot 都必须记录所依赖的版本。

## 5. Phase 2：资产圣经

先建立可复用资产，再批量生成镜头。

### Character

```text
身份 / 年龄 / 外貌 / 发型 / 服装
性格 / 行为习惯 / 人物关系
Reference Images / Voice Profile
```

### Location

```text
地点 / 空间类型 / 固定元素
视觉风格 / 光照规则 / 时间状态 / Reference
```

### Prop

```text
名称 / 用途 / 外观 / 关键特征 / Reference
```

资产状态：`Draft → Approved → Locked → Archived`。

只有 Approved / Locked 资产进入批量镜头生产。

## 6. Phase 3：Season / Episode Plan

将完整故事拆成季和集。

每集至少包含：
- 标题
- 剧情摘要
- 本集目标
- 核心冲突
- 关键人物
- 关键场景
- 开场 Hook
- 高潮
- 结尾悬念

短视频默认节奏：

```text
0-3s      强 Hook
3-20s     建立冲突
20-80s    推进剧情
80-110s   高潮/反转
110-120s  悬念/下一集钩子
```

实际时间由项目配置决定。

## 7. Phase 4：Episode Script

由 Script Agent 生成：

```text
Narration
Dialogue
Action
Emotion
Scene
Character
Transition
```

人工审核重点：剧情逻辑、人物动机、对白、节奏、Hook、结尾悬念、Story Bible 一致性。通过后锁定 Script Version。

## 8. Phase 5：Scene Breakdown

将剧本拆成连续场景。Scene 保存：

- 时间
- 地点
- 人物
- 剧情目的
- 情绪
- 环境
- 连续性约束

## 9. Phase 6：Storyboard / Shot Plan

**Scene 是创作单元，Shot 是生产单元。**

例如：

```text
Scene 01
├── Shot 001 全景
├── Shot 002 人物近景
├── Shot 003 手部特写
├── Shot 004 屏幕特写
└── Shot 005 眼神特写
```

Shot 定义：
- 景别、机位、镜头运动、构图
- 人物、动作、表情、场景、道具
- 台词、时长
- Visual Prompt / Negative Prompt
- Reference Assets
- 目标质量等级

## 10. Phase 7：AI Router

Shot 进入生产后：

```text
Shot Requirement
 ↓
Capability Detection
 ↓
Quality / Cost Policy
 ↓
Provider Selection
 ↓
Model Selection
 ↓
Generation Request
```

决策因素：任务类型、质量要求、人物一致性、镜头重要度、预算、速度、Provider 可用性和限流状态。

业务层不能直接绑定具体厂商。

## 11. Phase 8：Asset Preparation

生产 Shot 前检查：

```text
Character Reference
Location Reference
Prop Reference
Style Reference
Voice Profile
```

不存在的资产先生产并 QC；已 Locked 的资产直接复用。

## 12. Phase 9：Key Frame

优先生成关键帧，再进入视频生成：

```text
Shot
 ↓
Prompt Assembly
 ↓
Image Provider
 ↓
Key Frame
 ↓
Visual QC
```

检查人物身份、人脸、服装、场景、构图、风格、道具和镜头要求。

失败只重新生成当前 Shot。

## 13. Phase 10：Video Shot

Key Frame 通过后生成视频。支持根据 Provider 能力选择：

```text
Image → Video
Text → Video
Reference → Video
First Frame → Video
First/Last Frame → Video
```

保存：Provider、Model、Prompt、References、Parameters、Generation ID、Cost、Duration、Status。

## 14. Phase 11：Voice / Music / SFX

声音与视频可以并行生成。

```text
Character → Voice Profile → TTS → Dialogue Audio
Scene/Episode → Music
Shot Event → SFX
```

音频必须最终绑定时间轴，而不是只保存一个 Episode 音频文件。

## 15. Phase 12：Timeline / Render

时间轴：

```text
Video Track
Voice Track
BGM Track
SFX Track
Subtitle Track
```

完成镜头排序、音频对齐、字幕时间轴、BGM Ducking、SFX 混音和转场，然后生成 Episode Master。

同时生成字幕、音频、封面候选图和发布元数据。

## 16. Phase 13：QC

### Media QC

检查视频解码、音频播放、时长、黑帧、静帧、花屏、音画同步、字幕越界和文件完整性。

### AI QC

检查人物一致性、场景连续性、动作合理性、镜头连续性、对白画面匹配、剧情一致性和内容安全。

结果：`PASS / WARN / FAIL`。

## 17. Phase 14：Human Review

用户可以播放整集并定位到具体 Shot，查看生成来源，修改 Prompt / 资产 / 参数，只重新生成当前 Shot，再重新 Render。

核心原则：**局部修复，不因一个镜头的问题重新生产整集。**

## 18. Phase 15：Publish Package

最终发布包：

```text
Episode Master
Subtitle
Cover
Description
Tags
Audio
Metadata
Generation Provenance
Cost Report
QC Report
```

支持按平台输出不同画幅、清晰度、码率和字幕版本。

## 19. Phase 16：数据闭环

持续统计：

```text
Episode Cost
Shot Cost
Provider Success Rate
Provider Latency
Retry Rate
QC Failure Rate
Human Edit Rate
Adoption Rate
```

这些数据反向优化 AI Router，实现质量、成本和速度的动态平衡。

## 20. Shot 状态机

```text
DRAFT
 ↓
READY
 ↓
ASSET_READY
 ↓
KEYFRAME_GENERATING
 ↓
KEYFRAME_REVIEW
 ↓
VIDEO_GENERATING
 ↓
VIDEO_REVIEW
 ↓
AUDIO_READY
 ↓
SHOT_READY
 ↓
QC
 ↓
APPROVED
```

失败从当前阶段恢复：

```text
KEYFRAME_FAILED → KEYFRAME_GENERATING
VIDEO_FAILED    → VIDEO_GENERATING
AUDIO_FAILED    → AUDIO_GENERATING
QC_FAILED       → 对应阶段
```

## 21. 人工与 AI 的职责

### AI 自动化

剧本初稿、分集规划、Scene/Shot 拆分、Prompt、Provider 选择、普通镜头生成、TTS、字幕、QC 初检。

### 人工决策

故事方向、核心角色、视觉风格、关键剧情、关键镜头、失败镜头和最终成片。

目标不是零人工，而是：

> **AI 负责规模化生产，人负责创作控制权。**

## 22. 50 集生产模型

```text
Project
 ↓
Story Bible
 ↓
20～50 个核心 Character / Location / Prop
 ↓
50 Episodes
 ↓
每集 5～15 Scenes
 ↓
每 Scene 3～10 Shots
```

因此系统从第一天就必须支持：Shot 并行、Shot 重试、Shot 成本、Shot QC、Shot 版本、Shot 缓存和 Shot 人工审核。

## 23. 最终闭环

```text
创意
 ↓
AI Director
 ↓
Story Bible
 ↓
Character Bible
 ↓
Episode Plan
 ↓
Script
 ↓
Storyboard
 ↓
Shot
 ↓
AI Router
 ↓
Cloud AI Providers
 ↓
Image / Video / Voice
 ↓
QC Loop
 ├── FAIL → Retry
 └── PASS → Timeline
                 ↓
               Render
                 ↓
            Human Review
                 ↓
              Publish
                 ↓
           Metrics / Cost
                 ↓
          Router Optimization
```

## 24. 核心原则

1. 创意层与生产层分离。
2. Shot 是最小可恢复生产单元。
3. 云端模型可替换。
4. 角色、场景、道具、声音必须长期复用并版本化。
5. Agent 做规划和决策，Workflow 做可靠执行。
6. 最终媒体必须可追溯到 Prompt、资产、Provider、Model、参数和成本。
7. 质量和成本同时优化。
8. 人拥有最终发布权。
