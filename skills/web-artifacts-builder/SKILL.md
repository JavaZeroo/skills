---
name: web-artifacts-builder
description: 使用现代前端 Web 技术（React、Tailwind CSS、shadcn/ui）创建复杂多组件 claude.ai HTML 制品的工具套件。适用于需要状态管理、路由或 shadcn/ui 组件的复杂制品——不适用于简单的单文件 HTML/JSX 制品。
license: Complete terms in LICENSE.txt
---

# Web 制品构建器

要构建强大的 frontend claude.ai 制品，请按照以下步骤操作：
1. 使用 `scripts/init-artifact.sh` 初始化前端仓库
2. 通过编辑生成的代码开发制品
3. 使用 `scripts/bundle-artifact.sh` 将所有代码打包成单个 HTML 文件
4. 向用户展示制品
5. （可选）测试制品

**技术栈**：React 18 + TypeScript + Vite + Parcel（打包）+ Tailwind CSS + shadcn/ui

## 设计与样式指南

非常重要：为避免通常所说的"AI 滥作"，请避免使用过度居中的布局、紫色渐变、统一的圆角和 Inter 字体。

## 快速开始

### 步骤 1：初始化项目

运行初始化脚本创建新的 React 项目：
```bash
bash scripts/init-artifact.sh <项目名称>
cd <项目名称>
```

这将创建一个完整配置的项目，包含：
- ✅ React + TypeScript（通过 Vite）
- ✅ Tailwind CSS 3.4.1 带 shadcn/ui 主题系统
- ✅ 已配置路径别名（`@/`）
- ✅ 预安装 40+ 个 shadcn/ui 组件
- ✅ 包含所有 Radix UI 依赖项
- ✅ 已配置 Parcel 用于打包（通过 .parcelrc）
- ✅ Node 18+ 兼容性（自动检测并固定 Vite 版本）

### 步骤 2：开发制品

要构建制品，请编辑生成的文件。有关指导，请参阅下面的**常见开发任务**。

### 步骤 3：打包为单个 HTML 文件

将 React 应用打包成单个 HTML 制品：
```bash
bash scripts/bundle-artifact.sh
```

这将创建 `bundle.html`——一个包含所有 JavaScript、CSS 和依赖项的自包含制品。此文件可以直接在 Claude 对话中作为制品分享。

**要求**：您的项目必须在根目录中有一个 `index.html`。

**脚本的作用**：
- 安装打包依赖项（parcel、@parcel/config-default、parcel-resolver-tspaths、html-inline）
- 创建带有路径别名支持的 `.parcelrc` 配置
- 使用 Parcel 构建（无源映射）
- 使用 html-inline 将所有资源内联到单个 HTML 中

### 步骤 4：与用户分享制品

最后，在对话中与用户分享打包后的 HTML 文件，以便他们将其作为制品查看。

### 步骤 5：测试/可视化制品（可选）

注意：这是完全可选的步骤。仅在必要或有要求时执行。

要测试/可视化制品，请使用可用工具（包括其他技能或 Playwright、Puppeteer 等内置工具）。通常情况下，避免提前测试制品，因为这会增加请求与完成制品之间的延迟。如有请求或出现问题，可在展示制品后再进行测试。

## 参考

- **shadcn/ui 组件**：https://ui.shadcn.com/docs/components