# Episode 001 Asset Manifest

## 1. 目的

定义 Episode 001 的正式生产资产清单、生成顺序、引用关系和锁定规则。

本文件是第一集进入实际 AI 生成前的资产基线。

核心原则：

> **先锁定长期资产，再批量生成 Shot。**

## 2. Asset Manifest

| Asset ID | 类型 | 用途 | 优先级 | 状态 |
|---|---|---|---|---|
| `linqi-v1` | Character | 成年林奇 | P0 | DRAFT |
| `young-linqi-v1` | Character | 年轻林奇 | P0 | DRAFT |
| `office-night-v1` | Location | 深夜办公室 | P0 | DRAFT |
| `past-room-v1` | Location | 过去的房间 | P0 | DRAFT |
| `computer-v1` | Prop | 电脑/显示器 | P1 | DRAFT |
| `keyboard-v1` | Prop | 机械键盘 | P1 | DRAFT |
| `monitor-v1` | Prop | 屏幕 | P1 | DRAFT |
| `adult-linqi-voice-v1` | Voice | 成年林奇 | P0 | DRAFT |
| `young-linqi-voice-v1` | Voice | 年轻林奇 | P0 | DRAFT |
| `anime-cinematic-v1` | Style | 全剧视觉风格 | P0 | DRAFT |

## 3. Generation Order

严格按照以下顺序执行：

```text
1. anime-cinematic-v1
        ↓
2. linqi-v1 / young-linqi-v1
        ↓
3. office-night-v1 / past-room-v1
        ↓
4. computer-v1 / keyboard-v1 / monitor-v1
        ↓
5. adult-linqi-voice-v1 / young-linqi-voice-v1
        ↓
6. Episode 001 Keyframes
        ↓
7. Critical Video Shots
```

如果 Character 或 Location 未通过审核，不进入下一阶段。

## 4. Global Visual Style

### `anime-cinematic-v1`

目标风格：

- 现代都市科幻漫剧
- 电影感构图
- 动漫角色表现
- 半写实背景
- 强烈光影对比
- 清晰人物轮廓
- 适合 16:9 视频
- 支持静态漫画和 Image-to-Video

### Style Prompt

```text
cinematic anime drama, modern urban science fiction, semi-realistic anime characters, cinematic composition, expressive faces, detailed but clean background, dramatic lighting, volumetric light, controlled color palette, strong silhouette, consistent character design, high visual continuity, 16:9
```

### Negative Constraints

```text
deformed face, extra fingers, extra limbs, duplicate person, inconsistent hairstyle, inconsistent clothing, distorted hands, malformed eyes, low detail face, random accessories, text artifacts, watermark, logo
```

## 5. Adult Lin Qi

### Asset

`linqi-v1`

### Character Definition

- 成年男性
- 中国人
- 程序员
- 年轻成年人
- 黑色程序员卫衣
- 深色长裤
- 黑色短发
- 偏瘦体型
- 长时间工作的疲惫感

### Personality Visual Cue

平时克制、专注；遇到系统异常时表现震惊；穿越后表现复杂、坚定。

### Reference Set

必须生成：

```text
front
three_quarter
side
upper_body
full_body
neutral
shocked
determined
```

### Prompt

```text
adult Chinese male programmer named Lin Qi, young adult, slim build, short black hair, clean natural face, black programmer hoodie, dark pants, tired but intelligent expression, cinematic anime drama character design, consistent facial identity, front three-quarter and full-body reference, neutral studio lighting
```

### Negative

```text
old man, teenager, beard, long hair, muscular body, different hairstyle, different clothing, glasses, facial scars, exaggerated anime eyes, duplicate character
```

## 6. Young Lin Qi

### Asset

`young-linqi-v1`

### Character Definition

- 少年/学生时代林奇
- 与成年林奇存在明确亲缘式视觉连续性
- 更青涩
- 普通学生服装
- 短黑发

### Prompt

```text
young Chinese male student, younger version of Lin Qi, short black hair, recognizable facial resemblance to adult Lin Qi, slimmer youthful appearance, simple student clothing, slightly inexperienced expression, cinematic anime drama, nostalgic warm lighting, character reference sheet
```

### Negative

```text
adult man, old face, beard, long hair, muscular body, fantasy costume, different facial identity
```

## 7. Office Location

### Asset

`office-night-v1`

### 必须固定

- 桌面位置
- 显示器位置
- 键盘位置
- 椅子
- 夜间窗户
- 冷色灯光
- 背景办公桌

### Prompt

```text
late-night modern Chinese software office, one programmer workstation, desktop computer and mechanical keyboard, dark office, blue monitor glow, cold fluorescent ceiling light, city night visible through window, cinematic anime background, consistent layout, 16:9
```

### Negative

```text
daytime, bright sunlight, crowded office, different desk layout, multiple monitors unless specified, fantasy architecture, text watermark
```

## 8. Past Room

### Asset

`past-room-v1`

### 视觉目标

与现代办公室形成明显反差：

- 温暖
- 普通
- 怀旧
- 安静
- 生活化

### Prompt

```text
modest room from the past, warm nostalgic lighting, simple desk, old computer, ordinary student living space, quiet atmosphere, cinematic anime background, emotional time travel drama, 16:9
```

## 9. Props

### `computer-v1`

现代办公电脑，黑色显示器，作为异常事件核心道具。

### `keyboard-v1`

机械键盘，黑色，RGB/蓝色背光；必须保持按键布局和外形稳定。

### `monitor-v1`

显示器是系统异常、`ERROR: 天道协议冲突` 和时空漩涡的视觉入口。

## 10. Voice Assets

### Adult Voice

`adult-linqi-voice-v1`

目标：成熟、低沉、克制、有思考感。

对白：

```text
Shot 06：什么情况……
Shot 16：这一次……换我来教你。
```

### Young Voice

`young-linqi-voice-v1`

目标：年轻、自然、略带青涩。

第一集可暂时不安排长对白，主要用于环境反应和后续 Episode。

## 11. Asset-to-Shot Mapping

### Adult Lin Qi

```text
02 03 05 06 08 09 10 11 12 13 15 16
```

### Young Lin Qi

```text
15 16
```

### Office

```text
02 03 04 05 06 07 08 09 10 11 12
```

### Past Room

```text
15 16
```

### Computer / Monitor

```text
03 04 05 07 10 11 12
```

### Keyboard

```text
03 05
```

## 12. Asset Lock Gates

### Gate A — Style

通过条件：

- 视觉方向明确
- 角色和背景风格匹配
- 适合 Image-to-Video

### Gate B — Character

通过条件：

- 面部稳定
- 发型稳定
- 服装稳定
- 年龄正确
- 身体比例稳定

### Gate C — Location

通过条件：

- 布局稳定
- 光照稳定
- 摄像机角度可复用

### Gate D — Voice

通过条件：

- 音色符合角色
- 普通对白自然
- 情绪可控

只有 A/B/C/D 全部通过，Episode 001 才进入批量 Shot 生成。

## 13. Cost Strategy

第一集资产生成优先使用当前可用的免费额度/平台能力。

如果某个核心资产免费生成质量不够：

```text
Free
 ↓ FAIL
Low-cost generation
 ↓
Approve
 ↓
Reuse across Episodes
```

核心 Character / Location 属于值得一次性投入的资产；普通 Shot 不重复投入。

## 14. Version Policy

任何核心资产发生重大修改：

```text
v1 → v2
```

禁止：

```text
修改 v1 内容
```

历史 Episode 必须继续引用原版本。

## 15. Final Manifest

第一集正式生产前，最终状态应为：

```text
[ ] anime-cinematic-v1 LOCKED
[ ] linqi-v1 LOCKED
[ ] young-linqi-v1 LOCKED
[ ] office-night-v1 LOCKED
[ ] past-room-v1 LOCKED
[ ] computer-v1 APPROVED
[ ] keyboard-v1 APPROVED
[ ] monitor-v1 APPROVED
[ ] adult-linqi-voice-v1 LOCKED
[ ] young-linqi-voice-v1 LOCKED
```

## 16. 下一生产动作

Manifest 完成后不再继续扩展第一集架构。

直接进入：

```text
Style Generation
 ↓
Character Generation
 ↓
Character Review
 ↓
Location Generation
 ↓
Location Review
 ↓
Keyframe Generation
 ↓
Video Generation
```

这份 Manifest 是 Episode 001 的资产生产入口。