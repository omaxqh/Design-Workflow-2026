# figma design Agent 页面级设计规范

## figma还原要求
- 优先使用自动布局
- 画板不要裁切内容

## Page02·列表页

Figma 原页面：
https://www.figma.com/design/2CXckWUEm1b8tpa3v7SG1M/%E9%85%92%E5%BA%97-AI-Design-Context?node-id=158-10886&t=VVTWEd3djXVrJwCO-11

### 新增模块

#### 模块圆角
- 模块整体圆角：24px（圆角 / Radius M）
- 模块内卡片圆角：12px（圆角 / Radius S）—— 注意此处指卡片，非标签
- 标签圆角：4px

#### 模块底色
根据新增模块所属类型，选用对应颜色：

| 类型 | 纯色 | 渐变 |
|------|------|------|
| 正常 | `#F2F3F5` | — |
| 会员 | `#FEF5EC` | `linear-gradient(92deg, #FEF5EC 0%, #FDEDDB 100%)` |
| 大促 | `#FFF0F0` | `linear-gradient(92deg, #FFECEC 0%, color_warning_5 100%)` |
| AI 相关 | `#F5F5FF` | — |

#### 内边距
均24px

#### 模块内元素间距
- 内容相对独立：24px
- 基础间距：18px 或 12px
- 同类型元素重复：6px

#### 文字规范
- 模块标题字号：30px（文字颜色 / 深色字 `color_darkgray`）
- 正文：28px（文字颜色 / 深色字 `color_darkgray`）
- 辅助文字：24px（文字颜色 / 深色字 `color_darkgray`）

#### 字体颜色
- 标题：`#0F131A`
- 文本：`#5C5F66`
- 辅助：`#919499`

#### 模块描边
- 模块级卡片不使用描边

#### 阴影
- 不使用阴影

--- 

## Page03·详情页

Figma 原页面：
https://www.figma.com/design/2CXckWUEm1b8tpa3v7SG1M/%E9%85%92%E5%BA%97-AI-Design-Context?node-id=226-19077&t=Uh3gZH2TauFdhzzi-11

### 新增模块

#### 模块尺寸
- 模块宽度：750px
- 模块高度：自适应内容高度

#### 模块圆角
- 模块整体圆角：24px（圆角 / Radius M）
- 模块内卡片圆角：12px（圆角 / Radius S）—— 注意此处指卡片，非标签
- 标签圆角：4px

#### 模块底色
**固定使用** #FFFFFF
模块底色禁止使用其他颜色

#### 模块内边距
- 均24px

#### 模块外边距
- 左右外边距：0px
- 上下外边距：18px

#### 模块内元素间距
- 内容相对独立：24px
- 基础间距：18px 或 12px
- 同类型元素重复：6px

#### 文字规范
- 文字样式使用 `references/design-system/agent-tokens.json`
- 模块标题字号：30px（文字颜色 / 深色字 `color_darkgray`）
- 正文：28px（文字颜色 / 深色字 `color_darkgray`）
- 辅助文字：24px（文字颜色 / 深色字 `color_darkgray`）

#### 字体颜色
- 标题：`#0F131A`
- 文本：`#5C5F66`
- 辅助：`#919499`

#### 模块描边
- 模块级卡片不使用描边

#### 阴影
- 不使用阴影

--- 

## Page04·下单页

Figma 原页面：
https://www.figma.com/design/2CXckWUEm1b8tpa3v7SG1M/%E9%85%92%E5%BA%97-AI-Design-Context?node-id=230-4336&t=Uh3gZH2TauFdhzzi-11

### 新增模块

#### 模块尺寸
- 模块宽度：714px
- 模块高度：自适应内容高度

#### 模块圆角
- 模块整体圆角：12px（圆角 / Radius S）—— 注意此处指模块，非标签
- 标签圆角：4px

#### 模块底色
**固定使用** #FFFFFF
模块底色禁止使用其他颜色

#### 模块内边距
- 均24px

#### 模块外边距
- 左右外边距：18px
- 上下外边距：18px

#### 模块内元素间距
- 内容相对独立：24px
- 基础间距：18px 或 12px
- 同类型元素重复：6px

#### 文字规范
- 文字样式使用 `references/design-system/agent-tokens.json`
- 模块标题字号：30px（文字颜色 / 深色字 `color_darkgray`）
- 正文：28px（文字颜色 / 深色字 `color_darkgray`）
- 辅助文字：24px（文字颜色 / 深色字 `color_darkgray`）

#### 字体颜色
- 标题：`#0F131A`
- 文本：`#5C5F66`
- 辅助：`#919499`

#### 模块描边
- 模块级卡片不使用描边

#### 阴影
- 不使用阴影

#### 模块固定布局
- *标题文字位于模块左上角，标题距模块左/上 均24px*
