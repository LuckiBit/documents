# Git 提交信息指南

## 1 提交信息格式

每个提交信息都由 **页眉（Header）**、**正文（Body）** 和 **页脚（Footer）** 组成。页眉具有特殊的格式，包含 **类型（Type）**、**范围（Scope）** 和 **主题（Subject）**：

```text
<type>(<scope>): <subject>
<空行>
<body>
<空行>
<footer>
```

**页眉（Header）** 是必填的，页眉中的 **范围（Scope）** 是可选的。

提交信息的任何一行长度都不能超过 100 个字符！这使得消息在 GitHub 以及各种 Git 工具中更易于阅读。

页脚应该包含对 [关闭 Issue](https://help.github.com/articles/closing-issues-via-commit-messages/) 的引用（如果有的话）。

示例：（更多 [示例](https://github.com/angular/angular/commits/master)）

```text
docs(changelog): update changelog to beta.5
```

```text
fix(release): need to depend on latest rxjs and zone.js

The version in our package.json gets copied to the one we publish, and users need the latest of these.
```

## 2 撤销（Revert）

如果提交用于撤销之前的提交，应以 `revert: ` 开头，后跟被撤销提交的页眉。在正文中应当写道：`This reverts commit <hash>.`，其中 `hash` 是被撤销提交的 SHA 校验和。

## 3 类型（Type）

必须是以下之一：

- **`build`**: 影响构建系统或外部依赖项的更改（示例范围：`Gulp`、`Broccoli`、`npm`）
- **`ci`**: 对 `CI` 配置文件和脚本的更改（示例范围：`Travis`、`CircleCI`、`BrowserStack`、`SauceLabs`）
- **`docs`**: 仅文档更改
- **`feat`**: 新功能
- **`fix`**: 缺陷修复
- **`perf`**: 提高性能的代码更改
- **`refactor`**: 既不修复缺陷也不添加功能的代码更改
- **`style`**: 不影响代码含义的更改（空白字符、格式化、缺失分号等）
- **`test`**: 添加缺失的测试或修改现有的测试

### 3.1 扩展类型

为符合 `@commitlint/config-conventional` 的通用约定，增加以下扩展类型：

- **`chore`**: 日常事务、脚手架或辅助工具的变动，不属于以上其他类型的修改（例如修改 `.gitignore`、配置依赖等）
- **`revert`**: 撤销之前的提交（详情请参阅 2 撤销（Revert） 章节）

## 4 范围（Scope）

范围应该是受影响的 `npm` 包名（由阅读从提交信息生成的变更日志的人来感知）。

以下是支持的范围列表：

- **`animations`**
- **`common`**
- **`compiler`**
- **`compiler-cli`**
- **`core`**
- **`elements`**
- **`forms`**
- **`http`**
- **`language-service`**
- **`platform-browser`**
- **`platform-browser-dynamic`**
- **`platform-server`**
- **`platform-webworker`**
- **`platform-webworker-dynamic`**
- **`router`**
- **`service-worker`**
- **`upgrade`**

目前有少数几个不遵守“使用包名”规则的例外：

- **`packaging`**: 用于更改所有包中 `npm` 包布局的修改，例如公共路径更改、对所有包进行的 `package.json` 更改、`d.ts` 文件/格式更改、构建包（Bundles）更改等。
- **`changelog`**: 用于更新 `CHANGELOG.md` 中的发布说明
- **`aio`**: 用于 `docs-app`（`angular.io`）相关的更改，位于仓库的 `/aio` 目录中
- 无/空字符串：适用于在所有包中进行的 `style`、`test` 和 `refactor` 更改（例如：`style: add missing semicolons`）

## 5 主题（Subject）

主题包含对更改的简要描述：

- 使用祈使句、一般现在时：用 `change`，不用 `changed` 或 `changes`
- 首字母不要大写
- 句尾不加句号（.）

## 6 正文（Body）

与 **主题** 一样，使用祈使句、一般现在时：用 `change`，不用 `changed` 或 `changes`。

正文应包含修改的动机，并与之前的行为进行对比。

## 7 页脚（Footer）

页脚应包含有关 **破坏性变更（Breaking Changes）** 的任何信息，也是引用该提交 **关闭（Closes）** 的 GitHub Issue 的地方。

**破坏性变更**应以 `BREAKING CHANGE:` 开头，后跟一个空格或两个换行符。随后是具体的变更说明。

详细的解释可以在此 [文档][commit-message-format] 中找到。
