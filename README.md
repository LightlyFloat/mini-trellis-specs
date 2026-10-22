# mini-trellis-specs

个人/团队的 mini-trellis spec 模板注册表（registry）。项目引入 mini-trellis 时，由 init 自动把模板写入 `.trellis/spec/`，作为默认的长期项目规则，避免每个项目重复编写。

本仓库由 [trellis-specs](https://github.com/LightlyFloat/trellis-specs) 迁移而来，`index.json` 格式与之相同。依赖 Trellis 工作流（`task.py`、阶段门、每轮注入、`trellis-implement` / `trellis-check` 子代理）的条目已改写为 mini-trellis 语义。

## 结构

```
mini-trellis-specs/
└── specs/                          ← registry 根（index.json 所在）
    ├── index.json                  ← 模板索引
    └── default-0621/               ← 模板：默认规范模板 (0621)
        └── guides/
            ├── index.md            ← 覆盖 mini-trellis 内置索引，并链接内置的 mini-memory.md
            ├── document-writing-style.md
            ├── project-doc-organization.md
            ├── pre-dev-checklist.md
            ├── search-tool-init.md
            ├── vision-mcp-usage.md
            ├── image-lifecycle.md
            ├── subagent-boundary.md
            ├── plan-execute-handoff.md
            ├── windows-text-io.md
            ├── git-scripting-on-windows.md
            └── external-structure-evidence.md
```

## 前提

上游 mini-trellis 0.3.0 的 init 不支持 registry。registry 功能位于 fork [LightlyFloat/mini-trellis](https://github.com/LightlyFloat/mini-trellis) 的 `feat/spec-registry` 分支，需从本地构建后全局安装：

```bash
git clone -b feat/spec-registry https://github.com/LightlyFloat/mini-trellis.git
cd mini-trellis
npx pnpm@10.32.1 install --frozen-lockfile
npx pnpm@10.32.1 build
cd packages/cli
npx pnpm@10.32.1 pack
npm install -g ./mini-trellis-0.3.0.tgz
```

`mini-trellis upgrade` 会安装 npm 上的上游版本并替换 fork，升级 fork 时应重复上述步骤。

## 使用方法

在用户配置 `~/.mini-trellis/config.json`（Windows 为 `%USERPROFILE%\.mini-trellis\config.json`）中登记默认模板：

```json
{
  "spec": {
    "registry": "gh:LightlyFloat/mini-trellis-specs/specs",
    "template": "default-0621"
  }
}
```

之后在任意新项目中执行：

```bash
mini-trellis init -u LightlyFloat --claude --codex
```

init 先拉取模板，再写入 `.trellis/` 骨架。拉取失败时 init 中止，项目不被改动。模板中的 `guides/index.md` 取代 mini-trellis 内置索引，内置的 `guides/mini-memory.md` 照常写入。

临时指定其他模板或跳过默认模板：

```bash
mini-trellis init -u <name> --claude --registry gh:<user>/<repo>/specs --template <id>
mini-trellis init -u <name> --claude --no-registry
```

已初始化的项目再次执行 init 时不拉取模板。需要补充缺失的规范文件时加 `-s`（已有文件跳过），需要覆盖时加 `-f`。从 Trellis 迁移（`mini-trellis migrate`）的项目保留原有 spec，迁移后 `guides/index.md` 不会自动增加 mini-memory.md 的条目，需手工补上。

## 现有模板

| id | 名称 | 说明 |
|----|------|------|
| `default-0621` | 默认规范模板 (0621，mini-trellis 版) | 中文文档文风 + 文档组织 + Pre-Dev Checklist 模式 + 搜索工具初始化 + 子代理边界 + 规划与执行分离等 11 条指南 |

## 新增模板

1. 在 `specs/` 下新建模板目录，例如 `specs/<template-id>/`，按 `.trellis/spec/` 的目录结构放置规范文件。
2. 若模板包含 `guides/index.md`，其中应链接 `./mini-memory.md`，否则 init 会给出警告，内置记忆层指南也无法从索引进入。
3. 在 `specs/index.json` 的 `templates` 数组中追加一条记录，`path` 指向该模板目录（仓库根相对路径），`type` 固定为 `spec`。
4. 提交并推送。

## index.json 字段说明

| 字段 | 必填 | 说明 |
|------|------|------|
| `id` | 是 | 模板标识，对应 `--template <id>` 或配置中的 `spec.template` |
| `type` | 是 | 固定为 `spec` |
| `name` | 是 | 模板显示名称 |
| `description` | 否 | 模板说明 |
| `path` | 是 | 模板目录的仓库根相对路径 |
| `tags` | 否 | 标签数组 |

registry 中只有一个 `spec` 模板时，可以省略 `template`。
