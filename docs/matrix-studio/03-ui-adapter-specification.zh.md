# UI 组件库适配器开发规范与参考实现 (UI Adapter Specification)

> **文档版本**: v1.2.0
> **面向对象**: 开源组件库作者、第三方插件开发者、平台扩展者
> **设计目标**: 任何团队或个人开发的 UI 组件库，仅需遵循本规范编写一个标准的 Cordis 适配插件，即可无缝接入 DSH Matrix Studio 的 AI 代码生成流水线。

---

## 1. UI 适配器核心接口协议 (`UiKitAdapter`)

所有接入平台的组件库（无论是 UniApp、Vue PC、React 还是 Web Components）均通过实现 `UiKitAdapter` 接入：

```typescript
export interface UiComponentMeta {
  name: string                 // 组件标签名，如 'lk-button' 或 'el-button'
  description: string          // 组件用途与语义说明（用于模型决策）
  propsSchema: object          // JSON Schema 属性元数据与默认值
  events: string[]             // 支持的事件列表，如 ['click', 'change']
  slots: string[]              // 支持的插槽，如 ['default', 'icon']
  promptSnippets: string       // 给 Agent 的最佳实践提示（如：表单提交时必须绑定 loading）
  templateExample: string      // 标准调用代码范例
}

export interface UiKitAdapter {
  id: string                   // 唯一标识符，如 'lucky-ui', 'shadcn-vue', 'element-plus'
  displayName: string          // 显示名称，如 'Lucky UI (UniApp 移动端)'
  targetPlatform: 'mobile' | 'pc' | 'admin' | 'universal'
  framework: 'vue' | 'react' | 'svelte'

  // 暴露给 AI 知识库的组件字典
  components: Record<string, UiComponentMeta>

  // 页面脚手架与样式引擎模板注入
  getThemeScaffold(stylingEngine: string): Record<string, string>

  // 将中立抽象 Layout AST 节点转译为该组件库的具体 JSX/Vue 模板标签
  mapSemanticNode(node: AbstractLayoutNode): GeneratedComponentCode
}
```

---

## 2. 官方参考实现：`lucky-ui` 适配器范例

作为首批开源参考实现，`@deepseek-ai/dsh-adapter-ui-lucky` 展示了如何将一个 UniApp 组件库插件化：

### 2.1 语义映射定义
```typescript
import { UiKitAdapter } from '@deepseek-ai/dsh-plugin-matrix-composer'

export const LuckyUiAdapter: UiKitAdapter = {
  id: 'lucky-ui',
  displayName: 'Lucky UI',
  targetPlatform: 'mobile',
  framework: 'vue',
  components: {
    'lk-button': {
      name: 'lk-button',
      description: '通用按钮，用于触发业务操作或表单提交',
      propsSchema: {
        type: { type: 'string', enum: ['primary', 'success', 'warning', 'danger', 'default'] },
        size: { type: 'string', enum: ['large', 'normal', 'small', 'mini'] },
        loading: { type: 'boolean' },
        disabled: { type: 'boolean' }
      },
      events: ['click'],
      slots: ['default', 'icon'],
      promptSnippets: '表单提交或异步操作时，必须通过 ref 或 state 绑定 :loading="isLoading"',
      templateExample: '<lk-button type="primary" :loading="submitting" @click="handleSubmit">提交</lk-button>'
    },
    'lk-form': {
      name: 'lk-form',
      description: '表单容器，支持多端统一校验与模型双向绑定',
      propsSchema: { model: { type: 'object' }, rules: { type: 'object' } },
      events: ['validate'],
      slots: ['default'],
      promptSnippets: '必须配合 :model 与 :rules 使用，校验规则与后端 Schema 自动对齐',
      templateExample: '<lk-form :model="formData" :rules="formRules" ref="formRef">...</lk-form>'
    }
  },
  getThemeScaffold: (stylingEngine) => ({
    'tailwind.config.js': `module.exports = { /* lucky-ui 主题预设 */ }`
  }),
  mapSemanticNode: (node) => {
    // 根据 AST 属性返回 `<lk-button ...>` 或 `<lk-form ...>` 代码
  }
}
```

---

## 3. 第三方组件库如何接入？

任何开发者想接入自己的组件库（例如 `Wot Design Uni`、`Ant Design 5` 或 `Shadcn-React`），只需要 3 步：

1. **新建适配器包**：`packages/matrix/adapters-ui/my-ui-kit/`；
2. **实现 `UiKitAdapter`**：整理组件清单与 Prompt 规则；
3. **在 Cordis 中注册**：
   ```typescript
   export default class MyUiPlugin {
     static inject = ['matrixComposer']
     apply(ctx: Context) {
       ctx.matrixComposer.registerUiAdapter('my-ui-kit', MyUiKitAdapter)
     }
   }
   ```

用户在 Stack Composer 界面即可立即看到该组件库并一键勾选！
