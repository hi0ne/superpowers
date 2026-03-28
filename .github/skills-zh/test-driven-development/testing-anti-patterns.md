# 测试反模式

**在以下情况加载此参考：** 编写或更改测试、添加模拟或被诱惑向生产代码添加仅测试方法。

## 概述

测试必须验证真实行为，不是模拟行为。模拟是隔离的手段，不是被测试的东西。

**核心原则：** 测试代码做什么，不是模拟做什么。

## 铁律

```
1. 永远不要测试模拟行为
2. 永远不要向生产类添加仅测试方法
3. 永远不要在不理解依赖的情况下模拟
```

## 反模式 1：测试模拟行为

```typescript
// ❌ 坏：测试模拟存在
test('渲染侧边栏', () => {
  render(<Page />);
  expect(screen.getByTestId('sidebar-mock')).toBeInTheDocument();
});

// ✅ 好：测试真实组件
test('渲染侧边栏', () => {
  render(<Page />);
  expect(screen.getByRole('navigation')).toBeInTheDocument();
});
```

**门槛：** 在对任何模拟元素断言之前，问："我在测试真实组件行为还是只是模拟存在？"

## 反模式 2：生产中的仅测试方法

```typescript
// ❌ 坏：destroy() 只在测试中使用
class Session {
  async destroy() { /* 清理 */ }
}

// ✅ 好：测试工具处理测试清理
export async function cleanupSession(session: Session) {
  const workspace = session.getWorkspaceInfo();
  if (workspace) await workspaceManager.destroyWorkspace(workspace.id);
}
```

**门槛：** 在向生产类添加任何方法之前，问："这只被测试使用吗？"如果是 → 放在测试工具中。

## 反模式 3：不理解就模拟

```typescript
// ❌ 坏：模拟破坏测试逻辑
vi.mock('ToolCatalog', () => ({
  discoverAndCacheTools: vi.fn().mockResolvedValue(undefined)
}));
// 模拟阻止测试依赖的配置写入！

// ✅ 好：在正确层次模拟
vi.mock('MCPServerManager'); // 只是模拟慢速服务器启动
// 配置写入仍然有效 → 重复检测有效
```

**门槛：** 在模拟之前，问："真实方法有什么副作用？这个测试依赖其中任何一个吗？"

## 反模式 4：不完整的模拟

```typescript
// ❌ 坏：部分模拟
const mockResponse = {
  status: 'success',
  data: { userId: '123', name: 'Alice' }
  // 缺失：下游代码使用的元数据
};

// ✅ 好：匹配真实结构的完整模拟
const mockResponse = {
  status: 'success',
  data: { userId: '123', name: 'Alice' },
  metadata: { requestId: 'req-123', timestamp: Date.now() }
};
```

**铁律：** 模拟现实中存在的完整数据结构，不仅仅是你的即时测试使用的字段。
