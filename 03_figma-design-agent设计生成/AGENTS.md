# Figma Design Agent 运行入口

本文件是 `figma-design-agent` 的真正执行入口。每次接到用户设计需求时，先读这里，再按状态机推进任务。

## 目标

把上游产品设计需求转成可编辑、可继续迭代、符合飞猪设计规范的 Figma 设计稿。

最终交付必须是 Figma 可编辑结构：frame、text、component、variant、variable、style、auto layout 和必要的独立素材。禁止把整张方案图、截图或 HTML 截图作为最终设计。

## 每次任务先读

按顺序读取：

1. `AGENTS.md`
2. `WORKFLOW_AGENT.md`
3. `STATE_AND_RESOURCES.md`
4. `CHECKLISTS.md`
5. `PROMPTS.md`
6. 路由需要的设计规范：
   - 新增页面 / 完整流程 / 跨页面模式：`design-rules.md`、`references/design-system/agent-tokens.json` 和 `references/design-system/agent-component-specs.json`
   - 已有页面新增模块 / 局部布局匹配：`figma design agent 页面级设计规范.md`
   - 新增模块 / 新增页面生成方案图前：`references/style-library/index.md`，并从 `/Users/tianzhongyi/Documents/figma-design-executor/assets/style-library` 查找参考图
7. 用户提供的 Figma 链接、目标页面、目标模块、文案、数据和参考素材。

只读取当前路由真正需要的资源。不要为“以防万一”加载完整资源库。

## 任务记录

每次任务都要创建或更新一条运行记录，放在 `runs/` 目录。

命名格式：

```text
runs/YYYYMMDD-HHMM-short-slug.json
```

例如：

```text
runs/20260515-1145-hotel-list-add-member-module.json
```

记录内容以 `runs/template.json` 为模板，并与 `STATE_AND_RESOURCES.md` 中的 schema 保持一致。

状态推进时同步更新运行记录：

- `parse_request` 完成后写入需求摘要和目标。
- `route_tasks` 完成后写入每个子任务路由。
- `read_context` 完成后写入已读取资源。
- `generate_design_image` 完成后写入所选参考图、方案图提示词和图片地址。
- `await_confirmation` 等待时标记为 `pending`。
- 用户确认后标记为 `confirmed`。
- `write_figma` 完成后写入目标节点和修改摘要。
- `self_check` 完成后写入检查结果。
- `handoff` 完成后写入交付说明和剩余风险。

## 执行状态机

按 `WORKFLOW_AGENT.md` 定义的状态推进：

1. `parse_request`
2. `route_tasks`
3. `read_context`
4. `generate_design_image`
5. `await_confirmation`
6. `prepare_assets`
7. `write_figma`
8. `self_check`
9. `handoff`

只能在路由规则允许时跳过状态。

## 路由规则

### 修改已有页面：`modify_existing_page`

适用：

- 修改现有页面、模块、组件、文案、布局或样式。

执行：

- 读取目标 Figma 上下文。
- 读取相关变量、组件和目标区域周边布局。
- 跳过 `generate_design_image` 和 `await_confirmation`。
- 使用 `use_figma` 做最小必要改动。
- 写入后自检并交付。

### 新增模块：`add_module`

适用：

- 在已有 Figma 页面中新增一个模块。

执行：

- 读取所在页面 Figma 上下文。
- 读取页面级设计规范。
- 读取相邻模块节奏和组件使用习惯。
- 从风格参考图库选择 1-3 张相关参考图。
- 生成方案图。
- 停下等待用户确认。
- 用户确认前禁止写入 Figma。
- 确认后准备必要素材，并用 `use_figma` 还原为可编辑模块。

### 新增页面：`add_page`

适用：

- 新增一个完整页面、screen 或完整流程。

执行：

- 读取产品级设计规范和 Figma token。
- 读取 Figma 组件规范清单。
- 读取最接近的页面类型或已有产品模式。
- 从风格参考图库选择 1-3 张相关参考图。
- 生成方案图。
- 停下等待用户确认。
- 用户确认前禁止写入 Figma。
- 确认后准备必要素材，并用 `use_figma` 还原为可编辑页面。

## 必须暂停的情况

遇到以下情况必须暂停并问用户：

- 目标 Figma 文件、页面或模块不明确。
- 同名页面、模块或元素存在多个候选。
- 用户需求无法判断是修改已有内容还是新增内容。
- 新增模块或新增页面的方案图已生成，等待方向确认。
- 设计规范与用户要求冲突，且会影响最终视觉或范围。

方案图确认必须使用这三个选项：

```text
1. 确认这个方向，继续还原到 Figma
2. 按当前方向重新生成
3. 调整方向后重新生成
```

## Figma 写入规则

- 所有 Figma 写入必须使用 `use_figma`。
- 写入前优先读取目标节点上下文、截图、变量和组件信息。
- 优先复用当前文件或设计系统中的 component、variable、text style、effect style。
- 修改 `酒店卡片/酒店信息区/价格优惠区/优惠条/异型优惠标签｜LIST.HOTEL_CARD.SPECIAL_PROMO_BADGE#HUNDRED_BILLION_SUBSIDY` 时，文本必须使用字体包 `/Users/tianzhongyi/Library/Fonts/FliggyFonts-MediumItalic.ttf` 对应的原字体。FigmaAgent 本机解析名为 `Fliggy Fonts / Medium Italic`，PostScript 为 `FliggyFonts-MediumItalic`；历史 Figma 节点里也可能显示为 `FliggyFont / Medium Italic`。写入前必须先用 `await figma.listAvailableFontsAsync()` 找到该字体的可加载 family/style，并 `await figma.loadFontAsync(found.fontName)` 成功后再改文字；如果当前 Figma/MCP 环境无法加载该字体，必须暂停并报告，不允许自动降级到其他字体。
- 新增内容必须使用清晰图层命名和合理 auto layout。
- 模块高度由内容驱动，不能裁切内容。
- 不允许把需求说明写进画布。
- 不允许把方案图整张粘贴成最终设计。
- 位图只用于照片、复杂装饰、纹理或无法干净矢量化的素材。
- 新增模块或新增页面写入 Figma 时，必须以用户确认的 image gen 方案图为视觉还原标准。禁止只按文字需求、设计规范或个人判断直接写入；写入前和写入过程中必须持续对照确认方案图视觉锚点。
- 确认方案图中的装饰元素、轻拟物 icon、插画、光效、质感物件、产品 mockup 或复杂纹理必须切图并处理为透明 PNG 后传入 Figma。禁止在 Figma 还原阶段用基础可编辑图层重新绘制这些元素来替代方案图视觉。
- 交付前必须保存并记录确认方案图与 Figma 设计稿的截图对比，检查模块比例、CTA、装饰元素、信息层级、留白节奏和视觉重心是否差距过大；涉及装饰元素时必须单独比对装饰元素一致性。

## Image Gen 参考素材规则

仅 `add_module` 和 `add_page` 使用 image gen 参考素材。

生成方案图前必须：

- 读取 `references/style-library/index.md`。
- 从 `/Users/tianzhongyi/Documents/figma-design-executor/assets/style-library` 查找参考图本体。
- 按业务主题、目标模块、改动意图、可迁移视觉模式、色彩方向和模块密度筛选。
- 选择 1-3 张最相关参考图。
- 将所选参考图的绝对路径写入运行记录。

参考图只用于：

- 视觉质感。
- 模块层级。
- 留白节奏。
- 色彩氛围。
- 卡片质感。
- 装饰克制度。
- 图标或插画抽象风格。

禁止照搬：

- 页面结构。
- 业务内容。
- 文案。
- 品牌元素。
- 与产品级或页面级规范冲突的组件样式。

## 设计规范边界

产品级规范可以来自 `fliggy-design-gui` 的视觉规则，但本 agent 只做 Figma 还原。

可以使用：

- 产品视觉基准。
- token、字号、色彩、间距、圆角、阴影层级。
- 平台组件语义。
- 页面类型和信息架构。
- 图片与图标语义。

禁止导入为执行规则：

- 单文件 HTML 交付。
- DOM / CSS / class / BEM 规则。
- `example.html` 整页拷贝流程。
- 浏览器预览和 HTML 审查流程。

## 自检

交付前必须运行 `CHECKLISTS.md` 中对应检查。

最低通过条件：

- 所有必需内容已出现。
- 没有占位文本和需求说明。
- 颜色、字号、字重、间距、圆角符合产品级和页面级规范。
- 文本没有重叠、裁切或溢出。
- frame 没有裁切内容。
- 新增模块 / 页面与已确认方案图方向一致。
- 新增模块 / 页面已与确认方案图截图并排比对，视觉差距不过大。
- 方案图中的装饰元素已按透明 PNG 素材传入 Figma，并完成装饰元素一致性比对。
- 使用了可编辑 Figma 结构。
- 位图使用合理。

检查失败时先修复，再交付；若当前轮无法修复，必须在 handoff 中说明失败项和风险。

## 交付格式

最终回复保持简洁，包含：

- 任务路由。
- 读取的关键上下文。
- 是否生成方案图和用户确认状态。
- Figma 写入内容。
- 自检结果。
- 剩余假设或风险。

如果任务暂停在用户确认点，不要声称完成，只说明当前状态和等待用户选择。
