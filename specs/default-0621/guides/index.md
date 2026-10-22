# Thinking Guides

> 本目录收录跨模块的通用规范与思考指南。撰写代码或文档前先查阅相关条目。

---

## Available Guides

| Guide | Purpose | When to Use |
|-------|---------|-------------|
| [Mini-Trellis Memory Layer](./mini-memory.md) | Journal、research、spec 索引与 SessionStart 的分工，remember 的记录方式 | **会话开始、compact 之后、执行 remember 或把 research 结论提升为 spec 时** |
| [文档撰写规范](./document-writing-style.md) | 交付文档的文风标准（正式、严谨、流畅，不口语化） | **撰写或修改任何面向交付的文档时（部署说明/使用手册/开发说明/README 等）** |
| [项目文档组织约定](./project-doc-organization.md) | document/ 目录与文档索引的组织方式 | **新建项目文档、整理文档结构、需要文档检索入口时** |
| [Vision MCP 调用规范](./vision-mcp-usage.md) | 图片输入自动调用 vision MCP，按意图决定是否注入工程上下文 | **用户输入包含图片时** |
| [图片生命周期管理规范](./image-lifecycle.md) | 会话内历史图片自动降级，防止 transcript 膨胀与 413 错误 | **会话内累积了多张截图或设计稿时** |
| [Pre-Dev Checklist 编写规范](./pre-dev-checklist.md) | 为 spec 目录编写 Pre-Development + Quality Check 闭环 | **新建 spec/\<package\>/index.md 时，需要编写 checklist 模板时** |
| [搜索工具初始化规范](./search-tool-init.md) | 禁用内置搜索、启用 MCP 高质量搜索 | **新建项目时，确保搜索质量不降级** |
| [子代理使用边界规范](./subagent-boundary.md) | 默认不派发实施类子代理，只保留 review 类 | **准备派发子代理时，或实施阶段开始前** |
| [规划与执行分离规范](./plan-execute-handoff.md) | 规划与执行拆到两个会话，含规划产物位置、交接话术与命名约定 | **规划产物写入并经用户确认后** |
| [Windows 文本 IO 编码规范](./windows-text-io.md) | 文本读写与控制台输出显式声明 UTF-8 | **在 Windows 上生成涉及文本读写或控制台输出的代码时** |
| [Windows 中文路径下的 Git 脚本规范](./git-scripting-on-windows.md) | 路径转义、守卫脚本自证、内容边界校验 | **在含中文路径的仓库中编写 git 自动化或提交前守卫脚本时** |
| [外部结构结论取证](./external-structure-evidence.md) | 页面结构、内嵌字段、接口端点的结论必须有证据支撑 | **涉及外部站点页面结构或内嵌数据的代码与文档** |

---

## When to Think About Memory Layer（记忆层）

- [ ] 会话刚开始，或刚经历 compact
- [ ] 准备执行 remember，记录本次会话的结论
- [ ] research 中的某条结论已确认为长期约束

→ 阅读并遵循 [Mini-Trellis Memory Layer](./mini-memory.md)。核心：启动时只给短入口，
规范与研究按需用 `mini-trellis mem recall` 检索；已确认的结论写入对应 spec 或 research，
journal 只记过程并链接到笔记。

## When to Think About Document Writing Style（撰写文档文风）

- [ ] 正在撰写或修改面向交付的文档（部署说明、使用手册、开发说明、README 等）
- [ ] 文档将交付给企事业单位用户或客户
- [ ] 你写下了口语化、比喻性、调侃性或带括号补充说明的句子

→ 阅读并遵循 [文档撰写规范](./document-writing-style.md)。核心：正式严谨、行文流畅、
不口语化、不用解释性括号、保留全部技术信息。

## When to Think About Project Doc Organization（项目文档组织）

- [ ] 正在新建一份不属于某个具体模块的文档（如学习笔记、通用参考）
- [ ] 需要为项目建立或更新文档检索入口
- [ ] 文档散落各处、难以查找

→ 阅读并遵循 [项目文档组织约定](./project-doc-organization.md)。核心：根目录设 `document/`
存放无固定归属的文档，并维护 `document/文档索引.md` 作为全项目检索入口。

## When to Think About Vision MCP（图片输入处理）

- [ ] 用户输入中包含图片（截图、照片、设计稿等）
- [ ] 需要理解界面内容或提取文字

→ 阅读并遵循 [Vision MCP 调用规范](./vision-mcp-usage.md)。核心：自动调用 vision MCP，
按用户意图决定是否在 prompt 中注入工程上下文。

## When to Think About Pre-Dev Checklist（编写开发检查清单）

- [ ] 正在为项目新建 `.trellis/spec/<package>/index.md`
- [ ] 现有 checklist 条目过多或过少，需要优化
- [ ] 希望 Agent 在编码前按 index.md 读取规范约束

→ 阅读并遵循 [Pre-Dev Checklist 编写规范](./pre-dev-checklist.md)。核心：Pre-Dev 3-6 条链接规范，
Quality Check 5-10 条验证条件，条目必须来源于真实 bug。

## When to Think About Search Tool Init（搜索工具初始化）

- [ ] 刚创建一个新项目，或刚执行完 `mini-trellis init`
- [ ] 发现模型使用了内置 WebSearch 而非 MCP 搜索
- [ ] 需要确认搜索工具配置状态

→ 阅读并遵循 [搜索工具初始化规范](./search-tool-init.md)。核心：init 后在 `.claude/settings.json`
中通过 `permissions.deny` 禁用内置搜索，确保使用高质量 MCP 搜索。

## When to Think About Subagent Boundary（子代理使用边界）

- [ ] 正准备派发子代理完成实施或调研工作
- [ ] 宿主的默认行为倾向于把多步骤任务交给子代理
- [ ] 需要确认哪类子代理仍然允许派发

→ 阅读并遵循 [子代理使用边界规范](./subagent-boundary.md)。核心：默认不派发实施类子代理，
实施由主会话内联完成，唯一例外是 review / critic 类子代理。

## When to Think About Plan-Execute Handoff（规划与执行分离）

- [ ] `design.md` 与 `implement.md` 已写入 `.trellis/research/<MM-DD>-<slug>/` 并经用户确认
- [ ] 正准备在规划完成的同一会话内直接进入实施
- [ ] 需要给出执行会话的名称、接续话术或模型切换建议

→ 阅读并遵循 [规划与执行分离规范](./plan-execute-handoff.md)。核心：规划产物经确认后
规划会话即停止，输出交接五项，执行在清空会话后的新会话中进行。

## When to Think About Windows Text IO（Windows 文本编码）

- [ ] 正在 Windows 上生成涉及文本文件读写的代码
- [ ] 生成的程序需要向控制台输出中文
- [ ] 需要用 `subprocess` 或管道捕获外部程序的中文输出

→ 阅读并遵循 [Windows 文本 IO 编码规范](./windows-text-io.md)。核心：显式声明 UTF-8，
不依赖系统默认代码页 cp936。

## When to Think About Git Scripting（Git 脚本与提交守卫）

- [ ] 正在编写解析 git 输出的脚本，而仓库中存在中文文件名或目录名
- [ ] 正在编写提交前的内容边界守卫，例如同一仓库向多个远端推送不同内容
- [ ] 守卫脚本报警但人工核对后发现内容正确

→ 阅读并遵循 [Windows 中文路径下的 Git 脚本规范](./git-scripting-on-windows.md)。
核心：解析路径一律加 `-c core.quotepath=false`，守卫先打印实际清单再判断，
需入库的目录不以 `.` 开头。

## When to Think About External Structure Evidence（外部结构取证）

- [ ] 正在编写或修改站点适配代码，涉及 DOM 选择器、URL 形态、内嵌状态字段
- [ ] 正在继承上一轮会话给出的页面结构结论
- [ ] 准备在代码注释或交付文档中陈述外部站点的页面结构
- [ ] 准备声称抓取缺陷已修复

→ 阅读并遵循 [外部结构结论取证](./external-structure-evidence.md)。核心：结构结论必须标注
证据来源，拦截页不构成证据，本机不可直连时只声称类型检查与构建通过。
