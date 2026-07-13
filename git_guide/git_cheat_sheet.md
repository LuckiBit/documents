# Git 速查表 (Git Cheat Sheet)

## 1 工作流程图 (Workflow)

```text
┌──────────────────────┐
│        Stash         │
└──────────────────────┘
 git stash pop ▲     │ git stash push
               │     ▼
┌──────────────────────┐ 
│      Workspace       │◀───────────────── git pull
└──────────────────────┘                     ▲
   git restore ▲     │ git add               │
               │     ▼                       │
┌──────────────────────┐                     │
│        Index         │                     │
└──────────────────────┘                     │
     git reset ▲     │ git commit            │
               │     ▼                       │
┌──────────────────────┐                     │
│   Local Repository   │                     │
└──────────────────────┘                     │
     git fetch ▲     │ git push              │
               │     ▼                       │
┌──────────────────────┐                     │
│ Upstream Repository  │─────────────────────┘
└──────────────────────┘
```

## 2 快速入门 (Getting Started)

- **初始化新仓库：**

  ```bash
  git init
  ```

- **克隆已有仓库：**

  ```bash
  git clone <url>
  ```

## 3 准备提交 (Prepare to Commit)

- **添加未追踪的文件或未暂存的修改：**

  ```bash
  git add <file>
  ```

- **添加所有未追踪的文件和未暂存的修改：**

  ```bash
  git add .
  ```

- **选择要暂存的文件部分：**

  ```bash
  git add -p
  ```

- **移动或重命名文件：**

  ```bash
  git mv <old-file> <new-file>
  ```

- **删除文件：**

  ```bash
  git rm <file>
  ```

- **停止追踪文件但不删除它：**

  ```bash
  git rm --cached <file>
  ```

- **取消暂存单个文件：**

  ```bash
  git reset <file>
  ```

- **取消暂存所有文件：**

  ```bash
  git reset
  ```

- **查看暂存区状态：**

  ```bash
  git status
  ```

## 4 提交代码 (Make Commits)

- **提交代码（打开编辑器编写提交信息）：**

  ```bash
  git commit
  ```

- **提交代码：**

  ```bash
  git commit -m 'message'
  ```

- **提交所有未暂存的修改：**

  ```bash
  git commit -am 'message'
  ```

## 5 暂存修改 (Stash Changes)

### 基础命令 (Basic Commands)

- **保存并添加描述（最推荐写法）：**

  ```bash
  git stash push -m "message"
  ```

- **同时暂存未跟踪的新文件（常用）：**

  ```bash
  git stash push -u -m "message"
  ```

- **暂存所有文件（包括忽略的文件）：**

  ```bash
  git stash push -a -m "message"
  ```

- **最简单的暂存（相当于 push，老写法仍可使用）：**

  ```bash
  git stash
  ```

### 查看 Stash (View Stash)

- **查看所有暂存记录：**

  ```bash
  git stash list
  ```

- **查看每个 stash 的改动文件：**

  ```bash
  git stash list --stat
  ```

- **查看某个 stash 的改动概要：**

  ```bash
  git stash show stash@{0}
  ```

- **查看某个 stash 的详细修改内容（patch）：**

  ```bash
  git stash show -p stash@{0}
  ```

### 恢复 Stash (Restore Stash)

- **恢复最新 stash 并删除记录（最常用）：**

  ```bash
  git stash pop
  ```

- **恢复指定 stash 并删除记录：**

  ```bash
  git stash pop stash@{1}
  ```

- **恢复最新 stash 但保留记录：**

  ```bash
  git stash apply
  ```

- **恢复指定 stash 但保留记录：**

  ```bash
  git stash apply stash@{2}
  ```

### 删除 Stash (Delete Stash)

- **删除最新（或某个默认）stash：**

  ```bash
  git stash drop stash@{0}
  ```

- **删除指定 stash：**

  ```bash
  git stash drop stash@{3}
  ```

- **清空所有 stash（慎用）：**

  ```bash
  git stash clear
  ```

## 6 分支管理 (Manage Branches)

### 基础分支操作 (Basic Branch Operations)

- **列出所有本地分支：**

  ```bash
  git branch
  ```

- **删除分支：**

  ```bash
  git branch -d <branch>
  ```

- **强制删除分支：**

  ```bash
  git branch -D <branch>
  ```

### 基础切换 (Basic Switch)

- **切换到已有分支：**

  ```bash
  git switch <branch>
  ```

- **切换到上一个分支：**

  ```bash
  git switch -
  ```

### 创建并切换分支 (Create and Switch)

- **创建新分支并切换：**

  ```bash
  git switch -c <new-branch>
  ```

- **从指定分支（如 `main`）创建新分支并切换：**

  ```bash
  git switch -c <new-branch> main
  ```

- **创建本地分支并关联远程分支：**

  ```bash
  git switch --create <local-branch> origin/<remote-branch>
  ```

- **强制创建并切换（如果分支已存在则覆盖）：**

  ```bash
  git switch -C <new-branch>
  ```

### 切换到 Tag 或 Commit (Switch to Tag/Commit)

> [!TIP]
> 推荐通过创建新分支来查看或修改旧版本的代码，这是最安全的做法。如果只是临时查看，可以使用 `--detach`（分离 HEAD）。

- **从 Tag 或 Commit 创建分支并切换（推荐，最安全做法）：**

  ```bash
  git switch -c <new-branch> <commit>
  ```

- **切换到指定 Tag 或 Commit（进入分离 HEAD 状态，仅临时查看）：**

  ```bash
  git switch --detach <commit>
  ```

- **切换到 N 个提交之前（进入分离 HEAD 状态）：**

  ```bash
  git switch --detach HEAD~3
  ```

> [!NOTE]
> 对于上述所有 `git switch` 相关的操作，老版本的 Git 中对应的命令是 `git checkout`（例如：`git checkout <branch>`，`git checkout -b <new-branch>`）。为了语义更加明确，较新的 Git 版本（2.23+）推荐统一使用 `git switch` 来处理分支切换逻辑。

## 7 差异比较 (Diff)

### 比较工作区与暂存区 (Diff Staged/Unstaged Changes)

- **比较工作区、暂存区与最后一次提交的差异：**

  ```bash
  git diff HEAD
  ```

- **仅比较暂存区的修改：**

  ```bash
  git diff --staged
  ```

- **仅比较未暂存的修改：**

  ```bash
  git diff
  ```

### 对比提交记录 (Diff Commits)

- **查看提交与其父提交的差异：**

  ```bash
  git show <commit>
  ```

- **比较两个提交：**

  ```bash
  git diff <commit> <commit>
  ```

- **查看自某次提交以来特定文件的变化：**

  ```bash
  git diff <commit> <file>
  ```

- **查看差异摘要：**

  ```bash
  git diff <commit> --stat
  ```

  或者

  ```bash
  git show <commit> --stat
  ```

## 8 撤销修改 (Discard Your Changes)

- **丢弃工作区单个文件的修改：**

  ```bash
  git restore <file>
  ```

  或者

  ```bash
  git checkout <file>
  ```

- **丢弃工作区和暂存区中单个文件的修改：**

  ```bash
  git restore --staged --worktree <file>
  ```

  或者

  ```bash
  git checkout HEAD <file>
  ```

- **丢弃所有工作区和暂存区的修改：**

  ```bash
  git reset --hard
  ```

- **删除未追踪的文件：**

  ```bash
  git clean
  ```

- **暂存 (Stash) 所有未提交的修改：**

  ```bash
  git stash
  ```

## 9 编辑历史记录 (Edit History)

- **“撤销”最近一次提交（保留工作区修改）：**

  ```bash
  git reset HEAD^
  ```

- **将最近 5 次提交压缩为一次：**

  ```bash
  git rebase -i HEAD~6
  ```

  然后将你要与前一个提交合并的那些 `commit` 的 `pick` 改为 `fixup`。

- **撤销失败的 `rebase`：**

  ```bash
  git reflog BRANCHNAME
  ```

  然后在 `reflog` 中手动找到正确的 `commit ID`，运行：

  ```bash
  git reset --hard <commit>
  ```

- **修改提交信息（或追加忘记提交的文件）：**

  ```bash
  git commit --amend
  ```

## 10 追溯历史 (Code Archaeology)

- **查看当前分支的提交历史：**

  ```bash
  git log
  ```

- **查看特定分支提交历史：**

  ```bash
  git log main
  ```

- **以图表形式查看分支提交历史：**

  ```bash
  git log --graph main
  ```

- **以精简的单行格式查看历史：**

  ```bash
  git log --oneline
  ```

- **以单行结合图表的形式查看历史：**

  ```bash
  git log --oneline --graph
  ```

- **以一行、图形化、包含时间与作者的形式查看历史：**

  ```bash
  git log --oneline --graph --format="%h %ad %an: %s" --date=short
  ```

- **查看特定文件的修改历史：**

  ```bash
  git log <file>
  ```

- **查看特定文件的修改历史（包括重命名前）：**

  ```bash
  git log --follow <file>
  ```

- **查找添加或删除了某段文本的提交：**

  ```bash
  git log -G banana
  ```

- **查看文件每一行最后的修改者：**

  ```bash
  git blame <file>
  ```

## 11 合并分叉的分支 (Combine Diverged Branches)

- **使用 `rebase` 合并：**

  ```text
  Before:
        D---E---F (banana)
       /
  A---B---C---G (main)

  After:
                D'--E'--F' (banana)
               /
  A---B---C---G (main)
  ```

  ```bash
  git switch banana
  ```

  ```bash
  git rebase main
  ```

- **使用 `merge` 合并：**

  ```text
  Before:
        D---E---F (banana)
       /
  A---B---C---G (main)

  After:
        D---E---F (banana)
       /         \
  A---B---C---G---H (main)
  ```

  ```bash
  git switch main
  ```

  ```bash
  git merge banana
  ```

- **使用 `squash merge` 压缩合并：**

  ```text
  Before:
        D---E---F (banana)
       /
  A---B---C---G (main)

  After:
        D---E---F (banana)
       /
  A---B---C---G---DEF (main)
  ```

  ```bash
  git switch main
  ```

  ```bash
  git merge --squash banana
  ```

  ```bash
  git commit
  ```

- **快进合并 (fast-forward merge)：**

  ```text
  Before:
        C---D---E (banana)
       /
  A---B (main)

  After:
  A---B---C---D---E (main, banana)
  ```

  ```bash
  git switch main
  ```

  ```bash
  git merge banana
  ```

- **复制一次提交到当前分支 (cherry-pick)：**

  ```text
  Before:
        D---E---F (banana)
       /
  A---B---C---G (main)

  After (cherry-pick E):
        D---E---F (banana)
       /
  A---B---C---G---E' (main)
  ```

  ```bash
  git cherry-pick <commit>
  ```

- **使用 `reset` 强制覆盖当前分支 (overwrite branch)：**

  ```text
  Before:
        D---E---F (banana)
       /
  A---B---C---G (main)

  After:
        D---E---F (main, banana)
       /
  A---B---C---G (abandoned)
  ```

  ```bash
  git switch main
  ```

  ```bash
  git reset --hard banana
  ```

- **保持线性历史的完整工作流 (Rebase, Merge, Delete & Push)：**

  ```text
  Before:
        D---E---F (banana)
       /
  A---B---C---G (main, origin/main)

  After:
  A---B---C---G---D'---E'---F' (main, origin/main)
  ```

  ```bash
  # 1. 确保子分支基于 main 的最新提交
  git switch banana
  git rebase main

  # 2. 切换回 main 并执行快进合并
  git switch main
  git merge banana

  # 3. 删除临时分支
  git branch -d banana

  # 4. 推送到远程仓库
  git push origin main
  ```

## 12 恢复文件状态 (Restore Files)

> [!NOTE]
> `git restore` 是 Git 2.23 引入的新命令，专门用于恢复工作区或暂存区的文件，使得职能更加清晰，替代了老版本中部分 `git checkout` 和 `git reset` 的功能。

### 基础恢复（撤销工作区修改）

- **恢复工作区文件（丢弃当前未暂存的修改）：**

  ```bash
  git restore <file>
  ```

- **恢复当前目录下的所有文件：**

  ```bash
  git restore .
  ```

- **恢复整个文件夹（例如 `src/`）：**

  ```bash
  git restore src/
  ```

### 取消暂存（撤销 `git add`）

- **从暂存区取消暂存指定文件（将其移回工作区）：**

  ```bash
  git restore --staged <file>
  ```

- **取消所有暂存的文件：**

  ```bash
  git restore --staged .
  ```

### 恢复到指定版本（核心用法）

- **恢复文件到上一个 commit 的状态：**

  ```bash
  git restore --source=HEAD~1 <file>
  ```

- **恢复文件到 `main` 分支的最新版本：**

  ```bash
  git restore --source=main <file>
  ```

- **恢复文件到指定 Tag 的状态：**

  ```bash
  git restore --source=v1.0.0 <file>
  ```

- **恢复文件到指定 Commit ID 的状态：**

  ```bash
  git restore --source=a1b2c3d <file>
  ```

## 13 标签管理 (Manage Tags)

### 创建标签 (Create Tags)

- **创建轻量标签：**

  ```bash
  git tag v1.0.0
  ```

- **创建带注释标签：**

  ```bash
  git tag -a v1.0.0 -m "message"
  ```

- **为指定提交打标签：**

  ```bash
  git tag -a v1.0.0 -m "message" <commit>
  ```

### 查看标签 (View Tags)

- **列出所有标签：**

  ```bash
  git tag
  ```

- **按模式过滤标签：**

  ```bash
  git tag -l "v1.*"
  ```

- **显示标签及说明：**

  ```bash
  git tag -n
  ```

- **查看标签详细信息：**

  ```bash
  git show v1.0.0
  ```

### 推送与拉取标签 (Push & Fetch Tags)

- **推送单个标签：**

  ```bash
  git push origin v1.0.0
  ```

- **推送所有本地标签：**

  ```bash
  git push origin --tags
  ```

- **拉取所有远程标签：**

  ```bash
  git fetch --tags
  ```

### 删除标签 (Delete Tags)

- **删除本地标签：**

  ```bash
  git tag -d v1.0.0
  ```

- **删除远程标签：**

  ```bash
  git push origin --delete v1.0.0
  ```

### 基于标签的操作 (Tag Operations)

- **切换到标签（推荐，处于分离头指针状态）：**

  ```bash
  git switch --detach v1.0.0
  ```

- **从标签创建并切换到新分支（推荐）：**

  ```bash
  git switch -c <new-branch> v1.0.0
  ```

- **从标签恢复所有文件到工作区：**

  ```bash
  git restore --source=v1.0.0 -- .
  ```

- **从标签恢复单个文件：**

  ```bash
  git restore --source=v1.0.0 <file>
  ```

## 14 管理远程仓库 (Manage Remotes)

- **添加远程仓库（常用 SSH）：**

  ```bash
  git remote add origin git@github.com:xxx/yyy.git
  ```

- **添加远程仓库（HTTPS）：**

  ```bash
  git remote add origin https://github.com/xxx/yyy.git
  ```

- **查看当前所有远程仓库：**

  ```bash
  git remote -v
  ```

- **重命名远程仓库（将原名 `origin` 改为新名 `upstream`）：**

  ```bash
  git remote rename origin upstream
  ```

- **修改远程仓库地址：**

  ```bash
  git remote set-url origin <new-url>
  ```

- **删除远程仓库关联：**

  ```bash
  git remote remove origin
  ```

## 15 推送修改 (Push Your Changes)

- **将 `main` 分支推送到远程 `origin`：**

  ```bash
  git push origin main
  ```

- **将当前分支推送到其远程“跟踪分支”：**

  ```bash
  git push
  ```

- **推送以前从未推送过的分支：**

  ```bash
  git push -u origin <branch>
  ```

- **强制推送：**

  ```bash
  git push --force-with-lease
  ```

- **推送标签：**

  ```bash
  git push --tags
  ```

## 16 拉取修改 (Pull Changes)

- **拉取变更（不修改本地分支）：**

  ```bash
  git fetch origin main
  ```

- **拉取变更并在当前分支执行 `rebase`：**

  ```bash
  git pull --rebase
  ```

- **拉取变更并合并到当前分支：**

  ```bash
  git pull origin main
  ```

  或者

  ```bash
  git pull
  ```

## 17 配置 Git (Configure Git)

- **查看配置：**

  ```bash
  git config --list
  ```

- **查看所有全局配置：**

  ```bash
  git config --global --list
  ```

- **配置用户名：**

  ```bash
  git config user.name 'your name'
  ```

- **配置邮箱：**

  ```bash
  git config user.email 'you@example.com'
  ```

- **配置默认编辑器为 `nvim`：**

  ```bash
  git config --global core.editor nvim
  ```

- **配置默认分支为 `main`：**

  ```bash
  git config --global init.defaultBranch main
  ```

- **创建常用别名：**

  ```bash
  git config --global alias.st status
  git config --global alias.lg "log --oneline --graph --decorate"
  ```

## 18 重要文件 (Important Files)

- **本地 Git 配置：** `.git/config`
- **全局 Git 配置：** `~/.gitconfig`
- **忽略文件列表：** `.gitignore`

## 19 引用提交的几种方式 (Ways to refer to a commit)

每次提到 `<commit>` 时，你可以使用以下任意一种格式代替：

- **分支 (Branch)：** `main`
- **标签 (Tag)：** `v0.1.1`
- **提交 ID (Commit ID)：** `3e887ab`
- **远程分支 (Remote Branch)：** `origin/main`
- **当前提交 (Current Commit)：** `HEAD`
- **3 次提交前 (3 Commits Ago)：** `HEAD^^^` 或 `HEAD~3`
