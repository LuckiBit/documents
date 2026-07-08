# Git 现代开发工作流（Main + Feature Branch）

## 1 准备工作（仓库初始化）

### 1.1 初始化本地仓库

```bash
git init
git branch -M main

git config --local user.name "lucki"
git config --local user.email "lucki@example.com"
```

创建初始文件：

```bash
printf "v1.0.0\n" > version.txt
```

提交初始版本：

```bash
git add version.txt
git commit -m "chore: initial project"
```

创建发布标签：

```bash
git tag v1.0.0
```

查看历史：

```bash
git log --oneline --decorate
```

输出类似：

```text
a1b2c3d (HEAD -> main, tag: v1.0.0) chore: initial project
```

### 1.2 配置与推送远程仓库

```bash
git remote add origin git@github.com:user/demo.git
```

查看远程仓库：

```bash
git remote -v
```

首次推送：

```bash
git push -u origin main
git push --tags
```

## 2 第一轮开发周期（以加法功能为例）

### 2.1 创建功能分支

从主分支创建功能分支：

```bash
git switch main
git pull

git switch -c feature/add
```

查看当前分支：

```bash
git branch
```

输出：

```text
* feature/add
  main
```

### 2.2 功能开发与本地提交

修改代码：

```bash
vim calculator.c
```

提交：

```bash
git add .
git commit -m "feature: add addition operation"
```

继续开发：

```bash
git add .
git commit -m "feature: add support for decimal addition"
```

修复问题：

```bash
git add .
git commit -m "fix: correct addition precision error"
```

查看历史：

```bash
git log --oneline
```

例如：

```text
c3c3c3c fix: correct addition precision error
b2b2b2b feature: add support for decimal addition
a2a2a2a feature: add addition operation
```

推送功能分支：

```bash
git push -u origin feature/add
```

### 2.3 合并到主分支

切回主分支：

```bash
git switch main
```

同步远程：

```bash
git pull
```

合并功能：

```bash
git merge feature/add
```

删除已完成功能分支：

```bash
git branch -d feature/add
```

删除远程功能分支：

```bash
git push origin --delete feature/add
```

### 2.4 发布新版本（打标签）

创建发布标签：

```bash
git tag v2.0.0
```

查看标签：

```bash
git tag
```

推送主分支：

```bash
git push
```

推送标签：

```bash
git push --tags
```

查看历史：

```bash
git log --oneline --decorate
```

例如：

```text
d4d4d4d (HEAD -> main, tag: v2.0.0) merge feature/add
a1b2c3d (tag: v1.0.0) chore: initial project
```

分支状态演变图解：

```text
合并前：
A (main, v1.0.0)
 └─ B (feature/add)

合并与打标签后：
A ──────── B (main, tag: v2.0.0)
```

## 3 协同与新环境开发

### 3.1 克隆与同步仓库

克隆仓库：

```bash
git clone git@github.com:user/demo.git
```

进入目录：

```bash
cd demo
```

查看分支：

```bash
git branch -a
```

同步最新代码：

```bash
git pull
```

## 4 第二轮开发周期（以减法功能为例）

### 4.1 创建新功能分支

创建新功能分支：

```bash
git switch main
git pull

git switch -c feature/subtract
```

开发：

```bash
git add .
git commit -m "feature: add subtraction operation"

git add .
git commit -m "feature: add negative number support for subtraction"
```

推送：

```bash
git push -u origin feature/subtract
```

### 4.2 冲突解决与分支合并（处理问题解决问题）

在实际协作中，当你准备将 `feature/subtract` 合并回 `main` 时，其他开发者可能已经向远程 `main` 推送了修改，这极易引发合并冲突。

#### 4.2.1 模拟冲突场景

在功能分支开发期间，另一个开发者在另外的本地仓库中直接对主分支上的 `calculator.c` 进行了加法优化并推送到了远程主机：

```c
// 另一个开发者在远程 main 分支上提交的代码改动：
double add(double a, double b) {
    return a + b;
}
```

#### 4.2.2 本地拉取最新主干并尝试合并

在你的本地仓库中，你切回 `main` 分支，拉取远程更新，然后尝试将你开发好的减法功能分支合并到主分支：

```bash
git switch main
git pull

# 尝试合并减法分支
git merge feature/subtract
```

此时终端会提示代码冲突，合并中断：

```text
Auto-merging calculator.c
CONFLICT (content): Merge conflict in calculator.c
Automatic merge failed; fix conflicts and then commit the result.
```

#### 4.2.3 定位并手动解决冲突

打开提示冲突的文件 `calculator.c`，你会看到 Git 自动标记出的冲突区域：

```c
<<<<<<< HEAD
// 这是主分支上其他开发者最新提交并已被你 pull 下来的加法逻辑优化
double add(double a, double b) {
    return a + b;
}
=======
// 这是你在功能分支 feature/subtract 上开发的减法逻辑
double subtract(double a, double b) {
    return a - b;
}
>>>>>>> feature/subtract
```

你需要手动修改此文件，保留两边的有效改动，并删掉冲突标记（`<<<<<<<`、`=======`、`>>>>>>>`）：

```c
// 手动处理保留后的正确代码：
double add(double a, double b) {
    return a + b;
}

double subtract(double a, double b) {
    return a - b;
}
```

#### 4.2.4 提交解决冲突后的代码

保存文件后，通过 `git add` 标记冲突已解决，并进行提交以完成此次合并：

```bash
# 标记冲突已解决
git add calculator.c

# 提交合并以生成合并提交（Merge Commit）
git commit -m "merge: resolve conflict in calculator.c and merge feature/subtract"
```

此时，合并顺利完成，主分支恢复到干净状态。

### 4.3 再次发布新版本

合并冲突解决后，删除已失效的本地分支，并为新版本打上标签并推送：

```bash
# 删除本地功能分支
git branch -d feature/subtract

# 创建版本标签并推送
git tag v3.0.0
git push
git push --tags
```

分支状态演变图解：

```text
合并前：
B (main, v2.0.0)
 └─ C (feature/subtract)

合并与打标签后：
B ──────── C (main, tag: v3.0.0)
```

## 5 版本回退与故障恢复

在实际开发中，如果发布的新版本（如 `v2.0.0`）出现严重故障，需要紧急回退至之前的版本（如 `v1.0.0`），应遵循以下规范操作。

### 5.1 临时切换至旧版本进行部署或验证

如果仅需临时部署旧版本，或在本地验证旧版本的行为，可直接检出对应的发布标签（`tag`）。此操作会使仓库处于“游离分支（detached HEAD）”状态，不会影响当前的主分支历史。

```bash
# 切换到旧版本标签
git switch --detach v1.0.0

# 验证或部署完成后，切回 main 分支
git switch main
```

### 5.2 生产环境永久回退（使用反向提交 revert）

在受保护的 `main` 分支上，**严禁使用 `git reset --hard` 并进行强制推送（force push）**，这会破坏其他协同开发者的历史记录。

规范的做法是使用 `git revert` 撤销错误的合并提交，生成一个“反向”的提交，然后作为新版本发布。

#### 5.2.1 查看历史图谱以定位错误的合并提交

在执行撤销之前，我们需要使用 `git log` 命令查看详细的历史分支图谱，准确找到需要撤销的合并提交的哈希值以及其第一亲代（Parent）哈希值：

```bash
# 查看包含分支图形和标签的单行历史记录
git log --oneline --graph --all --decorate
```

输出图形可能类似于：

```text
* d4d4d4d (HEAD -> main, origin/main, tag: v2.0.0) merge feature/add
|\  
| * c3c3c3c fix: correct addition precision error
| * b2b2b2b feature: add support for decimal addition
| * a2a2a2a feature: add addition operation
|/  
* a1b2c3d (tag: v1.0.0) chore: initial project
```

从图谱中可以看到：
- 错误的合并提交哈希值为 `d4d4d4d`。
- 它有两个亲代节点：第一亲代是原来 `main` 分支上的节点 `a1b2c3d`（即合并前的状态），第二亲代是功能分支 `feature/add` 上的最后一个提交 `c3c3c3c`。

#### 5.2.2 撤销已合并的提交

使用 `git revert` 撤销上述合并提交，并通过指定 `-m 1` 选项，告诉 Git 以第一亲代（即原来的主分支 `a1b2c3d`）为基准来撤销并恢复代码：

```bash
# -m 1 表示以第一亲代（HEAD -> main 合并前的状态）为基准进行反向重置
git revert -m 1 d4d4d4d
```

这会自动弹出一个编辑器供你确认撤销的提交信息，保存并关闭后将生成一个新的反向提交（例如哈希为 `e5e5e5e`）。

#### 5.2.3 推送并发布故障修复版本

撤销完成后，需要像正常发布新版本一样进行推送并打上新的热修复版本标签（如 `v2.0.1`）：

```bash
# 生成新的发布标签
git tag v2.0.1

# 推送至远程仓库
git push
git push --tags
```

分支状态演变图解：

```text
生产故障回滚：
d4d4d4d (tag: v2.0.0) ─── e5e5e5e (revert 掉合并, tag: v2.0.1)
                            ↑
                     代码恢复至 v1.0.0
```

## 6 工作流总结与核心原则

### 6.1 完整开发闭环

```text
main(v1.0.0)
    │
    ├── feature/add
    │       ↓
    │   开发提交
    │       ↓
    │   └── merge → tag(v2.0.0)
    │                   │
    │                   ├── feature/subtract
    │                   │       ↓
    │                   │   开发提交
    │                   │       ↓
    │                   └── merge → tag(v3.0.0)
    │                                   │
    │                                   ├── feature/multiply
    │                                   └── ...
```

### 6.2 核心开发原则

1. **`main` 分支稳定性**：`main` 分支永远保持稳定，不可直接在其上进行开发提交。
2. **分支隔离开发**：所有新功能/修复必须在 `feature/*` 或相关功能分支上进行开发。
3. **清晰的提交记录**：合理划分 `commit` 粒度，撰写清晰的提交说明。
4. **版本发布打标签**：使用 `tag` 明确记录发布版本（如语义化版本）。
5. **及时同步与推送**：发布后立即向远程推送 `main` 分支及新生成的 `tag`。
6. **迭代起点对齐**：下一轮开发应当从最新、最稳定的 `main` 分支拉出功能分支。
7. **持续闭环迭代**：遵循上述生命周期持续循环。
