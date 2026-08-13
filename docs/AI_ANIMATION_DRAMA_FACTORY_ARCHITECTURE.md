# AI 漫剧制作工厂架构方案

## 1. 项目定位

`ai_generator` 定位为一个可编排、可扩展、可替换模型供应商的 AI 漫剧生产平台。

核心目标不是简单的“文生图/文生视频工具”，而是把一部漫剧拆解成可追踪的生产流水线：

> 创意 → 世界观 → 剧本 → 分集 → 分镜 → 角色/场景资产 → 关键帧 → 动态镜头 → 配音 → 音效/BGM → 字幕 → 合成 → 质检 → 发布

系统应支持单集短剧，也支持几十至几百集的连续剧项目。

## 2. 产品目标

### 2.1 MVP

- 输入一句故事创意，生成可编辑的剧本与分镜。
- 自动建立角色、场景、道具资产库。
- 基于统一的角色 ID / 场景 ID 保持视觉一致性。
- 通过 Provider 抽象接入不同文生图、图生视频、TTS、LLM 服务。
- 自动生成镜头级任务并支持失败重试。
- 最终输出视频、字幕、配音和项目元数据。

### 2.2 中长期

- 多 Agent 漫剧生产流水线。
- 本地模型与云模型混合推理。
- GPU Worker 集群。
- 项目级资产记忆与风格记忆。
- 自动镜头连续性检查。
- 自动口型/动作/音画同步。
- 多平台发布规格适配。

## 3. 总体架构

```text
┌──────────────────────────────────────────────────────────┐
│                    AI 漫剧 Studio                         │
│  Project / Episode / Script / Storyboard / Timeline      │
└────────────────────────────┬─────────────────────────────┘
                             │
┌────────────────────────────▼─────────────────────────────┐
│                 Orchestrator / Workflow                  │
│  Job Queue · DAG · Retry · Checkpoint · Cost · Progress │
└───────┬───────────────┬───────────────┬──────────────────┘
        │               │               │
        ▼               ▼               ▼
┌──────────────┐ ┌──────────────┐ ┌──────────────────────┐
│ Story Agent  │ │ Visual Agent │ │ Audio Agent          │
│ 剧本/分镜     │ │ 角色/场景/镜头 │ │ TTS/BGM/SFX/字幕     │
└──────┬───────┘ └──────┬───────┘ └──────────┬───────────┘
       │                │                    │
       └────────────────┼────────────────────┘
                        ▼
              ┌───────────────────┐
              │ Provider Gateway   │
              │ LLM/Image/Video/   │
              │ TTS/Embedding      │
              └─────────┬─────────┘
                        ▼
              ┌───────────────────┐
              │ Asset / Media      │
              │ Object Storage     │
              └─────────┬─────────┘
                        ▼
              ┌───────────────────┐
              │ Render / FFmpeg    │
              │ QC / Export        │
              └───────────────────┘
```

## 4. 核心领域模型

### Project

一个完整漫剧项目，包含世界观、风格、角色、场景、剧集和生产配置。

关键字段：

- `projectId`
- `title`
- `genre`
- `visualStyle`
- `globalPrompt`
- `negativePrompt`
- `status`

### Character

角色是视觉一致性的核心实体。禁止只依赖自然语言角色名称。

关键字段：

- `characterId`
- `name`
- `description`
- `appearance`
- `personality`
- `voiceProfile`
- `referenceAssets[]`
- `styleProfile`

### Location / Prop

场景和关键道具同样需要稳定 ID 与参考资产。

### Episode

剧集负责组织一组镜头，并保存剧本版本、分镜版本和渲染状态。

### Shot

Shot 是生产系统最重要的执行单元。

建议字段：

```text
shotId
sequence
duration
camera
characters[]
locationId
propIds[]
action
emotion
dialogue
visualPrompt
negativePrompt
referenceAssets[]
imageTaskId
videoTaskId
audioTaskId
renderStatus
```

## 5. 标准生产流水线

### Phase 01：创意解析

输入：一句话故事、题材、目标时长、受众。

输出：

- Logline
- 世界观
- 主线冲突
- 角色关系
- 视觉风格
- 第一季结构

### Phase 02：剧本生成

LLM 生成：

- Episode
- Scene
- Dialogue
- Action
- Emotion
- Narration

所有内容必须结构化为 JSON，避免直接把自然语言作为后续任务输入。

### Phase 03：分镜生成

将 Scene 拆解成 Shot，并生成：

- 景别
- 镜头运动
- 机位
- 构图
- 人物动作
- 表情
- 环境
- 时长
- 台词

### Phase 04：资产生产

先生产资产，再生产镜头。

```text
Character → Character Reference Sheet
Location  → Location Reference
Prop      → Prop Reference
Style     → Style Reference
```

镜头任务引用资产 ID，而不是重复描述全部人物外观。

### Phase 05：关键帧生成

每个 Shot 先生成关键帧，执行视觉质量检查后再进入视频生成。

### Phase 06：动态镜头

支持：

- Image-to-Video
- Text-to-Video
- Motion Control
- Camera Motion

视频生成任务必须保存输入图片、Prompt、模型、参数和 seed，确保可复现。

### Phase 07：音频

并行生成：

- 角色 TTS
- Narration
- SFX
- BGM

声音必须绑定到 Shot / Dialogue，而不是只绑定到 Episode。

### Phase 08：字幕与合成

使用 FFmpeg 进行最终编排：

```text
Video Track
+ Voice Track
+ BGM Track
+ SFX Track
+ Subtitle Track
        ↓
     Master Video
```

### Phase 09：自动质检

至少检查：

- 视频是否可解码
- 音视频时长是否一致
- 黑帧/静帧异常
- 字幕是否越界
- TTS 是否缺失
- Shot 是否缺失
- 角色视觉一致性
- 内容安全策略

## 6. Agent 设计

建议初期采用“Agent + Workflow”的混合架构，而不是让 Agent 自由调用所有工具。

### Director Agent

负责项目级规划和生产调度。

### Script Agent

负责故事、剧本、对白、旁白。

### Storyboard Agent

负责 Scene → Shot。

### Character Agent

负责角色设计、参考图和角色一致性约束。

### Visual Prompt Agent

负责将结构化 Shot 转换为模型特定 Prompt。

### Video Agent

负责图片转视频、镜头运动和视频任务管理。

### Audio Agent

负责 TTS、BGM、SFX、音频时间轴。

### QC Agent

负责自动检查与失败任务回退。

## 7. Provider Gateway

所有外部模型都必须通过统一接口接入，避免业务层绑定具体厂商。

```text
LLMProvider
ImageProvider
VideoProvider
TTSProvider
MusicProvider
EmbeddingProvider
StorageProvider
RenderProvider
```

建议每次调用保存：

```text
provider
model
request
response
seed
usage
cost
latency
createdAt
```

这样才能实现成本统计、重试和任务复现。

## 8. Workflow / DAG

生产流程应采用 DAG，而不是简单串行调用。

示例：

```text
Script
  ↓
Storyboard
  ├── Character Assets
  ├── Location Assets
  └── Prop Assets
          ↓
      Key Frames
          ↓
      Video Shots
          ├── Voice
          ├── SFX
          └── BGM
                  ↓
                Render
                  ↓
                  QC
```

每个节点都应该具有：

- `taskId`
- `input`
- `output`
- `status`
- `retryCount`
- `error`
- `provider`
- `cost`
- `startedAt`
- `completedAt`

## 9. 数据与存储

建议逻辑分层：

```text
PostgreSQL / SQLite
  → 项目、剧本、任务、资产元数据

Object Storage
  → 图片、视频、音频、字幕

Redis
  → Queue、锁、进度、短期状态

Vector DB（可选）
  → 世界观、角色设定、历史镜头、风格记忆
```

MVP 可以先使用 SQLite + 本地文件系统，随后平滑升级到 PostgreSQL + S3/OSS/MinIO。

## 10. 成本控制

AI 漫剧平台的核心指标之一是单位分钟成本。

必须建立：

```text
Episode Cost
Shot Cost
Image Cost
Video Cost
TTS Cost
LLM Cost
Render Cost
```

优化原则：

1. 低成本模型负责规划和初稿。
2. 高成本模型只用于最终关键帧和最终视频。
3. Prompt/资产/结果缓存。
4. 失败重试禁止盲目重新生成所有上游资产。
5. Shot 级 checkpoint 支持断点续作。

## 11. 可复现生产

每一个最终媒体必须能够反向追踪：

```text
Final Video
  → Render Job
  → Shot Video
  → Key Frame
  → Prompt
  → Character/Location Assets
  → Model Provider
  → Model Parameters
  → Seed
  → Source Script Version
```

这是从“AI 玩具”升级到“生产系统”的关键设计。

## 12. MVP 技术路线

第一阶段不急于实现完整 Agent 平台，优先实现稳定的生产数据模型和任务系统。

```text
MVP-01 项目/剧集/镜头数据模型
MVP-02 JSON Schema
MVP-03 Workflow Engine
MVP-04 Provider Gateway
MVP-05 本地资产库
MVP-06 文生图适配
MVP-07 图生视频适配
MVP-08 TTS 适配
MVP-09 FFmpeg Render
MVP-10 QC
```

## 13. 后续扩展

- Web Studio
- Flutter Desktop / Mobile Client
- ComfyUI Provider
- 本地 Stable Diffusion / Flux Provider
- 多视频模型 Provider
- LoRA / IP-Adapter / ControlNet 管理
- 角色声音克隆适配层
- 自动口型同步
- 自动表情与动作控制
- 多语言配音
- 多平台发布

## 14. 核心原则

### 原则一：结构化优先

LLM 输出必须进入 Schema，再进入生产任务。

### 原则二：资产优先

角色、场景、道具必须先成为可复用资产。

### 原则三：Shot 是最小生产单元

所有生成、重试、成本、质量统计最终落到 Shot。

### 原则四：Provider 可替换

业务系统不能依赖某一家 AI 厂商的 API 数据结构。

### 原则五：所有结果可追溯

最终视频必须可以追溯到 Prompt、模型、参数和源版本。

### 原则六：Agent 不等于 Workflow

Agent 负责决策，Workflow 负责可靠执行。二者必须解耦。
