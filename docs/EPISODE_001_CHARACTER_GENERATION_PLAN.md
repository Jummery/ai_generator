# Episode 001 Character Generation Plan

## 1. Objective

完成 Episode 001 两个核心角色的可复用 Reference Set：

- `linqi-v1`：成年林奇
- `young-linqi-v1`：年轻林奇

角色生成优先于所有 Shot 生成。角色未 LOCKED，不进入批量生产。

## 2. Character Generation Pipeline

```text
Global Style
 ↓
Character Base Prompt
 ↓
Reference Sheet
 ↓
Face Consistency Review
 ↓
Pose / Body Review
 ↓
Costume Review
 ↓
Expression Set
 ↓
Human Approval
 ↓
LOCKED
```

## 3. Adult Lin Qi — `linqi-v1`

### Character Identity

- 成年男性
- 中国人
- 年轻成年人
- 程序员
- 偏瘦体型
- 黑色短发
- 黑色程序员卫衣
- 深色长裤
- 无明显特殊饰品

### Personality

平时理性、克制、专注；长期熬夜带来轻微疲惫感；遭遇异常时震惊但不夸张；穿越后逐渐表现出坚定和成熟。

### Base Prompt

```text
adult Chinese male programmer named Lin Qi, young adult, slim build, short natural black hair, intelligent slightly tired face, clean natural facial features, black programmer hoodie, dark pants, no glasses, no beard, restrained and thoughtful personality, cinematic anime drama character design, semi-realistic anime rendering, expressive but natural eyes, consistent facial identity, detailed face, clean silhouette, neutral studio background, character reference sheet, 16:9
```

### Negative Prompt

```text
old man, teenager, child, beard, mustache, long hair, dyed hair, glasses, muscular body, overweight body, fantasy armor, suit, different clothing, facial scar, tattoo, exaggerated eyes, chibi, deformed face, asymmetrical eyes, extra fingers, extra limbs, duplicate person, inconsistent hairstyle, inconsistent face, watermark, logo, text
```

## 4. Adult Reference Set

必须生成以下基础参考：

```text
linqi-v1/
├── front-neutral
├── three-quarter-neutral
├── side-neutral
├── upper-body
├── full-body
├── shocked
├── determined
└── costume-reference
```

### Front Neutral

用于建立最稳定的身份基准。

要求：正脸、自然表情、无遮挡、服装完整、光线均匀。

### Three Quarter

用于 Shot 02、06、08、15、16 等大多数叙事镜头。

### Side

用于方向性构图和动作镜头。

### Upper Body

用于对话、表情和屏幕光照镜头。

### Full Body

用于 Shot 09、12、13 等需要身体动作的镜头。

### Shocked

用于 Shot 05、06、07、08。

### Determined

用于 Shot 15、16。

## 5. Adult Costume Lock

默认服装：

```text
black hoodie
+
dark pants
+
casual programmer appearance
```

Shot 02～16 不允许无理由改变服装。

允许变化：

- 衣服褶皱
- 姿态
- 光照
- 轻微污渍

禁止变化：

- 服装颜色
- 衣服类型
- 发型
- 年龄
- 面部身份

## 6. Young Lin Qi — `young-linqi-v1`

年轻林奇必须与成年林奇具有可识别的视觉血缘关系，但不能直接复制成年角色。

### Base Prompt

```text
young Chinese male student, younger version of Lin Qi, recognizable facial resemblance to adult Lin Qi, youthful slim face, short natural black hair, clear youthful eyes, simple student clothing, slightly inexperienced and curious expression, cinematic anime drama character design, semi-realistic anime rendering, warm nostalgic lighting, consistent facial identity, character reference sheet, 16:9
```

### Negative Prompt

```text
adult man, old man, beard, mustache, long hair, dyed hair, muscular body, fantasy costume, suit, futuristic armor, exaggerated eyes, chibi, deformed face, different facial identity, inconsistent hairstyle, watermark, logo, text
```

## 7. Young Reference Set

```text
young-linqi-v1/
├── front-neutral
├── three-quarter-neutral
├── side-neutral
├── upper-body
├── full-body
└── curious
```

年轻林奇在第一集主要用于 Shot 15、16，因此不需要过度扩充表情库。

## 8. Generation Parameters

不要把具体模型参数硬编码为唯一标准。

第一轮只固定：

- 16:9
- consistent style
- reference image enabled when provider supports it
- moderate creativity
- character identity priority

不同 Provider 的参数由 Prompt Adapter 转换。

## 9. Reference Selection Rules

### Face Shot

```text
front-neutral
OR
three-quarter-neutral
```

### Dialogue Shot

```text
three-quarter-neutral
+
expression reference
```

### Action Shot

```text
full-body
+
three-quarter
```

### Emotional Close-up

```text
face reference
+
expression reference
```

## 10. Character Consistency QC

每张参考图至少检查：

### Identity

- Face shape
- Eyes
- Nose
- Mouth
- Hairline
- Hairstyle

### Body

- Height proportion
- Shoulder width
- Body shape

### Costume

- Hoodie
- Pants
- Colors
- Accessories

### Style

- Rendering style
- Line quality
- Lighting behavior

## 11. Acceptance Threshold

第一轮角色生成至少选择 4～8 个候选版本。

人工只需要选出：

```text
1 Base Face
1 Three-quarter
1 Full-body
1 Expression Set
```

然后建立最终 `Reference Set`。

不要为了追求“完美角色”无限生成。

## 12. Failure Classification

### F1 — Face Drift

处理：重新生成该参考图，不修改 Character Base。

### F2 — Costume Drift

处理：强化 Costume Constraint。

### F3 — Body Drift

处理：加入 Full-body Reference。

### F4 — Style Drift

处理：重新绑定 `anime-cinematic-v1`。

### F5 — Age Drift

处理：明确成年/年轻状态约束。

## 13. Lock Gate

`linqi-v1` / `young-linqi-v1` 只有满足以下条件才 LOCKED：

```text
[ ] Face approved
[ ] Hair approved
[ ] Body approved
[ ] Costume approved
[ ] Style approved
[ ] Expression approved
[ ] Reference Set complete
[ ] Human review passed
```

## 14. Shot Binding

LOCKED 后，Shot 不直接引用“某张图片”，而引用：

```text
character_id
character_version
reference_set
expression_state
costume_state
```

例如：

```text
character_id=linqi
character_version=v1
reference_set=three-quarter
expression_state=shocked
costume_state=default
```

这样后续更换 Provider 时仍然可以重新生成。

## 15. Generation Priority

第一阶段：

```text
linqi-v1
```

第二阶段：

```text
young-linqi-v1
```

第三阶段：

```text
Character Comparison
```

确认两者同时出现在 Shot 15/16 时仍具有明确关系。

## 16. Free-First Execution

优先使用当前可用的免费图像生成能力完成候选 Reference。

如果免费结果无法达到稳定身份：

```text
Free generation
 ↓ FAIL
Low-cost image generation
 ↓
Human approval
 ↓
Reuse across episodes
```

核心角色允许一次性低成本投入，因为角色会被复用几十集。

## 17. Deliverables

最终必须得到：

```text
linqi-v1
├── reference_set
├── identity_constraints
├── costume_constraints
├── expression_states
└── approved_reference_ids

young-linqi-v1
├── reference_set
├── identity_constraints
├── costume_constraints
├── expression_states
└── approved_reference_ids
```

## 18. Next Action

角色完成 LOCKED 后，立即进入：

```text
office-night-v1
past-room-v1
computer-v1
keyboard-v1
monitor-v1
```

然后开始 Episode 001 的 16 张 Keyframe 生成。
