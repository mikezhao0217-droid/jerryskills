---
name: supabase-integration
description: Integrate Supabase database with Next.js applications for data persistence
disable-model-invocation: false
allowed-tools: ["exec", "read", "write", "edit"]
---

# Supabase 数据库集成技能

## 用途
此技能用于在Next.js应用中集成Supabase数据库，实现数据持久化存储。

## 配置步骤

### 1. 安装依赖
```bash
npm install @supabase/supabase-js
```

### 2. 创建 Supabase 客户端
```typescript
// src/lib/supabaseClient.ts
import { createClient } from '@supabase/supabase-js'

const supabaseUrl = process.env.NEXT_PUBLIC_SUPABASE_URL as string
const supabasePublishableKey = process.env.NEXT_PUBLIC_SUPABASE_PUBLISHABLE_DEFAULT_KEY as string
const supabaseAnonKey = process.env.NEXT_PUBLIC_SUPABASE_ANON_KEY as string

// Prefer the new publishable key, fall back to anon key for backward compatibility
const supabaseKey = supabasePublishableKey || supabaseAnonKey;

if (!supabaseUrl || !supabaseKey) {
  console.warn('Supabase environment variables are not set. Database functionality will be disabled.')
}

export const supabase = supabaseUrl && supabaseKey 
  ? createClient(supabaseUrl, supabaseKey)
  : null
```

### 3. 环境变量配置
在 `.env.local` 中设置：
```env
NEXT_PUBLIC_SUPABASE_URL=your_supabase_project_url
NEXT_PUBLIC_SUPABASE_PUBLISHABLE_DEFAULT_KEY=your_supabase_publishable_key
```

或者：
```env
NEXT_PUBLIC_SUPABASE_URL=your_supabase_project_url
NEXT_PUBLIC_SUPABASE_ANON_KEY=your_supabase_anon_key  # 旧版，仍支持
```

### 4. 创建数据库表结构
在 Supabase 仪表板的 SQL Editor 中运行：
```sql
CREATE TABLE IF NOT EXISTS projects (
  id TEXT PRIMARY KEY,
  data JSONB NOT NULL,
  updated_at TIMESTAMP WITH TIME ZONE DEFAULT NOW()
);

ALTER TABLE projects ENABLE ROW LEVEL SECURITY;

CREATE POLICY "Allow read access for all users" ON projects
  FOR SELECT TO anon
  USING (true);

CREATE POLICY "Allow insert access for all users" ON projects
  FOR INSERT TO anon
  WITH CHECK (true);

CREATE POLICY "Allow update access for all users" ON projects
  FOR UPDATE TO anon
  USING (true);
```

### 5. 创建数据服务
```typescript
// src/services/projectService.ts
import { supabase } from '@/lib/supabaseClient';
import { ProjectData } from '@/types/project';

const PROJECTS_TABLE = 'projects';

// Fetch project data from Supabase
export const fetchProjectData = async (): Promise<ProjectData | null> => {
  if (!supabase) {
    // Fallback to default data if Supabase is not configured
    return {
      departments: []
    };
  }

  try {
    const { data, error } = await supabase
      .from(PROJECTS_TABLE)
      .select('data')
      .eq('id', 'main')
      .single();

    if (error) {
      if (error.code === 'PGRST116' || error.message.includes('Not Found')) {
        // Record doesn't exist, initialize with default data
        console.log('No project data found, initializing with default data...');
        const defaultData: ProjectData = {
          // ... default data structure
        };

        const insertResult = await supabase
          .from(PROJECTS_TABLE)
          .insert([{ id: 'main', data: defaultData, updated_at: new Date().toISOString() }]);

        if (insertResult.error) {
          console.error('Error inserting default data:', insertResult.error);
          return null;
        }

        console.log('Default data inserted successfully');
        return defaultData;
      } else {
        console.error('Error fetching project data:', error);
        return null;
      }
    }

    return data?.data as ProjectData || null;
  } catch (error) {
    console.error('Unexpected error fetching project data:', error);
    return null;
  }
};

// 其他CRUD操作...
```

## 部署注意事项

### Vercel 环境变量设置
1. 访问 Vercel 项目仪表板
2. 进入 Settings → Environment Variables
3. 添加必要的环境变量

### 数据库初始化
- 表必须在 Supabase 仪表板中手动创建
- 服务角色密钥（SUPABASE_SERVICE_ROLE_KEY）不应在客户端代码中使用
- 使用匿名密钥（anon key）或发布密钥（publishable key）进行客户端访问

## 常见问题和解决方案

### 1. 环境变量未设置
**问题**: 控制台显示 "variables are not set"
**解决方案**: 确保在部署平台（如Vercel）上正确设置了环境变量

### 2. 表不存在错误
**问题**: "Undefined table" 错误
**解决方案**: 在 Supabase 仪表板的 SQL Editor 中手动创建表

### 3. 权限错误
**问题**: 无法读写数据
**解决方案**: 检查并设置正确的 Row Level Security (RLS) 策略

### 4. 旧版密钥 vs 新版密钥
**问题**: Supabase 推荐使用 publishable key 替代 anon key
**解决方案**: 使用 NEXT_PUBLIC_SUPABASE_PUBLISHABLE_DEFAULT_KEY，保留 anon key 作为后备

## 最佳实践

1. **安全性**: 绝不在客户端代码中暴露服务角色密钥
2. **错误处理**: 妥善处理数据库连接和查询错误
3. **数据初始化**: 实现自动初始化机制，当数据库为空时插入默认数据
4. **环境管理**: 正确区分开发、测试和生产环境的配置
5. **类型安全**: 使用TypeScript确保数据结构的一致性

## 调试技巧

1. 检查浏览器控制台的错误信息
2. 验证环境变量是否正确设置
3. 在 Supabase 仪表板中检查表结构和数据
4. 使用 Supabase 日志排查API请求问题
5. 确认RLS策略配置正确