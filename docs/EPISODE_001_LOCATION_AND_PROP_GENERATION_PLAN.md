# Episode 001 Location & Prop Generation Plan

## 1. Objective

在角色资产之后锁定第一集核心场景和道具，确保 16 个 Shot 在空间布局、光照和关键物件上保持连续性。

核心资产：

```text
office-night-v1
past-room-v1
computer-v1
keyboard-v1
monitor-v1
```

## 2. Generation Order

```text
anime-cinematic-v1
 ↓
linqi-v1 / young-linqi-v1
 ↓
office-night-v1 / past-room-v1
 ↓
computer-v1 / keyboard-v1 / monitor-v1
 ↓
16 Keyframes
```

## 3. Office — `office-night-v1`

### Identity

深夜现代办公室，林奇的主要现实世界空间。

### Fixed Layout

- 主工作桌位于画面核心区域
- 显示器固定在桌面中央/略偏右
- 键盘固定在显示器前方
- 林奇座位固定在桌前
- 夜间窗户位于背景
- 冷色人工照明
- 背景保持简洁，避免随机增加人物

### Base Prompt

```text
late-night modern Chinese software office, one programmer workstation, fixed desk layout, black desktop monitor centered on desk, black mechanical keyboard directly in front of monitor, ergonomic office chair, dark window showing city lights, cold fluorescent ceiling light mixed with blue monitor glow, cinematic anime drama background, semi-realistic anime rendering, clean composition, consistent architecture, 16:9
```

### Negative

```text
daylight, crowded office, random people, multiple desks in foreground, different monitor arrangement, different keyboard, fantasy architecture, luxury office, bright colorful lighting, text, logo, watermark
```

## 4. Office Reference Set

至少生成：

```text
office-night-v1/
├── wide-front
├── desk-front
├── side-angle
├── monitor-angle
└── empty-room
```

用途：

- `wide-front`：Shot 01/02/08/11/12
- `desk-front`：Shot 03/04/05/06
- `side-angle`：Shot 09/10
- `monitor-angle`：Shot 07/11
- `empty-room`：建立环境基准

## 5. Past Room — `past-room-v1`

### Identity

年轻林奇所在的过去空间，需要与现代办公室形成明显视觉反差。

### Fixed Elements

- 普通书桌
- 老式电脑
- 简单椅子
- 学习用品
- 温暖室内光
- 生活化背景

### Base Prompt

```text
modest student room from the past, simple wooden desk, old computer monitor, ordinary chair, books and study materials, warm nostalgic indoor lighting, quiet evening atmosphere, slightly dated interior, cinematic anime drama, semi-realistic anime background, emotional time travel mood, consistent layout, 16:9
```

### Negative

```text
modern luxury bedroom, futuristic computer, gaming setup, neon room, crowded room, fantasy architecture, daylight, random people, watermark, logo, text
```

## 6. Past Room Reference Set

```text
past-room-v1/
├── wide-front
├── desk-front
├── character-angle
└── empty-room
```

## 7. Computer — `computer-v1`

电脑是第一集最重要的 Prop 之一。

必须保持：

- 显示器外形
- 边框比例
- 支架
- 桌面位置
- 屏幕方向

### Prompt

```text
black modern desktop computer monitor, thin but clearly defined black bezel, simple dark stand, realistic proportions, cinematic anime prop design, clean product reference, front and three-quarter views, consistent shape
```

## 8. Keyboard — `keyboard-v1`

键盘是 Shot 05 电弧事件的核心道具。

必须锁定：

- 黑色机械键盘
- 按键布局
- 长宽比例
- 蓝色/冷色背光

### Prompt

```text
black mechanical programmer keyboard, compact professional layout, dark keycaps, subtle blue backlight, clean modern design, cinematic anime prop reference, front and three-quarter views, consistent key arrangement
```

### Special Constraint

电弧效果属于动态状态，不修改键盘本体设计：

```text
keyboard-v1
+
electrical_arc_state
```

## 9. Monitor — `monitor-v1`

Monitor 与 Computer 可以关联，但屏幕内容作为独立状态处理。

### Base State

```text
normal_code
```

### Special States

```text
corrupted_code
system_error
portal
```

不要把 `ERROR: 天道协议冲突` 永久写入 Monitor Asset，否则普通镜头无法复用。

## 10. Prop State Model

```text
Prop Identity
+
Prop State
```

例如：

```text
monitor-v1
+
state=system_error
```

或者：

```text
keyboard-v1
+
state=electrical_arc
```

## 11. Location Continuity Rules

Shot 02～12 中办公室必须保持：

- 桌子位置一致
- 显示器位置一致
- 键盘位置一致
- 主灯光方向一致
- 窗户位置一致

允许变化：

- Camera Angle
- Camera Distance
- Monitor Content
- Lighting Intensity
- FX

禁止无理由变化：

- 房间布局
- 桌子
- 显示器型号外观
- 键盘外观
- 窗户位置

## 12. Lighting Continuity

办公室灯光状态按剧情逐渐变化：

```text
Shot 01-04
Normal Cold Light
        ↓
Shot 05
Electrical Flash
        ↓
Shot 07-10
Red Warning Light
        ↓
Shot 11-13
Blue / Black Portal Light
```

注意：这是 Lighting State 的变化，不是 Location Version 的变化。

## 13. Camera Reference

为办公室预先定义几个常用机位：

```text
office-wide
office-medium
office-desk-close
office-monitor-close
office-side
```

这样 Shot Planner 只引用 Camera ID，不重新描述整个房间。

## 14. Asset QC

### Location QC

- Layout consistency
- Perspective consistency
- Lighting consistency
- Background continuity
- No random people
- No unwanted objects

### Prop QC

- Shape consistency
- Scale consistency
- Position consistency
- Material consistency

## 15. Generation Selection

每个资产第一轮建议生成 4～8 个候选版本。

选择一个作为 baseline，再生成必要角度。

不追求所有角度都由独立生成得到完全不同的设计。

## 16. Free-First Strategy

Location 和 Prop 优先使用免费图片生成能力。

由于这些资产会复用多个 Shot 和多个 Episode，若免费结果不足，可对核心资产进行一次低成本升级。

```text
Free Candidate
 ↓
QC
 ↓ FAIL
Low-cost Candidate
 ↓
Approve
 ↓
Reuse
```

## 17. Episode 001 Mapping

### Office

```text
Shot 01-12
```

### Past Room

```text
Shot 15-16
```

### Computer

```text
Shot 03-05
Shot 07
Shot 10-12
```

### Keyboard

```text
Shot 03
Shot 05
```

### Monitor

```text
Shot 03-04
Shot 07
Shot 10-12
```

## 18. Lock Gate

进入 Keyframe 批量生成前：

```text
[ ] office-night-v1 APPROVED
[ ] past-room-v1 APPROVED
[ ] computer-v1 APPROVED
[ ] keyboard-v1 APPROVED
[ ] monitor-v1 APPROVED
[ ] Camera references defined
[ ] Lighting states defined
[ ] Location continuity reviewed
```

## 19. Next Production Stage

完成场景和道具锁定后，立即进入：

```text
16 Shot Keyframe Generation
```

每个 Keyframe 只引用：

```text
Style
+
Character ID / Version
+
Location ID / Version
+
Prop IDs / Versions
+
Camera ID
+
Action
+
Expression
+
Lighting State
```

这样可以让第一集真正进入批量 AI 生图，而不是每个镜头重新从零描述整个世界。