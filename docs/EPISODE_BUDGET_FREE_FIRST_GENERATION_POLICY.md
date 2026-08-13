# Episode Budget & Free-First Generation Policy

> 个人 AI 漫剧工厂的成本控制规范

## 1. 目标

`ai_generator` 的第一目标不是商业化，而是让个人用户能够以尽可能低的成本持续生成可观看的漫剧。

核心原则：

1. 免费优先。
2. 免费额度优先于付费 API。
3. 普通镜头优先使用图片动态化，而不是视频生成。
4. 付费能力只用于真正影响观感的关键镜头。
5. 单集生成前必须能够预测预算。
6. 达到预算上限后自动降级，而不是中断整集生产。
7. 不因为单个镜头失败而重新生成整集。

---

## 2. 成本等级

| Level | 名称 | 目标 | 适用镜头 |
|---|---|---|---|
| L0 | Free | 0 成本或已有免费额度 | 剧本、分镜、字幕、普通图片动画 |
| L1 | Low Cost | 极低成本 | 普通 Image-to-Video、TTS |
| L2 | Premium Shot | 高质量但受控 | 主角、高潮、战斗、转场 |
| L3 | Premium Episode | 精品单集 | 开场、高潮、结尾等少量镜头 |

默认策略为 `L0 -> L1 -> L2`，禁止默认使用 L3。

---

## 3. 单集预算档位

预算不是要求每一集必须花完，而是允许的最大外部 AI 成本。

### ¥0 模式

目标：完整生成可观看版本。

```text
LLM
  ↓
Script / Storyboard
  ↓
Image
  ↓
Motion Comic
  ↓
TTS / 免费音频
  ↓
Subtitle
  ↓
FFmpeg
  ↓
MP4
```

要求：即使没有付费视频生成，也必须能够完成整集。

### ¥5 模式

目标：在零成本版本基础上增加少量动态镜头。

建议：

- 80%~95% 普通镜头使用 L0。
- 少量关键镜头使用 L1。
- 禁止连续大量消耗视频生成额度。

### ¥10 模式

目标：提升关键剧情镜头质量。

建议预算分配：

- 60%~85% L0。
- 10%~30% L1。
- 1~5 个关键镜头使用 L2。

### ¥20 模式

目标：形成具有明显“AI 漫剧”观感的完整单集。

建议：

- 普通镜头仍保持 L0。
- 关键动作镜头使用 L1。
- 开场、高潮、结尾优先使用 L2。
- 不允许因为预算充足而把全部镜头升级。

---

## 4. Shot Budget

每个 Shot 在生成之前计算：

```text
estimated_cost
provider
model
mode
priority
fallback_mode
```

并根据以下因素决定生成等级：

```text
剧情重要度
角色重要度
动作复杂度
视觉复杂度
镜头时长
当前预算
Provider 免费额度
Provider 可用性
```

---

## 5. Shot 优先级

### P0：必须高质量

- 第 1 个镜头 / 开场 Hook
- 主角首次登场
- 核心反转
- 重大情绪变化
- 高潮
- 集尾悬念

### P1：建议升级

- 重要人物对话
- 核心动作
- 关键场景转换
- 重要道具

### P2：普通

- 过场
- 环境镜头
- 普通对话
- 静态反应

### P3：可完全免费

- 背景
- 建立镜头
- 重复场景
- 非关键补镜

默认只有 P0/P1 有资格自动申请付费生成。

---

## 6. 自动降级策略

如果当前预算不足：

```text
Premium Video
    ↓ 预算不足
Image-to-Video
    ↓ 不可用
Motion Comic
    ↓ 不可用
Static Image + TTS
```

如果 Provider 失败：

```text
Provider A
   ↓ failure
Provider B
   ↓ failure
Local/FFmpeg fallback
```

这里的 Local 指免费的视频后处理能力，不代表本项目需要本地部署 AI 模型。

---

## 7. Budget Gate

每次付费生成前必须执行 Budget Gate：

```text
当前已消费
+
预计本次消费
+
最低重试成本
<=
Episode Budget
```

只有满足条件才允许调用付费 Provider。

否则自动降级。

---

## 8. 重试策略

禁止无限重试。

建议：

```text
第一次失败 → 同 Provider 重试
第二次失败 → Provider fallback
第三次失败 → 免费降级方案
```

P0 镜头可以由用户手动批准额外预算后再次生成。

---

## 9. 成本记录

每次外部 AI 调用必须记录：

```text
project_id
episode_id
scene_id
shot_id
provider
model
request_id
usage
estimated_cost
actual_cost
currency
started_at
completed_at
status
```

这样可以得到：

```text
本集成本
本季成本
单镜头平均成本
Provider 成本
免费额度消耗
失败成本
重试成本
```

---

## 10. 预算预估

正式生成前先执行 Dry Run：

```text
Episode
  ↓
Storyboard
  ↓
Shot classification
  ↓
Provider selection
  ↓
Cost estimation
  ↓
Budget simulation
```

输出：

```text
免费版本：预计 ¥0
经济版本：预计 ¥4.80
标准版本：预计 ¥9.60
精品版本：预计 ¥18.70
```

用户选择后才开始真正生成。

---

## 11. 默认策略

个人用户默认：

```text
Budget = ¥0
Mode = FREE_FIRST
Allow_Paid = false
Auto_Downgrade = true
Auto_Retry = true
Max_Retry_Per_Shot = 2
```

如果用户主动打开付费：

```text
Budget = user defined
Mode = COST_OPTIMIZED
Allow_Paid = true
Auto_Downgrade = true
```

---

## 12. 最终产品原则

`ai_generator` 不追求“每一个镜头都是顶级 AI 视频”。

追求的是：

> **即使预算为 0，也能够把故事完整做出来；有预算时，只把钱花在观众真正能感知的镜头上。**

这也是个人 AI 漫剧工厂区别于单纯 AI 视频生成器的核心策略。
