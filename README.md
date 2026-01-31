# Jerry's Skills Repository

这是Jerry的技能知识库，用于记录各种开发经验和技能文档。遵循Agent Skills开放标准，适用于多个AI工具。

## 目录

- [Next.js 和 Vercel 部署](./nextjs-vercel-deployment/SKILL.md) - Next.js 16.1.6 和 Vercel 部署相关经验
- [Supabase 数据库集成](./supabase-integration/SKILL.md) - 在 Next.js 应用中集成 Supabase 数据库

## 说明

每个技能都放在单独的文件夹中，包含一个必需的SKILL.md文件，以及可选的支持文件。当需要执行任务时，应先查看此仓库中是否有相关的技能可以参考和使用。

## 技能管理机制

1. **创建新技能**：当遇到新问题并成功解决后，创建新的技能目录和SKILL.md文件
2. **更新现有技能**：当现有问题有新进展或发现新细节时，及时更新对应技能文档
3. **定期回顾**：定期检查和整理已有技能，确保内容准确有效
4. **技能复用**：在面对新问题时，优先查找已有技能，避免重复解决问题

## 添加新技能

要添加新技能，请创建一个新的文件夹，命名格式为`[技能名称]`，并在其中添加SKILL.md文件。参考[skill-template.md](./skill-template.md)创建符合标准的技能文件。

## 技能标准

技能遵循[Agent Skills](https://agentskills.io)开放标准，包含：

- 每个技能都在独立目录中
- SKILL.md为主入口文件
- 可选的支持文件（模板、示例、脚本等）
- YAML frontmatter配置技能行为

## 更新历史

- 2026-01-31: 建立技能管理机制
- 2026-01-31: 重构为官方Skills标准
- 2026-01-31: 添加Supabase集成技能