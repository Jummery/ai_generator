# AI Storyboard & Shot Specification

## 1. 目标

将自然语言故事稳定转换为可生产的 Episode / Scene / Shot，并为每个 Shot 生成结构化的视觉、动作、声音和 AI Provider 输入。

核心原则：

> AI 负责创作与拆解，Shot Specification 负责把创作结果固定成可执行生产任务。

## 2. Production Hierarchy

```text
Project
 ↓
Season
 ↓
Episode
 ↓
Scene
 ↓
Shot
 ↓
Generation Tasks
```

## 3. Episode Specification

Episode 至少包含：

- episode_id
- title
- logline
- duration_target
- narrative_goal
- emotional_arc
- characters
- locations
- scenes
- ending_hook

## 4. Scene Specification

Scene 描述一个连续的叙事空间/时间单元：

- scene_id
- location
- time
- characters
- purpose
- conflict
- start_state
- end_state
- shots

Scene 必须有明确的进入状态和退出状态，避免 AI 生成大量无叙事意义的镜头。

## 5. Shot Specification

每个 Shot 是最小生产单位。

```text
Shot
 ├─ narrative
 ├─ duration
 ├─ characters
 ├─ action
 ├─ environment
 ├─ camera
 ├─ composition
 ├─ lighting
 ├─ style
 ├─ dialogue
 ├─ voice
 ├─ sfx
 ├─ bgm
 ├─ generation_mode
 ├─ image_prompt
 ├─ video_prompt
 └─ qc_requirements
```

## 6. Narrative

明确该 Shot 在故事中的作用：

- establish
- introduce
- reaction
- action
- transition
- reveal
- conflict
- climax
- resolution
- hook

禁止所有 Shot 都使用相同的叙事类型。

## 7. Camera Specification

标准化镜头语言：

### Shot Size

- ECU
- CU
- MCU
- MS
- MLS
- LS
- ELS

### Camera Angle

- eye-level
- low-angle
- high-angle
- overhead
- dutch-angle

### Camera Movement

- static
- push-in
- pull-out
- pan
- tilt
- tracking
- orbit
- handheld

### Lens Intent

不强制绑定具体真实焦段，但可以表达视觉意图，例如：

- intimate
- cinematic
- wide environmental
- compressed portrait

## 8. Character Binding

Shot 不允许只写“林奇”。必须绑定：

```text
character_id
character_version
costume_state
expression_state
action_state
```

保证 Character Consistency System 生效。

## 9. Environment

环境至少包括：

- location_id
- time_of_day
- weather
- background_elements
- foreground_elements
- atmosphere
- continuity_constraints

## 10. Action

Action 必须可视化，避免抽象描述。

不推荐：

> 林奇非常焦虑。

推荐：

> 林奇快速敲击键盘，停顿，抬头盯住屏幕，呼吸急促，手指悬停在 Enter 键上。

## 11. Dialogue

Dialogue 与画面动作分离：

```text
Dialogue
 ├─ speaker
 ├─ text
 ├─ emotion
 ├─ delivery
 └─ timing
```

这样可以独立重新生成 TTS，而不需要重新生成画面。

## 12. Audio Specification

Shot 可以绑定：

```text
Voice
BGM
SFX
Ambience
```

音频必须拥有独立时间轴，避免将音频内容硬编码进视频生成 Prompt。

## 13. Generation Mode

每个 Shot 必须选择生成模式：

```text
STATIC
MOTION_COMIC
IMAGE_TO_VIDEO
TEXT_TO_VIDEO
```

推荐决策：

- 信息交代 → STATIC / MOTION_COMIC
- 表情变化 → MOTION_COMIC
- 简单动作 → IMAGE_TO_VIDEO
- 复杂物理效果 → TEXT_TO_VIDEO
- 高潮镜头 → IMAGE_TO_VIDEO / TEXT_TO_VIDEO

## 14. Free-First Generation Rule

默认优先级：

```text
STATIC
 ↓
MOTION_COMIC
 ↓
IMAGE_TO_VIDEO
 ↓
TEXT_TO_VIDEO
```

只有当前级别无法满足叙事要求时才升级。

## 15. Prompt Assembly

Prompt 不由单个 Agent 一次性生成，而由结构化字段组装：

```text
Style Profile
+
Character Constraints
+
Environment
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

这样可以在不同 Provider 之间复用同一个 Shot Specification。

## 16. Provider Prompt Adapter

同一个 Shot 可以转换为不同 Provider 的 Prompt 格式：

```text
Shot Specification
       ↓
Provider Adapter
 ├─ Image Provider
 ├─ Video Provider
 └─ TTS Provider
```

业务层不直接依赖某个具体 AI 平台。

## 17. Shot Duration

Shot Duration 应根据动作和对白确定，而不是固定时长。

参考：

```text
Establishing Shot   3～6s
Reaction Shot       2～4s
Dialogue Shot       3～8s
Action Shot         2～6s
Transition          1～3s
Climax              3～8s
```

最终时长由实际生成资产与音频时间轴校正。

## 18. Storyboard Review

进入生成阶段前必须检查：

- 故事是否完整
- Scene 是否有因果关系
- Shot 是否重复
- Character 是否正确
- Location 是否连续
- Camera 是否合理
- Dialogue 是否匹配动作
- Shot Duration 是否覆盖 Episode 时长
- Generation Mode 是否符合预算

## 19. Shot Readiness

Shot 只有满足以下条件才进入 AI Generation：

```text
READY
 ├─ Character Bound
 ├─ Location Bound
 ├─ Action Defined
 ├─ Camera Defined
 ├─ Duration Defined
 ├─ Generation Mode Defined
 └─ Budget Check Passed
```

## 20. 第一集目标

第一集采用约 120 秒结构，先拆解为约 16 个核心 Shot，再根据实际生成效果调整。

优先保证：

1. 前 3 秒有 Hook
2. 30 秒内完成世界观建立
3. 中段产生明显事件升级
4. 穿越事件具有视觉高潮
5. 结尾留下下一集 Hook

## 21. 生产闭环

```text
Story
 ↓
Episode
 ↓
Scene
 ↓
Shot Specification
 ↓
Prompt Assembly
 ↓
Provider Adapter
 ↓
Generation
 ↓
QC
 ↓
Shot Approved
 ↓
Timeline
```

## 22. 关键原则

### 原则 A：Shot 是最小可重试单位

一个 Shot 失败，只重新生成该 Shot。

### 原则 B：Prompt 必须可追溯

记录：

- source specification
- assembled prompt
- provider
- model
- generation parameters
- reference assets

### 原则 C：创作与生产分离

AI Director 可以重新修改故事，但已批准的 Shot 不应被无条件覆盖。

### 原则 D：预算进入分镜阶段

在生成之前就确定每个 Shot 的成本等级，避免生产到一半才发现预算超限。
