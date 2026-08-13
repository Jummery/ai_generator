# 个人免费优先 AI 漫剧工厂 MVP

## 1. 产品定位

`ai_generator` 第一阶段定位为个人使用的 AI 漫剧生产工具，而不是商业 SaaS、模型平台或 GPU 推理平台。

核心目标：

> 用尽可能低的成本，优先利用免费或已有云端 AI 能力，将个人创意持续生产为可观看的 MP4 漫剧视频。

不建设：

- 基础模型
- 本地 GPU 推理集群
- 多租户 SaaS
- 商业计费系统
- 对外模型 API
- 自研 TTS / 视频 / 图片基础模型

---

## 2. Free-First 原则

所有生产任务按照以下优先级路由：

```text
免费额度 / 免费平台
        ↓
低成本云端 API
        ↓
高质量付费模型
```

但“免费”不是唯一指标。系统必须同时考虑：

- 输出质量
- 稳定性
- 速度
- 额度
- 商用/个人使用授权
- API 可用性
- 生成失败率

因此最终策略是：

> 免费优先，而不是免费强制。

---

## 3. MVP 的核心生产链

第一版只解决 7 个问题：

```text
创意
 ↓
故事 / 剧本
 ↓
分镜
 ↓
角色与场景参考图
 ↓
动态镜头
 ↓
配音 / BGM / 字幕
 ↓
MP4
```

重点是避免所有 Shot 都直接调用昂贵的视频模型。

---

## 4. Shot Generation Mode

每个 Shot 必须拥有生成模式：

```text
STATIC
MOTION_COMIC
IMAGE_TO_VIDEO
TEXT_TO_VIDEO
```

### STATIC

适合：

- 封面
- 静态插画
- 对话停帧
- 转场

### MOTION_COMIC

优先用于普通镜头：

```text
Key Frame
 ↓
Pan / Zoom / Ken Burns
 ↓
字幕 / Voice / SFX
 ↓
Video Shot
```

这是 MVP 的主要低成本模式。

### IMAGE_TO_VIDEO

用于：

- 人物动作
- 转身
- 行走
- 镜头运动
- 需要真实动态的关键镜头

### TEXT_TO_VIDEO

仅用于：

- 特殊视觉效果
- 穿越
- 爆炸
- 高动态场景
- 其他无法通过图片动画完成的镜头

默认不作为大规模生产方式。

---

## 5. 生产成本策略

假设一集包含 50 个 Shot：

```text
普通 Shot
→ MOTION_COMIC

重要 Shot
→ IMAGE_TO_VIDEO

核心高潮 Shot
→ TEXT_TO_VIDEO / 高质量 Video Provider
```

目标不是让每个 Shot 都达到最高模型质量，而是：

> 在有限预算下，让整集的观感达到最大化。

---

## 6. 免费优先 Router

```text
Shot
 ↓
任务分类
 ↓
判断是否可以 Motion Comic
 ├── YES → 本地/低成本合成
 └── NO
      ↓
检查免费 Provider
 ├── 可用 → 免费 Provider
 └── 不可用
      ↓
检查低成本 Provider
 ├── 可用 → 低成本 Provider
 └── 不可用
      ↓
高质量 Provider
```

Router 必须记录：

- Provider
- Model
- Generation Mode
- 成本
- 耗时
- 成功/失败
- 重试次数

---

## 7. 本地处理只承担确定性工作

“云端 AI”与“本地部署 AI”必须区分。

MVP 不在本地运行 AI 模型，但允许使用本地工具处理最终媒体，例如：

```text
图片
 ↓
本地媒体处理
 ↓
字幕
 ↓
音频混合
 ↓
视频拼接
 ↓
MP4
```

优先使用 FFmpeg 等确定性媒体工具完成：

- 图片转视频
- 缩放
- 平移
- 转场
- 音频混合
- BGM Ducking
- 字幕烧录
- 视频拼接
- 编码

这样可以显著降低 AI API 消耗。

---

## 8. 第一版内容生产流程

### Step 1：创意

用户输入一句话或一段故事梗概。

### Step 2：AI Director

生成：

- 故事主线
- 世界观
- 角色
- 场景
- 分集计划

### Step 3：Character Bible

确定主角、配角和视觉参考图。

角色必须有稳定 ID 和版本。

### Step 4：Episode Script

生成单集剧本。

### Step 5：Storyboard

生成 Scene / Shot。

### Step 6：Key Frame

为 Shot 准备图片。

### Step 7：Motion / Video

根据 Shot Generation Mode 选择：

- 静态
- 图片动画
- Image-to-Video
- Text-to-Video

### Step 8：Voice

按照 Character Voice Profile 生成对白。

### Step 9：Timeline

组合：

```text
Video
Voice
BGM
SFX
Subtitle
```

### Step 10：Render

生成 MP4。

### Step 11：个人审核

用户观看并只重做有问题的 Shot。

---

## 9. MVP 不追求一次生成整集

系统必须支持：

```text
Episode
 ↓
Shot 001
Shot 002
Shot 003
...
Shot N
```

任何一个 Shot 失败，只重新生成该 Shot。

禁止：

```text
Shot 37 失败
 ↓
整集重新生成
```

正确方式：

```text
Shot 37
 ↓
重新生成
 ↓
替换 Artifact
 ↓
重新 Render
```

---

## 10. 免费策略的现实边界

免费额度不是稳定生产资源。

因此系统必须允许 Provider 随时进入：

```text
AVAILABLE
LIMITED
QUOTA_EXHAUSTED
UNAVAILABLE
DISABLED
```

免费 Provider 不可用时，不应阻塞整个项目。

可以自动切换到：

```text
Motion Comic
```

或者低成本 Provider。

---

## 11. 第一阶段不做复杂 AI Router

MVP 不需要机器学习式模型推荐。

第一版只采用明确规则：

```text
if 可以图片动画:
    MOTION_COMIC
elif 有免费 Provider:
    FREE_PROVIDER
elif 预算允许:
    LOW_COST_PROVIDER
else:
    WAIT / MANUAL_SELECT
```

等积累真实生产数据以后，再根据：

- 质量评分
- 成功率
- 成本
- 延迟
- 重试率

优化 Provider Routing。

---

## 12. 个人生产目标

第一阶段验证目标不是商业收入，而是：

> 一个普通个人能否在较低成本下持续完成一部 10～50 集 AI 漫剧。

建议 MVP 指标：

| 指标 | 目标 |
|---|---|
| 单集生成 | 可重复 |
| Shot 失败可重试 | 100% |
| 人物资产复用 | 100% |
| 字幕自动生成 | 100% |
| MP4 自动合成 | 100% |
| 普通镜头 Motion Comic | 支持 |
| 关键镜头 AI Video | 支持 |
| 单集成本统计 | 支持 |
| Provider 切换 | 支持 |

---

## 13. 第一阶段明确不追求

```text
❌ 电影级全 AI 视频
❌ 全部 Shot 都使用 Video AI
❌ 完全无人审核
❌ 一次生成 50 集
❌ 多租户
❌ SaaS 商业化
❌ 自研模型
❌ 本地大模型部署
```

优先证明：

> “个人 + AI + 少量人工审核”可以稳定产出连续可观看的漫剧。

---

## 14. 最终 MVP 闭环

```text
                  个人创意
                     ↓
                AI Director
                     ↓
               Story / Episode
                     ↓
                Character Bible
                     ↓
                  Storyboard
                     ↓
                    Shot
                     ↓
             Free-First Router
                     ↓
        ┌────────────┼────────────┐
        ↓            ↓            ↓
   Motion Comic   Free AI      Paid AI
        │            │            │
        └────────────┼────────────┘
                     ↓
              Voice / Music
                     ↓
                  Timeline
                     ↓
                  FFmpeg
                     ↓
                    MP4
                     ↓
                 个人观看
```

## 15. 下一阶段

下一阶段不再继续扩大架构，而是建立真正可执行的 **“免费/低成本 Provider 清单”**，逐项定义：

- 剧本：免费额度与可替代方案
- 图片：免费网页能力 / API / 低成本 API
- 视频：免费额度 / 低成本 API
- TTS：免费额度 / 免费本地方案
- 音乐：免费方案
- 字幕：本地确定性方案
- Render：FFmpeg

并为每个 Provider 定义统一的 `Capability / Cost / Quota / Availability` 数据模型。
