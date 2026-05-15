# Figma 设计 Workflow Agent

这个工作区定义了一个 workflow agent：接收上游产品设计需求，并把需求转成可编辑、可继续迭代的 Figma 设计稿。

当前实现是文档驱动的：

- [AGENTS.md](AGENTS.md)：真正运行入口；每次任务从这里开始，包含启动、路由、确认、Figma 写入、自检和交付协议。
- [WORKFLOW_AGENT.md](WORKFLOW_AGENT.md)：完整 agent 执行指令。
- [STATE_AND_RESOURCES.md](STATE_AND_RESOURCES.md)：任务状态、资源读取约定和交付记录结构。
- [workflow.agent.json](workflow.agent.json)：机器可读的状态与路由配置。
- [PROMPTS.md](PROMPTS.md)：生图、用户确认、Figma 还原、装饰素材处理提示词模板。
- [CHECKLISTS.md](CHECKLISTS.md)：路由检查、方案图确认、Figma 还原自检和场景测试。
- [design-rules.md](design-rules.md)：Figma 还原专用的产品级设计规范，用于新增页面、完整流程、跨页面组件和品牌级模式；来源可参考 Fliggy Design GUI，但已排除 HTML 还原规则。
- [figma design agent 页面级设计规范.md](figma%20design%20agent%20%E9%A1%B5%E9%9D%A2%E7%BA%A7%E8%AE%BE%E8%AE%A1%E8%A7%84%E8%8C%83.md)：页面级设计规范，用于已有页面新增模块和局部布局匹配。
- [references/design-system/agent-tokens.json](references/design-system/agent-tokens.json)：Figma 设计系统 token 来源。
- [references/design-system/agent-component-specs.json](references/design-system/agent-component-specs.json)：image gen 和 Figma 还原前使用的 Figma 组件规范清单。
- [references/style-library/README.md](references/style-library/README.md)：image gen 方案图生成前使用的风格参考图库说明；参考图从 `/Users/tianzhongyi/Documents/figma-design-executor/assets/style-library` 查找。
- [runs/template.json](runs/template.json)：每次任务运行记录模板。

## 当前目录结构

当前仓库根目录的业务内容主要集中在 `02_figma-design-agent设计生成/`。这个目录内部建议按“入口文档、长期参考、过程记录、运行产物”来理解：

```text
02_figma-design-agent设计生成/
├── AGENTS.md
├── README.md
├── WORKFLOW_AGENT.md
├── STATE_AND_RESOURCES.md
├── CHECKLISTS.md
├── PROMPTS.md
├── design-rules.md
├── figma design agent 页面级设计规范.md
├── workflow.agent.json
├── memory/
│   └── YYYY-MM-DD.md
├── references/
│   ├── design-system/
│   │   ├── agent-tokens.json
│   │   └── agent-component-specs.json
│   └── style-library/
│       ├── README.md
│       └── index.md
└── runs/
    ├── template.json
    ├── YYYYMMDD-HHMM-*.json
    ├── *.png
    └── assets/
```

各目录职责如下：

- `AGENTS.md` 到 `PROMPTS.md`：agent 的主执行文档，定义入口、状态机、提示词和检查清单。
- `design-rules.md` 与 `figma design agent 页面级设计规范.md`：设计规范层，分别承载产品级规则和页面级硬约束。
- `workflow.agent.json`：机器可读配置，方便把文档规则同步给自动化流程。
- `memory/`：按日期沉淀关键变更、决策和阶段性记录，适合作为长期记忆。
- `references/design-system/`：稳定的设计系统输入，例如 token 和组件规格。
- `references/style-library/`：方案图阶段使用的风格参考索引，不直接产出最终 Figma。
- `runs/`：单次任务运行记录和过程中产生的截图、方案图、素材等短周期产物。
- `runs/assets/`：运行过程中切出来的独立素材、比对图和图标资源，属于 `runs/` 的附属产物。

## 维护建议

- 长期有效的规则、协议、模板放在根层文档或 `references/`，不要混进 `runs/`。
- 一次性任务产物统一进 `runs/`，命名继续沿用时间戳加短语义 slug。
- `memory/` 只记录结论和关键决策，不重复堆积完整运行日志。
- 如果某份文档已经删除或迁移，优先更新这里的入口清单，避免 README 与真实目录脱节。

## 核心原则

Agent 必须区分“修改已有设计”和“新增设计内容”：

- 修改已有页面或元素：跳过 image gen，直接读取目标 Figma 上下文并做定点修改。
- 新增模块或新增页面：必须先用 image gen 指令生成 PNG/JPEG/WebP 等位图设计方案图，并等待用户明确确认后再进入 Figma 还原；禁止用 SVG、HTML/CSS、Canvas 或代码绘图替代方案图。
- 最终 Figma 产物必须是可编辑、结构化、符合产品设计规范的设计稿，不能用整张截图粘贴替代。

## 预期执行流程

1. 解析需求，拆成页面级任务。
2. 将每个任务路由为 `modify_existing_page`、`add_module` 或 `add_page`。
3. 在 `runs/` 中创建或更新运行记录。
4. 按需读取 Figma 上下文和设计资源。
5. 对新增模块/页面选择 1-3 张参考图并生成方案图，随后等待确认。
6. 只在需要时准备独立装饰素材。
7. 使用 `use_figma` 写入可编辑 Figma 设计。
8. 自检通过后交付。
