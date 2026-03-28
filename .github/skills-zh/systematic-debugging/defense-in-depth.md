# 纵深防御验证

## 概述

当你修复由无效数据引起的 bug 时，在一个地方添加验证感觉很足够。但是该单一检查可能会被不同的代码路径、重构或模拟绕过。

**核心原则：** 在数据通过的每个层验证。使 bug 结构上不可能。

## 四个层

### 层 1：入口点验证
在 API 边界拒绝明显无效的输入。

```typescript
function createProject(name: string, workingDirectory: string) {
  if (!workingDirectory || workingDirectory.trim() === '') {
    throw new Error('workingDirectory 不能为空');
  }
  if (!existsSync(workingDirectory)) {
    throw new Error(`workingDirectory 不存在: ${workingDirectory}`);
  }
}
```

### 层 2：业务逻辑验证
确保数据对此操作有意义。

```typescript
function initializeWorkspace(projectDir: string, sessionId: string) {
  if (!projectDir) {
    throw new Error('workspace 初始化需要 projectDir');
  }
}
```

### 层 3：环境防护
防止在特定上下文中的危险操作。

```typescript
async function gitInit(directory: string) {
  if (process.env.NODE_ENV === 'test') {
    const normalized = normalize(resolve(directory));
    const tmpDir = normalize(resolve(tmpdir()));
    if (!normalized.startsWith(tmpDir)) {
      throw new Error(`测试期间拒绝在临时目录外进行 git init: ${directory}`);
    }
  }
}
```

### 层 4：调试工具
为取证捕获上下文。

```typescript
async function gitInit(directory: string) {
  const stack = new Error().stack;
  logger.debug('即将 git init', { directory, cwd: process.cwd(), stack });
}
```

## 应用模式

1. **跟踪数据流** - 坏值从哪里产生？在哪里使用？
2. **映射所有检查点** - 列出数据通过的每个点
3. **在每层添加验证** - 入口、业务、环境、调试
4. **测试每层** - 尝试绕过层 1，验证层 2 捕获它

## 关键见解

所有四个层都是必要的。不同的层捕获不同的情况：
- 入口验证捕获大多数 bug
- 业务逻辑捕获边缘情况
- 环境防护防止上下文特定的危险
- 调试日志在其他层失败时有帮助

**不要停留在一个验证点。** 在每层添加检查。
