# AI Animation Timeline & Render Pipeline

## 1. 目标

把已通过 QC 的 Shot、TTS、BGM、SFX、字幕和转场自动组装成可观看的最终 MP4。

核心原则：

> 生成素材不是成片。Timeline 才是把不稳定的 AI 素材变成稳定视频产品的关键层。

## 2. Production Pipeline

```text
Approved Shots
      ↓
Media Normalize
      ↓
Audio Duration Analysis
      ↓
Timeline Planning
      ↓
Shot Alignment
      ↓
Subtitle Alignment
      ↓
BGM / SFX Mix
      ↓
Episode Render
      ↓
Media QC
      ↓
Final MP4
```

## 3. Timeline as Source of Truth

Episode 必须存在唯一 Timeline。

```text
Episode Timeline
 ├─ Shot Track
 ├─ Dialogue Track
 ├─ Subtitle Track
 ├─ BGM Track
 ├─ SFX Track
 ├─ Transition Track
 └─ Metadata Track
```

最终 MP4 是 Timeline 的渲染结果，而不是各个 AI Provider 输出直接拼接。

## 4. Shot Timing

AI 视频实际生成时长可能与目标时长不同，因此不能假设：

```text
requested_duration == generated_duration
```

实际流程：

```text
Shot Specification Duration
          ↓
Generated Media Duration
          ↓
Audio / Dialogue Duration
          ↓
Final Shot Duration
```

## 5. Dialogue First Principle

对于有对白的 Shot，优先确定 TTS 实际时长，再调整画面。

```text
Dialogue Text
 ↓
TTS
 ↓
Actual Audio Duration
 ↓
Shot Duration
 ↓
Visual Alignment
```

这样可以避免出现“人物还在说话，画面已经结束”的问题。

## 6. Visual Alignment Strategy

根据视频长度和目标时长选择：

### Generated > Target

- trim
- speed adjustment（小幅度）
- 选择最佳连续片段

### Generated < Target

优先级：

1. Extend / loop 不明显的镜头运动
2. 使用 Keyframe + Motion Comic 补充
3. 增加 reaction shot
4. 必要时重新生成

禁止大量重复明显帧。

## 7. Shot Boundary

Shot 切换点优先由以下因素决定：

```text
Dialogue End
Action Completion
Emotional Beat
Music Beat
Narrative Transition
```

不单纯按照固定秒数切片。

## 8. Subtitle Timeline

字幕必须来自结构化 Dialogue，而不是从最终视频重新 OCR。

```text
Dialogue
 ├─ speaker
 ├─ text
 ├─ start
 └─ end
        ↓
Subtitle Segment
```

字幕时间与 TTS 时间保持一致。

## 9. BGM Strategy

BGM 使用独立轨道：

```text
BGM
 ↓
Episode / Scene Emotional Curve
 ↓
Volume Automation
```

对白期间自动降低 BGM，转场或高潮可以提高 BGM。

## 10. SFX Strategy

SFX 绑定事件，而不是绑定 Provider。

例如：

```text
keyboard_typing
keyboard_electric_arc
system_error
portal_open
portal_suction
heartbeat
```

每个 SFX 具有 start、duration、volume、fade 参数。

## 11. Audio Mix

推荐混音层级：

```text
Dialogue
 >
SFX
 >
BGM
 >
Ambience
```

最终进行：

- peak control
- loudness normalization
- clipping detection
- silence detection

## 12. Transition

默认少使用复杂转场。

优先：

- hard cut
- short fade
- dip to black
- motion transition

转场必须服务于叙事节奏。

## 13. First Episode Timeline

第一集时间轴基线：

```text
00:00  Shot 01
00:05  Shot 02
00:11  Shot 03
00:17  Shot 04
00:22  Shot 05
00:28  Shot 06
00:33  Shot 07
00:39  Shot 08
00:46  Shot 09
00:52  Shot 10
00:59  Shot 11
01:07  Shot 12
01:15  Shot 13
01:22  Shot 14
01:30  Shot 15
01:40  Shot 16
≈01:50  Episode End
```

实际 TTS 和生成视频完成后允许自动重新计算，不强制保持该基线。

## 14. Render Preset

个人观看优先，可以提供：

### Preview

- 720p
- 快速编码
- 用于人工审核

### Final

- 1080p
- 高质量编码
- 完整音频混音
- 字幕烧录或独立字幕轨

## 15. Render QC

最终成片至少检查：

### Video

- 分辨率
- FPS
- 黑帧
- 花屏
- 编码错误
- Shot 顺序

### Audio

- 是否存在静音异常
- Dialogue 是否完整
- BGM 是否过响
- SFX 是否削波
- 音画同步

### Subtitle

- 文本完整
- 时间正确
- 不超出画面
- 不出现重叠

### Narrative

- 开头 Hook 完整
- 穿越事件完整
- 最终 Hook 完整

## 16. Render Failure Recovery

渲染失败不重新生成 AI 素材。

```text
Render Failed
 ↓
Diagnose
 ├─ Media Error → Normalize
 ├─ Timeline Error → Rebuild Timeline
 ├─ Audio Error → Remix
 └─ Encoder Error → Render Again
```

只有素材本身 QC 失败时，才返回 Shot Generation。

## 17. Artifact Lineage

最终 MP4 必须能够追溯：

```text
Final MP4
 ↓
Timeline Version
 ↓
Shot Versions
 ↓
Character Versions
 ↓
Prompt Versions
 ↓
Provider / Model
 ↓
Source Assets
```

这样后续发现问题时，可以准确定位到具体 Shot，而不是重新生产整集。

## 18. Personal Free-First Render Policy

渲染本身优先使用成熟的开源/免费媒体处理能力，不因为后期剪辑再次产生 AI 调用。

AI 只负责：

- 内容
- 图片
- 视频
- 声音

Timeline / 编码 / 混音 / 字幕合成属于确定性生产步骤。

## 19. Episode Completion Criteria

Episode 只有同时满足以下条件才标记 `FINAL`：

```text
All Shots Approved
AND
Timeline Valid
AND
Audio Valid
AND
Subtitle Valid
AND
Media QC Passed
```

最终状态：

```text
DRAFT
 ↓
PRODUCTION
 ↓
ASSEMBLY
 ↓
QC
 ↓
FINAL
```

## 20. 关键原则

1. Timeline 是成片唯一事实来源。
2. TTS 实际时长优先于预估时长。
3. AI 视频时长允许变化，Timeline 自动适配。
4. 素材失败与渲染失败必须分离处理。
5. 渲染失败不能导致 AI 素材重新生成。
6. 最终 MP4 必须可追溯到每一个 Shot。
7. 个人免费优先，不为确定性剪辑步骤调用昂贵 AI。
