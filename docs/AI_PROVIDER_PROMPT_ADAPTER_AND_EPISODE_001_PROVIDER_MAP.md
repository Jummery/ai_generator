# AI Provider Prompt Adapter & Episode 001 Provider Map

## 1. 目标

建立 Provider-agnostic 的 AI 生成层，使同一个 Shot Specification 可以被不同 AI 平台执行，而不让业务层绑定单一 Provider。

```text
Shot Specification
        ↓
Prompt Adapter
        ↓
Capability Router
        ↓
Provider
        ↓
Artifact
        ↓
QC
```

核心原则：

> Provider 是可替换执行器，不是业务模型。

## 2. Provider Adapter 分层

### Layer A — Canonical Specification

业务侧只维护：

- Story
- Character
- Location
- Action
- Camera
- Style
- Duration
- Generation Mode
- Budget Level
- Reference Assets

### Layer B — Prompt Package

统一转换为：

```text
PromptPackage
├─ positive_prompt
├─ negative_prompt
├─ reference_assets
├─ aspect_ratio
├─ duration
├─ resolution
├─ motion_intent
├─ audio_intent
└─ provider_constraints
```

### Layer C — Provider Adapter

不同 Provider 自己处理：

- Prompt syntax
- 参数名称
- Reference image 格式
- 视频时长限制
- 分辨率限制
- API 能力
- 错误码
- Retry

## 3. Provider Capability Matrix

> 本矩阵是 2026-08-13 的生产基线。价格、免费额度、模型名称和可用地区可能变化，实际执行前必须重新读取 Provider 官方页面。

| Provider | 主要职责 | 第一集默认用途 | 成本策略 |
|---|---|---|---|
| DeepSeek | Story / Episode / Scene / Shot / Prompt | 主规划 LLM | 低成本优先 |
| Vidu | Image-to-Video / Reference-to-Video | 关键动态镜头 | 按需付费 |
| Google Cloud TTS | TTS | 普通角色对白 | 免费额度优先 |
| ElevenLabs | 高质量 TTS / SFX / Music | 重点对白、备用音频 | 免费额度优先，超额再付费 |
| FFmpeg | Timeline / Mux / Subtitle / Render | 最终 MP4 | 免费 |

## 4. DeepSeek Adapter

DeepSeek 作为文本规划主 Provider。

当前官方 API 支持 `deepseek-v4-flash` 与 `deepseek-v4-pro`，均支持 JSON Output 和 Tool Calls。V4 Flash 更适合作为个人项目的默认规划模型，V4 Pro 仅在复杂剧情推理或质量不足时升级。

推荐路由：

```text
普通 Story / Scene / Shot
        ↓
DeepSeek V4 Flash

复杂剧情 / 长链推理
        ↓
DeepSeek V4 Pro
```

Prompt Adapter 输出必须要求结构化 JSON，避免直接把自由文本交给下一生产阶段。

## 5. Vidu Adapter

Vidu 作为第一集的主 Video Provider 候选。

当前官方 API 支持 Image-to-Video、Text-to-Video、Reference-to-Video 等能力。Q3 Turbo 支持 Image-to-Video 和 Text-to-Video，Reference-to-Video 可用于主体一致性场景。

第一集推荐：

```text
普通镜头
 ↓
MOTION_COMIC

关键动作
 ↓
Vidu Image-to-Video

需要更强主体连续性
 ↓
Vidu Reference-to-Video
```

当前官方价格中，Vidu Q3 Turbo 1080p Image-to-Video 为 $0.065/sec，540p 为 $0.035/sec；Reference-to-Video 540p 为 $0.02/sec。非高峰价格更低。具体价格必须在实际生产前再次确认。

## 6. TTS Adapter

### Primary

Google Cloud TTS：适合作为低成本/免费额度优先的普通旁白与对白 Provider。官方说明 Standard voices 每月前 4M 字符免费，WaveNet 每月前 1M 字符免费，但使用 API 需要启用 Billing。

### Quality Fallback

ElevenLabs：用于重点角色、情绪对白和需要更高表现力的场景。其 API 大部分 endpoint 对 Free plan 可用，但会消耗账户额度；实际免费额度与模型能力需要在生成前确认。

路由：

```text
普通对白
 ↓
Google Cloud TTS

重点对白 / 情绪高潮
 ↓
ElevenLabs
```

## 7. Episode 001 Provider Map

### Story / Planning

```text
Idea
 ↓
DeepSeek V4 Flash
 ↓
Story / Episode / Scene / Shot JSON
```

### Image / Character Reference

Provider 不在业务层硬编码。优先选择当前可用的低成本/免费图像平台，并要求：

- Character Reference
- 统一 Style
- 可重复生成
- 可下载原图
- 商用与个人使用条款明确

第一集只在 Character Reference 和关键 Keyframe 上追求更高质量。

### Video

| Shot | 推荐模式 | Provider |
|---|---|---|
| 01-04 | MOTION_COMIC | 免费/已有图像工具 |
| 05 | IMAGE_TO_VIDEO | Vidu |
| 06 | MOTION_COMIC | 免费 |
| 07 | IMAGE_TO_VIDEO | Vidu |
| 08 | MOTION_COMIC | 免费 |
| 09 | IMAGE_TO_VIDEO | Vidu |
| 10 | IMAGE_TO_VIDEO | Vidu |
| 11 | IMAGE_TO_VIDEO | Vidu |
| 12 | IMAGE_TO_VIDEO | Vidu |
| 13 | IMAGE_TO_VIDEO | Vidu |
| 14 | MOTION_COMIC | 免费 |
| 15 | MOTION_COMIC | 免费 |
| 16 | MOTION_COMIC / 可升级 | 免费优先 |

如果预算为 ¥0，则所有 Video Shot 自动降级为 MOTION_COMIC。

## 8. Budget-aware Provider Routing

```text
Shot
 ↓
Budget Gate
 ↓
Free capability available?
 ├─ YES → Free
 └─ NO
      ↓
Low-cost capability?
 ├─ YES → Low-cost
 └─ NO
      ↓
Can Motion Comic satisfy narrative?
 ├─ YES → Motion Comic
 └─ NO → Request explicit paid approval
```

系统禁止自动突破 Episode Budget。

## 9. Prompt Adapter Example

### Canonical

```text
Character: Lin Qi
Action: pulled toward a dimensional portal
Camera: dynamic forward movement
Environment: dark office
Style: cinematic anime drama
Duration: 8s
Mode: IMAGE_TO_VIDEO
```

### Provider Package

```text
positive_prompt:
consistent Lin Qi, black hoodie, shocked expression,
dimensional vortex inside computer display,
body pulled toward portal, flying papers and sparks,
dynamic cinematic anime drama

motion_intent:
forward pull, body displacement, swirling particles

reference_assets:
linqi-v1/front.png
linqi-v1/3q.png
```

Provider Adapter 再将该 Package 转换为目标平台所需的具体参数。

## 10. Provider Failover

### Video Provider Failure

```text
Vidu unavailable
 ↓
Try alternate configured Video Provider
 ↓
If unavailable
 ↓
MOTION_COMIC
```

### TTS Failure

```text
Primary TTS failure
 ↓
Secondary TTS
 ↓
If both unavailable
 ↓
Mark audio task pending
```

不能因为单个 Provider 故障导致整个 Episode 失败。

## 11. Provider Selection Criteria

Provider 评分不只看价格：

```text
Score =
Cost
+ Quality
+ Consistency
+ Availability
+ Latency
+ Capability Fit
+ Usage Rights
```

个人免费生产场景中默认权重：

```text
完成生产 > 成本 > 一致性 > 质量 > 速度
```

但主角 Reference、高潮镜头和最终 Hook 可以提高质量权重。

## 12. Provider Metadata

每个 Generation Task 必须保存：

- provider
- model
- adapter_version
- prompt_version
- generation_mode
- reference_assets
- parameters
- estimated_cost
- actual_cost
- request_id
- artifact_id
- qc_result

## 13. Cost Guard

每个任务在发送 Provider 前执行：

```text
estimated_cost
 ↓
remaining_episode_budget
 ↓
allowed?
 ├─ YES → Execute
 └─ NO → Downgrade / Wait for approval
```

任何自动重试都必须重新计算成本。

## 14. First Episode Recommended Route

```text
DeepSeek V4 Flash
       ↓
Story / Shot / Prompt
       ↓
Character Reference
       ↓
Free Image Generation
       ↓
┌──────────────────────────┐
│ 12+ Motion Comic Shots   │
│ 4-6 Key Video Shots      │
└──────────────────────────┘
       ↓
Vidu / Alternative Video
       ↓
Google Cloud TTS
       ↓
ElevenLabs only if needed
       ↓
BGM / SFX
       ↓
FFmpeg
       ↓
Episode 001 MP4
```

## 15. Provider Independence Rules

1. 不在 Story / Shot 模型中保存 Provider-specific Prompt。
2. 不允许 Provider API 参数进入业务层。
3. Provider 可随时替换。
4. Provider 不可用时必须有降级路径。
5. Provider 价格变化不能破坏剧情生产流程。
6. 每个 Provider 都必须经过 Capability Matrix。
7. 个人生产模式下默认不自动购买额度。

## 16. 当前结论

第一集的默认组合为：

- **DeepSeek V4 Flash：**大脑
- **免费图片工具：**角色与普通 Keyframe
- **Vidu：**关键动态镜头
- **Google Cloud TTS：**普通对白
- **ElevenLabs：**高质量音频备用
- **FFmpeg：**最终合成

这套组合不是永久绑定，而是 Episode 001 的第一版生产基线。