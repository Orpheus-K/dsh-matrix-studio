# DSH Matrix Studio 插件包技术规格说明书 (Plugin Packages Spec)

> **文档版本**: v1.3.0 (全功能集成与沉浸式 UI 工作台版)
> **包命名前缀**: `@deepseek-ai/dsh-plugin-matrix-*`

---

## 1. 插件拓扑与 Monorepo 目录规划

```text
packages/matrix/
├── composer/             # @deepseek-ai/dsh-plugin-matrix-composer (拼装器中枢、Recipe 解析与轻量模板库)
├── contract/             # @deepseek-ai/dsh-plugin-matrix-contract (Drizzle/TypeBox/Hono RPC 数据契约)
├── ingest/               # @deepseek-ai/dsh-plugin-matrix-ingest (Figma/Vision多模态解析 + 老项目逆向分析)
├── ui-studio/            # @deepseek-ai/dsh-plugin-matrix-ui-studio (四区联动沉浸式 Web 工作台扩展)
│
├── adapters-ui/          # UI 组件库适配器集合
│   ├── lucky-ui/         # @deepseek-ai/dsh-adapter-ui-lucky (lucky-ui 官方参考适配器)
│   ├── wot-design/       # @deepseek-ai/dsh-adapter-ui-wot (Wot Design Uni 适配器)
│   └── shadcn-vue/       # @deepseek-ai/dsh-adapter-ui-shadcn (Shadcn-Vue PC 适配器)
│
├── adapters-target/      # 终端目标生成适配器
│   ├── uniapp/           # @deepseek-ai/dsh-adapter-target-uniapp (UniApp Vue3 + Vite6 生成器)
│   ├── nuxt4/            # @deepseek-ai/dsh-adapter-target-nuxt4 (Nuxt 4 SSR 生成器)
│   └── vben5/            # @deepseek-ai/dsh-adapter-target-vben5 (Vben Admin v5 后台生成器)
│
├── adapters-backend/     # 服务端与运行时适配器
│   ├── hono/             # @deepseek-ai/dsh-adapter-backend-hono (Hono Edge-Native 生成器)
│   └── nestjs/           # @deepseek-ai/dsh-adapter-backend-nestjs (NestJS 11 企业微服务生成器)
│
├── preview/              # @deepseek-ai/dsh-plugin-matrix-preview (WebContainer 混合多视口沙箱)
├── delivery/             # @deepseek-ai/dsh-plugin-matrix-delivery (微信 CI / Docker 交付)
└── bundle-matrix/        # @deepseek-ai/dsh-bundle-matrix-studio (Studio 全套组合包与 Profile)
```

---

## 2. 核心扩展服务接口 (Service Interfaces)

### 2.1 技术栈拼装器与模板中心：`ctx.matrixComposer`
```typescript
export interface TemplateManifest {
  id: string
  displayName: string
  description: string
  tags: string[]
  recipe: StackRecipe
  blueprintPath: string
}

export interface MatrixComposerService {
  // 模板中心能力
  listTemplates(): Promise<TemplateManifest[]>
  getTemplate(id: string): Promise<TemplateManifest | undefined>
  applyTemplate(id: string): Promise<ActiveStackContext>

  // 拼装与适配器注册
  registerTargetAdapter(id: string, adapter: TargetAdapter): void
  registerUiAdapter(id: string, adapter: UiKitAdapter): void
  registerBackendAdapter(id: string, adapter: BackendAdapter): void
  loadRecipe(recipe: StackRecipe): ActiveStackContext
  synthesizePromptRules(recipe: StackRecipe): string
}
```

### 2.2 存量项目逆向复刻切面：`ctx.matrixIngest`
```typescript
export interface LegacyProjectAnalysis {
  sourceStack: {
    vueVersion?: string
    bundler?: 'webpack' | 'vite'
    uiLibraries: string[]
    stateLibraries: string[]
    httpClients: string[]
  }
  detectedPages: Array<{ path: string; file: string; title?: string }>
  detectedApiEndpoints: Array<{ method: string; url: string; file: string }>
  migrationRecommendations: {
    targetStack: Partial<StackRecipe>
    complexityScore: 'easy' | 'medium' | 'hard'
    warnings: string[]
  }
}

export interface MatrixIngestService {
  // 扫描并分析本地旧项目
  inspectLegacyProject(dirPath: string): Promise<LegacyProjectAnalysis>

  // 生成迁移与重构执行计划
  planProjectMigration(analysis: LegacyProjectAnalysis, targetRecipe: StackRecipe): Promise<MigrationPlan>
}
```

### 2.3 沉浸式 UI 工作台切面：`ctx.matrixUiStudio`
```typescript
export interface MatrixUiStudioService {
  // 注册工作台左侧/右侧自定义 Inspector 面板
  registerInspectorPanel(id: string, component: React.ComponentType): void

  // 注册顶部/视口自定义工具栏
  registerViewportAction(action: ViewportActionDefinition): void

  // 双向 AST 属性变更写入
  syncPropertyMutation(filePath: string, nodePath: string, newValue: unknown): Promise<void>
}
```

---

## 3. Profile 组装声明

在 `profiles/matrix-studio/cordis.patch.yml` 中挂载完整组合：

```yaml
# profiles/matrix-studio/cordis.patch.yml
- id: dsh-base
- id: dsh-web-app

# 现成基础设施
- id: @deepseek-ai/dsh-plan-mode
- id: @deepseek-ai/dsh-tool-todo
- id: @deepseek-ai/dsh-tool-ask-user
- id: @deepseek-ai/dsh-attachment-local
- id: @deepseek-ai/dsh-directory-picker-native
- id: @deepseek-ai/dsh-session-persistence-sqlite
- id: @deepseek-ai/dsh-mcp-client
- id: @deepseek-ai/dsh-tool-terminal

# Matrix Studio 核心插件
- id: @deepseek-ai/dsh-plugin-matrix-composer
- id: @deepseek-ai/dsh-plugin-matrix-contract
- id: @deepseek-ai/dsh-plugin-matrix-ingest
- id: @deepseek-ai/dsh-plugin-matrix-ui-studio
- id: @deepseek-ai/dsh-plugin-matrix-preview
- id: @deepseek-ai/dsh-plugin-matrix-delivery

# 官方第一方适配器
- id: @deepseek-ai/dsh-adapter-ui-lucky
- id: @deepseek-ai/dsh-adapter-target-uniapp
- id: @deepseek-ai/dsh-adapter-backend-hono
```
