# Episode 001 Keyframe Generation Plan

## 1. Objective

将 Episode 001 的 16 个 Shot 转换为 AI 图片生成任务。

Keyframe 是视频生成前的视觉基准。

流程：

```text
Shot Specification
 ↓
Asset Reference
 ↓
Keyframe Prompt
 ↓
AI Image Generation
 ↓
QC
 ↓
Image-to-Video / Motion Comic
```

## 2. Global Style

Asset:

```text
anime-cinematic-v1
```

统一要求：

- cinematic anime drama
- semi-realistic character
- consistent character identity
- 16:9 composition
- dramatic lighting
- clean visual continuity

## 3. Shot 01

主题：深夜城市开篇

Assets:

```text
Style: anime-cinematic-v1
```

Camera:

```text
wide establishing shot
```

Prompt:

```text
modern city at deep night, skyscrapers with scattered lights, quiet futuristic urban atmosphere, cinematic anime drama style, blue night color tone, dramatic composition, semi-realistic background, 16:9
```

Mode:

```text
IMAGE + slow camera motion
```

## 4. Shot 02

主题：林奇办公室

Assets:

```text
linqi-v1
office-night-v1
```

Camera:

```text
medium shot
```

Prompt:

```text
adult Chinese male programmer Lin Qi sitting in a dark office at night, black hoodie, working in front of computer, blue monitor glow reflecting on face, modern software office background, cinematic anime drama, consistent character identity
```

## 5. Shot 03

主题：疯狂编码

Assets:

```text
linqi-v1
computer-v1
keyboard-v1
```

Camera:

```text
desk close-up
```

Prompt:

```text
programmer hands typing rapidly on black mechanical keyboard, computer screen glow, focused atmosphere, cinematic anime close-up, detailed desk environment
```

## 6. Shot 04

主题：异常代码

Assets:

```text
computer-v1
monitor-v1
```

Monitor State:

```text
corrupted_code
```

Prompt:

```text
computer monitor showing abstract corrupted code patterns, digital glitch effects, dark office environment, red and blue screen flicker, cinematic science fiction anime style
```

说明：文字内容后期合成，不依赖 AI 生成文字。

## 7. Shot 05 ★

主题：键盘电弧爆发

Assets:

```text
linqi-v1
keyboard-v1
office-night-v1
```

State:

```text
electrical_arc
```

Prompt:

```text
shocked programmer sitting at desk, blue electrical arcs erupting from mechanical keyboard, dramatic flash light, monitor glow, cinematic anime science fiction scene, dynamic energy effect
```

Mode:

```text
IMAGE → I2V
```

## 8. Shot 06

主题：林奇惊醒

Assets:

```text
linqi-v1
```

Expression:

```text
shocked
```

Prompt:

```text
adult programmer shocked expression, blue computer light on face, dark office background, cinematic anime close-up, emotional facial expression
```

## 9. Shot 07 ★

主题：系统警告

Assets:

```text
monitor-v1
```

State:

```text
system_error
```

Prompt:

```text
computer screen with intense red warning interface, digital failure atmosphere, dark room, futuristic system error visualization, cinematic anime style
```

文字后期添加。

## 10. Shot 08

主题：系统升级

Prompt:

```text
programmer office surrounded by red digital light, computer emitting futuristic energy, cinematic anime science fiction atmosphere
```

## 11. Shot 09 ★

主题：空间异常

Assets:

```text
office-night-v1
linqi-v1
```

Prompt:

```text
office space bending and distorting around programmer, blue black dimensional energy forming, cinematic anime science fiction, dramatic perspective
```

Mode:

```text
IMAGE → I2V
```

## 12. Shot 10 ★

主题：错误爆发

Prompt:

```text
digital particles exploding around programmer workstation, red warning light, dramatic action scene, cinematic anime science fiction
```

## 13. Shot 11 ★

主题：屏幕漩涡

Assets:

```text
monitor-v1
```

State:

```text
portal
```

Prompt:

```text
computer monitor transforming into a blue space time vortex, dark room, futuristic portal effect, cinematic anime science fiction close-up
```

## 14. Shot 12 ★

主题：被吸入

Assets:

```text
linqi-v1
```

Prompt:

```text
adult programmer being pulled toward a glowing portal, dynamic perspective, strong motion feeling, cinematic anime time travel scene
```

## 15. Shot 13 ★

主题：时空穿越

Prompt:

```text
silhouette of programmer traveling through blue white time tunnel, abstract cosmic energy, cinematic anime science fiction, emotional atmosphere
```

## 16. Shot 14

主题：世界静止

Prompt:

```text
silent frozen transition space, suspended particles, mysterious time stop atmosphere, cinematic anime scene
```

## 17. Shot 15

主题：看见年轻自己

Assets:

```text
linqi-v1
young-linqi-v1
past-room-v1
```

Prompt:

```text
adult Lin Qi seeing his younger self in a nostalgic room, emotional reunion, warm lighting, cinematic anime drama, two character consistency
```

## 18. Shot 16

主题：结尾 Hook

Assets:

```text
linqi-v1
young-linqi-v1
```

Prompt:

```text
adult programmer standing face to face with younger self, determined expression, warm nostalgic room, cinematic anime ending scene, emotional time travel story
```

## 19. Generation Priority

第一批生成：

```text
linqi-v1 reference
↓
office-night-v1
↓
Shot 02
Shot 06
Shot 15
Shot 16
```

确认角色连续后，再生成特效镜头。

## 20. Critical Video Shots

需要 Image-to-Video：

```text
05
07
09
10
11
12
13
```

普通镜头：

```text
01
02
03
04
06
08
14
15
16
```

## 21. QC Rules

检查：

- Character consistency
- Location consistency
- Prop consistency
- Camera continuity
- Lighting continuity
- Story readability

## 22. Next Stage

Keyframe 完成后进入：

```text
AI Video Generation
+
TTS
+
BGM
+
SFX
+
Timeline Assembly
```
