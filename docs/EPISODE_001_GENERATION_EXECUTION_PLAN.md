# Episode 001 Generation Execution Plan

## 1. Objective

在不本地部署模型、不建设 GPU 集群的前提下，使用现有 AI 平台完成 Episode 001 的第一版可观看成片。

目标不是追求每个镜头最高质量，而是：

> **优先用免费能力完成整集，关键镜头再选择低成本升级。**

## 2. Execution Strategy

```text
Shot Specification
 ↓
Free Capability Check
 ↓
Free Provider / Platform
 ↓
Quality Check
 ↓ PASS → Keep
 ↓ FAIL
Low-cost Provider
 ↓
Quality Check
 ↓ FAIL
Alternative Mode
```

## 3. Provider Role

Provider 不是固定绑定，而是按能力选择：

| 能力 | 首选策略 | 备用策略 |
|---|---|---|
| Story / Script | 可用免费 LLM | 低成本 LLM |
| Image | 当前可用免费额度/平台 | 低成本 Image API |
| Video | 免费平台能力 | 低成本 I2V |
| TTS | 免费额度 | 低成本 TTS |
| BGM | 免费/自有素材 | 低成本生成 |
| SFX | 免费素材/生成 | 低成本生成 |
| Render | 确定性媒体处理 | 不调用 AI |

Provider Matrix 是动态配置，不把某个平台的价格、额度或免费政策写死在业务流程中。

## 4. Shot Execution Matrix

| Shot | 内容 | 首选 Mode | Provider Strategy | 失败降级 |
|---|---|---|---|---|
| 01 | 城市夜景 | MOTION_COMIC | 免费 Image + Motion | STATIC |
| 02 | 林奇办公室 | MOTION_COMIC | 免费 Image | STATIC |
| 03 | 键盘编码 | MOTION_COMIC | 免费 Image | STATIC |
| 04 | 异常代码 | MOTION_COMIC | 免费 Image | STATIC |
| 05 | 键盘电弧 | IMAGE_TO_VIDEO | 低成本 Video | MOTION_COMIC |
| 06 | 林奇惊醒 | MOTION_COMIC | 免费 Image | STATIC |
| 07 | ERROR 警告 | IMAGE_TO_VIDEO | Image + Video | MOTION_COMIC |
| 08 | 红光警报 | MOTION_COMIC | 免费 Image | STATIC |
| 09 | 空间异常 | IMAGE_TO_VIDEO | 低成本 Video | MOTION_COMIC |
| 10 | 错误爆发 | IMAGE_TO_VIDEO | 低成本 Video | MOTION_COMIC |
| 11 | 屏幕漩涡 | IMAGE_TO_VIDEO | 低成本 Video | MOTION_COMIC |
| 12 | 被吸入 | IMAGE_TO_VIDEO | 低成本 Video | MOTION_COMIC |
| 13 | 时空通道 | IMAGE_TO_VIDEO | 低成本 Video | MOTION_COMIC |
| 14 | 世界静止 | MOTION_COMIC | 免费 Image | STATIC |
| 15 | 看见年轻自己 | MOTION_COMIC | 免费 Image | STATIC |
| 16 | 结尾 Hook | MOTION_COMIC | 免费 Image | STATIC |

## 5. Character Asset Preparation

正式生成 Shot 前先生成角色基准资产。

### Adult Lin Qi

需要：

- 正面
- 45°
- 侧面
- 半身
- 全身
- 基础表情
- 黑色程序员卫衣

### Young Lin Qi

需要独立 Reference Set，不直接复制成年角色。

### Character Lock

角色参考通过人工确认后锁定：

```text
linqi-v1
young-linqi-v1
```

后续 Shot 不允许随意替换参考图。

## 6. Location Asset Preparation

优先建立两个 Location：

```text
office-night-v1
past-room-v1
```

办公室需要锁定：

- 桌面
- 显示器
- 键盘
- 灯光
- 主色调
- 摄像机常用角度

## 7. Image Generation Order

不要按 Shot 顺序直接生成。

推荐：

```text
Character
 ↓
Location
 ↓
Keyframe
 ↓
Shot Motion
```

这样可以降低角色和场景漂移。

## 8. Video Generation Order

第一轮只生成关键镜头：

```text
05
07
09
10
11
12
13
```

确认核心视觉效果后，再批量处理其它 Shot。

## 9. TTS Execution

先确定角色声音，再批量生成对白。

```text
Adult Voice
Young Voice
 ↓
Dialogue List
 ↓
TTS
 ↓
Actual Duration
 ↓
Timeline
```

Episode 001 对白基线：

- Shot 06：林奇“什么情况……”
- Shot 16：成年林奇“这一次……换我来教你。”

其余镜头暂时以环境音和 SFX 为主，避免第一集制作复杂度过高。

## 10. BGM Execution

只准备一条主 BGM 的多个时间段/剪辑版本即可：

```text
Suspense
 ↓
Escalation
 ↓
Time Travel
 ↓
Emotional Ending
```

避免第一集使用过多音乐素材。

## 11. SFX Execution

优先准备：

1. Keyboard
2. Electrical Spark
3. Digital Glitch
4. System Alarm
5. Low Frequency Impact
6. Portal Suction
7. Time Tunnel
8. Heartbeat

SFX 优先使用免费素材或已有素材库。

## 12. Prompt Execution Rule

所有生成都从 Shot Specification 组装 Prompt：

```text
Global Style
+
Character Reference
+
Location Reference
+
Action
+
Camera
+
Lighting
+
Continuity
+
Negative Constraints
```

不要为每个 Provider 手工重新创作故事描述。

## 13. Generation Batch Strategy

### Batch A — Assets

Character + Location。

### Batch B — Static Keyframes

16 个 Shot 的基础画面。

### Batch C — Critical Motion

05 / 07 / 09 / 10 / 11 / 12 / 13。

### Batch D — TTS

所有对白。

### Batch E — Audio

BGM + SFX。

### Batch F — Timeline

自动组装。

### Batch G — QC

整集审核。

## 14. Budget Gate

执行前计算：

```text
Estimated Cost
+
Reserved Cost
+
Retry Reserve
```

只有满足：

```text
Total <= Episode Budget
```

才允许付费生成。

推荐第一集目标：

```text
Primary Target: ¥0
Fallback Target: ≤ ¥5
Hard Limit: 用户未明确授权时不超过预算
```

## 15. Retry Policy

每个 Shot 默认：

```text
Attempt 1 → Prompt adjustment
Attempt 2 → Reference / parameter adjustment
Attempt 3 → Generation Mode downgrade
```

不要无限重试同一个昂贵模型。

## 16. Quality Priority

当资源有限时：

```text
Character Consistency
 >
Narrative Clarity
 >
Audio Synchronization
 >
Motion Quality
 >
Visual Detail
```

## 17. First Pass Acceptance

第一版不追求完美，只需要：

- 故事看得懂
- 林奇角色稳定
- 穿越事件成立
- 关键镜头有视觉冲击
- 台词清晰
- 音画基本同步
- 能输出完整 MP4

## 18. Second Pass

第一版完成后，只优化最明显的问题：

1. Character drift
2. Shot transition
3. TTS timing
4. Key visual shots
5. BGM volume
6. Subtitle timing

禁止第一版失败后整体推倒重做。

## 19. Execution Checklist

```text
[ ] Character Reference Approved
[ ] Location Reference Approved
[ ] 16 Keyframes Generated
[ ] Critical Video Shots Generated
[ ] TTS Generated
[ ] BGM Ready
[ ] SFX Ready
[ ] Timeline Built
[ ] Subtitle Generated
[ ] Preview Rendered
[ ] Narrative QC Passed
[ ] Character QC Passed
[ ] Audio QC Passed
[ ] Final MP4 Rendered
```

## 20. Final Definition of Done

Episode 001 完成标准：

```text
16 Shots
+
Character Consistency
+
TTS
+
BGM
+
SFX
+
Subtitle
+
Timeline
+
QC
+
MP4
```

最终交付物只有一个核心目标：

> **用户可以直接打开 MP4，从头到尾完整看完第一集。**
