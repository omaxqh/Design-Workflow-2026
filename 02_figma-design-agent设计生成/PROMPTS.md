# 提示词模板

## 方案图生成提示词

用于 `add_module` 和 `add_page`。

```text
请生成一张产品 UI 设计方案图。

生成方式：
- 必须使用 image gen 指令生成 PNG/JPEG/WebP 等位图方案图。
- 禁止使用 SVG、HTML/CSS、Canvas、Figma shape 脚本或任何矢量/代码绘图方式生成方案图。
- 禁止把 SVG 再转成 PNG 后冒充 image gen 方案图；方案图源头必须是位图 image generation。

需求：
{request_summary}

路由：
{add_module_or_add_page}

目标上下文：
{target_page_or_product_context}

需要遵循的设计规范：
{design_rules}

参考图：
{reference_images}

参考图使用方式：
- 只参考视觉质感、模块层级、留白节奏、色彩氛围、卡片质感和装饰克制度。
- 不照搬页面结构、业务内容、文案、品牌元素或与 Figma 设计规范冲突的组件样式。

必须包含的内容：
{required_content}

视觉方向：
{visual_direction}

约束：
- 这是方案图，不是最终 Figma 交付物。
- 方案图必须是 image gen 位图输出，不得是 SVG 或代码绘图输出。
- 使用真实产品 UI 的信息层级、布局节奏和间距。
- 文本必须可读，不要生成无意义占位文案，除非需求明确要求。
- 保持与现有产品设计系统兼容。
- 不要引入无关模块、控件或营销声明。
- 参考图只作为美学校准，不作为最终 Figma 结构。
```

## 用户确认提示词

生成方案图后使用。

```text
请确认这版设计方向：

1. 确认，继续还原到 Figma
2. 按当前方向重新生成
3. 调整方向后重新生成

请回复序号。
```

## Figma 还原 Brief

调用 `use_figma` 前使用。

```text
请将已确认的设计方向还原为可编辑 Figma 图层。

路由：
{route_type}

目标：
{figma_target}

已确认方案图：
{proposal_image_reference}

设计规范：
{product_or_page_design_rules}

实现要求：
- 使用可编辑 frame、text layer、component、variable、style 和 auto layout。
- 修改已有页面时，保留原有结构和组件习惯。
- 新增模块/页面时，匹配已确认方案图的信息层级和视觉节奏。
- 使用设计规范内的颜色、字体、间距、圆角和组件。
- 不要把整张方案图粘贴成最终设计。
- 不要把需求说明写进画布。
- 确保没有裁切、重叠或文本溢出。
```

## 装饰素材 Brief

当方案图中包含需要独立导出的装饰视觉时使用。

```text
请为已确认方案图中的装饰视觉准备独立透明 PNG 素材。

需要处理的素材：
{asset_list}

检查项：
- 背景透明。
- 浅色背景下边缘干净。
- 深色背景下边缘干净。
- 没有无关 UI 碎片。
- 没有明显色边。
- 光效、阴影和透明度保留自然。
```
