# AI Animation Asset Library

## 1. 目标

建立跨 Episode 长期复用的个人 AI 漫剧资产库，使角色、场景、道具、声音和视觉风格成为稳定生产资产，而不是每集重新生成。

核心原则：

> **Asset 是长期资产，Shot 是一次性生产任务。**

## 2. Asset Taxonomy

```text
Asset Library
│
├── Characters
├── Locations
├── Props
├── Voices
├── Styles
├── Music
├── SFX
└── Reference Collections
```

## 3. Character Asset

Character 是最高优先级资产。

### Adult Lin Qi

```text
linqi-v1
├── face
├── hair
├── body
├── costume
├── accessories
├── expressions
└── reference_set
```

### Young Lin Qi

```text
young-linqi-v1
├── face
├── hair
├── body
├── costume
├── expressions
└── reference_set
```

Character Version 必须不可变。

如果角色发生重大视觉变化：

```text
linqi-v1
 ↓
linqi-v2
```

而不是覆盖 v1。

## 4. Character Reference Set

每个核心角色建议至少包含：

- front
- three_quarter
- side
- upper_body
- full_body
- neutral_expression
- happy_expression
- angry_expression
- surprised_expression
- costume_reference

实际生成时不需要每个 Shot 都使用全部参考图，由 Character Director 根据任务选择最小必要集合。

## 5. Character State

角色身份和状态分离：

```text
Character Identity
+
Character State
```

Identity 固定：

- Face
- Hair
- Body
- Base Costume

State 可变化：

- Expression
- Pose
- Action
- Temporary Clothing State
- Emotion

因此“林奇惊恐”不是新角色，而是：

```text
linqi-v1
+
expression=shocked
```

## 6. Location Asset

第一集核心场景：

```text
office-night-v1
past-room-v1
```

Location 应记录：

- Layout
- Camera Angles
- Lighting
- Color Palette
- Time
- Weather
- Background Elements
- Foreground Elements

Location Version 同样不可变。

## 7. Prop Asset

重复出现的物体应进入 Prop Library：

```text
computer-v1
keyboard-v1
phone-v1
monitor-v1
```

重要 Prop 可以建立 Reference Set。

例如键盘电弧镜头必须保证键盘外形、位置和桌面关系稳定。

## 8. Voice Asset

声音也是角色资产。

```text
adult-linqi-voice-v1
young-linqi-voice-v1
```

Voice Asset 记录：

- voice provider
- voice model
- voice identifier
- language
- speaking style
- speed
- pitch
- emotional range

如果更换声音，创建新版本，不覆盖旧版本。

## 9. Style Asset

建立统一视觉 Style：

```text
anime-cinematic-v1
```

包含：

- Visual style
- Line style
- Lighting
- Color language
- Character rendering
- Background rendering
- Camera language
- Negative constraints

Style 是全剧级资产。

## 10. Asset Manifest

每个 Episode 只引用 Asset ID，不复制资产本身：

```text
Episode 001
├── linqi-v1
├── young-linqi-v1
├── office-night-v1
├── past-room-v1
├── computer-v1
├── keyboard-v1
├── adult-linqi-voice-v1
├── young-linqi-voice-v1
└── anime-cinematic-v1
```

## 11. Asset Lock

经过人工确认的核心资产进入：

```text
DRAFT
 ↓
REVIEW
 ↓
APPROVED
 ↓
LOCKED
```

LOCKED 资产默认禁止 AI 自动修改。

任何重大修改必须创建新 Version。

## 12. Asset Change Request

当生成 Shot 时发现角色有问题，不允许直接修改角色资产。

流程：

```text
Shot QC Failed
 ↓
Is Asset Wrong?
 ├─ NO → Regenerate Shot
 └─ YES
      ↓
Create Asset Change Request
      ↓
Human Review
      ↓
New Asset Version
```

这样可以避免一次错误生成污染整个剧集。

## 13. Asset Reuse

Asset 复用优先级：

```text
Approved Asset
 >
Existing Variant
 >
New Variant
 >
New Asset
```

只要已有资产满足要求，不重新生成。

## 14. Reference Selection

不同 Shot 选择不同 Reference：

### Face close-up

优先：front / three_quarter face reference。

### Full body

优先：full_body reference。

### Action

使用 full_body + relevant pose reference。

### Emotional shot

使用 face reference + expression state。

## 15. Asset Cache

生成后的资产必须缓存：

```text
Asset ID
+
Version
+
Provider
+
Model
+
Prompt
+
Parameters
+
Reference IDs
+
Artifact
```

如果完全相同的生产请求再次出现，优先复用缓存。

## 16. Asset Provenance

每个资产必须能够回答：

- 谁生成的
- 使用什么 Provider
- 使用什么 Model
- 使用什么 Prompt
- 使用什么 Reference
- 什么时候生成
- 哪个 Version
- 哪些 Episode 使用

## 17. Asset Quality Levels

### L0 — Draft

仅用于探索。

### L1 — Usable

可以用于普通 Shot。

### L2 — Approved

通过人工检查，可进入正式 Episode。

### L3 — Locked

核心剧集资产，禁止自动替换。

## 18. Free-First Asset Strategy

免费优先不意味着每次都重新寻找免费生成。

真正的成本优化来自：

```text
Generate Once
 ↓
Approve
 ↓
Cache
 ↓
Reuse 10 / 20 / 50 Episodes
```

因此核心角色和 Location 的生成质量值得优先投入。

## 19. Episode 001 Asset Baseline

第一集至少建立：

### Characters

- linqi-v1
- young-linqi-v1

### Locations

- office-night-v1
- past-room-v1

### Props

- computer-v1
- keyboard-v1
- monitor-v1

### Voices

- adult-linqi-voice-v1
- young-linqi-voice-v1

### Style

- anime-cinematic-v1

## 20. 50-Episode Strategy

随着 Episode 增加：

```text
Episode 001
 ↓
Asset Library
 ↓
Episode 002
 ↓
Reuse + Extend
 ↓
Episode 003
 ↓
Reuse + Extend
 ↓
...
 ↓
Episode 050
```

资产库成为整个系列的长期生产基础。

## 21. Asset Garbage Collection

不删除已被历史 Episode 引用的资产。

未引用的 Draft Asset 可以进入候选清理区。

规则：

```text
Referenced → Keep
Approved → Keep
Locked → Keep
Draft + Unreferenced → Candidate for cleanup
```

## 22. Definition of Done

第一集进入正式生产前：

```text
[ ] Adult Character Approved
[ ] Young Character Approved
[ ] Office Location Approved
[ ] Past Room Approved
[ ] Core Props Approved
[ ] Adult Voice Approved
[ ] Young Voice Approved
[ ] Visual Style Approved
[ ] Asset IDs Locked
[ ] Episode 001 Manifest Created
```

最终目标：

> **以后生成第 2～50 集时，AI 不是重新认识这个世界，而是在已经建立的世界中继续讲故事。**
