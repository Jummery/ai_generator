# Episode 001 MVP Production Plan

## 1. 目标

使用“免费优先”策略完成第一集约 120 秒的 AI 漫剧样片，验证从创意、剧本、角色、分镜到最终 MP4 的完整生产链。

故事原型：**我穿越过去教导以前的自己成才**。

第一集目标不是追求电影级画质，而是验证：

- 故事是否成立
- 主角视觉是否稳定
- Shot 是否可以独立生产和重做
- 普通镜头是否可以 0 成本/极低成本完成
- 少量关键镜头是否可以按需升级为 AI Video
- 配音、字幕、音乐和画面能否形成完整成片
- 任意失败镜头是否不会拖垮整集生产

## 2. 第一集建议结构

总时长：约 120 秒。

| 段落 | 时间 | 目的 |
|---|---:|---|
| Hook | 0–5s | 3 秒内建立异常事件 |
| Setup | 5–25s | 介绍林奇和现实处境 |
| Inciting Event | 25–50s | 天道协议冲突出现 |
| Escalation | 50–80s | 屏幕异常、空间失控 |
| Transformation | 80–105s | 林奇被吸入屏幕 |
| Cliffhanger | 105–120s | 穿越后看到“过去的自己” |

## 3. Shot 规划

建议第一集控制在约 16 个核心 Shot，而不是一开始追求大量镜头。

| Shot | 内容 | 默认模式 | 成本策略 |
|---|---|---|---|
| 01 | 深夜写字楼外景 | Motion Comic | 免费优先 |
| 02 | 林奇坐在电脑前 | Motion Comic | 免费优先 |
| 03 | 蓝光映脸特写 | Motion Comic | 免费优先 |
| 04 | 手指快速敲键盘 | Motion Comic | 免费优先 |
| 05 | 键盘产生电弧 | Image→Video | 关键动态 |
| 06 | 屏幕突然黑掉 | Motion Comic | 免费优先 |
| 07 | 红色 ERROR 弹窗 | Motion Comic | 免费优先 |
| 08 | 林奇惊讶 | Motion Comic | 免费优先 |
| 09 | 天道协议文字持续出现 | Motion Comic | 免费优先 |
| 10 | 屏幕形成旋涡 | Image→Video | 重点镜头 |
| 11 | 林奇身体被拉向屏幕 | Image→Video | 重点镜头 |
| 12 | 眼睛/脸部极近特写 | Motion Comic | 免费优先 |
| 13 | 世界碎裂 | Image→Video | 高潮镜头 |
| 14 | 白光/黑场转场 | Motion Comic | 免费优先 |
| 15 | 年轻时期的林奇出现 | Motion Comic | 角色一致性 |
| 16 | 主角意识到自己穿越 | Image→Video 或 Motion Comic | 结尾升级 |

## 4. 角色资产

第一集只建立一个核心角色：林奇。

### 林奇当前版本

- Character ID：LINQI-001
- 年龄：成年程序员
- 身份：程序员
- 核心视觉特征：固定发型、脸型、服装轮廓
- 常态服装：深色程序员风格服装
- 场景：深夜办公室
- 视觉风格：统一二次元/漫剧风格

角色必须先生成 Reference Set，再生产 Shot。

后续镜头不得重新发明人物外观。

## 5. 免费优先生产策略

### 默认

```text
Character Reference
        ↓
静态图
        ↓
Camera Motion / Pan / Zoom
        ↓
对白
        ↓
字幕
        ↓
BGM / SFX
```

### 关键镜头

```text
Reference Image
        ↓
Image → Video
        ↓
QC
        ↓
通过后进入 Timeline
```

### 预算耗尽

```text
Video Provider 不可用
        ↓
自动降级
        ↓
Motion Comic
        ↓
继续生产
```

## 6. 音频

第一集采用最小音频组合：

- 主角 TTS
- 系统/协议提示音
- 键盘声
- 电弧声
- 屏幕异常声
- 穿越冲击声
- 一条持续 BGM

音频先按 Shot 管理，再进入 Episode Timeline。

## 7. 字幕

字幕以对白和系统提示为主要内容。

系统提示例如：

```text
ERROR: 天道协议冲突
```

字幕必须具有时间轴信息，并与 Shot/Audio 绑定。

## 8. QC

第一集只做五项核心 QC：

1. 人物脸部/服装是否明显漂移
2. Shot 是否出现严重画面异常
3. 台词与人物动作是否基本对应
4. 音频是否削波、断裂或明显不同步
5. 成片是否存在黑帧、无声段、字幕越界

QC 失败时只重做当前 Shot，不重新生成整集。

## 9. 第一集完成标准

### P0 必须满足

- 120 秒左右完整 MP4
- 16 个核心 Shot 全部有结果
- 主角外观基本统一
- 有对白
- 有字幕
- 有 BGM/SFX
- 可以完整播放
- 可以从单个 Shot 重新生成

### P1

- 3 个关键镜头使用 Image→Video
- 画面节奏基本符合短视频结构
- 开头 3 秒具有明确 Hook
- 结尾形成下一集悬念

### P2

- AI 自动 QC
- 自动选择免费/低成本 Provider
- 自动成本统计
- 自动生成多个质量版本

## 10. 第一集验证指标

不是用“电影级画质”作为第一成功指标，而是验证生产闭环：

```text
创意
 ↓
剧本
 ↓
角色
 ↓
分镜
 ↓
Shot
 ↓
AI Generation
 ↓
Audio
 ↓
Timeline
 ↓
QC
 ↓
MP4
```

如果这个闭环跑通，再扩展到 Episode 002–050。

## 11. 下一阶段

第一集 MVP 之后，进入 **Character Consistency System**：

- Character Reference Set
- Character Version
- Reference Selection
- Prompt Constraint
- Shot Character Binding
- Regeneration Without Character Drift

这是从“能生成视频”走向“能持续生成同一部漫剧”的关键能力。
