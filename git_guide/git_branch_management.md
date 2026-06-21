# Git 分支管理标准规范

## 1 简介

本规范旨在建立一套通用、兼容、统一、可长期维护的 Git 分支管理标准。

设计目标：

- 适用于个人开发、小团队 and 企业团队；
- 不依赖具体编程语言、框架、操作系统或行业；
- 支持单版本持续迭代；
- 支持同一产品的多个长期维护版本；
- 正式发布版本采用 SemVer（语义化版本）规范；
- 保持职责清晰，降低误操作风险。

本规范只定义 Git 管理规则。

### 1.1 系统边界说明

Git 只负责：

- 源码版本
- 提交历史
- 分支结构
- Tag 标记

Git 不负责：

- 构建产物
- 缓存文件
- 运行环境
- IDE 配置文件
- 临时文件

## 2 核心设计原则

### 2.1 职责分离

| 对象 | 职责 |
| --- | --- |
| `main` | 当前工作入口（方案一）/ 唯一维护线（方案二） |
| `vN.x` | 长期维护线（方案一） |
| `feature/*` | 临时开发分支 |
| `tag` | 正式发布版本 |
| `stash` | 临时修改保存 |

### 2.2 Feature 短生命周期

- 创建用于开发
- 合并后必须删除
- 不允许长期存在
- 不作为发布基准

### 2.3 Tag 即发布原则

Tag 是唯一发布标识：

- `v1.0.0`
- `v1.0.1`
- `v1.1.0`
- `v2.0.0`

### 2.4 SemVer 版本规范

版本格式：

```text
MAJOR.MINOR.PATCH
```

含义：

| 类型 | 含义 | 示例 |
| --- | --- | --- |
| `PATCH` | Bug 修复 | `v1.0.1` |
| `MINOR` | 向后兼容功能新增 | `v1.1.0` |
| `MAJOR` | 不兼容修改 / 重大重构 | `v2.0.0` |

## 3 工作区管理与清理规范

### 3.1 工作区状态一致性原则

任何 Git 操作前后必须保证工作区状态可预期，确保切换前和切换后的状态均处于可控范围，避免构建结果被旧文件或残留的未跟踪文件污染。

### 3.2 工作区分层模型

为了规范化工作区管理，我们将工作区划分为三个层级：

- **Git 元数据层**
  - `.git/`（绝对禁止删除、修改、覆盖或移动）
- **仓库级文件层**
  - `README`、`CI` 配置文件、`docs/`、`scripts/`、`LICENSE`、`.gitignore` 等
- **运行与构建产物层**
  - `build/`、`out/`、`obj/`、`cache/` 以及 `*.bin`、`*.elf`、`*.log` 等

### 3.3 工作区清理原则与对象

**清理目标**

保证“新架构 / 新版本运行环境一致性”，防止因残留文件导致编译报错或行为异常。

**清理对象**

- 构建产物
- 缓存文件
- 自动生成文件
- 临时运行文件

**不清理对象**

- `.git`
- 仓库级配置文件（如按项目定义的本地配置文件）

### 3.4 清理模式定义

为了应对不同的环境切换需求，定义了两种清理模式：

**模式 A：完全架构隔离（强清理）**

- **通俗含义**：彻底大扫除！清除工作区中所有未追踪的文件，**连同所有被 `.gitignore` 忽略的文件（如历史编译产物、本地依赖包、缓存、IDE 本地配置文件等）也全部强力清除**，使工作区瞬间恢复到最初克隆时的纯净状态。
- **命令**：
  ```bash
  git clean -fdx
  ```
- **等价操作**：手动执行 `rm -rf`（清除除 `.git` 外的所有内容）。
- **适用场景**：`v1.x`、`v2.x`、`v3.x` 之间完全不同架构、不共享构建系统或需要彻底排查本地残留编译干扰的场景。

**模式 B：轻隔离（同架构）**

- **通俗含义**：温和清理。仅清除未追踪的临时新建文件和文件夹，**但保留被 `.gitignore` 忽略的文件（如本地环境配置文件 `.env`、已下载的依赖包等）**，避免因为切换分支而重新下载庞大的依赖包。
- **命令**：
  ```bash
  git clean -fd
  ```
- **适用场景**：相同架构分支间的快速切换，或者不需要重新安装依赖包、仅替换源码的日常开发场景。

### 3.5 `.git` 绝对保护规则

`.git` 是 Git 仓库的唯一历史源。禁止对 `.git` 目录进行删除、修改、覆盖或移动操作，否则会导致仓库完全失效（等同于删库）。

## 4 分支管理方案一：多版本并行维护

### 4.1 适用场景

- 多版本并行维护（如同时维护 `v1.x` 和 `v2.x`）
- 多平台产品、多客户版本的定制开发

### 4.2 分支结构

- 主开发入口：`main`
- 长期维护分支：`v1.x`、`v2.x`、`v3.x`
- 临时开发分支：`feature/*`
- 版本标签（Tag）：`v1.x.y`、`v2.x.y`、`v3.x.y`

### 4.3 初始化

```bash
git init
git branch -m master main
```

### 4.4 创建维护线

```bash
git switch main
git switch -c v1.x
git add .
git commit -m "feat: init v1"
git tag v1.0.0
```

### 4.5 Feature 生命周期

```bash
git switch v1.x
git switch -c feature/login
git commit -m "feat: login"
git switch v1.x
git merge --no-ff feature/login
git branch -d feature/login
git tag v1.1.0
```

### 4.6 创建新架构

```bash
git switch main
git switch -c v2.x
```

### 4.7 新架构初始化与清理规则

新架构分支必须处于“干净初始化状态”。在切换和初始化新分支后，必须执行清理：

```bash
git clean -fd
# 或
git clean -fdx
```

### 4.8 `main` 分支的职责定义

`main` 分支是：当前工作入口 + 对齐点 + 派生点。它是一个“版本入口指针”，不是具体的开发分支。

### 4.9 `main` 分支允许行为

- 允许切换版本：
  ```bash
  git switch main
  ```

- 允许创建新维护线：
  ```bash
  git switch -c v3.x
  ```

- 允许对齐版本：
  ```bash
  git branch -f main v2.2.3
  ```

### 4.10 `main` 分支禁止行为

- 禁止直接进行代码开发
- 禁止直接合并 `feature` 分支
- 禁止作为长期开发分支

### 4.11 历史版本切换与清理

切换到历史版本：

```bash
git switch --detach v2.1.0
```

切换后必须保证工作区与目标版本的一致性，按照清理原则对工作区进行清理，避免旧产物污染。

### 4.12 远程拉取与推送规范

在多人协作开发模式下，对远程分支及 `Tag` 的拉取与推送需遵循以下规范。为防止分支追踪关系混乱，操作分为“首次操作（分支关联尚未建立）”与“已有本地分支的日常操作”两种情况。

#### 4.12.1 情况 A：首次拉取或推送（尚未建立追踪关系）

1. **首次拉取远程已有的维护线（本地尚无该分支）**：
   当远程已存在某维护分支（如 `v1.x`），但本地克隆后尚未创建对应分支时：
   ```bash
   # 1. 获取远程最新的全部分支与 Tag 状态
   git fetch origin --tags

   # 2. 检出并创建本地分支（Git 会自动与同名远程分支建立 upstream 追踪关系）
   git switch v1.x
   # 如果 Git 版本较旧，请使用如下显式关联命令：
   # git checkout -b v1.x origin/v1.x
   ```

2. **首次推送本地新建的维护线（远程尚无该分支）**：
   若在本地通过 `git switch -c v1.x` 创建了新的维护线，并准备发布至远程：
   ```bash
   # 推送分支并将其与远程分支建立追踪关联（-u 或 --set-upstream）
   git push -u origin v1.x
   ```

#### 4.12.2 情况 B：已有本地分支（已建立追踪关系）的日常操作

1. **日常拉取更新**：
   开始开发新功能或切换维护线前，需将本地分支与远程同步：
   ```bash
   # 1. 更新远程索引与 Tag
   git fetch origin --tags

   # 2. 切换到对应维护线
   git switch v1.x

   # 3. 拉取最新修改（因已关联 upstream，直接 pull 即可）
   git pull
   ```

2. **日常推送已合并的更新与 Tag**：
   本地合并 `feature/*` 到维护线并打上 `Tag` 后，推送至远程：
   ```bash
   # 1. 推送维护线分支的最新的提交（已关联 upstream，直接 push）
   git push

   # 2. 推送本次发布的特定 Tag 标签
   git push origin v1.1.0
   ```

3. **对齐 main 分支指针**：
   若在本地更新了 `main` 分支的对齐指针（如 `git branch -f main v1.1.0`），需强制推送更新远程 `main` 分支：
   ```bash
   git push origin main -f
   ```

## 5 分支管理方案二：单版本持续迭代

### 5.1 适用场景

满足以下条件：

- 只有一个长期维护版本；
- 不存在多个 Major 版本并行维护的场景；
- 只有一个主开发方向。

### 5.2 分支结构

- 主开发分支：`main`
- 临时开发分支：`feature/*`

正式发布版本标签（Tag）：

- `v1.0.0`
- `v1.0.1`
- `v1.1.0`
- ……

### 5.3 初始化

如果默认分支为 `master`，修改为 `main`：

```bash
git branch -m master main
```

提交并发布初始版本：

```bash
git add .
git commit -m "feat: initial commit"
git tag v1.0.0
```

### 5.4 Feature 生命周期

创建：

```bash
git switch main
git switch -c feature/login
```

开发：

```bash
git add .
git commit -m "feat: add login"
```

合并：

```bash
git switch main
git merge --no-ff feature/login
```

删除：

```bash
git branch -d feature/login
```

发布：

```bash
git tag v1.1.0
```

### 5.5 历史版本恢复

切换到历史版本：

```bash
git switch --detach v1.0.0
```

完成历史版本的工作后，按照项目约定恢复工作区干净状态。

恢复主干继续开发：

```bash
git switch main
```

### 5.6 远程拉取与推送规范

在单版本持续迭代方案中，所有开发均合并至 `main` 分支，团队协作的拉取与推送应遵循以下规范：

#### 5.6.1 情况 A：首次拉取与推送（主要指 Feature 分支）

在单版本方案中，主要的长生命周期分支只有 `main`。日常的“首次操作”主要涉及临时开发分支 `feature/*` 的共享与推送：

1. **首次拉取他人创建的远程 Feature 分支**：
   当协作同事已将 Feature 分支推送到远程，你需要切换到本地进行协作或 review 时：
   ```bash
   # 1. 获取远程最新的分支索引
   git fetch origin

   # 2. 检出并自动关联追踪
   git switch feature/login
   ```

2. **首次推送本地新建的 Feature 分支（用于代码审查 PR/MR）**：
   若本地新建的开发分支需要首次发布至远程进行协作或 Code Review：
   ```bash
   # 推送本地 Feature 分支并建立追踪关联
   git push -u origin feature/login
   ```

#### 5.6.2 情况 B：日常拉取与推送（基于已关联分支）

对于已建立追踪关联的 `main` 分支，日常操作规范如下：

1. **开始开发前拉取最新主干**：
   在基于 `main` 切出新功能分支前，或者开始工作前，确保本地 `main` 是最新的：
   ```bash
   # 1. 更新远程索引及 Tag
   git fetch origin --tags

   # 2. 切换至主干分支
   git switch main

   # 3. 拉取最新修改（已关联 upstream，直接 pull 即可）
   git pull
   ```

2. **日常推送合并后的主干与发布 Tag**：
   本地完成 `feature/*` 合并至 `main` 并标记发布 Tag 后：
   ```bash
   # 1. 推送主干分支最新提交（已关联 upstream，直接 push）
   git push

   # 2. 推送新版本 Tag
   git push origin v1.1.0
   ```

## 6 分支选择决策树

```text
项目是否存在多个长期维护版本？
│
├─ 否
│
│   main
│   └─ feature/*
│
│   Tag：
│   v1.0.0
│   v1.0.1
│   v1.1.0
│   ...
│
└─ 是
    │
    ├─ v1.x
    ├─ v2.x
    ├─ v3.x
    ├─ main（当前工作入口）
    └─ feature/*
    
    Tag：
    v1.x.y
    v2.x.y
    v3.x.y
    ...
```

## 7 Git 常用命令速查表

### 7.1 初始化与仓库配置

```bash
# 初始化本地仓库
git init

# 克隆远程仓库
git clone <仓库地址>

# 重命名当前分支为 main
git branch -m master main

# 管理远程仓库地址关联
git remote add origin <远程仓库地址>
git remote -v
```

### 7.2 状态、改动与历史查看

```bash
# 查看工作区与暂存区状态
git status

# 查看工作区与暂存区/本地仓库的改动细节
git diff

# 查看本地分支列表
git branch

# 以图形化单行显示所有分支的提交历史
git log --graph --oneline --decorate --all
```

### 7.3 分支开发、合并与删除

```bash
# 切换到已有分支
git switch 分支名

# 创建并切换到新分支
git switch -c 新分支

# 强制移动已有分支指针（如将 main 强制指向某个 tag 提交点）
git branch -f 分支名 目标提交或标签名

# 暂存工作区所有修改
git add .

# 提交已暂存的改动到本地仓库
git commit -m "说明"

# 非快进合并分支（显式生成合并节点以保留 Feature 历史）
git merge --no-ff 分支名

# 删除本地分支（已合并）
git branch -d 分支名

# 强制删除本地分支（未合并）
git branch -D 分支名
```

### 7.4 远程同步操作

```bash
# 从远程仓库拉取最新的分支与 Tag 变动（不自动合并）
git fetch

# 拉取远程分支并与本地当前分支合并（若已关联上游，可直接用 git pull）
git pull

# 首次推送本地新建分支至远程，并建立关联
git push -u origin 分支名

# 推送本地提交到已关联的远程分支
git push

# 推送指定 Tag 标签到远程
git push origin 标签名

# 将本地所有 Tag 一起推送到远程仓库
git push origin --tags
```

### 7.5 Tag 标签管理

```bash
# 查看本地 Tag 列表
git tag

# 在当前提交上创建 Tag
git tag v1.0.0

# 查看指定 Tag 的详细提交信息
git show v1.0.0

# 切换到历史 Tag（进入 Detached HEAD 状态）
git switch --detach v1.0.0
```

### 7.6 工作区清理、暂存与撤销

```bash
# 轻隔离清理：清除未追踪的新建文件，但保留被忽略的文件（如依赖包和环境配置）
git clean -fd

# 强隔离清理：彻底清除未追踪文件，包括所有被忽略的编译产物、本地依赖和配置等
git clean -fdx

# 将当前未提交改动（含工作区与暂存区）打包存入暂存栈
git stash

# 恢复最新暂存的改动并移出暂存栈
git stash pop

# 撤销工作区某个文件的改动（恢复至最后一次提交状态）
git restore <文件路径>
```

## 8 总结与执行口令

### 8.1 方案对比总结

- **方案一（多版本并行维护）**：多架构长期维护。`vN.x` 作为独立维护线，`main` 作为入口与派生点，`feature/*` 用于临时开发，`tag` 用于发布，工作区必须按架构进行清理隔离。
- **方案二（单版本持续迭代）**：单架构持续迭代。`main` 为唯一维护线，`feature/*` 用于临时开发，`tag` 用于发布，工作区仅做状态一致性维护。

### 8.2 最终执行口令

`Feature` 用于开发，`main` 用于入口，`vN.x` 用于维护，`Tag` 用于发布；切换必须确认状态，构建必须清理污染，`.git` 永远不可动。
