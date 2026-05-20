---
name: design-state-completion
description: >
  当用户已有主页面 Figma 设计稿，并希望补齐业务全情况、全状态、状态矩阵或补全设计稿时使用。
  适用于移动端业务页面，重点覆盖飞猪酒店、会员、活动权益和交易链路。
  不用于单纯 UI 美化、从零页面设计、普通 Figma 搭建，或只补 loading、空态、错误态等平台基础状态。
requires_human: true
knowledge_sink: true
self_evolution: true
author_intent:
  load_mode: description
  model_policy: runtime_default
  tool_policy: require_confirmation_for_writes
---

# Design State Completion

## 能力定义

从已经完成的主页面 Figma 设计稿出发，主动推演业务变量、状态矩阵和需要补齐的页面 / 模块 / 子组件状态，并在用户明确要求时写回 Figma。

核心价值不是“多画几个状态”，而是先判断状态发生在哪一层，再用变量组合过滤出成立且有设计价值的全情况。

默认先输出：

1. 业务状态推演
2. 组件复用预检摘要
3. 状态矩阵
4. 需要人工确认的问题

只有用户明确要求“写回 Figma”“直接生成画稿”“补全设计稿”或同等意图时，才写入原 Figma 文件的新补全设计区。

## 使用边界

使用本 skill：

- 已完成主页面 Figma 链接，希望补全“全情况”“全状态”“业务状态”“场景分支”
- 希望从主态设计反推还缺哪些业务稿
- 希望把状态矩阵和补全稿写回 Figma
- 希望基于自动稿问题、用户反馈或调试期对照信息复盘并沉淀规则

不要使用本 skill：

- 单纯 UI 美化、视觉调优、组件样式调整
- 没有主页面设计稿的从零设计
- 普通 Figma 页面搭建
- 只补 loading、网络失败、通用空态、骨架屏、权限失败等平台基础状态
- 组件 hover / pressed / focused 等低层交互态

平台基础状态除非用户明确要求，否则只可在矩阵中简要标注“由平台基础规范覆盖”，不展开画稿。

## 输入要求

最小输入：

- 主页面 Figma 链接，或 file key + node id
- 简单需求背景
- 页面所属业务域，未说明时默认按飞猪 App 通用移动端页面判断

建议输入：

- 业务规则、PRD、字段说明或接口状态
- 已知必须补充的状态
- 不希望生成的状态范围

正式使用时默认没有手动全情况或标准答案。不得要求用户提供标准答案；必须基于主页面、需求背景、业务来源和组件预检独立输出状态推演、组件复用预检和状态矩阵。

调试 / 回归评测阶段如用户主动提供手动全情况稿，只能用于复盘和规则抽象，不得进入正式生成链路，也不得复制其节点作为生成结果。

## 执行主线

必须按 `references/execution-pipeline.md` 的流程模式判定、阶段 0 能力路由和阶段 1-7 执行流程推进。`SKILL.md` 只定义入口和强约束，阶段细节以 `execution-pipeline.md` 为准。

阶段概要：

0. 推演能力路由：使用 `capability-routing.md` 判断主能力和辅助能力，并选择需要执行的规则检查清单。
1. 业务状态推演：使用 `decision-framework.md` 定位对象层级、变量、合法组合和稳定字段。
2. 组件复用预检：确认原页面组件、实例、component set、variant 覆盖范围。
3. 状态矩阵：生成触发条件、用户含义、页面变化、CTA、生成方式和是否画稿。
4. 子组件变体：复合模块先补齐子组件系统，再组合页面状态。
5. 页面组合态：复制真实页面或真实模块实例，验证状态在页面中的承接效果。
6. 截图 QA：使用 `failure-patterns.md` 主动检查高频失败模式。
7. 复盘沉淀：基于自动稿问题、用户反馈或调试期对照信息抽象规则；只有用户确认后才写入参考文件。

关键强约束：

- 不要一上来平铺状态名；先判断状态发生在页面级、模块级、子结构级还是字段级。
- 阶段 1 前必须先完成推演能力路由；最多选择 1 个主能力和 1 个辅助能力。
- 内部调试资产边界以 `execution-pipeline.md` 为准，正式生成不得依赖调试期材料。
- 阶段 1-3 必须使用 `decision-framework.md`，矩阵主轴必须来自目标对象变量组合。
- 用户明确要求补某个子结构全情况时，矩阵主轴必须对齐该子结构内部变量。
- 写入 Figma 前必须确认目标 file / node、补全区域名称和写入范围；先使用 `figma-use` skill，再调用 `use_figma`。
- 阶段 6 必须使用 `failure-patterns.md`，不能只做截图表面确认。
- 阶段 7 只能在用户确认后写入参考文件，确认前不得声称“已更新文件”。

## 参考文件读取顺序

主线必读：

- `references/execution-pipeline.md`：能力路由 + 固定 7 阶段执行流水线
- `references/capability-routing.md`：推演能力路由和各能力检查重点
- `references/decision-framework.md`：对象定位、变量识别、合法组合过滤、展示策略和稳定字段

按任务读取：

- `references/scenario-decomposition.md`：页面级 / 模块级 / 子结构级 / 内容边界拆解
- `references/state-matrix-template.md`：页面级、模块级、子结构级状态矩阵模板
- `references/business-state-taxonomy.md`：通用业务状态分类、判断变量和不适用规则
- `references/fliggy-domain-patterns.md`：飞猪页面 / 模块落地模式、组件拆分和生成判断

写入 Figma 时读取：

- `references/figma-generation-rules.md`：Figma 写入、命名、组件复用和画布组织规则
- `references/quality-checklist.md`：交付前验收清单
- `references/failure-patterns.md`：高频失败模式和 QA 检查点

复盘时读取：

- `references/case-review-patterns.md`：自动稿问题、用户反馈和调试期对照信息的复盘规则

## 最终回复格式

最终回复必须包含：

### 对标能力

先用一句话说明本次补全需求命中的能力路由：

`对标 {主能力}，辅助能力是 {辅助能力 / 无}。`

能力名称必须来自阶段 0 推演能力路由结果，例如：`对标内容边界布局，辅助能力是页面组合态生成。`

### 补全结果

用一句话说明补全规模：

`已补全 X 个状态：X 个已画稿，X 个仅进入矩阵，X 个为不适用组合。`

### 重点变化

最多列 5 条最影响用户理解和验收的变化：

- 状态 / 场景 A：核心变化
- 状态 / 场景 B：核心变化
- 状态 / 场景 C：核心变化

完整状态矩阵默认写入 Figma 的 `00 状态矩阵`，或在用户要求时再展开，不在最终回复中完整复述。

### 需要人工确认的问题

只列真正影响业务判断的问题。优先使用选择题形式：

```md
1. 问题描述
   - A. 选项一：影响说明
   - B. 选项二：影响说明
   - C. 选项三：影响说明
```

如果没有问题，写：

`暂无必须人工确认的问题。`
