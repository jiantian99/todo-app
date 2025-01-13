# Vue3 Todo App

一个基于 Vue3 和 Element Plus 的待办事项管理应用。

## 功能特点

- 📅 显示当前日期（年月日 + 星期）
- ✨ 支持待办事项优先级管理
  - 通过点击旗帜图标循环切换优先级（普通/急/紧急）
  - 不同优先级使用不同颜色标识（绿/黄/红）
- 📋 待办事项列表功能
  - 按优先级自动排序
  - 支持标记完成/未完成
  - 支持删除操作
- 💾 数据本地持久化存储
- 🎨 简洁美观的界面设计

## 技术栈

- Vue 3
- Element Plus
- LocalStorage

## 项目设置

### 安装依赖
```bash
npm install
```

### 开发环境运行
```bash
npm run serve
```

### 生产环境构建
```bash
npm run build
```

### 代码检查
```bash
npm run lint
```

## 性能优化

- 使用 Object.freeze() 优化静态数据
- 优化数组操作和内存使用
- 合理的错误处理机制
- 优化的代码结构和变量作用域

## 部署指南

### Cloudflare Pages 部署

1. Fork 本项目到你的 GitHub 账号

2. 登录 Cloudflare Dashboard，进入 Pages 页面

3. 创建新项目
   - 点击 "Create a project"
   - 选择 "Connect to Git"
   - 选择你 fork 的仓库

4. 设置构建配置
   - 构建命令：`npm run build`
   - 构建输出目录：`dist`
   - 框架预设：Vue
   - Node.js 版本：16

5. 环境变量设置（可选）
   ```
   NODE_VERSION=16
   ```

6. 点击 "Save and Deploy"

完成以上步骤后，Cloudflare Pages 会自动部署你的应用，并提供一个 `*.pages.dev` 的域名。每次推送代码到主分支时，都会自动触发重新部署。

## 备注

本项目代码由 AI 辅助生成，使用 Claude AI 进行开发指导和代码优化。

## License

MIT
