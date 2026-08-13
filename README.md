# ai_generator

AI 漫剧自动化生产工厂：从创意、剧本、分镜、角色资产，到图片、视频、配音、字幕、FFmpeg 合成和自动质检。

## 项目目标

将 AI 漫剧生产从“单次生成”升级为可追踪、可重试、可复用、可扩展的工程化生产流水线。

```text
创意
  ↓
世界观 / 剧本
  ↓
分集 / 场景 / 分镜
  ↓
角色 / 场景 / 道具资产
  ↓
关键帧
  ↓
动态镜头
  ↓
配音 / BGM / 音效
  ↓
字幕
  ↓
FFmpeg 合成
  ↓
自动质检
  ↓
成片
```

## 架构文档

- [AI 漫剧制作工厂架构方案](docs/AI_ANIMATION_DRAMA_FACTORY_ARCHITECTURE.md)
- [Roadmap](docs/ROADMAP.md)

## 核心设计原则

1. **结构化优先**：LLM 输出先进入 Schema，再进入生产任务。
2. **资产优先**：角色、场景、道具都是可复用资产。
3. **Shot 是最小生产单元**：生成、重试、成本和质量统计落到 Shot。
4. **Provider 可替换**：业务层不绑定单一 AI 厂商。
5. **结果可追溯**：最终视频可追溯到 Prompt、模型、参数、Seed 和源版本。
6. **Agent 与 Workflow 解耦**：Agent 负责决策，Workflow 负责可靠执行。

## 当前阶段

当前处于 **Phase 0：架构验证**，优先建立 Production Core，再逐步接入 AI Provider 和媒体生产能力。

## License

MIT License
