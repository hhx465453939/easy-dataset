# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## 项目概述

Easy Dataset 是一个用于创建大语言模型（LLM）微调数据集的桌面应用。用户可以上传文档文件，系统会自动分割文本、生成问题并创建高质量的数据集。

## 常用命令

### 开发命令
```bash
# 开发环境启动
pnpm dev                    # 启动开发服务器（端口1717），自动推送数据库schema
pnpm build                  # 构建生产版本
pnpm start                  # 启动生产服务器（端口1717）

# 数据库操作
pnpm db:push                # 推送Prisma schema到数据库
pnpm db:studio              # 打开Prisma Studio数据库管理界面
pnpm db:template            # 生成数据库模板文件

# Electron桌面应用
pnpm electron               # 直接运行Electron应用
pnpm electron-dev           # 开发模式：启动Next.js服务后运行Electron
pnpm electron-pack          # 打包应用到目录
pnpm electron-dist          # 打包应用并分发
pnpm electron-build         # 构建并打包所有平台（macOS, Windows, Linux）
pnpm electron-build-mac     # 仅构建macOS版本
pnpm electron-build-win     # 仅构建Windows版本
pnpm electron-build-linux   # 仅构建Linux版本

# Docker部署
pnpm docker                 # 构建Docker镜像

# 代码质量
pnpm lint                   # 运行ESLint检查
pnpm prettier               # 格式化所有代码文件
```

### 项目初始化流程
```bash
# 克隆项目后
npm install                  # 或 pnpm install
pnpm dev                    # 首次启动开发环境
```

## 核心架构

### 技术栈
- **前端框架**: Next.js 14 (App Router)
- **桌面应用**: Electron
- **数据库**: SQLite + Prisma ORM
- **UI框架**: Material-UI (MUI) 5.16.14
- **状态管理**: Jotai
- **国际化**: i18next + react-i18next
- **LLM集成**: 支持OpenAI、Ollama、OpenRouter等多种提供商

### 数据库架构
数据库使用SQLite，主要实体包括：
- **Projects**: 项目管理，包含所有配置和元数据
- **UploadFiles**: 上传的文件记录
- **Chunks**: 文件分割后的文本块
- **Questions**: 生成的问题
- **Datasets**: 最终的数据集（问答对）
- **Tags**: 标签树结构
- **ModelConfig**: 模型配置
- **Task**: 后台任务管理

### 应用架构
采用Next.js + Electron混合架构：
- **Next.js**: 提供Web界面和API服务
- **Electron**: 提供桌面应用壳层，支持离线使用和系统集成

### 主要功能模块

1. **文档处理** (`app/projects/[projectId]/text-split/`)
   - 支持PDF、Markdown、DOCX、TXT格式
   - 智能文本分割
   - 目录结构提取

2. **问题生成** (`app/projects/[projectId]/questions/`)
   - 基于文本块生成问题
   - 标签树管理
   - 问题编辑和优化

3. **数据集创建** (`app/projects/[projectId]/datasets/`)
   - 生成答案
   - 数据集管理
   - 多格式导出（Alpaca、ShareGPT格式）

4. **模型配置** (`lib/llm/`)
   - 支持多种LLM提供商
   - 自定义提示词
   - 参数配置

### 关键目录结构
```
lib/
├── api/                    # API客户端
├── db/                    # 数据库操作层
├── file/                  # 文件处理
│   ├── file-process/      # 文件处理逻辑
│   └── split-markdown/    # Markdown分割逻辑
├── llm/                   # 大模型集成
│   ├── core/             # 核心LLM逻辑
│   └── prompts/          # 提示词模板
├── services/             # 业务服务层
└── util/                 # 工具函数

components/
├── distill/              # 蒸馏相关组件
├── text-split/           # 文本分割组件
├── questions/            # 问题管理组件
├── datasets/             # 数据集组件
└── playground/           # 模型测试组件
```

## 开发注意事项

### 数据库操作
- 所有数据库变更需要更新 `prisma/schema.prisma`
- 修改schema后运行 `pnpm db:push`
- 使用 `pnpm db:studio` 查看数据库状态

### 文件处理
- 文件处理逻辑在 `lib/file/` 目录
- PDF处理支持多种引擎：pdf2md-js、@opendocsg/pdf2md
- 文件分割使用自定义的Markdown分割器

### 国际化
- 支持中文和英文
- 翻译文件在 `locales/` 目录
- 使用 `i18next` 进行语言切换

### Electron开发
- 开发模式会同时启动Next.js和Electron
- 生产模式需要先构建Next.js应用
- 自动更新使用 `electron-updater`

### 代码规范
- 使用ESLint进行代码检查
- 使用Prettier进行代码格式化
- 提交信息遵循Conventional Commits规范

## 环境变量
项目需要配置以下环境变量：
- `DATABASE_URL`: SQLite数据库连接字符串

## 部署相关
- Docker部署使用 `docker-compose.yml`
- 支持多平台构建：macOS、Windows、Linux
- 构建产物在 `dist/` 目录