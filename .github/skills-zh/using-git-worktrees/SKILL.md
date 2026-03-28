---
name: using-git-worktrees
description: "当开始需要与当前工作空间隔离的功能工作或执行实现计划之前使用 - 通过智能目录选择和安全验证创建隔离的 git 工作树"
---

# 使用 Git 工作树

## 概述

Git 工作树创建共享同一仓库的隔离工作空间，允许在多个分支上同时工作而无需切换。

**核心原则：** 系统的目录选择 + 安全验证 = 可靠的隔离。

**开始时宣布：** "我正在使用 using-git-worktrees 技能设置隔离的工作空间。"

## 目录选择流程

遵循此优先顺序：

### 1. 检查现有目录

```bash
ls -d .worktrees 2>/dev/null     # 首选（隐藏）
ls -d worktrees 2>/dev/null      # 替代
```

如果找到：使用该目录。如果两者都存在，`.worktrees` 优先。

### 2. 检查工作空间指令

检查工作空间指令（例如 `copilot-instructions.md`）了解工作树目录偏好。

### 3. 询问用户

如果没有目录存在且没有指定偏好：

```
未找到工作树目录。我应该在哪里创建工作树？

1. .worktrees/ (项目本地，隐藏)
2. ~/worktrees/<project-name>/ (全局位置)

你更偏好哪个？
```

## 安全验证

### 对于项目本地目录

**必须在创建工作树之前验证目录被忽略：**

```bash
git check-ignore -q .worktrees 2>/dev/null || git check-ignore -q worktrees 2>/dev/null
```

**如果未被忽略：** 添加到 .gitignore，提交，然后继续。

### 对于全局目录

不需要 .gitignore 验证 - 完全在项目外。

## 创建步骤

### 1. 检测项目名称

```bash
project=$(basename "$(git rev-parse --show-toplevel)")
```

### 2. 创建工作树

```bash
git worktree add "$path" -b "$BRANCH_NAME"
cd "$path"
```

### 3. 运行项目设置

自动检测并运行适当的设置：

```bash
if [ -f package.json ]; then npm install; fi
if [ -f Cargo.toml ]; then cargo build; fi
if [ -f requirements.txt ]; then pip install -r requirements.txt; fi
if [ -f pyproject.toml ]; then poetry install; fi
if [ -f go.mod ]; then go mod download; fi
```

### 4. 验证干净的基线

运行测试确保工作树开始干净。

**如果测试失败：** 报告失败，询问是继续还是调查。
**如果测试通过：** 报告就绪。

### 5. 报告位置

```
工作树准备就绪于 <完整路径>
测试通过 (<N> 个测试，0 个失败)
准备实现 <功能名称>
```

## 危险信号

**永远不要：**
- 未验证它被忽略就创建工作树（项目本地）
- 跳过基线测试验证
- 不询问就带着失败测试继续
- 当模糊时假设目录位置

**总是：**
- 遵循目录优先级：现有 > 工作空间指令 > 询问
- 验证项目本地的目录被忽略
- 自动检测并运行项目设置
- 验证干净的测试基线

## 集成

**被以下技能调用：**
- **brainstorming** - 当设计被批准并且随后是实现时
- **subagent-driven-development** - 在执行任何任务之前
- **executing-plans** - 在执行任何任务之前

**配对使用：**
- **finishing-a-development-branch** - 工作完成后清理
