# @eonova/eslint-config 使用指南

本项目纯自用，感谢`antfu`大佬，代码forks自 [`antfu/eslint`](https://github.com/antfu/eslint-config)

## 用法

### 启动向导

我们提供了一个 CLI 工具帮助初始化项目或迁移到新配置：

```bash
pnpm dlx @eonova/eslint-config@latest
```

### 手动安装

如果手动设置：

```bash
pnpm add -D eslint @eonova/eslint-config
```

并在项目根创建 `eslint.config.ts`：

```ts
// eslint.config.ts
import eonova from '@eonova/eslint-config'

export default eonova()
```

<details>
<summary>结合旧配置：</summary>

使用 [`@eslint/eslintrc`](https://www.npmjs.com/package/@eslint/eslintrc`) 转换旧格式配置：

```js
// eslint.config.ts
import eonova from '@eonova/eslint-config'
import { FlatCompat } from '@eslint/eslintrc'

const compat = new FlatCompat()

export default eonova(
  {
    ignores: [],
  },

  // 旧配置
  ...compat.config({
    extends: [
      'eslint:recommended',
      // 其他扩展...
    ],
  })

  // 其他扁平配置...
)
```

> 注：Flat 配置中 `.eslintignore` 已失效，详细自定义方法见 [自定义配置](#customization)

</details>

### 添加 npm 脚本

在 `package.json` 中添加：

```json
{
  "scripts": {
    "lint": "eslint .",
    "lint:fix": "eslint . --fix"
  }
}
```

## IDE 支持（保存时自动修复）

### VS Code 支持

安装 [VS Code ESLint 扩展](https://marketplace.visualstudio.com/items?itemName=dbaeumer.vscode-eslint)，添加以下设置到 `.vscode/settings.json`：

```jsonc
{
  "prettier.enable": false,
  "editor.formatOnSave": false,

  "editor.codeActionsOnSave": {
    "source.fixAll.eslint": "explicit",
    "source.organizeImports": "never"
  },

  "eslint.rules.customizations": [
    { "rule": "style/*", "severity": "off", "fixable": true },
    { "rule": "format/*", "severity": "off", "fixable": true },
    { "rule": "*-indent", "severity": "off", "fixable": true },
    { "rule": "*-spacing", "severity": "off", "fixable": true },
    { "rule": "*-spaces", "severity": "off", "fixable": true },
    { "rule": "*-order", "severity": "off", "fixable": true },
    { "rule": "*-dangle", "severity": "off", "fixable": true },
    { "rule": "*-newline", "severity": "off", "fixable": true },
    { "rule": "*quotes", "severity": "off", "fixable": true },
    { "rule": "*semi", "severity": "off", "fixable": true }
  ],

  "eslint.validate": [
    "javascript",
    "javascriptreact",
    "typescript",
    "typescriptreact",
    "vue",
    "html",
    "markdown",
    "json",
    "jsonc",
    "yaml",
    "toml",
    "xml",
    "gql",
    "graphql",
    "astro",
    "svelte",
    "css",
    "less",
    "scss",
    "pcss",
    "postcss"
  ]
}
```

## 自定义配置

### 基础配置

导入 `eonova` 预设并导出配置：

```js
// eslint.config.ts
import eonova from '@eonova/eslint-config'

export default eonova()
```

### 进阶配置示例

```js
// eslint.config.ts
import eonova from '@eonova/eslint-config'

export default eonova({
  type: 'lib',
  stylistic: {
    indent: 2,
    quotes: 'single'
  },
  typescript: true,
  vue: true,
  ignores: ['**/fixtures'],
  jsonc: false,
  yaml: false
})
```

### 多配置组合

通过 `FlatConfigComposer` 组合多个配置：

```js
import {
  combine,
  javascript,
  typescript,
  vue
} from '@eonova/eslint-config'

export default combine(
  javascript(),
  typescript(),
  vue()
)
```

### 插件重命名

若需恢复原始插件前缀：

```ts
import eonova from '@eonova/eslint-config'

export default eonova()
  .renamePlugins({
    ts: '@typescript-eslint',
    yaml: 'yml',
    node: 'n'
  })
```

## 支持的扩展配置

### Formatter 支持

启用外部格式化器：

```js
import eonova from '@eonova/eslint-config'

export default eonova({
  formatters: {
    css: true,
    html: true,
    markdown: 'prettier'
  }
})
```

需安装依赖：

```bash
pnpm add -D eslint-plugin-format
```

### React 支持

启用 React：

```js
import eonova from '@eonova/eslint-config'

export default eonova({
  react: true
})
```

安装依赖：

```bash
pnpm add -D @eslint-react/eslint-plugin eslint-plugin-react-hooks eslint-plugin-react-refresh
```

### Svelte 支持

启用 Svelte：

```js
import eonova from '@eonova/eslint-config'

export default eonova({
  svelte: true
})
```

安装依赖：

```bash
pnpm add -D eslint-plugin-svelte
```

（其余框架如 Astro、Solid、UnoCSS 的启用方式类似）

## 规则覆盖

文件级规则覆盖示例：

```js
// eslint.config.ts
import eonova from '@eonova/eslint-config'

export default eonova({
  files: ['**/*.vue'],
  rules: {
    'vue/operator-linebreak': ['error', 'before']
  }
})
```

## 类型感知配置

启用 TypeScript 类型检查：

```js
import eonova from '@eonova/eslint-config'

export default eonova({
  typescript: {
    tsconfigPath: 'tsconfig.json'
  }
})
```

## 其他

### 查看已启用规则

运行命令查看详细规则列表：

```bash
npx @eslint/config-inspector
```

### Lint Staged 集成

添加 Git Hook 并配置：

```json
{
  "simple-git-hooks": {
    "pre-commit": "pnpm lint-staged"
  },
  "lint-staged": {
    "*": "eslint --fix"
  }
}
```

安装依赖：

```bash
pnpm add -D lint-staged simple-git-hooks
npx simple-git-hooks
```
