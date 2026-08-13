# Character Consistency System

## 1. 目标

确保同一角色在不同 Episode、Scene、Shot 中保持稳定的身份特征、视觉风格、服装状态与叙事连续性。

核心原则：

> Character 是长期资产，Shot 是短期生成任务。任何单个镜头失败，都不应破坏 Character 基准。

## 2. Character Bible

每个角色建立唯一 Character Identity，包括：

- character_id
- canonical_name
- 年龄区间
- 性别表现
- 身高/体型
- 发型与发色
- 面部特征
- 眼睛/眉毛/鼻子/嘴部特征
- 常用服装
- 配色
- 性格与表演特征
- 禁止变化项
- 可变化项

## 3. Reference Set

角色不使用单张参考图作为唯一依据，而建立 Reference Set：

```text
Character
 ├─ Front
 ├─ 3/4
 ├─ Side
 ├─ Full Body
 ├─ Expression
 └─ Costume Variants
```

Reference Set 必须有版本号，例如 `linqi-v1`、`linqi-v2`。

## 4. Character Version

角色视觉资产采用不可变版本思路：

```text
linqi
 ├─ v1  初始角色
 ├─ v2  修正脸型
 └─ v3  新服装
```

已发布 Episode 默认锁定对应版本，不因后续角色更新而自动改变。

## 5. Shot Binding

每个 Shot 必须明确绑定：

```text
Shot
 ├─ character_id
 ├─ character_version
 ├─ reference_set
 ├─ costume_state
 └─ expression_state
```

禁止仅依赖自然语言角色名称生成角色。

## 6. Prompt Constraint

生成请求分为三层：

```text
Global Style
      +
Character Constraints
      +
Shot Description
```

Character Constraints 的优先级高于普通 Shot 描述。

## 7. Consistency QC

生成后执行视觉 QC，重点检查：

- 脸型
- 发型
- 发色
- 服装
- 年龄感
- 体型
- 标志性配饰
- 角色身份是否错误

QC 结果：

```text
PASS
REVIEW
FAIL
```

FAIL 只重新生成当前 Shot，不回滚整个 Episode。

## 8. Costume Continuity

服装属于 Episode Continuity 状态。

例如：

```text
Scene 01 → 黑色程序员卫衣
Scene 02 → 黑色程序员卫衣
Scene 03 → 黑色程序员卫衣
Scene 04 → 穿越后服装变化
```

服装变化必须由剧情事件触发，而不是由模型随机决定。

## 9. Expression / State

角色身份与表情状态分离。

```text
Identity
 └─ 固定

State
 ├─ happy
 ├─ angry
 ├─ shocked
 ├─ tired
 └─ crying
```

这样可以生成大量不同表情，同时保持角色身份稳定。

## 10. Generation Strategy

免费优先策略：

1. 首先生成高质量 Character Reference。
2. Reference Set 一旦确认，长期复用。
3. 普通 Shot 尽量使用参考图驱动的图片生成。
4. 动态镜头只在必要时调用 Image-to-Video。
5. 失败时只重试 Shot。

## 11. Character Lock

Episode 进入生产后，可以锁定：

```text
Character Version
Reference Set
Costume State
Style Profile
```

锁定后，生成任务不得自动升级角色版本。

## 12. Character Change Request

如果用户主动修改角色：

```text
Character Change
 ↓
Create New Version
 ↓
Review Reference Set
 ↓
选择生效 Episode
```

禁止直接覆盖历史角色资产。

## 13. 质量优先级

角色一致性优先级：

```text
Identity
 >
Face
 >
Hair
 >
Costume
 >
Body
 >
Expression
 >
Minor Details
```

## 14. 目标

最终实现：

```text
Episode 001 ─┐
Episode 002 ─┤
Episode 003 ─┤
Episode 010 ─┤→ 同一个 Character
Episode 050 ─┘
```

并允许单 Shot 局部重生成，而不影响已通过 QC 的其它资产。
