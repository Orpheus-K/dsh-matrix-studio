# DSH Matrix Studio: 开放式 AI 原生全栈工程矩阵

[English](README.md) | 中文

**DSH Matrix Studio** 是依托 [DeepSeek Harness (`dsh`)](../../README.zh.md) 微内核插件体系构建的开放式、可组合全栈软件工程底座。它支持 **从 0 到 1 自由拼装技术栈**、**开箱即用行业模板库**、**存量老项目逆向复刻翻新**，并配备了 **四区联动沉浸式可视化 UI 工作台**。

---

## 📚 文档目录与技术规格索引

1. [**系统需求与架构设计文档 (System PRD)**](01-system-prd.zh.md)
   - 开放中立理念、3 大工程使用形态、7 大正交技术栈维度定义、`stack.recipe.yml` 动态配置机制。
2. [**插件包技术规格说明书 (Plugin Packages Spec)**](02-plugin-packages-spec.zh.md)
   - 拼装器与模板中心（Composer）、老项目逆向复刻、沉浸式 UI 工作台切面与标准 Adapter 接口规范。
3. [**UI 组件库适配器开发规范与参考实现 (UI Adapter Spec)**](03-ui-adapter-specification.zh.md)
   - 标准 UI 适配器开发指南、`lucky-ui` 官方开源参考实现、第三方组件库 3 步接入法。
4. [**开发演进路线图与阶段任务 (Development Roadmap)**](04-development-roadmap.zh.md)
   - 拼装器优先策略（Phase 1 Top 1）、四大演进里程碑与验收标准。
5. [**可视化全栈工作台 UI/UX 交互规范 (UI/UX Specification)**](05-ui-ux-workbench-specification.zh.md)
   - 四区联动布局（AI 副驾决策流 + 多端沙箱画布 + 双向属性微调/Monaco + 状态栏）、技术栈乐高拼装看板、老项目复刻拖拽区与模板画廊设计。

---

## 🧩 7 大可拼装维度

```text
1. 终端与渲染形态 ───► [UniApp 移动端] | [Next.js 15/16] | [Nuxt 4 SSR] | [PC CSR] | [Vben 5 Admin]
2. UI 组件体系   ───► [lucky-ui] | [Wot Design] | [Shadcn-UI] | [Ant Design] | [Element Plus]
3. 样式与原子化 ───► [Tailwind CSS v4] | [UnoCSS] | [SCSS Modules]
4. 服务端架构   ───► [Hono (Edge)] | [NestJS 11] | [Nitro] | [Go-Zero]
5. 数据契约与ORM ───► [Drizzle ORM] | [Prisma v6] | [TypeBox / Zod]
6. 状态与请求   ───► [Hono RPC (零胶水)] | [tRPC] | [Pinia 2.3]
7. 交付流水线   ───► [微信/支付宝 miniprogram-ci] | [Docker] | [Vercel / Cloudflare]
```
