<div align="center">
  <img src="./docs/public/vigor.png" alt="Vigor.js" width="160" />

  # Vigor.js

  基于 Vite、React 与 MDX 的静态站点生成器

  [![npm version](https://img.shields.io/npm/v/vigor-moon?color=646cff&label=npm)](https://www.npmjs.com/package/vigor-moon)
  [![npm downloads](https://img.shields.io/npm/dw/vigor-moon?color=2ea44f)](https://www.npmjs.com/package/vigor-moon)
  [![license](https://img.shields.io/github/license/min9xuan/vigor.js)](./LICENSE)

  [在线文档](https://vigor-js.vercel.app) · [快速开始](#快速开始) · [GitHub](https://github.com/min9xuan/vigor.js) · [npm](https://www.npmjs.com/package/vigor-moon)
</div>

## 项目简介

Vigor.js 是一个面向文档站、知识库和个人技术站点的静态站点生成器。它以 Markdown/MDX 文件作为内容源，通过文件系统自动生成路由，并在构建阶段完成服务端渲染与逐页预渲染，输出可直接部署的静态 HTML、CSS 和 JavaScript。

生成的页面兼顾静态 HTML 的首屏性能与 SEO 能力，同时通过 Hydration 保留 React 应用的客户端路由和交互体验。项目在 npm 上的包名为 [`vigor-moon`](https://www.npmjs.com/package/vigor-moon)，命令行名称为 `vigor`。

> [Vigor.js 在线文档](https://vigor-js.vercel.app) 本身即由 Vigor.js 构建，既是完整使用文档，也是框架的自举示例；对应内容源码位于 [`docs`](./docs) 目录。

## 核心特性

- **Vite 驱动的开发体验**：提供快速启动的开发服务器与模块热更新。
- **文件系统路由**：扫描 Markdown、MDX、JavaScript、TypeScript 及 React 页面文件，按目录结构自动生成路由。
- **SSG 与 Hydration**：并行构建客户端和 SSR Bundle，逐路由生成静态 HTML，再在浏览器端完成 Hydration。
- **Markdown 与 MDX**：支持 GFM、Frontmatter、React 组件、标题锚点、自动目录和 Shiki 代码高亮。
- **内置文档主题**：提供首页 Hero、Feature、导航栏、侧边栏、文章目录、上下页导航、404 页面及明暗主题切换。
- **原子化 CSS**：内置 UnoCSS，并集成 Wind、Attributify、Icons 等预设。
- **完整 CLI**：覆盖本地开发、生产构建和静态产物预览。
- **静态部署友好**：构建结果无需运行时服务，可部署至 Vercel 或其他静态托管平台。

## 工作原理

```text
Markdown / MDX / React 页面
          │
          ▼
   文件扫描与路由生成
          │
          ▼
 MDX 编译与内容元数据提取
          │
          ├──────────────┐
          ▼              ▼
   Client Bundle     SSR Bundle
          │              │
          └──────┬───────┘
                 ▼
       逐路由预渲染静态 HTML
                 │
                 ▼
      build/ 静态产物 + Hydration
```

构建时，Vigor.js 使用 Vite 分别生成客户端 Bundle 和 SSR Bundle，再调用服务端入口渲染每条文件路由，并生成对应 HTML 文件。浏览器获取完整 HTML 后加载客户端脚本完成 Hydration，因此站点既能直接输出页面内容，也能保留前端交互能力。

## 快速开始

### 1. 创建项目

```bash
mkdir vigor-app
cd vigor-app
npm init -y
npm install vigor-moon
```

### 2. 创建内容

```text
vigor-app/
├─ docs/
│  ├─ index.md
│  └─ config.ts
└─ package.json
```

在 `docs/index.md` 中写入：

```md
# Hello Vigor

这是我的第一个 Vigor.js 页面。
```

### 3. 配置脚本

在 `package.json` 中加入：

```json
{
  "scripts": {
    "dev": "vigor dev docs",
    "build": "vigor build docs",
    "preview": "vigor preview docs"
  }
}
```

### 4. 启动开发服务器

```bash
npm run dev
```

默认访问地址为 <http://localhost:5173>。

## 站点配置

在内容根目录创建 `config.ts`：

```ts
import { defineConfig } from 'vigor-moon'

export default defineConfig({
  title: 'My Docs',
  description: 'Documentation powered by Vigor.js',
  icon: '/logo.svg',
  themeConfig: {
    nav: [
      { text: '首页', link: '/' },
      { text: '指南', link: '/guide/getting-started' }
    ],
    sidebar: {
      '/guide/': [
        {
          text: '使用指南',
          items: [
            { text: '快速开始', link: '/guide/getting-started' }
          ]
        }
      ]
    },
    footer: {
      message: 'Released under the MIT License',
      copyright: 'Copyright © 2023-present'
    }
  }
})
```

Vigor.js 会按照文件位置自动生成访问路径，例如：

| 内容文件 | 生成路由 |
| --- | --- |
| `docs/index.md` | `/` |
| `docs/guide/index.md` | `/guide/` |
| `docs/guide/getting-started.md` | `/guide/getting-started` |

## Frontmatter

页面可以通过 Frontmatter 选择主题布局。普通文档默认使用 `doc` 布局：

```md
---
pageType: doc
---

# 快速开始
```

首页可以通过 `pageType: home` 配置 Hero、操作按钮、Feature 和 Footer。完整示例可查看 [`docs/index.md`](./docs/index.md)。

## CLI

| 命令 | 说明 |
| --- | --- |
| `vigor dev <root>` | 启动开发服务器 |
| `vigor build <root>` | 构建生产环境静态站点 |
| `vigor preview <root>` | 本地预览构建产物 |
| `vigor preview <root> --port <port>` | 在指定端口预览，默认端口为 `9999` |

执行生产构建：

```bash
npm run build
```

产物默认生成在内容根目录的 `build` 文件夹，例如 `vigor build docs` 会输出到 `docs/build`。

本地预览：

```bash
npm run preview
```

## 部署

`build` 目录是完整的静态站点产物，可以部署到 Vercel、Netlify、对象存储或其他静态 Web 服务器。

项目同时提供交互式部署与发布辅助包：

- [`vigor-moon-deployer`](./vigor-deployer)：初始化 GitHub/Vercel 部署流程。
- [`vigor-moon-publisher`](./vigor-publisher)：重新构建并发布已连接的文档站点。

详细流程参见[在线部署文档](https://vigor-js.vercel.app/tutorial/projectDeployer)。

## 仓库结构

```text
vigor.js/
├─ src/node/             CLI、配置解析、构建流程和 Vite 插件
├─ src/runtime/          React 运行时、客户端与 SSR 入口
├─ src/theme-default/    默认主题及文档组件
├─ src/types/            配置与页面类型定义
├─ docs/                 由 Vigor.js 构建的官方文档
├─ e2e/                  端到端测试与示例站点
├─ vigor-deployer/       交互式部署工具
└─ vigor-publisher/      文档更新发布工具
```

## 本地开发

```bash
git clone https://github.com/min9xuan/vigor.js.git
cd vigor.js
pnpm install

# 构建框架
pnpm build

# 运行单元测试
pnpm test:unit

# 准备并运行端到端测试
pnpm prepare:e2e
pnpm test:e2e
```

## 技术栈

- TypeScript、React、React Router
- Vite、Rollup、tsup
- Unified、Remark、Rehype、MDX
- UnoCSS、Sass、Shiki
- Vitest、Playwright

## 相关链接

- [在线文档](https://vigor-js.vercel.app)
- [npm：vigor-moon](https://www.npmjs.com/package/vigor-moon)
- [更新日志](./CHANGELOG.md)
- [MIT License](./LICENSE)

## License

[MIT](./LICENSE) © sungMoon
