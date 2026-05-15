# 状态与资源

## 任务记录 Schema

每个页面级子任务记录一条。

运行记录放在 `runs/` 目录，使用 [runs/template.json](runs/template.json) 作为模板。文件命名格式：

```text
runs/YYYYMMDD-HHMM-short-slug.json
```

```json
{
  "taskId": "task-001",
  "requestSummary": "",
  "routeType": "modify_existing_page | add_module | add_page",
  "target": {
    "figmaFile": "",
    "page": "",
    "module": "",
    "element": ""
  },
  "contextRead": {
    "productRules": [],
    "pageRules": [],
    "productSpecConstraints": [],
    "pageSpecConstraints": [],
    "figmaNodes": [],
    "components": [],
    "variables": [],
    "referenceAssets": []
  },
  "proposalImage": {
    "required": false,
    "prompt": "",
    "referenceImages": [],
    "imagePathOrUrl": "",
    "outputType": "bitmap_png | bitmap_jpeg | bitmap_webp | not_generated",
    "generationMethod": "image_gen",
    "userConfirmation": "not_required | pending | confirmed | regenerate | revise",
    "visualAnchors": [],
    "decorativeAssetCandidates": [],
    "specConflicts": []
  },
  "decorativeAssets": [
    {
      "name": "",
      "source": "",
      "referenceCrop": "",
      "greenSource": "",
      "alphaPng": "",
      "outputPng": "",
      "redBackgroundCheck": "pass | fail | not_checked",
      "greenBackgroundCheck": "pass | fail | not_checked",
      "blackBackgroundCheck": "pass | fail | not_checked",
      "lightBackgroundCheck": "pass | fail | not_checked",
      "darkBackgroundCheck": "pass | fail | not_checked",
      "moduleBackgroundCheck": "pass | fail | not_checked",
      "figmaNodeId": "",
      "figmaScreenshot": ""
    }
  ],
  "figmaWrite": {
    "targetNode": "",
    "summary": "",
    "usedUseFigma": true
  },
  "selfCheck": {
    "content": "pass | fail",
    "designSystem": "pass | fail",
    "layout": "pass | fail",
    "visualMatch": "pass | fail | not_required",
    "assets": "pass | fail | not_required",
    "specConstraintsApplied": "pass | fail | not_required",
    "pageSpecMechanicalCheck": "pass | fail | not_required",
    "visualAnchorsCompared": "pass | fail | not_required",
    "specConstraintEvidence": [
      {
        "category": "fontSize | color | spacing | padding | margin | radius | stroke | shadow | size | fixedLayout",
        "nodeId": "",
        "specValue": "",
        "actualValue": "",
        "verificationMethod": "figma_node_property | variable_binding | script_measurement | screenshot_measurement",
        "result": "pass | fail",
        "fixAction": ""
      }
    ]
  },
  "handoffNotes": ""
}
```

## 资源读取约定

只读取当前路由真正需要的资源。

### 产品级设计规范

默认文件：

- [design-rules.md](design-rules.md)
- [references/design-system/agent-tokens.json](references/design-system/agent-tokens.json)
- [references/design-system/agent-component-specs.json](references/design-system/agent-component-specs.json)

使用场景：

- 新增页面。
- 新增完整流程。
- 跨页面组件或全局导航。
- 影响品牌级模式的设计调整。

预期内容：

- 颜色变量。
- 字体样式。
- 栅格和断点规则。
- 通用组件。
- 全局间距和圆角规则。
- 品牌语气和视觉风格规则。

边界：

- 可以参考 `fliggy-design-gui` 的产品视觉规范、组件语义、页面类型和 token。
- 不导入 HTML 还原规则：单文件 HTML、DOM/CSS、`example.html` 拷贝、浏览器预览和 HTML 审查流程都不属于本 agent 的产品级规范。
- 落地目标始终是 Figma 可编辑图层、组件、变量和自动布局。

### 页面级设计规范

默认文件：

- [figma design agent 页面级设计规范.md](figma%20design%20agent%20%E9%A1%B5%E9%9D%A2%E7%BA%A7%E8%AE%BE%E8%AE%A1%E8%A7%84%E8%8C%83.md)

使用场景：

- 在已有页面中新增模块。
- 局部布局调整。
- 匹配相邻模块节奏。

预期内容：

- 目标页面章节，例如 `Page02·列表页`、`Page03·详情页` 或 `Page04·下单页`。
- 必须提取为 `pageSpecConstraints` 的硬性数值：模块尺寸、圆角、底色或渐变、内边距、外边距、元素间距、字号、文字颜色、描边、阴影和固定布局规则。
- 页面模块顺序和信息层级。
- 局部栅格行为。
- 当前页面组件使用方式。
- 页面专属图片和图标风格。

使用要求：

- 已有页面新增模块和局部布局匹配时，页面级规范是最终自检的硬规则源。
- 校验时优先读取 Figma 节点属性、变量绑定或脚本量测结果；截图目测只用于辅助判断布局、裁切、重叠和整体视觉差距。
- 每个硬性数值都必须在 `selfCheck.specConstraintEvidence` 中记录规范值、实际值、目标节点、验证方式和结论。

### Figma 上下文

所有路由都需要读取。

预期内容：

- 节点结构。
- 截图或设计上下文。
- 变量和样式。
- 相关组件。
- 现有自动布局约束。

### Image Gen 参考素材

仅在新增模块或新增页面，且视觉方向需要参考时使用。

方案图生成产物要求：

- 必须通过 image gen 指令直接生成 PNG、JPEG 或 WebP 等位图。
- 禁止使用 SVG、HTML/CSS、Canvas、Figma shape 脚本、手写矢量或任何代码绘图方式生成方案图。
- 禁止把 SVG 或代码绘图产物导出/截图为 PNG 后记录为方案图。
- 运行记录必须写入 `proposalImage.generationMethod: "image_gen"` 和 `proposalImage.outputType`；非位图或来源不明时，`generate_design_image` 视为失败。

默认索引：

- [references/style-library/index.md](references/style-library/index.md)

参考图查找路径：

- `/Users/tianzhongyi/Documents/figma-design-executor/assets/style-library`

预期内容：

- 1 到 3 张相关风格参考图。
- 素材使用约束。
- 可借鉴内容：构图、光感、密度、节奏或视觉母题。
- 不可借鉴内容：无关信息、非品牌色、不一致的 UI 模式。

选择规则：

- 先提取业务主题、目标模块、改动意图和可迁移视觉模式。
- 跨目录查找候选参考图，不按目标页面目录做硬限制。
- 参考图只校准视觉质感、模块层级、留白节奏、色彩氛围、卡片质感和装饰克制度。
- 不得照搬参考图中的页面结构、文案、业务内容或品牌元素。

## 歧义处理规则

以下情况需要先问用户：

- 多个页面、模块或元素拥有同一个目标名称。
- 需求没有说明是修改已有页面还是新增内容，且 Figma 上下文无法安全判断。
- 缺少必要 Figma 文件信息。
- 方案图生成后，需要用户确认。

让用户选择时使用简洁编号列表。

## 失败处理规则

以下情况停止并报告：

- 无法读取 Figma 上下文。
- 必需设计规范不可用，且无法安全推断。
- 新增模块/页面的 image gen 失败。
- 用户尚未确认方案图。
- `use_figma` 写入失败。
- 自检发现裁切、重叠、内容缺失或设计规范违规，且当前轮无法修复。
