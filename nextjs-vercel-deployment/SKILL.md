---
name: nextjs-vercel-deployment
description: Deploy Next.js applications to Vercel, handling common issues with Next.js 16.1.6, Turbopack, and API routes
disable-model-invocation: false
allowed-tools: ["exec", "read", "write", "edit"]
---

# Next.js 16.1.6 和 Vercel 部署技能

## 用途
此技能用于部署Next.js应用到Vercel平台，并处理Next.js 16.1.6版本特有的常见问题。

## 常见问题和解决方案

### 1. API 路由参数类型变更
**问题**: Next.js 16.1.6 中 API 路由的 `params` 变成了 Promise 对象
**解决方案**:
```typescript
// 旧写法
export async function PATCH(
  request: NextRequest,
  { params }: { params: { id: string; milestoneId: string } }
)

// 新写法
export async function PATCH(
  request: NextRequest,
  { params }: { params: Promise<{ id: string; milestoneId: string }> }
) {
  const awaitedParams = await params;
  const { id: projectId, milestoneId } = awaitedParams;
  // ... 其他逻辑
}
```

### 2. 重复定义错误
**问题**: 文件中出现重复的函数定义或变量声明
**解决方案**: 仔细检查文件内容，移除重复的代码块

### 3. Turbopack 配置冲突
**问题**: Next.js 16 默认启用 Turbopack，但配置了 webpack 导致冲突
**解决方案**:
- 移除不必要的 webpack 配置
- 使用正确的 Turbopack 配置选项

### 4. Next.js 配置选项变更
**问题**: Next.js 16 中某些配置选项的位置发生变化
**解决方案**:
- `typedRoutes` 从 `experimental.typedRoutes` 移到顶层配置
- `turbopack` 选项不是有效选项，应移除

### 5. 静态导出与 API 路由冲突
**问题**: 使用 `output: 'export'` 会禁用 API 路由
**解决方案**: 如果需要 API 路由，不要使用静态导出配置

## 部署流程
1. 确保 Next.js 配置与当前版本兼容
2. 检查 API 路由参数类型是否正确
3. 避免代码重复定义
4. 在 Vercel 上部署前本地测试构建

## Next.js 16 配置示例
```typescript
import type { NextConfig } from "next";

const nextConfig: NextConfig = {
  typedRoutes: true,
};

export default nextConfig;
```

## 调试技巧
1. 本地运行 `npm run build` 测试构建
2. 注意查看构建日志中的详细错误信息
3. 检查 TypeScript 类型定义是否正确
4. 确认所有导入导出语法正确