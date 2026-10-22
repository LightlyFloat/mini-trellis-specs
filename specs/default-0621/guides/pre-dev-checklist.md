# Pre-Development Checklist 编写规范

> 适用范围：所有使用本模板的项目。
> 目的：为每个 package/module 的 spec 目录提供统一的"开发前读什么、开发后验什么"闭环结构。

---

## 一、设计意图

每个 package/module 的 `.trellis/spec/<package>/index.md` 应包含两个清单：

1. **Pre-Development Checklist** — 开发者开始写代码前必须读的规范列表
2. **Quality Check** — 代码完成后的自检清单

两者配合形成"输入把关 → 输出验证"的闭环。AI 助手在收到编码任务时先读对应的 index.md，确保每次修改都经过规范约束。

---

## 二、模板结构

```markdown
# <Package> 开发规范

> 适用范围：`<path>` 下所有代码文件。

---

## Pre-Development Checklist

开始任何开发任务前，逐项确认：

- [ ] 读取 [目录结构规范](./directory-structure.md) — 确认新文件的放置位置
- [ ] 读取 [<核心规范1>](./<file>.md) — <一句话说明关注什么>
- [ ] 读取 [<核心规范2>](./<file>.md) — <一句话说明关注什么>
- [ ] 读取 [代码质量规范](./quality-guidelines.md) — 命名、分层、审查约束

---

## Quality Check

代码完成后，逐项验证：

- [ ] <该 package 最常犯的错误1>
- [ ] <该 package 最常犯的错误2>
- [ ] <统一的错误处理模式已遵循>
- [ ] <权限/安全约束已满足>
- [ ] <命名/放置规范已满足>
```

---

## 三、编写原则

| 原则 | 说明 |
|------|------|
| 条目必须可执行 | 每条是一个可勾选的动作，不是模糊描述 |
| Pre-Dev 只链接 spec 文件 | 不在 checklist 内重复规范内容，只提供链接 + 一句话聚焦点 |
| Quality Check 是验证条件 | 每条是 boolean 判断："是否满足 X" |
| 条目来源于真实 bug | 只收录实际出过错的检查点，不加教科书条目 |
| 保持精简 | Pre-Dev 3-6 条，Quality Check 5-10 条；太多则无人看 |

---

## 四、与 AI 工具集成

mini-trellis 不在编码前自动注入 spec 正文。SessionStart 只列出 `.trellis/spec/guides/index.md`
与各 package 的 `.trellis/spec/<package>/index.md` 路径作为入口，正文由 Agent 按需读取。
因此 index.md 是 Agent 进入某个 package 的唯一入口，两个清单必须写在 index.md 内：

- Agent 修改某个 package 前，先读该 package 的 index.md，再按 Pre-Development Checklist 读取链接的规范
- Quality Check 作为完成前自检与 review 类子代理的验证基准
- 跨 package 查找规范时使用 `mini-trellis mem recall "<关键词>"`，摘录不足时再用 `mem read` 展开
- 新成员无需口头交代规范，Agent 会按入口代为执行

新建 `.trellis/spec/<package>/` 后无需额外登记，SessionStart 会自动发现其中的 index.md。
