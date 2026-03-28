# 根本原因跟踪

## 概述

Bug 通常在调用堆栈深处显现。你的本能是在错误出现的地方修复，但那是在治疗症状。

**核心原则：** 向后通过调用链跟踪直到找到原始触发器，然后在源头修复。

## 何时使用

- 错误发生在执行深处（不在入口点）
- 堆栈跟踪显示长调用链
- 不清楚无效数据从哪里产生
- 需要找到哪个测试/代码触发问题

## 跟踪过程

### 1. 观察症状
```
错误: git init 在 /Users/user/project/packages/core 中失败
```

### 2. 找到直接原因
```typescript
await execFileAsync('git', ['init'], { cwd: projectDir });
```

### 3. 问：什么调用了这个？
```typescript
WorktreeManager.createSessionWorktree(projectDir, sessionId)
  → 被 Session.initializeWorkspace() 调用
  → 被 Session.create() 调用
  → 被 Project.create() 处的测试调用
```

### 4. 继续向上跟踪
- `projectDir = ''` (空字符串！)
- 空字符串作为 `cwd` 解析为 `process.cwd()`

### 5. 找到原始触发器
```typescript
const context = setupCoreTest(); // 返回 { tempDir: '' }
Project.create('name', context.tempDir); // 在 beforeEach 之前访问！
```

## 添加堆栈跟踪

当你无法手动跟踪时：

```typescript
async function gitInit(directory: string) {
  const stack = new Error().stack;
  console.error('DEBUG git init:', {
    directory,
    cwd: process.cwd(),
    stack,
  });
  await execFileAsync('git', ['init'], { cwd: directory });
}
```

**关键：** 在测试中使用 `console.error()`（不是 logger - 可能不显示）

## 关键原则

**永远不要只在错误出现的地方修复。** 跟踪回去找到原始触发器。

## 堆栈跟踪技巧

- **在测试中：** 使用 `console.error()` 而不是 logger
- **操作前：** 在危险操作前记录，而不是在它失败后
- **包含上下文：** 目录、cwd、环境变量、时间戳
- **捕获堆栈：** `new Error().stack` 显示完整调用链
